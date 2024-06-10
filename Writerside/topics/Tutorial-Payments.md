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