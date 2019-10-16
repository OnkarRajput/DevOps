# ansible become

Become. Ansible allows you to 'become' another user, different from the user that logged into the machine (remote user).


--ask-become-pass
 	ask for privilege escalation password

–become,-b
run operations with become (no password implied)


--become-method=BECOME_METH
OD
 	privilege escalation method to use (default=sudo), valid choices: [ sudo | su | pbrun | pfexec | doas ]

     
--become-user=BECOME_USER
 	run operations as this user (default=root)