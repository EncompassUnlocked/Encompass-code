# Button: Modal

A button click script that opens an Encompass form inside a modal (popup) 
window. This is useful for displaying additional forms or information 
without navigating away from the current page, keeping the user in context 
while they complete a task.

## How it works

**Retrieves the application object** — The script calls 
`elli.script.getObject("application")` to get a reference to the current 
Encompass application session, which provides access to UI-level actions 
such as opening modals.

**Defines the modal options** — A set of options controls how the modal 
appears and what it displays:

- `type` — Specifies this modal will display a form (`"form"`)
- `size` — Controls the size of the modal window (`"xl"` for extra large)
- `name` — The name of the form to display inside the modal
- `showFooter` — Whether to show the modal footer (`false` hides it)
- `formType` — Specifies this is a built-in Encompass form (`"STANDARD_FORM"`)

**Opens the modal** — Using `.openModal()` the script launches the popup 
window and waits for the user's response, which is stored in `modalResp` 
for optional further use.

## The code
```javascript
async function btnNameGoesHere_click(ctrl) {
  const appObj = await elli.script.getObject("application");
  const modalOptions = {
    'type': 'form',
    'size': 'xl',
    'name': 'NameGoesHere',
    'showFooter': false,
    'formType': 'STANDARD_FORM'
  };
  const modalResp = await appObj.openModal(modalOptions);
}
```

## How to use it

1. In Encompass, open your web form in the form builder
2. Add a button and rename `btnNameGoesHere` to match your button name
3. Paste this code into the button's click event handler
4. Replace `'NameGoesHere'` with the exact name of the form you want 
   to open in the modal
5. Adjust `'size'` if needed — options are `'sm'`, `'md'`, `'lg'`, `'xl'`
6. Save and test by clicking the button on a live loan

## Notes

- `'formType': 'STANDARD_FORM'` means this opens a built-in Encompass form 
  — change this to `'CUSTOM_FORM'` if you want to open one of your own 
  custom forms instead
- Set `'showFooter': true` if you want the standard modal footer buttons 
  (OK/Cancel) to appear
- The `modalResp` variable captures the user's response when they close 
  the modal — this can be used to trigger additional logic based on what 
  the user did inside the modal
- Modal size options from smallest to largest: `'sm'`, `'md'`, `'lg'`, `'xl'`