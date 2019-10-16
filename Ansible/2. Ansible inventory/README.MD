# Ansible inventory

- ansible inventry file conatiner hostname, user name, ssh password and ssh port and group host names.

- inventry file default localtion is /etc/ansible/hosts

- invenry file having `.yml` and `.ini` format.


1. use ssh `password utility` to ping remote hosts. So need to install 

note : Ansible provisioning ERROR! Using a SSH password instead of a key is not possible

    sudo vim /etc/ansible/ansible.cfg 

    [defaults]        host_key_checking = false

   `sudo apt install sshpass -y`

  - add hostne node with pasword option. Bydefault `root user` will enable for ansible task.
  
  
        vim /etc/ansible/hosts`

        172.31.12.85 ansible_ssh_pass=password

        172.31.15.105 ansible_ssh_pass=password


  2. create your custome inventory file. like : inventory.txt
        
        vim inventory.txt


        workernode1.example.local ansible_ssh_host=172.31.12.85  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22


        workernode2.example.local ansible_ssh_host=172.31.15.105  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22


     `Now test custome inventory.txt file`

          ansible workernode2.example.local -m ping -i inventory.txt 

         output:

        workernode2.example.local | SUCCESS => {
           "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python3"
        }, 
               "changed": false, 
               "ping": "pong"
        }
        
         ansible workernode1.example.local -m ping -i inventory.txt 
        
        output:

        workernode1.example.local | SUCCESS => {
          "ansible_facts": {
             "discovered_interpreter_python": "/usr/bin/python3"
          }, 
               "changed": false, 
               "ping": "pong"
         }

       - ping all node   
       
        ansible all -m ping -i inventory.txt   
        
        ansible worker* -m ping -i inventory.txt
      

  3. create group base inventory file.

         vim inventory_groups.txt    

          workernode1.example.local ansible_ssh_host=172.31.12.85  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22
          
          workernode3.example.local ansible_ssh_host=172.31.12.86  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22

          workernode4.example.local ansible_ssh_host=172.31.12.87  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22

         workernode2.example.local ansible_ssh_host=172.31.15.105  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22

         workernode5.example.local ansible_ssh_host=172.31.15.106  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22

         workernode6.example.local ansible_ssh_host=172.31.15.107  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22

         workernode7.example.local ansible_ssh_host=172.31.15.108  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22                

         [web_servers]
         workernode1.example.local
         workernode3.example.local
         workernode4.example.local
         

         [database_servers]
         workernode2.example.local
         workernode5.example.local
         workernode6.example.local
         workernode7.example.local


  `` ansible web_servers -m ping -i inventory_groups.txt ``

  `` ansible database_servers -m ping -i inventory_groups.txt ``       


3. create inventory group of group.

       vim inventory_group_of_group.txt


       workernode1.example.local ansible_ssh_host=172.31.12.85  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22
          
       workernode3.example.local ansible_ssh_host=172.31.12.86  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22

       workernode4.example.local ansible_ssh_host=172.31.12.87  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22

       workernode2.example.local ansible_ssh_host=172.31.15.105  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22

       workernode5.example.local ansible_ssh_host=172.31.15.106  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22

       workernode6.example.local ansible_ssh_host=172.31.15.107  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22

       workernode7.example.local ansible_ssh_host=172.31.15.108  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22   


       workernode8.example.local ansible_ssh_host=172.31.15.109  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22
       workernode9.example.local ansible_ssh_host=172.31.15.110  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22
       workernode10.example.local ansible_ssh_host=172.31.15.111  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22              
       workernode11.example.local ansible_ssh_host=172.31.15.112  ansible_ssh_user=root  ansible_ssh_pass=password ansible_ssh_port=22 

       [web_servers]
       workernode1.example.local
       workernode3.example.local
       workernode4.example.local
         

       [database_servers]
       workernode2.example.local
       workernode5.example.local
       workernode6.example.local
       workernode7.example.local

       [nfs_servers]
        workernode[8:12]                              ::[8:12] workernode8.example.local ... to workernode11.example.local


       [database_nfs_servers:children]               :: join two group use :children option
        database_servers
        nfs_servers

        

       ansible database_nfs_servers -m ping -i inventory_group_of_group.txt 