# Asynchronous Actions and Polling

By default tasks in playbooks block, meaning the connections stay open until the task is done on each node. This may not always be desirable, or you may be running operations that take longer than the SSH timeout.

To avoid blocking or timeout issues, you can use asynchronous mode to run all of your tasks at once and then poll until they are done.

The behaviour of asynchronous mode depends on the value of poll.

## Avoid connection timeouts: poll > 0

The default poll value is 15 seconds if you do not specify a value for poll:

```
vim asyn.yml

---
- hosts: webservers

  tasks:
    - name: sleep for 60 
      command: sleep 60
      async: 70
      poll : 35


```
async: 70, poll : 35 will wait for 70+35=105 minutes for task.

- fire and forget will not check its successful or not. Set `poll : 0`

```
vim fire_and_forget.yml

---
- hosts: webservers

  tasks:
    - name: sleep for 60 
      command: sleep 60
      async: 70
      poll : 0

```


