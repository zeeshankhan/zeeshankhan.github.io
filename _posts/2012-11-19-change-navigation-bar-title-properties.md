---
title: "Change Navigation Bar Title Properties"
date: "2012-11-19"
categories: 
  - "technical-posts"
tags: 
  - "ios"
  - "uilabel"
  - "uinavigationbar"
  - "view-controllers"
---

NSDictionary \*dic = \[NSDictionary dictionaryWithObjectsAndKeys:
\[UIFont fontWithName:@"Arial" size:22\],UITextAttributeFont,
\[UIColor whiteColor\], UITextAttributeTextColor,
\[UIColor grayColor\], UITextAttributeTextShadowColor,
\[NSValue valueWithUIOffset:UIOffsetMake(-1, 0)\], UITextAttributeTextShadowOffset,nil\];

self.navigationController.navigationBar.titleTextAttributes = dic;

### **OR**

### Add UILabel to titleView

##### [Apple Doc](http://developer.apple.com/library/ios/#samplecode/NavBar/Introduction/Intro.html "Apple Doc")
