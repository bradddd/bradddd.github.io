---
layout: post
title: Uploads work with Python Development Server but not Nginx/Gunicorn
date: '2015-10-10 16:48:43'
---

I ran into this the other day when working on a simple flask app, so I thought I'd share this if anyone else was banging their head like I was.

I was testing some uploads for large files, around 50mb give or take. Now anyone who has worked with nginx, or maybe from dabbling in word press, will know that the default upload file size is 1mb by default. I knew this.

#### Nginx max upload file size

The way to override that 1mb limit is by passing ```max_client_body_size 50mb;``` into your ```http```, ```server```, or ```location``` block of your particular nginx conf file-- each with increasing level of specificity for your given setup. You can change the 50mb to whatever you need.

I logged in and changed this setting, but it still didn't work. I restarted nginx a few times, even rebooting the machine, but file uploads still weren't working.

The browser was telling me that it was able to upload 100% of the file before the error.

Skimming the ```nginx``` logs yielded this: 
```
[error] 20958#0: *41 client intended to send too large body: 1082681 bytes
...
```
What gives?!

Anyways, when debugging this, I didn't have the problem when running the python development server, so when I switched to production settings and tried ```nginx```, I figured for sure that was the issue causing the ```Internal Server Error``` messages.

I had done this a few times before, so I knew that *should* have been all I needed to change. This made me suspect that something was up with ```gunicorn```, so instead of running ```gunicorn``` as a daemon, I launched it from the command line. This allowed me to enable debugging to ```STDOUT```. I thought I was going to be able to dissect the problem front and center.

Then it worked. For all file sizes even.

#### Enter permissions, because, it's always permissions.

I was so quick to throw the nginx conf file under the bus, that I overlooked the obvious. I had never changed the permissions for the project directory. What was happening was the directory was owned by ```root``` but gunicorn was being run by ```webappuser```. I would have run into the upload size limit issue immediately, but the more pressing issue was that ```webappuser``` didn't have permission to write to the ```/uploads``` directory.

A quick ```chown``` over to the user that runs gunicorn fixed it.

