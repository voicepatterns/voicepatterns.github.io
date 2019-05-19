---
title: Language Elements
---
Voice conversations need to be supported by combining language elements for receiving user input, along with elements for output as well as conversational elements (which track user state). Custom elements (widgets) can also be created and used as needed.

Each element can be given an `id` for accessing later to either extend the conversational model or to allow the flow to jump to the point.


## Input & Output Elements

Voice scripts can communicate to users primarily through the `say` element and can define what phrases could trigger an intent through the `expecting` element. When there are multiple `say` elements, they are concatenated.

A trivial voice script would be:
```xml
<app><choice id="launch">
  <expecting>Hello</expecting>
  <say>Hey</say>
  <say>Good to meet you</say>
</choice></app>
```

The above script, on being triggered, would say: 'Hey. Good to meet you'.

For input, scripts can add to the `expecting` element parameters that would be expected. To do this, they should ideally define the parameter types first. For example, the below lets the user give their name and greets them accordingly:
```javascript
convo.addInputTypes({
  'name': 'firstName',
});

convo.addScript(`<app>
  <expecting>My name is [[name]]</expecting>
  <say>Hey [[name]]</say>
  <say>Good to meet you</say>
</app>`);
```

Supported parameter types include: `firstName`, `lastName`, `number`, `date`, `time`, `phoneNumber`, and `phrase`


For output, beyond the `say` element, scripts can:
- Use the `sayOne` element to describe multiple optional output phrases for the framework to pick one randomly and provide for a variety of responses.
- Use the `ask` element to ask a question to the user, which the framework will present to the user at the end of all the `say` elements.
- Use the `prompt` element to ask one of up to 3 questions to the user, which will be presented together to the user at the end.

Furthermore, for output, there is also a potential to also have elements to support displays and other device-specific directives.


## Conversational Elements

Conversational elements enable controlling the flow of the conversation. Each element can be set as being `framed` or not (default is false).

### Decisions
Most voice applications boil down to the system asking a question, the user making a `decision`, responding back, and the conversation flow going into one of many `choices` or branches.

These can be written as - in JavaScript:
```javascript
convo.addDecision({
  prompt: "What two numbers would you like me to add"
  choices: [
    {
      expecting: "[[NumOne]] and [[NumTwo]]",
      resolve: (response)=>{
        response.say(`The sum of [[NumOne]] and [[NumTwo]] is ${app.add(NumOne, NumTwo)}`);
      }
    },
    //...
  ]
});
```

And using the Conversation Language:
```xml
<decision>
  <prompt>What two numbers would you like me to add</prompt>
  <choice>
    <expecting>[[NumOne]] and [[NumTwo]]</expecting>
    <say>The sum of [[NumOne]] and [[NumTwo]] is [[app.add(NumOne, NumTwo)]]</say>
  </choice>
  <choice>
    <expecting>Cancel</expecting>
    <say>Canceling Addition</say>
  </choice>
  ...
</decision>
```

### Dialogs
While most of a conversation can be defined by its branching nature, there are times when there are multiple inputs needed from a user for a particular task. In web apps, these are often done via forms.

We use `dialog` to enable obtaining data for such a situation. The biggest difference between a dialog and a form is that dialogs are constantly checking (via `resolveCheck`) if they have all the input parameters to `resolve`. Dialogs also define an `expecting` phrase, required parameters (via `redqParam`) which are the default for `resolveCheck` and optional parameters that can assist.

An example of a dialog in JavaScript:

```javascript
convo.addDialog({
  expecting: "What time does the [[airline]] flight arrive {from [[city]]|}"
  resolveCheck: convo.reqdParams
  items: [{
    name: "airline",
    required: true,
    prompt: "What airlines are you looking for the arrival time?",
    expecting: "{Coming on|} [[airline]]"
  },
  {
    name: "city",
    required: true,
    prompt: "What city do you want the flight to be arriving from?",
    expecting: "{From|} [[city]]"
  },
  {
    name: "flightDay",
    required: true,
    prompt: "Are you looking for flights today, tomorrow, or the day after?",
    expecting: "{Looking for|} [[flightDay]]"
  }],
  resolve: (response)=>{
    if (!response.dialog.hasReqdParams()) return;
    flightArrivalTimeSvc.query(response.params, (arrivalTime)=>{
      response.say(`Flight [[airline]] from [[city]] is expected to arrive [[flightDay]] at ${arrivalTime}`);
    });
  }
});
```

And using the Conversation Language:
```xml
<dialog>
  <expecting>What time does the [[airline]] flight arrive {from [[city]]|}</expecting>
  <item name="airline" required>
    <prompt>What airlines are you looking for the arrival time?</prompt>
    <expecting>{Coming on|} [[airline]]</expecting>
  </item>
  <item name="city" required>
    <prompt>What city do you want the flight to be arriving from?</prompt>
    <expecting>{From|} [[city]]</expecting>
  </item>
  <item name="flightDay" required>
    <prompt>Are you looking for flights today, tomorrow, or the day after?</prompt>
    <expecting>{Looking for|} [[flightDay]]</expecting>
  </item>
  <if value="[[response.dialog.hasReqdParams()]]">
    <resolve value="[[flightArrivalTimeSvc.query(response, 'arrivalTime')]]">
      <say>Flight [[airline]] from [[city]] is expected to arrive [[flightDay]] at [[arrivalTime]]</say>
    </resolve>    
  </if>
</dialog>
```

Dialogs can be used anywhere a `choice` is expected.

## Changing Flow

Voice scripts can have the conversational flow `jump` to any other element by adding the target location:
```xml
  <jump target="#id"/>
```

Additionally, a `scriptlet` element does not do anything other than grouping other elements. This element is often used to group a bunch of items that can be jumped to from another location.

## Application Logic

Voice scripts start with a top level `app` element that is used to wait for inputs and provide responses when a script is launched. This element consists of a set of `choice` elements representing a top-level menu. Using an id at the top-level as `launch` is used to respond to the user when the script is launched, and a top-level id of `close` is used to tell the app what phrases should be used to end a conversation session.

For example, a conversational script looks like the below:
```xml
<app>
  <choice id="launch">
    <sayOne>
      <say>Greetings<say>
      <say>How can I help?<<say>
    </sayOne>
  </choice>
  <choice id="close">
    <expecting>Thanks</expecting>
  </choice>
  <choice>
    <expecting>Whats next on my list</expecting>
    <say>You need to call Jack</say>
  </choice>
  ...
</app>
```

By default, apps respond on being launched with one of the following:
* Yes. How can I help?
* Hey. Need me?
* Yup. I am here.

Additionally, the following are used to close conversation sessions by default:
* I am good
* No I am good
* Thanks
* Thank you

Voice scripts access session variables or services provided by the app by providing expressions in the form `[[expr]]`. When needing to process a method call before executing on other elements, the `resolve` element can be used, for example:

```xml
<resolve value="[[svc.methCall()]]">
  <!-- elements that are run after the promise in methCall above returns -->
</resolve>
```

Scripts can call a method to see if it returns a `true` value or not using the `if` tag:
```xml
<if value="[[svc.exists()]]">
  <!-- ... -->
</if>
```

Finally, scripts can check for multiple values using a `check`-`case`-`default` set of elements:
```xml
<check value="[[svc.exists()]]">
  <case value="...">
    <!-- ... -->
  </case>
  <default>
    <!-- ... -->
  </default>
</check>
```


## An Example

Consider the below code that creates a simple voice-based calculator.

We first load the conversation engine, declare input variables and their types, define the service, and then load the conversational script.

```javascript
var convo = require('...');

convo.addInputTypes({
  "NumOne": "NUMBER",
  "NumTwo": "NUMBER",
});

var app = {
  add: (a, b)=>{return parseInt(a)+parseInt(b); },
  subtract: (a, b)=>{return parseInt(a)-parseInt(b); },
  multiply: (a, b)=>{return parseInt(a)*parseInt(b); },
  divide: (a, b)=>{return parseInt(a)/parseInt(b); }
}
convo.addFlowScript('script.cfl', {app});
```

The conversational script becomes:
```xml
<app>
  <choice id="launch">
    <expecting>What can you do</expecting>
    <say>I can add, subtract, multiply, or divide two numbers</say>
  </choice>
  <choice>
    <expecting>I want to add</expecting>
    <say>Sure</say>
    <decision>
      <prompt>What two numbers would you like me to add</prompt>
      <prompt>What would you like me to add</prompt>
      <choice>
        <expecting>[[NumOne]] and [[NumTwo]]</expecting>
        <say>The sum of [[NumOne]] and [[NumTwo]] is [[app.add(NumOne, NumTwo)]]</say>
      </choice>
      <choice>
        <expecting>Cancel</expecting>
        <say>Canceling Addition</say>
      </choice>
    </decision>
  </choice>
  <choice>
    <expecting>I want to subtract</expecting>
    <say>Sure</say>
    <decision>
      <prompt>What two numbers would you like me to subtract</prompt>
      <choice>
        <expecting>[[NumOne]] and [[NumTwo]]</expecting>
        <say>Subtracting [[NumTwo]] from [[NumOne]] gives [[app.subtract(NumOne, NumTwo)]]</say>
      </choice>
      <choice>
        <expecting>Cancel</expecting>
        <say>Canceling Subtraction</say>
      </choice>
    </decision>
  </choice>
  <choice>
    <expecting>I want to multiply</expecting>
    <say>Sure</say>
    <decision>
      <prompt>What two numbers would you like me to multiply</prompt>
      <choice>
        <expecting>[[NumOne]] and [[NumTwo]]</expecting>
        <say>Multiplying [[NumOne]] and [[NumTwo]] gives [[app.multiply(NumOne, NumTwo)]]</say>
      </choice>
      <choice>
        <expecting>Cancel</expecting>
        <say>Canceling Multiplication</say>
      </choice>
    </decision>
  </choice>
  <choice>
    <expecting>I want to divide</expecting>
    <say>Sure</say>
    <decision>
      <prompt>What two numbers would you like me to divide</prompt>
      <choice>
        <expecting>[[NumOne]] and [[NumTwo]]</expecting>
        <say>Dividing [[NumOne]] by [[NumTwo]] gives [[app.divide(NumOne, NumTwo)]]</say>
      </choice>
      <choice>
        <expecting>Cancel</expecting>
        <say>Canceling Division</say>
      </choice>
    </decision>
  </choice>
</app>
```
