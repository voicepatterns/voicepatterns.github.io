

## Modularization
A significant amount of user intents do not interact with one another. We likely do want some intents that are global.
Challenge: We will need to define (and implement) what happens when a user is interacting with a module and an intent from another module gets triggered (because the underlying recognition system is not context aware).

Q: Is there anything else to modularization than just chained branching?

