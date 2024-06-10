# Tutorial Using webhooks


Webhooks exist for Payments, eMandates and Identity. They trigger during requests to the Bluem flow and send data to your application.
They are vital to ensure all processes are always completed, even if the customer/user does not reach your regular callback method(s) in your flow.

### How to use them

Activate the endpoints by creating an endpoint for testing and production environments.
They then need to be configured on Bluem's side and implemented on the client side.
Create an HTTP-reachable endpoint for TEST and PRODUCTION environments. Examples are:

- [https://example.com/webhook/test](https://example.com/webhook/test)
  or [https://example.com/webhook_test.php](https://example.com/webhook/test)
- [https://example.com/webhook/production](https://example.com/webhook/production)
- etc.

Call the `Webhook()` function within that endpoint:

```php
$bluem_object->setConfig("webhookDebug", false);
$webhook = $bluem_object->Webhook();

// implement this like you implemented the callback
// for the regular services in your application.
if ($webhook !== null) {     
    if ($webhook->getStatus() === "Success") {
        // deal with the successful callback
    }
    // elseif (...) {...etc
}
```
Refer to the `examples/payments-webhook.php` for a more in-depth example implementation and retrieval of the data when a webhook is received in your application.


You can utilize the following functions on the callback object to get the data from the webhook and process the relevant data.

_Note_: Most functions return string values, unless otherwise specified.

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

### 2. How to configure
**Communicate the endpoint URLs to [pluginsupport@bluem.nl](mailto:pluginsupport@bluem.nl?subject=Bluem+Webhook+Endpoints) to have these URLs be configured in your account.**
Please allow for a few working days for this to be configured. We will follow up as soon as the endpoints have been added.

### 3. Verify that it works
You can POST to your own endpoints to verify that it works. Please contact us for a sample Webhook call that you can use for your service.

### Support for webhooks
We can help you troubleshoot any problems you might face after creating the endpoints. We can also help you verify that data gets sent properly.
Please contact us if you need help in this regard.