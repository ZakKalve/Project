# Project## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![diagram](https://github.com/ZakKalve/Project/blob/main/diagrams/week%2012%20vm%20diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the _____ file may be used to install only certain pieces of it, such as Filebeat.

#### Playbook 1: playbook.yml

  ---
  - name: Config Web VM with Docker
    hosts: webservers
    become: true
    tasks:

    - name: Install docker.io
      apt:
        force_apt_get: yes
        update_cache: yes 
        name: docker.io
        state: present
   
    - name: Install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

    - name: Install Python Docker module
      pip:
        name: docker
        state: present
    
    - name: download and launch a docker web container
      docker_container:
        name: dvwa
        image: cyberxsecurity/dvwa
        state: started
        restart_policy: always
        published_ports: 80:80

    - name: Enable docker service
      systemd:
        name: docker
        enabled: yes


#### Playbook 2: install-elk.yml

  ---
  - name: config elk VM with docker
    hosts: elk
    remote_user: azadmin  
    become: True
    tasks:
    - name: Config_map_count
      sysctl:
        name: vm.max_map_count
        value: 262144
        sysctl_set: yes

    - name: docker.io
      apt:
        force_apt_get: yes
        update_cache: yes
        name: docker.io
        state: present

    - name: install pip3
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present

    - name: install docker python module
      pip:
        name: docker
        state: present

    - name: download and launch a docker web container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        published_ports:
          - 5601:5601
          - 9200:9200
          - 5044:5044
 
    - name: enable service docker on boot
      systemd:
        name: docker
        enabled: yes

#### Playbook 3: setup.yml

  ---
  - name: Configure Web Servers
    hosts: webservers
    become: yes
    tasks:

    - name: Install docker.io
      apt:
        name: docker.io
        state: present
        update_cache: yes


This document contains the following details:
- Description of the Topology
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
- Load balancers help solve the performance, economy, and availability problems. The advantage of a jump box is that it minimises the attack surface by funneling remote connections through the virtual machine. 

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the configuration and system files.
- Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to Elasticsearch or Logstash for indexing.
- Metricbeat collects machine metrics, such as uptime.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.1   | Linux            |
| Web-1    | DVWA     | 10.1.0.8   | Linux            |
| Web2     | DVWA     | 10.1.0.7   | Linux            |
| Elk-VM   | Elk      | 10.2.0.5   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 98.193.55.85

Machines within the network can only be accessed by Jump Box.
- I allowed only the Jump Box machine to access the ELK-VM via SSH. Its IP address is 10.1.0.4

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes (SSH)           | 98.193.55.85         |
| Web-1    | Yes (HTTP)          | 98.193.55.85         |
| Web2     | Yes (HTTP)          | 98.193.55.85         |
| Elk-VM   | Yes (HTTP)          | 98.193.55.85         |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because
servcies running can be limited, system installation and update can be streamlined, and processes become more replicable.

Playbooks
The aformentioned playbooks implement the following tasks:

Playbook 1: playbook.yml

- Installs Docker
- Installs Python
- Installs Python Docker Module
- Downloads and launches the DVWA Docker container
- Enables the Docker service

Playbook 2: install-elk.yml

- Installs Docker
- Installs Python
- Installs Docker's Python Module
- Increases virtual memory to 262144 to support the ELK stack
- Downloads and launches the Docker ELK container

Playbook 3: filebeat-playbook.yml

- Downloads and installs Filebeat
- Enables and configures the system module
- Configures and launches Filebeat
- Enables Filebeat on boot

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![Screenshot](https://github.com/ZakKalve/Project/blob/main/diagrams/sudo%20docker%20ps.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1 10.1.0.8
- Web2  10.1.0.7

We have installed the following Beats on these machines:
- Filebeat-7.6.1-darwin-x86_64.tar.gz
- Metricbeat-7.6.1-darwin-x86_64.tar.gz

These Beats allow us to collect the following information from each machine:
- Filebeat collects log events from the VM's, and forwards them either to Elasticsearch or Logstash for indexing.
- Metricbeat takes the metrics and statistics that it collects and ships them to the output that I specify. 

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the playbook file to the Elk-VM.
- Update the hosts file located at /etc/ansible/hosts to include the following:

[webservers]

- 10.1.0.8 ansible_python_interpreter=/usr/bin/python3
- 10.1.0.7 ansible_python_interpreter=/usr/bin/python3

[elk]

- 10.2.0.5 ansible_python_interpreter=/usr/bin/python3

- Run the playbooks, and navigate to Kibana to check that the installation worked as expected.
  - filebeat-playbook.yml
  - install-elk.yml
  - metricbeat-playbook.yml

- Copy the filebeat-playbook to the Elk-VM
- You would update the hosts file (/etc/ansible/) to include the virtual machines that ansible is going to be run on, in addition to this you also list the web/elk servers IP
- In order to check that the ELK server is running, go to the http://[your.VM.IP]:5601/app/kibana
- Run the filebeat-playbook with ansible-playbook filebeat-playbook.yml command.
- You can make and edit playbooks with nano. 
