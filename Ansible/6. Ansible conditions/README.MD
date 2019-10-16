# ansible conditions

```
vim conditions.yml

```

```
---
- hosts: webservers
  vars:
    age: 15
  tasks:
    - name: create file using variable
      command: touch /tmp/18.txt
      when: age == 18

    - name: create file using variable
      command: touch /tmp/grater_then_18.txt
      when: age > 18


    - name: create file using variable
      command: touch /tmp/less_then_10.txt
      when: age < 10

    - name: create file using variable
      command: touch /tmp/between_10_to_18.txt
      when: age > 10 and age < 18

    - name: create file using variable
      command: touch /tmp/between_14_or_15.txt
      when: age == 14 and age == 15      

```

- install & Configure nginx with condition module:

```
---
- hosts: aws
  vars:
   - docroot: /var/www/bogotobogo.com/public
  tasks:
   - name: Add Nginx Repository
     apt_repository: repo='ppa:nginx/stable' state=present
     register: ppastable

   - name: Install Nginx
     apt: pkg=nginx state=installed update_cache=true

     when: ppastable|success
     register: nginxinstalled
     notify:
      - Start Nginx

   - name: Create Web Root
     when: nginxinstalled|success
     file: dest=/var/www/bogotobogo.com/public mode=775 state=directory owner=www-data group=www-data
     notify:
      - Reload Nginx

  handlers:
   - name: Start Nginx
     service: name=nginx state=started

   - name: Reload Nginx
     service: name=nginx state=reloaded

```


