# Project## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

![diagram](https://github.com/ZakKalve/Project/blob/main/diagrams/week%2012%20vm%20diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the _____ file may be used to install only certain pieces of it, such as Filebeat.

Playbook 1: playbook.yml

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


Playbook 2: install-elk.yml

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

Playbook 3: filebeat-playbook.yml

  ---
  - name: installing and launching filebeat
    hosts: webservers
    become: yes
    tasks:
    
    - name: download filebeat deb
      command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.6.1-amd64.deb 

    - name: install filebeat deb
      command: sudo dpkg -i filebeat-7.6.1-amd64.deb

    - name: drop in filebeat.yml
      copy:
        src: /etc/ansible/filebeat-config.yml
        dest: /etc/filebeat/filebeat.yml
    
    - name: enable and configure system module
      command: sudo filebeat modules enable system

    - name: setup filebeat
      command: sudo filebeat setup
  
    - name: start filebeat service
      command: sudo service filebeat start

    - name: enable service filebeat on boot
      systemd:
        name: filebeat
        enabled: yes



This document contains the following details:
- Description of the Topologu
- Access Policies
- ELK Configuration
  - Beats in Use
  - Machines Being Monitored
- How to Use the Ansible Build


### Description of the Topology

The main purpose of this network is to expose a load-balanced and monitored instance of DVWA, the D*mn Vulnerable Web Application.

Load balancing ensures that the application will be highly available, in addition to restricting access to the network.
- Load balancers help solve the performance, economy, and availability problems. The advantage of a jump box

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the configuration and system files.
- Filebeat monitors the log files or locations that you specify, collects log events, and forwards them either to ELasticsearch or Logstash for indexing.
- Metricbeat collects machine metrics, such as uptime.

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.1   | Linux            |
| Web-1    | DVWA     | 10.1.0.8   | Linux            |
| Web2     | DVWA     | 10.1.0.7   | Linux            |
| Elk-VM   | DVWA     | 10.2.0.5   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- 98.193.55.58

Machines within the network can only be accessed by Jump Box.
- I allowed only the Jump Box machine to access the ELK-VM. Its IP address is 10.1.0.4

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes/No              | 10.0.0.1 10.0.0.2    |
|          |                     |                      |
|          |                     |                      |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
- _TODO: What is the main advantage of automating configuration with Ansible?_

The playbook implements the following tasks:
- _TODO: In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc._
- ...
- ...

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![TODO: Update the path with the name of your screenshot of docker ps output](Images/docker_ps_output.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- _TODO: List the IP addresses of the machines you are monitoring_

We have installed the following Beats on these machines:
- _TODO: Specify which Beats you successfully installed_

These Beats allow us to collect the following information from each machine:
- _TODO: In 1-2 sentences, explain what kind of data each beat collects, and provide 1 example of what you expect to see. E.g., `Winlogbeat` collects Windows logs, which we use to track user logon events, etc._

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the _____ file to _____.
- Update the _____ file to include...
- Run the playbook, and navigate to ____ to check that the installation worked as expected.

_TODO: Answer the following questions to fill in the blanks:_
- _Which file is the playbook? Where do you copy it?_
- _Which file do you update to make Ansible run the playbook on a specific machine? How do I specify which machine to install the ELK server on versus which to install Filebeat on?_
- _Which URL do you navigate to in order to check that the ELK server is running?

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
