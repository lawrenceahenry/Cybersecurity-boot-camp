# Cybersecurity-boot-camp
Project 1
## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

https://app.diagrams.net/#G1zPLZgBGqDFZ9h9GnVdb6dDyJT6rMKglO
https://drive.google.com/file/d/1zPLZgBGqDFZ9h9GnVdb6dDyJT6rMKglO/view?usp=sharing

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the YML file may be used to install only certain pieces of it, such as Filebeat.

  name: Configure Elk VM with Docker
  hosts: elk
  remote_user: azadmin
  become: true
  task:
    # Use apt module
    - name: install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present

    # Use apt module
    - name: install docker.io
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
      
    # Use pip module
    - name: Install Docker python module
      pip:
        name: docker
        state: present
      
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
      
    # Use docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        #Please list the ports that ELK runs on
        published_ports:
          5601:5601
          9200:9200
          5044:5044
        
    # Use systemd module
    - name: Enable service docker on boot
      systemd:
        name: docker
        enabled: yes


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

What aspect of security do load balancers protect? What is the advantage of a jump box?

- Load balancing protects the availability of the network by distributing incoming traffic to the available servers. This insures that the network remains available to the clients accessing the web application in the event of high traffic, breakdow, or system compromise. The load balancer sits between the client and the application servers and is not visible to the clients.

- A jump box can be used by network admins to provide simple and secure access to make application or administrative changes to the network.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the jump box and system network.

What does Filebeat watch for?
- Changes to files
What does Metricbeat record?
- Metrics from the operating system and services running on the server

The configuration details of each machine may be found below.
_Note: Use the [Markdown Table Generator](http://www.tablesgenerator.com/markdown_tables) to add/remove values from the table_.

| Name     | Function  | IP Address    | Operating System |
|----------|-----------|---------------|------------------|
| Jump Box | Gateway   | 10.0.0.4      | Linux            |
| Web-1    | Webserver | 10.0.0.5      | Linux            |
| Web-2    | Webserver | 10.0.0.6      | Linux            |
| ELK      | Monitoring| 10.1.0.4      | Linux            |
| LB       | Balancing | 104.43.196.18 | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the jump box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
- My local desktop 75.51.37.56

Machines within the network can only be accessed by jump box.
Which machine did you allow to access your ELK VM? What was its IP address?
- My local desktop 75.51.37.56 via port 5601

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes                 | 75.51.37.56          |
| Web-1    | No                  | 10.1.0.4             |
| Web-2    | No                  | 10.1.0.4             |
| ELK      | No                  | 10.0.0.5 / 10.0.0.6  |
| LB       | Yes                 | 104.43.196.18*       |
*Note: Load balancer IP changes each time it is rebooted

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because...
What is the main advantage of automating configuration with Ansible?
- Multiple machines can be correctly configured at once without concern about errors due to multiple manual configurations

The playbook implements the following tasks:
In 3-5 bullets, explain the steps of the ELK installation play. E.g., install Docker; download image; etc.
- Install Docker.io
- Install pip3
- Install Docker Python module
- Increase and access additional virtual memory
- Download and launch Docker instance

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

azadmin@ELK:~$ sudo docker ps
CONTAINER ID   IMAGE          COMMAND                  CREATED      STATUS       PORTS                                                                              NAMES
ef925de6d787   sebp/elk:761   "/usr/local/bin/star…"   8 days ago   Up 2 hours   0.0.0.0:5044->5044/tcp, 0.0.0.0:5601->5601/tcp, 0.0.0.0:9200->9200/tcp, 9300/tcp   elk

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1  10.0.0.5
- Web-2  10.0.0.6

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat collects log files and network data
- Metricbeat collects machine metrics such as CPU & memory usage which can be used to spot trend, potential system problems, or possibly attacks

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the playbook file to Ansible control node.
- Update the hosts file to include webservers and elk.
- Run the playbook, and navigate to http://40.71.94.66:5601/app/kibana to check that the installation worked as expected.

Answer the following questions to fill in the blanks:
- Which file is the playbook? Where do you copy it? filebeat-playbook.yml & metricbeat-playbook.yml  Both files copied to the /etc/ansible/roles directory
- Which file do you update to make Ansible run the playbook on a specific machine? filebeat-config.yml & metricbeat-config.yml
- How do I specify which machine to install the ELK server on versus which to install Filebeat on?
- Which URL do you navigate to in order to check that the ELK server is running? http://40.71.94.66:5601/app/kibana

_As a **Bonus**, provide the specific commands the user will need to run to download the playbook, update the files, etc._
