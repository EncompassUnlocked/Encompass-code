# Encompass Field Trigger: Date & Time Stamp with Time Zone

## Overview

This field trigger captures a date and time stamp — converted to a specific time zone — and writes it into one or more custom fields. It is useful for recording key loan events (e.g., broker submission, lock request, document receipt) with an accurate local timestamp rather than relying on UTC or the server's default time zone.

---

## Code Blocks by Time Zone

Each block includes the time zone abbreviation (`EST`/`EDT`, `CST`/`CDT`, `PST`/`PDT`) appended automatically based on whether Daylight Saving Time is in effect.

### Eastern Time

```vb
IgnoreValidationErrors

Dim easternZone As TimeZoneInfo = TimeZoneInfo.FindSystemTimeZoneById("Eastern Standard Time")
Dim easternTime As DateTime = TimeZoneInfo.ConvertTimeFromUtc(DateTime.UtcNow, easternZone)
Dim tzLabel As String = If(easternZone.IsDaylightSavingTime(easternTime), "EDT", "EST")
Dim stamp As String = easternTime.ToString("MM/dd/yyyy hh:mm tt") & " " & tzLabel

[CX.YOUR.FIELD] = stamp
```

### Central Time

```vb
IgnoreValidationErrors

Dim centralZone As TimeZoneInfo = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time")
Dim centralTime As DateTime = TimeZoneInfo.ConvertTimeFromUtc(DateTime.UtcNow, centralZone)
Dim tzLabel As String = If(centralZone.IsDaylightSavingTime(centralTime), "CDT", "CST")
Dim stamp As String = centralTime.ToString("MM/dd/yyyy hh:mm tt") & " " & tzLabel

[CX.YOUR.FIELD] = stamp
```

### Pacific Time

```vb
IgnoreValidationErrors

Dim pacificZone As TimeZoneInfo = TimeZoneInfo.FindSystemTimeZoneById("Pacific Standard Time")
Dim pacificTime As DateTime = TimeZoneInfo.ConvertTimeFromUtc(DateTime.UtcNow, pacificZone)
Dim tzLabel As String = If(pacificZone.IsDaylightSavingTime(pacificTime), "PDT", "PST")
Dim stamp As String = pacificTime.ToString("MM/dd/yyyy hh:mm tt") & " " & tzLabel

[CX.YOUR.FIELD] = stamp
```

### Example Output
```
04/19/2026 10:30 AM CDT
```

---

## Key Components

| Component | Purpose |
|---|---|
| `IgnoreValidationErrors` | Allows the trigger to write to fields that may have validation restrictions |
| `TimeZoneInfo.FindSystemTimeZoneById(...)` | Looks up the target time zone by its Windows ID |
| `TimeZoneInfo.ConvertTimeFromUtc(...)` | Converts the current UTC time to the target time zone, including daylight saving time adjustments automatically |
| `DateTime.UtcNow` | Gets the current time in UTC — always accurate regardless of the server's local time |
| `.ToString("MM/dd/yyyy hh:mm tt")` | Formats the timestamp (e.g., `04/19/2026 10:30 AM`) |

---

## Changing the Time Zone

Replace the time zone ID string with any valid Windows time zone ID. Common options:

| Time Zone | ID String |
|---|---|
| Eastern | `"Eastern Standard Time"` |
| Central | `"Central Standard Time"` |
| Mountain | `"Mountain Standard Time"` |
| Pacific | `"Pacific Standard Time"` |

> **Note:** Despite the name "Standard Time" in the ID, `ConvertTimeFromUtc` automatically accounts for Daylight Saving Time. You do not need a separate ID for daylight time.

---

## Customizing the Format

Adjust the format string in `.ToString(...)` to change the output:

| Format String | Example Output |
|---|---|
| `"MM/dd/yyyy hh:mm tt"` | `04/19/2026 10:30 AM` |
| `"MM/dd/yyyy HH:mm"` | `04/19/2026 10:30` (24-hour) |
| `"MM/dd/yyyy hh:mm:ss tt"` | `04/19/2026 10:30:45 AM` (with seconds) |
| `"M/d/yyyy h:mm tt"` | `4/19/2026 10:30 AM` (no leading zeros) |

---

## Setup in Encompass

1. Go to **Settings → Business Rules → Field Triggers**
2. Create a **New** trigger and set the triggering field (e.g., a broker submission checkbox or status field)
3. Under **Action**, select **"Run advanced code"**
4. Paste the trigger code into the code editor
5. Replace `[CX.YOUR.FIELD]` with your actual custom field ID
6. Save and test by changing the trigger field on a test loan

---

## Notes

- This trigger can write to **multiple fields** in a single execution — useful when you need the same stamp on both a loan field and a dashboard/reporting field.
- The trigger will **not fire again** on the same field change even if the fields it writes to would normally re-trigger it — Encompass prevents infinite loops automatically.
- The time zone abbreviation (e.g., `CST`/`CDT`) is included in all code blocks above. The `IsDaylightSavingTime()` check handles the switch automatically — no manual adjustment needed.
