---
layout: post
title: "Storyboard problems and How Tool By Square (codegenutils) Solves Some"
date: 2014-02-22 16:15:54 +0200
comments: true
categories: [ios, xcode] 

---
[Storyboards](https://developer.apple.com/library/ios/documentation/general/conceptual/Devpedia-CocoaApp/Storyboard.html) are great to use, save time and allows developers to visualise the whole (or part) of app, in terms of screen relationships.

####Problem: Storyboards fail at runtime, note compile time
<!-- more -->
Using storyboards means also accessing storyboards created items programmatically. Such as:  
1. Segue identifiers.  
2. Cell reuse identifiers.  
3. View Controllers identifiers.  
When changing an identifier in storyboard, developer should also change the identifier in the code.
This is prone to error.

####Solution: Auto Generated Constants from Storyboard 
[Square](http://squareup.com) have [recently wrote](http://corner.squareup.com/2014/02/objc-codegenutils.html) in [Square Engineering Blog](http://corner.squareup.com) about a new tool they have released: [objc-codegenutils](http://github.com/square/objc-codegenutils). and specifically: `objc-identifierconstants`.

This "generates string identifiers into-compiler checked constants". AKA a file for every storyboard that contains:
 `NSString *const <StoryBoardFileName>MySegueIdentifier` or:
 `NSString *const <StoryBoardFileName>MyCellIdentifier`.
 
Using constants avoid typos.


####How:
 
1. Clone the project at [objc-codegenutils](http://github.com/square/objc-codegenutils).
2. Open the cloned project.
3. Choose `identifierconstants` as target:  
![alt text](/images/posts/codegen-22.2.14.png  =300x)
4. Build and find the built product:  
![alt text](/images/posts/show-in-finder-22.2.14.png  =300x) 
5. Copy  `objc-identifierconstants` into your project. (I place under  `<ProjectRoot>/objc-codegen-utils/`).
6. Add it as a run script on every build:
   
		objc-codegen-utils/objc-identifierconstants -o ${SRCROOT}/Classes/GeneratedIdentifiers/ ${SRCROOT}/Classes/en.lproj/MainStoryboard.storyboard
I output the files into `${SRCROOT}/Classes/GeneratedIdentifiers/` and `${SRCROOT}/Classes/en.lproj/MainStoryboard.storyboard` is the path to my storyboard file.
7. Build your project and add the generated files to the project.
8. Replace string identifiers with new constants.

No more typos.