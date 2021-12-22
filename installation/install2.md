# Installation Guide(Second Phase):
We will install and configure all of the components First and will move to Integrating them one by one.
## Snort
```bash
#Update your repositories
$ sudo apt-get update

#Install the prerequisites packages.       
$ sudo apt install -y gcc libpcre3-dev zlib1g-dev libluajit-5.1-dev libpcap-dev openssl libssl-dev libnghttp2-dev libdumbnet-dev bison flex libdnet autoconf libtool       
       
#Create a temporary folder to download and compile Snort.
$ mkdir ~/snort_temp && cd ~/snort_temp
$ wget https://www.snort.org/downloads/snort/daq-2.0.7.tar.gz       
       
#Extract the package.
$ tar -xvzf daq-2.0.7.tar.gz
$ cd daq-2.0.7
       
#Compile
$ autoreconf -f -i
$ ./configure && make && sudo make install
       
#Configuration
$ sudo ldconfig  
$ sudo ln -s /usr/local/bin/snort /usr/sbin/snort
$ sudo groupadd snort
$ sudo useradd snort -r -s /sbin/nologin -c SNORT_IDS -g snort
$ sudo mkdir -p /etc/snort/rules
$ sudo mkdir /var/log/snort
$ sudo mkdir /usr/local/lib/snort_dynamicrules
$ sudo chmod -R 5775 /etc/snort
$ sudo chmod -R 5775 /var/log/snort
$ sudo chmod -R 5775 /usr/local/lib/snort_dynamicrules
$ sudo chown -R snort:snort /etc/snort
$ sudo chown -R snort:snort /var/log/snort
$ sudo chown -R snort:snort /usr/local/lib/snort_dynamicrules
$ sudo touch /etc/snort/rules/white_list.rules
$ sudo touch /etc/snort/rules/black_list.rules
$ sudo touch /etc/snort/rules/local.rules
$ sudo cp ~/snort_src/snort-2.9.16/etc/*.conf* /etc/snort
$ sudo cp ~/snort_src/snort-2.9.16/etc/*.map /etc/snort
       
#Configuring Rules
$ wget https://www.snort.org/rules/community -O ~/community.tar.gz
$ sudo tar -xvf ~/community.tar.gz -C ~/
$ sudo cp ~/community-rules/* /etc/snort/rules
$ sudo vim /etc/snort/snort.conf
       var RULE_PATH /etc/snort/rules #In Snort configuration file, change the RULE_PATH pointing to the directory where we download the rules.
$ sudo vim /etc/snort/snort.conf

#Change the configurations below related to your server.
ipvar HOME_NET <YOUR_PUBLIC_IP>/32
ipvar EXTERNAL_NET !$HOME_NET
var RULE_PATH /etc/snort/rules
var SO_RULE_PATH /etc/snort/so_rules
var PREPROC_RULE_PATH /etc/snort/preproc_rules
var WHITE_LIST_PATH /etc/snort/rules
var BLACK_LIST_PATH /etc/snort/rules
output unified2: filename snort.log, limit 128
include $RULE_PATH/local.rules
       
#Check if the configuration will run properly after our changes.
$ sudo snort -T -c /etc/snort/snort.conf
       
#Test
$ sudo vim /etc/snort/rules/local.rules
alert icmp any any -> $HOME_NET any (msg:"ICMP test"; sid:10000001; rev:001;)       
       
$ /usr/local/bin/snort -A fast -c /etc/snort/snort.conf -q -i eth0

#Creating a Snort service
$ sudo vim /lib/systemd/system/snort.service
[Unit]
Description=Snort
After=syslog.target network.target

[Service]
Type=simple
ExecStart=/usr/local/bin/snort -A fast -c /etc/snort/snort.conf -q -i eth0

[Install]
WantedBy=multi-user.target  
       
#Reload the daemon and start Snort service.
$ sudo systemctl daemon-reload
$ sudo systemctl start snort
$ sudo systemctl status snort       
```       
## Cowrie Honeypot
       
```bash
$ sudo apt-get update

#Install dependencies packages.
$ sudo apt-get install -y git python-virtualenv libssl-dev libffi-dev build-essential libpython3-dev python3-minimal authbind virtualenv
       
#Create a user account for cowrie.       
$ sudo adduser --disabled-password cowrie
$ sudo su - cowrie
       
#Download the source code.
$ git clone http://github.com/cowrie/cowrie
$ cd cowrie
       
#Setup the virtual environment and install prerequisite packages.
$ virtualenv --python=python3 cowrie-env
$ source cowrie-env/bin/activate
$ pip install --upgrade pip
$ pip install --upgrade -r requirements.txt
       
#Make a copy of the default configuration file of cowrie    
$ cd /home/cowrie/cowrie/etc
$ cp cowrie.cfg.dist cowrie.cfg
$ vim cowrie.cfg
[honeypot]
hostname = database (here, choose a hostname to trick the attacker)
listen_endpoints = tcp:22:interface=0.0.0.0 (change the default SSH port to port 22)
[telnet]
enabled = true
listen_endpoints = tcp:23:interface=0.0.0.0     
       
#Run authbind to listen as non-root on port 22 and 23 directly.
$ sudo apt-get install authbind
$ sudo touch /etc/authbind/byport/22
$ sudo chown cowrie:cowrie /etc/authbind/byport/22
$ sudo chmod 770 /etc/authbind/byport/22
$ sudo touch /etc/authbind/byport/23
$ sudo chown cowrie:cowrie /etc/authbind/byport/23
$ sudo chmod 770 /etc/authbind/byport/23
       
#Change the default SSH port connection for your operation system.
$ sudo vim /etc/ssh/sshd_config
# uncomment the Port line and change the number
Port 3393
       
#Restart ssh service and start
$ sudo systemctl restart sshd
$ bin/cowrie start       
```       
