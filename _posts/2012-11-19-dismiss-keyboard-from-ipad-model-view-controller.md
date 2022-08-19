---
title: "Dismiss Keyboard From iPad Model View Controller"
date: "2012-11-19"
categories: 
  - "technical-posts"
tags: 
  - "ios"
  - "ipad"
  - "view-controllers"
---

When you have a view controller under navigation controller presented as model view then you have to add a method (given below) into navigation controller through categories.

\- (BOOL)disablesAutomaticKeyboardDismissal {
    return NO;
  }
