## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

Azure Week 13 Diagram ![ELK-Project/Diagram Folder/Azure Week 13 Diagram.png](https://github.com/EVman88/ELK-Project/blob/main/Diagram%20Folder/Azure%20Week%2013%20Diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

 Playbook 1: 
 ![webserver](https://github.com/EVman88/ELK-Project/blob/main/Ansible/configure_webserver.yml)

---
  - name: Webservers playbook
    hosts: webservers
    become: true
    tasks:

    - name: Uninstall apache httpd (state=present is optional)
      apt:
        name: apache2
        state: absent

    - name: Install docker.io (state=present is optional)
      apt:
        name: docker.io
        state: present

    - name: Install python3-pip (state=present is optional)
      apt:
        name: python3-pip
        state: present

    - name: Install docker  
      pip:
        name: docker

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
        
Playbook 2: configure_elk.yml ![https://github.com/EVman88/ELK-Project/blob/main/Ansible/configure_elk.yml]

---
- name: Configure Elk VM with Docker
  hosts: elk
  remote_user: azadmin
  become: true
  tasks:
    # apt module
    - name: Install docker.io
      apt:
        update_cache: yes
        force_apt_get: yes
        name: docker.io
        state: present
    
    # apt module
    - name: Install python3-pip
      apt:
        force_apt_get: yes
        name: python3-pip
        state: present
      
    # pip module 
    - name: Install Docker module
      pip:
        name: docker
        state: present
    
    # command module
    - name: Increase virtual memory
      command: sysctl -w vm.max_map_count=262144
      
    # sysctl module
    - name: Use more memory
      sysctl:
        name: vm.max_map_count
        value: '262144'
        state: present
        reload: yes
      
    # Docker_container module
    - name: download and launch a docker elk container
      docker_container:
        name: elk
        image: sebp/elk:761
        state: started
        restart_policy: always
        # ports run
        published_ports:
          -  5601:5601
          -  9200:9200
          -  5044:5044

Playbook 3: filebeat-playbeat.yml ![https://github.com/EVman88/ELK-Project/blob/main/Ansible/filebeat-playbook.yml]

---
- name: installing and launching filebeat
  hosts: webservers
  become: yes
  tasks:

  - name: download filebeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-7.4.0-amd64.deb 

  - name: install filebeat deb
    command: dpkg -i filebeat-7.4.0-amd64.deb 

  - name: drop in filebeat.yml 
    copy:
      src: /etc/ansible/files/filebeat-config.yml
      dest: /etc/filebeat/filebeat.yml

  - name: enable and configure system module
    command: filebeat modules enable system

  - name: setup filebeat
    command: filebeat setup

  - name: Start filebeat service
    command: service filebeat start

  - name: Enable service filebeat on boot
    systemd:
      name: filebeat
      enabled: yes

Playbook 4: metricbeat-playbook.yml ![https://github.com/EVman88/ELK-Project/blob/main/Ansible/metricbeat-playbook.yml]

---
- name: installing and launching metricbeat
  hosts: webservers
  become: yes
  tasks:

  - name: download metricbeat deb
    command: curl -L -O https://artifacts.elastic.co/downloads/beats/metricbeat/metricbeat-7.4.0-amd64.deb 

  - name: install metricbeat deb
    command: dpkg -i metricbeat-7.4.0-amd64.deb 

  - name: drop in metricbeat.yml 
    copy:
      src: /etc/ansible/files/metricbeat-config.yml
      dest: /etc/metricbeat/metricbeat.yml

  - name: enable and configure system module
    command: metricbeat modules enable docker

  - name: setup metricbeat
    command: metricbeat setup

  - name: Start metricbeat service
    command: service metricbeat start

  - name: Enable service metricbeat on boot
    systemd:
      name: metricbeat
      enabled: yes
      masked: no


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
-Load balancers provide security to applications during peak times as well as providing seamless access to the applications.
-The advantage of the jump box is that it allows an administrator to prevent attacks by only allowing specific remote connections. It prevents the other machines on the network from exposure.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the configuration and system files.
-Filebeat is used to monitor logs. 
-Metricbeat is used to gather metrics and statistics from servers running on the server and from the operating system.

The configuration details of each machine may be found below. 

| Name     | Function | IP Address | Operating System |
|----------|----------|------------|------------------|
| Jump Box | Gateway  | 10.0.0.4   | Linux            |
| Web-1    | DVWA     | 10.0.0.5   | Linux            |
| Web-2    | DVWA     | 10.0.0.6   | Linux            |
| Web-3    | DVWA     | 10.0.0.7   | Linux            |
| Elk-1    | ELK      | 10.1.0.4   | Linux            |

### Access Policies

The machines on the internal network are not exposed to the public Internet. 

Only the Jump Box machine can accept connections from the Internet. Access to this machine is only allowed from the following IP addresses:
-Personal IP

Machines within the network can only be accessed by the Jump Box.
-The Jump Box (IP address 10.0.0.4) is allowed to acces the ELK VM through port 22.

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses        |
|----------|---------------------|-----------------------------|
| Jump Box | Yes (SSH)           | Personal IP                 |
| Web-1    | NO                  | 10.0.0.4, 10.0.0.6, 10.0.0.7|
| Web-2    | NO                  | 10.0.0.4, 10.0.0.5, 10.0.0.7|                     
| Web-3    | NO                  | 10.0.0.4, 10.0.0.5, 10.0.0.6|
| ELK      | YES(HTTP)           | Personal IP, 10.0.0.4       |

### Elk Configuration

Ansible was used to automate configuration of the ELK machine. No configuration was performed manually, which is advantageous because we saved time and prevented errors by automaing the process and not having to SSH into each individual VM. 

The playbook implements the following tasks:
- Install docker.io
- Install python3-pip
- Install Docker module
- Increase virtual memory
- Use more memory
- Download and launch a docker elk container

The following screenshot displays the result of running `docker ps` after successfully configuring the ELK instance.

![sudo docker ps](https://github.com/EVman88/ELK-Project/blob/main/Images/sudo%20docker%20ps.png)

### Target Machines & Beats
This ELK server is configured to monitor the following machines:
- Web-1: 10.0.0.5
- Web-2: 10.0.0.6
- Web-3: 10.0.0.7

We have installed the following Beats on these machines:
- Filebeat
- Metricbeat

These Beats allow us to collect the following information from each machine:
- Filebeat monitors the log files or locations that you specify, collects log events, and forwards them for indexing. You would use filebeat to collect all logs.
- Metricbeat is used to gather metrics and statistics from servers running on the server and from the operating system. You could use metricbeat to gather statistics of all servers.

### Using the Playbook
In order to use the playbook, you will need to have an Ansible control node already configured. Assuming you have such a control node provisioned: 

SSH into the control node and follow the steps below:
- Copy the playbook files to the ansible docker container /etc/ansible.
- Update the ansible hosts file /etc/ansible/hosts to include:

[webservers]
10.0.0.5 ansible_python_interpreter=/usr/bin/python3
10.0.0.6 ansible_python_interpreter=/usr/bin/python3
10.0.0.7 ansible_python_interpreter=/usr/bin/python3

[elk]
10.1.0.4 ansible_python_interpreter=/usr/bin/python3

- Update the /etc/ansible/ansible.cfg and change remote_user to azadmin (username chosen)
- Run the playbook, SSH into the elk VM, then run docker ps to check that the installation worked as expected.
- Playbook is configure_elk.yml located under /etc/ansible/configure_elk.yml
- Update /etc/ansible/hosts under ELK. You specify in the hosts file.
- Check that the ELK server is running at https://20.55.192.205:5601/app/kibana
