# Tutorial: Using webhooks

Webhooks are available for Payments, eMandates, and Identity services. They trigger during requests to the Bluem flow, sending data to your application. This ensures all processes are completed, even if the user does not reach your regular callback methods.

#### How to Use Webhooks

To use webhooks, you need to activate the endpoints by creating one for both testing and production environments. These endpoints must be configured on our side and implemented on your client side.

**Most importantly: Communicate the endpoint URLs to [pluginsupport@bluem.nl](mailto:pluginsupport@bluem.nl?subject=Bluem+Webhook+Endpoints) to have these URLs be configured in your account.**
Please allow for a few working days for this to be configured. We will follow up as soon as the endpoints have been added.

We can help you troubleshoot any problems you might face after creating the endpoints. We can also help you verify that data gets sent properly.
Please contact us if you need help in this regard.


#### 1. Setting Up Endpoints

Without further ado, lets learn how to set this up:
> Note: to make it easier, ensure you have the request and status features of this library implemented in your application before you dive into webhooks.

Create HTTP-reachable endpoints for your production environment. If possible, you are recommended to do the same for a testing and/or acceptance environment. 
> Examples include:
> **https://example.com/webhook/test** or
  > **https://example.com/webhook/production**

#### 2. Create a webhook receiver

Initialize the Bluem object (see the [tutorial on setting up your library](tutorial-configuration.md)) 

Call the `Webhook()` function on your Bluem object within that endpoint. Implement this like you implemented the callback for the regular services in your application.

```php
$bluem_object->setConfig("webhookDebug", false);
$webhookResponse = $bluem_object->Webhook();


if ($webhookResponse !== null) {     
  if ($webhookResponse->getStatus() === "Success") {
    // deal with the successful callback
  } elseif (...) {
    // ...etc
  }
}
```
Refer to the `examples/payments-webhook.php` for a more in-depth example implementation and retrieval of the data when a webhook is received in your application.

You can utilize the following functions on the callback object to get the data from the webhook and process the relevant data.

> _Note_: Most functions return string values, unless otherwise specified.

**Functions available in all services:**

```php
$webhook->getEntranceCode();
$webhook->getPaymentReference();
$webhook->getCreationDateTime();
$webhook->getStatus();
$webhook->getDebtorReference();
```

**Payments specific functions:**
```php
$transactionID = $webhook->getTransactionID();
$amount = $webhook->getAmount();
$amountPaid = $webhook->getAmountPaid();
$currency = $webhook->getCurrency();
$paymentMethod = $webhook->getPaymentMethod();

// note: these return a SimpleXML object
$paymentMethodDetails = $webhook->getPaymentMethodDetails(); 
$iDealDetails = $webhook->getIDealDetails();

// note: these are iDEAL specific
$debtorAccountName = $webhook->getDebtorAccountName();
$debtorIBAN = $webhook->getDebtorIBAN();
$debtorBankID = $webhook->getDebtorBankID();
```

**EMandates specific functions:**
```php
$mandateID = $webhook->getMandateID();
$statusDateTime = $webhook->getStatusDateTime();

$originalReport = $webhook->getOriginalReport(); 
// note: returns raw XML cdata object that still needs to be parsed.

$acceptanceReport = $webhook->getAcceptanceReportArray(); 
// note: returns array with a lot of values that are of use.
```

**Identity specific functions:**

```php
$requestType = $webhook->getRequestType();
$transactionID = $webhook->getTransactionID();
$debtorReference = $webhook->getDebtorReference();
$authenticationAuthorityID = $webhook->getAuthenticationAuthorityID();
$authenticationAuthorityName = $webhook->getAuthenticationAuthorityName();

// note: returns array with a lot of values that are of use.
$identityReportArray = $webhook->getIdentityReportArray(); 
```

#### 3. How to configure
**Communicate the endpoint URLs to [pluginsupport@bluem.nl](mailto:pluginsupport@bluem.nl?subject=Bluem+Webhook+Endpoints) to have these URLs be configured in your account.**
Please allow for a few working days for this to be configured. We will follow up as soon as the endpoints have been added.

#### 4. Verify that it works
You can POST to your own endpoints to verify that it works. Please contact us for a sample Webhook call that you can use for your service.

#### Support for webhooks
We can help you troubleshoot any problems you might face after creating the endpoints. We can also help you verify that data gets sent properly.
Please contact us if you need help in this regard.

#### Further resources
##### Example webhook for payments:

Creating a webhook for payments
```php
$bluem_object = new Bluem(...);

/*
 * Creating a webhook
 *
 * when running this in a webserver, this allows you to expose the webhook to the url like this:
 *
 * http://example.com/payments-webhook.php?action=webhook
 *
 * change this URL to match your web server.
 */

// This GET parameter is an example. This is not required.
if ($_GET['action'] === "webhook") {

    // if you want debug information and verbose results when testing the webhook, set this to true
    $bluem_object->setConfig("webhookDebug", false);

    // this call will exit with a 200 or 400 HTTP status code, and parse the incoming data
    // Returns null if the webhook didn't parse successfully.
    $webhook = $bluem_object->Webhook();

    // implement this like you implemented the callback for the regular services in your application
    if ($webhook !== null) {

        $status = $webhook->getStatus();

        if ($status === "Success") {

            $transactionID = $webhook->getTransactionID();
            $amount = $webhook->getAmount();
            $amountPaid = $webhook->getAmountPaid();
            $currency = $webhook->getCurrency();
            $paymentMethod = $webhook->getPaymentMethod();

            // note: these are currently iDEAL specific
            $debtorAccountName = $webhook->getDebtorAccountName();
            $debtorIBAN = $webhook->getDebtorIBAN();
            $debtorBankID = $webhook->getDebtorBankID();

            // deal with the successful callback

        } elseif($status ==="Cancelled") {
            // deal with the cancelled callback
        } elseif($status ==="Open") {
            // deal with the open callback
        } elseif($status ==="Expired"){
            // deal with the failed or expired callback
        } else {
            // deal with any other status
        }

        // refer to the readme on webhooks for example methods to use for retrieving data from the callback object.
    }
}
```
