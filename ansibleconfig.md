### Ansible Configuration Management - Automate Project 7 to 10

This project will make use of Ansible Configuration Management to automate routine server configuration tasks. 
The project will focus on developing Ansible scripts to simulate the use of a Jump box/Bastion host to access the Web Servers.

#### Task:

- Install and configure Ansible client to act as a Jump Server/Bastion Host
- Create a simple Ansible playbook to automate servers configuration

#### Step 1 - Install and configure Ansible on EC2 Instance:

##### 1. Update Name tag on the Jenkins EC2 Instance to Jenkins-Ansible. This server will be used to run the playbooks.

##### 2. In the GitHub account, create a new repository and name it ansible-config-mgt.

##### 3. Install Ansible

```
sudo apt update

sudo apt install ansible

ansible --version
```
#### Step 2 - Prepare the development environment using Visual Studio Code

Next step is to install a versatile Integrated development environment (IDE) or Source-code Editor. The IDE used in this project is Visual Studio Code (VSC). Configure it and connect it to the GitHub respository created for this project (https://code.visualstudio.com/docs/editor/github).

#### Step 3 - Begin Ansible Development

In the ansible-config-mgt GitHub repository, create a new branch that will be used for development of a new feature - ansible-newfeature. 
1. Checkout the newly created feature branch to your local machine and start building your code and directory structure
2. 2. Create a directory and name it playbooks - it will be used to store all your playbook files. Also, create a directory and name it inventory - it will be used to keep your hosts organised.
3. Within the playbooks folder, create your first playbook, and name it common.yml.
```
cd playbooks
touch common.yml
```
4.  Within the inventory folder, create an inventory file (.yml) for each environment (Development, Staging Testing and Production) dev, staging, uat, and prod respectively.
```
cd inventory
touch dev.yml staging.yml uat.yml prod.yml
```

#### Step 4 - Set up an Ansible Inventory

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. The intention is to execute Linux commands on remote hosts and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

Save below inventory structure in the inventory/dev file to start configuring the development servers. Ensure to replace the IP addresses with the actual IP addresses of the setup.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host - for this copy the private (.pem) key to the server. Change permissions to the private key with chmod 400 key.pem, otherwise EC2 will not accept the key. Import the key into ssh-agent:

```
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```

##### Blocker: It was a huge learning experience to figure out how to transfer files from local machine to EC2 instance and also to set up the permissions and key pair transfer to the Ansible master in order to be able to connect with the host servers. I saved a copy of the pem file in the ansible-config-mgt folder and added the path to the inventory file. 

#### Step 4 - Set up an Ansible Inventory

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. The intention is to execute Linux commands on remote hosts and ensure that it is the intended configuration on a particular server that occurs. It is important to have a way to organize our hosts in such an Inventory.

Save below inventory structure in the inventory/dev file to start configuring the development servers. Ensure to replace the IP addresses with the actual IP addresses of the setup.

Note: Ansible uses TCP port 22 by default, which means it needs to ssh into target servers from Jenkins-Ansible host - for this copy the private (.pem) key to the server. Change permissions to the private key with chmod 400 key.pem, otherwise EC2 will not accept the key. Import the key into ssh-agent:

```
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```

##### Blocker: It was a huge learning experience to figure out how to transfer files from local machine to EC2 instance and also to set up the permissions and key pair transfer to the Ansible master in order to be able to connect with the host servers. I saved a copy of the pem file in the ansible-config-mgt folder and added the path to the inventory file. 

#### Step 5 - Create a Common Playbook

The common.yml playbook will contain configuration code for repeatable, re-usable, and multi-machine tasks that is common to systems within the infrastructure.

Update the playbooks/common.yml file with following code:

```yml
---
- name: update web, nfs servers
  hosts: webservers, nfs
  remote_user: ec2-user
  become: yes
  become_user: root
  tasks:
  - name: ensure wireshark is at the latest version
    yum:
      name: wireshark
      state: latest

- name: update LB and db server
  hosts: lb, db
  remote_user: ubuntu
  become: yes
  become_user: root
  tasks:
  - name: ensure wireshark is at the latest version
    apt:
      name: wireshark
      state: latest
  ```
  
  This playbook is divided into two parts, each of them is intended to perform the same task: install wireshark utility (or make sure it is updated to the latest version) on the RHEL 8 and Ubuntu servers. It uses root user to perform this task and respective package manager: yum for RHEL 8 and apt for Ubuntu.

#### Step 6 - Update GIT with the latest code

Now all of the directories and files live on the machine and the changes need to be pushed to GitHub.

The changes have been made in a separate branch, so a pull request needs to be raised, the branch needs to be peer reviewed and merged to the master branch.

Commit the code into GitHub:
```
git status

git add <selected files>

git commit -m "commit message"
```

##### Integrating the code into the Master branch after reviewing it

1. Create a Pull request (PR).
2. Review the code and configuration from a different perspective.
3. If everything looks okay, merge the code to the master branch.
4. Head back on the terminal, checkout from the feature branch into the master, and pull down the latest changes.

Run the playbook to execute the commands on the host servers:

```
ansible-playbook -i inventory/dev.yml playbooks/common.yml
```
