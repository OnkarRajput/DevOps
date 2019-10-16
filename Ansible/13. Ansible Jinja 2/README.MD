
# Jinj2 templating

Jinja2 is a modern and designer-friendly templating language for Python frameworks. It is fast, reliable and widely used for dynamic file generation based on its parameter.

# How it works
The Jinja variables and expressions indicated using the default delimiters as follows:

* {% … %} for control statements (conditions)

* {{ … }} for expressions (variables)

* {# … #} for comments (describe the task)



```

- 
  name: this is fist play
  host : webserver
  vars: 
    your_name: ajay
    dummy_list:
       - 1
       - 10
       - 20
       - 30
       - 10

    dummy_list:
       - 30
       - 40
       - 10
       - 50
  tasks:      
    -  debug:
         msg: "hello {{ your name }}"


    -  debug:
         msg: "hello {{ your name  | upper }}"

    -  debug:
         msg: "hello {{ your name  | lower }}"         

    -  debug:
         msg: "hello {{ your name  | replace('Ajay', 'Vijay') }}"

    -  debug:
         msg: "hello {{ dummy_list  | min }}"         

    -  debug:
         msg: "hello {{ dummy_list  | max }}"          

    -  debug:
         msg: "hello {{ dummy_list  | unique }}"    

    -  debug:
         msg: "hello {{ dummy_list  | unique(dummy_list2) }}"

    -  debug:
         msg: "hello {{ dummy_list  | intersect(dummy_list2) }}"                
    -  debug:
         msg: "{{ /etc/ansible/ansible.cfg | basename }}"             

```

https://hub.packtpub.com/advanced-playbooks/
