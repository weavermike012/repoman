

# repoman
config files, ansible playbooks, scripts

## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![alt text](https://raw.githubusercontent.com/weavermike012/repoman/main/Diagrams/azurenet.jpg)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the Ansible folder's files may be used to install only certain pieces of it, such as Filebeat.

[Filebeat Playbook](https://github.com/weavermike012/repoman/blob/main/Ansible/filebeat-playbook.yml)


This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting inbound access to the network.
   The load balancer ensures that the load from the backend pool(three VM's running a DVWA in a docker container) will be shared and the traffic distributed across all three for redundancy. The DVWA on the backend pool, which are part of the subnet, are made public by the loadbalancer's front end IP address. 

  The Jump Box provisioner is the gateway to the network and allows the configuration of the servers and the automation via the docker/ansible container running within. The jump box also gives an extra layer of security between the network and within a controlled environment.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the data and system logs.
- Filebeat monitors log files or locations that you specify, collects log events, and forwards them to Elasticsearch or Logstash for indexing. 

- Metricbeat takes the metrics and statistics that it collects and ships them to the output specified such as Elasticsearch or Logstash in addition to helping you monitor servers by collecting metrics from the system and services running on the server, such as: Apache(in this instance)

   see: https://www.elastic.co/

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.1.0.4/13.66.135.75  | Linux            |
| WEBvm-1  | WebServer| 10.1.0.8   | Linux            |
| WEBvm-2  | WebServer| 10.1.0.9   | Linux            |
| WEBvm-3  | WebServer| 10.1.0.10  | Linux            |
| ELKvm    | ELK      | 10.2.0.4/52.159.104.201   | Linux            |
| LBalancer| PublicIP | 52.160.141 | Linux            |
### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the ELK stack server machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses: My public workstation IP address

Machines within the network can only be accessed by ssh into the Jumpbox withing the ansible container: Which machine did you allow to access your ELK VM? What was its IP address?
   -The Jumpbox via ssh p22
   -Personal PC to view the Kibana website on port 5601

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | my pub ip            |
| WEBvm-1  | No                  | jbox 10.1.0.4        |
| WEBvm-2  | No                  | jbox 10.1.0.4        |
| WEBvm-3  | No                  | jbox 10.1.0.4        |
|ELK Server| Yes                 | my pub ip/10.1.0.4   |
| LoadB    | Yes                 | my pub ip            |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because it allows the configuration of 2 or 200 servers with a single file.

The playbook implements the following tasks:
- Installs the docker engine on the elk host
- Installs python-pip3
- Installs the Docker python module with pip
- Tells the system to use more memory and reloads
- Installs the docker elk container and configures it to always restart 

The following screenshot displays the result of running `docker ps` and `docker container list -a` after successfully configuring the ELK instance.

![alt text](https://raw.githubusercontent.com/weavermike012/repoman/main/Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines running DVWA:
- 10.1.0.8, 10.1.0.9, 10.1.0.10

We have installed the following Beats on these machines:
- Filebeat & Metricbeat on 10.1.0.8, 10.1.0.9, 10.1.0.10 Web server VMs

These Beats allow us to collect the following information from each machine:
-Filebeat feeds logs into the ELK server via the Web Servers
-Metric beat feeds metric stats to the ELK server via the Webservers

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

  1) SSH into your jumpbox from your workstation
  2) Start and attach to the ansible container within
  3) copy the [elk.yml installation Playbook](https://github.com/weavermike012/repoman/blob/main/Ansible/elk.yml) into /etc/ansible/roles
  4) Update the [/etc/ansible/hosts](https://github.com/weavermike012/repoman/blob/main/Ansible/hosts.yml) file to include the elk servers private ip address(10.2.0.4 in this case)
  5) Within your jumpbox and inside the ansible container run `ansible-playbook elk.yml` and navigate to your elk stack servers public ip [elkstackpubip]:5601 to view the the Kibana interface to verify a working installation.

  
 ----------------------------------------------
 Once you have successfully ssh'd into your jumpbox from home/school run the following commands to list, start, and attach to your docker/ansible container.
root@Jump-Box-Provisioner:~$ sudo docker container list -a
CONTAINER ID   IMAGE                           COMMAND                  CREATED       STATUS                          PORTS     NAMES
64ae06fd699d   cyberxsecurity/ansible:latest   "/bin/sh -c /bin/bas…"   2 weeks ago   Exited (0) About a minute ago             festive_meitner

root@Jump-Box-Provisioner:~$ sudo docker start festive_meitner
festive_meitner

root@Jump-Box-Provisioner:~$ sudo docker attach festive_meitner

(You will now be inside of your ansible container) 

root@64ae06fd699d:~# nano /etc/ansible/hosts
  (copy the above [hosts](https://github.com/weavermike012/repoman/blob/main/Ansible/hosts.yml) file and paste it into yours or make the appropriate changes to the ip address under [elk])
  
root@64ae06fd699d:/etc/ansible/roles# nano elk.yml
  (copy the ansible playbook for [elk](https://github.com/weavermike012/repoman/blob/main/Ansible/elk.yml) and paste into elk.yml)
  
root@64ae06fd699d:/etc/ansible/roles# ansible-playbook elk.yml
runs the [elk.yml](https://github.com/weavermike012/repoman/blob/main/Ansible/elk.yml) playbook

 you should see something similar to
  ![alt text](https://raw.githubusercontent.com/weavermike012/repoman/main/Images/elkplaybookrun.JPG)
 
 

 - Now head on over to the forward facing ip address of your elk stack server..
![alt text](https://raw.githubusercontent.com/weavermike012/repoman/main/Images/kibana.jpg)

  
  
  


 
 
