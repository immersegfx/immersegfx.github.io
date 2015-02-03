---
layout: post
title: "Setting up a NodeJs development environment on Ubuntu 14"
description: "Notes on getting setting up a NodeJs development environment on Ubuntu 14"
category: information
tags: [NodeJs, Npm]
---
If you are like me the first thing that comes to mind when installing a new application is to fire up apt and download it
from the repository.  I mean why not get all the benefits of simple updates and configuration.  Well quickly I noticed there
were issues running <code>npm</code> and permissions.  Npm will warn you when running <code>sudo npm install -g</code> and 
in my experience ignoring warnings is not the best way to get started with something. 

### NodeJs setup without sudo

After a bit a research I found a link to a Github Gist with instructions on how to configure your dev without having to worry 
about sudo.  [https://gist.github.com/isaacs/579814#file-node-and-npm-in-30-seconds-sh](https://gist.github.com/isaacs/579814#file-node-and-npm-in-30-seconds-sh)

I chose the first option which worked like a charm but it is worth a read as there may be one better suited for your needs.

<script src="https://gist.github.com/isaacs/579814.js?file=node-and-npm-in-30-seconds.sh">
</script>

### Conclusion

And that's all there is so far.  Hope this can help save someone a little time and as I find more tips I will come 
back and update things.
