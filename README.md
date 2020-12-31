# SA-Spico_SIEM
For licensed users of Spico SIEM to access documentation and submit issues

![](https://spicosolutions.com/wp-content/uploads/2015/03/spico-logo-small.png)

**Table of Contents**

[TOCM]

[TOC]

#Implementation
##Install Dependencies
##Add-ons
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

##Indexes
|  Index |Purpose   |
| ------------ | ------------ |
|  ss_risk | Stores all data created by risk generators |
|  ss_incident |  Store all data created by incident generators|

#Components
##Lookup Tables
| Name  | Fields  | Purpose  |
| ------------ | ------------ | ------------ |
| ss_base_score  |   |  - Defines the starting score to be applied to a risk object <br/> - Custom labels may be created to further customize generated base scores|
|  ss_master |   |  - Master table for Spico SIEM to function<br/> - Defines the risk_rule which serves as a lazy primary key across most tables<br/>- Ties all tables together into a functional set of data to provide risk rules with enough information to generate a score|
|  ss_reliability |   |  - Defines a modifier to be applied to the overall score of a risk object in which it is assigned<br/>- Custom labels may be created to further customize generated scores |
| ss_search_filter  |   | Creates the ability to define a filter for restricting noisy data<br/>- Two different modes<br/>--filter - eliminates the possibility for results to display for the associated risk rule<br/>--suppression - with the existence of start_epoch and end_epoch, provides a way to suppress data from showing in results for a defined period of time. When end_epoch is reached, data will once again display.|

##Macros
#Runtime
##Risk Generator
##Incident Generator
#Views
##Executive View
##Scoreboard
##Management



###FlowChart

```flow
st=>start: Login
op=>operation: Login operation
cond=>condition: Successful Yes or No?
e=>end: To admin

st->op->cond
cond(yes)->e
cond(no)->op
```

###Sequence Diagram
                    
```seq
Andrew->China: Says Hello 
Note right of China: China thinks\nabout it 
China-->Andrew: How are you? 
Andrew->>China: I am good thanks!
```

###End
