# ansible playbook



# Ansible Playbook Basics

1. Hosts and Users

     Ansible needs target machines in the infrastructure on which plays must be deployed from Ansible playbook. Hosts are added to Ansible inventory through their IP addresses. Hosts are a list of one or more groups or host patterns separated by a colon. The remote_user contains the name of the user account.

```
---
- hosts: ProdServers
  remote_user: geekflare

```
2. Variables

   Variables enable you to alter how a playbook runs.  They can be used nearly everywhere in the playbook and can be inherited from an inventory, explicitly set at runtime, discovered at the start of a playbook run. You can define a variable name using letters, numbers, and underscores, but it must start only with a letter.

   For example, port_01 is a valid variable, whereas 01_post is an invalid variable. Here is an example of variables in an Ansible Playbook:

```
vars:
    http_port: 80
    max_clients: 200

````

3. Inventories

   To run a playbook, you need a list of targets on which to you want automation to happen. This is what an inventory does. Inventory lists can be built and stored in several different ways, including static files, i.e., Ansible Hosts. Or it can be dynamically generated via an inventory script that will pull a list of hosts for an external source.

   You can also specify a variable as part of an inventory list. Inventories are ultimately a list of things you want to automate across.

```
[webservers]
192.168.20.1
192.168.20.2
192.168.20.4
[dbservers]
172.17.1.56
172.17.1.57

```

4. Tasks

   Plays in ansible playbook run tasks. The task is responsible for running ansible modules. At a time, only one task can run, and tasks get executed in sequential order. Their tasks are written in YAML, which is pretty much English like language.

   For examples: install package_name, Update software_name, etc. Below is an example of a task in Ansible playbook to install httpd:

```
tasks:
  - name: Install httpd Package
    yum: name=httpd state=latest

````

5. Handlers

   Handlers are a special kind of tasks.

   They can be triggered by a task and are run once at the end of the play. It is used to send notifications if there is any change in the configuration file, for example, notify service to start apache once it gets installed. “notify” syntax is used to call handlers. Below is an example of a handler to restart apache:

```
---
- hosts: all
  tasks:
  - name: ensure apache is at the latest version
    yum: name=httpd state=latest
    notify:
    - restart apache
  - name: ensure apache is running (and enable it at boot)
    service: name=httpd state=started enabled=yes
  handlers:
    - name: restart apache
      service: name=httpd state=restarted

```


  - Create and Run Your First Ansible Playbook

    Let me tell you how to write a playbook. Every playbook starts with three dashes (---) on the top.
    
    The first thing you mention in an Ansible Playbook is the host machines on which you want to run the playbook.

   Then you can mention variables by gathering facts; then you can mention the different tasks that you want to perform. Now, remember that the task gets executed in the same order that you write them. For example, you want to install software A first and then software B, make sure that the first task written in the playbook would be to install software A and the next task could install software B.

   Then you have got handlers at the bottom. The handlers are also tasks, but the difference is to execute handlers you need some sort of triggers in the list of tasks to run handlers.

   Let me show you how to create an ansible playbook to install and start nginx on a host and start it.

   Create a .yml file where you will put your YAML codes for creating an ansible playbook.

```
 vim nginx.yml

```

Put the below YAML code in this file and save the file.

```
---
- hosts: Client
  sudo: yes
  vars:
    - server_port: 8080

  tasks:
    - name: Installs nginx web server
      apt: pkg=nginx state=installed update_cache=true
      notify:
        - start nginx

  handlers:
    - name: start nginx
      service: name=nginx state=started
```


  The above YAML file starts with hosts, I want to run this playbook on the client machine (Client). The client IP address is already saved in /etc/ansible/hosts file.

  The next line allows running the tasks with sudo privileges.

  Then I have defined server_port as a variable in this playbook.

  Then comes my first task in this ansible playbook to install an Nginx web server. This task also has notify parameter, which means after this task a handler with run.

  Finally, I have written a handler to start nginx on the client machine.

  Now that you are clear with the YAML codes in the ansible playbook let’s run the playbook. Below is the syntax to run an ansible playbook:

```
ansible-playbook file_name.yml

```
Now, I will run the ansible playbook, which I create to install and start nginx.

```

ansible-playbook nginx.yml

PLAY [Client] ******************************************************************

TASK [Gathering Facts] *********************************************************
ok: [node1]

TASK [Installs nginx web server] ***********************************************

changed: [node1]

RUNNING HANDLER [start nginx] **************************************************
ok: [node1]

PLAY RECAP *********************************************************************
node1                      : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
Run the command below to check if the nginx server was installed and started correctly by the ansible playbook.

```

```

ps waux | grep nginx
root      3021  0.0  0.0  77676  1516 ?        Ss   15:27   0:00 nginx: master process /usr/sbin/nginx -g daemon on; master_process on;

```







```
vim inventroy.txt

workernode1.example.local ansible_ssh_host=172.31.12.85  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22          
workernode2.example.local ansible_ssh_host=172.31.15.105  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22


[web_servers]
workernode1.example.local

[database_servers]
workernode2.example.local

[database_web_servers]              
workernode2.example.local
workernode1.example.local

        
        
```
vim playbook.yml

---
- hosts: web_servers
  tasks:

    - name: create dummy file on web_servers
      command: touch test_file
     
    - name: copy hosts file in tmp folder
      command: cp /etc/hosts /tmp/mthosts
 
- hosts: database_servers
  tasks:

    - name: create directory in tmp folder
      command: mkdir /tmp/second_test_file



          