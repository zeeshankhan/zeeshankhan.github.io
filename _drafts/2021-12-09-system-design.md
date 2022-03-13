---
title: System Design
date: 2021-12-09 07:18:10 +0400
categories: [Notes]
tags: [system_design]
---

## SOLID Principles

### Single-responsibility principle: 
Every class should have only one responsibility.
### Open–closed 
Software entities should be open for extension, but closed for modification.
### Liskov substitution 
Functions that uses references must be able to use objects of derived classes without knowing it.
### Interface segregation
Many client-specific interfaces are better than one general-purpose interface.
### Dependency inversion 
Depend upon abstractions, not concretions.


## 1. Understand the Problem
## 2. Define the scope
## 3. Identify technical requirements
## 4. Propose a high level design with few components
## 5. Deep dive into one component
## 6. Wrap up

## 1. Understand the Problem
1. Avoid jumping to the conclusion too quickly
2. Evaluate the ability to analyse an incomplete question, identify the dark/hidden areas and ask the right questions. For open ended problems.
    - What are we being asked to design?
    - Who is the user, and how will they use our system?
    - What's the initial number of users? And the expected growth?
    - Are we being given an initial design/wireframes, or should we produce them as well?
    - Are we designing an MVP or final-product?
    - Are we building this from scratch, or can we leverage any existing components? Any existing patterns/architecture we should follow?
    - How big is the team who will implement and maintain our system?
    - Are we expected to design just the mobile application or other parts of the overall system too (e.g. API)?
    - Is it iOS or Android only, or cross-platform? Shall we support smartphones, tablets, both?


## 2. Define the scope
    - Identifying the functional requirement and agreeing on the scope / features of the app. What and what not to support.
    - Define the use case and some features, Input / Output
    - Constraint, Assumptions — Define the boundary of the problem, any scale constraints, features, etc.
    - Examples


## 3. Identify technical requirements
- Networking: RESTFul or not, APIs are ready or to be defined by the front end
    - Client-Server Communication — Define the connection options such as HTTP request, polling, server-side events.
- Security: 
    - Authentication: auth token, validation, 
    - Storing sensitive data: Keychain, refresh token
    - Secure communication: SSL Pinning, HTTPs compliant, TLS encryption.
- Availability - iOS, Android, KmP, iPadOS, macOS, watchOS, carOS
- Scalability, Scale requirements
- Performance and Experience
    - Memory consumptions
    - Battery 
    - Network bandwidth
- Testing
- Error Handling 
- Deployment
- Monitoring
- Resilience (Recovery)
- Persistence

## 4. Propose a high level design with few components
- Wire-frames
- Draw principle systems
- Define basic data entities
    - Data Models — Define data model fields of your objects.
- Primary endpoints - API Design — Define the endpoints for the feature that you are building.
- Propose client architecture
- App Flow (Use cases flow) — Define and walk through the user flow for the feature you are designing.
    - Diagrams
- Performance & Tooling — Define how you will gather data and metrics to see how the app is performing — memory utilization, CPU utilization.
- Accessibility — Make sure you define the accessibility feature and how the app will be made accessible.
- Internationalization — How would you approach as the app grows internationally.
- Security — Define how you would secure the app.

## 5. Deep dive into one component
## 6. Wrap up

## Examples:
Some common questions include:
- Design a URL shortener (e.g. Bitly)
- Design a social media website (e.g. Twitter)
- Design a video watching website (e.g. YouTube)
- Design a chatting service (e.g. Telegram, Slack, Discord)
- Design a file sharing service (e.g. Google Drive, Dropbox)
- Design a ride sharing service (e.g. Uber, Lyft)
- Design a photo sharing service (e.g. Flickr, Pinterest)
- Design an e-commerce website (e.g. Amazon, eBay)
- Design a jobs portal (e.g. LinkedIn, Indeed)
- Design a web crawler (e.g. Google)
- Implement library download feature

## Resources:
[1] https://themobileinterview.com/cracking-the-mobile-system-design-interview/

## Essential Developer Notes
- Sometimes cost of coupling is worst than duplication.
- Avoid shared module because a tiny change in shared module would require to recompile all the module it is being used.

