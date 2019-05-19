---
title: Framework Design
---
We want to use *good software engineering principles*. We see this as making it easy for experienced developers who are new to Voice; building a highly modular framework; leveraging software engineering practices recognizable from other domains; using off-the-shelf tools if possible; releasing in an agile way; and providing users with an upgrade path.

We don’t want a user to author anything in a format that is hard to understand. Ideally, conversation builders will be able to get started and have a working conversation from a single file/script by themselves, but also be able to take advantage of an app folder structure for full-fledged projects.


## Design Considerations
1. While humans demand sophisticated conversations, building support for it can have challenges. In particular, if the underlying platforms/voice-recognition-systems cannot take advantage of application state and therefore have recognition errors. An ideal solution would need intent recognition probabilities either being exposed OR need to be able to provide application context to the voice recognition system. A Conversation Engine needs to be implemented to enable the latter to happen.
2. We don't want to just support the lowest common denominator. For example, if Amazon's Devices and API has Display's and Google does not have displays, we want to support Displays and allow applications to gracefully downgrade the experiences as appropriate. This means Platform API support will likely need to be provided through plugins.
3. We need to support run-time conversational needs such as permanent storage of user data, analytics data, and allowing conversations to run on one of many platforms (like Alexa and Google's Dialogflow).
4. It will be good for people to understand what is happening behind the covers. So the core of a Conversation Engine should not be very heavy/thick. It will be good for it to essentially be express plus a little more. Similarly, a CLI is nice but (a) it should not be required, and (b) it is often a crutch to deal with designs that are hard to understand.


## Subsystems
- Platforms ← Alexa, Google, etc
- Component Handler ← implements the components using the state manager
- State Manager ← includes the state machine management and ideally pushes the state down to the platform
- Conversational Object Model (COM)
- Conversational Script Language (CSL) ← XML Script
- Output Manager ← Managing the difference between `say`, `ask`, and other commands that a user might want to use
- Input Manager ← ?Is this really needed?
- CSL Parser
- Embedded Literal Parser ← For any parsing needed by the literals in JS or in CSL

### Plugin Types
- Widgets ← Similar to Angular's components, for example, to do faceted navigation.
- Stores ← To easily query a persistent data store (Postgres, DynamoDB etc)
- Analytics

## Notes
- Putting the debugging tools inside the core client add perhaps more weight to it than might be appreciated by others.



## Modularization
A significant amount of user intents do not interact with one another. We likely do want some intents that are global.
Challenge: We will need to define (and implement) what happens when a user is interacting with a module and an intent from another module gets triggered (because the underlying recognition system is not context aware).

Q: Is there anything else to modularization than just chained branching?
