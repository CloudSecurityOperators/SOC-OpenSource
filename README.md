## PRESENTED BY <p align="center"> <img src="images/Logo-Transparent for Black BG.png" width="220" height="200"> </p>
# SOC-OpenSource
This is a Project Designed for Security Analysts and all SOC audiences who wants to play with implementation and explore the Modern SOC architecture. All of the componenets are used based on Open Source Projects(Availabe at the time of first commit). 

This Projects serves below usecases:
 - **Collect Data** to a Single Place.
 - **Normalize** and **Parse Data**
 - **Visualize Data** and prepare meaningful Security Analytics
 - Create **Incidents/Cases** out of Secuirty Alerts identified based on collected data/logs
 - **Automate** process of Threat Hunt, Creation of actionable Playbooks, SOC data Analytics
 - **Automate** the process of analsis observables they have collected, **at scale, by querying a single tool** instead of several
 - Actively respond to threats and interact with the constituency and other teams
 - **Enrich** Data feeds with Open Source Threat Intelligence Platoform

# Index:
 - [Architecture Diagram](#Architecture-Diagram)
 - [Components used in this Project](#Components)
 - [Installation Requirements](#Installation-Requirements)
 - [Installation Guide](https://github.com/archanchoudhury/SOC-OpenSource/blob/main/installation/install.md)
 - [Integration Guide](https://github.com/archanchoudhury/SOC-OpenSource/blob/main/integration/integration.md)
 - [Contributing](#Contributing)
 - [Support](#Support)

# Architecture-Diagram:
<p align="center"> <img src="images/SIEM2.png"> </p>

# Components:
All of the components used in this projects are Open Source.
 - **Elastic SIEM**: Open source SIEM platform powered by ElasticSearch, Logstash, Kibana
 - **TheHive**: [TheHive](https://thehive-project.org/) is a scalable 3-in-1 open source and free Security Incident Response Platform designed to make life easier for SOCs, CSIRTs, CERTs and any information security practitioner dealing with security incidents that need to be investigated and acted upon swiftly.
    - Official GitRepo of TheHive is **[HERE](https://github.com/TheHive-Project/TheHive)**
 - **Cortex**: Cortex, an open source and free software, has been created by TheHive Project for this very purpose. Observables, such as IP and email addresses, URLs, domain names, files or hashes, can be analyzed one by one or in bulk mode using a Web interface. Analysts can also automate these operations thanks to the Cortex REST API.
    - Official GitRepo of Cortex is **[HERE](https://github.com/TheHive-Project/Cortex)**
 - **MISP**: MISP is an open source software solution for collecting, storing, distributing and sharing cyber security indicators and threats about cyber security incidents analysis and malware analysis. MISP is designed by and for incident analysts, security and ICT professionals or malware reversers to support their day-to-day operations to share structured information efficiently.
   - Official GitRepo of MISP is **[HERE](https://github.com/MISP/MISP)**
 - **Jupyter Notebook**: The Jupyter Notebook is a web-based interactive computing platform. The notebook combines live code, equations, narrative text, visualizations etc.
   - Official website of Jupyter is **[HERE](https://jupyter.org/)**

# Installation-Requirements: 
We have created the environment in AWS. You can follow along or choose any other alternative cloud provider. Or ever you can utilize EKS to deploy the full setup.
## VM Requirements:
 - MISP- Ubuntu20- t3.micro
 - Elastic SIEM- Ubuntu20- t2.medium (Best performence can be achived on t2.large)
 - Cortex- Ubuntu20- t3a.medium (Can work on t2.medium as well)
 - TheHive- Ubuntu20- t2.medium
## Network Rules:
| Ports | IP Ranges | Comments |
| --- | --- | --- |
| 22 | Your IP | SSH to the VMs |
| 443 | Your IP | Accessing MISP UI on browser|
| 9200 | Your IP | Accessing ElasticSearch|
| 5601 | Your IP | Accessing Kibana UI
| 9001 | Your IP | Accessing Cortex UI|
| 9000 | Your IP | Accessing TheHive UI|
| All TCP | Cortex VM IP | Accssing inbound API|
| All TCP | MISP VM IP | Accssing inbound API|
| All TCP | TheHive VM IP | Accssing inbound API|

# Contributing
We welcome your contributions. Please feel free to fork the code, play with it, make some patches and send us pull requests. 

# Support
 - Please [open an issue on GitHub](https://github.com/archanchoudhury/SOC-OpenSource/issues/new) if you'd like to report a bug or request a feature.
 - For real DFIR Training, subscribe to my [YouTube Channel](https://www.youtube.com/c/BlackPerl)
 - If you like to support my creation, <p align="left"><a href="https://www.buymeacoffee.com/BlackPerl"> <img src="images/KULQlzAg.png" width="210" height="60"></p>
