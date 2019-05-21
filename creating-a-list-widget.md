```javascript
var violet = require('violet');


// constructed (at load-time) and then launched by calling init method: respondWithItems (at run-time)
class ListWidget {

  constructor(id, humanName, humanNamePl) {
    // ...
  }

  // use this in the parent script to define a branch so that the list items can be interacted with
  interactionBranchID() {
    return `interactWith${id}`;
  }

  // override this to customize how to find the name from the results
  getItemNameObj(listItemObj) {
    return listItemObj.value;
  }

  // call to kick off this widget
  respondWithItems(response) {
    var results = response.get('listVar');
    response.runScript('respondWithItems', results); // same as response.get('respondWithItems').run(results);

    if (results.length>3)
      response.runScript(`hearPastThree${id}`);
    response.runScript(interactionBranchID());
  }


  // called by the Widget to lists the next 7 items till the 17th item.
  respondWithMoreItems(response, results, start=0) {
    response.runScript('respondWithMoreItems', results);

    if (results.length>10 && start===0) // we dont speak past 17 cases
      response.runScript(`hearPastTen${id}`);
    response.runScript(interactionBranchID());
  }

  // call from an interaction branch to get the value of the outlet
  getItemFromResults(response, itemNo) {
    if (itemNo) {
      itemNo = parseInt(itemNo)-1;
    }
    if (itemNo === undefined || itemNo === null || itemNo<0 || itemNo>17)
      return response.get('errMgrInvalidItemNo');

    var results = response.get(id);
    if (results === undefined || results === null || !Array.isArray(results))
      return response.get('errMgrNotFoundItems');
    if (results.length<itemNo)
      return response.get('errMgrNotFoundTgtItem');

    return results[itemNo];
  }

  hearMore(start=0) {
    response.say(`Getting more ${humanNamePl}.`);
    var results = response.get(id);
    listWidget.respondWithMoreItems(response, results);
  }

}

var listWidgetCoreScript = `
  <scriptlet id="getItemName" params="ndx, listItemObj, widget.humanName">
    <say>${humanName} ${ndx+1} is ${getItemNameObj(listItemObj)}.</say>
  </scriptlet>
  <scriptlet id="respondWithItems" params="results, humanNamePl">
    <say>I found ${results.length} ${humanNamePl}.</say>
    <say vFor="${results}" vFilter="firstThree">${self.getItemName}</say>
  </scriptlet>
  <scriptlet id="respondWithMoreItems" params="results">
    <say vFor="${results}" vFilter="items(${start}, 10)">${self.getItemName}</say>
  </scriptlet>
  <decision id="hearPastThree${id}" params="humanNamePl, interactionBranchID">
    <prompt>Do you want to hear more ${humanNamePl}?</prompt>
    <choice>
      <expecting>Yes</expecting>
      <expecting>Hear more</expecting>
      <call resolve="self.hearMore"/>
    </choice>
    <choice>
      <expecting>No</expecting>
      <decision id="${interactionBranchID}"/>
    </choice>
  </decision>
  <decision id="hearPastTen${id}" params="humanNamePl">
    <prompt>Do you want to hear more ${humanNamePl}?</prompt>
    <choice>
      <expecting>Yes</expecting>
      <expecting>Hear more</expecting>
      <call resolve="self.hearMore(10)"/>
    </choice>
    <choice>
      <expecting>No</expecting>
      <decision id="${interactionBranchID}"/>
    </choice>
  </decision>
  <scriptlet id="err" params="ndx, listItemObj, self.humanName">
    <say>${humanName} ${ndx+1} is ${getItemNameObj(listItemObj)}.</say>
  </scriptlet>
  <scriptlet id="errMgrNotFoundItems" params="self.humanNamePl">
    <say>Could not find ${humanNamePl}</say>
  </scriptlet>
  <scriptlet id="errMgrNotFoundTgtItem" params="self.humanName, itemNo">
    <say>Could not find ${humanName} ${itemNo}</say>
  </scriptlet>
  <scriptlet id="errMgrInvalidItemNo" params="self.humanName">
    <say>Invalid ${humanName} Number</say>
  </scriptlet>
`;

violet.addWidget({
  name: 'listWidget', 
  script: listWidgetCoreScript, 
  class: ListWidget,
  reqdParams: ['id', 'humanName', 'humanNamePl', 'listVar'],
  childrenBranches: {
    interaction: interactionBranchID
  },
  init: respondWithItems
});
```

Used as:
```xml
<listWidget id="case" humanName="case" humanNamePl="cases" listVar="caseList">
  <interaction>
    ...
  </interaction>
</listWidget>
```