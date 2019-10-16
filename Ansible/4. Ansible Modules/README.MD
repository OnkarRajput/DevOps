# ansible modules

https://www.toptechskills.com/ansible-tutorials-courses/


- cp, apt, handlers, lineinfile, 

# cp and apt, service modules
``` 
vim nginx.yml

---
- hosts: webservers
  tasks:
    - name: Installs nginx web server
      apt: pkg=nginx state=installed update_cache=true


    - name: Upload default index.php for host
      copy: src=static_files/index.php dest=/usr/share/nginx/www/ owner=ubuntu group=www-data mode=0644

  handlers:
    - name: start nginx
      service: name=nginx state=started

```

1. Add a Default Index File: 

We can tell it to transfer a file from our Ansible server onto the host by adding some lines like this:

```
- name: Upload default index.html for host
      copy: src=static_files/index.html dest=/usr/share/nginx/www/ mode=0644

```      
       
We can then make a directory called static_files in our current directory and place an index.html file inside.

```
mkdir static_files

vim static_files/index.html
````

Inside of this file, let’s just create a basic html structure:

```
<html>
  <head>
    <title>This is a sample page</title>
  </head>
  <body>
    <h1>Here is a heading!</h1>
    <p>Here is a regular paragraph.  Wow!</p>
  </body>
</html>

```
Run nginx.yml in webserver host only with flag `-l`

```
ansible-playbook -l webservers nginx.yml -i inventroy.txt 
```

# lineinfile module – Manage lines in text files

Ansible lineinfile module can be used to insert a line, modify an existing line, remove an existing line or to replace a line.

##  Ansible lineinfile examples – Add, Modify, Delete, Replace lines

https://www.toptechskills.com/ansible-tutorials-courses/

Ansible lineinfile module is helpful when you want to add, remove, modify a single line in a file.  You can also use conditions to match the line before modifying or removing using the regular expressions. You can reuse and modify the matched line using the back reference parameter.

- Consider yourself having any of these following requirement:

1. You want to add a line when it is not already present.

2. You want to change the port number in the configuration 

3. You want to disable the SSL when the SSL is enabled

4. You want to add a new entry in the /etc/hosts file

5. You want to upgrade the package version when the installed version is matching your regular expression

6. You want to remove a username from /etc/passwd file using regex

`https://www.middlewareinventory.com/blog/ansible-lineinfile-examples/`

- create hosts file in remote and manually entry like: `172.31.12.85 foo.lab.net foo`

```

vim lineinfile.yml


---
  - name: Examples of lineinfile
    hosts: webservers
    
    tasks:
      - name: "Example2: Add the line if it does not exist"
        become: yes
        become_user: root
        tags: example2
        lineinfile:
          path: /tmp/hosts
          line: "172.31.12.85 foo.lab.net foo"
          create: yes
          backup: yes
        register: example2out      

```

# Script module

```
- name: Execute the command in remote shell; stdout goes to the specified file on the remote.
  shell: somescript.sh >> somelog.txt

- name: Change the working directory to somedir/ before executing the command.
  shell: somescript.sh >> somelog.txt
  args:
    chdir: somedir/

```
You can also use the 'args' form to provide the options.

```

- name: This command will change the working directory to somedir/ and will only run when somedir/somelog.txt doesn't exist.
  shell: somescript.sh >> somelog.txt
  args:
    chdir: somedir/
    creates: somelog.txt
```
# user module

create password for user encrypt with below 

```
mkpasswd --method=sha-512

````
or

```
sudo apt install python-pip

sudo pip install passlib

sudo python -c "from passlib.hash import sha512_crypt; print (sha512_crypt.encrypt('myPlainTextPassword'))"

```
Disable host key checking. Open the /etc/ansible/ansible.cfg configuration file

```
#host_key_checking = False

```

```

```






```

- create user with sudo acces

```

```
---
- hosts: all
  become: true

  tasks:
  - name: create user
    user:
      name: test22
      password: $6$saltsalt$qFmFH.bQmmtXzyBY0s9v7Oicd2z4XSIecDzlB5KiA2/jctKu9YterLp8wwnSq.qc.eoxqOmSuNp2xS0ktL3nh/
      groups: sudo # Empty by default, here we give it some groups
      state: present
      shell: /bin/bash       # Defaults to /bin/bash
      system: no             # Defaults to no
      createhome: yes        # Defaults to yes
      home: /home/test

```

# Register module

Register variable show you output of palybook.

```
vim register.yml

---
- host: webservers
  
  task:
  - name: system uptime
    command: uptime
    register: uptime

  - debug: var=uptime.stout

```

run multipal stages

```
- host: webservers
  
  task:
  - name: system uptime
    command: uptime
    register: uptime

  - name: system host
    command: hostname
    register: host 

  - debug: var={{ item
  }}
    loop:
      - uptime.stdout
      - host.stdout

```       



