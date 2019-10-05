---
layout: post
title: Getting started with CMU Sphinx on Mac OS X
date: '2015-06-01 18:02:24'
---

This is going to be the first post in a short series about getting the CMU Sphinx stack up and running both on OS X and then subsequently on iOS. This first post is just going to detail getting the OS X setup sorted out. I'm working on Yosemite, but it should also work with Mountain Lion and Mavericks. The github repo linked below has some notes on OS X verision compatibility if you run into any issues.

The documentation for CMU Sphinx is great, but the library out of Carnegie Mellon is far from "it just works" easy. It takes a great deal of effort to get it setup and working properly, so I wanted to chronicle those actions at this time.

I hadn't messed with it for a few years, so the version I had was outdated. Not to mention that I had a great deal of difficulty getting it installed.

Gone are the days of downloading the source code off sourceforge and having to make it yourself...

### Enter homebrew

I'm going to use [homebrew](https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/FAQ.md#faq) because it greatly simplifies the install process. If you're a mac user and don't have that installed, I suggest you install that first.

If you already have it, I like to update it to make sure I'm cooking with fire. Really this just updates homebrew itself and any existing formulae.

    brew update

### Custom Brew Formula

Then we're going to add a "tap" to get a better formula for CMU Sphinx. The standard one managed by homebrew is installing v0.8 which was released in 2012. **Don't use that one.** There are a lot of new features in a more current release, so I'm using this formula: https://github.com/watsonbox/homebrew-cmu-sphinx. It grabs the most recent version pushed to the library's repository.

    brew tap watsonbox/cmu-sphinx

### Install

Then we're going to install Sphinxbase and PocketSphinx. (Sphinxbase is required for PocketSpinx) Note that the HEAD argument just tells the install formula to grab the most recent commit from each repo.

    brew install --HEAD watsonbox/cmu-sphinx/cmu-sphinxbase
    brew install --HEAD watsonbox/cmu-sphinx/cmu-pocketsphinx

You should see some code in the terminal telling you that each has been installed. If you get any errors, google around.

### Testing your install

Next, we'll test it out to make sure it's working before moving on to iOS. 

    pocketsphinx_continuous -infile sample.wav

Where sample.wav is some file that you you're wanting to extract the text.

If you want to use the live mic feed, then use this command

    pocketsphinx_continuous -inmic yes

You should see it spit out some text after you speak a few words. The matching won't be the best, but it should demonstrate that your install is working. To get more precise results, you'll want to pass in superior hmm (acoustic model), language model, and dictionary files. 

To learn more about those, you can read the [tutorial](http://cmusphinx.sourceforge.net/wiki/tutorial).