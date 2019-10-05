---
layout: post
title: Working with Groot on iOS
---

Going to work with https://github.com/gonzalezreal/Groot, an iOS library that "provides a simple way of serializing Core Data object graphs from or into JSON."

I found the README to be little lacking, so I thought I'd document my thoughts here to help others in a similar position or myself at a later date.

The big key is to head to https://github.com/gonzalezreal/Groot/blob/master/Documentation/Annotations.md as the secret sauce is the annotations to your Core Data models.

To dive deeper, and maybe see a few examples, I searched github to find another project that uses the library (at the time of writing, it had 359 stars, so it's definitely being used... but it hasn't gotten a lot of discussion).

Anyways, turns out the author dogfoods pretty well, so I didn't have to look beyond his [ComicSearch](https://github.com/gonzalezreal/ComicSearch) code--an app that allows you to query an API to get a bevy of information about a wide range of comic books.

Firing that up and poking around the Data Model inspector was pretty useful.

The big thing, and it's documented in the github repo, are the "annotations" or more importantly, a Key/Value pair for each attribute for a given model.

The below image shoes you what to look for. The problem is that it's more subtle than that.
![](/content/images/2015/11/Screen-Shot-2015-11-18-at-7-50-09-PM-1.png)

Going back and rereading the original documentation, the author says all of this. I don't know why it wasn't immediately clear.

*side rant* - editing those annotations is a pain. For some reason, Xcode always wants to kick you back up to the parent Entity (which can have it's own key/value pairs) when you're trying to work on its attributes. I'm sure I'm not alone in getting wrecked by this little frustration.

Here's what you're shooting for. Note that it says "Attribute" at the top.

![](/content/images/2015/11/Screen-Shot-2015-11-18-at-8-01-09-PM.png)

Don't add it here. This is the model. You can tell because it says "Entity" at the top.

![](/content/images/2015/11/Screen-Shot-2015-11-18-at-8-01-21-PM.png)

###Non-modular header inside framework problem when building (Cocoapods v0.39.0)

Fast forward to when you try to compile.

Currently, with cocoapods 0.39, you'll get an error about non-modular something...
https://github.com/gonzalezreal/Groot/issues/57

This is allegedly a cocoapods problem: https://github.com/CocoaPods/CocoaPods/issues/4420

The [fix](https://github.com/CocoaPods/CocoaPods/issues/4420#issuecomment-150668304) for this, at least until cocoapods resolves it, is to add this to the bottom of you ```Podfile``` and run ```pod install``` again.

```
post_install do |installer|
   `find Pods -regex 'Pods/Groot.*\\.h' -print0 | xargs -0 sed -i '' 's/\\(<\\)Groot\\/\\(.*\\)\\(>\\)/\\"\\2\\"/'`
end
```

I'm not going to lie, I don't fully understand it any more than this will go through and replace angle bracket include statements with ones enclosed by quotation marks.

Whatever the case, this got my project to build again.