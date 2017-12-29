I follow the tutorial found online to set up my SSH login, and meet the problem that even everything is setup correctly I still need to enter the password. And there are no error messages printing out, either. 

I log in the **reomote machin** manually, and do the following command under **root mode**:
```[root@wyeasthead .ssh]# service sshd status -l```

and get the following info:

```
Redirecting to /bin/systemctl status  -l sshd.service
● sshd.service - OpenSSH server daemon
   Loaded: loaded (/usr/lib/systemd/system/sshd.service; enabled; vendor preset: enabled)
   Active: active (running) since Mon 2016-09-19 17:37:27 EDT; 3 weeks 6 days ago
     Docs: man:sshd(8)
           man:sshd_config(5)
  Process: 12553 ExecReload=/bin/kill -HUP $MAINPID (code=exited, status=0/SUCCESS)
 Main PID: 1161 (sshd)
   CGroup: /system.slice/sshd.service
           └─1161 /usr/sbin/sshd -D

Oct 16 21:16:08 wyeasthead  systemd[1]: Reloaded OpenSSH server daemon.
Oct 16 21:16:08 wyeasthead  sshd[1161]: Server listening on 0.0.0.0 port 22.
Oct 16 21:16:08 wyeasthead  sshd[1161]: Server listening on :: port 22.
Oct 16 21:16:12 wyeasthead  sshd[12555]: Authentication refused: bad ownership or modes for directory /home/jiaqi/.ssh
Oct 16 21:19:30 wyeasthead  sshd[12565]: Connection closed by 131.252.208.103 [preauth]
Oct 16 21:19:30 wyeasthead  sshd[12567]: Authentication refused: bad ownership or modes for directory /home/jiaqi/.ssh
Oct 16 21:19:30 wyeasthead  sshd[12567]: Connection closed by 131.252.208.103 [preauth]
Oct 16 21:19:30 wyeasthead  sshd[12569]: Authentication refused: bad ownership or modes for directory /home/jiaqi/.ssh
Oct 16 21:19:51 wyeasthead  sshd[12569]: Accepted password for jiaqi from 131.252.208.103 port 43790 ssh2
Oct 16 21:20:17 wyeasthead  sshd[12592]: Authentication refused: bad ownership or modes for directory /home/jiaqi/.ssh
```

This line `Authentication refused: bad ownership or modes for directory /home/jiaqi/.ssh` indicates that the permission of the directory `/home/jiaqi/.ssh` is not correct. 

All right, Let me take a look at the permission.

```
[jiaqi@wyeasthead .ssh]$ ls -al
total 8
drwx------. 2 jiaqi jiaqi   28 Oct 16 22:58 .
drwx------. 3 jiaqi jiaqi 4096 Oct 16 22:58 ..
-rwx------. 1 jiaqi jiaqi  741 Oct 16 21:19 authorized_keys
```

**My home directory should be writable only by myself, `~/.ssh` should be 700, and `authorized_keys` should be 600.**

Fix the permission:

```
[jiaqi@wyeasthead .ssh]$ chmod g-w /home/jiaqi/
[jiaqi@wyeasthead .ssh]$ chmod 700 /home/jiaqi/.ssh/
[jiaqi@wyeasthead .ssh]$ chmod 600 /home/jiaqi/.ssh/authorized_keys 
```

OK, check the permission again:

```
[jiaqi@wyeasthead .ssh]$ ls -al
total 8
drwx------. 2 jiaqi jiaqi   28 Oct 16 22:58 .
drwx------. 3 jiaqi jiaqi 4096 Oct 16 22:58 ..
-rw-------. 1 jiaqi jiaqi  741 Oct 16 21:19 authorized_keys
```
The permission of the file `authorized_keys` was modified. 

Now let me try SSH again:

```
➜  ~ ssh jiaqi@wyeasthead
Last login: Sun Oct 16 21:43:36 2016 from ada.cs.pdx.edu
[jiaqi@wyeasthead ~]$ 
```

It works!






