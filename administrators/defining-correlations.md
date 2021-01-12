---
title: "Defining Correlations"
tags: [correlating]
keywords: correlation, how-to, reference
last_updated: December 10, 2020
summary: "How to define alert correlations"
sidebar: mydoc_sidebar
permalink: defining-correlations.html
folder: moogsoft
---

<!--
xml version="1.0" encoding="UTF-8" standalone="no"? html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd"    Defining Correlations            Defining Correlations   [Prev](../correlate-alerts-into-incidents.html)  Correlate Alerts into Incidents  [Next](../user-management.html)         #### Defining Correlations
-->

The **Data Config** > **Correlation Engine** page in the UI lists the active definitions in your Moogsoft instance. Each definition specifies the following:


 *  The description to apply to incidents created by this correlation, based on alert data of interest.


 *  The definition scope — you can apply a correlation to all alerts, or specify an alert filter for matching alerts only.


 *  The alert fields and tags to consider for correlation, such as source or service.

     ![[Note]](images/note.png) A definition can have multiple fields and tags. You can also specify a similarity threshold for each element to correlate values that are similar but not identical. See [Fields and Tags to correlate](defining-correlations.html#UUID-17cf5f43-560d-d481-dc78-c07bf1c31288_section-5d3a0d8289484-idm45315539783872 "Fields and Tags to correlate") below.


 *  The correlation time window — that is, how long an incident is a candidate for correlation using this definition.



  You can also use the [Correlation Definitions API](../../moogsoft-apis/correlation-definitions-api.html "Correlation Definitions API") to create, retrieve, update, and delete correlation definitions.

## Good practices for defining correlations

The key to defining good correlations for your organization is to think about how you want to organize your alerts into incidents. Ask the following questions:


 *  Which data fields do you want to use to correlate your alerts into incidents? For example, you might want to use fields such as:


	 +  source: Correlate alerts into incidents based on the nodes where the performance-impacting events occurred.


	 +  service: Correlate alerts into incidents based on the apps or services that generated the alerts.


	 +  location: Correlate alerts into incidents based on the physical locations where the performance-impacting events occurred.  
 *  How many fields do you want to consider for correlation?

 You can include multiple fields in a definition. Fewer fields in a correlation results in fewer, more general incidents with more alerts. More fields in a correlation results in a higher number of incidents with fewer alerts.


 *  How similar do the corresponding values need to be for an alert and an incident to be correlated?



  The following fields can be useful for correlating alerts into incidents:


 *   class


 *   service


 *   location


 *  manager — This field can be useful if you want to correlate based on an event generator such as "collector," "docker," or "new-relic." For example, suppose you have multiple EC2 instances running Docker. You want to correlate all Docker alerts for each EC2 into incidents. You can then define a correlation that includes the "source" field (the EC2) and the "manager" field (in this case, "docker").


 *  source — This field works well in organizations where hostnames have formal naming conventions. Some organizations include tags in hostnames to indicate a host's function, team, or organization. Hostnames might include tags that indicate geographic locations, engineering teams, deployment statuses, and other attributes. In this case, you might want to correlate alerts based on hostname tags such as "*hq," "*nyc," "*dev," "*stage," or "*prod."

 This field does not work well in organizations where hostnames are ephemeral or auto-assigned, unless you want to correlate all alerts from the same source into one incident.


 *  Description — This field can be useful if your alert descriptions have a consistent format. Correlation considers alert descriptions only, not incident descriptions.


 *  Custom tags — You can also correlate based on custom alert tags. Tags can provide a great deal of flexibility for correlation, because you can create any tags for any key-value data you want. The primary requirement is that the tag values themselves have a consistent convention and format to enable the correlation engine to compare two values directly.



  The following fields are generally not useful for correlating alerts into incidents:


 *  Severity — This field is useful for filtering alerts to correlate, but not for correlating alerts into incidents.


 *   Status


 *   Assignee


 *   Ticket



## Before you begin

Do the following:


 2.  Set up your [event ingestions](../ingest-source-data.html "Ingest Source Data").


 4.  Examine your alerts to determine if they include the data that you want to use for correlation. If they do not, set up your [alert enrichments](../enrich-and-deduplicate-events-into-alerts-using-workflows.html "Enrich and Deduplicate Events into Alerts using Workflows") to add this data.



## Correlation Settings

To define a correlation, go to **Data Config** > **Correlation Engine** and then click **Add Correlation Definition**. Each definition has the following settings:


 *  Correlation Name


 *   [Incident description](defining-correlations.html#UUID-17cf5f43-560d-d481-dc78-c07bf1c31288_section-idm4573163334574431658448441384 "Incident description")

     The description to use for all incidents that get generated from this correlation definition. These descriptions appear in the [Incidents](../../operators---viewing-and-analyzing-your-data/view-incidents.html "View incidents") table. You can use macros to generate incident descriptions dynamically based on the member alerts, as described below.


 *   [Scope](defining-correlations.html#UUID-17cf5f43-560d-d481-dc78-c07bf1c31288_section-5d6409bd35f15-idm46263062077840 "Scope")

     You can specify an alert filter to limit the scope of alerts to consider for a specific correlation.


 *   [Fields and Tags to correlate](defining-correlations.html#UUID-17cf5f43-560d-d481-dc78-c07bf1c31288_section-5d3a0d8289484-idm45315539783872 "Fields and Tags to correlate")

     The set of alert fields to consider for correlation, and the similarity required for a match between an alert and an incident.


 *   [Correlation time period](defining-correlations.html#UUID-17cf5f43-560d-d481-dc78-c07bf1c31288_section-5d3b248d242e8-idm46391455805008 "Correlation time period")

     The window for correlating alerts into the same incident.

### Incident description

You can specify incident descriptions and fields dynamically, based on the alert data in each incident. For example, suppose you are defining a correlation based on the Service alert field. You can then specify a label string such as

 Service Incident: cited(service) in classes unique(class,3) for cited(check,2) checks Given this string, the resulting descriptions include the three most-cited services and the number of times each service is cited by a member alert:

 Service Incident: ShoppingCart, Online Store in classes Storage, Compute, Network for Disk, CPU checks     


#### Incident macros

You can use the following macros to generate incident descriptions:


 *  Count (*alert-field*) — Return the count of alert-field citations, including duplicates.


 *  Unique Count (*alert-field*) — Return the count of unique alert-field citations, excluding duplicates.


 *  To List (*alert-field*) — Return a comma-separated string of all elements in a list, including duplicates.


 *  Unique (*alert-field, N*) — Return a comma-separated string of N unique elements in a list, excluding duplicates.


 *  Top (*alert-field*) — Return the top-cited item.


 *  Cited (*alert-field, N*) — Return a list of the top-cited N items. If two or more items have the same number of citations, the items are sorted alphabetically.



### Scope

If the correlation is relevant only to a subset of alerts, you can enter a filter string to consider only alerts of interest.

To define an alert filter:


 2.  Go to the [Alerts table](../../operators---viewing-and-analyzing-your-data/view-alerts.html "View alerts") in the UI.


 4.  Enter and validate your filter in the search field above the table.


 6.  Copy and paste the filter string into the request.



### Fields and Tags to correlate

The set of alert fields and tags to consider for correlation, and the similarity required for a match between an alert and an incident. Two alerts are considered correlated if all the fields and tags in the definition meet the specified degree of similarity. For specific guidance, see [Good practices for defining correlations](defining-correlations.html#UUID-17cf5f43-560d-d481-dc78-c07bf1c31288_section-5d4b27ddaf70e-idm44897684106192 "Good practices for defining correlations").

#### Alert field similarity

The required degree of similarity between the same fields in a new vs. an open alert. Moogsoft uses the [bag-of-words](https://en.wikipedia.org/wiki/Bag-of-words_model) model and the [shingling](https://en.wikipedia.org/wiki/W-shingling) natural-language processing methods to calculate the text similarity between two fields.

 The correlation engine calculates the similarity differently depending on the field type:


 *  [Shingle similarity](defining-correlations.html#UUID-17cf5f43-560d-d481-dc78-c07bf1c31288_section-5d56f5888fb8c-idm45911915492816 "Shingle similarity") for the source and all custom tag fields.


 *  [Array and object similarity](defining-correlations.html#UUID-17cf5f43-560d-d481-dc78-c07bf1c31288_section-idm4596649867929631692681334851 "Array and object similarity") for the service and location fields.


 *  [Multi-word similarity](defining-correlations.html#UUID-17cf5f43-560d-d481-dc78-c07bf1c31288_section-idm4523350768102431692741009491 "Multi-word similarity") for the. agent, class, description, and manager fields.

#### Shingle similarity

This section describes similarity for the source field and all custom tag fields. The following example illustrates how the correlation engine determines if two fields are similar.


 2.  A correlation definition specifies **service** as the one field to correlate, with a similarity threshold of **80%**.


 4.  The correlation engine receives an alert with service = loginver012. An open alert has service = loginver011 .


 6.  To determine if the two fields are similar, the engine does the following:


	 2.  Splits each string into a set of shingles based on the default shingle size, which is 3.


	 4.  Compares each 3-character sequence in the new-alert field with the corresponding sequence in the open-alert field:

	 log ogi gin inv nve ver er0 r01 011

	 log ogi gin inv nve ver er0 r01 012


	 6.  Calculates the similarity using the [Sørensen–Dice coefficient](https://en.wikipedia.org/wiki/S%C3%B8rensen%E2%80%93Dice_coefficient):

	     (number\_of\_identical\_shingles * 2) / total\_number\_of\_shingles

      In this case, 8 out of the 9 shingles are identical between the two fields. The total number of shingles in both fields is 18:

	    8 * 2 / 18 = 0.88...

	 8.  Checks the similarity setting for this field. The similarity is 80%, so the two values are similar.  

#### Array and object similarity

This section describes similarity for location and service fields.

The location field is a JSON object that can include component fields such as city and region. The service field is a JSON array that can include one or more strings. In both cases, the correlation engine creates strings from the objects and then applies [Shingle similarity](defining-correlations.html#UUID-17cf5f43-560d-d481-dc78-c07bf1c31288_section-5d56f5888fb8c-idm45911915492816 "Shingle similarity") on the concatenated strings.

For example, suppose an alert arrives with "location": { "region" : "us-west-1", "building":"sfhqlc" } . The engine creates a string that includes all characters except spaces, which are removed:

 {"region":"us-west-1","building":"sfhqlc"}

 The engine converts arrays similarly. Suppose an alert has a list of services: "service": [ "retail", "support" ]. The engine creates the string:

 ["retail","support"]      

#### Multi-word similarity

This section describes similarity calculations for agent, class, description, and manager fields that contain multiple words.

 agent, class, description, and manager are string fields that might consist of multiple words separated by spaces. Instead of splitting the string into shingles, the correlation engine splits each string into words with space characters as the delineators. Then it calculates the similarity between the two strings as described in [Shingle similarity](defining-correlations.html#UUID-17cf5f43-560d-d481-dc78-c07bf1c31288_section-5d56f5888fb8c-idm45911915492816 "Shingle similarity") .

 The following example illustrates how the correlation engine calculates similarity between two multi-word strings.


 2.  A correlation definition specifies **class** as the one field to correlate, with a similarity threshold of **80%**.


 4.  Two open alerts have the following classes:


	 *  Alert 1: "class" : "HTTP 5xx% mcrsrvc-login1"


	 *  Alert 2: "class" : "HTTP 5xx% mcrsrvc-dbsrv3"  

 6.  Alert 3 arrives with "class" : "HTTP 5xx% mcrsrvc-login2".


 8.  Comparing Alert 3 with Alert 1, the engine has 40 tokens total with 19 matches. (In this example, the underscores indicate spaces.)

 HTT TTP TP\_ P\_5 \_5x 5xx xx% x%\_ %\_m mar crs rsv srv vc- c-l -lo log ogi gin in1

 HTT TTP TP\_ P\_5 \_5x 5xx xx% x%\_ %\_m mar crs rsv srv vc- c-l -lo log ogi gin in2

 The correlation engine feeds these numbers into the Sørensen–Dice coefficient:

 (19 * 2) / 40 = 0.96 These classes meet the 80% similarity threshold. The engine clusters Alert 3 with Alert 1.


 10.  Comparing Alert 3 with Alert 2, the engine has 40 tokens total with 14 matches.

 HTT TTP TP\_ P\_5 \_5x 5xx xx% x%\_ %\_m mar crs rsv srv vc- c-l -lo log ogi gin in2

 HTT TTP TP\_ P\_5 \_5x 5xx xx% x%\_ %\_m mar crs rsv srv vc- c-d -db dbs bsr srv rv3

 The correlation engine feeds these numbers into the Sørensen–Dice coefficient:

 (14 * 2) / 40 = 0.7 These classes do not meet the 80% similarity threshold. The engine does not cluster Alert 3 with Alert 2.

 ### Correlation time period

    The length of time for clustering similar alerts into the same incident, starting from the incident creation time. When the correlation period ends, Moogsoft correlates alerts into a new incident.

 The options are as follows:

 *  **Automatic**: With this option, the correlation period is 15 minutes but can extend up to 45 minutes. The extension period is 3 minutes 45 seconds. The correlation period can extend as follows:

	 +  The timer starts when the engine creates the incident.


	 +  If the engine does not add an alert after 11:15 (15:00 ‑ 3:45) , the time window closes at 15:00.

	     ![v2-correlation-time-window-01.png](image/uuid-a66e7572-cda2-1c45-ab03-fcd6eaca10c1-en.png)     
	 +  If the engine adds an alert after 11:15, the period extends to 18:45 (15:00 + 3:45).

	     ![v2-correlation-time-window-02.png](image/uuid-f628b8a2-f3da-d4b4-d7ea-ae7c2de98533-en.png)     
	 +  If another alert gets added between 15:00 and 18:45, the time window extends again.

	 This process can repeat up to 45 minutes.

	     ![v2-correlation-time-window-03.png](image/uuid-719845e0-85b7-a593-975a-81776453fced-en.png)  
 *  **Manual**: Select this option if you know the time window that you want to specify.
