---
title: "Enrich Events with Additional Data"
tags: [enrichment, workflows]
keywords: enrichment, workflows
last_updated: December 10, 2020
summary: "How to enrich events with additional data"
sidebar: mydoc_sidebar
permalink: enrich-events-with-additional-data.html
folder: moogsoft
---


####  {.title}

Moogsoft ingests raw
[events](glossary.html#UUID-d691664b-e5f0-e08d-8eea-13bd3c05133c_glossterm-idm93125404758188)
based on alert notifications and metric anomalies. In most cases, you
will want to enrich your events with additional data after ingestion.
Enrichment has the following benefits:

-   You want to fine-tune how Moogsoft clusters your alerts into
    incidents.

    You can enrich sources with information about their associated
    clusters, apps, services, teams, locations, and so on. You can
    leverage data from a CMDB or other central repository to define the
    relationships between different nodes. Once you define these
    relationships in your enrichment data, you can define a simple,
    smart correlation pattern to cluster your alerts.

-   You want to make your alerts more informative and readable.

    In some cases, your raw events and metrics might not include all the
    information necessary for a user to investigate and troubleshoot an
    Incident.

-   You want to normalize events that come from different sources and
    have different formats.

    For example, one event stream uses IPs as the source while another
    stream uses domain names. You can use enrichment to ensure that all
    events are formatted consistently. This can make deduplication and
    correlation much simpler.

Watch how to **Add External Data to the Events** in Moogsoft.

##### Event Workflows {.title}

*Event workflows* provide enrichment for events. An event workflow is a
user-defined, fully-automated sequence of
[actions](enrich-events-with-additional-data.html#UUID-9054b34a-6b03-8cce-cd3a-e9d049f6e603 "Event Workflow Actions")
applied to each new event:

1.  A new event arrives at the workflow engine, which triggers the
    workflow.

2.  Each workflow has an initial
    [trigger](enrich-events-with-additional-data.html#UUID-6515cfc7-a6a2-855b-b79a-390f2d4aee87 "Workflow trigger"),
    which is an event filter that specifies the events that the workflow
    will process.

    -   If the event does not pass the trigger filter, the workflow
        exits.

    -   If the workflow passes the filter, the workflow proceeds to the
        next step.

3.  The event passes through a series of
    [actions](enrich-events-with-additional-data.html#UUID-9054b34a-6b03-8cce-cd3a-e9d049f6e603 "Event Workflow Actions")
    that enhance and update the data in the event.

    A workflow can
    [enrich](enrich-events-with-additional-data.html#UUID-9ff5a77d-5132-d686-c386-f5421f9ce3eb "Query Catalog action")
    events with data from external
    [catalogs](enrich-events-with-additional-data.html#UUID-a2aa1727-a357-7bb3-c6b2-e4e8b38c0032 "Creating Data Catalogs").
    You can also create workflows that
    [update](enrich-events-with-additional-data.html#UUID-04acf6c2-2289-95d3-8787-429cc9a2c948 "Match and Update action")
    [fields](enrich-events-with-additional-data.html#UUID-02d450d6-cee1-0cdc-1f0b-8f28e8752859 "Template Field Action")
    [in](enrich-events-with-additional-data.html#UUID-f03dc6c0-b441-6ae6-3889-b31a9815de4a "Extract Substring action")
    [an](enrich-events-with-additional-data.html#UUID-b41cc92c-92cc-98e4-5d54-ef5b0b62c5ff "Parse FQDN action")
    [event](enrich-events-with-additional-data.html#UUID-516fa146-64c0-62fe-5dbd-bd0ffdd28960 "Split action")
    based on other fields in the same event.

4.  Once the event passes through all
    [actions](enrich-events-with-additional-data.html#UUID-9054b34a-6b03-8cce-cd3a-e9d049f6e603 "Event Workflow Actions")
    in all relevant workflows, the data pipeline does the following:

    1.  [Deduplicates](event-deduplication.html "Event deduplication: how-to and best practices")
        the event into an alert.

    2.  Sends the alert to the [correlation
        engine](correlate-alerts-into-incidents.html "Correlate Alerts into Incidents").

The [Workflow Engine
UI](enrich-events-with-additional-data.html#UUID-3dd6be18-2411-2f1c-2daf-20c9745b915d "Creating Event Workflows")
(**Data Config** \> **Workflow Engine**) provides a simple drag-and-drop
interface for creating workflows.

Watch how to add a **Custom Process to Events** in Moogsoft.

##### Event Enrichment Example: How it Works {.title}

This example illustrates how you can define workflows to format your raw
events and enrich them with additional data from your environment.

Suppose you have a brewAPM service that observes performance on all
nodes for a specific app. The service sends events to Moogsoft that look
like this:

``` {.programlisting}
'{
    "description": "RT > 500 msec",
    "severity": 5,
    "source": "brewAPM",
    "check": "api",
    "service" : [ "REST" ],  
    "tags": {
        "labels": ["ip=172.31.17.101,hostName=websrv01.us=west.myorg.org, id=125989934839832182"]
    },
}'
```

Your raw events have the following issues:

1.  The `source`{.code} field describes the service that generated the
    event, not the host where the event occurred.

2.  The hostname where the event occurred is embedded in the
    `labels`{.code} tag.

3.  The `service`{.code} value is generic: it describes the type of
    service but not the service name.

Given this information, you want an event workflow that does the
following:

1.  Extracts the hostname from the labels tag,

2.  Copies the hostname to the source field,

3.  Uses the hostname to look up an entry from an external catalog, and
    finally

4.  Copies the service name from the catalog entry to the service field
    in the alert.

###### Defining the workflow {.title}

To create a new workflow, choose **Data Config** \> **Workflow Engine**
and then click **Add Workflow**. This opens the Workflow Editor with an
empty workflow.

Every workflow consists of a *trigger* and one or more *actions*. The
trigger is a filter that defines the events that the workflow will
process. Once an event passes the trigger, each action processes the
event in sequence. To create the desired workflow, you do the following:

###### Create the data catalog {.title}

To enrich your events, begin by creating one or more catalogs. A catalog
is a collection of data from your environment, formatted in a tabular
format. The Query Catalog action maps data from a catalog to new events
in a workflow.

The simplest way to create a catalog is to define your enrichment data
in a CSV and upload it. For every row in the CSV, Moogsoft creates a
*document* in the catalog. See [Creating Data
Catalogs](enrich-events-with-additional-data.html#UUID-a2aa1727-a357-7bb3-c6b2-e4e8b38c0032 "Creating Data Catalogs").

###### Create the workflow {.title}

Choose **Data Config** \> **Workflow Engine** and then click **Add
Workflow**. A new workflow appears with a single element. Every workflow
has a trigger, which defines the events that trigger the workflow.

![wf-example-00.png](image/uuid-7e877601-4d52-f543-ba68-541353b2dfa0-en.png)

###### Define the workflow trigger {.title}

The trigger is basically an event filter. The workflow only processes
events that pass this filter. You double-click on the trigger and then
specify the types of events that you want the workflow to process. You
want this workflow to process events from the brewAPM process, so you
specify the following filter: `source = brewAPM`{.code}.

###### Action 1: Update the `source`{.code} field {.title}

To add an action, drag it into the workspace and click on it. In this
case you add the Extract Substring action, which applies a regex to one
event field and copies the result to another.

![wf-example-01a.png](image/uuid-cc738406-b677-50f7-3633-5eaf6f635414-en.png)

You want this action to extract the hostname from the `labels`{.code}
tag and copy it to the `source`{.code} field. You extract the hostname
using a regex.

![wf-extract-string.png](image/uuid-4bfdabf0-d71b-682d-689a-6f36d7285e69-en.png)

![[Note]](images/note.png)

Note

There are three types of input and output field:

-   A base field that is already defined in the [Events
    API](events-api.html "Events API") schema

-   An existing tag that has been ingested in a previous event

-   A new tag that has not been ingested

###### Action 2: Map catalog data to the event {.title}

After the Extract String action processes an event, the event now has
the event FQDN as its `source`{.code}. Now you want to add information
about this source from your catalog to your events. You can do this
using the [Query Catalog
action](enrich-events-with-additional-data.html#UUID-9ff5a77d-5132-d686-c386-f5421f9ce3eb "Query Catalog action").

![wf-example-02a.png](image/uuid-fd455eb3-9b4d-faf0-e157-657c5a97fb43-en.png)

You drag the action into the workspace and configure it as follows.

-   Catalog Name

-   Lookup Field Name

    The action uses this field to query the catalog for the relevant
    [document](glossary.html#UUID-d691664b-e5f0-e08d-8eea-13bd3c05133c_glossterm-idm93128845038350).
    You specify a JSON object that says: take the event source (key) and
    find the document with the matching hostname (value):

    ![wf-query-catalog-lookup-field.png](image/uuid-e1e602cd-c496-e567-aa30-403a47488be5-en.png)

-   Apply Field Names

    Once it finds the document, the action copies the document values to
    the event. Here you define a set of JSON objects that say: take the
    catalog value (key) and map it to the specified output field in the
    event. You can also specify a default value if the catalog field is
    missing or empty.

    ![wf-query-catalog-apply-field-names.png](image/uuid-a6d51b16-1f28-cef6-14b6-ab5571ba8d9f-en.png)

  -------------------------------------------------------------------------------
  ![wf-example-02a.png](image/uuid-fd455eb3-9b4d-faf0-e157-657c5a97fb43-en.png)
  -------------------------------------------------------------------------------

###### Example event: before and after {.title}

The following table shows how this workflow updates and enriches an
example event. The workflow extracted the hostname from the
`labels`{.code} tag and copied it to the `source`{.code} field. Then it
updated the `service`{.code} field and created the
`location.region`{.code} tag based on data from the catalog.

+--------------------------------------+--------------------------------------+
| Event before                         | Event after                          |
+======================================+======================================+
| ``` {.programlisting}                | ``` {.programlisting}                |
| '{                                   | '{                                   |
|     "description": "RT > 500 msec",  |     "description": "RT > 500 msec",  |
|     "severity": 5,                   |     "severity": 5,                   |
|     "source": "brewAPM",             |     "source": "websrv01.us-west.myor |
|     "check": "api",                  | g.org",                              |
|     "service" : [ "REST" ],          |     "check": "api",                  |
|     "tags": {                        |     "service" : [ "custLogin" ],     |
|         "labels": ["ip=172.31.17.101 |     "tags": {                        |
| ,hostName=websrv01.us=west.myorg.org |         "labels": ["ip=172.31.17.101 |
| , id=125989934839832182"]            | ,hostName=websrv01.us=west.myorg.org |
|     },                               | , id=125989934839832182"]            |
| }'                                   |     },                               |
| ```                                  |     "location": {                    |
|                                      |         "region": "West US"          |
|                                      |     }                                |
|                                      | }'                                   |
|                                      | ```                                  |
+--------------------------------------+--------------------------------------+

##### Creating Data Catalogs {.title}

This topic describes how to create catalogs with enrichment data from
your environment.

Enrichment provides added flexibility for correlating your alerts into
incidents — you can correlate using enrichment data, not just data in
the raw ingested events. Enrichment can also make your alerts more
informative and easier to troubleshoot.

See also [Catalog API](catalog-api.html "Catalog API").

###### What is a catalog? {.title}

A catalog is a collection of data from your environment. The simplest
way to create a catalog is to generate a CSV from your environment and
then upload it to Moogsoft. The following example illustrates the
format.

``` {.programlisting}
host, app, aws-region, cluster
ip-172-31-37-159.ec2, music-match, us-west-1, cluster-1
ip-172-23-21-112.ec2, music-maker, us-east-2, cluster-7
```

Note the following:

-   The first row defines the keys, the following rows define the values
    to add to individual events

-   After you define a catalog, you must define a [Query Catalog
    action](enrich-events-with-additional-data.html#UUID-9ff5a77d-5132-d686-c386-f5421f9ce3eb "Query Catalog action")
    that matches entries in the catalog with new events. In this action,
    you define the event and catalog fields that the query uses to look
    up matching documents.

    Lookup fields most commonly specify the source where the event
    originated. In this example, the `host`{.code} column contains the
    lookup values. Thus you would specify a key-value pair with the
    event and catalog field names:

    ``` {.programlisting}
    {
       "Source" : "host"
    }
    ```

-   Each lookup must be unique. Thus in this example, a catalog cannot
    have multiple documents with the same `host`{.code} value .

-   A catalog consists of one or more *documents*. A document is
    equivalent to a single row in a CSV.

-   A Query Catalog action can specify multiple lookup fields. In this
    case, all the lookup values must match for a document to match an
    event. You might want to do this if (for example) your catalog spans
    multiple regions and you want to enrich your sources differently
    depending on the region.

-   When you upload the CSV, it deletes and overwrites any previous
    enrichment entries in the catalog. If you want to add or remove
    entries from the catalog, add or remove them from the CSV and then
    upload. You can also use the [Catalog
    API](catalog-api.html "Catalog API") to add or delete rows in a
    catalog.

-   When a lookup results in a match between an alert and a document,
    Moogsoft maps the other values in the document with fields in the
    alert. You specify how this mapping occurs in the Query Catalog
    action.

-   In this example, three columns map directly to fields in the alert
    schema:

    +--------------------------+--------------------------+--------------------------+
    | `host`{.code}            | to                       | `Source`{.code}          |
    +--------------------------+--------------------------+--------------------------+
    | `service`{.code}         | to                       | `Service`{.code}         |
    +--------------------------+--------------------------+--------------------------+
    | `cloud-provider-region`{ | to                       | `Location.region`{.code} |
    | .code}                   |                          |                          |
    +--------------------------+--------------------------+--------------------------+

    The fourth column, `cluster`{.code}, does not have an equivalent in
    the alert schema, so we will map it to a custom tag:
    `tags:cluster`{.code}.

-   If your organization stores its infrastructure in a CMDB, registry,
    spreadsheet, or other centralized repository, the simplest workflow
    is to publish or export the relevant data to the expected CSV format
    shown above. You can also use the [Catalog
    API](catalog-api.html "Catalog API") to push your updates
    programmatically.

-   The catalog CSV file name should not include dollar-sign
    (`$`{.code}) characters.

###### Creating a catalog {.title}

Do the following:

1.  [Define your enrichment
    data](enrich-events-with-additional-data.html#UUID-a2aa1727-a357-7bb3-c6b2-e4e8b38c0032_section-5cd9d5b5c8701-idm45390844315424 "Define your enrichment data")

2.  [Upload and review your data
    catalog](enrich-events-with-additional-data.html#UUID-a2aa1727-a357-7bb3-c6b2-e4e8b38c0032_section-idm231919819572256 "Upload and review your data catalog")

###### Define your enrichment data {.title}

To add enrichment data, you specify your data in a .csv file and then
upload the file to Moogsoft.

###### Upload and review your data catalog {.title}

After you define your enrichment data, you can upload the CSV and add it
to an event workflow. Do the following:

1.  Go to the **Data Config \> Data Catalog** page and then click **Add
    Catalog**.

2.  **Setup tab**: Select your CSV file and upload.

3.  **Catalog tab**: Review the catalog data.

Once you upload your catalog, you can include it in an event workflow.
See [Creating Event
Workflows](enrich-events-with-additional-data.html#UUID-3dd6be18-2411-2f1c-2daf-20c9745b915d "Creating Event Workflows").

###### Updating a catalog {.title}

The [Catalog API](catalog-api.html "Catalog API") enables you to create,
retrieve, update, and delete catalogs. It also enables you to add and
remove individual documents (rows) in a catalog.

##### Creating Event Workflows {.title}

This topic describes how to create an automated workflow that enriches
your events with external data from your environment. You can also
split, combine, extract, and update event data automatically using
workflow actions. See also [Workflow Service
API](workflow-service-api.html "Workflow Service API").

###### Before you begin {.title}

###### What information do you want in your alerts that isn't already there? {.title}

Before you set up an event workflow, you need to evaluate your current
alerts and identify the data that you want to add. Go to the Alerts
table and examine the data fields in your alerts. What contextual
information do you want to add?
([Events](glossary.html#UUID-d691664b-e5f0-e08d-8eea-13bd3c05133c_glossterm-idm93125404758188)
form the raw data of
[alerts](glossary.html#UUID-d691664b-e5f0-e08d-8eea-13bd3c05133c_glossterm-idm93125404714298);
by enriching your events, you ensure that the new information is
included in the resulting alerts.)

A key enrichment consideration is to ensure that your alerts include the
necessary information to correlate your alerts into the incidents that
you want. See [Good practices for defining
correlations](defining-correlations.html#UUID-17cf5f43-560d-d481-dc78-c07bf1c31288_section-5d4b27ddaf70e-idm44897684106192 "Good practices for defining correlations").

You can enrich your events with any information you and your users find
useful. The [Events
schema](events-api.html#UUID-435b238e-82a1-1bcd-0afd-1b9d12f79ad5_section-idm4615711507348831679167884383 "Event field reference")
includes a `tags`{.code} field that you can use to add custom
information.

In some cases, you might also want to update some event fields based on
other data in the same event. Example use cases include:

-   You want to use the hostname for the source but the raw events have
    the hostname embedded in a tag.

-   You want to update the event description, using information in other
    data fields, so that all event descriptions are formatted
    consistently.

-   You want to classify the event service or check based on information
    in other fields or tags.

###### Create the data catalog {.title}

To add external information to your events, you first need to create one
or more catalogs. See [Creating Data
Catalogs](enrich-events-with-additional-data.html#UUID-a2aa1727-a357-7bb3-c6b2-e4e8b38c0032 "Creating Data Catalogs").

###### Workflow Editor {.title}

+--------------------+--------------------------------------------------------+
| ![Workflow\_Exampl | The workflow editor (**Data Config** \> **Workflow     |
| e.png](image/uuid- | Engine**) provides a simple drag-and-click interface   |
| 0a9bcd5f-e2be-d30e | for creating workflows.                                |
| -530c-3fb5e4e1663b |                                                        |
| -en.png)           | Each workflow consists of a trigger and one or more    |
|                    | actions. The trigger is an event filter; if a new      |
|                    | event matches the trigger, the workflow processes the  |
|                    | event.                                                 |
|                    |                                                        |
|                    | Each action updates the event and passes it to the     |
|                    | next action.                                           |
|                    |                                                        |
|                    | When a workflow finishes processing an event, it       |
|                    | passes the event to the next workflow. The workflow    |
|                    | engine processes each workflow in the order in which   |
|                    | they are listed in the Workflows table.                |
|                    |                                                        |
|                    | When all workflows are finished processing an event,   |
|                    | the event gets deduplicated into an alert. The alert   |
|                    | then gets passed to the [correlation                   |
|                    | engine](correlation--alerts-into-incidents.html "Corre |
|                    | lation: Alerts into Incidents").                       |
|                    |                                                        |
|                    | Available actions include:                             |
|                    |                                                        |
|                    | -   [Query Catalog                                     |
|                    |     action](enrich-events-with-additional-data.html#UU |
|                    | ID-9ff5a77d-5132-d686-c386-f5421f9ce3eb "Query Catalog |
|                    |  action")                                              |
|                    |     — Add data from an external catalog to the event.  |
|                    |                                                        |
|                    | -   [Extract Substring                                 |
|                    |     action](enrich-events-with-additional-data.html#UU |
|                    | ID-f03dc6c0-b441-6ae6-3889-b31a9815de4a "Extract Subst |
|                    | ring action")                                          |
|                    |     — Extract a substring from one field and copy it   |
|                    |     to another.                                        |
|                    |                                                        |
|                    | -   [Match and Update                                  |
|                    |     action](enrich-events-with-additional-data.html#UU |
|                    | ID-04acf6c2-2289-95d3-8787-429cc9a2c948 "Match and Upd |
|                    | ate action")                                           |
|                    |     — Evaluate one or more fields against one or more  |
|                    |     regexes, then update another field based on the    |
|                    |     results.                                           |
|                    |                                                        |
|                    | -   [Template Field                                    |
|                    |     Action](enrich-events-with-additional-data.html#UU |
|                    | ID-02d450d6-cee1-0cdc-1f0b-8f28e8752859 "Template Fiel |
|                    | d Action")                                             |
|                    |     — Combine two or more fields into another field.   |
|                    |                                                        |
|                    | -   [Parse FQDN                                        |
|                    |     action](enrich-events-with-additional-data.html#UU |
|                    | ID-b41cc92c-92cc-98e4-5d54-ef5b0b62c5ff "Parse FQDN ac |
|                    | tion")                                                 |
|                    |     — Split a fully-qualified domain name, then copy   |
|                    |     the hostname and domain name to separate fields.   |
+--------------------+--------------------------------------------------------+

##### Event Workflow Actions {.title}

You can include any of the following actions in an event workflow. Each
action updates one event and then passes it to the next action.

-   [Workflow
    trigger](enrich-events-with-additional-data.html#UUID-6515cfc7-a6a2-855b-b79a-390f2d4aee87 "Workflow trigger")
    — Every workflow starts with a trigger, which defines the events
    that trigger the workflow.

-   [Split
    action](enrich-events-with-additional-data.html#UUID-516fa146-64c0-62fe-5dbd-bd0ffdd28960 "Split action")
    — Split one field into substrings based on a character pattern and
    then copy the substrings to other fields.

-   [Query Catalog
    action](enrich-events-with-additional-data.html#UUID-9ff5a77d-5132-d686-c386-f5421f9ce3eb "Query Catalog action")
    — Enrich your events by mapping data from a catalog to new events.

-   [Parse FQDN
    action](enrich-events-with-additional-data.html#UUID-b41cc92c-92cc-98e4-5d54-ef5b0b62c5ff "Parse FQDN action")
    — Parse a field with an FQDN and then copy the hostname and domain
    name to other fields.

-   [Match and Update
    action](enrich-events-with-additional-data.html#UUID-04acf6c2-2289-95d3-8787-429cc9a2c948 "Match and Update action")
    — Search for a set of strings in a set of fields, then update a
    field based on the results. This is useful for normalizing events
    with different formats.

-   [Extract Substring
    action](enrich-events-with-additional-data.html#UUID-f03dc6c0-b441-6ae6-3889-b31a9815de4a "Extract Substring action")
    — Use a regex to extract strings from one field and then copy the
    strings to one or more fields.

-   [Template Field
    Action](enrich-events-with-additional-data.html#UUID-02d450d6-cee1-0cdc-1f0b-8f28e8752859 "Template Field Action")
    — Update a field based on the values of other fields.

###### Workflow trigger {.title}

The trigger defines the events that you want the workflow to process.
The workflow is triggered whenever a new event matches this filter.

Click in the Event filter field and select the fields, values, and
operators from the pull-down menus.

###### Extract Substring action {.title}

This action extracts one or more substrings from an input field using a
regex. Then it copies the substrings to the output fields, in order.

-   Input field

    The field to search.

-   Regex capture groups

    The regex to apply to the input field.

-   Output fields

    Copy the extracted substrings to these fields, in order.

###### Example {.title}

One of your event streams has `source`{.code} fields that are formatted
as follows:

-   country code, 2 characters

-   data-center code, 2 characters

-   device name, 4 characters

You want to store this information in separate tags. You add an Extract
Strings action to your workflow and format it as follows:

-   Input field = `source`{.code}

-   Regex capture groups = `(\w{2})(\w{2})-(.*)`{.code}

-   Output fields:

    -   `location.country`{.code}

    -   `location.datacenter`{.code}

    -   `tags.devicename`{.code}

+--------------------------------------+--------------------------------------+
| Event before                         | Event after                          |
+======================================+======================================+
| ``` {.programlisting}                | ``` {.programlisting}                |
| {                                    | {                                    |
|    "description":"cpu load > 90%",   |    "description":"cpu load > 90%",   |
|    "severity": 5,                    |    "severity": 5,                    |
|    "source":"ussf-sw99",             |    "source":"ussf-sw99",             |
|    "check":"cpu",                    |    "check":"cpu",                    |
|    "service":[ "custLogin"],         |    "service":[ "custLogin"],         |
| }                                    |     "location": {                    |
| ```                                  |         "country": "us",             |
|                                      |         "datacenter" : "sf"          |
|                                      |     },                               |
|                                      |     "tags": {                        |
|                                      |         "devicename": "sw99"         |
|                                      |     },                               |
|                                      | }                                    |
|                                      | ```                                  |
+--------------------------------------+--------------------------------------+

###### Match and Update action {.title}

This action enables you to update a field based on the contents of other
fields. You specify a set of input fields to evaluate. Each input has a
corresponding regex tag and an output value. On the first match, the
action copies the output value to the output field.

This action provides a simple way to normalize events that have
inconsistent formats. Suppose you have multiple event sources that
indicate the event class -- application, network, database, etc. --
using different strings in different fields. You can use this action to
find a matching string and then update the event `class`{.code}
consistently based on the results.

-   Input fields

    The set of input fields to search.

-   Regex tags

    Each input field has a corresponding regex tag and output string. On
    the first match, copy the corresponding output string to the output
    field.

-   Output field

    Apply the output string to this field.

###### Example {.title}

You have two event sources. For one source, "ping" in the
`description`{.code} field indicates a network event. For another
source, "stored procedure" in the `check`{.code} field indicates a
database event. You want to update the `class`{.code} field to either
`network`{.code} or `database`{.code}.

+--------------------------------------+--------------------------------------+
| ``` {.programlisting}                | ``` {.programlisting}                |
| {                                    | {                                    |
|    "description":"ping to cntnr04 >  |    "description":"proc get-all-addre |
| 200ms",                              | sses failed -- access denied",       |
|    "severity":4,                     |    "severity": 5,                    |
|    "source":"cntnr04",               |    "source": "pd4058",               |
|    "check":"rtt",                    |    "check": "stored procedure get-al |
|    "service":[ "infraTestService"]   | l-addresses",                        |
| }                                    |    "service":[ "custService"]        |
| ```                                  | }                                    |
|                                      | ```                                  |
+--------------------------------------+--------------------------------------+

You set up your action as follows:

-   Input fields = `description`{.code}, `check`{.code}

-   Regex tags and output strings:

    -   `.*ping.*`{.code} =\> `network`{.code}

    -   `.*stored procedure.*`{.code} =\> `database`{.code}

-   Output field = `class`{.code}

+--------------------------------------+--------------------------------------+
| Event Before                         | EventAfter                           |
+======================================+======================================+
| ``` {.programlisting}                | ``` {.programlisting}                |
| {                                    | {                                    |
|    "description":"ping to cntnr04 >  |    "description":"proc get-all-addre |
| 200ms",                              | sses failed -- access denied",       |
|    "severity":4,                     |    "severity": 5,                    |
|    "source":"cntnr04",               |    "source": "pd4058",               |
|    "check":"rtt",                    |    "check": "stored procedure get-al |
|    "service":[ "infraTestService"],  | l-addresses",                        |
|    "class" : "network"               |    "service":[ "custService"],       |
| }                                    |    "class" : "database"              |
| ```                                  | }                                    |
|                                      | ```                                  |
+--------------------------------------+--------------------------------------+

###### Parse FQDN action {.title}

This action parses an FQDN field and copies the host and domain names to
the specified fields .

-   FQDN field

    The field that contains the fully-qualified domain name.

-   Hostname output field

    Copy the hostname to this field.

-   Domain output field

    Copy the domain name to this field (*optional*)

###### Example {.title}

Your event sources populate the `source`{.code} field with the FQDN. You
want your events to use the hostname for the `source`{.code} and store
the domain name in a tag. You add a Parse FQDN action to your workflow
and format it as follows:

-   FQDN field = `Source`{.code}

-   Short output field = `Source`{.code}

-   Domain output field = `Tags`{.code}

+--------------------------------------+--------------------------------------+
| Event Before                         | EventAfter                           |
+======================================+======================================+
| ``` {.programlisting}                | ``` {.programlisting}                |
| {                                    | {                                    |
|   "source": "myHost.myApp.myDomain.c |   "source": "myHost",                |
| om",                                 |   ....                               |
|   ....                               |   "tags": {                          |
| }                                    |     "domain": "myApp.myDomain.commyN |
| ```                                  | amespace",                           |
|                                      |   }                                  |
|                                      | }                                    |
|                                      | ```                                  |
+--------------------------------------+--------------------------------------+

###### Query Catalog action {.title}

This action queries a data catalog and maps the matching data to the
specified event fields.

-   Catalog name

    The catalog to query.

-   Lookup mapping

    Look up the matching catalog entry for an object: If the catalog
    field = the event field, the entry matches.

-   Apply mapping

    If the action finds a matching entry, copy data from the catalog
    field to the event field. You can specify a default value if a
    catalog field is undefined.

###### Example {.title}

Your monitoring service sends metrics to Moogsoft. These metrics have
all the data required by the Metrics API, but they do not specify the
service running on each source. You create a catalog called mySources,
which specifies each source and its associated service, region, and
instance type:

``` {.programlisting}
instance-id,          service,      region,      instance-type
i-0z267fg498xc23750,  custLogin,    us-east-1f,  t2.large
i-0w690ji481vb38749,  dbQuery,      us-west-1b,  t3a.xlarge
...
```

To map the catalog data to your monitored sources, you drag a Query
Catalog action into your workflow and configure it as follows:

-   Catalog name = mySources

-   Lookup mapping : instance-id = `Source`{.code}

-   Apply mapping:

    -   service = `Service`{.code}

    -   region = `Location.region`{.code}

    -   instance-type = `Tags.instance-type`{.code}

When this action processes an event from one of these sources, it adds
the catalog data to the event.

+--------------------------------------+--------------------------------------+
| Event before                         | Event after                          |
+======================================+======================================+
| ``` {.programlisting}                | ``` {.programlisting}                |
| {                                    | {                                    |
|    "description":"CPU spike to 75%", |    "description":"CPU spike to 75%", |
|                                      |                                      |
|    "severity":4,                     |    "severity":4,                     |
|    "source":"i-0z267fg498xc23750",   |    "source":"i-0z267fg498xc23750",   |
|    "time":1592322348182,             |    "time":1592322348182,             |
|    "check":"cpu",                    |    "check":"cpu",                    |
|    "service":[""],                   |    "service":["custLogin"],          |
| }                                    |    "location": { "region":"us-east-1 |
| ```                                  | f"},                                 |
|                                      |    "tags": { "instance-type":"t2.lar |
|                                      | ge"}                                 |
|                                      | }                                    |
|                                      | ```                                  |
+--------------------------------------+--------------------------------------+

###### Split action {.title}

This action splits one field into substrings and copies them to other
fields in the same object.

-   Input fields

    The object field to split.

-   Regex tags

    The string pattern to split on, such as **/** or **::**

-   Output fields

    Copy substrings to the following fields, in order.

######   {.title}

Example

Your monitoring service generates events with a "database" tag like
this:

``` {.programlisting}
{
    ...
    "tags": {
        "database": "myNamespace_myCluster_myDBname"
    }
}
```

You want to split the namespace, cluster, and database name into
separate tags. You configure your Split action like this:

-   Input fields = `tags.database`{.code}

-   Regex tags = `_`{.code}

-   Output fields = `tags.namespace`{.code}, `tags.cluster`{.code},
    `tags.database`{.code}

+--------------------------------------+--------------------------------------+
| Event Before                         | EventAfter                           |
+======================================+======================================+
| ``` {.programlisting}                | ``` {.programlisting}                |
| {                                    | {                                    |
|   "tags": {                          |   "tags": {                          |
|     "database": "myNamespace_myClust |     "namespace": "myNamespace",      |
| er_myDBname"                         |     "cluster": "myCluster",          |
|   }                                  |     "database": "myDatabase"         |
| }                                    |   }                                  |
| ```                                  | }                                    |
|                                      | ```                                  |
+--------------------------------------+--------------------------------------+

###### Template Field Action {.title}

This action enables you to construct a string, based on one or more
event fields or tags, and copy the string to an output field.

-   Template

    The template used to construct the output string. To reference a
    field or tag, use the following format:
    `${field-or-tag-name}`{.code}

    You can also include plain text in a template, for example:
    `${class} issue found in ${location}`{.code}

-   Output field

    Copy the output string to the following field.

###### Example {.title}

In one of your event streams, the `source`{.code} field contains the
hostname and the domain name is in a tag:

``` {.programlisting}
{
    "source" : "myhost"
     ....
    "tags": {
        "domain": "mydomain.com"
    }
}
```

Your data catalogs use the FQDNs for their source fields. You want to
update the `source`{.code} field in these events to include both the
hostname and the domain name. You add a Template Field action to your
workflow, before the Query Catalog action, and format it like this:

-   Template = `${source}.${tags.domain}`{.code}

-   Output field = `source`{.code}

+--------------------------------------+--------------------------------------+
| Event Before                         | EventAfter                           |
+======================================+======================================+
| ``` {.programlisting}                | ``` {.programlisting}                |
| {                                    | {                                    |
|     "source" : "myhost"              |     "source" : "myhost.mydomain.com" |
|      ....                            |      ....                            |
|     "tags": {                        |     "tags": {                        |
|         "domain": "mydomain.com"     |         "domain": "mydomain.com"     |
|     }                                |     }                                |
| }                                    | }                                    |
| ```                                  | ```                                  |
+--------------------------------------+--------------------------------------+

* * * * *

  ------------------------------------------------------------------------- ---------------------------------------------------------------------- -------------------------------------------------
  [Prev](enrich-and-deduplicate-events-into-alerts-using-workflows.html)    [Up](enrich-and-deduplicate-events-into-alerts-using-workflows.html)    [Next](event-deduplication.html)
  Enrich and Deduplicate Events into Alerts using Workflows                 [Home](index-en.html)                                                   Event deduplication: how-to and best practices
  ------------------------------------------------------------------------- ---------------------------------------------------------------------- -------------------------------------------------
