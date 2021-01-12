---
title: Administrators — Setting up Moogsoft
tags: [administrators, setup]
keywords: administrators, setup, Moogsoft
last_updated: January 11, 2021
summary: "How to set up Moogsoft"
sidebar: mydoc_sidebar
permalink: admin-setup.html
folder: moogsoft
---

* * * * *

Administrators — Setting up Moogsoft
------------------------------------

The following steps describe the high-level workflow for setting up
Moogsoft.

1.  Install the [Moogsoft Collector](collectors.html "Collectors") on
    your Linux and Windows servers.

    To install the collector, you download and run an auto-generated
    script. You can then immediately see metrics and anomalies in the
    UI.

2.  Set up your other data ingestions as needed:

    -   If you use AWS CloudWatch to monitor your environment, set up
        the [CloudWatch
        integration](aws-cloudwatch-integration.html "AWS CloudWatch integration").

    -   If you use Datadog to monitor your environment, set up the
        [Datadog
        integration](datadog-integration-mcp.html "Datadog integration").

    -   If you use AppDynamics to monitor your environment, set up the
        [AppDynamics
        integration](appdynamics-integration.html "AppDynamics Integration").

    -   If you use New Relic to monitor your environment, set up the
        [New Relic
        Integration](new-relic-integration.html "New Relic Integration").

    -   If you use Zabbix to monitor your environment, set up the
        [Zabbix
        Integration](zabbix-integration.html "Zabbix Integration").

    -   Set up other external monitoring tools to send metrics and
        events to Moogsoft.

        The [Metrics API](metrics-api.html "Metrics API") and [Events
        API](events-api.html "Events API") can ingest data from any
        external systems that can generate and send JSON objects with
        the required schema.

    -   For external systems that do not generate data with the required
        schema, you can also [create your own
        integrations](create-your-own-integration.html "Create your own integration")
        that automatically translate third-party data to Moogsoft events
        and metrics.

3.  Enrich your raw data with information to correlate your alerts into
    incidents, and to help your operators analyze and troubleshoot your
    incidents. See [Data
    Enrichment](enrich-and-deduplicate-events-into-alerts-using-workflows.html "Enrich and Deduplicate Events into Alerts using Workflows").

4.  Define the correlation logic to cluster your alerts into meaningful
    incidents.

    To define your correlations, you specify the alert fields and
    similarity thresholds to determine if two alerts are correlated. See
    [Correlation
    Engine](correlate-alerts-into-incidents.html "Correlate Alerts into Incidents").

5.  Add users to the instance as needed. See [Managing Moogsoft
    Users](user-management.html "Managing Moogsoft Users").

6.  If you use notification tools such as Slack and PagerDuty, you can
    set up outbound integrations so you get notified of new alerts or
    incidents. You can also send alerts to Moogsoft Enterprise and use
    the correlation, notification, and ticket features in that product:

    -   [Send Notifications using
        PagerDuty](pagerduty-integration.html "Send Notifications using PagerDuty")

    -   [Send Notifications to
        Slack](send-notifications-to-slack.html "Send Notifications to Slack")

    -   [Send Alerts to Moogsoft
        Enterprise](send-alerts-to-moogsoft-enterprise.html "Send Alerts to Moogsoft Enterprise")

* * * * *

  --------------------------------------------------- ----------------------- ----------------------------------
  [Prev](incident-analysis-and-notifications.html)                             [Next](ingest-source-data.html)
  Incident Analysis and Notifications                 [Home](index-en.html)    Ingest Source Data
  --------------------------------------------------- ----------------------- ----------------------------------
