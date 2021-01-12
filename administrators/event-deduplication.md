---
title: "Event deduplication: how-to and best practices"
tags: [enrichment, workflows]
keywords: enrichment, workflows
last_updated: December 10, 2020
summary: "How to enrich events with additional data"
sidebar: mydoc_sidebar
permalink: event-deduplication.html
folder: moogsoft
---

Deduplication is the process of identifying duplicate events and
combining them into alerts. Deduplication reduces the number of alerts
in the system. It is good practice to format your events to optimize
deduplication. In some cases you might also want to customize and
further optimize the default behavior.

##### How it works: the dedupe key {.title}

When Moogsoft ingests a raw event, it compares the new event with all
open alerts. Does an open alert already describe the same issue, on the
same node and service?

-   If there is a matching alert, Moogsoft flags the new event as a
    duplicate and updates the alert.

-   If there is no matching alert, Moogsoft creates a new alert.

Each incoming event has a `dedupe_key`{.code} field. By default,
Moogsoft autogenerates this key based on the `source`{.code},
`service`{.code}, and `check`{.code} fields in the event itself. (The
dedupe key also includes `class`{.code} if an event includes this
field.) The dedupe key defines the context shared by all events that
belong to the same alert. 

##### An Example {.title}

Suppose a series of events all describe response times for the same
microservice on the same host. Although their timestamps, descriptions,
and severities differ, all these events have the same key:
`source`{.code} = server 23, `service`{.code} = db-query-svc,
`check`{.code} = response-time.

The following sequence illustrates how Moogsoft deduplicates these
events:

1.  The first event arrives with `description`{.code} =
    “db-query-response-time \> 400 ms”  and `severity`{.code} = minor.

2.  Moogsoft compares this event against all open alerts. There is no
    open alert with the same key. Moogsoft creates a new alert based on
    the new event.

3.  The second event arrives with `description`{.code} =
    “db-query-response-time \> 600 ms”  and `severity`{.code} = major.

4.  Moogsoft compares the new event with the alert it just created. The
    event and alert have the same key. It updates the alert fields with
    the new event information:

    -   `Event count`{.code} = 2 (was 1)

    -   `Last event time`{.code} = new event time (was previous event
        time)

    -   `Severity`{.code} = major (was previous event severity)

    -   `Description`{.code} = “db-query-response-time \> 600 ms” (was
        previous event description)

5.  The response time remains high and several more events arrive with
    varying levels of severity. With each event, Moogsoft updates the
    alert as described previously.

6.  Finally, the response time falls to within acceptable levels. An
    event arrives with `description`{.code} = “db-query-response-time \<
    200 ms”  and `severity`{.code} = clear.

7.  Moogsoft updates the alert as described previously. Because the
    status of this alert is now clear, any new events with the same
    dedupe key get added to a new alert.

##### Best practices {.title}

If two events involve different problems on the same source and service,
they should belong to different alerts. For this reason, the
`check`{.code} field should be fairly specific. Suppose two events
describe database errors on the same source : one is a replication error
and another is a query error. If `check`{.code} = “database” for both
events, they will get deduplicated into the same alert. The better
choice is to use “database-replication” for the first check and
“database-query” for the second.

##### Customizing deduplication (advanced) {.title}

To customize the deduplication behavior, you can explicitly define the
`dedupe_key`{.code} in the raw events you send to Moogsoft. The
following use cases provide some examples of when you might want to
customize your deduplication:

-   You want to generate alerts for a service that runs in multiple
    containers. You’re primarily concerned about the overall performance
    of the service rather than specifics of the container in which the
    service runs. In this case, you can specify a dedupe key such as
    `my-service::user-response-time`{.code}.

-   You want to deduplicate based on information that is not included in
    the default fields. For example, you might want to deduplicate all
    events that have a specific error code. In this case, you could
    define a dedupe key such as
    `my-source::my-service::my-check::err3059`{.code}.

###### Do’s and Don’ts for custom deduplication {.title}

A dedupe key is made up of a subset of event properties. Different types
of events require different keys. A perfect dedupe key contains just
enough information to identify the context of an event.

In most cases a combination of the following values tends to work well:

-   Source, such as hostname

-   Event type or class

-   Static unique IDs

-   Error codes

-   Impacted entities

Do NOT include fields that might change between events with the same
context. For example:

-   Timestamp --  Every event has a different timestamp. If the dedupe
    key includes the timestamp, deduplication is effectively disabled.

-   State changes such as up or down

-   Event count

-   Variable unique IDs

-   Severity

-   Descriptions with changing content such as metrics

When you define a custom dedupe key, concatenate multiple fields with
two colons "::" to prevent misleading results. For example, if you
concatenate source "Node A" and unique ID "1234" as "NodeA1234" this
could potentially also match Node A1 and unique ID 234.

* * * * *

  -------------------------------------------------- ---------------------------------------------------------------------- -----------------------------------------------
  [Prev](enrich-events-with-additional-data.html)    [Up](enrich-and-deduplicate-events-into-alerts-using-workflows.html)    [Next](correlate-alerts-into-incidents.html)
  Enrich Events with Additional Data                 [Home](index-en.html)                                                   Correlate Alerts into Incidents
  -------------------------------------------------- ---------------------------------------------------------------------- -----------------------------------------------
