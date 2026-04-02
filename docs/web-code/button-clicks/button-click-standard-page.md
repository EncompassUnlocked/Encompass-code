
# Button: Navigate to Settlement Service Provider List

When triggered, this button instantly navigates the user to the Settlement 
Service Provider List — a standard built-in form within Encompass — eliminating 
the need to manually search through menus.

## How it works

**Retrieves the application object** — The script calls 
`elli.script.getObject("application")` to get a reference to the current 
Encompass application session. This object acts as the controller for UI-level 
actions such as navigation.

**Navigates to a standard form** — Using the `.navigate()` method, the script 
directs Encompass to open the Settlement Service Provider List form using two 
key parameters:

- `target` — The name of the destination form
- `type` — Specifies this is a built-in Encompass form (`STANDARD_FORM`)

## The code

```javascript
async function Button17_click(ctrl) {
  // Retrieve the current application object from Encompass
  const appObj = await elli.script.getObject("application");
  // Navigate to the Settlement Service Provider List standard form
  await appObj.navigate({
    "target": "Settlement Service Provider List",
    "type": "STANDARD_FORM"
  });
}
```

## How to use it

1. In Encompass, open your web form in the form builder
2. Add a button and name it `Button17`
3. Paste this code into the button's click event handler
4. Save and test by clicking the button on a live loan

## Notes

- This navigates to a **standard built-in Encompass form** — no custom form ID needed
- To navigate to a different standard form, simply change the `target` value to 
  the exact name of the form you want
- The button name `Button17` can be changed to match your form's button name — 
  just update the function name to match
```

