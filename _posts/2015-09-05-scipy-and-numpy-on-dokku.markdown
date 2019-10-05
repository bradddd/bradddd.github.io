---
layout: post
title: Installing scipy and numpy on Dokku for Flask app
date: '2015-09-05 04:28:41'
---

If you're trying to work with a basic Flask app and have scipy and/or numpy in your requirements.txt file, Dokku will not be able to install these when you try to push your repo to your deployment server.

You'll see lines like the following:

```
Could not locate executable gfortran
Could not locate executable f95
Could not locate executable ifort
Could not locate executable ifc
Could not locate executable lf95
Could not locate executable pgfortran
Could not locate executable f90
Could not locate executable f77
Could not locate executable fort
Could not locate executable efort
Could not locate executable efc
Could not locate executable g77
Could not locate executable g95
Could not locate executable pathf95
don't know how to compile Fortran code on platform 'posix'
```

Ultimately, pip isn't going to get it done. You have a couple options. You can set a custom Dockerfile and use that during deployment, or you can use a custom buildpack. Now Dokku has a default buildpack for each language of deployment, but you can specify a specific one by committing a file named .env to the root of your repo. You can read more about it in the Dokku documentation [here](http://progrium.viewdocs.io/dokku/application-deployment/).

The relevant line is in the file .env
```
export BUILDPACK_URL=<repository>
```

For our purposes, you can find a github repo where someone has shared their buildpack for use on heroku.

Here's the one I used: https://github.com/thenovices/heroku-buildpack-scipy

Just make sure to get one that is still maintained.

Then, take note of the supported versions, and make sure that your requirements.txt reflect the appropriate versions of numpy and scipy.

Then push as you normally would to deploy:
```
git push dokku master
```

Success!

Note: I was also working with something that required matplotlib. For some reason, I had to downgrade to 1.1 to get it to work via pip. As of September 2015, the current version of matplotlib (1.4.3) installs fine.


