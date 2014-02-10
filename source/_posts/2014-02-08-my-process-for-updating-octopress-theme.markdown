---
layout: post
title: "My Process for updating Octopress Theme"
date: 2014-02-08 21:25:33 +0200
comments: true
categories: [git, octopress]
publish: true
---

TLDR: This is my general process of modifying code from Github (or other) that I use (could be an Octopress theme or a [cocoapod](http://cocoapods.org/) or any other open source code one might use), all while maintaining the ability to update and contributing back (sometimes)...
<!-- more -->
Recently I installed Octopress theme by [Adrian Artiles](http://www.adrianartiles.com/), for this blog.  
As suggested by Adrian using git submodule, in octopress ``.theme`` folder.  
My first post was link-post (see [Link Bloging](http://octopress.org/docs/blogging/linklog/)). When checking out the preview of the blog with the new link post I noticed that the post title did not link to the external-url of the post...  

Long story short I [found](http://www.candlerblog.com/2012/01/30/octopress-linked-list/)  what's needed to be changed in the theme to make the post title link to external site.

At this point I do the following:  

1. Fork the project on Github.
2. Change the submodule origin to the forked repo:  
	
	
		$ cd /submodule/path
		$ git remote set-url origin https://github.com/[your-user]/[forked-repo].git

3.  Add the original repo of the fork as upstream:

		cd /submodule/path  
		git remote add upstream https://github.com/[user-forked-from]/[repo-forked-from].git

4.  Create a new branch in the submodule with the name of the change (I create a branch for each change that is applied).

5.  Apply the fix / change.

6.  Commit, Push.

7.  Pull request back to upstream.

To use all the changes done I sometimes create a ``beta`` branch and merge all the changes to there.

That way future updates are easy to do:

1.  Pull changes from ``upstream`` remote into (submu) ``master`` branch.
2.  Merge changes from ``master`` into your branches.
 