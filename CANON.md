# Android Dojo Canon

**Status:** Foundational
**Version:** 1.1.0
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

## 3. System Architecture

Android Dojo is a system, not merely a collection of tutorials and scripts.

Its components communicate through explicit, testable contracts. Components must be independently understandable, observable, replaceable, and verifiable wherever practical.

The Dojo communication spine is **compatible with the established Organs communication architecture**. The Organs protocol reference is authoritative for that compatibility boundary.

The Dojo must not invent a competing service-discovery mechanism, message bus, correlation convention, telemetry transport, or common HTTP error convention when an established Organs contract already provides one.

### 3.1 Communication Layers

These layers are complementary and must not be treated as interchangeable:

- **Registry:** service discovery and liveness/heartbeat registration.
- **Communications BUS:** event publication, consumption, cursors, and dispatch.
- **Organ base:** common HTTP presentation, health/info endpoints, error envelopes, correlation propagation, and safety/risk integration.
- **Organ client:** discovery, registration, heartbeat, and organ-to-organ client behavior.
- **Correlation:** request-scoped `X-Correlation-ID` context.
- **Telemetry:** best-effort operational observation that must not turn a successful operation into failure.
- **Safety/risk gate:** authorization and risk control for mutating or risky operations.

Registry discovers services. The BUS moves events. The common organ layer presents services consistently. Correlation identifies related work. Telemetry observes it. Safety controls risky actions.

### 3.2 Discovery Rule

The Registry is the hardcoded service address in the communication architecture. Organ-to-organ addresses must be discovered through the Registry rather than hardcoded into components.

Components must refuse stale or non-live registrations rather than silently communicating with an obsolete service.

### 3.3 BUS Rule

The Communications BUS is the system event and dispatch layer.

Dojo components must use the established BUS semantics for inter-component events instead of creating a second message system.

The BUS is broadcast/pub-sub capable: consuming an event advances a consumer's own cursor and does not remove the event for other consumers.

Consumers must have stable identities when cursor persistence matters. Peek/read operations must not advance cursors. Dispatch behavior must use the established dispatch semantics and persistent dispatch state.

### 3.4 HTTP and Error Rule

Compatible Dojo organs use the established common HTTP surface, including:

- `/health`
- `/info`
- standard JSON error envelopes
- `X-Correlation-ID` generation, propagation, and return on errors

Mutating or risky requests pass the established safety/risk gate unless an explicit documented infrastructure exemption applies. GET/HEAD/OPTIONS behavior follows the established exemption convention.

Executive-approved operations carry `X-Executive-Approved` where that contract applies.

### 3.5 Telemetry Rule

Telemetry is independent of the safety gate.

Telemetry is best effort. A telemetry failure must never convert an otherwise successful organ operation into a failure.

Telemetry must preserve correlation and provenance and must avoid recursive telemetry noise using the established internal telemetry convention.

### 3.6 System Boundary

The Dojo distinguishes three things:

1. **Host system:** the computer running the Dojo services and Android tooling.
2. **Dojo system:** the software, curriculum, tools, organs, contracts, BUS activity, evidence, and safety machinery built by this repository.
3. **Target device:** the Android hardware being inspected, modified, recovered, or otherwise used as a laboratory target.

A host capability must never be confused with an Android target capability. Device-specific operations must identify their target explicitly.

## 4. Repository Architecture

The repository is organized into four conceptual layers:

```text
FOUNDATION
    Canon, schemas, terminology, communication contracts, safety rules
        ↓
CURRICULUM
    Lessons, labs, exercises, prerequisites
        ↓
TOOLING
    Scripts, validators, inspectors, helpers, system components
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

## 5. Standard Lesson Format

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

## 6. Lab Format

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

## 7. Command Standard

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

## 8. Device Identity

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

## 9. Risk Classification

| Level | Meaning |
|---|---|
| SAFE | Observation or reversible learning with negligible device risk. |
| LOW | Reversible operation with limited consequences. |
| MODERATE | Operation can cause data loss or a recoverable software failure. |
| HIGH | Operation can produce a serious brick, failed boot, or difficult recovery. |
| CRITICAL | Operation may permanently damage a device or require specialized recovery. |

Risk labels describe the operation, not the learner's experience level.

## 10. Evidence and Verification

The Dojo distinguishes between:

- **Observed:** directly reproduced or inspected.
- **Documented:** supported by authoritative external documentation.
- **Reported:** supplied by another person and not independently reproduced.
- **Hypothesis:** an explanation that remains unverified.

Do not present a hypothesis as fact.

## 11. Tool Contract

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

## 12. File Naming

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

## 13. Versioning

Canonical schemas and formats use semantic versioning:

```text
MAJOR.MINOR.PATCH
```

- **MAJOR:** incompatible structural or semantic change.
- **MINOR:** backward-compatible addition.
- **PATCH:** clarification or correction without changing meaning.

Lessons and labs receive stable IDs so links and references remain durable even if filenames change.

## 14. No Cargo Culting

The Dojo does not teach commands merely because they are common in forum posts.

Every procedure should answer:

> What does this change?
> Why are we doing it?
> How do we know it worked?
> What happens if it fails?
> How do we get back?

If those questions cannot be answered, the procedure is not ready for the curriculum.

## 15. Safety Boundaries

The Dojo may teach powerful techniques, including bootloader operations, image modification, recovery, kernel experimentation, and low-level Android internals.

It must not normalize reckless experimentation on someone's primary device.

Where a technique has meaningful brick or data-loss risk, the curriculum should first provide a safer simulation, inspection exercise, emulator workflow, disposable target, or recovery exercise when one is practical.

## 16. Contributions

Contributions should improve the learner's understanding, safety, reproducibility, or ability to recover from mistakes.

A contribution that works but cannot be explained is incomplete.

A contribution that documents a failure and what was learned from it can be valuable even when no code is produced.

## 17. Canon Changes

Changes to this document are architectural changes.

Before modifying Canon, consider:

- Does the change preserve the Dojo's mission?
- Does it simplify or complicate the learning path?
- Does it improve safety or reproducibility?
- Does it introduce a new standard where an existing one is sufficient?
- Will existing lessons remain understandable?
- Does it preserve established Organs compatibility where applicable?

Canon should evolve deliberately, not continuously.

---

**The purpose of the Dojo is not to prevent every mistake.**

It is to turn mistakes into controlled experiments, controlled experiments into understanding, and understanding into knowledge that can be shared.

**Study. Experiment. Recover. Document. Teach.**
