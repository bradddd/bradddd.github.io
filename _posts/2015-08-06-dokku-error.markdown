---
layout: post
title: 'Dokku - error: failed to push some refs to'
date: '2015-08-06 05:11:11'
---

I'm posting this in the hopes that I can solve another persons's problem, as it took me longer to resolve than it should have.

I was playing around with moving some simple Flask apps over to a dokku server on digital ocean. 

Out of the blue, I push one repo to the server, and I get the following error: 

```
To dokku@mydomain.com:myapp
 ! [remote rejected] master -> master (pre-receive hook declined)
error: failed to push some refs to 'dokku@mydomain.com:myapp'
```

I enabled some verbose mode that output stack traces during the git push. Somewhere in a wall of text, toward the end where I'm getting the error message, I see this:

```
setuidgid: fatal: unable to run gunicorn: file does not exist
```

I wasn't immediately aware of what it was saying, but ultimately, I realized that gunicorn wasn't listed in the requirements.txt file and therefore was not being setup in the container.

So let this serve as a reminder to anyone else that might have done the same thing. I had gunicorn installed in my virtualenv, but I failed to do a pip freeze to update the requirements.txt file.

