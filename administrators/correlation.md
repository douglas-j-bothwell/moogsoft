---
title: "Correlating Alerts into Incidents"
tags: [correlating]
keywords: correlation, concepts, examples
last_updated: December 10, 2020
summary: "Intro to the Correlation Engine, with an example."
sidebar: mydoc_sidebar
permalink: correlation.html
folder: moogsoft
---

<!--
xml version="1.0" encoding="UTF-8" standalone="no"? html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"    Correlate Alerts into Incidents            Correlate Alerts into Incidents   [Prev](enrich-and-deduplicate-events-into-alerts-using-workflows/event-deduplication.html)  Administrators — Setting up Moogsoft  [Next](correlate-alerts-into-incidents/defining-correlations.html)         ### Correlate Alerts into Incidents
-->

_Correlation_ is the process of clustering related alerts into incidents. Moogsoft uses correlation definitions that specify the data fields of interest to determine if an alert and incident are correlated. To define an effective correlation, you need to determine the following:


1.  How you want to correlate your alerts — such as by node, service, or location.


1.  The alert fields in your data that contain the relevant information.



#### Correlation example: how it works

This example illustrates how Moogsoft clusters alerts with just a few high-level bits of information from you.

You're a DevOps engineer responsible for setting up Moogsoft. You're using Datadog, AWS CloudWatch, and Moogsoft collectors to monitor your infrastructure. You go to the [Correlation editor](correlate-alerts-into-incidents/defining-correlations.html "Defining Correlations") and create a new correlation.

You want to cluster application, system, cloud, and network alerts for the same service in the same location. When prompted for the alert fields to correlate, you select the following:


*  service field, with 80% similarity. The 80% similarity helps cluster alerts with slight variations on the same service name.


*  location field, with 100% similarity. You want to make sure that all the alerts in each incident are from the same location.

![correlation-example-screenshot.png](images/uuid-9dbfccbc-c06b-5855-e147-a728aac5ac8c-en.png)  

You accept the defaults for the other options. You click Save and the correlation engine processes new alerts using your correlation:


    1.  AWS CloudWatch sends an alarm to the Events API: inbound network traffic is high on cntnr23.


    1.  The alarm passes through the [enrichment](../product-overview/enriching-events-with-external-data.html "Enriching Events with External Data") and [deduplication](../product-overview/deduplication--events-into-alerts.html "Deduplication: Events into Alerts") pipelines.


    1.  The alarm arrives at the correlation engine as Alert 100 with class = Network, service = dbQuery\_1.0, and location = us-east-1.


    1.  Your correlation filter allows for class = Network, so this is a candidate for correlation. The engine creates a new incident based on this alert. As yet there are no correlations with any other alerts.     
    ![v3-correlation-example-01.png](images/uuid-26975ddd-a4c9-92ad-13ce-00bff479baf6-en.png)  


1.  AWS CloudWatch flags an anomaly and sends it to the Metrics API: a spike in 4xx (bad requests) at a specific endpoint.


    1.  The anomaly passes through the pipeline and arrives at the correlation engine as Alert 101 with class = API.


    1.  The correlation scope does not include class = API, so this alert is not a candidate for correlation.

1.  A Moogsoft collector flags an anomaly and sends it to the Metrics API: free memory is down on cntnr00.


    1.  The anomaly passes through the pipeline and arrives at the correlation engine as Alert 102 with class = System, service = wsfe\_1.0, and location = us-west-1.


    1.  The alert passes your correlation filter, so the engine creates a new incident. Again there are no correlations based on your definitions.
    ![v3-correlation-example-02.png](images/uuid-3931f853-12a1-efc7-5b9c-b73749385714-en.png)   

1.  The anomaly detector flags another anomaly in the Datadog metrics: HTTP response times are spiking on cntnr01.


    1.  The anomaly arrives at the correlation engine as Alert 103 with class = Application, service = custLogin\_1.0, and location = us-west-1.


    1.  The engine finds that Alerts 102 and 103 are correlated based on your definition: Their services meet the 80% similarity threshold ( **custLogin\_1.0** and **wife\_1.1**) and their locations are both **us-west-1**.


    1.  The engine adds Alert 103 to Incident 2.     ![v3-correlation-example-03.png](images/uuid-3d2b14ad-a0e1-3b81-7f7f-946c8b268701-en.png)


1.  The anomaly detector flags two CloudWatch metrics for another container: CPU utilization and disk write operations are significantly down on containers running the customer-login service.


    1.  Alert 104 arrives with class = Cloud, service = custLogin\_1.1, and location = us-east-2.


    4.  This alert passes the filter but does not correlate with any open alerts. The engine creates Incident 3 with alert 104.


    6.  Alert 105 arrives with class = Cloud, service = cust-login\_1.2, and location = us-east-2.


    8.  The engine finds that alerts 104 and 105 are correlated based on their similar service names and identical locations. The engine adds alert 105 to incident 3.   
    ![v3-correlation-example-04.png](images/uuid-9b3b22fe-f07a-e612-2157-8b68eba6ed76-en.png)  

1.  Another 15 minutes go by. Moogsoft ingests more alerts and adds them to incidents based on your correlation. Another alert arrives from CloudWatch, noting that network traffic is still high on cntnr23.

    2.  The alarm arrives at the correlation engine as Alert 117 with class = Network, service = dbQuery\_1.0, and location = us-east-1.

    4.  This alert matches your correlation filter. It also meets the source and location similarities with Alert 100 in incident 1.

    However, the correlation time window for incident 1 has expired. Each correlation has a configurable time window, which is set to 15 minutes by default.


    6.  The engine creates a new incident and adds alert 117 to it.  

     ![v3-correlation-example-v5.png](images/uuid-7e566aa2-86d1-ab5f-36fb-cd0d5d4dbd9f-en.png)  

Watch a video on the **Correlation Engine in Moogsoft**.

<!--
iframe instead of embed for d:videodata           [Prev](enrich-and-deduplicate-events-into-alerts-using-workflows/event-deduplication.html)   [Up](../administrators---setting-up-moogsoft.html)   [Next](correlate-alerts-into-incidents/defining-correlations.html)   Event deduplication: how-to and best practices   [Home](../index-en.html)   Defining Correlations      
-->
