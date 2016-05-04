---
layout: page
title: WDC Lifecyle and Phases
base: docs
---

This document explains the overall lifecyle of a Tableau Web Data Connector. Understanding 
the material in this page will be extremely helpful in successfully
developing a web data connector.  It is recommended that you understand the material
from the Quick Start section before diving into this material.

A WDC is always run with an associated phase.  Tableau loads
the connector inside a web browser at different times and in distinct phases. 
This document will explain each of these phases and when each one runs.

-   [Lifecyle Diagram](#diagram)

-   [First phase: Interact with the user (if necessary)](#phase-one)

-   [Second phase: Gather data](#phase-two)

-   [Alternative phase: Display authentication UI
    (refresh extract)](#phase-three)

Lifecycle Diagram {#diagram}
--------------------------------------------------

At a high level, the WDC lifecycle is as follows.  The following sections will 
describe each part of this flow in detail.

<img class="img-responsive docs-img" src="{{ site.baseurl }}assets/wdc_flow.png" alt="">

**Note**: This is slightly simplified.
For example, shutdown and shutdownCallback were left out, but both 
of these events take place at the end of each phase as wel (the mirror to
init and initCallback).

Interactive phase: Interact with the user (if necessary) {#phase-one}
--------------------------------------------------

- Tableau launches the web data connector in the interactive phase.  This
    could be Tableau Desktop, or the Simulator.  
    
    In the interactive phase, the following actions occur:
    
    - The conncetor's init method is called by Tableau. A connector will have a default 
      init method if not provided by the WDC Developer (see [Custom
      Initilaiztion and Shutdown for more details]({{ site.baseurl }}docs/wdc_custom_init_and_shutdown.html)).
          
    - The connector calls the passed in initCallback to tell Tableau it has finished initialization.
    
    - The connector waits for interaction to be completed by the end user.  This could entail waiting for
      the user authenticate, waiting for the user to enter some sort of data in a form, and more.
      This is optional, if a connector has no need for user input, it can call submit() immediately.
      
    - The connector calls the function tableau.submit(), which tells Tableau that the connector
      is ready to finish the interactive phase.

Gather Data phase: fetch data from a web source {#phase-two}
-------------------------

- After the interactive phase has been completed, Tableau will launch the web data connector
    in a new, headless (meaning without UI) browser session.  The connector will now be in the 
    gather data phase.  This phase can be launched from Tableau Desktop, Tableau Server/Online,
    or the Simulator.
    
    In the gather data phase, the following actions occur:
    
    - The conncetor's init method is called by Tableau, just as it was in the interactive phase. 
      The connector may want to take different action in the init method in the gather data phase than
      it did in the interactive phase.  At any time, the current phase of the connector can be read
      through the [tableau.phase]({{ site.baseurl }}ref/ref_home.html#webdataconnectorapi.tableau.phase)
      property.
          
    - The connector calls the passed in initCallback to tell Tableau it has finished initialization.
    
    - Tableau first calls the 
      [getSchema]({{ site.baseurl }}ref/ref_home.html#webdataconnectorapi.webdataconnector.getschema)
      method, which you, the WDC Developer, define on your connector object.
      This method will tell Tableau what the schema is for any tables your
      connector returns.
      
    - The connector will will call schemaCallback with a schema object, which passes
      the connector's schema back to Tableau.
      
    - Tableau then calls the [getData]({{ site.baseurl }}ref/ref_home.html#webdataconnectorapi.webdataconnector.getdata) method of the connector to get the actual 
      data. The getData method will be called by Tableau once for each table that 
      has been selected by the end user. For example, if a user drags out two tables
      from the web data connector into the join canvas in Tableau, getData will be called
      twice.  
      
    - The getData method receieves a table object as a parameter.  In the getData
      method, the WDC needs to fetch data for that specific table from the web data source,
      and use the [table.appendRows]({{ site.baseurl }}ref/ref_home.html#webdataconnectorapi.table.appendrows) 
      method to pass data for that table back to Tableau.
      
    - Once all the data has been fetched for the current table, the WDC should call the passed
      in dataDoneCallback().  At this point, Tableau will go back and call getData for another 
      table if necessary.  If not, the WDC flow is completed and data can now be 
      analyzed within Tableau.

Auth phase: Display authentication UI when needed {#phase-three}
--------------------------------------------------------------

An alternative phase can occur if the connector is invoked in order to
refresh a data extract that the connector created. When an extract must
be refreshed, Tableau does not need to change the schema, but it might
need to make a call to the external data source for authentication.
(This is not really a third phase, because it does not follow the other
two; it's an alternative to the first phase.)

If the connection uses basic or digest authentication, Tableau displays
a dialog box that asks the user for a user name and password. However,
if the connector is using OAuth for authentication, and if the OAuth
token has expired when the extract is refreshed, Tableau invokes the web
data connector in the authentication phase. In this mode, the connector
should display only the UI that is required in order to get an updated
token.  Updates to properties other than tableau.username and tableau.password
will be ignored during this phase. 

Please see [WDC Authentication]({{ site.baseurl }}docs/wdc_authentication.html)
for more details on how the auth phase comes into play.