\---

name: field-triggers

description: >

&#x20; Use this skill whenever the user wants to create, write, explain, or troubleshoot

&#x20; Advanced Coding rules in Encompass (by Ellie Mae / ICE Mortgage Technology). This

&#x20; includes Field Triggers, Field Data Entry validation, Milestone Completion conditions,

&#x20; Loan Form Printing rules, and Advanced Conditions. Trigger this skill any time the

&#x20; user mentions Encompass business rules, triggers, VB.NET in Encompass, custom

&#x20; conditions, field rules, or asks how to make a field auto-populate or validate in

&#x20; Encompass — even if they don't use the phrase "advanced coding."

\---



\# Encompass Advanced Coding for Business Rules



This skill helps you write, explain, and troubleshoot Advanced Coding rules in Encompass.

It is written for a mixed audience — Encompass admins who may not have a coding background

as well as more technical users. Plain-English explanations are included alongside code examples.



\---



\## Background: What Is Advanced Coding?



Encompass has built-in "point and click" business rules for common scenarios (e.g., "apply

this rule only for FHA loans"). When your scenario is more complex — comparing multiple

fields, doing math, or reacting to user input — you use \*\*Advanced Coding\*\* instead.



Advanced Coding is written in \*\*Visual Basic .NET (VB.NET)\*\*. You don't need to be a

full developer, but you do need to follow the syntax carefully. The key concepts:



\- \*\*Field references\*\*: Use `\[FieldID]` to read a field. Example: `\[19]` is Loan Purpose.

\- \*\*Numeric fields\*\*: Use `\[#FieldID]` to treat a field as a number. Example: `\[#1109]` is Loan Amount as a number.

\- \*\*Date fields\*\*: Use `\[@FieldID]` to treat a field as a date.

\- \*\*Custom fields\*\*: Prefix with `CX.` — e.g., `\[CX.MYFIELD]`.

\- \*\*Setting a field value\*\* (triggers only): `\[FieldID] = "value"`



\---



\## Rule Types That Support Advanced Coding



\### 1. Advanced Conditions

Used as the \*condition\* (the "when does this rule apply?") on any business rule.

Must evaluate to \*\*True or False\*\*.



\*\*Where to find it:\*\* Any business rule → "Is there a condition?" → Yes → Select "Advanced Conditions"



\*\*Syntax examples:\*\*

\\`\\`\\`vb

' Apply rule only for Purchase loans over $1,000,000 in NY or NJ

\[19] = "Purchase" And \[#136] > 999999 And (\[14] = "NY" Or \[14] = "NJ")



' Apply only for loans originated on or after Jan 1, 2010

\[@MS.START] > #1/1/2010#



' Different loan amount thresholds by state (CA vs. everywhere else)

IIF(\[14] = "CA", \[#1109] > 200000, \[#1109] > 300000)



' Apply when loan amount exceeds $500,000

\[#1109] > 500000

\\`\\`\\`



\---



\### 2. Field Data Entry (Validation Rules)

Used to \*\*validate what a user types\*\* into a specific field before saving it.



\- Only available when the rule has \*\*no other conditions\*\*.

\- Runs \*before\* the field value is saved.

\- Use `Value` to refer to what the user just typed.

\- Call `Fail("your message")` to reject the input and show the user a message.



\*\*Where to find it:\*\* Settings → Business Rules → Field Data Entry → New → Value Rule tab → Advanced Coding



\*\*Example — Reject if custom field exceeds loan amount:\*\*

\\`\\`\\`vb

If XDec(Value) > \[#1109] Then

&#x20;   Fail("This field's value cannot exceed the amount of the loan.")

End If

\\`\\`\\`



\---



\### 3. Milestone Completion

Used to enforce \*\*extra requirements before a milestone can be completed\*\*.



\*\*Example:\*\*

\\`\\`\\`vb

If \[#FR0112] < 2 Then

&#x20;   Fail("At least 2 years of residence is required to complete the milestone.")

End If

\\`\\`\\`



\---



\### 4. Loan Form Printing

Used to \*\*block printing a form\*\* until certain conditions are met.



\*\*Where to find it:\*\* Settings → Business Rules → Loan Form Printing → Advanced Coding tab



\---



\### 5. Field Triggers

Triggers \*\*run automatically after a field value changes\*\* and can read/write other fields.



\*\*Key concepts:\*\*

\- `FieldID` — the field that changed

\- `PriorValue` — what it was before

\- `NewValue` — what it changed to

\- `IgnoreValidationErrors` — bypasses field restrictions when writing values

\- No infinite loops — Encompass prevents a trigger from firing itself recursively



\*\*Example — Set Broker Company Name by state:\*\*

\\`\\`\\`vb

If \[14] = "CA" Then

&#x20;   \[315] = "California Mortgage Corp"

ElseIf \[14] = "NV" Then

&#x20;   \[315] = "Nevada Home Lending"

Else

&#x20;   \[315] = "US Mortgages"

End If

\\`\\`\\`



\*\*Example — Calculate custom profit field:\*\*

\\`\\`\\`vb

IgnoreValidationErrors

\[CX.MYPROFIT] = \[#1109] \* \[#3] \* 0.02 / 100.0

If \[19] = "NoCashOutRefi" Then

&#x20;   \[CX.MYPROFIT] = 2 \* \[#CX.MYPROFIT]

End If

\\`\\`\\`



\---



\## Milestone, Task \& Document Functions



| Function | What it does |

|----------|-------------|

| `Milestones.

