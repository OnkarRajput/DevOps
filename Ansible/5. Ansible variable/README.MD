# ansible variable

- create user without sudo access 

```
- hosts: all
  become: true

  tasks:
  - name: create user
    user:
      name: rajat12
      password: $6$saltsalt$qFmFH.bQmmtXzyBY0s9v7Oicd2z4XSIecDzlB5KiA2/jctKu9YterLp8wwnSq.qc.eoxqOmSuNp2xS0ktL3nh/
      shell: /bin/bash

```
or 

```
- hosts: all
  become: true
  vars:
    users:
    - username: "paul1"
    - username: "tanya1"
    - username: "ruby1"

  tasks:
  - name: create user
    user:
      name: "{{ item.username }}"
      password: $6$saltsalt$qFmFH.bQmmtXzyBY0s9v7Oicd2z4XSIecDzlB5KiA2/jctKu9YterLp8wwnSq.qc.eoxqOmSuNp2xS0ktL3nh/
      shell: /bin/bash
    with_items: "{{ users }}"

```

- delete users in ansible

```
- hosts: all
  become: true
  vars:
    users:
    - username: "paul1"
    - username: "tanya1"
    - username: "ruby1"

  tasks:
  - name: ansible delete users 
    user:
      name: "{{ item.username }}"
      state: absent
      remove: yes 
      force: yes
    with_items: "{{ users }}"

```
- set content directory group/permissions

```
- hosts: all
  become: true

  - name: set content directory group/permissions 
    file:
      path: /var/www/html
      owner: root
      group: web
      state: directory
      mode: u=rwx,g=rwx,o=rx,g+s
```


```
- hosts: all
  become: true
  become: yes

  vars:
    user_pass: $6$saltsalt$qFmFH.bQmmtXzyBY0s9v7Oicd2z4XSIecDzlB5KiA2/jctKu9YterLp8wwnSq.qc.eoxqOmSuNp2xS0ktL3nh/
  tasks:
  - include_vars: users.yml

  - name: Add users | create users, shell, home dirs
    user: name={{ item.username }} shell=/bin/bash createhome=yes comment='create with ansible'
  with_items: "{{users}}"


  - name: Sudoers | update sudoers file and validate
    lineinfile: dest=/etc/sudoers
      insertafter=EOF
      line='{{ item.username }} ALL=(ALL) NOPASSWD: ALL'
      regexp='^{{ item.username }} .*'
      state=present"
    when: {{ item.use_sudo }} == True
    with_items: "{{users}}"

```

```
- hosts: all
  become: true
  become: yes

  vars:
    servicename: nginx

  tasks:
  - name: restart nginx
    service: name={{ servicename }} state=started

```


