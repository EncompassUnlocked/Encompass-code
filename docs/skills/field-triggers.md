# Field Triggers

Field Triggers are rules in Encompass that automatically run code or apply logic
when a specific field value changes. They allow you to respond to user input in
real time — validating data, updating other fields, showing alerts, or enforcing
business rules without requiring a button click.

---

## What Field Triggers Can Do

- **Auto-populate fields** — when one field changes, fill in related fields automatically
- **Validate input** — check that values are in range or match expected formats
- **Show alerts** — warn users about consequences before they move on
- **Conditionally lock or hide fields** — control the form based on loan state
- **Log changes** — stamp a date or user ID when a key field is updated

---

## How to Use the Field Triggers Skill

This skill is designed to work with Claude AI. Give Claude the field ID you want
to trigger on, describe the logic you want to apply, and the skill will generate
ready-to-use Encompass field trigger code.

**Example prompts:**

- *"When field 4002 changes, check if the value is greater than 100 and alert the user."*
- *"When CX.LOAN.TYPE is set to 'HELOC', set field 1172 to today's date."*
- *"When field 1109 changes, log the change to CX.AUDIT.LOG with a timestamp."*

---

## Skill File

Download and install the Field Triggers skill to use it in Claude Code or Claude.ai:

[Download encompass-field-triggers.skill](../skills/encompass-field-triggers.skill)
