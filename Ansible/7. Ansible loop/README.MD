# ansible loop
 if you want to install multiple packages, you need to create the section for each package. This could increase the length of the playbook and have to create a new block of code for the same action but different packages. In such cases, “loop” function will help to repeat the action for a list of items.

- install pakages

```
vim install_packages1.yaml

---

- hosts: all
  become: yes

  tasks:
   - name: Install Packages
     apt: name={{ item }} update_cache=yes state=latest
     with_items:
       - vim
       - lsof
       - nano

```

```
ansible-playbook  install_packages1.yaml

```

```
---
- hosts: all
  vars:
    pkg:
     - vsftpd
     - tree

  tasks:
   - name: Install Packages
     apt: name="{{item}}"  state=present
     with_items: "{{pkg}}"
  
```


