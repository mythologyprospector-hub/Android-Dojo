# AD-white-001 — Establish a Device Dossier

- **Belt:** White
- **Risk:** SAFE
- **Status:** Foundational

## Objective

Learn to identify the target device and record evidence before attempting any modification.

## What You Will Learn

- Why exact device identity matters.
- The difference between observed facts and assumptions.
- How to create a minimal device dossier.
- Why a dossier must be updated when the target changes.

## Before You Begin

- Use an emulator, spare device, or a device you are authorized to inspect.
- Do not unlock a bootloader, erase data, flash images, or change system state for this lesson.
- Have a place to save the dossier and its evidence.

## Concepts

A **device dossier** is a structured record of the target and the evidence used to identify it. At minimum, record:

- Manufacturer
- Model
- Regional or hardware variant, when available
- Codename, when available
- Android version
- Build identifier
- Bootloader state, when observable
- Active slot, when applicable
- Date and time of observation
- Commands, tools, or screens used as evidence

Use these evidence labels:

- **Observed:** directly seen or returned by a tool.
- **Documented:** supported by an authoritative document.
- **Reported:** supplied by another person or source.
- **Hypothesis:** plausible but not yet confirmed.

Never turn a hypothesis into a device identity.

## Procedure

1. Select one authorized target and give it a local dossier name.
2. Record the manufacturer and model from the device settings or another direct observation.
3. Record the Android version and build identifier.
4. Record the variant, codename, bootloader state, and slot only when evidence supports them.
5. For every field, mark the evidence status and record its source.
6. Mark unknown fields as `unknown`; do not fill gaps by guessing.
7. Save the dossier without making changes to the device.

## Verification

Confirm that:

- The dossier names one target unambiguously enough for the current lesson.
- Every populated field has an evidence source.
- Unknown values remain explicitly unknown.
- No write, unlock, erase, or flash operation was performed.

## Expected Result

A saved dossier exists for one authorized target, with observed facts separated from reported information and hypotheses.

## Failure Modes

- **Multiple identities appear:** stop and record the conflict; do not choose one arbitrarily.
- **A field cannot be verified:** mark it `unknown` and continue only with safe inspection.
- **A command proposes a write or erase:** stop before execution and classify the operation.

## Recovery

No device-state recovery should be required because this lesson is read-only. If an unexpected change occurred, stop work, document exactly what happened, and do not continue until the state is understood.

## Cleanup

- Save the dossier in the designated lesson workspace.
- Remove temporary output only after the dossier contains the required evidence.
- Do not delete device data or alter device state.

## What You Learned

A safe workflow starts by proving what the target is. The dossier is the reference point for later diagnosis, planning, verification, and audit reporting.

## Next Lesson

The next lesson should teach how to classify an operation by risk before running it.
