# SA-Spico_SIEM
For licensed users of Spico SIEM to access documentation and submit issues

![](https://spicosolutions.com/wp-content/uploads/2015/03/spico-logo-small.png)

**Table of Contents**

[TOCM]

[TOC]

# Implementation
## Install Dependencies
## Add-ons
|  Add-on |URL   |
| ------------ | ------------ |
|  JSON Tools |  https://splunkbase.splunk.com/app/3540/ |
| Splunk Common Information Model (CIM) | https://splunkbase.splunk.com/app/1621/  |
| ES Content Updates  |  https://apps.splunk.com/apps/id/DA-ESS-ContentUpdate |
| Splunk Security Essentials  |  https://apps.splunk.com/apps/id/Splunk_Security_Essentials |
| Event Timeline Viz  | https://apps.splunk.com/apps/id/event-timeline-viz  |
| Lookup Editor  | https://apps.splunk.com/apps/id/lookup_editor  |
|Phantom Add-On (Optional)| https://apps.splunk.com/apps/id/phantom  |
| Alert Manager (Optional)  |  https://apps.splunk.com/apps/id/alert_manager |

## Indexes
|  Index |Purpose   |
| ------------ | ------------ |
|  ss_risk | Stores all data created by risk generators |
|  ss_incident |  Store all data created by incident generators|

# Components
## Lookup Tables
| Name  | Fields  | Purpose  |
| ------------ | ------------ | ------------ |
| ss_base_score  | ![](https://github.com/spicosolutions/SA-Spico_SIEM/blob/main/images/ss_base_score.png)  |  - Defines the starting score to be applied to a risk object <br/> - Custom labels may be created to further customize generated base scores|
|  ss_master |  ![](https://github.com/spicosolutions/SA-Spico_SIEM/blob/main/images/ss_master.png) |  - Master table for Spico SIEM to function<br/> - Defines the risk_rule which serves as a lazy primary key across most tables<br/>- Ties all tables together into a functional set of data to provide risk rules with enough information to generate a score|
|  ss_reliability |  ![](https://github.com/spicosolutions/SA-Spico_SIEM/blob/main/images/ss_reliability.png) |  - Defines a modifier to be applied to the overall score of a risk object in which it is assigned<br/>- Custom labels may be created to further customize generated scores |
| ss_search_filter  | ![](https://github.com/spicosolutions/SA-Spico_SIEM/blob/main/images/ss_search_filter.png)  | Creates the ability to define a filter for restricting noisy data<br/>- Two different modes<br/>--filter - eliminates the possibility for results to display for the associated risk rule<br/>--suppression - with the existence of start_epoch and end_epoch, provides a way to suppress data from showing in results for a defined period of time. When end_epoch is reached, data will once again display.|

## Macros
|  Name | Purpose   |
| ------------ | ------------ |
|  testmode | This is a global flag used to prevent risk generators from posting into the ss_risk table when detections are found |
|  windows_security |  Given a lot of searches leverage Windows security data, this macros is used to define the location of Windows data|
# Runtime
## Risk Generator

A risk generating search (SSRG) is one that executes against the data and upon detection, creates a risk value associated with a risk object and stores it for  potential incident generation.

A SSRG is made up of several different parts:

SPL - this defines the basic search which identifies the risk object that should populate risk

Spico SIEM setup - a group of macros are applied at the end of the SPL which pulls data from a number of lookup tables preparing the data for scoring

Scoring - this is the final step where score is applied.  So what does that mean exactly?  A score is compiled from a few bits of information gathered (base score, reliability factor) and then a record is written to the ss_risk index for storage and reporting.  This indexed data is what is used  for grouping risk events and scores for potential notable generation.
## Incident Generator

An incident generating search (SSIG) is one that bypasses scoring and alerts the security team of a potential threat.  When writing such a search it is important to append the below to the end to ensure your incident genrator is posting to the correct location.
```
| mkjson
| collect index=ss_incident sourcetype=incident:ssig testmode=`testmode`
```

There is one OOTB SSIG named "SSIG - 24 hour risk threshold exceeded - Rule" which will evaluate risk objects over a 24 hour period and trigger based on a score greater than 100.
# Views
## Executive View
## Scoreboard
## Management
### Basic Flow
The flow to using the Spico SIEM should be looked at as not just a bunch of steps and configurations and the same old noisy notables but rather one that applies intelligence, correlation and meaning around the notables that do eventually get generated.  The flow below shows the basic logic that should be applied when approaching whether a risk generator or an incident generator should be used.  

The upper part of the flow focuses on the risk object only and how we should be scoring specific risk objects.

The lower flow relates to correlation searches in Enterprise Security today where notifications are created as notables individually rather than a grouping of events.

![](https://github.com/spicosolutions/SA-Spico_SIEM/blob/main/images/ss_flow.png)

### Tuning
When it comes to Spico SIEM, tuning will greatly depend on the user’s tolerance for noise.  With defined default alerting thresholds for risk score being 100 and for tactics and techniques being 3 in a week there is a huge potential that without appropriate tuning many notables may be generated.  In the beginning you want that noise though to know where you stand and to determine what is good and what is bad.  Once a baseline of noise is determined the next steps are to determine where to raise and lower the bar.  There are a variety of items that can be tuned to make scoring your own:

#### Filtering/Suppression

These tuning attributes can be done a couple of different ways.  These may be implemented in the search itself or the ss_search_filter lookup.  The recommendation would be to leverage the lookup file since there would be no need to touch the search itself.  Less fingers touching the SPL (or code) the better.  Updating the lookup table may be done in the Lookup Editor application where you can define a filter or a suppression.  A filter is a record without specifying the start/stop epoch times while a suppression uses the epoch times to define a time period to eliminate risk objects from generating risk.  At the completion of each change, please add to the notes field specifying the date, user and change made for record keeping.

#### Base Score/Reliability

These attributes are the main values for generating the initial risk.  Base Score specifies the starting score while the Reliability applies a modification to affect the score.  There are a base set of each attributea defined in their lookup tables but custom records may also be created for custom use cases.  Values defined here are referenced from the ss_master_ table where each SSRG is defined.

If a new confidence or impact is created, make sure to associate it to the risk rule in the rr_meta.csv

#### Throttling


After modifying attributes during the tuning cycle it is important to let the process bake a little to attain a new baseline.  This is a repeatable step that should be used for each item that is being tuned so that the appropriate baseline for what is normal can be determined.
