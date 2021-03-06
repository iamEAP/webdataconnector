---
layout: page
title: Frequently Asked Questions
base: docs
---

#### Where can I find known issues?

You can view open issues, and submit new issues, on our [Github issues page](https://github.com/tableau/webdataconnector/issues).

#### Can I contact Tableau for help with my connector?

Tableau does not provide support for connectors or for other programs written to interface with the WDC API. However, you can submit questions and ask for help on the [Tableau developer community forums](https://community.tableau.com/community/developers/content). 

Tableau *does* provide support the WDC library and SDK though. If you find an issue with the WDC library, the simulator, or any of the developer samples, [submit an issue on Github](https://github.com/tableau/webdataconnector/issues).

#### The global variables in my connector display as undefined. What's happening? 

The WDC API runs connectors in multiple phases, and each phase runs in a separate instances of a web browser. To pass data between phases, use the `tableau.connectionData` variable created by the API for this purpose. For more information, see the documentation for the [phases of the web data connector]({{ site.baseurl }}/docs/wdc_phases.html).

#### Why are my extract refreshes failing on Tableau Server?

To run extract refreshes for connectors, you need to configure Tableau Server and import the connector. Contact your server administrator and see the [Tableau Server documentation on web data connectors](http://onlinehelp.tableau.com/v0.0/server/en-us/help.htm#datasource_wdc.htm).

#### Can I refresh extracts for connectors on Tableau Online?

Because running custom code for a connector represents a security risk, there is currently no way to refresh extracts for connectors published to Tableau Online. As an alternative, you can use the [Tableau Online Sync Client](https://onlinehelp.tableau.com/current/online/en-us/to_sync_local_data.htm) to schedule extract refreshes on a desktop computer.

#### Where are the check boxes and radio buttons that I put in my connector page? I see them in the simulator, but not in Tableau.

This was an issue in a previous version of the WDC. You can link to the latest version of the WDC library [here](https://connectors.tableau.com/libs/tableauwdc-2.0.0-beta.js) to fix the issue.

#### I'm seeing differences between what I see in the simulator and what I see in Tableau. What's going on? 

Tableau Desktop embeds the Qt Webkit browser into the product to display your connector pages. This browser might lack some of the features of modern browsers, including specific HTML5 and other features. For more information on browser support, see the features in [Qt 5.4](https://wiki.qt.io/New_Features_in_Qt_5.4), which is the version used by Tableau. You may also want to see the Qt Webkit page on [HTML5 support](https://wiki.qt.io/Qt_Webkit_HTML5_Score). 

#### Where can I find the Tableau Desktop logs to troubleshoot my connector? 

By default, the Tableau Desktop log files are stored in the following location:

```
Users/<username>/Documents/My Tableau Repository
```

The `log.txt` file contains information for the interactive phase of your connector. The `tabprotosrv.txt` file contains logs for the data gathering phase. 

#### Why aren't my connector methods being called? 

The methods in your connector code are run by the WDC API. Ensure that you are running the connector in the simulator or Tableau. You might also want to ensure that the `tableau.submit` function is being called either by user input or by a page load event. 

