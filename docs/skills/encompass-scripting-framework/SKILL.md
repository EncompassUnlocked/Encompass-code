---
name: encompass-scripting-framework
description: >
  Use this skill whenever the user wants to write, explain, debug, or work with
  ICE Mortgage Technology (Encompass) Secure Scripting Framework code — including
  Custom Forms, Custom Tools, and Plugins. Trigger this skill any time the user
  mentions Encompass scripting, elli.script, scripting objects (Application, Auth,
  Http, Loan, Session, Script), JavaScript in Encompass, Custom Tool scripting,
  Plugin scripting, or asks how to interact with loan data, UI, authentication, or
  HTTP calls from within an Encompass guest context. Also trigger when the user
  asks about debugging, console.log, logging patterns, or structured output in an
  Encompass or JavaScript context — even if they don't use the phrase "Secure
  Scripting Framework."
---

# ICE Mortgage Technology — Secure Scripting Framework

This skill covers writing, troubleshooting, and debugging JavaScript-based
scripting code inside the **ICE Secure Scripting Framework** for Encompass.
It targets Custom Forms, Custom Tools, and Plugins running as guests in the
Encompass application — including smart logging and debugging patterns.

---

## What Is the Secure Scripting Framework?

The Secure Scripting Framework lets developers embed JavaScript inside Encompass
guests (Custom Forms, Custom Tools, Plugins). Scripts run in a sandboxed browser
context and interact with the host application through a set of **Scripting Objects**
exposed via the `elli.script` API.

**Supported integration types:**
- Custom Form
- Custom Tool
- Plugin

**Key constraint:** Scripts cannot access the DOM directly or make unrestricted
network calls — all host interaction goes through the scripting objects.

---

## Accessing Scripting Objects

All objects are accessed via `elli.script.getObject()` using the object's
lowercase string ID. This is **case-sensitive**.

```javascript
const loan    = await elli.script.getObject("loan");
const app     = await elli.script.getObject("application");
const auth    = await elli.script.getObject("auth");
const http    = await elli.script.getObject("http");
const session = await elli.script.getObject("session");
const script  = await elli.script.getObject("script");
```

> All `getObject` calls are async — always `await` them.

---

## Available Scripting Objects

| Object ID     | Description |
|---------------|-------------|
| `application` | UI-level events and context for the host application |
| `auth`        | Retrieve authentication tokens for API calls |
| `http`        | Make HTTP requests to approved endpoints |
| `loan`        | Read/write loan field data |
| `session`     | Access current user session info |
| `script`      | Control the script lifecycle (close, resize, etc.) |

Each object is a **singleton** — call `getObject` once and cache the result.

---

## Application Object

**Object ID:** `application`
**Available in:** Custom Form, Custom Tool, Plugin

### Events

| Event | Description | Feedback |
|-------|-------------|----------|
| `login` | Fires after user is fully logged in and UI is rendered. Does not fire for super administrator users — test with a non-admin account. | none |

### Methods

#### `getApplicationContext()`
Returns a JSON object with environment and route information.

```javascript
const app     = await elli.script.getObject("application");
const context = await app.getApplicationContext();
// context.env.apiHost => "https://api.elliemae.com"
// context.route.url, context.route.type, context.route.name, context.route.id
```

**Sample return value:**
```json
{
  "env": { "apiHost": "https://api.elliemae.com" },
  "route": {
    "url": "https://encompass.ice.com/pipeline/12345b77-.../custom-tools-tool",
    "type": "CUSTOM_TOOL",
    "name": "My Pipeline",
    "id": "GUID"
  }
}
```

**Possible `route.type` values:**

| type | Description |
|------|-------------|
| `GLOBAL_CUSTOM_TOOL` | Global custom tool pipeline view |
| `CUSTOM_TOOL` | Loan-level custom tool |
| `CUSTOM_FORM` | Custom form inside a loan (IFB) |
| `STANDARD_FORM` | Standard form inside a loan |
| `OTHER` | Any other context (name and id may be null) |

---

## Auth Object

**Object ID:** `auth`
**Available in:** Custom Form, Custom Tool, Plugin

#### `getAccessToken()`
Returns the current user's OAuth access token as a string.

```javascript
const auth  = await elli.script.getObject("auth");
const token = await auth.getAccessToken();

fetch("https://api.elliemae.com/...", {
  headers: { "Authorization": `Bearer ${token}` }
});
```

> **Security:** Never log the token value. Log `!!token` (a boolean) instead.

---

## Http Object

**Object ID:** `http`
**Available in:** Custom Form, Custom Tool, Plugin

#### `request(config)`
Sends an HTTP request. Config mirrors standard fetch options.

```javascript
const http     = await elli.script.getObject("http");
const response = await http.request({
  method: "GET",
  url: "https://api.elliemae.com/encompass/v3/loans/...",
  headers: { "Authorization": `Bearer ${token}` }
});
const data = JSON.parse(response.body);
```

---

## Loan Object

**Object ID:** `loan`
**Available in:** Custom Form, Custom Tool, Plugin

#### `getField(fieldId)` — Read a single field
```javascript
const loan  = await elli.script.getObject("loan");
const state = await loan.getField("14");   // Subject Property State
const amt   = await loan.getField("1109"); // Loan Amount
```

#### `getFields(fieldIds[])` — Batch read multiple fields
```javascript
const fields = await loan.getFields(["14", "19", "1109"]);
// fields["14"] => "CA"
```

#### `setField(fieldId, value)` — Write a field
```javascript
await loan.setField("CX.MYFLAG", "Y");
await loan.setField("1109", "450000");
```

#### `save()` — Persist changes
```javascript
await loan.save(); // Required — setField does NOT auto-save
```

---

## Session Object

**Object ID:** `session`
**Available in:** Custom Form, Custom Tool, Plugin

#### `getUser()`
Returns the current user's info as a JSON object.

```javascript
const session = await elli.script.getObject("session");
const user    = await session.getUser();
// user.id, user.name, user.email, user.role
```

---

## Script Object

**Object ID:** `script`
**Available in:** Custom Form, Custom Tool, Plugin

#### `close()`
Closes the custom tool or form window.

```javascript
const script = await elli.script.getObject("script");
await script.close();
```

---

## Async/Await Programming Model

All scripting object methods are **Promise-based**. Always use `async/await`.

**Top-level async IIFE (recommended pattern):**
```javascript
(async () => {
  const loan    = await elli.script.getObject("loan");
  const loanAmt = await loan.getField("1109");
  console.log("[MyTool] loanAmt:", loanAmt);
})();
```

**Event listener pattern:**
```javascript
(async () => {
  const app = await elli.script.getObject("application");
  app.addEventListener("login", async () => {
    const session = await elli.script.getObject("session");
    const user    = await session.getUser();
    console.log("[login] user:", user.name);
  });
})();
```

---

## Debugging with console.log

Logs appear in the **browser DevTools console** inside the Encompass embedded
Chromium window. Open with `F12` or right-click → Inspect.

> **Never use `alert()`** — it blocks the UI thread and can hang Encompass.

### Choosing the Right Console Method

| Method | When to use |
|--------|-------------|
| `console.log()` | General values, flow checkpoints |
| `console.warn()` | Unexpected but non-fatal situation |
| `console.error()` | Caught errors, failed conditions |
| `console.table()` | Arrays of objects — much more readable than log |
| `console.group()` / `console.groupEnd()` | Collapsible sections for related output |
| `console.groupCollapsed()` | Same but starts collapsed — great for noisy loops |
| `console.time()` / `console.timeEnd()` | Measure how long something takes |
| `console.assert()` | Only logs if condition is false — great sanity checks |
| `console.trace()` | Prints the call stack — use when you need to know *how* you got here |

### Always Label Your Logs

Use a **bracket tag** to show which component or function the log came from:

```javascript
// ❌ Bad — what is this?
console.log(user);

// ✅ Good
console.log("[fetchLoan] response:", response);
console.log("[AuthHelper] token present:", !!token); // never log the token itself
```

### Grouping Related Logs

```javascript
console.group("[fetchLoan]");
console.log("loanId:", loanId);
console.log("token present:", !!token);
console.log("response status:", response.status);
console.groupEnd();
```

Use `console.groupCollapsed()` inside loops or frequent event handlers to keep
the console readable.

### Logging Objects and Arrays

```javascript
// ❌ Coerces to string — logs [object Object]
console.log("data: " + data);

// ✅ Logs the full object
console.log("data:", data);

// ✅ Snapshot — won't mutate if the object changes later
console.log("data:", JSON.parse(JSON.stringify(data)));

// ✅ Arrays of objects — renders as a readable grid
console.table(users);
```

### Logging Async Flows

```javascript
// ❌ Logs Promise {} — not the value
console.log("result:", fetchLoan());

// ✅ Always await first, then log
const result = await fetchLoan();
console.log("[fetchLoan] result:", result);
```

### Gated Debug Logging (keep production clean)

```javascript
const DEBUG = true; // flip to false before committing

function log(...args) {
  if (DEBUG) console.log("[APP]", ...args);
}

log("fields loaded:", fields);
```

### Performance Timing

```javascript
console.time("fetchLoan");
const data = await fetchLoan(loanId);
console.timeEnd("fetchLoan");
// Output: fetchLoan: 243ms
```

### Assertions for Sanity Checks

```javascript
// Only fires when condition is FALSE — zero noise when things are correct
console.assert(loanId !== null, "loanId should never be null", { loanId });
console.assert(Array.isArray(fields), "fields must be an array", fields);
```

### Encompass-Specific Debugging Example

```javascript
(async () => {
  const loan   = await elli.script.getObject("loan");
  const fields = await loan.getFields(["14", "19", "1109"]);

  console.group("[LoanDebug] fields snapshot");
  console.table(Object.entries(fields).map(([id, val]) => ({ id, val })));
  console.groupEnd();
})();
```

### Cleanup Checklist Before Committing

- [ ] Remove bare `console.log(value)` statements with no label
- [ ] Replace debug logs with a `DEBUG` flag or remove entirely
- [ ] Use `console.error()` for actual errors, not `console.log`
- [ ] Confirm no tokens, passwords, or PII are logged
- [ ] Replace multi-line related logs with `console.group()` blocks

---

## Full Code Samples

### Read loan fields and call the Encompass API

```javascript
(async () => {
  const auth = await elli.script.getObject("auth");
  const loan = await elli.script.getObject("loan");
  const http = await elli.script.getObject("http");

  const token  = await auth.getAccessToken();
  const loanId = await loan.getField("364"); // Loan GUID

  console.log("[fetchLoan] loanId:", loanId);
  console.log("[fetchLoan] token present:", !!token);

  const resp = await http.request({
    method: "GET",
    url: `https://api.elliemae.com/encompass/v3/loans/${loanId}`,
    headers: {
      "Authorization": `Bearer ${token}`,
      "Content-Type": "application/json"
    }
  });

  const loanData = JSON.parse(resp.body);
  console.group("[fetchLoan] response");
  console.log("status:", resp.status);
  console.log("data:", loanData);
  console.groupEnd();
})();
```

### Conditionally set a field based on another field

```javascript
(async () => {
  const loan  = await elli.script.getObject("loan");
  const state = await loan.getField("14");

  console.log("[setRegion] state:", state);

  if (state === "CA") {
    await loan.setField("CX.REGION", "West");
  } else if (["NY", "NJ", "CT"].includes(state)) {
    await loan.setField("CX.REGION", "Northeast");
  } else {
    await loan.setField("CX.REGION", "Other");
  }

  await loan.save();
  console.log("[setRegion] saved.");
})();
```

### React to login event and display user info

```javascript
(async () => {
  const app     = await elli.script.getObject("application");
  const session = await elli.script.getObject("session");

  app.addEventListener("login", async () => {
    const user = await session.getUser();
    console.log("[login] user:", user.name);
    document.getElementById("welcome").textContent = `Welcome, ${user.name}`;
  });
})();
```

---

## Common Gotchas

| Gotcha | Fix |
|--------|-----|
| Forgot `await` on `getObject()` | Returns a Promise, not the object — always await |
| `login` event not firing | Super admins are excluded — test with a non-admin account |
| Calling `getObject()` multiple times | Objects are singletons — cache the result in a variable |
| `setField` not persisting | Must call `loan.save()` explicitly |
| Wrong object ID casing | IDs are case-sensitive — `"Loan"` fails, use `"loan"` |
| HTTP request blocked | The `http` object only reaches pre-approved domains |
| `alert()` hanging the app | Use `console.log()` or `console.warn()` instead |
| Logging the auth token | Security risk — log `!!token` (boolean) instead |
| `console.log(promise)` | Logs `Promise {}` — always `await` before logging |
| Bare log with no label | Use bracket tags like `[MyTool]` so logs are scannable |

---

## Quick Reference: Common Loan Field IDs

| Field ID | Description |
|----------|-------------|
| 14 | Subject Property State |
| 19 | Loan Purpose |
| 136 | Purchase Price |
| 364 | Loan GUID |
| 1109 | Loan Amount |
| 1401 | Loan Program |
| 3 | Interest Rate |
| CX.* | Custom Fields (prefix) |

---

## Relationship to Advanced Coding (VB.NET Rules)

This skill covers **JavaScript-based scripting** in Custom Forms, Custom Tools,
and Plugins. For **VB.NET-based business rules** (Field Triggers, Field Data Entry
validation, Milestone Completion, Loan Form Printing), see the
`encompass-advanced-coding` skill instead.

| Feature | Secure Scripting (this skill) | Advanced Coding |
|---------|-------------------------------|-----------------|
| Language | JavaScript | VB.NET |
| Context | Custom Form / Tool / Plugin | Business Rules engine |
| Runs in | Guest browser (Chromium) | Server-side rule engine |
| Can call APIs | Yes (via http object) | No |
| Can modify UI | Yes (DOM in guest) | No |
| Triggered by | Events / page load | Field changes, milestones |