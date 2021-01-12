---
title: Customizing Anomaly Detection for Individual Metrics (*Advanced*)
tags: [metrics, anomalies, events, Moogsoft]
keywords: metrics, anomalies, events, Moogsoft
last_updated: January 11, 2021
summary: "How to customize anomaly detection for individual metrics."
sidebar: mydoc_sidebar
permalink: customizing-anomaly-detection-for-individual-metrics--advanced-.html
folder: moogsoft
---

Moogsoft includes a default set of anomaly detection engines and
supported metrics. Each supported metric has a set of anomaly-detection
settings that you can configure as needed.

## Viewing and editing metric settings

You can view and edit metric settings in two places:

-   Choose **Data Config** \>
    [**Integrations**](ingest-source-data.html "Ingest Source Data") and
    go to the the integration: collector, CloudWatch, etc. Then click
    **Configuration** and drill down to the metric of interest.

-   Choose **Observe** \>
    [**Metrics**](view-metrics.html "View metrics"), select the metric
    of interest, and then configure the anomaly detection settings in
    the [data point
    table](view-metrics.html#UUID-0f568e1e-5c1f-2440-44b8-6f14975e0eff_section-5d362a25d5811-idm46391458293168 "Metric chart reference")
    on the right.

## Before you begin

Before you change the default anomaly-detection settings for any
specific metric, you should clearly identify and understand the
following:

-   The criteria you want to define for normal and anomalous behavior
    for the metric of interest,

-   The detection engines and the configuration settings described on
    this page, and

-   The effects of changing the default anomaly-detection behavior for
    the metric of interest.

It is also good practice to monitor the affected metric closely after
you change the detector to ensure that you are getting the detection
behavior you want.

## Anomaly Detectors

Moogsoft includes the following detectors.

-   **[Adaptive
    detector](customizing-anomaly-detection-for-individual-metrics--advanced-.html#UUID-421c5cdd-6184-b68a-2053-22854b55a499_section-5d3f8cc46a58c-idm45315537479280 "Adaptive detector")**
    — Useful for metrics with consistent ranges in normal conditions.

-   **[Threshold
    detector](customizing-anomaly-detection-for-individual-metrics--advanced-.html#UUID-421c5cdd-6184-b68a-2053-22854b55a499_section-5d3f8c41e2658-idm45764250070352 "Threshold detector")**
    — Useful for metrics with fixed thresholds for normal vs. anomalous
    behavior.

-   **[Bitwise / false
    detectors](customizing-anomaly-detection-for-individual-metrics--advanced-.html#UUID-421c5cdd-6184-b68a-2053-22854b55a499_section-5d66b4d3b3920-idm46324031216416 "Bitwise / false detectors")**
    — Useful for binary metrics such as "System Up/Down."

### Adaptive detector

The Adaptive detector identifies anomalies based on a statistical
calculation against a median absolute deviation, which varies over time
and determines the high and low thresholds. This detector is useful for
metrics where performance does not deviate widely under normal
conditions. For example, you might want to observe a specific server and
detect sudden spikes or drops in CPU utilization that indicate possible
problems with the OS, platform, or mission-critical apps running on that
server.

Most supported metrics in Moogsoft use the Adaptive detector by default.

### Threshold detector

The Threshold detector identifies anomalies based on a fixed upper
and/or lower threshold. This detector is useful for metrics where you
know the thresholds for normal and anomalous behavior for a specific
host or platform, and these thresholds do not change over time. For
example, suppose you want to identify anomalies in the amount of free
physical memory on a specific server. You might define a lower bound of
10%, to signal the server might be running out of memory; and an upper
bound of 90%, to indicate possible problems with the mission-critical
apps running on that server.

![[Note]](images/note.png) Few metrics use the Threshold detector by default. The criteria for
normal and anomalous behavior can be highly variable and dependent on
the specific metric, monitored host, and other factors. You should
carefully consider the thresholds for the metric and host of interest to
avoid false positives and false negatives in the anomaly-detection
behavior.

### Bitwise / false detectors

Bitwise and false detectors identify anomalies in a bitmask or Boolean
metric. These detectors are provided to support metrics that evaluate
whether a system is running correctly. You cannot configure these
detectors or use them for custom metrics.

## Anomaly detection settings

You can customize how Moogsoft detects anomalies and collects metric
data for an individual metric. Not all of these options are configurable
for all metrics.

-   **[Anomaly
    Detector:](customizing-anomaly-detection-for-individual-metrics--advanced-.html#UUID-421c5cdd-6184-b68a-2053-22854b55a499_UUID-b310e050-ed20-f420-0c95-8832e8fb6641 "Anomaly Detectors")**
    In some cases, you can change the anomaly engine for a specific
    metric.

-   **Determine Severities:** Choose whether to use calculated
    confidence to determine severity or set the number of deviations for
    each severity value.

-   **[Deviations:](customizing-anomaly-detection-for-individual-metrics--advanced-.html#UUID-421c5cdd-6184-b68a-2053-22854b55a499_section-idm231922755605311 "Deviations")**
    The number of standard deviations from the norm to determine if a
    data point is anomalous.

-   [**Hold
    for**](customizing-anomaly-detection-for-individual-metrics--advanced-.html#UUID-421c5cdd-6184-b68a-2053-22854b55a499_section-5dacaae64ceea-idm45304085265104 "Hold for")
    The number of anomalous data points to observe before generating an
    anomaly event.

-   **Reset hold for:** When a metric is in an anomaly state, this
    setting determines the number of non-anomalous data points to hold
    for before resetting the metric severity to Clear.

-   **Learning:** The number of datums to collect before anomaly
    detection begins.

-   **[Minimum
    Deviation:](customizing-anomaly-detection-for-individual-metrics--advanced-.html#UUID-421c5cdd-6184-b68a-2053-22854b55a499_section-idm231853502086205 "Minimum deviation")**
    The minimum possible deviation used to calculate anomalies, based on
    the historic range of values. This setting is useful for metric data
    sets with very small ranges.

-   **Samples:** Number of datums to keep in memory to determine the
    threshold ranges.

-   [**Stateful:**](customizing-anomaly-detection-for-individual-metrics--advanced-.html#UUID-421c5cdd-6184-b68a-2053-22854b55a499_N1607715385153)
    Generate an anomaly only when the metric changes state (True) or for
    every anomalous data point (False)

-   **Symmetric:** By default, the Adaptive detector calculates upper
    and lower sigma values to determine anomalies above and below the
    metric mean. Enable this to calculate the same sigma value for
    anomalies in both directions.

-   **Vector**: (*Threshold detector only*) Consider Low Threshold or
    High Threshold only, or both High and Low thresholds to identify
    anomalies.

-   **Window** : The number of data points sent to Moogsoft and
    displayed before and after each anomaly.

### Hold for

The number of anomalous data points to hold for until generating an
event. Suppose **Hold for** = 1. When a metric generates an anomaly, the
detector holds for one more anomaly before it generates an event.

The **Hold For** window is 1 by default for most supported metrics. You
might want to increase this number for a specific metric in the
following corner case:

-   You want to reduce the "noise" for a metric that generates a lot of
    repeat anomalies. This can happen if a metric has a very short
    polling cycle and moves frequently between normal and anomalous
    range. This can cause the detector to generate a series of
    repetitive anomalies that say, in essence, "this metric is
    constantly switching between normal and anomalous range."

![[Note]](images/note.png) If you increase the **Hold For** window, the detector might miss some
anomalies that do not generate enough consecutive data points to trigger
an anomaly event.

Consider the following metric, which switches between anomalous and
normal states every 2 minutes or so. When hold-for and reset-hold-for
are both set to the default of 1, this results in a spurt of anomalies.

![hold-for-eq\_1-v2.png](image/uuid-08860dc0-692c-f0a0-b277-1721c5766d54-en.png)

You might decide that this is normal behavior, and that you only want to
generate anomalies when the metric is in anomalous state for 3 minutes
or more. In this case, set hold-for and reset-hold-for to 2 or higher.

![hold-for-eq\_2-v2.png](image/uuid-4037e340-05f4-e20b-a512-02231b0146de-en.png)

### Stateful

When True, generate an anomaly only when the metric changes state: when
it enters an anomalous state, when its value changes significantly while
in an anomalous state, or when it returns to a normal state.

When False, generate an anomaly for every anomalous data point.

The **Stateful** setting is True by default. You might want to set this
to False for a specific metric in the following corner cases:

-   You are sending anomaly data to Moogsoft and you need to consider
    every anomalous data point for your AIOps analyses.

-   You are receiving anomaly notifications using the Slack integration,
    and a metric of interest has a very long polling cycle. You might
    want to set Stateful to False for this metric and thereby ensure
    that you continue receiving Slack notifications until the metric
    performance returns to normal.

### Minimum deviation

The minimum possible deviation used to calculate anomalies, based on the
historic range of values. This setting is useful for metric data sets
with very narrow ranges.

Consider the following metric, where the value remains within 1.0 and
1.05 nearly all the time. If the range of values is very narrow, even
tiny deviations can result in "false-positive" anomalies.

  --------------------------------------------------------------------------------------
  ![minimum-deviation-OFF.png](image/uuid-65e99498-a7cb-411f-58c0-fd09bd2f94f1-en.png)
  --------------------------------------------------------------------------------------

With the minimum deviation set to 0.3, only values outside of 0.3 times
the mean (plus or minus) are considered for anomaly detection.

  -------------------------------------------------------------------------------------
  ![minimum-deviation-ON.png](image/uuid-908d421b-6bc9-672c-0737-cda3534e7f23-en.png)
  -------------------------------------------------------------------------------------

### Deviations

The number of standard deviations to determine if a data point is
anomalous. You might want to change this setting in the following cases:

-   A metric changes frequently and over a wide range, which causes the
    detector to flag non-anomalous data points as "false-positive"
    anomalies. In this case you might want to increase the number of
    deviations.

-   A metric changes very little and a data point outside the norm, even
    by a small amount, indicates an anomaly. In this case you might want
    to decrease the number of deviations.

![[Note]](images/note.png) Changing the number of deviations can affect anomaly detection
dramatically. As with any change to an anomaly detector, you should
closely monitor the metric after you apply the change to ensure that you
are getting the detection behavior you want.

Consider the following metric, where the number of deviations is set to
4.

![deviations-eq-4.png](image/uuid-3e15673e-e5dd-8c8b-9a5b-ee2bb582ff37-en.png)

If we lower the number of deviations to 2, more data points are now
considered anomalous.

![deviations-eq-2.png](image/uuid-d3af6bd6-0b4e-ce60-a148-635dc9019a02-en.png)

If we raise the number of deviations to 8, more data points are now
considered non-anomalous.

![deviations-eq-8.png](image/uuid-d3092ebc-51cb-39d0-1656-d6abe5e3a314-en.png)

##  Watch a video

Watch a video on **Anomaly Detection Models** in Moogsoft.

Watch how to **Analyze Data** in Moogsoft.

* * * * *

  -------------------------------------------------- ------------------------------------------------- -----------------------------------------------------------
  [Prev](send-alerts-to-moogsoft-enterprise.html)    [Up](administrators---setting-up-moogsoft.html)    [Next](operators---viewing-and-analyzing-your-data.html)
  Send Alerts to Moogsoft Enterprise                 [Home](index-en.html)                              Operators — Viewing and Analyzing your Data
  -------------------------------------------------- ------------------------------------------------- -----------------------------------------------------------
