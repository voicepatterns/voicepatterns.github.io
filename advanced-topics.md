Conversational Flow Scripts are expected to be extended to support a few important use cases. While we expect these to happen often, we see them as being orthogonal to focusing on the main items in the user and app responses and therefore the core goal of tracking the conversational flow.

## Experience Level
The temporal nature of voice means that voice scripts need to be careful as to how much is communicated to the user. This will often be to provide detailed information to a user using the app for the first time as opposed to the nth time. While this can be done via app logic, given how often this needs to happen in voice there is explicit support for it.

Support for experience level is provided by voice scripts providing a default amount of information in the script and providing constraints for alternate values, such as:
```javascript
violet.get(id).addConstrainedValue(amountUsedGT, 5, 'shorter value');
```
In the above example, `id` is the id of a `say` element, `amountUsedGT` is an app logic provided function that is called with the parameter 5 and if it returns true then the constrained `shorter value` is used instead of the default.

## Alternate Responses
Natural sounding conversations do not respond to users in the same way every time. This is supported via the `sayOne` element. However, as we often want to focus on the core conversational flow in main voice script, these alternate responses are supported by adding additional code.

 For example, when adding a response to a `sayOne` element:
```javascript
violet.get(id).addSibling('<expecting>Hey<expecting>')
```
Or, when upgrading a `say` element to a `sayOne` element and adding the alternate response:
```javascript
violet.get(id).insertElement('sayOne'); // this adds a sayOne as the given id to replace the say element, adds the say element to the new sayOne, AND removed the id from the say element.
violet.get(id).addChild('<say>How are things</say>');
```

## Goals
A Goal is a lower-level concept used to implement Conversational Elements (such as dialogs and decisions). It packages up common conversational components together while managing states automatically. Triggering a goal prompts users if necessary and waits for a response back from the user. Goals are implemented by adding to the prompt queue and setting the next state so that the goal to handle user responses.

All conversational elements are built on goals to manage states and prompts with Decisions focus on the branching use-case and Dialogs focusing on the multi-parameter input use case.

Only widget designers should need to understand how goals work.