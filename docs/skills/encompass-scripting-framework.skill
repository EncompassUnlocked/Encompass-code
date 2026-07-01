---
name: encompass-scripting-framework
description: >
  Use this skill whenever the user wants to write, explain, debug, or work with
  ICE Mortgage Technology (Encompass) Secure Scripting Framework code — including
  Custom Forms, Custom Tools, and Plugins. Trigger this skill any time the user
  mentions Encompass scripting, elli.script, scripting objects (Application, Auth,
  Http, Loan, Session, Global, Service), Custom Form controls (TextBox, Dropdown,
  Collection/Grid Box, Button), JavaScript in Encompass, Custom Tool scripting,
  Plugin scripting, or asks how to interact with loan data, UI, authentication, or
  HTTP calls from within an Encompass guest context. Also trigger when the user
  asks about debugging, console.log, breakpoints, logging patterns, or structured
  output in an Encompass or JavaScript context — even if they don't use the phrase
  "Secure Scripting Framework."
---

# ICE Mortgage Technology — Secure Scripting Framework

This skill covers writing, troubleshooting, and debugging JavaScript-based
scripting code inside the **ICE Secure Scripting Framework** for Encompass.
It targets Custom Forms, Custom Tools, and Plugins running as guests in the
Encompass application — including smart logging and debugging patterns.

---

## What Is the Secure Scripting Framework?

The Secure Scripting Framework lets developers embed JavaScript inside Encompass
guests (Custom Forms, Custom Tools, Plugins). Scripts run in a sandboxed HTML5
iframe and interact with the host application through a set of **Scripting
Objects** exposed via the global `elli.script` entry point.

**Supported integration types:**
- Custom Form
- Custom Tool
- Plugin (auto-launched at sign-in, lives in an iframe for the whole session)
- TPO Connect (parent/child application contexts)

**Key constraints:**
- Scripts cannot access the host application directly or make unrestricted
  network calls — all host interaction goes through the scripting objects.
- Many methods and events are **platform-specific** — tagged **(Web only)** or
  **(Desktop only)** below. Don't assume a method that works in Encompass Web
  also works in Encompass Desktop, or vice versa.
- **Code must be ES5-compatible.** Encompass does not auto-transpile. If you
  write ES6/ES2017 (`async/await`, arrow functions, etc.), transpile it with
  the **Elli-CLI** tool (Babel-based) before deploying.

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
const global_ = await elli.script.getObject("global");
const service = await elli.script.getObject("service");
```

> All `getObject` calls are async — always `await` them.

### The `elli.script` Global

`elli.script` itself is the single entry point for the guest library:

| Method | Description |
|--------|--------------|
| `getObject(objectId)` | Returns the named Scripting Object |
| `subscribe(objectId, eventName, callback)` | Registers a callback for an object's event, returns a subscription token |
| `unsubscribe(objectId, eventName, token)` | Removes a previously registered subscription |

```javascript
const token = elli.script.subscribe("loan", "change", (ctx) => {
  console.log("[loan.change]", ctx);
});
// later
elli.script.unsubscribe("loan", "change", token);
```

### Fire-and-Forget vs. Interactive Events

- **Fire-and-forget** — the handler's return value is ignored (e.g. `loan.change`).
- **Interactive** — the handler must return a boolean (or a Promise resolving to
  one) to allow/block the host action, and it's held to a timeout — commonly
  **1 second on Web**, up to **60 seconds on Desktop** for events like
  `loan.precommit` and `loan.premilestoneComplete`. Don't do slow work
  (network calls, heavy loops) inside an interactive handler or it'll time out.

---

## Available Scripting Objects

| Object ID     | Description | Platform |
|---------------|-------------|----------|
| `application` | UI-level events, navigation, and host context | Web + Desktop (some methods Web- or Desktop-only) |
| `auth`        | Retrieve access tokens and current user identity | Web + Desktop |
| `http`        | Make HTTP requests to approved endpoints | Web only |
| `loan`        | Read/write loan field data, calculate, commit | Web + Desktop (some methods Web- or Desktop-only) |
| `session`     | Per-guest key/value scratch storage | Web only |
| `global`      | Cross-customization key/value storage for the session | Web only |
| `service`     | Launch service-provider integrations (credit, title, etc.) | Web only |

Each object is a **singleton** — call `getObject` once and cache the result.

---

## Application Object

**Object ID:** `application`
**Available in:** Custom Form, Custom Tool, Plugin

### Events

| Event | Description | Feedback |
|-------|-------------|----------|
| `login` | Fires after user is fully logged in and UI is rendered. Does not fire for super administrator users — test with a non-admin account. Async on Web; synchronous with a 5-second max on Desktop. | none |

### Methods

| Method | Description | Platform |
|--------|-------------|----------|
| `getApplicationContext()` | Returns environment + route info (see below) | Web + Desktop |
| `getDescriptor()` | Returns `{ id, name }` for the current customization | Web + Desktop |
| `navigate(navigateOptions)` | Routes to another form, tool, or global page | Web + Desktop |
| `performAction(actionName, actionOptions)` | Executes a predefined action (30+ types, e.g. `UpdateCorrespondentBalance`, `CalculateEemMortgage`) | Web + Desktop |
| `open(openOptions)` | Opens a URL/resource in a new browser tab | Web + Desktop |
| `openModal(openOptions)` | Opens a URL/resource in a modal dialog (`sm`/`md`/`lg` sizes) | Web + Desktop |
| `closeModal()` | Closes the modal opened by `openModal()` | Web + Desktop |
| `print(printOptions)` | Opens the browser print dialog for PDF/JPG output | Web + Desktop |
| `supportsAction(actionName)` | Returns boolean — is this action supported here | Web only |
| `getCapabilities()` | Returns supported actions/features | Web only |
| `keepSessionAlive()` | Extends the user's session | Web only |
| `getUserAccessRights()` | Returns the current user's permission rights | Desktop only |
| `getInfo()` | Returns the Encompass version string | Desktop only |
| `getCompanySettings(options)` | Returns filtered company settings | Desktop only |

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
Returns an **object** — not a bare string — representing the current user's
access token:

```javascript
const auth   = await elli.script.getObject("auth");
const result = await auth.getAccessToken();
// result.access_token, result.token_type, result.host_name

fetch(`https://${result.host_name}/...`, {
  headers: { "Authorization": `${result.token_type} ${result.access_token}` }
});
```

> **Security:** Never log the token value. Log `!!result.access_token` (a boolean) instead.

#### `getUser()`
Returns the current user's identity — this lives on **`auth`**, not `session`.

```javascript
const auth = await elli.script.getObject("auth");
const user = await auth.getUser();
// user.id, user.realm, user.firstName, user.lastName, user.email,
// user.phone, user.cellPhone, user.personas, user.clientId, user.chumId,
// user.employeeId, user.nmlsOriginatorId, user.isAdmin, user.instanceId
```

#### `createAuthCode(clientID)`
**Deprecated** — use `getAccessToken()` instead. Don't copy sample code that
still uses this.

---

## Http Object

**Object ID:** `http`
**Available in:** Custom Form, Custom Tool, Plugin (**Web only**)

There is no generic `request(config)` method — each HTTP verb has its own
method. All return Promises that resolve to the response, or reject for
non-200 responses.

| Method | Description |
|--------|-------------|
| `get(url, headerObjOrAccessToken)` | Executes a GET request |
| `post(url, contentObj, headerObjOrAccessToken)` | Executes a POST request |
| `put(url, contentObj, headerObjOrAccessToken)` | Same as `post()` but PUT verb |
| `patch(url, contentObj, headerObjOrAccessToken)` | Same as `post()` but PATCH verb |
| `delete(url, headerObjOrAccessToken)` | Same as `get()` but DELETE verb |

```javascript
const http  = await elli.script.getObject("http");
const auth  = await elli.script.getObject("auth");
const token = await auth.getAccessToken();

const loanData = await http.get(
  "https://api.elliemae.com/encompass/v3/loans/GUID",
  token // headerObjOrAccessToken accepts the token result directly
);
```

---

## Loan Object

**Object ID:** `loan`
**Available in:** Custom Form, Custom Tool, Plugin

### Methods

| Method | Description | Platform |
|--------|-------------|----------|
| `getField(fieldId)` | Read a single field value | Web + Desktop |
| `setFields(fieldMap)` | Batch-write multiple fields from a JSON map | Web + Desktop |
| `calculate()` | Runs calculations and business rules | Web + Desktop |
| `commit()` | Persists pending changes to the server (**not `save()`**) | Web + Desktop |
| `isReadOnly()` | Returns boolean — can the loan be edited right now | Web + Desktop |
| `all()` | Returns the entire loan object (v3 mode) | Web only |
| `merge()` | Syncs the workspace with other users' changes | Web only |
| `execAction()` | Executes a supported loan action (v3 stateful) | Web only |
| `getCurrentApplication()` | Returns the current borrower pair (`index`, `id`, `legacyId`) | Desktop only |
| `getFields(fieldIds[])` | Batch-read multiple fields | Desktop only |
| `getFieldsOptions(fieldIds[])` | Returns enum/option lists for fields | Desktop only |
| `applyLock(fieldId, locked)` | Adds/removes a calculation lock on a field | Desktop only |
| `getSnapshot()` | Returns a lock-request snapshot | Desktop only |
| `openLoan(loanGuid)` | Opens a different loan | Desktop only |

```javascript
const loan  = await elli.script.getObject("loan");
const state = await loan.getField("14");   // Subject Property State
const amt   = await loan.getField("1109"); // Loan Amount

await loan.setFields({ "CX.MYFLAG": "Y", "1109": "450000" });
await loan.commit(); // Required — setFields does NOT auto-save
```

**Borrower pairs:** field access uses colon notation for pair-specific fields,
e.g. `"4000:0"` for the first borrower pair (zero-based index). Field `4460`
holds the total count of active borrower pairs.

### Events

| Event | Description | Platform / Feedback |
|-------|-------------|----------------------|
| `open` | Fires after the loan opens and is ready | Web + Desktop, fire-and-forget |
| `change` | Fires on each user UI change | Web + Desktop, fire-and-forget |
| `precommit` | Fires before save; return `true`/`false` to allow/block the commit | Web sync, 1s max; Desktop sync, 60s max — **interactive** |
| `committed` | Fires after a successful save | Web + Desktop, fire-and-forget |
| `sync` | Fires after the loan syncs following `calculate()` or `merge()` | Web only, fire-and-forget |
| `close` | Fires before the loan closes; return `true`/`false` | Web sync, 1s max; Desktop async — **interactive** |
| `premilestoneComplete` | Fires when the user attempts milestone completion; return `true`/`false` | Web sync, 1s max; Desktop sync, 60s max — **interactive** |
| `milestoneCompleted` | Fires after milestone completion | Desktop only, async, fire-and-forget |
| `applicationselected` | Fires when the active borrower pair changes | Web only, fire-and-forget |
| `fieldChangeSync` | Synchronous variant queued during plugin callbacks | Desktop only |

---

## Session Object

**Object ID:** `session`
**Available in:** Custom Form, Plugin (**Web only**)

A small per-guest key/value scratch store — **not** where user identity lives
(that's `auth.getUser()`).

| Method | Description |
|--------|-------------|
| `set(key, valueObj)` | Stores a value in session state (max 5 KB per value) |
| `get(key)` | Retrieves a previously stored value |

**Constraint:** max **5 values** per guest application.

```javascript
const session = await elli.script.getObject("session");
await session.set("draftNotes", { text: "follow up Friday" });
const notes = await session.get("draftNotes");
```

---

## Global Object

**Object ID:** `global`
**Available in:** Custom Form, Custom Tool, Plugin (**Web only**)

Like `session`, but shared across **all** customizations running in the
current session rather than scoped to one guest.

| Method | Description |
|--------|-------------|
| `set(key, valueObj)` | Stores a value in global state (max 5 KB per value) |
| `get(key)` | Retrieves a previously stored value |

**Constraint:** max **50 values**, visible to every customization in the session.

---

## Service Object

**Object ID:** `service`
**Available in:** Custom Form, Plugin (**Web only**)

Launches service-provider integrations (credit, title, etc.). Requires
committed loan changes before launching; appends `transactionId` and
`orderId` query parameters on redirect.

| Method | Description |
|--------|-------------|
| `getEligibleServices(category, providerId)` | Returns matching service setups |
| `launchByCategory(category, options)` | Launches a service integration by category |
| `launchByProvider(providerId, options)` | Launches a service integration by provider ID |
| `launchByServiceSetup(serviceSetupId, options)` | Launches a service integration by setup ID |

---

## Custom Form Controls

Custom Forms have a second, control-based scripting surface in addition to the
guest objects above. Handlers are named `<ControlName>_On<Event>(ctrl)` and
receive the control instance.

```javascript
function Dropdown1_OnChange(ctrl) {
  const state = ctrl.value();
  if (state === "CA") {
    // ...
  }
}

function Form_OnLoad() { /* form initialization */ }
function Form_OnUnload() { /* form teardown */ }
```

### Common Controls and Their Methods

| Control | Methods | Events |
|---------|---------|--------|
| TextBox / Multi-line Text Box | `value()`, `Color()`, `disabled()`, `visible()`, `interactive()` | Change, FocusIn, FocusOut |
| Dropdown | same as TextBox | Change, FocusIn, FocusOut |
| Checkbox | `value()`, `disabled()`, `visible()` | Change |
| Radio Button | `value()`, `disabled()`, `visible()` | Change |
| Calendar | `value()`, `disabled()`, `visible()` | Change |
| Rolodex / Contact Button | `value()`, `disabled()`, `visible()` | Change, FocusIn, FocusOut |
| Button | `color()` | Click |
| Hyperlink | — | Click |
| Collection/Grid Box | `getRowCount()`, `getRowAt(index)` | fires on manual row add/remove |

```javascript
function Grid1_OnChange(ctrl) {
  const rowCount = ctrl.getRowCount();
  for (let i = 0; i < rowCount; i++) {
    const row = ctrl.getRowAt(i);
    // ...
  }
}
```

---

## Async/Await Programming Model

Guest-object methods (`elli.script.getObject(...)` and everything returned
from it) are **Promise-based** — use `async/await`. Custom Form control
handlers (`Dropdown1_OnChange`, etc.) are plain synchronous callbacks invoked
by the host, but can still call `await` inside an async IIFE if they need to
talk to a scripting object.

**Top-level async IIFE (recommended pattern):**
```javascript
(async () => {
  const loan    = await elli.script.getObject("loan");
  const loanAmt = await loan.getField("1109");
  console.log("[MyTool] loanAmt:", loanAmt);
})();
```

**Subscription pattern:**
```javascript
(async () => {
  const session = await elli.script.getObject("session");
  elli.script.subscribe("application", "login", async () => {
    const auth = await elli.script.getObject("auth");
    const user = await auth.getUser();
    console.log("[login] user:", user.firstName, user.lastName);
  });
})();
```

> Remember: this code must be transpiled to ES5 (Elli-CLI / Babel) before deployment.

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
  const fields = await loan.getFields(["14", "19", "1109"]); // Desktop only

  console.group("[LoanDebug] fields snapshot");
  console.table(Object.entries(fields).map(([id, val]) => ({ id, val })));
  console.groupEnd();
})();
```

### Setting Breakpoints in DevTools

Your plugin/tool code runs in its own sandboxed iframe, so it isn't found
under the page's normal script list:

1. Open DevTools (`F12`) with the loan already open.
2. Go to **Sources** → **Page** sub-tab.
3. Find your code under **Top**, in its own sandboxed page — look for an
   asset hosted at `asset-service-bucket-[server name].amazonaws.com`.
4. Click into it to set breakpoints; inspect variables by hovering, via the
   **Watch** panel, or by typing into the **Console**.

> **Critical limitation:** once you resume execution past a breakpoint, the
> console can no longer inspect your plugin's variables until execution is
> paused again inside your code. Don't expect to poke at state after clicking
> "Resume" — pause again first.

### Cleanup Checklist Before Committing

- [ ] Remove bare `console.log(value)` statements with no label
- [ ] Replace debug logs with a `DEBUG` flag or remove entirely
- [ ] Use `console.error()` for actual errors, not `console.log`
- [ ] Confirm no tokens, passwords, or PII are logged
- [ ] Replace multi-line related logs with `console.group()` blocks
- [ ] Confirm code is transpiled to ES5 before deployment

---

## Full Code Samples

### Read loan fields and call the Encompass API

```javascript
(async () => {
  const auth = await elli.script.getObject("auth");
  const loan = await elli.script.getObject("loan");
  const http = await elli.script.getObject("http");

  const tokenResult = await auth.getAccessToken();
  const loanId      = await loan.getField("364"); // Loan GUID

  console.log("[fetchLoan] loanId:", loanId);
  console.log("[fetchLoan] token present:", !!tokenResult.access_token);

  const loanData = await http.get(
    `https://api.elliemae.com/encompass/v3/loans/${loanId}`,
    tokenResult
  );

  console.group("[fetchLoan] response");
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

  let region = "Other";
  if (state === "CA") region = "West";
  else if (["NY", "NJ", "CT"].includes(state)) region = "Northeast";

  await loan.setFields({ "CX.REGION": region });
  await loan.commit();
  console.log("[setRegion] saved.");
})();
```

### React to login event and display user info

```javascript
(async () => {
  elli.script.subscribe("application", "login", async () => {
    const auth = await elli.script.getObject("auth");
    const user = await auth.getUser();
    console.log("[login] user:", user.firstName, user.lastName);
    document.getElementById("welcome").textContent = `Welcome, ${user.firstName}`;
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
| Looking for `getUser()` on `session` | It's on `auth`, not `session` — `session` is just key/value storage |
| Treating `getAccessToken()` as a string | It returns `{ access_token, token_type, host_name }` |
| Using `createAuthCode()` | Deprecated — use `getAccessToken()` |
| Calling `http.request(config)` | Doesn't exist — use `http.get/post/put/patch/delete()` |
| Calling `loan.save()` | Doesn't exist — use `loan.commit()` |
| `setFields` not persisting | Must call `loan.commit()` explicitly |
| Using a Desktop-only or Web-only method on the wrong platform | Check the Platform column before relying on a method |
| Slow work inside an interactive event (`precommit`, `close`) | Will time out (1s Web / up to 60s Desktop) — keep handlers fast |
| Wrong object ID casing | IDs are case-sensitive — `"Loan"` fails, use `"loan"` |
| Shipping ES6/ES2017 code as-is | Must transpile to ES5 with Elli-CLI first — no auto-transpile |
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
| 4460 | Total active borrower pair count |
| 4000:N | Borrower-pair-scoped fields — colon notation, zero-based pair index |
| CX.* | Custom Fields (prefix) |

---

## Relationship to Advanced Coding (VB.NET Rules)

This skill covers **JavaScript-based scripting** in Custom Forms, Custom Tools,
and Plugins. For **VB.NET-based business rules** (Field Triggers, Field Data Entry
validation, Milestone Completion, Loan Form Printing), see the
`encompass-advanced-coding` skill instead.

| Feature | Secure Scripting (this skill) | Advanced Coding |
|---------|-------------------------------|-----------------|
| Language | JavaScript (ES5 after transpilation) | VB.NET |
| Context | Custom Form / Tool / Plugin | Business Rules engine |
| Runs in | Guest browser (sandboxed iframe) | Server-side rule engine |
| Can call APIs | Yes (via `http` object, Web only) | No |
| Can modify UI | Yes (Custom Form controls / DOM in guest) | No |
| Triggered by | Events / page load | Field changes, milestones |
