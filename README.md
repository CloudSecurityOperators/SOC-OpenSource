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

# Architecture Diagram:
<p align="center"> <img src="images/SIEM2.png"> </p>

# Components used in this Project:
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

# Installation Requirements: 
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

# Installation Guide:
We will install and configure all of the components First and will move to Integrating them one by one.
## Elasticsearch-Kibana:
 - SSH into your VM created for Elastic SIEM
 - Run below commands to spin up elasticseach and kibana using docker. (Note- If any of the below utilities doesn't exists, use "sudo apt install <package>" )
 ```bash
 sudo apt update
 sudo apt upgrade
 sudo apt install docker-compose
 sudo apt install docker.io
 cd /
 wget https://raw.githubusercontent.com/archanchoudhury/SOC-OpenSource/main/codes/elk/docker-compose.yml?token=AMFWN76WO6EJP3LVF5DVHNLBWN7KQ
 sudo docker-compose up -d
 ```
  - Run below to check if the host is listening on 9200, 5601 to confirm the service
 ```bash
 netstat -ltpnd
 ```
  - Now access the Kibana Console from your browser using this- http://Public_IP_ofEc2:5601
 
## TheHive:
  - You can follow the detailed documentation **[HERE](https://docs.thehive-project.org/thehive/installation-and-configuration/installation/step-by-step-guide/)**
  - Referring below steps for installation on Ubuntu20
 ```bash
#Install JAVA
apt-get install -y openjdk-8-jre-headless
echo JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64" >> /etc/environment
export JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"

#Install Cassandra database
curl -fsSL https://www.apache.org/dist/cassandra/KEYS | sudo apt-key add -
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
sudo apt update
sudo apt install cassandra

#Run Cassandra
sudo service cassandra start

#Cassandra Configuration
cqlsh localhost 9042
cqlsh> UPDATE system.local SET cluster_name = 'thp' where key='local';
exit
nodetool flush
 
#Configure Cassandra by editing /etc/cassandra/cassandra.yaml file.
cluster_name: 'thp'
listen_address: 'xx.xx.xx.xx' # address for nodes
rpc_address: 'xx.xx.xx.xx' # address for clients
seed_provider:
    - class_name: org.apache.cassandra.locator.SimpleSeedProvider
      parameters:
          # Ex: "<ip1>,<ip2>,<ip3>"
          - seeds: 'xx.xx.xx.xx' # self for the first node
data_file_directories:
  - '/var/lib/cassandra/data'
commitlog_directory: '/var/lib/cassandra/commitlog'
saved_caches_directory: '/var/lib/cassandra/saved_caches'
hints_directory: 
  - '/var/lib/cassandra/hints'
 
#Save the config file and Restart the service
service cassandra restart

#Install TheHive
curl https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY | sudo apt-key add -
sudo apt-get update
sudo apt-get install thehive4
 
#Indexing Engine
mkdir /opt/thp/thehive/index
chown thehive:thehive -R /opt/thp/thehive/index
 
#File Storage
mkdir -p /opt/thp/thehive/files
chown -R thehive:thehive /opt/thp/thehive/files

#Check for the secret key if created or not here- /etc/thehive/secret.conf
 
#TheHive configuration file (/etc/thehive/application.conf) has to be edited
db {
  provider: janusgraph
  janusgraph {
    storage {
      backend: cql
      hostname: ["127.0.0.1"] # seed node ip addresses
      #username: "<cassandra_username>"       # login to connect to database (delete if not configured)
      #password: "<cassandra_passowrd"
      cql {
        cluster-name: thp       # cluster name
        keyspace: thehive           # name of the keyspace
        local-datacenter: datacenter1   # name of the datacenter where TheHive runs (relevant only on multi datacenter setup)
        # replication-factor: 2 # number of replica
        read-consistency-level: ONE
        write-consistency-level: ONE
      }
    }
  }
}

#Update Index in same config file (/etc/thehive/application.conf)
db {
  provider: janusgraph
  janusgraph {
    storage {
      [..]
    }
    ## Index configuration
    index.search {
      backend : lucene
      directory:  /opt/thp/thehive/index #this location needs to be changed if not done
    }
  }
}

#Update Filesystem in same config file (/etc/thehive/application.conf)
storage {
provider = localfs
localfs.location = /opt/thp/thehive/files #this location needs to be changed if not done
}
       
#Save the config and Run hive
service thehive start
       
#Verify the logs, process to check if the service has been started
sudo tail -f /var/log/thehive/application.log
netstat -ltpnd #you should see the server is listening on 9000    
 ```
 - Please note that the service may take some time to start. Once it is started, you may launch your browser and connect to http://YOUR_SERVER_ADDRESS:9000/.
 - The default admin user is admin@thehive.local with password secret. It is recommended to change the default password.
       
## Cortex
 - SSH into the EC2 VM created for Cortex
 - You can follow the detailed documentation **[HERE](https://github.com/TheHive-Project/CortexDocs/blob/master/installation/install-guide.md#elasticsearch-installation)**
 - Referring below steps for installation on Ubuntu20
```bash
#Install Elasticsearch 
sudo apt-get update
sudo apt install openjdk-8-jre-headless
java -version
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-7.x.list
sudo apt-get update    
sudo apt install elasticsearch

#Change below lines in the configation file of Elasticsearch here- /etc/elasticsearch/elasticsearch.yml
network.host: 0.0.0.0 #Uncomment + mentioned UniversalIP address for production use
http.port: 9200 #Uncomment this line
discovery.seed_hosts #Uncomment this line
node.name: node1 #Uncomment this line
cluster.initial_master_nodes: ["node1"] #uncomment this line and change acoordingly       
http.host: 127.0.0.1
cluster.name: hive
thread_pool.search.queue_size: 100000

#Now that Elasticsearch is configured, start it as a service and check whether it's running:
sudo systemctl enable elasticsearch.service
sudo systemctl start elasticsearch.service
sudo systemctl status elasticsearch.service

#Setup apt configuration with the release repository for Cortex
curl https://raw.githubusercontent.com/TheHive-Project/TheHive/master/PGP-PUBLIC-KEY | sudo apt-key add -
echo 'deb https://deb.thehive-project.org release main' | sudo tee -a /etc/apt/sources.list.d/thehive-project.list
sudo apt-get update
sudo apt install cortex

#Create Cortex key of the server
sudo mkdir /etc/cortex
(cat << _EOF_
# Secret key
# ~~~~~
# The secret key is used to secure cryptographics functions.
# If you deploy your application to several instances be sure to use the same key!
play.http.secret.key="$(cat /dev/urandom | tr -dc 'a-zA-Z0-9' | fold -w 64 | head -n 1)"
_EOF_
) | sudo tee -a /etc/cortex/application.conf

#Make modification in the cortex config file- /etc/cortex/application.conf
## ElasticSearch
search {
   uri = "http://Cortex_vm_ip:9200"

#Save config and Run Cortex service
sudo systemctl start cortex
tail -f /var/log/cortex/application.log #verify if cortex is starting successfully or not
       
#Access Cortex UI from browser using http://Cortex_VM_IP:9001/       
``` 
 - Once you are done with the installation and you access the Cortex UI, follow this [QUICK START](https://github.com/TheHive-Project/CortexDocs/blob/master/admin/quick-start.md) guide to set up Cortex   
### Setting up Analyzers and responders       
Neurons (analyzers and responders) are not embedded anymore in docker image. The recommended method to run neurons is to use docker. Official neurons have their own image is dockerhub under cortexneurons organisation. The catalog of available neurons is provided to Cortex using --analyzer-url/--responder-url parameters.
 - Currently, all the analyzers and responders supported by TheHive Project are written in Python 2 or 3. They don't require any build phase but their dependencies have to be installed. Before proceeding, you'll need to install the system package dependencies that are required by some of them.
 - Also you need to make sure you have python2, python3, pip2, pip3 all present in the VM, otherwise it will cause errors. You can follow below steps to ensure them.
```bash
#Get pip2
wget https://bootstrap.pypa.io/pip/2.7/get-pip.py
python2 get-pip.py

#Get python2
sudo apt install python2.7       

#Dependencies & setup tools
sudo apt-get install -y --no-install-recommends python-pip python2.7-dev python3-pip python3-dev ssdeep libfuzzy-dev libfuzzy2 libimage-exiftool-perl libmagic1 build-essential git libssl-dev
sudo pip install -U pip setuptools && sudo pip3 install -U pip setuptools
       
#Once finished, clone the Cortex-analyzers repository in the directory of your choosing
git clone https://github.com/TheHive-Project/Cortex-Analyzers

#Install Analyzers
for I in $(find Cortex-Analyzers -name 'requirements.txt'); do sudo -H pip2 install -r $I; done && \
for I in $(find Cortex-Analyzers -name 'requirements.txt'); do sudo -H pip3 install -r $I || true; done

#Change the path of Analyzers in config file- /etc/cortex/application.conf
analyzer {
  # Directory that holds analyzers
  path = [
    "/path/to/default/analyzers",
    "/path/to/my/own/analyzers" #Change it here, for us it was /opt/cortex/Cortex-Analyzers/analyzers
  ]  

#Save the config and Restart Cortex service
sudo systemctl restart cortex
       
```       
 - Now refresh the Cortex UI with the newly created user, you should see the analyzers.
       
## MISP
 - You can refer the clear installation Steps [HERE](https://misp.github.io/MISP/INSTALL.ubuntu2004/)
 - For setting up the MISP for first time, watch the tutorial [HERE](https://youtu.be/gSzop2pKM1I)
