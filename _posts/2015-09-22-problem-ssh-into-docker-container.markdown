---
layout: post
title: Problem trying to SSH into Docker container on OS X
date: '2015-09-22 01:41:23'
---

I was doing something that I've done hundreds of times before and not had an issue with. I was running a ```docker exec``` command to open a shell in one of my containers.

```
$ sudo docker exec -it bca92q289b7e bash
Post http:///var/run/docker.sock/v1.20/containers/bca92q289b7e/exec: dial unix /var/run/docker.sock: no such file or directory.
* Are you trying to connect to a TLS-enabled daemon without TLS?
* Is your docker daemon up and running?
```

But as you can see from the above output, I was getting an error. I couldn't figure out what was different from previous times.

The ```sudo``` was what was giving me problems.

```
$ docker exec -it bca92q289b7e bash
root@bca92q289b7e:/# ls
```
Removing the ```sudo``` from the command made it work. Phew.