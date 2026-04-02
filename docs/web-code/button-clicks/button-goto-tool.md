# Button: Goto tool — navigate to Conversation Log

A convenience shortcut button that instantly navigates the user to the 
Conversation Log — a built-in Encompass tool used to track and record 
borrower and loan-related communications — eliminating the need to manually 
search through menus.

## How it works

**Retrieves the application object** — The script calls 
`elli.script.getObject("application")` to get a reference to the current 
Encompass application session, which provides access to UI-level actions 
such as navigation.

**Navigates to the Conversation Log** — Using the `.navigate()` method, 
the script directs Encompass to open the Conversation Log using two key 
parameters:

- `target` — The name of the destination (`"Conversation Log"`)
- `type` — Specifies this as a built-in Encompass tool (`"STANDARD_TOOL"`)

## The code
```javascript
async function Button6_click(ctrl) {
  // Retrieve the current application object from Encompass
  const appObj = await elli.script.getObject("application");
  // Navigate to the Conversation Log standard tool
  await appObj.navigate({
    "target": "Conversation Log",
    "type": "STANDARD_TOOL"
  });
}
```

## How to use it

1. In Encompass, open your web form in the form builder
2. Add a button and name it `Button6`
3. Paste this code into the button's click event handler
4. Save and test by clicking the button on a live loan

## Use case

This is especially useful for loan officers, processors, or other Encompass 
users who frequently log borrower interactions and need quick access during 
an active loan file.

## Notes

- This script uses `STANDARD_TOOL` as the navigation type — this is 
  different from `STANDARD_FORM` used in form-based navigation scripts
- To navigate to a different tool, change the `target` value to the exact 
  name of the tool you want
- The button name `Button6` can be changed to match your form's button name 
  — just update the function name to match