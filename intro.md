---
title: Voice Patterns
---
# Software Patterns for Voice Development

Voice Patterns are software patterns to help address common challenges and scenarios that arise when building voice applications such as skills for Amazon Alexa, or actions for Google Assistant.

The challenge of building great experiences can can be seen as having two parts: firstly enabling the current state-of-the-art when building conversations, and secondly making such best-practices easy to adopt.

## Conversational Design Patterns

Building a Voice App, even more so than building a Web or Mobile App, needs implementation work to be done in different independent modes: the Voice Experience (VUX) - focusing on the UX and Conversational flow; Copywriting - focusing on potential user requests; and Developers adding in the business logic.

Common Voice Experience Design Patterns include supporting the need for...
- joint app+user driven initiative
- supporting constant decisions to be made by the user (and the app)
- getting multiple pieces of information from a user to meet a need
- variety of ways that a user and the app can communicate the same idea
- supporting different experience levels of users with an app

## Framework Design

Such conversational applications can be built using a framework to assist. Given that there are few other solutions for this problem, there is an explicit desire is to gradually figure out the right abstractions for this problem as well as the right API's to ease adoption.

An ideal framework should be designed to provide support for such abstractions as well as support easy adoption by using familiar terms.
