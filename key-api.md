Developers will mostly want to call methods on one of a few classes: 
1. `violet` to access the conversation engine/model and the contained elements,
2. conversational `element` to change parameters of elements and make modifications, and
3. `response` at run-time when a user request comes in, and

## Violet

Builds the conversational model based on a given script or JavaScript code. Main methods:
- `addInputTypes`: to declare parameter types for the conversation
- `respondTo` & `defineGoal`: add top-level intent and goal definitions
- `addScript`: define a script in the conversational language
- `add` [Decision, Dialog, etc]: add conversational element 
- `get`: get conversational element

## Response

Runtime access when an intent is triggered. Main methods:
- `get` and `set`: access conversation variables 
- `say`, `ask`, and `prompt`: respond back to the user
- `load`, `store`, `update`, and `delete`: access a persistent store based on loaded plugins
- `model`: read-only access to the conversation model, i.e. `violet`
- `runScript`: responds to the user as defined by the given `id`

## Element

Conversational Elements accessed via the conversational model. Main methods:
- `addChild`: adds a child element to the current element
- `addSibling`: adds an element immediately after the current element
- `addConstrainedValue`: adds an alternate value that is used if the given constraint is true (only support for the `say`, `ask`, and `prompt` elements)
- `changeElement`: changes an element type (only support for the `say` element)

