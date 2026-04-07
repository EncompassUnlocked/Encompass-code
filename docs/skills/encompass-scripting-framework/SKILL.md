---
name: Field Triggers
description: >
  Use this skill whenever the user wants to create, write, explain, or troubleshoot
  Advanced Coding rules in Encompass (by Ellie Mae / ICE Mortgage Technology). This
  includes Field Triggers, Field Data Entry validation, Milestone Completion conditions,
  Loan Form Printing rules, and Advanced Conditions. Trigger this skill any time the
  user mentions Encompass business rules, triggers, VB.NET in Encompass, custom
  conditions, field rules, or asks how to make a field auto-populate or validate in
  Encompass — even if they don't use the phrase "advanced coding."
---

# Encompass Advanced Coding for Business Rules

This skill helps you write, explain, and troubleshoot Advanced Coding rules in Encompass.
It is written for a mixed audience — Encompass admins who may not have a coding background
as well as more technical users. Plain-English explanations are included alongside code examples.

---

## Background: What Is Advanced Coding?

Encompass has built-in "point and click" business rules for common scenarios (e.g., "apply
this rule only for FHA loans"). When your scenario is more complex — comparing multiple
fields, doing math, or reacting to user input — you use **Advanced Coding** instead.

Advanced Coding is written in **Visual Basic .NET (VB.NET)**. You don't need to be a
full developer, but you do need to follow the syntax carefully. The key concepts:

- **Field references**: Use `[FieldID]` to read a field. Example: `[19]` is Loan Purpose.
- **Numeric fields**: Use `[#FieldID]` to treat a field as a number. Example: `[#1109]` is Loan Amount as a number.
- **Date fields**: Use `[@FieldID]` to treat a field as a date.
- **Custom fields**: Prefix with `CX.` — e.g., `[CX.MYFIELD]`.
- **Setting a field value** (triggers only): `[FieldID] = "value"`

---

## Rule Types That Support Advanced Coding

### 1. Advanced Conditions
Used as the *condition* (the "when does this rule apply?") on any business rule.
Must evaluate to **True or False**.

**Where to find it:** Any business rule → "Is there a condition?" → Yes → Select "Advanced Conditions"

**Syntax examples:**
```vb
' Apply rule only for Purchase loans over $1,000,000 in NY or NJ
[19] = "Purchase" And [#136] > 999999 And ([14] = "NY" Or [14] = "NJ")

' Apply only for loans originated on or after Jan 1, 2010
[@MS.START] > #1/1/2010#

' Different loan amount thresholds by state (CA vs. everywhere else)
IIF([14] = "CA", [#1109] > 200000, [#1109] > 300000)

' Apply when loan amount exceeds $500,000
[#1109] > 500000
```

> **Plain English tip:** Think of this like a filter — the rule only "turns on" when
> your expression is True. If there's any error in the code, the rule is skipped entirely.

---

### 2. Field Data Entry (Validation Rules)
Used to **validate what a user types** into a specific field before saving it.
- Only available when the rule has **no other conditions** (set condition to "No - Always apply").
- Runs *before* the field value is saved.
- Use `Value` to refer to what the user just typed.
- Call `Fail("your message")` to reject the input and show the user a message.

**Where to find it:** Settings → Business Rules → Field Data Entry → New → Value Rule tab → Advanced Coding

**Key functions:**
| Function | What it does |
|----------|-------------|
| `Value` | The text the user just typed into the field |
| `XDec(Value)` | Converts Value to a decimal number safely |
| `Fail("message")` | Rejects the input and shows the message to the user |
| `[#FieldID]` | Reads another field as a number |

**Example — Reject if custom field exceeds loan amount:**
```vb
If XDec(Value) > [#1109] Then
    Fail("This field's value cannot exceed the amount of the loan.")
End If
```

**Example — Different limits by loan program:**
```vb
Select Case [1401]
    Case "5/1 ARM"
        If XDec(Value) > 650000 Then
            Fail("Loan amount cannot exceed $650,000 for a 5/1 ARM.")
        End If
    Case "3/1 ARM"
        If Value <> "" And XDec(Value) < 30000 Then
            Fail("Loan amount must be at least $30,000 for a 3/1 ARM.")
        End If
    Case "30 Year Fixed"
        If XDec(Value) > 450000 Then
            Fail("Loan amount cannot exceed $450,000 for a 30 Year Fixed.")
        End If
End Select
```

**Example — Sum multiple GFE fee fields and compare:**
```vb
Dim sum As Decimal = 0
Dim i As Integer
Dim fieldIds As String() = New String() {"454", "1093", "640", "641", "329", "L228", "L230"}
For i = 0 To fieldIds.Length - 1
    sum = sum + XDec(Fields(fieldIds(i)))
Next
If XDec(Value) > sum Then
    Fail("The discount value cannot exceed the sum of the prepaid charges.")
End If
```

> **Plain English tip:** If your code finishes without calling `Fail()`, Encompass
> considers the value valid and saves it. If any unexpected error occurs, the validation
> *fails automatically* and the user sees a generic error.

---

### 3. Milestone Completion (Advanced Conditions tab)
Used to enforce **extra requirements before a milestone can be completed**.
Uses the same `Fail("message")` pattern as Field Data Entry.

**Where to find it:** Settings → Business Rules → Milestone Completion → New →
Advanced Conditions tab → Add

**Example — Require 2+ years at current address before completing Qualification:**
```vb
If [#FR0112] < 2 Then
    Fail("At least 2 years of residence is required to complete the milestone.")
End If
```

---

### 4. Loan Form Printing (Print Suppression)
Used to **block printing a form** until certain conditions are met.
Same `Fail()` pattern applies.

**Where to find it:** Settings → Business Rules → Loan Form Printing → New →
Print Form Suppression Rule → Advanced Coding tab

---

### 5. Field Triggers
Triggers **run automatically after a field value changes** and can read/write other
fields in the loan. This is the most powerful rule type.

**Where to find it:** Settings → Business Rules → Field Triggers → New →
Action section → "Run advanced code"

**Trigger code receives three parameters automatically:**
| Parameter | Meaning |
|-----------|---------|
| `FieldID` | The ID of the field that was just changed |
| `PriorValue` | What the field contained before the change |
| `NewValue` | What the field was just changed to |

**Key concepts unique to triggers:**

- **Setting a field:** `[FieldID] = "new value"` (same bracket notation, but on the left side)
- **IgnoreValidationErrors:** Add this as the first line to bypass field-level restrictions
  that might block your trigger from writing to certain fields.
- **No infinite loops:** If a trigger changes a field that would re-fire the same trigger,
  Encompass automatically prevents it from running a second time.

**Example — Set Broker Company Name based on property state:**
```vb
If [14] = "CA" Then
    [315] = "California Mortgage Corp"
ElseIf [14] = "NV" Then
    [315] = "Nevada Home Lending"
Else
    [315] = "US Mortgages"
End If
```

**Example — Calculate a custom profit field, double it for refis:**
```vb
IgnoreValidationErrors
[CX.MYPROFIT] = [#1109] * [#3] * 0.02 / 100.0
If [19] = "NoCashOutRefi" Then
    [CX.MYPROFIT] = 2 * [#CX.MYPROFIT]
End If
```

**Example — Use a document receipt date to set a custom field:**
```vb
If [@Document.DateReceived.Credit Report] > #3/15/2010# Then
    [CX.REPORTDATE] = [Document.DateReceived.Credit Report]
End If
```

---

## Working with Milestones, Tasks, and Documents in Triggers

When you need to check or update things beyond fields (milestones, tasks, documents),
use these built-in functions:

**Milestone functions:**
| Function | What it does |
|----------|-------------|
| `Milestones.IsComplete("Name")` | Returns True if that milestone is done |
| `Milestones.SetComplete("Name")` | Marks the milestone as complete |

**Task functions:**
| Function | What it does |
|----------|-------------|
| `Tasks.Exists("Title")` | True if the task exists in the loan |
| `Tasks.IsComplete("Title")` | True if the task is marked complete |
| `Tasks.SetComplete("Title", True)` | Marks the task complete |

**Document functions:**
| Function | What it does |
|----------|-------------|
| `Documents.Exists("Title")` | True if the document is in the loan |
| `Documents.IsReceived("Title")` | True if the document has been received |
| `Documents.IsOrdered("Title")` | True if the document has been ordered |
| `Documents.IsExpired("Title")` | True if the document has expired |

**Example — Mark a task complete when a document is received:**
```vb
If Documents.IsReceived("Credit Report") Then
    Tasks.SetComplete("Pull borrower credit")
End If
```

---

## Common Gotchas

- **Don't use MsgBox()** or any pop-up UI functions — Encompass runs rules in non-interactive
  contexts (like API calls) and the application will hang. Use `Fail()` for messages instead.
- **Value is always a string** — even for number fields. Always use `XDec(Value)` before
  doing math on it.
- **Virtual field IDs are read-only** — you can read `[Document.DateReceived.Credit Report]`
  but cannot assign to it. Use the Document/Task/Milestone functions to make changes.
- **Errors = rule skipped** — for conditions, any code error causes the rule to be ignored.
  For validation rules, errors cause the validation to fail.

---

## Quick Reference: Common Field IDs

| Field ID | Description |
|----------|-------------|
| 14 | Subject Property State |
| 19 | Loan Purpose |
| 136 | Purchase Price |
| 315 | Broker Company Name |
| 1109 | Loan Amount |
| 1401 | Loan Program |
| 3 | Interest Rate |
| FR0112 | Years at Present Address |
| MS.START | File Started Date |
| CX.* | Custom Fields (prefix) |