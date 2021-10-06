---
title: "Sc exe Manipulating Windows Services"
excerpt: "Windows Service"
categories:
  - Endpoint
last_modified_at: 2020-07-21
toc: true
tags:
  - TTP
  - T1543.003
  - Windows Service
  - Persistence
  - Privilege Escalation
  - Splunk Enterprise
  - Splunk Enterprise Security
  - Splunk Cloud
  - Endpoint
  - Installation
---



[Try in Splunk Security Cloud](https://www.splunk.com/en_us/cyber-security.html){: .btn .btn--success}

#### Description

This search looks for arguments to sc.exe indicating the creation or modification of a Windows service.

- **Type**: TTP
- **Product**: Splunk Enterprise, Splunk Enterprise Security, Splunk Cloud
- **Datamodel**: [Endpoint](https://docs.splunk.com/Documentation/CIM/latest/User/Endpoint)
- **Last Updated**: 2020-07-21
- **Author**: Rico Valdez, Splunk
- **ID**: f0c693d8-2a89-4ce7-80b4-98fea4c3ea6d


#### ATT&CK

| ID          | Technique   | Tactic       |
| ----------- | ----------- |--------------|
| [T1543.003](https://attack.mitre.org/techniques/T1543/003/) | Windows Service | Persistence, Privilege Escalation |


#### Search

```

| tstats `security_content_summariesonly` values(Processes.process) as process min(_time) as firstTime max(_time) as lastTime from datamodel=Endpoint.Processes where Processes.process_name = sc.exe (Processes.process="* create *" OR Processes.process="* config *") by Processes.process_name Processes.parent_process_name Processes.dest Processes.user 
| `drop_dm_object_name(Processes)` 
| `security_content_ctime(firstTime)` 
| `security_content_ctime(lastTime)` 
| `sc_exe_manipulating_windows_services_filter`
```

#### Associated Analytic Story
* [Windows Service Abuse](/stories/windows_service_abuse)
* [DHS Report TA18-074A](/stories/dhs_report_ta18-074a)
* [Orangeworm Attack Group](/stories/orangeworm_attack_group)
* [Windows Persistence Techniques](/stories/windows_persistence_techniques)
* [Disabling Security Tools](/stories/disabling_security_tools)
* [NOBELIUM Group](/stories/nobelium_group)


#### How To Implement
To successfully implement this search you need to be ingesting information on process that include the name of the process responsible for the changes from your endpoints into the `Endpoint` datamodel in the `Processes` node.

#### Required field
* _time
* Processes.process_name
* Processes.process
* Processes.parent_process_name
* Processes.dest
* Processes.user


#### Kill Chain Phase
* Installation


#### Known False Positives
Using sc.exe to manipulate Windows services is uncommon. However, there may be legitimate instances of this behavior. It is important to validate and investigate as appropriate.



#### RBA

| Risk Score  | Impact      | Confidence   | Message      |
| ----------- | ----------- |--------------|--------------|
| 56.0 | 70 | 80 | A sc process $process_name$ with commandline $process$ to create of configure services in host $dest$ |



#### Reference


#### Test Dataset
Replay any dataset to Splunk Enterprise by using our [`replay.py`](https://github.com/splunk/attack_data#using-replaypy) tool or the [UI](https://github.com/splunk/attack_data#using-ui).
Alternatively you can replay a dataset into a [Splunk Attack Range](https://github.com/splunk/attack_range#replay-dumps-into-attack-range-splunk-server)

* [https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1543.003/atomic_red_team/windows-sysmon.log](https://media.githubusercontent.com/media/splunk/attack_data/master/datasets/attack_techniques/T1543.003/atomic_red_team/windows-sysmon.log)



[*source*](https://github.com/splunk/security_content/tree/develop/detections/endpoint/sc_exe_manipulating_windows_services.yml) \| *version*: **4**