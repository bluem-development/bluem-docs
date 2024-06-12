# Tutorial: Payments

**From onboarding to payments:** Bluem makes payments easy for your customers and is convenient to set up! 

*Requirements & Additional configuration*: An instantiated Bluem object. No direct additional configuration is required.

#### Creating a Payment request

1. After setting up your Bluem configuration, you can instantiate it and actually start making requests.

    The first step is to set the right parameters for the request, which you can also store inside your application for referencing/retrieving the request in your own data stores.
    
    ```php
    $bluem_object = new Bluem($configuration);
    
    // description: a concise description with references to order name etc.
    $description = "Test payment";
    // amount: as a float
    $amount = 100.00;
    // currency: if set to null, will default to EUR as string
    $currency = "EUR";
    // any additional reference to the customer, user or order
    $debtorReference = "1234023";
    // dueDateTime: set by default to a day in advance.
    // format: use a datetime string in "YYYY-MM-DD H:i:s" format
    $dueDateTime = null;
    
    // returnUrl: set this if you want to override the return URL 
    // in your own callback function location specific for payments. 
    // If empty string or not given, the configuration-defined return 
    // URL will be used.
    $returnUrl = "";
    
    // creating the request object
    $request = $bluem_object->CreatePaymentRequest(
        $description,
        $debtorReference,
        $amount,
        $dueDateTime,
        $currency,
        "",
        $returnUrl
    );
    ```

2. Then you can actually perform it and deal with the response.
   
    The response will contain an `entranceCode` and a `transactionID` which allow you to identify the transaction.
   And most importantly a `transactionURL`, which is where the customer or user has to go.

    ```php
    $response = $bluem_object->PerformRequest($request);
    
    if ($response->ReceivedResponse()) {
    
        $transactionURL = $response->GetTransactionURL();
    
        // The EntranceCode is set by the response; 
         // it is required for following status requests.
   
        // Suggestion: save the initiated transaction details; for example
        $entranceCode = $response->GetEntranceCode();
        $transactionId = $response->GetTransactionID();
   
        $_SESSION['entranceCode'] = $entranceCode;
        $_SESSION['transactionId'] = $transactionId;
    
        // Suggestion: redirect to the above transaction URL
        header("Location: ".$transactionURL);
    } else {
        $error = $response->Error();
        // To implement: no proper status given, 
        // show an error, exit, etc..
    }
    ```

3. The customer can now pay in the Bluem (ViaMijnBank) portal.
   
   Afterwards, they will come back to your return URL set in the configuration or in the request initialization.
   
   At that point, you can verify if the payment was completed and with what status. We do this by doing an additional request to Bluem, but this time we request a transaction status.
   
   The below code sample is how you can deal with the several cases of returned status responses.

    ```php
    // --------------------------------------------
    /**
     * Requesting a Payment status
     * */
    
    // retrieve these parameters from your local data store
    $transactionID = "12345";
    $entranceCode = "20200921162354249";
    
    $response = $bluem_object->PaymentStatus($transactionID, $entranceCode);
    
    
    if (!$response->Status()) {
        // To implement: Error when retrieving status.
        // you can use  $response->Error()
        // and deal with it.
    }
    
    $statusUpdateObject = $response->PaymentStatusUpdate;
    $statusCode = $statusUpdateObject->PaymentStatus->Status . "";
    
    if ($statusCode === "Success") {
    
        // deal with a proper payment
    } elseif ($statusCode === "Cancelled") {
    
        // Implement: do what you need to when the transaction has been cancelled
    
    } elseif ($statusCode === "Open" || $statusCode === "Pending") {
    
        // the transaction is still open. it might take some time to process
        // Implement: show a message that reflects this
    
    } elseif ($statusCode === "Expired") {
    
        // Implement: show a message that reflects an expired transaction;
    
    } else {
    
        // unknown status occurred
    
        echo "Error: Unknown or incorrect status retrieved: $statusCode
            <br>Contact the administrator and communicate this status";
        exit;
    
    }
     ```

   This is very much alike to the status response that one gets from the webhook (see [webhooks](tutorial-webhooks.md) for more information).


## Preselecting a bank for Payment requests using debtorWallet
**Note:** This is relevant for bank-based transactions and services:

It is possible to preselect a Bank within your own application for Payments, based on an IssuerID (BIC/Swift code) when creating a Mandate, Payment or Identity request. This can be used if you want to user to select the given bank in your own interface and skip the bank selection within the Bluem portal interface.
This reduces the amount of steps required by performing the selection of the bank within your own application and interface by utilizing the preselection feature from the PHP library on the request object as so:

```php
$BIC = "INGBNL2A";
$request->selectDebtorWallet($BIC);
```
Parameter has to be a valid BIC code of a supported bank. An invalid BIC code will trigger an exception. **Please note that supported BICs differ per service as not every bank offers the same services!** The supported BIC codes per service can also be requested from a Bluem object, given the service context. **As appendix to this Documentation file, you can find a full list of all BICs per context.**

Illustrated here is that a list of BICs per context can also be retrieved programmatically:

```php
$MandatesBICs = $bluem->retrieveBICsForContext("Mandates"); // also specific to localInstrumentCode, see notes.
$PaymentsBICs = $bluem->retrieveBICsForContext("Payments");
$IdentityBICs = $bluem->retrieveBICsForContext("Identity");
```
Input of a different context will trigger an exception. If valid, the result is an array of `Bluem\BluemPHP\BIC` objects with attributes `IssuerID` and `IssuerName`: the BIC and Bank name respectively. You can use this to populate your user interface.

Please note that the BIC list will vary when a different `localInstrumentCode` is configured. The localInstrumentCode `CORE` and `B2B` are supported by different banks. Based on your configuration, the right BIC list is loaded from context automatically and used to verify the debtorWallet.

This method can be used when creating iDIN and when creating iDEAL requests; you could store the selected bank (“Issuer”) on user level and use it when creating a request for your user.
- You can inform the user WHY this is necessary and refer to the new laws and rules, in your own website/application or refer to the news/public announcements.
- You can inform the user about the amount of trouble required: display a piece of text saying that it only takes a minute or two, and that it is stored for your convenience: that it ensures integrity, and a valid webshop experience.

## Using different Payment transaction methods

**Important note: ensure you have the right BrandID set up for specific payment methods. 
Refer to your account manager to retrieve a list of the specific BrandIDs per payment method**

You can allow the PayPal and Creditcard payment methods by selecting these within the request object before sending it.

To use iDeal, (default option). A BIC **can** be provided. If left empty, bank selection will occur in the Bluem portal.
```php
$BIC = 'INGBNL2A';
$request = $request->setPaymentMethodToIDEAL($BIC); 
```

To use PayPal, give in a PayPal account email address. The email is also not required.
```php
$payPalAccount = 'john.doe@gmail.com';
$request = $request->setPaymentMethodToPayPal($payPalAccount); 
```

To use Creditcards, you can set the credit card details as follows (not required)
```php
$request = $request->setPaymentMethodToCreditCard();
```
or
```php
$cardNumber = '1234000012340000';
$name = 'John Doe';
$securityCode = 123;
$expirationDateMonth = 11;
$expirationDateYear = 2025;

$request = $request->setPaymentMethodToCreditCard(
    $cardNumber,
    $name,
    $securityCode,
    $expirationDateMonth,
    $expirationDateYear
); 
```

For example, to use Sofort, use the following method:
```php
$request = $request->setPaymentMethodToSofort(); 
```

Available options are:

```php
$request->setPaymentMethodToIDEAL(...);
$request->setPaymentMethodToPayPal(...);
$request->setPaymentMethodToCreditCard(...);
$request->setPaymentMethodToSofort();
$request->setPaymentMethodToCarteBancaire();
```

From version 2.3.3 of this plug-in you can utilize also:
```php
$request->setPaymentMethodToSofortDigitalServices();
$request->setPaymentMethodToBancontact();
$request->setPaymentMethodToGiropay();
```



Required information per payment method:

| Payment Method          | Required Parameters                                                                                                                      |
|-------------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| iDEAL                   | BIC (optional, when set will bypass bank selection)                                                                                      |
| PayPal                  | PayPal Account (optional)                                                                                                                |
| Credit Card             | All optional: Card Number (string), Name (string), Security Code (string), Expiration Date Month (string), Expiration Date Year (string) |
| Sofort                  | None                                                                                                                                     |
| Sofort Digital Services | None                                                                                                                                     |
| Carte Bancaire          | None                                                                                                                                     |
| Bancontact              | None                                                                                                                                     |
| Giropay                 | None                                                                                                                                     |

Once the request executes, the link to the transaction will send you to the Bluem Portal with the corresponding interface and flow.
