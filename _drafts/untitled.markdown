---
layout: post
title: "(Untitled)"
---

```
brew install sox
```

you can follow this tutorial to get gstreamer on OSX
```
$ brew install wxpython
$ brew install gstreamer gst-plugins-base gst-plugins-good gst-plugins-bad gst-plugins-ugly gst-libav
```

```
$ svn checkout http://voiceid.googlecode.com/svn/trunk voiceid
$ cd voiceid
```
I was tryign to run the install command, but kept getting an error message
```
Error: GStreamer not found
```
If you google this, it takes you to the voiceid source code, specifically a check to make sure gst-launch is installed. 
```
...
try:
    sphinx = check_output('which gst-launch')
except:
    print "ERROR: GStreamer not installed"
    exit(0)
...
```
When I look on my system, it's installed, but it is version 1.0 and has that appended to the end of the gst-launch name. So the quick fix for this is to just create a symbolic link so that it finds something. If anyone has a more eloquent solution or install method than this, let me know.
```
$ ln -s /usr/local/bin/gst-launch-1.0 /usr/local/bin/gst-launch
```

Then run 
```
$ sudo python setup.py install
```
It'll install correctly, but then you're prompted w/ an error whenever you try to run videoplayerid. It'll complain about not being to import Publisher. If you google it, you'll wind up here: http://stackoverflow.com/questions/5374451/importerror-cannot-import-name-publisher

So long story short, the version of wxpython is too new. The videoid code was last updated in 2013, so that's not too surprising. You could go back and , or you could update the code according to the Stack Overflow post. I opted for the latter as I had some other things that used wxpython already and didn't want to risk downgrading my install.

So here I use some perl to do some find replaces to change Publisher() to Publisher.
```
$ sudo perl -pi -w -e 's/Publisher()./Publisher./g;' *
$ sudo perl -pi -w -e 's/wx.lib.pubsub import Publisher/wx.lib.pubsub import setuparg1/g;' ./scripts-2.7/onevoiceidplayer 
$ sudo perl -pi -w -e 's/wx.lib.pubsub import Publisher/wx.lib.pubsub import setuparg1/g;' ./scripts-2.7/voiceidplayer 
```
After that, run install again.

pip install MplayerCtrl