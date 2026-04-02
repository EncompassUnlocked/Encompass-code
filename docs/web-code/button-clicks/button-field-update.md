# Button: Field update

A simple button click script that writes a value directly into an Encompass 
field. When clicked, the button sets a specified field to a predetermined 
value and saves it to the loan — no manual data entry required.

## How it works

**Retrieves the loan object** — The script calls 
`elli.script.getObject("loan")` to get a reference to the current loan file, 
which provides access to reading and writing loan field values.

**Sets the field value** — Using the `.setFields()` method, the script writes 
a value directly into the specified field. In this example it writes 
`"Friday"` into the custom field `CX.PING`.

## The code
```javascript
async function Button2_click(ctrl) {
  // Retrieve the current loan object from Encompass
  const loan = await elli.script.getObject("loan");
  // Set the custom field CX.PING to "Friday"
  await loan.setFields({ "CX.PING": "Friday" });
}
```

## How to use it

1. In Encompass, open your web form in the form builder
2. Add a button and name it `Button2`
3. Paste this code into the button's click event handler
4. Replace `CX.PING` with your own field ID
5. Replace `"Friday"` with the value you want to write into the field
6. Save and test by clicking the button on a live loan

## Notes

- `CX.PING` is an example custom field — replace it with your own field ID 
  from your Encompass environment. Custom fields always start with `CX.`
- The value being written can be any string, number, or date your field accepts
- You can set multiple fields at once by adding more entries inside 
  `setFields({ })` separated by commas — for example: 
  `{ "CX.FIELD1": "Value1", "CX.FIELD2": "Value2" }`
- The button name `Button2` can be changed to match your form's button name 
  — just update the function name to match