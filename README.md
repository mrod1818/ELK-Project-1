# ELK-Project-1
Automated ELK Stack Deployment
The files in this repository were used to configure the network depicted below.
Project-1-Red-Team-Network-Diagram.png


These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the Project-1-Red-Team-network-diagram file may be used to install only certain pieces of it, such as Filebeat.
  



This document contains the following details:
* Description of the Topology                   
* Access Policies
* ELK Configuration
o Beats in Use
o Machines Being Monitored
* How to Use the Ansible Build


Description of the Topology
The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.
Load balancing ensures that the application will be highly functional, in addition to restricting high traffic to the network.

* What aspect of security do load balancers protect?

o Load balancers help prevent the servers from being overloaded as well as maximizes uptime, and optimizers productivity. 
o Which allows for high availability by rerouting live traffic from one server to another protecting against attacks such as (DDoS) denial- of- service attack.  

* What is the advantage of a jump box?

o You can increase the security of your entire network with a jump box since it only has one path in via SSH. 
o It also provides you with the ability for easy installation, updates, maintenance, and monitoring through a single VM gateway. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the data on the machine and system logs.
* What does Filebeat watch for? 
      
o Monitors the log files that you specify, collects log events, and forwards them to Logstash for indexing. 
* What does Metricbeat record?_

o Metricbeat takes the metrics and statistics that it collects and sends the output that you specify such as Logstash. The output data can then be viewed in things such as Kibana. 







The configuration details of each machine may be found below.

|   Name                | Function         | IP Address   |              OS                 |
| --------------------- | :----------------| ------------:| --------------------------------|
| Jump-Box-Provisioner  |  Gateway            |10.1.0.4 (Private)      |Linux ubuntu(20.04)
|                       |                     |52.188.69.108 (Public)   |  
| ELK-VM               |    Monitoring-Server  |10.0.0.4 (Private) |Linux ubuntu(20.04)
|                      |                      |104.43.231.206(Public)|     
|Web-1                  |   Web-Server         |10.1.0.5 (Private)|Linux ubuntu(20.04)
|                       |                      |104.45.196.167(Public)|    
|Web-2                  |   Web-Server         |10.1.0.6 (Private)|Linux ubuntu(20.04)
|                        |                    |104.45.196.67(Public)|    

 


# Access Policies
The machines on the internal network are not exposed to the public Internet. 
* Only the Jump-Box-Provisioner can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses: 
   o 24.107.133.208 ( LocalHost IP address) 

Machines within the network can only be accessed by the Jump-Box-Provisioner.

* Which machine did you allow to access your ELK VM? 
o Allows Jump-Box-Provisoner to SSH through port 22
      
* What was its IP address? 
o 10.0.0.4/32
A summary of the access policies in place can be found in the table below.
 

| Name                 |Publicly Accessible |Allowed IP Addresses|
| -----                | :----------------: |----------          |
| Jump-Box-Provisioner |Yes                 |24.107.133.208      |
|                      |                    |Personal IP         |
|ELK-VM                |No                  | 10.0.0.4           |
|                      |                    | Virtual Network    |    
|Web-1                 |No                  |10.1.0.5            |
|                      |                    |Virtual Network|
|Web-2                 |No                 | 10.1.0.6|
|                      |                    |Virtual Network|


 All these VMs can only be accessed from the Jump-Box-Provisioner 




# Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because services running can be limited, system installation and update can be streamlined, and processes become more replicable. 

What is the main advantage of automating configuration with Ansible?
	
o Ansible can be run from the command line and will ensure our provisioning scripts run identical everywhere. Ansible automation helps with the representation of Infrastructure as Code (IAC). 
The playbook implements the following tasks:
o Configure Elk VM with Docker: 
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: azadmin
  become: true
  tasks:

 o Install docker.io
 o Install python3-pip
 o Install Docker module

- name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present

      # Use apt module
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

      # Use pip module (It will default to pip3)
    - name: Install Docker module
      pip:
        name: docker
        state: present



o Increase virtual memory

# Use command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144

      # Use sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: "262144"
        state: present
        reload: yes

o Download and launch a docker elk container
   # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        # Please list the ports that ELK runs on
        published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044

o Enable service docker on boot  
   # Use systemd module
    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes
 
The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance. 

![image](https://user-images.githubusercontent.com/88940042/148263631-7b7ce0fe-0986-4653-a0b3-abf8dc6b802a.png)






Target Machines & Beats
This ELK server is configured to monitor the following machines:
	
o Web-1 10.1.0.5
o Web-2 10.1.0.6

      We have installed the following Beats on these machines:
o  filebeat-7.4.0-amd64.deb

https://github.com/mrod1818/ELK-Project-1/blob/main/Filebeat_Module_Status_Screenshot.PNG


o  metricbeat-7.4.0-amd64.deb

![image](https://user-images.githubusercontent.com/88940042/148263848-dd9c86cf-1bbe-4de9-a269-846f0d58365d.png)



These Beats allow us to collect the following information from each machine:

o filebeat-7.4.0-amd64.deb Installed as an agent on your servers, filebeat is used to monitor the log directories or specific log files and forwards them to either Elasticsearch or Logstash for indexing. Example would be the logs produced from the MySQL database supporting our application. 

o metricbeat-7.4.0-amd64.deb Collects the machines metrics. It is a measurement to tell analysts how healthy it is. Example of metricbeat can be CPU usage/Uptime. 

# Using the Playbook

In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 
SSH into the control node and follow the steps below:
Filebeat:
	
* Copy the configuration file to your webservers and ELK server. 
* Update /etc/ansible/hosts to include the ELK and webservers private IP address, mine for this project ELK private IP ( 10.0.0.4), Web-1 (10.1.0.5), Web-2 (10.1.0.6).  
* Run the playbook and navigate http://104.43.231.206:5601 (ELK public IP) to check that the installation worked as expected.

Metricbeat:

* Copy the metricbeat-config.yml to /etc/ansible/roles/files.

* Update the metricbeat-config.yml file to include the ELK private IP address.

* Run the playbook and navigate to http://104.43.231.206:5601 (ELK public IP) to check that the installation worked as expected. 

* Which file is the playbook? 
    o filebeat-playbok.yml 

* Where do you copy it? 
    o You copy file from /etc/ansible/files/filebeat-config.yml to /etc/ansible/roles/files/filebeat-config.yml

* Which file do you update to make Ansible run the playbook on a specific machine?
    o Update the hosts file /etc/ansible/hosts



*  How do I specify which machine to install the ELK server on versus which to install Filebeat on?
o At the beginning of the playbook you specify which hosts: 

- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

* Which URL do you navigate to in order to check that the ELK server is running?
to http://104.43.231.206:5601/app/kibana

As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc.

  -------Filebeat---------

- To create the filebeat-config.yml file: nano filebeat-config.yml. For this, I used the filebeat configuration file template.

- To create the playbook: nano filebeat-playbook.yml

---
- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

  - name: download filebeat deb
    copy:
      src: /etc/ansible/roles/filebeat-7.4.0-amd64.deb
      dest: /etc/filebeat-7.4.0-amd64.deb
  - name: install filebeat deb
    command: dpkg -i /etc/filebeat-7.4.0-amd64.deb

  - name: drop in filebeat.yml
    copy:
      src: /etc/ansible/files/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

  - name: enable and configure system module
    command: filebeat modules enable system

  - name: setup filebeat
    command: filebeat setup

  - name: start filebeat service
    command: service filebeat start

  - name: enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes

---
-To run the playbook: ansible-playbook filebeat-playbook.yml

* In order to run the playbook, you have to be in the directory the playbook is at, or give the path to it.


-------Metricbeat-------

- To create the metricbeat-config.yml file: nano metricbeat-config.yml. For this, I used the metricbeat configuration file template.

- To create the playbook: nano metricbeat-playbook.yml
---
- name: Install metric beat
  hosts: webservers
  become: true
  tasks:
    # Use command module
  - name: Download metricbeat
    copy:
      src: /etc/ansible/roles/metricbeat-7.4.0-amd64.deb
      dest: /etc/metricbeat-7.4.0-amd64.deb
    # Use command module
  - name: install metricbeat
    command: dpkg -i /etc/metricbeat-7.4.0-amd64.deb

    # Use copy module
  - name: drop in metricbeat config
    copy:
      src: /etc/ansible/files/metricbeat-config.yml
      dest: /etc/metricbeat/metricbeat.yml

    # Use command module
  - name: enable and configure docker module for metric beat
    command: metricbeat modules enable docker

    # Use command module
  - name: setup metric beat
    command: metricbeat setup

    # Use command module
  - name: start metric beat
    command: service metricbeat start

    # Use systemd module
  - name: enable service metricbeat on boot
    systemd:
      name: metricbeat
      enabled: yes

   
   - To run the playbook: ansible-playbook metricbeat-playbook.yml
   
   * In order to run the playbook, you have to be in the directory the playbook is at or give the path to it. 

