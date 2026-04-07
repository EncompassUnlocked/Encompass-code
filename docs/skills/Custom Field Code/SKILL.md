---
name: encompass-loan-custom-fields
description: >
  Use this skill whenever the user wants to create, write, explain, or troubleshoot
  Loan Custom Field calculations in Encompass (by Ellie Mae / ICE Mortgage Technology).
  This includes arithmetic calculations, date math, text manipulation, IIF branching
  logic, list/range lookups, and safe operators for custom fields. Trigger this skill
  any time the user mentions Encompass custom fields, CX. fields, loan custom field
  calculations, CUST01FV, or asks how to calculate, populate, or derive a value
  in a custom Encompass field — even if they don't use the phrase "loan custom fields."
---

# Encompass Loan Custom Field Calculations

This skill helps you write, explain, and troubleshoot Loan Custom Field calculations
in Encompass. It covers all supported expression types, safe operators, and common pitfalls.

---

## Background: What Are Loan Custom Field Calculations?

Encompass lets you define custom fields (e.g., `CUST01FV` or `CX.MYFIELD`) that
**automatically compute their value** from an expression you write. Unlike Advanced
Coding (VB.NET), custom field calculations use a **dedicated expression engine** with
a simpler syntax — no If/End If blocks, just inline expressions.

> **Note:** These calculations can also be referenced inside Advanced Coding business
> rules by reading the custom field's value.

### How to Set Up a Custom Field Calculation

1. **Encompass → Settings → Loan Setup → Loan Custom Fields**
2. Double-click a pre-defined field (e.g., `CUST01FV`) or click **New** and enter a Field ID
3. Enter a Description and select a **Format** (Integer, Decimal, String, Date, Y/N, etc.)
4. Enter your calculation expression
5. Click **Validate** to check syntax, then **OK**

---

## Referencing Loan Fields

Use square brackets with the field ID to read any loan field:

```
[1109]      → Loan Amount (numeric)
[4000]      → Borrower First Name (text)
[136]       → Purchase Price (numeric)
[CX.MYFIELD] → Another custom field
```

**Field Modifiers** (placed inside brackets before the ID):

| Modifier | Effect | Example |
|----------|--------|---------|
| `#` | Force numeric — returns 0 if blank or non-numeric | `[#1109]` |
| `-` | Force unformatted string (e.g., `200000.00`) | `[-1109]` |
| `+` | Force formatted string (e.g., `200,000.00`) | `[+1109]` |
| `@` | Force date — returns 1/1/1 if not a valid date | `[@1402]` |

> **Important:** An unpopulated numeric field returns an **empty string**, not zero.
> Always use `[#FieldID]` or safe functions when doing math to avoid errors.

---

## Arithmetic Operations

Standard operators with normal order-of-operations. Parentheses supported.

```
0.02 * [1109]
0.02 * ([1109] + [44])
([#1109] - [#136]) / [#136] * 100
```

### Safe Arithmetic (use when fields may be empty)

| Function | Description |
|----------|-------------|
| `Sum(x, y, ...)` | Adds values; blank if any operand is non-numeric |
| `SumAny(x, y, ...)` | Adds values, **skipping** non-numeric ones; blank only if all are non-numeric |
| `Diff(x, y)` | `x - y`; blank if either is non-numeric |
| `Mult(x, y, ...)` | Multiplies; blank if any operand is non-numeric |
| `MultAny(x, y, ...)` | Multiplies, skipping non-numeric ones |
| `Div(x, y)` | `x / y`; blank if either is non-numeric |

**Example — safe vs. unsafe:**
```
Unsafe:  0.02 * ([1109] + [44])
Safe:    Mult(0.02, Sum([1109], [44]))
```

---

## Math Functions

| Function | Description | Example |
|----------|-------------|---------|
| `Abs(x)` | Absolute value | `Abs([101] - [102])` |
| `Min(x, y, ...)` | Smallest value | `Min([102], [103], [104])` |
| `Max(x, y, ...)` | Largest value | `Max([102], [103], [104])` |
| `LMedian(x, y, ...)` | Median (lower of middle two if even count) | `LMedian([#67], [#1450], [#1414])` |
| `UMedian(x, y, ...)` | Median (upper of middle two if even count) | `UMedian([#67], [#1450], [#1414])` |
| `Sqrt(x)` | Square root | `Sqrt(0.02 * [1109])` |
| `Round(x, precision)` | Round to decimal places | `Round([1109], 2)` |
| `Trunc(x, precision)` | Truncate to decimal places | `Trunc([1109], 2)` |
| `Log(x)` | Natural log | `Log(1000 + [910])` |
| `Log10(x)` | Base-10 log | `Log10(1000 + [910])` |
| `Exp(x)` | e^x | `Exp([1171] / 100)` |
| `Pow(x, y)` | x^y | `Pow([1171], 5)` |
| `Sgn(x)` | Sign: 1 / 0 / -1 | `Sgn([1093])` |
| `XInt(x, default)` | Convert to integer (default 0 on failure) | `XInt([1109], 1)` |
| `XDec(x, default)` | Convert to decimal (default 0 on failure) | `XDec([1109], -1)` |

---

## Text (String) Operations

Use `&` for concatenation. Enclose literals in double quotes. To include a literal
double-quote, use two of them (`""`).

```
[4000] & " " & [4002]             → "Joe Smith"
"$" & [+1109] & " USD"            → "$200,000.00 USD"
[4000] & " is the ""primary"" borrower."   → Joe is the "primary" borrower.
```

| Function | Description | Example |
|----------|-------------|---------|
| `Trim(x)` | Remove leading/trailing whitespace | `Trim([4000] & " " & [4002])` |
| `Left(x, n)` | First n characters | `Left([4002], 5)` |
| `Right(x, n)` | Last n characters | `Right([4002], 5)` |
| `Mid(x, start, length)` | Substring (0-based start) | `Mid([4002], 3, 2)` |
| `InStr(x, y)` | Position of y in x (case-sensitive) | `InStr([4002], "mith")` |
| `LCase(x)` | Lowercase | `LCase([4002])` |
| `UCase(x)` | Uppercase | `UCase([4002])` |
| `Replace(x, y, z)` | Replace all y with z in x | `Replace([1264], "Investor", "Lender")` |
| `Int2Text(x)` | Integer to words: "Five Hundred Thirty-Four" | `Int2Text([4])` |
| `Dec2Text(x)` | Decimal to words: "Sixty-Five and Fifty-Three Hundredths" | `Dec2Text([3])` |
| `Money2Text(x)` | Decimal to money words: "Sixty-Five Dollars and Fifty-Three Cents" | `Money2Text([1109])` |

---

## Date Operations

Field format must be DATE type (MONTHDAY fields are not compatible).

| Function | Description | Example |
|----------|-------------|---------|
| `Day(x)` | Day portion of date | `Day([1402])` |
| `Month(x)` | Month portion | `Month([1402])` |
| `Year(x)` | Year portion | `Year([1402])` |
| `DateAdd(period, count, x)` | Add days/months/years; blank on error | `DateAdd("yyyy", 1, [1402])` |
| `XDateAdd(period, count, x)` | Add days/months/years; returns empty (not blank calc) on error | `XDateAdd("m", 3, [1402])` |
| `DateDiff(period, x, y)` | Difference between dates; blank on error | `DateDiff("d", [1402], [1403])` |
| `XDateDiff(period, x, y)` | Difference; returns empty on error | `XDateDiff("d", [1402], [1403])` |
| `XDate(x, default)` | Convert to date (default 1/1/1 on failure) | `XDate([1402], "11/30/2010")` |
| `XMonthDay(x, default)` | Convert to month-day (stored as 2010 date) | `XMonthDay("3/15")` |
| `Today` | Today's date | `DateAdd("d", 7, Today)` |

Period codes: `"d"` = days, `"m"` = months, `"yyyy"` = years

---

## Calendar-Based Operations

Uses your company's compliance calendars (Business, Postal, Reg-Z):

| Function | Description |
|----------|-------------|
| `Calendar.AddBusinessDays(date, count, moveToNext)` | Add business days (per Business Calendar) |
| `Calendar.AddPostalDays(date, count, moveToNext)` | Add postal days (US Postal Calendar) |
| `Calendar.AddRegZBusinessDays(date, count, moveToNext)` | Add Reg-Z business days |

`moveToNext` = `true` advances to next business day if start date is a non-business day.

```
Calendar.AddBusinessDays([763], 5, true)
```

---

## Branching and Logic (IIF)

```
IIF(condition, value_if_true, value_if_false)
```

**Simple example:**
```
IIF([1109] > 100000, 0.02 * [1109], 0.05 * [1109])
```

**With AND/OR:**
```
IIF([1109] > 100000 AND [1335] < 20000, 0.02 * [1109], 0.05 * [1109])
```

**Nested IIF (multiple branches):**
```
IIF([1811] = "PrimaryResidence", 0.05 * [#1109],
IIF([1811] = "SecondHome",       0.02 * [#1109],
IIF([1811] = "Investor",         0.01 * [#1109], 0)))
```

> ⚠️ **IIF evaluates BOTH branches before testing the condition.** If either branch
> throws an error, the whole calculation fails — even if that branch would never be used.
> Use `[#FieldID]` modifiers or safe functions to prevent this.

**Conditional helper functions:**

| Function | Description |
|----------|-------------|
| `IsEmpty(x)` | True if x is the empty string |
| `IfEmpty(x, val)` | Returns x if non-empty, otherwise val |
| `IsNumeric(x)` | True if x can be converted to a number |
| `IsDate(x)` | True if x can be converted to a date |

---

## List and Range Operations

Cleaner alternatives to deeply nested IIF statements:

| Function | Description |
|----------|-------------|
| `Match(x, v0, v1, ...)` | Index of first match; -1 if none |
| `Range(x, v0, v1, ...)` | Index of first value **greater than** x |
| `RangeLow(x, v0, v1, ...)` | Index of first value **≥** x |
| `Pick(x, v0, v1, ...)` | Returns value at index x |
| `Count(v0, v1, ...)` | Count of non-empty values |

**Example — replace nested IIF with Match + Pick:**
```
' Nested IIF version:
IIF([1811] = "PrimaryResidence", 0.05 * [#1109],
IIF([1811] = "SecondHome",       0.02 * [#1109],
IIF([1811] = "Investor",         0.01 * [#1109], 0)))

' Match + Pick version:
Pick(Match([1811], "PrimaryResidence", "SecondHome", "Investor", ""),
     0.05 * [#1109], 0.02 * [#1109], 0.01 * [#1109], 0)
```

**Range example — fee tier by loan amount:**
```
Pick(Range([1109], 100000, 200000, 500000), 500, 750, 1000, 1500)
```
Returns 500 for ≤$100k, 750 for $100k–$200k, 1000 for $200k–$500k, 1500 for >$500k.

---

## Calculation Errors and How to Avoid Them

**Two error types:**
- **Syntax errors** — caught when you click Validate
- **Runtime errors** — occur during loan use; Encompass clears the field when this happens

**Common causes:**
- A referenced field is blank (unpopulated numeric fields return `""`, not 0)
- Division by zero
- Date function receiving a non-date value
- IIF branch that fails even when not selected

**Fixes:**
1. Use `[#FieldID]` to convert blanks to 0
2. Use safe functions: `SumAny`, `Diff`, `Mult`, `Div`, etc.
3. Use `IfEmpty([1109], 0)` to supply a fallback
4. Split complex logic across two custom fields if one branch is inherently unsafe

**Circular dependencies** — Encompass will warn you if your custom fields reference
each other in a loop. Avoid these patterns:
```
CX.A = [1109] + [CX.B]
CX.B = [CX.C] * 2
CX.C = [CX.A] + 20000    ← circular!
```

---

## Advanced: VB.NET Functions

The calculation engine is built on VB.NET, so any `Microsoft.VisualBasic` function
is available. Do **not** use anything that shows UI (e.g., `MsgBox`) — this will
crash Encompass Server.

---

## Common Field IDs

| Field ID | Description |
|----------|-------------|
| 3 | Interest Rate |
| 14 | Subject Property State |
| 19 | Loan Purpose |
| 44 | Secondary Financing Amount |
| 136 | Purchase Price |
| 1109 | Loan Amount |
| 1335 | Borrower Income |
| 1402 | Application Date |
| 1403 | Closing Date |
| 1811 | Occupancy Type |
| 4000 | Borrower First Name |
| 4002 | Borrower Last Name |
| CX.* | Custom Field prefix |
| MS.APP | Approval Milestone Date |
| MS.DOC | Documents Milestone Date |