# Strategies in ansible

* linear

* free

- linear strategies will wait for the task complete  with another host's task to complete,  then procced to do next task with them. this `linear` by default by ansible.

```
vim playbook.yml

---
- hosts: webservers, sqlservers
strategy: linear
  tasks:
    - name: first task 
      apt: name= 'apache2' state='present'

    - name: second tasl 
      apt: name= 'nginx' state='present'

```

- free strategies will not wait for another host to complete the first step to complete. it will independent to each others.


```
vim playbook.yml

---
- hosts: webservers, sqlservers
strategy: free
  tasks:
    - name: first task 
      apt: name= 'apache2' state='present'

    - name: second tasl 
      apt: name= 'nginx' state='present'
```      
* Forks:

Forks are the default number of parallel processes to spawn when communicating with remote hosts. If you are orchestrating your playbook across many servers, then you may have noticed that the number of servers where the playbook executes simultaneously is 5. You can increase this number inside the ansible.cfg file:

By default ansible take first 1-5 machines and second 6-10 machines and so on ... 

```
# ansible.cfg 
forks = 10

```

- Assign  `serial` number to host server to run all job setps  first without go with another host.

```
vim playbook.yml

---
- hosts: webservers, sqlservers, nfsserver, phpserver..... so on
serial: 1
  tasks:
    - name: first task 
      apt: name= 'apache2' state='present'

    - name: second task 
      apt: name= 'nginx' state='present'
``` 
:: above two task will first in webserver. After mysqlserver andter nfs server....


