---
title: Github Action for iOS
date: 2021-08-27 13:41:10 +0400
categories: [Swift, CI]
tags: [writing, swift, ci]
---

To start with Github Action we need to have a repo and few test written to verify our build and test command.
First let's create a YML file in the directory `.github/workflows` at the root of your repository.

_There is a very good post on the YML file [here][YML_doc]_

Let's look into the a sample github action workflow.
Below workflow will build and test a Swift Package Manager

```yml
name: Build My Framework #1

on: #2
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs: #3
  build:

    runs-on: macos-latest

    steps:
    - uses: actions/checkout@v2
    - name: Build
      run: swift build -v
    - name: Run tests
      run: swift test -v
```
1. The name of the work flow 
2. Define when the workflow should get triggered. here it will start either on any commit to `main` branch or on a pull request.
3. The actions which we want to perform in the work flow.
    - First we choose the operating system which is `macos-latest` here. [Other virtual environments](https://github.com/actions/virtual-environments)
    - We define the steps or actions in form of `name and then run in next line`
    - `actions/checkout@v2` is a predefined action from [github action marketplace][GithubMarketplace]. we can use any action from here based on our requirement.
    - Later in the post we have used a 3rd party action from marketplace `maxim-lobanov/setup-xcode@v1` from `maxim-lobanov`.

To trigger this work flow we either push a commit to main branch or create a pull request.

## Select Xcode

### Select a specific version of Xcode
```yml
- name: Show the currently detailed version of Xcode for CLI
  run: xcode-select -p

- name: Select Xcode 12.4
  run: sudo xcode-select -s /Applications/Xcode_12.4.app && xcodebuild -version
```

### Select the latest Xcode available
```yml
- uses: maxim-lobanov/setup-xcode@v1
  with:
    xcode-version: latest-stable
```

### Select the latest beta Xcode
```yml
- uses: maxim-lobanov/setup-xcode@v1
    with:
    xcode-version: latest
```

## Install a specific simulator version
This process takes some time (around 15 mins) for installing the pkg

```yml
- name: install simulator
  run: xcversion simulators --install='iOS 14.3'
```

## Prepare the existing simulator for use
Since github action do not load the simulator on its own we need to pre install the main iOS simulators from Xcode runtime to use for xcode build and test.
[Here is the discussion thread on the same.][PrepareSimulator]
It is a workaround with symlink simulators to CoreSimulator directory.

```yml
- name: Prepare iOS 14.4 simulator
  run: |
    sudo mkdir -p /Library/Developer/CoreSimulator/Profiles/Runtimes
    sudo ln -s /Applications/Xcode_12.4.app/Contents/Developer/Platforms/iPhoneOS.platform/Library/Developer/CoreSimulator/Profiles/Runtimes/iOS.simruntime /Library/Developer/CoreSimulator/Profiles/Runtimes/iOS\ 14.4.simruntime
    xcrun simctl list runtimes
    xcrun simctl create iPhone_8 "iPhone 8" "com.apple.CoreSimulator.SimRuntime.iOS-14-4"
    xcrun simctl list devices 14.4
```

## Install dependency (if any)
Here, we will be installing cocoapods.

```yml
- name: Install Cocoapods
  run: gem install cocoapods

- name: Install pods to prepare workspace
  run: pod install
```

## Print current Build settings
```yml
- name: Show Build Settings
  run: xcodebuild -workspace Grocery.xcworkspace -scheme Grocery -showBuildSettings
```

## Print Build SDK
```yml
- name: Show Build SDK
  run: xcodebuild -workspace Grocery.xcworkspace -scheme Grocery -showsdks
```

![SDKs]({{ "/assets/img/post/showSDKs.png" | relative_url }})


## Print available destinations for workspace/schema
```yml
- name: Show Available Destinations
  run: xcodebuild -workspace Grocery.xcworkspace -scheme Grocery -showdestinations
```

![Destinations]({{ "/assets/img/post/showDestinations.png" | relative_url }})

## Perform Clean Build
```yml
- name: clean and build
  run: xcodebuild clean build -workspace Grocery.xcworkspace -scheme Grocery -destination 'platform=iOS Simulator,OS=14.4,name=iPhone 12 mini' -showBuildTimingSummary
``` 

## Perform Clean Test
```yml
- name: build and test
  run: xcodebuild clean test -workspace Grocery.xcworkspace -scheme Grocery -destination 'platform=iOS Simulator,OS=14.4,name=iPhone 8' -showBuildTimingSummary
``` 

## References:
- [Github Action setup][GithubActionDoc]
- [Apple documentation][AppleCommandDocs]
- [Github Marketplace][GithubMarketplace]

[GithubMarketplace]: https://github.com/marketplace
[AppleCommandDocs]: https://developer.apple.com/library/archive/technotes/tn2339/_index.html
[GithubActionDoc]: https://docs.github.com/en/actions 
[YML_doc]: https://alisoftware.github.io/yaml/2021/08/17/yaml-part1-json/
[PrepareSimulator]: https://github.com/actions/virtual-environments/issues/551

<!-- 
*TODO:*
- Define config to read scheme and destination from here https://rhonabwy.com/2020/05/09/continuous-integration-with-github-actions-for-macos-and-ios-projects/ 
- Define Another way to create simulator PR where GithubAction simulator issue is addressed: https://github.com/cashapp/AardvarkCrashReport/pull/2/files?diff=unified&w=1
- Define moving action to separate file https://github.com/GetStream/stream-chat-swift/pull/919/files
- Some more reference https://github.com/heckj/MPCF-TestBench/blob/master/.github/workflows/build.yml
- 
-->