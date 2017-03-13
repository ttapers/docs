---
title: Test the Hologram network on an iPhone
nav_sort: 7
layout: tutorial.hbs
preview_image: /wp-content/uploads/2016/11/iphone.jpg
lunr: true
tags:
---

{{#callout}}
Even on unlocked iPhones, we and
our customers have often found that certain carriers have blocked the
ability to change iPhone APN settings in iOS; therefore, it is possible
to have an iPhone that is unlocked but the procedure described in this
article to be impossible to complete (due to missing menu options).
{{/callout}}

This is the process for changing the APN
settings on iOS running on an unlocked iPhone (iOS 6 and up), which will
place the iPhone on the Hologram cellular network:

{{{ image src="/wp-content/uploads/2015/05/ipn-e1431618309238.png"
    alt="iOS APN configuration" }}}

1.  From the main menu, select **Settings**.
2.  Select **General**.
3.  Select **Network** (note: for iOS6, select **Cellular**).
4.  Verify the following settings:
    * **Data Roaming:** On
    * **Wi-Fi:** Off
    * **3G Enable:** On
5.  Select **Cellular Data Network**, and change the following settings:
    * **APN:** apn.konekt.io
    * **User:** (no user)
    * **Pass:** (no pass)
6.  Reset the iPhone and verify that it is not in Airplane Mode after
    booting is complete

Be aware of your data usage when connected to the network. For more
information:

-   [Apple Support on APN settings](https://support.apple.com/en-us/HT201699)
-   [Stack Exchange thread](https://apple.stackexchange.com/questions/103651/where-can-i-find-the-apn-settings-for-an-unlocked-att-iphone-4-running-ios-7)

