# 📅 Important recent plug-in updates

[//]: # (We have not been sitting still and we're working on the next major release that will be labeled 1.3.19)

- ⚡ **Key changes in hotfix version 1.3.17.15 (June 2024)**
  
  > A webhook certificate update is imminent (in effect per 1st of July).
  > **This affects websites that utilize Payment gateways through the plug-in and have webhooks set up.**
  > Read more about it in the [News & annoucnements](News-announcements.md). 
  
  If you encounter problems per that date, we have created a patch version that can be downloaded as a ZIP archive.
  This version only applies. You can install this as site administrator by replacing your existing `wp-content/plugins/bluem` plugin folder and then any webhook issues will be resolved.
  
  > **Download the new version here:** [https://github.com/bluem-development/bluem-woocommerce/releases/tag/1.3.17.15-certificate](https://github.com/bluem-development/bluem-woocommerce/releases/tag/1.3.17.15-certificate)
  
  Subsequent versions of the plug-in are compatible. As soon as we can update again, they will simply be offered the new versions automatically. So no further additional work is needed for after that.


- ⚡ **Main changes in version 1.3.17.5 (May 2023)**.
  - Age verification per product *.
    This can be set via plug-in settings, under 'Identity' (iDIN).

  * To use the functionality, you need to purchase the iDIN service from us.
    If you do not yet purchase it, please contact us: [info@bluem.nl](mailto:info@bluem.nl).

- ⚡ **Main changes in version 1.3.17.4 (May 2023)**
  - Stability improvements.

- ⚡ **Main changes in version 1.3.17.3 (April 2023)**.
  - Implementation with our new PHP library.

- ⚡ **Main changes in version 1.3.17.2 (April 2023)**.
  - Gravity Forms eMandates integration improvements.

- ⚡ **Main changes in version 1.3.17.1 (March 2023)**.
  - Additional fields for Gravity Forms eMandates integration.


- ⚡ **Main changes in version 1.3.17.0 (March 2023)**.
  - New: Gravity Forms eMandates integration.

- ⚡ **Main changes in version 1.3.16.2 (January 2023)**.
  - Stability improvements.

- ⚡ **Main changes in version 1.3.16.1 (January 2023)**.
  - Stability improvements.

- ⚡ **Main changes in version 1.3.16 (December 2022)**.
  - New payment methods SOFORT and Carte Bancaire added.



- ⚡ **Main changes in version 1.3.15 (November 2022)**.
  - Stability improvements.

- ⚡ **Main changes in version 1.3.14 (September 2022)**.
  - IPAPI bug fix.

- ⚡ **Main changes in version 1.3.13 (July 2022)**.
  - Contact Form 7 eMandates integration *.

  * To use the functionality, you need to purchase the Mandate service from us.
    If you do not yet purchase it, please contact us at: [info@bluem.nl](mailto:info@bluem.nl).
  - Stability improvements.

- ⚡ **Main changes in version 1.3.4**

  The plugin is now even more stable! 🤖


- ⚡**Main changes in version 1.3, beginning October 2021**
  - 📢 An automatic email with non-personal technical information of error messages is sent to the plugin developer. Disable this option in the general settings
  - 🖼️ Layout improvements
  - 🪲 Bug fix in guest user identification, which was not properly recognised when the session expired in the browser.
  - 📂 Import and export your entire Bluem configuration from your site, so you can easily transfer it to new sites or test environments.




- 📜 **Important changes in version 1.2.17, dated 7 July 2021**
  - The manual can now be accessed from the plugin in the navigation bar.
  - You now see an interface of the bank in test Identity requests itself where you can edit the response.
  - Stability improvements and friendlier layout

- **📜 Important changes in version 1.2.16: **
  - You can now fill in a popup with an explanation of the identification procedure, to guide users more through the process. The text in the explanation can be changed via the Settings page
  - The bug that incorrectly displayed in the mail that an identification was completed has been fixed. Now this should no longer be visible and, if an identification is required and this is put in the mail, it should be filled correctly.
  - Identified Customers who create an account during ordering take their identification with them to their customer account. Customers who identify after they are registered naturally do so too.
  - Redirects after identification now work better.
  - Fixed bugs where descriptions of identity requests contained illegal characters.
  - Stability improvements.

