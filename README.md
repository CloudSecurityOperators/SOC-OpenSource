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
 - [Integration Guide]
 - [Contributing]
 - [Support]

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

