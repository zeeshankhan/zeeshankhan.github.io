---
title: "Hidden Features of Xcode 4"
date: "2012-01-31"
tags: 
  - "shortcuts"
  - "xcode"
---

Hi All, I was exploring Xcode 4 features and i found some pretty stuff to share. Below is the list of Xcode features which may help you with writing Cocoa/Cocoa touch code. You can find the discussion on [Stack Over Flow](http://stackoverflow.com/questions/5410191/hidden-features-of-xcode-4).

1\. Pragma mark: It provides nice visual grouping in the Xcode class dropdown list. [More on Pragma](http://cocoasamurai.blogspot.in/2006/09/tip-pragma-mark-organizing-your-source.html)

Syntax: #pragma mark - <name>

[![](images/1.png "Pragma Mark Example")](http://izeeshan.files.wordpress.com/2012/01/1.png)

2\. You can double-click tabs in Xcode 4 to rename them, then use named tabs in Prefs > Behaviors

3\. Some useful short cuts:

- cmd\-0 to hide the Navigator Pane on left.
- cmd\-\[1..n\] switch between Navigators Pane on left.
- cmd\-opt\-0 to hide the Utilities Pane on right.
- cmd\-opt\-\[1..n\] switch between Utilities Pane on right.
- cmd-shift-y to show/hide Debug area on bottom.
- cmd-shift-1 to open Welcome page of Xcode.
- cmd-shift-2 to open the Organizer window.
- shift\-cmd\-opt click on a symbol lets you tell Xcode _exactly_ where in the UI you want the navigated-to file to open.
- ctrl\-6 pops down within-file segment of jump bar (enables keyboard-controlled jump to methods)
- cmd\-{/} select previous / next tab
- cmd\-J navigate to any editor (but why oh why doesn't TAB then cycle between them?)

**Editor ShortCuts**

- Cmd + Ctrl + up/down Jump between .h & .m Files.
- Cmd + Ctrl + Left/Right Jump between Previous Edited Files/Next Edited files.

**Layout Shortcuts**

- Cmd + 0 hide or show navigators
- Cmd + opt + 0 hide or show inspectors
- Cmd + Shift + Y hide or show debugger console.

**Navigators Shortcuts**

- Cmd + 1 Project
- Cmd + 2 Symbol
- Cmd + 3 Search
- Cmd + 4 Issue
- Cmd + 5 Debug
- Cmd + 6 BreakPoint
- Cmd + 7 Log

**Tab Switching & Jumping**

- Cmd + { Previous Tab
- Cmd + } Next Tab
- Cmd + j Select location to jump
- Shift + Option + ClickFile Select location to open file
- Cmd + Alt + Option + Click Select location to open Method

**Inspectors**

- Cmd + Opt + 1 File Inspector
- Cmd + Opt + 2 Quick Help
- Cmd + Opt + 3 Identity Inspector
- Cmd + Opt + 4 Attribute Inspector
- Cmd + Opt + 5 Size inspector
- Cmd + Opt + 6 Connection Inspector

**Build Related**

- Cmd + B Build
- Cmd + Shift + K Clean
- Cmd + . top

**Code Folding**

- Cmd + Alt + Left Minimize Current Method/Class/Interface
- Cmd + Alt + Right Expand Current Method/Class/Interface
- Cmd + Alt + Shift + Left Minimize All Method/Class/Interface
- Cmd + Alt + Shift + Right Expand All Method/Class/Interface
