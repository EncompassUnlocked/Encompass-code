# Button: Conditional field color

A button click script that does two things at once — writes a value into 
an Encompass field AND changes the color of that field on the form. This is 
useful for visually flagging important fields or drawing attention to updated 
values.

## How it works

**Sets a color constant** — The script defines a salmon color using its 
hex code `#FA8072`. This makes it easy to change the color in one place 
if needed.

**Retrieves the loan object and updates the field** — Using 
`elli.script.getObject("loan")` and `.setFields()`, the script writes a 
value directly into the specified field — in this example `"Friday"` into 
`CX.PING`.

**Retrieves the form element and applies the color** — Using 
`elli.script.getObject()` the script gets a reference to the visual form 
control on screen and calls `.color()` to change its background color to 
salmon.

## The code
```javascript
async function Button2_click(ctrl) {
  const SALMON_COLOR = "#FA8072";
  // Set the loan field value
  let loan = await elli.script.getObject("loan");
  await loan.setFields({ "CX.PING": "Friday" });
  // Set the color of the form control
  // Replace "Ping" with your actual control ID
  let formElement = await elli.script.getObject("Ping");
  await formElement.color(SALMON_COLOR);
}
```

## How to use it

1. In Encompass, open your web form in the form builder
2. Add a button and name it `Button2`
3. Paste this code into the button's click event handler
4. Replace `CX.PING` with your own field ID
5. Replace `"Friday"` with the value you want to write into the field
6. Replace `"Ping"` with your actual form control ID
7. Replace `#FA8072` with any hex color code you prefer
8. Save and test by clicking the button on a live loan

## Notes

- `CX.PING` is an example custom field — replace it with your own field ID 
  from your Encompass environment. Custom fields always start with `CX.`
- The form control ID (in this example `"Ping"`) is the name of the control 
  on your web form — this is set in the form builder and may be different 
  from the field ID
- Any standard hex color code works — for example `#FF0000` for red, 
  `#FFFF00` for yellow, `#90EE90` for light green
- The field value update and color change happen together in one button click
- The button name `Button2` can be changed to match your form's button name 
  — just update the function name to match