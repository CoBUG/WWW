---
title: What is OpenSSH?
date: '2014-05-28'
tags:
  - OpenSSH
---

# What is OpenSSH?

>"A cryptographic network protocol for secure data communication, remote command-line login, remote command execution, and other secure network services between two networked computers." -Wikipedia


###Why would I want to use ssh?

Because it's awesome!

---

### About SSH

* Designed to replace telnet which sends information via plaintext.  For instance, telnet sends passwords in plaintext.  
* There are two versions of ssh, SSH-1 and SSH-2.  SSH-1 is considered obsolute due to design and security issues.
* OpenSSH was created by OpenBSD devs and is ported to many other systems, like Linux.
* OpenSSH is the most popular ssh implementation.  
* RFC 4253
* TCP/UDP Port 22

---

### Getting started with OpenSSH in OpenBSD


* OpenSSH server and client is installed by default in OpenBSD's.  

* OpenSSH server is started at boot by default.  

To check to make sure that the OpenSSH server (the ssh daemon, or sshd) is running, log into the system and do something like the following:

```
pgrep -lf sshd
```

And you should see soemthing like this:

```
7656 sshd
```

If you do, sshd is running.

---

### The first SSH

If you have an OpenBSD box with a user account, you can ssh into it.

```
ssh yourUser@theBoxYouWantToSshInto 
```

Enter your password and that's it!


---
### Managing sshd

To stop the daemon (must command as root):

```
/etc/rc.d/sshd stop
```

```
/etc/rc.d/sshd start
```

There is also restart, reload, and check.  

If a user is already ssh'd in the box, **stopping the daemon will not stop active connections.**  It only stops new connections from initiating!  

If you want to stop all ssh activity, you must also kill active ssh sessions in addition to stopping the daemon. "pgrep -lf sshd" will show active ssh connections.  

---

### Locking down SSH

In the ssh_config file there are many options available to tighten security. For example, you could do something like this:

```
Ciphers 3des
PasswordAuthentication no
Protocol 2 
AllowUsers zamicol kur0
PermitRootLogin no
Port 7070
```

Some of these settings may not be a good idea, but it might help keep the baddies away.  

You must restart sshd for these changes to take effect.

```
/etc/rc.d/sshd restart
```

---

### Passwords are lame. Save us ssh-keygen!

Passwords *are* lame.  That is why there is ssh-keygen, enabling passwordless logins.

* Go to ~/.ssh
    * There should be "authorized_keys" and "known_hosts", but nothing else on a fresh system
* Type
	```
	ssh-keygen -t rsa -b 2048 -C "LabelofYouChoosing"
	```
	Pick a good lable, as it will identify your key to others. 

* It will ask for a place to save it.  The default is fine. 
* Leave the passphrase empty.  

---

### Passwords are lame continued

* There should now be an id_rsa and a id_rsa.pub file.
* Append id_rsa.pub to the end of the authorized_keys file on the machine you want to ssh to.  You can give out id_rsa.pub to any machine you want to log into using a key.
    * **Never** give out the id_rsa.  That's your private key!
    * id_rsa.pub should be in /home/theUser/.ssh/authorized_keys.  
* You will now be able to login as that user without a password. Whoooo!

---

### Secret tunnels

![Secret Tunnel](http://24.media.tumblr.com/eb5a56a9082029777ee0421e34d6d6c3/tumblr_mitciedNmy1qat7jlo2_1280.png)

Is your work place blocking that sweet, sweet ASCII porn?  Use a ssh tunnel!

---

### Secret tunnels: Port Forwarding

Here's an example of port forwarding.  

```
ssh -L localPort:hostYouAreRoutingThrough:hostPort destinationHost

```

So if your workplace was blocking reddit, this is how you get around that:

```
ssh -L 7070:reddit.com:80 homeIpAddress
```

Now in your browser, use the following URL to browse!

```
http://localhost:7070
```

There's Reddit!  Weeeee!

---

### Moar tunnels!

What if you want to send all web browser traffic over a proxy?

```
ssh -D 7070 user@host 
```

Then configure your browser to use the proxy on the local port 7070.  Now you can browse anywhere over the proxy!

-D still does not tunnel DNS lookups.  If you need all traffic to go through a tunnel, you can use vpn or ssh vpn!

---


###More Resources
[OpenBSD man page](http://www.openbsd.org/cgi-bin/man.cgi?query=ssh)

[RFC 4253](http://tools.ietf.org/html/rfc4253)

[VPN's on OpenBSD](http://www.kernel-panic.it/openbsd/vpn/vpn5.html)
