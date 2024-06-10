# Tutorial: Getting Started
Setting up the PHP library is straightforward: 
it is a matter of installing the library and 
instantiating it in your PHP application with the given credentials. 

## Requirements

- PHP v8.0 or higher
- Composer

## Steps

1. Install the library through [Composer](https://getcomposer.org). Run Composer to install this library and dependencies from your project folder.

    ```bash
    composer require bluem-development/bluem-php
    ```

2. Ensure that you are loading the Composer packages in your application.

    ```php
    require __DIR__ . '/vendor/autoload.php';
    ```
   Read more about Composer [in the docs](https://getcomposer.org/doc/01-basic-usage.md).

3. Initialize a Bluem config object in your PHP application like so: 

   ```php
   
   use Bluem\BluemPHP\Bluem;
   
   $bluem_config = new Stdclass();
   ```

4. Set up the basic credentials and configuration settings:

   ```php
   $bluem_config->environment = "test" ;                
   $bluem_config->senderID = "";                         
   $bluem_config->brandID = "";                          
   $bluem_config->test_accessToken = "";                
   $bluem_config->production_accessToken = "" ;
   $bluem_config->merchantReturnURLBase = "https://example.com";         
   $bluem_config->expectedReturnStatus = "success" ;
   ```
   The above settings are your credentials. They must be filled before the integration works.
   
   | Key                       | Value                                                                                                                                                                |
   |---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------|
   | environment 🔑            | Fill in `prod`, `test` or `acc` for production, test or acceptance environments respectively.                                                                        |
   | senderID 🔑               | The sender ID, issued by Bluem. Always starts with an `S`, followed by a number.                                                                                     |
   | brandID 🔑                | Your BrandID is set at Bluem and given through email. It can vary for specific services: if so you will                                                              |
   | test_accessToken 🔑       | The access token to communicate with Bluem, for the test environment.                                                                                                |
   | production_accessToken 🔑 | The access token to communicate with Bluem, for the production environment.                                                                                          |
   | merchantReturnURLBase     | URL to return to after finishing the process. This should simply be alike to your domain name or webshop/website URL.                                                |
   | expectedReturnStatus      | What status would you like to get back for a TEST transaction or status request?<br/><br/>Possible values: none, success, cancelled, expired, failure, open, pending |

   The credentials marked with a 🔑 will be provided to you by your Bluem account manager or contact.

   > Note: there are several additional settings that are important to set for specific services. The tutorials will refer to these settings in their respective tutorials

5. You can then instantiate a new Bluem object in any of your classes:

   ```php
   $bluem_object = new Bluem($bluem_config);
   ```
   
   You can see a full example initialization in [this file](https://github.com/bluem-development/bluem-php/blob/master/examples/initialization.md);
   > Note: that creating this Bluem object will generate an exception if the configuration is not valid. This applies mostly to the essential configuration variables.

6. You are now ready to move on to actually using individual parts of the library!

> Note: In upcoming versions: we will update the configuration flow to be more convenient.
