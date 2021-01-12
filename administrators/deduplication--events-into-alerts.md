---
title: Deduplication -- Events into Alerts
tags: [metrics, anomalies, events, Moogsoft]
keywords: metrics, anomalies, events, Moogsoft
last_updated: January 11, 2021
summary: "How Moogsoft deduplications events into alerts, and how to customize deduplication (_advanced_)."
sidebar: mydoc_sidebar
permalink: event-deduplication.html
folder: moogsoft
---

Once your workflows finish processing events, Moogsoft deduplicates the
events into
[alerts](glossary.html#UUID-d691664b-e5f0-e08d-8eea-13bd3c05133c_glossterm-idm93125404714298).
When it adds a new event to an alert, Moogsoft updates the alert with
the latest information from the event. Thus you can think of events as
hard-coded snapshots of an issue, while alerts get updated with each new
event.

This topic describes how Moogsoft removes duplicate events from the data
ingestion stream.

+--------------------------------------+--------------------------------------+
| ![express-data-pipeline-03-deduplica | [The Moogsoft data                   |
| tion.png](image/uuid-c1c9978f-b23f-1 | pipeline](data-pipeline.html "The Mo |
| f1b-1f35-61de62c77de6-en.png)        | ogsoft Data Pipeline")               |
|                                      |                                      |
|                                      | 1.  [Your Data: Events, Metrics,     |
|                                      |     Anomalies, and                   |
|                                      |     Security](your-data--events,-met |
|                                      | rics,-anomalies,-and-security.html " |
|                                      | Your Data: Events, Metrics, Anomalie |
|                                      | s, and Security")                    |
|                                      |                                      |
|                                      | 2.  [Enriching Events with External  |
|                                      |     Data](enriching-events-with-exte |
|                                      | rnal-data.html "Enriching Events wit |
|                                      | h External Data")                    |
|                                      |                                      |
|                                      | 3.  Deduplication: events into       |
|                                      |     alerts                           |
|                                      |                                      |
|                                      | 4.  [Correlation: Alerts into        |
|                                      |     Incidents](correlation--alerts-i |
|                                      | nto-incidents.html "Correlation: Ale |
|                                      | rts into Incidents")                 |
|                                      |                                      |
|                                      | 5.  [Incident Analysis and           |
|                                      |     Notifications](incident-analysis |
|                                      | -and-notifications.html "Incident An |
|                                      | alysis and Notifications")           |
+--------------------------------------+--------------------------------------+

#### Deduplication and noise reduction {.title}

A busy service with multiple monitors can generate a flood of metrics,
anomalies, and events. One issue might trigger a large number of repeat
and duplicate events. Moogsoft analyzes every new piece of data — What
is this? When did it happen? What is its severity? How often has it
happened before? — and aggregates events for the same issue into
[alerts](glossary.html#UUID-d691664b-e5f0-e08d-8eea-13bd3c05133c_glossterm-idm93125404714298).
Whenever it adds a new event, Moogsoft updates the alert fields — event
count, last event time, severity — so the alert always contains the
latest information about the underlying issue. This process removes the
duplicate, repeat, and obsolete noise from the data stream.

#### How deduplication works {.title}

Moogsoft converts each new event notification and metric anomaly into a
generic JSON object that describes a specific performance-related event:
what happened, when it happened, where it happened, and so on.

Moogsoft then adds each new event to an
[alert](glossary.html#UUID-d691664b-e5f0-e08d-8eea-13bd3c05133c_glossterm-idm93125404714298).
An *event* describes one specific event; an *alert* describes a set of
events that all relate to the same issue. For example, an alert might be
"High CPU load on server 23." and consist of the following events:

1.  server 23, 12:00: CPU load = 72%

2.  server 23, 12:01: CPU load = 80%

3.  server 23, 12:02: CPU load = 67%

Moogsoft adds new events to alerts as follows:

1.  A new JSON event object arrives at the ingestion endpoint.

2.  Moogsoft generates a *dedupe key* for the event based on the
    `source`{.code}, `service`{.code}, and `check`{.code} fields in the
    event.

3.  Moogsoft compares the new event with each open alert using the
    dedupe key.

    -   If the dedupe key matches an open alert, Moogsoft increments the
        alert's `event count`{.code} field and updates the
        `severity`{.code} field based on the new event.

    -   If the dedupe key does not match any open alert, Moogsoft
        creates a new alert and adds the event to it.

See also [Event deduplication: how-to and best
practices](event-deduplication.html "Event deduplication: how-to and best practices")

* * * * *

  --------------------------------------------------- ----------------------------- --------------------------------------------------
  [Prev](enriching-events-with-external-data.html)    [Up](product-overview.html)    [Next](correlation--alerts-into-incidents.html)
  Enriching Events with External Data                 [Home](index-en.html)          Correlation: Alerts into Incidents
  --------------------------------------------------- ----------------------------- --------------------------------------------------
