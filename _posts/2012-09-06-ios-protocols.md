---
title: "iOS Protocols"
date: "2012-09-06"
categories: 
  - "technical-posts"
tags: 
  - "ios"
---

##### **Protocols** in Objective C is a Class from which structure is inherited, not implementation.

A protocol is means to define a list of required and/or optional methods that a class implements. If a class adopts a protocol, it must implement all required methods in the protocols it adopts. Since Objective-C does not support multiple inheritances, you can achieve similar functionality with protocols, as a class can adopt more than one protocol. A good example of a protocol is _NSCoding_, which has two required methods that a class must implement. This protocol is used to enable classes to be encoded and decoded, that is, archiving of objects by writing to permanent storage.

##### Protocol in Objective C is like an Interface in Java.

##### Some default protocols:

- UIAlertViewDelegate,
- UIActionSheetDelegate,
- UIImagePickerControllerDelegate,
- UIWebViewDelegate,
- Table View DataSource etc.
