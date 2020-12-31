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
| ss_base_score  |   |  - Defines the starting score to be applied to a risk object <br/> - Custom labels may be created to further customize generated base scores|
|  ss_master |   |  - Master table for Spico SIEM to function<br/> - Defines the risk_rule which serves as a lazy primary key across most tables<br/>- Ties all tables together into a functional set of data to provide risk rules with enough information to generate a score|
|  ss_reliability |   |  - Defines a modifier to be applied to the overall score of a risk object in which it is assigned<br/>- Custom labels may be created to further customize generated scores |
| ss_search_filter  |   | Creates the ability to define a filter for restricting noisy data<br/>- Two different modes<br/>--filter - eliminates the possibility for results to display for the associated risk rule<br/>--suppression - with the existence of start_epoch and end_epoch, provides a way to suppress data from showing in results for a defined period of time. When end_epoch is reached, data will once again display.|

## Macros
|  Name | Purpose   |
| ------------ | ------------ |
|  testmode | This is a global flag used to prevent risk generators from posting into the ss_risk table when detections are found |
|  windows_security |  Given a lot of searches leverage Windows security data, this macros is used to define the location of Windows data|
# Runtime
## Risk Generator
## Incident Generator
# Views
## Executive View
## Scoreboard
## Management
### Basic Flow
The flow to using the Spico SIEM should be looked at as not just a bunch of steps and configurations and the same old noisy notables but rather one that applies intelligence, correlation and meaning around the notables that do eventually get generated.  The flow below shows the basic logic that should be applied when approaching whether a risk generator or an incident generator should be used.  

The upper part of the flow focuses on the risk object only and how we should be scoring specific risk objects.

The lower flow relates to correlation searches in Enterprise Security today where notifications are created as notables individually rather than a grouping of events.

