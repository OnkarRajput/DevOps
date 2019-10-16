# Ansible error handling

If you want to ingnor error in playbook use `ignore_errors: yes` option.


```
- name: Install apache2 web server
  become: true
  apt: pkg={{ item }} state=installed update_cache=true
  with_items:
  - apache2
  - libapache2-mod-fastcgi

- name: Enable apache2 modules
  become: yes
  command: a2enmod rewrite actions alias fastcgi proxy_fcgi

- name: Upload apache2 default configuration
  become: yes
  copy: src=files/000-default.conf dest=/etc/apache2/sites-enabled/

- name: Remove default apache2 index.html file
  become: yes
  ignore_errors: yes
  command: rm /var/www/html/index.html 

```
