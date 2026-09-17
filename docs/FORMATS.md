# Android Dojo Standard Formats

**Status:** Foundational
**Version:** 1.0.0
**Authority:** Secondary to `CANON.md`; these formats implement Canon.

## Purpose

This document is the quick-reference specification for creating new Dojo lessons, labs, device records, experiments, and tools.

## 1. Lesson Metadata

```yaml
id: AD-<belt>-<number>
level: White
risk: SAFE
prerequisites: []
target: "<target description>"
estimated_time: "<duration>"
```

## 2. Lab Metadata

```yaml
id: LAB-<belt>-<number>
risk: LOW
target_type: emulator
prerequisites: []
required_files: []
required_tools: []
```

## 3. Device Record

```yaml
device:
  manufacturer: "<manufacturer>"
  model: "<model>"
  variant: "<variant or region>"
  codename: "<codename>"
  android_version: "<version>"
  build: "<build identifier>"
  bootloader_state: "<locked|unlocked|unknown>"
  slot: "<a|b|unknown>"
```

## 4. Experiment Record

```yaml
experiment:
  id: EXP-<year>-<number>
  date: "YYYY-MM-DD"
  operator: "<name or handle>"
  target: "<device or environment>"
  objective: "<what is being tested>"
  starting_state: "<known state>"
  hypothesis: "<testable expectation>"
  result: "<observed result>"
  evidence: "<logs, hashes, screenshots, measurements, or references>"
  recovery_status: "<not-needed|completed|failed|unknown>"
```

## 5. Tool Documentation

Every tool should document:

```text
Name
Purpose
Inputs
Outputs
Dependencies
Privileges
Read-only mode
Destructive operations
Failure behavior
Examples
Recovery considerations
```

## 6. Evidence Labels

Use one of these labels when describing technical claims:

- `OBSERVED` — reproduced directly.
- `DOCUMENTED` — supported by authoritative documentation.
- `REPORTED` — reported by another party and not independently reproduced.
- `HYPOTHESIS` — proposed explanation awaiting verification.

## 7. Risk Labels

Use exactly one:

`SAFE` · `LOW` · `MODERATE` · `HIGH` · `CRITICAL`

## 8. Status Labels

Use exactly one where a lesson or tool needs lifecycle state:

`PLANNED` · `DRAFT` · `EXPERIMENTAL` · `VERIFIED` · `DEPRECATED`

`VERIFIED` means the procedure has been tested under the conditions documented by the lesson. It does not mean the procedure is universally safe for every Android device.

## 9. Standard Procedure Sequence

Where applicable, procedures should follow:

```text
IDENTIFY
  ↓
BACK UP
  ↓
INSPECT
  ↓
EXPLAIN
  ↓
EXECUTE
  ↓
VERIFY
  ↓
RECOVER (if necessary)
  ↓
DOCUMENT
```

Skipping a stage must be intentional and documented.

## 10. Hashes and Artifacts

When a binary artifact matters to reproducibility, record its cryptographic hash. Prefer SHA-256 unless a stronger project requirement exists.

```text
artifact: <filename>
sha256: <64 hexadecimal characters>
source: <where it came from>
version: <version/build>
```

Never identify a binary solely by filename when a hash can remove ambiguity.

## 11. Standard Directory Roles

```text
/docs/        Canonical documentation and specifications
/docs/...     Curriculum and reference documentation
/labs/        Hands-on exercises
/tools/       Deterministic helper tooling
/examples/    Small, self-contained examples
```

Do not create a new top-level directory without a clear architectural reason.
