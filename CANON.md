# Android Dojo Canon

**Status:** Foundational
**Version:** 1.0.0
**Authority:** This document defines the project's non-negotiable architectural and documentation rules.

> **Canon is law.**

Android Dojo is a training ground. Its architecture must therefore teach good engineering practice while making dangerous experimentation as controlled, explicit, and reproducible as practical.

## 1. Prime Directive

The Dojo exists to teach people how Android works and how to experiment responsibly. No lesson, tool, automation, or convenience feature may undermine that purpose.

When convenience conflicts with safety, clarity, or reproducibility, safety and clarity win.

## 2. The Four Rules

1. **Know the target.** Every hardware operation identifies the exact device, variant, Android version, and relevant firmware context.
2. **Know the operation.** Every destructive or potentially destructive operation explains what it changes before asking the learner to perform it.
3. **Know the recovery.** A risky lab documents the expected failure modes and a recovery path before the risky step.
4. **Leave a trail.** Experiments record enough information to reproduce, diagnose, and learn from the result.

## 3. Architecture

The repository is organized into four conceptual layers:

```text
FOUNDATION
    Canon, schemas, terminology, safety rules
        ↓
CURRICULUM
    Lessons, labs, exercises, prerequisites
        ↓
TOOLING
    Scripts, validators, inspectors, helpers
        ↓
REFERENCE
    Device notes, recovery procedures, examples, case studies
```

### Foundation

Foundation material defines the language and rules used everywhere else. It must remain small, stable, and authoritative.

### Curriculum

Curriculum teaches concepts and procedures. Lessons may reference tools, but a lesson must remain understandable without reverse-engineering a script.

### Tooling

Tools automate deterministic work. Tools must fail loudly rather than silently making assumptions about a device.

### Reference

Reference material records device-specific knowledge and real-world lessons. It must never be treated as universal instruction unless explicitly marked as such.

## 4. Standard Lesson Format

Every instructional lesson uses this structure:

```text
# Lesson: <title>

ID: AD-<belt>-<number>
Level: <White|Yellow|Orange|Blue|Black>
Risk: <SAFE|LOW|MODERATE|HIGH|CRITICAL>
Prerequisites:
Target:
Estimated time:

## Objective

## What You Will Learn

## Before You Begin

## Concepts

## Procedure

## Verification

## Expected Result

## Failure Modes

## Recovery

## Cleanup

## What You Learned

## Next Lesson
```

The order may only be changed when the lesson genuinely requires it.

## 5. Lab Format

Labs are practical exercises and use a stricter contract:

```text
# Lab: <title>

ID: LAB-<belt>-<number>
Risk: <SAFE|LOW|MODERATE|HIGH|CRITICAL>
Target Type: <emulator|virtual|spare-device|specific-device>
Prerequisites:
Required Files:
Required Tools:

## Goal
## Safety Gate
## Setup
## Experiment
## Observe
## Verify
## Break/Fault Exercise
## Recovery
## Reset
## Results
## Lessons Learned
```

A lab that intentionally introduces failure must clearly label the failure as intentional and provide a recovery procedure.

## 6. Command Standard

Commands must be presented with:

- A description of what the command does.
- The working directory when relevant.
- Required privileges.
- Expected output or observable effect when useful.
- A warning before destructive operations.

Prefer explicit commands over magical one-liners.

Never hide a destructive operation behind an innocuous script name.

Example:

```bash
# Inspect the connected device. This does not modify it.
adb devices -l
```

Destructive commands require a visible warning immediately before the command.

## 7. Device Identity

Device-specific material must identify the target as precisely as practical.

Use this metadata format:

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

Never assume two phones are interchangeable merely because they share a product name.

## 8. Risk Classification

| Level | Meaning |
|---|---|
| SAFE | Observation or reversible learning with negligible device risk. |
| LOW | Reversible operation with limited consequences. |
| MODERATE | Operation can cause data loss or a recoverable software failure. |
| HIGH | Operation can produce a serious brick, failed boot, or difficult recovery. |
| CRITICAL | Operation may permanently damage a device or require specialized recovery. |

Risk labels describe the operation, not the learner's experience level.

## 9. Evidence and Verification

The Dojo distinguishes between:

- **Observed:** directly reproduced or inspected.
- **Documented:** supported by authoritative external documentation.
- **Reported:** supplied by another person and not independently reproduced.
- **Hypothesis:** an explanation that remains unverified.

Do not present a hypothesis as fact.

## 10. Tool Contract

Every repository tool should have:

- A single clear purpose.
- Explicit inputs.
- Predictable outputs.
- Useful error messages.
- No silent device selection.
- No destructive default behavior.
- A `--help` interface when appropriate.
- A dry-run or inspection mode where practical.

Tools must not silently guess a device, partition, slot, firmware, or file.

## 11. File Naming

Use lowercase kebab-case for ordinary documentation and lesson filenames:

```text
unlocking-the-bootloader.md
inspect-boot-image.md
recover-from-bootloop.md
```

Stable canonical documents may use uppercase names when their role is repository-wide and unmistakable:

```text
README.md
CANON.md
LICENSE
CONTRIBUTING.md
CODE_OF_CONDUCT.md
```

## 12. Versioning

Canonical schemas and formats use semantic versioning:

```text
MAJOR.MINOR.PATCH
```

- **MAJOR:** incompatible structural or semantic change.
- **MINOR:** backward-compatible addition.
- **PATCH:** clarification or correction without changing meaning.

Lessons and labs receive stable IDs so links and references remain durable even if filenames change.

## 13. No Cargo Culting

The Dojo does not teach commands merely because they are common in forum posts.

Every procedure should answer:

> What does this change?
> Why are we doing it?
> How do we know it worked?
> What happens if it fails?
> How do we get back?

If those questions cannot be answered, the procedure is not ready for the curriculum.

## 14. Safety Boundaries

The Dojo may teach powerful techniques, including bootloader operations, image modification, recovery, kernel experimentation, and low-level Android internals.

It must not normalize reckless experimentation on someone's primary device.

Where a technique has meaningful brick or data-loss risk, the curriculum should first provide a safer simulation, inspection exercise, emulator workflow, disposable target, or recovery exercise when one is practical.

## 15. Contributions

Contributions should improve the learner's understanding, safety, reproducibility, or ability to recover from mistakes.

A contribution that works but cannot be explained is incomplete.

A contribution that documents a failure and what was learned from it can be valuable even when no code is produced.

## 16. Canon Changes

Changes to this document are architectural changes.

Before modifying Canon, consider:

- Does the change preserve the Dojo's mission?
- Does it simplify or complicate the learning path?
- Does it improve safety or reproducibility?
- Does it introduce a new standard where an existing one is sufficient?
- Will existing lessons remain understandable?

Canon should evolve deliberately, not continuously.

---

**The purpose of the Dojo is not to prevent every mistake.**

It is to turn mistakes into controlled experiments, controlled experiments into understanding, and understanding into knowledge that can be shared.

**Study. Experiment. Recover. Document. Teach.**
