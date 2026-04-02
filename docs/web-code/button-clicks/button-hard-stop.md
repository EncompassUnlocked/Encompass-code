# Check Lock Status and Stamp Closure Date

This button checks whether a loan is currently locked before stamping today's 
date into a custom field. If the loan is locked, it alerts the user to transfer 
or cancel the lock before proceeding. If the loan is not locked, it records 
today's date in the custom field `CX.CLOSED.FOR.INCOMPLETENESS` and 
recalculates the loan.

## The code
```javascript
async function Button2_click(ctrl) {
  // Retrieve the loan object
  const loan = await elli.script.getObject("loan");
  // Check if the loan is locked (Field 2400)
  const lockField = await loan.getField("2400");
  const isLocked = lockField === true || lockField === "true" || lockField === "Y";
  if (isLocked) {
    // Notify user that the loan is locked and action is required
    alert(
      "Loan is Locked: The lock needs to be transferred to a new loan " +
      "or have the lock cancelled with the Lock Desk and then disposition the file."
    );
  } else {
    // Get today's date formatted as YYYY-MM-DD
    const today = new Date();
    const formattedDate = [
      today.getFullYear(),
      String(today.getMonth() + 1).padStart(2, "0"),
      String(today.getDate()).padStart(2, "0")
    ].join("-");
    // Stamp the date into the custom field and recalculate the loan
    await loan.setFields({ "CX.CLOSED.FOR.INCOMPLETENESS": formattedDate });
    await loan.Calculate();
  }
}
```

## How to use it

1. In Encompass, open your web form in the form builder
2. Add a button to your form and name it `Button2`
3. Paste this code into the button's click event handler
4. Update `CX.CLOSED.FOR.INCOMPLETENESS` to match your actual custom field ID if different
5. Save and test by opening a locked loan and an unlocked loan to verify both paths work

## Notes

## Notes

- Field `2400` is the standard Encompass rate lock field — this should work without changes
- The date is stamped in `YYYY-MM-DD` format — change the `join("-")` separator if your 
  field expects a different format like `MM/DD/YYYY`
- `CX.CLOSED.FOR.INCOMPLETENESS` is an example custom field — replace this with your own 
  custom field ID from your Encompass environment. Custom fields always start with `CX.`
- The `await loan.Calculate()` at the end ensures any dependent fields update immediately