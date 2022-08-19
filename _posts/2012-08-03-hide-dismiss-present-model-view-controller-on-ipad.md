---
title: "Hide / Dismiss Present Model View Controller on iPad"
date: "2012-08-03"
tags: 
  - "ios"
  - "ipad"
---

If Base view controller is a View Controller then override this method

```
- (BOOL)disablesAutomaticKeyboardDismissal {
    return NO;
}
```

Be careful if you are displaying the modal with a `UINavigationController`. You then have to set the`disablesAutomaticKeyboardDismissal` on the navigation controller and not on the view controller. You can easily do this with categories.

File: UINavigationController+KeyboardDismiss.h

```
#import <Foundation/Foundation.h>

@interface UINavigationController (KeyboardDismiss)

- (BOOL)disablesAutomaticKeyboardDismissal;

@end
```

File: UINavigationController+KeyboardDismiss.m

```
#import "UINavigationController+KeyboardDismiss.h"

@implementation UINavigationController(KeyboardDismiss)

- (BOOL)disablesAutomaticKeyboardDismissal
{
    return NO;
}

@end
```

Do not forget to import the category in the file where you use the UINavigationController.

Related links:

http://stackoverflow.com/questions/3372333/ipad-keyboard-will-not-dismiss-if-modal-view-controller-presentation-style-is-ui

http://stackoverflow.com/questions/3372333/ipad-keyboard-will-not-dismiss-if-modal-view-controller-presentation-style-is-ui
