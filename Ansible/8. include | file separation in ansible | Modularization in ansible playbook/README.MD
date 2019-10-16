# include | file separation in ansible | Modularization in ansible playbook

- problem to handle large.yml

```
- hosts: all
  vars:
    var1: var1
    var2: var2
    var3: var3
    var4: var4
    var5: var5
    var6: var6
    var7: var7

  tasks:
   - name: task1
     command: touche /tmp/large/{{ var1 }}.txt

   - name: task2
     command: touche /tmp/large/{{ var2 }}.txt

   - name: task3
     command: touche /tmp/large/{{ var3 }}.txt 

   - name: task4
     command: touche /tmp/large/{{ var4 }}.txt

    - name: task5
     command: touche /tmp/large/{{ var5 }}.txt

   - name: task6
     command: touche /tmp/large/{{ var6 }}.txt

   - name: task7
     command: touche /tmp/large/{{ var7 }}.txt     

```

- To solve this large file handle create one file to add `vars:`

```
vim  variable.yml

var1: var1
var2: var2
var3: var3
var4: var4
var5: var5
var6: var6
var7: var7

```
- create task.yml file

```
- name: task1
     command: touche /tmp/large/abc/{{ var1 }}.txt

   - name: task2
     command: touche /tmp/large/abc/{{ var2 }}.txt

   - name: task3
     command: touche /tmp/large/abc/{{ var3 }}.txt 

   - name: task4
     command: touche /tmp/large/abc/{{ var4 }}.txt

    - name: task5
     command: touche /tmp/large/abc/{{ var5 }}.txt

   - name: task6
     command: touche /tmp/large/abc/{{ var6 }}.txt

   - name: task7
     command: touche /tmp/large/abc/{{ var7 }}.txt     

```


- Now create smart YML file.

```
vim short.yml

- hosts: all
  vars_files:
    - variable.yml
  tasks:
    - include: task.yml


      
