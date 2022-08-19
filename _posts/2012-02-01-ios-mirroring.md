---
title: "iOS Mirroring"
date: "2012-02-01"
tags: 
  - "ios-mirroring"
  - "iphone-projector"
---

Requirement: ——————— The prospect is trying to create an iOS app which will MIRROR the iPhone, iPad content using the pico projector and also be able to control the projector ( on the projector, increase volume etc). Can you please get a feasibility check done if this is possible and if so how.

Client is looking to create an iOS application for PICO projectors. Refer http://www.brookstone.com/pocket-projector-for-iphone-4

Requirement Highlights: ————————————— 1. Mirror the iPhone, iPad content using the pico projector. 2. Control the projector ( on the projector, increase volume etc).

Findings for solution/feasibility ———————————————— To mirror iPhone screen: Jailbreaked iOS and use Veency, iDemo or screensplitr http://www.youtube.com/watch?v=wjPZzCZWxAI

To output to external device from your application: Implement External Display Support in your app in iOS 3.2, 4.0

External Display Support - Introduced in iOS 3.2 (Supports iPhone/iPod touch from iOS 4.0) ————————————— http://developer.apple.com/library/ios/#releasenotes/General/WhatsNewIniPhoneOS/Articles/iPhoneOS3\_2.html

In iOS 3.2 we have UIScreen class object that provides support for retrieving screen objects for external display through a set of supported cables.

Windows (represented by the UIWindow class) can now be assigned to a specific screen.

Apple VGA Adapter - (It has limitations, read reviews) ————————————— http://store.apple.com/us/product/MC552ZM/B#overview

AirPlay - Introduced with iOS 4.2 ———— AirPlay is a technology that lets your application stream AUDIO to Apple TV and to third-party AirPlay speakers and receivers.

AirPlay Video Support - iOS 4.3 ——————————— This support allows you to play video-based content on AirPlay–enabled hardware such as Apple TV.

AirPlay Improvements - iOS 5.0 ——————————— AirPlay lets users stream audio and video from an iOS-based device to AirPlay–enabled devices such as televisions and audio systems.

Note: In iOS 5, AirPlay is enabled by default for all objects that support it. If you do not want your application’s content to be playable over AirPlay, you must opt out explicitly.

iOS: About Apple AV Adapter compatibility ————————————— http://support.apple.com/kb/ht4108

Stack Overflow: ———————— http://stackoverflow.com/questions/3289147/possible-to-mirror-iphone-ipad-screen-on-a-monitor-without-jailbreaking

Available External Device: ————————————— Optima Pocket Project

http://www.optoma.co.uk/picoconsumer.aspx?PC=PK120&ShowMenu=Y&PTypeDB=Pico

http://www.optoma.co.uk/picoconsumer.aspx?PC=PK201&ShowMenu=Y&PTypeDB=Pico

Apple store link: http://store.apple.com/us/product/H1713ZM/A

1\. http://www.youtube.com/watch?v=fROxb6WdsS0 2. http://www.youtube.com/watch?v=\_kO-pN5Ccfs

Apple Q/A link: http://store.apple.com/us/questions/product/H1713ZM/A

http://www.core77.com/blog/videos/optoma\_pico\_pocket\_projector\_for\_iphoneipod\_live\_at\_the\_apple\_store\_today\_13655.asp

Other Videos and link: ———————————

http://picoprojector.org/projectors/optoma-pk102/

Pocket Projector: http://www.youtube.com/watch?v=vqqxB\_r7VoQ Store: http://store.apple.com/us/product/H1713ZM/A Queries: http://store.apple.com/us/questions/product/H1713ZM/A

Mini Projector: http://www.youtube.com/watch?v=bqRfNq-qydc&feature=related

http://www.youtube.com/watch?v=uNm3Fe6AJX8&feature=BFa&list=ULliMqQmpgSKY&lf=mfu\_in\_order

http://www.youtube.com/watch?v=wjPZzCZWxAI

Release note of all the iOS ——————————————— http://developer.apple.com/library/ios/#releasenotes/General/WhatsNewIniPhoneOS/Introduction/Introduction.html

Some code stuff —————————— http://blog.appideas.net/using-iphone-video-output-to-demo-your-apps-o
