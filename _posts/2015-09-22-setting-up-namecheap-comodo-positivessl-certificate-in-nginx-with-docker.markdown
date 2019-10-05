---
layout: post
title: Setting up NameCheap (Comodo PositiveSSL) Certificate in Nginx with Docker
date: '2015-09-22 16:27:26'
---

There are two main steps to setting up SSL in Nginx with Docker:

1) Getting and setting up your certificate on nginx in general

2) Deploying your files and key to the relevant container


#### General Setup

*For this writeup, I'm using ```docker-compose``` to manage a Docker deployment with a separate ```web``` and ```nginx``` container. For this one, the ```web``` container is running gunicorn on port 8000 while the ```nginx``` container is listening on 80 and 443.*

For #1, this [link](https://ar.al/scribbles/setting-up-ssl-with-nginx-using-a-namecheap-essentialssl-wildcard-certificate-on-digitalocean/) was super helpful with information about what filesd NameCheap provides you. They ultimately send you multiple files that you need to combine into a cert bundle. 

Make sure that you have generated the proper CSR and submitted it to NameCheap. Once they verify the request and issue you the certificate, they'll email you 4 files in a zip. It should look something like the below after you extract it:

```
yourdomain_com
├── AddTrustExternalCARoot.crt
├── COMODORSAAddTrustCA.crt
├── COMODORSADomainValidationSecureServerCA.crt
└── yourdomain_com.crt
```

You need to combine them into a cert bundle with ```*.cer``` extenstion. Just ```cat``` the files all together into a file named ```something.cer```.
```
cat yourdomain_com.crt COMODORSADomainValidationSecureServerCA.crt COMODORSAAddTrustCA.crt AddTrustExternalCARoot.crt > yourdomain_com.cer
```

 You can choose whatever filename you want, but make sure it has the ```*.cer``` file extension.

Now move this ```.cer``` file to the same location that your original key is located. It's probably named something like ```yourdomain_com.key``` or something similar. Ultimately it just makes it easier to copy them over to your server or Docker setup.

##### Basic SSL setup on nginx

Ultimately, the nginx conf file will point to local copies of the ```cer``` and ```key``` file from above. We're talking about Docker containers, but it'd be the same for a VPS on something like AWS or digitalocean.

To configure Nginx to use the certs, they need to be somewhere on the host box. A common practice is to put the public cert in the ```/etc/ssl/certs``` directory and the private in ```/etc/ssl/private```

You can put them anywhere, so long as you keep it straight. The benefit of the setup above is that you can set the permissions to 0755 for the private folder and 0700 for the public. For both of them, the owner/group is root:root.

This will be the structure I'll be using within the ```nginx``` container.

### Putting it all together in your Dockerfile

Your ```Dockerfile``` is going to copy over the certs and keys to the directory structure discussed above on the ```nginx``` container.

Then, it will copy over a conf file for Nginx that references the certs. That's it.

##### Copy Certs in Dockerfile

The relevant line in your Nginx Dockerfile is
```
ADD ./ssl /etc/ssl
```
This copies the contents of the local ```ssl``` directory to the host ```/etc/ssl``` directory. In this instance, it's to the Nginx container.

This is what our local ```./ssl``` directory looks like:
```
ssl/
├── certs
│   └── yourdomain_com.cer
└── private
    └── yourdomain_com.key
```


#### Nginx conf file

Last, we need to copy over our config file to tell Nginx to use the ssl certs, so put something like this in your ```nginx``` Dockerfile:

```
ADD sites-enabled/ /etc/nginx/sites-enabled
```

Your local sites-enabled folder should look like:
```
sites-enabled/
└── yourdomain_com
```


Your nginx conf file may differ, but here's a sample one entitled ```yourdomain_com``` to demonstrate the relevant ssl portion of the block:
```
server {
    listen 443;
    
    ssl on;
    ssl_certificate /etc/ssl/certs/yourdomain_com.cer;
    ssl_certificate_key /etc/ssl/private/yourdomain_com.key;

    server_name yourdomain.com www.yourdomain.com;
    access_log /var/www/yourdomain.com/access.log;
    error_log /var/www/yourdomain.com/error.log;

    location / {
        proxy_pass http://web:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}


server {

    listen 80;
    server_name yourdomain.com www.yourdomain.com;
    charset utf-8;

    location / {
        proxy_pass http://web:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```
The important part is that there are two server blocks: one listening on port 443 (ssl) and one on 80.

Note this section though:
```
ssl on;
ssl_certificate /etc/ssl/certs/yourdomain_com.cer;
ssl_certificate_key /etc/ssl/private/yourdomain_com.key;
```
First, it sets ssl on and then sets it up to point to the directory on the host (Nginx container) that you copied the files to in your Dockerfile.




Putting it all together, the directory for the ```nginx``` container looks like this:
```
nginx/
├── Dockerfile
├── sites-enabled
│   └── yourdomain_com
└── ssl
    ├── certs
    │   └── yourdomain_com.cer
    └── private
        └── yourdomain_com.key
```

#### Finishing Up
That's it. Now run the usual commands to get up and running
```
docker-compose build
docker-compose up
```

You should now be able to access your site via ```http``` or ```https```. If you want to only serve traffic using SSL, you'll need to alter the server block listening on port 80 above in the nginx conf above to rewrite or redirect all requests to https. 





