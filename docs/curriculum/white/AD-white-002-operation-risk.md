# AD-white-002 — Classify an Operation Before Execution

- **Belt:** White
- **Risk:** SAFE
- **Status:** Foundational

## Objective

Learn to classify a proposed action before running it and to stop when its effects or recovery path are unclear.

## What You Will Learn

- The difference between read-only, reversible or recoverable, and destructive or boot-critical work.
- Why a command must be understood before it is executed.
- How to document expected effects and recovery requirements.
- How to recognize ambiguity as a reason to stop.

## Before You Begin

- Use written examples, documentation, or a simulation.
- Do not execute any command that changes a device.
- Do not test a proposed action on a daily-driver device.

## Concepts

Classify every proposed operation before execution:

- **Read-only:** observes or collects information without intentionally changing device state.
- **Reversible/recoverable:** changes state but has a known and practical restoration path.
- **Destructive/boot-critical:** may erase data, affect boot, alter partitions, or leave the device difficult to recover.

A classification is incomplete when the target, inputs, expected effect, or recovery path is unknown.

The safest default for ambiguity is to stop and gather evidence. Never infer safety from a command's short length, popularity, or presence in an online guide.

## Procedure

1. Select a proposed operation from documentation or a simulated example.
2. Identify the exact target to which the operation would apply.
3. Describe what the operation reads, changes, creates, erases, or replaces.
4. Identify prerequisites and required privileges.
5. Assign one risk class: read-only, reversible/recoverable, or destructive/boot-critical.
6. Record the expected result and the evidence needed to verify it.
7. Record the recovery method, including what must be available before execution.
8. If any material detail is unknown, mark the operation **blocked pending evidence**.
9. Do not execute the operation as part of this lesson.

## Verification

Confirm that the operation record includes:

- Exact target
- Purpose
- Inputs
- Expected effect
- Risk class
- Prerequisites
- Verification method
- Recovery method
- Any unresolved uncertainty

## Expected Result

A proposed operation has a documented risk classification and a clear decision to remain blocked or proceed only after the required evidence and safeguards exist.

## Failure Modes

- **Target is ambiguous:** stop and identify the target.
- **Input file origin is unclear:** do not use it; obtain authoritative provenance.
- **Recovery depends on unavailable firmware or tools:** mark the operation blocked.
- **Expected effect is uncertain:** do not execute; gather evidence.
- **The operation combines several actions:** split it into separately classified steps.

## Recovery

No device-state recovery should be required because this lesson does not execute an operation. If an operation was accidentally started, stop safely, preserve logs, and document the exact point reached before taking further action.

## Cleanup

- Save the operation record with the lesson materials.
- Label simulated or example operations clearly.
- Do not run unclassified commands against real hardware.

## What You Learned

Classification is a safety gate, not paperwork after the fact. A proposed operation must be understood, bounded, verifiable, and recoverable before execution is considered.

## Next Lesson

The next lesson should introduce evidence collection and verification as separate steps in a read-only inspection workflow.
