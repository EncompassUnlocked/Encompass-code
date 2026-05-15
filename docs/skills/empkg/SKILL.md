---
name: empkg
description: "Use this skill whenever the user wants to create, edit, update, or repair an Encompass .empkg package file. This includes adding custom fields to the manifest, remapping field IDs in form controls, fixing field naming issues (dot count, character length), updating .emfrm form HTML, or repackaging an existing .empkg. Trigger any time the user mentions .empkg, .emfrm, Encompass custom fields, manifest.xml, or wants to add/rename/remap fields in an Encompass custom input form."
---

# EMPKG Skill

A guide for creating, editing, and repairing Encompass `.empkg` package files — including the `manifest.xml` field registry and the `.emfrm` form HTML.

---

## What is an .empkg?

An `.empkg` file is a ZIP archive that Encompass imports to register custom fields and deploy custom input forms. It contains exactly two files:

```
UW Matrix.empkg
├── manifest.xml       ← Custom field definitions
└── UW Matrix.emfrm   ← Custom form (also a ZIP containing FORM.htm)
```

The `.emfrm` is itself a ZIP archive containing `FORM.htm` — an HTML file that defines the form layout and wires each control to an Encompass field via `emid="..."` attributes.

---

## Encompass Field ID Rules

These rules are enforced by Encompass at import time. Violations cause fields to be silently skipped.

| Rule | Limit | Example |
|---|---|---|
| Max character length | 29 chars | `CX.ELEVATE.DSCLTV.STATUS` ✅ |
| Prefix | Must start with `CX.` | `CX.EMBARK.LTV.STATUS` ✅ |

**Common mistakes:**

- `CX.ELEVATE.DSCR.LTV.OVERRIDE` — 30 chars ❌ → shorten to `CX.ELEVATE.DSCLTV.OVERRIDE` ✅

**Field naming convention:**
```
CX.{PRODUCT}.{DOCTYPE+SECTION}.{SUFFIX}
```
Where:
- `PRODUCT` = product code (ELEVATE, EMBARK, EMPOWER, ENRICH)
- `DOCTYPE+SECTION` = merged doc type + section (e.g., `DSCLTV` = DSCR + LTV)
- `SUFFIX` = STATUS | OVERRIDE | ORNOTE | ORNOTES

---

## Step 1 — Inspect the .empkg

```bash
# List contents
unzip -l MyPackage.empkg

# Read manifest fields
unzip -p MyPackage.empkg manifest.xml | grep "Field id"

# Count fields
unzip -p MyPackage.empkg manifest.xml | grep -c "Field id"

# Check field dot counts and lengths
unzip -p MyPackage.empkg manifest.xml | grep "Field id" \
  | sed 's/.*id="//;s/".*//' \
  | awk '{print length, $0}' | sort -rn | head -20
```

---

## Step 2 — Extract for Editing

```bash
# Extract .empkg
mkdir -p work && cp MyPackage.empkg work/ && cd work
unzip -o MyPackage.empkg

# Extract .emfrm (it's also a ZIP)
mkdir -p emfrm_extracted
unzip -o "UW Matrix.emfrm" -d emfrm_extracted
# Now edit: work/manifest.xml and work/emfrm_extracted/FORM.htm
```

---

## Step 3 — Add Fields to manifest.xml

Fields are declared inside `<CustomFieldList>...</CustomFieldList>`.

**Field type reference:**

| Type | Usage |
|---|---|
| `STRING` | Text output fields — always add `maxlength` |
| `Y/N` | Checkbox override fields — no maxlength needed |

**Template for a full section set (STATUS + OVERRIDE + ORNOTE):**
```xml
<Field id="CX.PRODUCT.DSCSECTION.STATUS"   desc="Product DSCR Matrix: Section Check Results"    type="STRING" maxlength="300" />
<Field id="CX.PRODUCT.DSCSECTION.OVERRIDE" desc="Product DSCR Matrix: Manager Section Override"  type="Y/N" />
<Field id="CX.PRODUCT.DSCSECTION.ORNOTE"   desc="Product DSCR Matrix: Reason for Override"       type="STRING" maxlength="300" />
```

**Insert before the closing tag:**
```python
# Python pattern for safe insertion
new_fields = '    <Field id="CX.EXAMPLE.DSCLTV.STATUS" desc="..." type="STRING" maxlength="300" />\r\n'
content = content.replace('  </CustomFieldList>', new_fields + '  </CustomFieldList>')
```

**Always verify after adding:**
```bash
unzip -p MyPackage.empkg manifest.xml | grep "Field id" \
  | sed 's/.*id="//;s/".*//' \
  | awk -F'.' '{print NF-1, $0}' | sort -rn | head -10
# All new fields should show 3 in the first column
```

---

## Step 4 — Remap Form Controls in FORM.htm

Each form control has an `emid="..."` attribute that links it to an Encompass field. To remap a control, update that attribute.

**Find controls by panel:**
```bash
grep -n "pnlElevateDSCR.*emid=\"CX\." emfrm_extracted/FORM.htm \
  | sed 's/.*emid="//;s/".*//' | sort -u
```

**Safe surgical replacement using control ID prefix:**

Because every control ID contains the panel name (e.g., `pnlElevateDSCR`), you can remap safely without touching other panels:

```python
with open('emfrm_extracted/FORM.htm', 'r', encoding='latin-1') as f:
    content = f.read()

replacements = [
    # (old_string, new_string) — include control ID to be surgical
    ('pnlElevateDSCRltvst\" emid=\"CX.ELEVATE.LTV.STATUS\"',
     'pnlElevateDSCRltvst\" emid=\"CX.ELEVATE.DSCLTV.STATUS\"'),
    ('pnlElevateDSCRltvoc\" emid=\"CX.ELEVATE.LTV.OVERRIDE\"',
     'pnlElevateDSCRltvoc\" emid=\"CX.ELEVATE.DSCLTV.OVERRIDE\"'),
    # ... add all sections
]

for old, new in replacements:
    if old in content:
        content = content.replace(old, new)
        print(f'✅ {old.split("emid=")[1]} → {new.split("emid=")[1]}')
    else:
        print(f'⚠️  NOT FOUND: {old.split("emid=")[1]}')

with open('emfrm_extracted/FORM.htm', 'w', encoding='latin-1') as f:
    f.write(content)
```

**Always verify after remapping:**
```bash
# Confirm target panel is updated
grep "pnlElevateDSCR.*emid=\"CX\." emfrm_extracted/FORM.htm \
  | sed 's/.*emid="//;s/".*//' | sort -u

# Confirm other panels are untouched
grep "pnlElevateNonQM.*emid=\"CX\." emfrm_extracted/FORM.htm \
  | sed 's/.*emid="//;s/".*//' | sort -u
```

---

## Step 5 — Repack

**Always repack in this order — emfrm first, then empkg:**

```bash
# 1. Repack the .emfrm (form ZIP)
cd work
zip -j "UW Matrix.emfrm" emfrm_extracted/FORM.htm

# 2. Repack the .empkg (package ZIP)
zip -j MyPackage_v2.empkg manifest.xml "UW Matrix.emfrm"

# 3. Quick sanity check
unzip -p MyPackage_v2.empkg manifest.xml | grep -c "Field id"
echo "Fields in package: $(above output)"
```

---

## Common Section Field Map

For UW Matrix packages, each product × doc type combination needs this standard set of 9 fields per section (LTV, MTG, RES, CO, FN, PROP, CRED) plus DSR and CTC:

| Suffix | Type | maxlength |
|---|---|---|
| `.STATUS` | STRING | 300 |
| `.OVERRIDE` | Y/N | — |
| `.ORNOTE` | STRING | 300 |
| `.ORNOTES` (LTV only) | STRING | 500 |

**CTC rollup field:**
```xml
<Field id="CX.PRODUCT.DSCCTC.STATUS" desc="Product DSCR Matrix: CTC Gate" type="STRING" maxlength="300" />
```

---

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| Fields not appearing after import | 4-dot field IDs or >29 chars | Merge doc+section segment, recount |
| Fields appear but wrong panel writes to them | Multiple panels share same `emid` | Remap DSCR panel controls to DSC-prefixed fields |
| Wrong data in a panel | Panel control points to another product's field | Check `emid=` attributes with panel-scoped grep |
| Import succeeds but no new fields | Old `.empkg` version re-imported | Delete old package, reimport fresh download |
| `latin-1` encoding errors | FORM.htm uses Windows-1252 encoding | Always open/write with `encoding='latin-1'` |

---

## Full Workflow Checklist

- [ ] Copy `.empkg` to working directory
- [ ] `unzip -l` to confirm structure
- [ ] Extract `.empkg` → get `manifest.xml` + `.emfrm`
- [ ] Extract `.emfrm` → get `FORM.htm`
- [ ] Check all existing field dot counts and lengths
- [ ] Add new fields to `manifest.xml` (inside `<CustomFieldList>`)
- [ ] Verify new fields: 3 dots, ≤29 chars
- [ ] Remap `FORM.htm` controls using panel-scoped replacement
- [ ] Verify target panel remapped, other panels untouched
- [ ] Repack `.emfrm` first, then `.empkg`
- [ ] Final field count check
- [ ] Deliver to user as `PackageName_vN.empkg`
