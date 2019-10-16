# ansible lookups

# What is Ansible lookup?

Lookup plugins allow Ansible to access data from outside sources. ... Lookups are an Ansible-specific extension to the Jinja2 templating language. Note. Lookups are executed with a working directory relative to the role or play, as opposed to local tasks, which are executed relative the executed script.

# What is lookup in Ansible playbook?

Lookups. Lookup plugins allow access to outside data sources. Like all templating, these plugins are evaluated on the Ansible control machine, and can include reading the filesystem as well as contacting external datastores and services. ... Lookups are an advanced feature.

# features
* we can put query from external data source like :
we have to load or get remote hosts passowrd while run inventory file .`webserver ansible_host=192.168.0.3
ansible_ssh_pass` password will load with `.csv` file or `.ini` file without mention in inventory file.


* .csv format

```
cat credentials.csv

output :

Hostname,Password
webserver1,password
sqlserver1,password

```
* Write a playbook to load .csv file.

```
vim lookups.yml

- 
  name: Test Connectivity
  hosts: webservers1
  vars: 
    ansible_ssh_pass: "{{ lookup('csvfile', 'webserver1 file=credentials.csv delimiter=, ') }}"
  tasks:
     - name: create a dummy file on webserver
     command: touch /tmp/abc.txt

```



* .ini format

```
cat credentials.ini

output :


[webserver1]
password=password

[sqlserver1]
password=password

```
* Write a playbook to load .ini file.

```
vim lookups.yml

- 
  name: Test Connectivity
  hosts: webservers1
  vars: 
    ansible_ssh_pass: "{{ lookup('ini', 'password section=webserver1 file=credentials.ini') }}"
  tasks:
     - name: create a dummy file on webserver
     command: touch /tmp/abc.txt

```

Example of Jinja template

```

3
4
5
6
7
8
9
10
11
12
13
14
15
16
  location ~ \.php$ {
    try_files $uri =404;
    root           /var/www/htdocs/;
    fastcgi_pass   unix:/var/run/php65-php-fpm.sock;
    fastcgi_intercept_errors        on;
    fastcgi_buffers 256 16k;
    fastcgi_max_temp_file_size 0;
    fastcgi_index  index.php;
    fastcgi_param  SCRIPT_FILENAME $document_root$fastcgi_script_name;
    include        fastcgi_params;
 
    {% if env is defined and env=="testos" %}
    auth_basic           "Ninja Test ";
    auth_basic_user_file {{ nginx_basic_auth }} ;
    {% endif %}
  }

  ```

then within our playbook we define it as following

```
---
- name: Testing
  hosts: localhost
  vars:
    - blabla: "Testing123"
    - testing: "{{ lookup('template', 'template.j2') }}"
 
  tasks:
  - debug: msg="{{testing}}"

```


for more info 
`
https://hub.packtpub.com/depth-look-ansible-plugins/`