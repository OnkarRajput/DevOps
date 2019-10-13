## Architecture Overview
- Mater can work as a worker
- Manager assign task to worker
- Master moniter the statement for cluster
- Managers have one leader as a Master
-  Each manager moniter to each others with help of `Raft consensus Algo`
- Master should be in old number ``` number of node -1/2 ```
- manager become as a manager but worker cann't become a worker. Worker want manager to take task from Manager. 
- worker node having installed one agent. Agent have fullfill the task as assigned the task by Manager. Agent update his current state to Master.
- Master mantain the desire state for cluster.
- Managers decided to elect a Master between each others.
- Master and worker works with `TLS` and `Certificate Authority` security.

# Docker Swarm Installation
### How to Install and Configure Docker Swarm on Ubuntu 18.04 

# Docker Swarm Highlights:

- Cluster management integrated with Docker Engine

- Decentralized design

- Declarative service model

- Scaling

- Multi-host networking

- Service discovery

- Load balancing

- Secure by default

- Rolling updates

## Docker is now available in two editions,

- Community Edition (CE)

- Enterprise Edition (EE)

## Here, we will install Docker Comunity Edition (CE).

- Install Docker from Ubuntu Repository

    ``` sudo apt install docker.io ``` 

    ``` sudo systemctl start docker ```

    ``` sudo systemctl enable docker ```

    ``` docker --version ```
- Install Docker from the Official Docker Repository

      Install the Dependencies

      sudo apt update 

      sudo apt install apt-transport-https ca-certificates curl software-properties-common
       
      sudo vim  /etc/apt/sources.list.d/docker.list

      STABLE (NOT YET AVAILABLE!), please check availabilty before using:

      deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic stable

      EDGE:
      deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic edge

      NIGHTLY:
      deb [arch=amd64] https://download.docker.com/linux/ubuntu bionic nightly 


- add Docker's GPG key.

   ``` curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - ```

   ``` sudo apt update ```

- Install Docker CE

   ``` sudo apt install docker-ce ```

   
   ``` docker --version ```

# Firewall
##### You would need to open following ports on the firewall for a swarm cluster to work properly.

    ufw status 
    ufw enable 
    ufw allow 22/tcp
    ufw allow 2376/tcp 
    ufw allow 7946/tcp 
    ufw allow 7946/udp 
    ufw allow 2377/tcp 
    ufw allow 4789/udp 
    ufw allow 80/tcp    :: We are Testing Docker Swarm with WebService
    ufw reload 
    ufw enable 

``` systemctl restart docker ```


Port ==> Protocol ==> Description

2376,2377 ==> TCP ==> used for Docker daemon encrypted communication

7946 ==>	TCP, UDP ==>	used for container network discovery

4789 ==>	UDP	 ==> used for container ingress network

# Create a Swarm Cluster

    sudo vim /etc/hosts

    172.31.11.66     dockermanager.example.local       dockermanager
    172.31.12.85    workernode1.example.local         workernode1 
    172.31.15.10     workernode2.example.local         workernode2
- Set hostname in Docker Manager server

      sudo hostnamectl set-hostname dockermanager.example.local 
- Set hostname in Docker orkernode1

      sudo hostnamectl set-hostname workernode1.example.local 
- Set hostname in Docker orkernode2

      sudo hostnamectl set-hostname workernode2.example.local

## dockermanager
##### HostName
    dockermanager.example.local

##### IP Address
    172.31.11.66
#####  Operating System   	
    ubuntu 18.04
##### Purpose      	
    Acts as a Docker manager who manages Docker engine running on nodes. Docker manager will also take a part of a cluster ie, It will run containers as well.
## dworkernode1
##### HostName
    dworkernode1.example.local

##### IP Address
    172.31.12.85 
#####  Operating System   	
    ubuntu 18.04
##### Purpose      	
    Worker Node 1 (Running Docker engine)
## workernode2
##### HostName
    workernode2.example.local

##### IP Address
    172.31.15.10
#####  Operating System   	
    ubuntu 18.04
##### Purpose      	
    Worker Node 2 (Running Docker engine)


# Create a Swarm manager node
- Run below commands in dockermanager

``` docker swarm init --advertise-addr 172.31.11.66 ```

       Swarm initialized: current node (sf5za4vm1fg9b9xgkrsjoptgc) is now a manager.

    To add a worker to this swarm, run the following command:

        docker swarm join --token SWMTKN-1-25ujd3b60lcrybwpxo9d942iklwkvew9r61929dg9cceh73wmo-chbb0lyb42vu0k87lubh9zcgp 172.31.11.66:2377

    To add a manager to this swarm, run 'docker swarm join-token manager' and follow the instructions.


### View the current status of the swarm using the following command.

``` docker info ```

     Containers: 0
      Running: 0
      Paused: 0
      Stopped: 0
     Images: 0
     Server Version: 18.09.7
     Storage Driver: overlay2
      Backing Filesystem: extfs
      Supports d_type: true
      Native Overlay Diff: true
     Logging Driver: json-file
     Cgroup Driver: cgroupfs
     Plugins:
      Volume: local
      Network: bridge host macvlan null overlay
      Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
     "Swarm: active"
      NodeID: sf5za4vm1fg9b9xgkrsjoptgc
     " Is Manager: true"
      ClusterID: jnveba4j77arzd3mtmsn948ug
      "Managers: 1 "
       " Nodes: 3 "
     Default Address Pool: 10.0.0.0/8  
     SubnetSize: 24
     Orchestration:
     Task History Retention Limit: 5
    Raft:
     Snapshot Interval: 10000
    Number of Old Snapshots to Retain: 0
     Heartbeat Tick: 1
    Election Tick: 10
    Dispatcher:
    Heartbeat Period: 5 seconds
    CA Configuration:
    Expiry Duration: 3 months
     Force Rotate: 0
     Autolock Managers: false
    Root Rotation In Progress: false
    Node Address: 172.31.11.66
    Manager Addresses:
     172.31.11.66:2377
    Runtimes: runc
    Default Runtime: runc
     Init Binary: docker-init
     containerd version: 
     runc version: N/A
     init version: v0.18.0 (expected: fec3683b971d9c3ef73f284f176672c44b448662)
     Security Options:
      apparmor
     seccomp
      Profile: default
    Kernel Version: 4.15.0-1044-aws
     Operating System: Ubuntu 18.04.3 LTS
      OSType: linux
     Architecture: x86_64
      CPUs: 1
      Total Memory: 983.9MiB
    "Name: dockermanager.example.local "
     ID: IFVE:PIPJ:2DJZ:W4Q3:ZBK3:Y6A4:CTMY:3EWJ:J2TU:5QTJ:VQG7:QTM2
      Docker Root Dir: /var/lib/docker
       Debug Mode (client): false
       Debug Mode (server): false
      Registry: https://index.docker.io/v1/
      Labels:
         Experimental: false
       Insecure Registries:
       127.0.0.0/8
      Live Restore Enabled: false

#### list down the Docker nodes in the swarm with the following command.

``` docker node ls ```

    ID                            HOSTNAME                      STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
    sf5za4vm1fg9b9xgkrsjoptgc *   dockermanager.example.local   Ready               Active              Leader              18.09.7
    
    thy6pw2cl1zhab0l2gjfksj35   
      workernode1.example.local     Ready               Active                                  18.09.7
    
    y3tnuvnhj3lesj91bmsfg9688     workernode2.example.local     Ready               Active                                  18.09.7

# Adding Nodes to a Swarm Cluster

## workernode1

``` sudo docker swarm join --token SWMTKN-1-25ujd3b60lcrybwpxo9d942iklwkvew9r61929dg9cceh73wmo-chbb0lyb42vu0k87lubh9zcgp 172.31.11.66:2377 ```

## workernode2

``` sudo docker swarm join --token SWMTKN-1-25ujd3b60lcrybwpxo9d942iklwkvew9r61929dg9cceh73wmo-chbb0lyb42vu0k87lubh9zcgp 172.31.11.66:2377 ```


# Troubleshooting Tip
## Retrieve Token
If you had lost the token of any swarm, run the following command on the manager node to retrieve it.

``` docker swarm join-token worker ```
   
     To add a worker to this swarm, run the following command:

    docker swarm join --token SWMTKN-1-25ujd3b60lcrybwpxo9d942iklwkvew9r61929dg9cceh73wmo-chbb0lyb42vu0k87lubh9zcgp 172.31.11.66:2377


## Certificate Error:
If you get a certificate error, ensure all the nodes are in time sync.

Error response from daemon: x509: certificate has expired or is not yet valid

# Add and remove node form the docker swarm
- To add a worker to this swarm, run the following command:

      docker swarm join-token worker

- To add a manager to this swarm, run the following command:

      docker swarm join-token manager  

- remove node `workernode2` from `Swarm Manager` 
   
   - run below commands in  `workernode2`  
      
         docker swarm leave
         docker info | less    :: show "Swarm: inactive" 

   - run below commands in  `dockermanager` 

         docker node  rm workernode2.example.local 

# Swarm Node promote and Demote
    docker node inspect workernode1.example.local | less 
    docker node inspect workernode2.example.local | less 

- promote workernode1 & workernode2 to as Managers because `Manager will works in 1-3-5-7 old numbers`

      docker node promote workernode1.example.local workernode2 .example.local 

      docker node ls

      output :

      ID                            HOSTNAME                      STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
       sf5za4vm1fg9b9xgkrsjoptgc *   dockermanager.example.local   Ready               Active              Leader              18.09.7

      thy6pw2cl1zhab0l2gjfksj35     workernode1.example.local     Ready               Active              Reachable           18.09.7
      
      othbejouc8kynecx3nu2mq0r4     workernode2.example.local     Ready               Active              Reachable           18.09.7


- workernode1 server side :

      sudo docker node ls
      
      output:

      ID                            HOSTNAME                      STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
      sf5za4vm1fg9b9xgkrsjoptgc     dockermanager.example.local   Ready               Active              Leader              18.09.7
      
      thy6pw2cl1zhab0l2gjfksj35 *   workernode1.example.local     Ready               Active              Reachable           18.09.7
      
      othbejouc8kynecx3nu2mq0r4     workernode2.example.local     Ready               Active              Reachable           18.09.7

- workernode2 server side :


      sudo docker node ls
      
      output:

      ID                            HOSTNAME                      STATUS              AVAILABILITY        MANAGER STATUS      ENGINE VERSION
      sf5za4vm1fg9b9xgkrsjoptgc     dockermanager.example.local   Ready               Active              Leader              18.09.7
      
      thy6pw2cl1zhab0l2gjfksj35 *   workernode1.example.local     Ready               Active              Reachable           18.09.7
      
      othbejouc8kynecx3nu2mq0r4     workernode2.example.local     Ready               Active              Reachable           18.09.7

- demote workernode1 & workernode2 to as Managers 

      docker node demote workernode1.example.local workernode2.example.local 
      
      docker node inspect workernode1.example.local | less 
      docker node inspect workernode2.example.local | less


# Docker Swarm - Docker Service(create,ls,logs)  
- service command will run only master node not for worker nodes

- create new service `alpine` ping to master's IP `172.31.11.66` 
     
      docker service create -d alpine ping 172.31.11.66

- `docker service ls` will list the created services

      docker service ls 

      output:

      ID                  NAME                MODE                REPLICAS            IMAGE               PORTS
      a1ebmbn8vywm        eager_galois        replicated          1/1                 alpine:latest

- Inspect service
   
      docker service inspect  ajj6pocztrmo | less

- logs service

      docker service logs ajj6pocztrmo

### `workernode1` too see status of assign alpine 
  
      watch docker container ls

### `workernode2`  too see status of assign alpine    

      watch docker container ls 

# Docker Swarm -Docker Service (create replica)

- create 4 replicas for `alpine`
   
      docker service create -d --replicas 4 alpine ping 172.31.11.66

      docker services ls

- service ps `docker service ps <ID>`
    
      docker service ps ajj6pocztrmo

- service ls `docker service ls` after delete container form nodes workers. Replicas should ne mentain 4 after delete containers.
         
      docker container rm -f ajj6pocztrmo

      docker service ls


#  Docker Swarm -Docker Service scale, port mapping

- scale service for need  `docker service scale <service_ID=numbers>`

      docker service scale ajj6pocztrmo=10 pqj6pocztrmo=5

      or 

      docker service scale ajj6pocztrmo=10 

      output:

      ajj6pocztrmo scaled to 10
      overall progress: 10 out of 10 tasks 
      1/10: running   [==================================================>] 
      2/10: running   [==================================================>] 
      3/10: running   [==================================================>] 
      4/10: running   [==================================================>] 
      5/10: running   [==================================================>] 
      6/10: running   [==================================================>] 
      7/10: running   [==================================================>] 
      8/10: running   [==================================================>] 
      9/10: running   [==================================================>] 
      10/10: running   [==================================================>] 
         verify: Service converged 

        
       docker service scale pqj6pocztrmo=5  

       docker service ls

- remove scale service as created: `docker service rm <service_ID>`

      docker service rm ajj6pocztrmo

- service port mapping

      docker service create -d -p 80:80 --name webserver --replicas 5 nginx 

      docker rm a9c3d2172670 

      docker container ls

      docker service ps webserver

    - As per above output we can see containers are deployed across the cluster nodes including manager node. Now we can access web page from any of worker node and Docker Manager using the following URLs :

           http://172.31.11.66  or

           http://172.31.12.85 or

           http://172.31.15.105


          docker service scale webserver=7

          docker service scale webserver=4

          docker service ls 

- High Availability or Container Self-Healing
   - Container self-healing feature in the swarm mode provides high availability for your service. If anything goes wrong with a container or a worker node, the manager will make sure atleast 4 containers must be running for service “webserver“.
   - service docker stop in `workernode2`
      
         service docker stop 

         docker service ps webserver

         


# Docker Swarm -Docker Service Mode (Replicated, global)


### docker-swarm-visualizer
    docker service create --name=viz --publish=8080:8080/tcp --constraint=node.role==manager --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock  dockersamples/visualizer
 
    docker service ls

    output:

    ID                  NAME                MODE                REPLICAS            IMAGE                             PORTS
    ykhlebjgkgso        viz                 replicated          1/1                 dockersamples/visualizer:latest   *:8080->8080/tcp

  http://172.31.11.66:8080/

     docker container rm -f 3800374afa3a

http://172.31.11.66:8080/

- run one service container automatic in every worker machine. like :  monitoring services, antivirus etc. to moniter workernodes containers.

      docker service create -d -p 80:80 --name webserver --mode=global nginx

note : if you will add new workernode `global container` automatic add in new workernode. 


# Docker Swarm -Docker Swarm Label and Constraint

## Constraint
   
   - create sepific service container node  in some master node 
   only.
     

         docker service create -d -p 80:80 --name webserver --replicas=3 --constraint="node.role==manager" nginx

       above command to create replicas in manager node only.

          
         docker service create -d -p 80:80 --name webserver --replicas=10 --constraint="node.role==worker" nginx

       above command to create replicas in worker nodes only.

## Label
- how many types on label
  1.  node label
        
        - users defines label

       :: update label for ssd-drive in `workernode1`
       
          docker node update --label-add="ssd=true" workernode1.example.local

       Create new containers replicas in `SSD_Drive` label base in  workernode1

           docker service create -d -p 81:80 --name webserver --replicas=10 --constraint="node.labels.ssd ==true" nginx

       

          
   2. engine label

        - modify label in workernode2           

              cd /etc/docker/

                   vim daemon.json

                    {
                        "labels" : ["name=ajay", "ssd=true"]
                    }

            ``` sudo service docker restart ```

            ``` docker node inspect workernode2.example.local | less ```

       Create new containers replicas in `ajay` label base in  workernode2

              docker service create -d -p 80:80 --name webserver --replicas=10 --constraint="engine.labels.name==ajay" nginx

# Docker Swarm -Docker Swarm Node Availability
  
  availability string   Availability of the node ("active"|"pause"|"drain")

1. active 

   workernodes ready to take task from master 

        docker node ls


2. pause 
   
   It will not take any task from master. Existing tasks will run successuly without facing any issues. It will not take any new task. We use `pause` mode for debug purpose.
        
       docker node update --availability=pause workernode2.example.local

    if we will create new container it will not assign any new container to `workernode2`. New tasks will  assign to `Manager` and `workernode1` node.  s 

        service create -d -p 80:80 --name webserver --replicas=10  nginx

    see status in `workernode2`. new container will not assign to `workernode2`.     

        docker container ls  


    Active `workernode2`. 

        docker node update --availability=active workernode2.example.local    

    after active `workernode2` It will not take any existing containers or tasks from another workernodes to mantain the load balancing.       

3. drain
   
   Use for patching, debuging purpose for worker node and shift existing running node to another active workers nodes. In `workernode2` Existing running container will move to another  `Active workernodes`
   
       docker node update --availability=drain workernode2.example.local 

  In `drain` mode new task wiill not assign to `workernode2`    

       docker service create -d -p 82:80 --name webserver2 --replicas=10  nginx  

# Docker Swarm - Docker Swarm Service Create Options 

      --generic-resource list              User defined resources

      --reserve-cpu decimal                Reserve CPUs
      --reserve-memory bytes               Reserve Memory

      --limit-cpu decimal                  Limit CPUs
      --limit-memory bytes                 Limit Memory
      
      --update-delay duration              Delay between updates (ns|us|ms|s|m|h) (default 0s)
    
      --update-failure-action string       Action on update failure ("pause"|"continue"|"rollback") (default "pause")

      --update-max-failure-ratio float     Failure rate to tolerate during an update (default 0)

      --update-monitor duration            Duration after each task update to monitor for failure (ns|us|ms|s|m|h) (default 5s)

      --update-order string                Update order ("start-first"|"stop-first") (default "stop-first")

      --update-parallelism uint            Maximum number of tasks updated simultaneously (0 to update all at once) (default 1)

1. Resource limt

2. Reserve resource

3. update Polices
  
       docker service create  --replicas=2 nginx

# Docker Swarm - Docker Secret

    docker secret create dbpass -
    welcome123

    Control+D
    lwotk5kww1wxj8szk9c7thv49


    docker secret ls

how to assign secret in service. 
 
     docker service create -d --secret dbpass -p 83:80 --name webserver2 --replicas=10  nginx 

     docker exec -it 45753b8d576b sh

     cd /run/secrets

     cat dbpass


now create service with secret file.

     docker service create -d --secret dbpass -e MYSQL_ROOT_PASSWORD_FILE=/run/secrets/dbpass mysql

login mysql container verify secrect.

    docker exec -it aajgj6567 sh

    mysql -u root -pwelcome123



# Docker Swarm : Docker Overlay Networking


`docker network ls`

    NETWORK ID          NAME                DRIVER              SCOPE
    
    dc4dcf884c31        bridge              bridge              local
    
    1eb6259f5606        docker_gwbridge     bridge              local
    
    7b39c25dd18a        host                host                local
    
    knqq1ptk974i        ingress             overlay             swarm   
    
    a67a91d40321        none                null                local

`knqq1ptk974i        ingress             overlay             swarm` it will create by default while you will init docker swarm.

`1eb6259f5606        docker_gwbridge     bridge              local` it will  attachment overlay network.

1. how to create overlay network

       docker network create -d overlay test
       
       :: -d use for driver

      `docker network create -d <driver_name> <network_name>`

  :: If you don't mention your service any network name it will assign automatic to `ingress` `overlay` network. 

- create service with attach ingress network as created

    docker service  create -d --network test coolgourav147/sleeper_traceroute    

create replicas 3 for exist container 

    docker service scale vntx4z0qb4erd4xls6zslizu0=3

check created overlay network `test` on Manager

    docker network ls

    1ufstfvwwy6z        test                overlay             swarm


Check and verify overlay network `test` created automatic in workernodes as replica deployed.

    docker network ls

    1ufstfvwwy6z        test                overlay             swarm

:: note :  we can't attach overlay network `test` as created to container 

- create a overlay network to container

`docker network create -d overlay --attachable test1`
      
      docker container  create -d --network test coolgourav147/sleeper_traceroute 

now attach `test1` overlay network in `workernode1` and `worknode2`

       docker container run -d --network=test1 coolgourav147/sleeper_traceroute

       docker network ls

go to `workernode1`

`docker container exec -it a2780104032e bash`

`ifconfig`
  
          eth0      Link encap:Ethernet  HWaddr 02:42:0a:00:01:02  
                inet addr:10.0.1.2  Bcast:10.0.1.255  Mask:255.255.255.0
                UP BROADCAST RUNNING MULTICAST  MTU:1450  Metric:1
                RX packets:0 errors:0 dropped:0 overruns:0 frame:0
                TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
                collisions:0 txqueuelen:0 
                RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

          eth1      Link encap:Ethernet  HWaddr 02:42:ac:12:00:04  
                 inet addr:172.18.0.4  Bcast:172.18.255.255  Mask:255.255.0.0
                 UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
                 RX packets:11 errors:0 dropped:0 overruns:0 frame:0
                 TX packets:0 errors:0 dropped:0 overruns:0 carrier:0



 go to `workernode2`

`docker container exec -it 29f69433ded6 sh` 

`ifconfig`
 
          eth0      Link encap:Ethernet  HWaddr 02:42:0a:00:01:04  
               inet addr:10.0.1.4  Bcast:10.0.1.255  Mask:255.255.255.0
               UP BROADCAST RUNNING MULTICAST  MTU:1450  Metric:1
               RX packets:0 errors:0 dropped:0 overruns:0 frame:0
               TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
               collisions:0 txqueuelen:0 
               RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

         eth1      Link encap:Ethernet  HWaddr   02:42:ac:12:00:04  
                inet addr:172.18.0.4  Bcast:172.18.255.255  Mask:255.255.0.0
                UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
                RX packets:11 errors:0 dropped:0 overruns:0 frame:0
                TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
                collisions:0 txqueuelen:0 

    
`ping 10.0.1.2`            


:: note : `eth1` with attach bydefault with below network:

       1eb6259f5606        docker_gwbridge     bridge              local


2. create ` ingress network `   

- create a nginx container with replicas 2 with attach any network driver bcz Bydefault it with attach `ingress network`.

      docker service create -d -p 9000:80 --replicas=2 nginx

modify nginx html file in both containers.

      docker container exec -it 3428df63f82f /bin/bash
      
      cd /usr/share/nginx/html

      echo "worker2" > index.html


     docker container exec -it 2ec43dc8902e /bin/bash
      
     cd /usr/share/nginx/html/   

     echo "manager" > index.html

http://workernodesip  to check the load balancing.


- now create service to attch  `overlay test` network 

      docker service create -d --network test -p 9000:80 --replicas=2 coolgourav147/sleeper_traceroute

login in container in workernode
 
      docker container exec -it a1b148dcebed /bin/bash
       
`ifconfig`

          eth0      Link encap:Ethernet  HWaddr 02:42:0a:ff:00:7c  
          inet addr:10.255.0.124  Bcast:10.255.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1450  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

          eth1      Link encap:Ethernet  HWaddr 02:42:0a:00:00:0f  
          inet addr:10.0.0.15  Bcast:10.0.0.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1450  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

         eth2      Link encap:Ethernet  HWaddr 02:42:ac:12:00:04  
          inet addr:172.18.0.4  Bcast:172.18.255.255  Mask:255.255.0.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:12 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:936 (936.0 B)  TX bytes:0 (0.0 B)

        lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

`doker network inspect test | less`

`docker network inspect ingress | less`


note : if we will publish any port it will attach with ingress network as well as `test` overlay network 