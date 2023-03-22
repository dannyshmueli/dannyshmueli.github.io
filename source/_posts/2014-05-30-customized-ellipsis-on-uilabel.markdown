---
layout: post
title: "Customized Ellipsis on UILabel"
date: 2014-05-30 22:25:55 +0300
comments: true
categories: ios
---
_Ellipsis (plural ellipses) is a series of dots that usually indicates an intentional omission of a word, sentence, or whole section from a text without altering its original meaning._[1](http://www.thefreedictionary.com/ellipsis)

In UILabel ellipsis '...' show up when text is to long to fit in label frame:
 
![alt text](/images/posts/truncated-label-31.5.14.png  =400x)

Currenlty in UILabel there isn't an easy way to customize the ellipses and change them to something like in Appstore reviews:

![alt text](/images/posts/custom-ellipsis-on-appstore-30.5.14.png  =400x)
<!-- more -->
###Introducing DSExpandingLabelWithCustomEllipsis:

Inspired by an answer on [Stackoverflow](http://stackoverflow.com/a/15118452/207682) - a) Store the full text for later expanding b) Trim the full text so it will fit in the required number of lines while considering the custom ellipsis. 

DSExpandingLabelWithCustomEllipsis attributes:

1. Using attributed text, for really custom ellipses.
2. Finds the trimed text length using binary search.
3. Does not use deprecated `sizeWithFont`
4. Simple to use.

Usage:

```objectivec
self.myExpandingLabel.customEllipsisAttributedText = customEllipsis;
self.myExpandingLabel.attributedText = loremIpsum;
self.myExpandingLabel.didExpandBlock = ^(){
    NSLog(@"did tap label");
};
[self.myExpandingLabel setTruncatingForNumberOfLines:2];
```

Check it out on Github: [DSExpandingLabelWithCustomEllipsis](https://github.com/dannyshmueli/DSExpandingLabelWithCustomEllipsis) 
