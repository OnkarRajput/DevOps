# To enable the slow query log in MySQL, follow these steps:

# Open MySQL configuration file my.cnf:

```sudo vi /etc/mysql/my.cnf```

# Add the records below under the [mysqld] section:
```
long_query_time = 2  

log_output = TABLE  

slow_query_log = ON  
```
# Restart mysql service:
```
sudo systemctl restart mysql
```
