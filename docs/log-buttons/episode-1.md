# Log Button Episode 1

A walkthrough of building a comment log button in the Encompass Desktop Input Form Builder. The button captures a comment, stamps it with a DST-safe Central Time timestamp and the current user's name, and prepends it to a custom log field — so the newest entry always appears at the top.

## How it works

**Reads the comment entry box** — The script reads the text the user typed into the comments multiline text box and checks that it isn't blank before doing anything else.

**Gets the existing log** — The current value of the custom log field (`CX.PCC.COMPLIANCE.COMM.LOG`) is read so new entries can be prepended to it rather than overwriting it.

**Builds a DST-safe Central Time timestamp** — Instead of using the machine's local time (which can shift or be incorrect), the script explicitly converts UTC to Central Standard Time using `TimeZoneInfo` and then checks whether daylight saving time is active to label the entry `CDT` or `CST` correctly.

**Builds the log entry** — The timestamp, timezone abbreviation, and the current user's full name are combined with the comment text into a formatted block.

**Prepends to the log** — If the log field already has content, the new entry is placed at the top. If it's empty, the new entry becomes the first.

**Clears the entry box and refreshes the display** — After saving, the comment input is cleared and the log viewer is updated so the user immediately sees the new entry.

## The code

```vb
' ================================================================
' COMPLIANCE AUDIT - Comment Logging
' ================================================================
Dim caCommentValue As String = MultilineTextBoxComments.Text
Dim caExistingLog As String = Loan.Fields("CX.PCC.COMPLIANCE.COMM.LOG").Value & ""
If caCommentValue.Trim() <> "" Then

    '- DST-SAFE CENTRAL TIME -
    Dim centralZone As TimeZoneInfo = TimeZoneInfo.FindSystemTimeZoneById("Central Standard Time")
    Dim centralTime As DateTime = TimeZoneInfo.ConvertTimeFromUtc(DateTime.UtcNow, centralZone)
    Dim zoneAbbrev As String = If(centralZone.IsDaylightSavingTime(centralTime), "CDT", "CST")

    ' Build log entry block
    Dim caLogEntry As String = _
        centralTime.ToString("MM/dd/yyyy hh:mm tt") & " " & zoneAbbrev & "  " & _
        EncompassApplication.CurrentUser.FullName & vbCrLf & _
        caCommentValue & vbCrLf & vbCrLf

    ' Prepend new entry to existing log
    If caExistingLog.Trim() <> "" Then
        Loan.Fields("CX.PCC.COMPLIANCE.COMM.LOG").Value = caLogEntry & caExistingLog
    Else
        Loan.Fields("CX.PCC.COMPLIANCE.COMM.LOG").Value = caLogEntry
    End If

    ' Clear entry and update visible log display
    MultilineTextBoxComments.Text = ""
    MultilineTextBoxCommentLog.Text = Loan.Fields("CX.PCC.COMPLIANCE.COMM.LOG").Value

End If
' ================================================================
' END COMPLIANCE AUDIT COMMENT LOGGING
' ================================================================
```

## How to use it

1. In the Encompass Desktop Input Form Builder, open the form you want to add the log button to
2. Add a multiline text box for comment entry and name it `MultilineTextBoxComments`
3. Add a second multiline text box to display the log and name it `MultilineTextBoxCommentLog`
4. Add a button (e.g. labeled **Post**) and open its Click event
5. Paste the code above into the Click event
6. Replace `CX.PCC.COMPLIANCE.COMM.LOG` with the custom field ID you want to use for storing the log in your environment
7. Save and test by typing a comment and clicking the button — the log display should populate with a timestamped entry

## Notes

- The custom field used in this example is `CX.PCC.COMPLIANCE.COMM.LOG` — replace it with your own custom field ID. Custom fields always start with `CX.`
- Entries are **prepended** so the most recent comment always appears at the top of the log
- The timestamp is deliberately pulled from UTC and converted to Central Time rather than using the machine's local clock — this keeps entries consistent regardless of where the user is located
- `CDT` / `CST` is set dynamically based on whether daylight saving time is active at the moment the button is clicked
- `EncompassApplication.CurrentUser.FullName` pulls the full name of the logged-in Encompass user — no manual entry required
- The control names in this example (`MultilineTextBoxComments`, `MultilineTextBoxCommentLog`) are named controls. If your form uses indexed names (e.g. `MultilineTextBox265`), substitute accordingly
