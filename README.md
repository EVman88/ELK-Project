## Automated ELK Stack Deployment

The files in this repository were used to configure the network depicted below.

Azure Week 13 Diagram ([ELK-Project/Diagram Folder/ Azure Week 13 Diagram.png](https://github.com/EVman88/ELK-Project/blob/main/Diagram%20Folder/Azure%20Week%2013%20Diagram.png)

These files have been tested and used to generate a live ELK deployment on Azure. They can be used to either recreate the entire deployment pictured above. Alternatively, select portions of the playbook file may be used to install only certain pieces of it, such as Filebeat.

 Playbook 1: [configure_webserver.yml](https://github.com/EVman88/ELK-Project/blob/main/Ansible/configure_webserver.yml)
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
-Load balancers provide security to applications during peak times as well as providing seamless access.
-The advantage of the jump box is that it allows an adminitrator to prevent attacks by preventing malicious remote connections.

Integrating an ELK server allows users to easily monitor the vulnerable VMs for changes to the configuration and system files.
-Filebeat is used to monitor logs. 
-Metricbeat is used to gather metrics and statistics from servers running on the server and from the operating system.

The configuration details of each machine may be found below. Filebeat is used to monitor logs. Metricbeat is used to gather metrics and statistics from servers running on the server and from the operating system.


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
-74.99.162.165

Machines within the network can only be accessed by the Jump Box.
-The Jump Box (IP address 10.0.0.4) is allowed to acces the ELK VM through port 22.

A summary of the access policies in place can be found in the table below.

| Name     | Publicly Accessible | Allowed IP Addresses |
|----------|---------------------|----------------------|
| Jump Box | Yes (SSH)             | 10.0.0.1 10.0.0.2    |
| Web-1          | Yes (HTTP)                    |                      |
| Web-2         | Yes (HTTP)                    |                      |
| Web-3

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
