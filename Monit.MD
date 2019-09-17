



```
sudo vim /etc/monit/monitrc
```
```
set daemon 60 #check services ever 60 seconds
  set logfile /var/log/monit.log
  set idfile /var/lib/monit/id
  set statefile /var/lib/monit/state

#Event queue
  set eventqueue
      basedir /var/lib/monit/events # set the base directory where events will be stored
      slots 100                     # optionally limit the queue size

#Mail settings
set mail-format {
  from: monit@wp-bullet.com
  subject: monit alert --  $EVENT
  message: $EVENT Service $SERVICE
                Date:        $DATE
                Action:      $ACTION
                Host:        $HOST
                Description: $DESCRIPTION

           Your faithful employee,
           Monit }
set mailserver smtp.mailgun.org port 587
  username postmaster@mg.yourdomain.com password "your-smtp-password"
  using TLSV1 with timeout 30 seconds
set alert webmaster@wp-bullet.com #email address which will receive monit alerts

#http settings
set httpd port 2812 address 0.0.0.0  # allow connections on all adapters
    ssl enable
    pemfile  /var/certs/monit.pem
#    allow 0.0.0.0/0.0.0.0 # allow all IPs, can use local subnet too
#    allow wp-bullet.crabdance.com        # whitelist allow dynamicdns address to connect
    allow admin:wp-bullet      # require user 'admin' with password 'wp-bullet'

#allow modular structure
    include /etc/monit/conf.d/*
```


```
sudo monit -t
```

```
sudo service monit restart
```

###If you have problems consider using a Mailgun SMTP relay, the monit configuration for mail then looks like this
```
set mailserver 127.0.0.1 port 25
set alert admin@wp-bullet.com #email address which will receive monit alerts
```
































```
sudo vim /etc/init.d/tomcat
```


```
#!/bin/sh
####################################################################
# Created by: Abhijit Sandhan
# Purpose: Start/Stop/Restart Apache Tomcat service
####################################################################
# Check the path of Tomcat and set environment variables as follows
# in the .bashrc profile
export CATALINA_HOME="/opt/mount_point/application/tomcat8"
export CATALINA_BASE="/opt/mount_point/application/tomcat8"
export JAVA_HOME="/usr/lib/jvm/java-8-oracle"
export CATALINA_PID=/opt/mount_point/application/tomcat8/tomcat.pid 

 
#Use Case statement to start / stop /reset tomcat service
case $1 in
start)
cd /opt/mount_point/application/tomcat8/bin/
./startup.sh
;;
stop)
cd /opt/mount_point/application/tomcat8/bin/
./shutdown.sh
;;
restart)
cd /opt/mount_point/application/tomcat8/bin/
./shutdown.sh
cd /opt/mount_point/application/tomcat8/bin
./startup.sh
;;
esac
exit 0
````

```
/etc/init.d/tomcat start
```
```
/etc/init.d/tomcat restart
```

```
sudo ln -s /etc/init.d/tomcat /etc/rc2.d/S99tomcat
```






##tomcat

```
check process tomcat with pidfile /opt/mount_point/application/tomcat8/tomcat.pid
  start program = "/etc/init.d/tomcat8 start" 
  as uid root gid tomcat
  stop program = "/etc/init.d/tomcat8 stop" 
  as uid root gid tomcat
  if failed port 8080 then alert
  if failed port 8080 for 5 cycles then restart
```
##ssh
```
check process sshd with pidfile /var/run/sshd.pid
    start program  "/etc/init.d/ssh start"
    stop program  "/etc/init.d/ssh stop"
    if failed port 25499 protocol ssh then restart
    if 3 restarts within 5 cycles then alert
```

##php-7.0fpm
 ```
 check process php7-fpm with pidfile /run/php/php7.0-fpm.pid
    start program = "/usr/sbin/service php7.0-fpm start" with timeout 60 seconds
    stop program  = "/usr/sbin/service php7.0-fpm stop"
    if failed host 127.0.0.1 port 9000 type tcp for 3 cycles then restart
```
##nginx
```
check process nginx with pidfile /var/run/nginx.pid
    start program = "/etc/init.d/nginx start"
    stop program = "/etc/init.d/nginx stop"
You have new mail in /var/mail/witfin
```

##mysql
```check process mysql with pidfile /run/mysqld/mysqld.pid
    group mysql
    start program = "/etc/init.d/mysql start"
    stop program = "/etc/init.d/mysql stop"
    if failed unixsocket /var/run/mysqld/mysqld.sock then restart
    if failed port 3306 protocol mysql then restart
    if 3 restarts within 5 cycles then alert```
