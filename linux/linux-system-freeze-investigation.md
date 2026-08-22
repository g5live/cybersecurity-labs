# Linux System Freeze Investigation

## Overview

This case study documents an ongoing investigation into intermittent desktop freezes and severe responsiveness issues on a Pop!_OS Linux workstation.

The objective is not simply to record a fix, but to document the troubleshooting process: gathering evidence, isolating variables, testing hypotheses and distinguishing relevant events from unrelated system-log noise.

## Environment

- Pop!_OS 24.04 LTS
- Linux / Wayland desktop environment
- COSMIC desktop
- NVIDIA discrete GPU
- Single-monitor configuration
- Daily-use workstation

The behaviour became noticeable following a change from a dual-monitor to single-monitor configuration.

## Initial Symptoms

The system experienced intermittent periods of severe graphical slowdown and, on some occasions, complete loss of desktop responsiveness.

Observed behaviour included:

- Very slow graphical response
- Window movement and resizing becoming unresponsive
- Keyboard or mouse input occasionally becoming unavailable
- Forced shutdown required during complete freezes
- No obvious corresponding CPU or memory exhaustion

The inconsistent nature of the fault meant that reproducing and observing the failure was more useful than immediately assuming a specific cause.

## Initial Investigation

I began by checking whether normal resource exhaustion could explain the behaviour.

System monitoring showed no individual process consuming unusually high CPU or memory resources during the slowdown.

This reduced the likelihood that the problem was simply caused by an application exhausting normal system resources and shifted the investigation toward the graphics stack, desktop environment and system logs.

## Log Investigation

I used `journalctl` to inspect system events surrounding the periods where the slowdown or freeze occurred.

Rather than treating every warning or error as relevant, I began narrowing the logs to the time immediately before and during each observed incident.

Examples of events identified during investigation included:

- COSMIC compositor/display configuration errors
- Wayland/X11-related events
- NVIDIA Xid GPU errors
- Application and graphics-related warnings
- Unrelated background events including firewall and application-security messages

This highlighted an important troubleshooting principle:

**An error appearing in a log does not automatically mean it caused the observed failure.**

Timing, repetition and correlation with the actual symptoms are required before treating an event as significant.

## Graphics Investigation

Earlier logs contained an NVIDIA Xid error associated with a graphical application.

This made the NVIDIA graphics stack a reasonable investigation path, but not a confirmed root cause.

Further testing included changing how graphical applications were launched and observing whether system behaviour changed.

Running one Electron-based application with Vulkan disabled appeared to improve responsiveness during one period of testing.

This was treated as evidence worth investigating rather than proof that Vulkan or the application itself caused the wider system issue.

## Hardware Isolation

The system also contained integrated AMD graphics.

As part of reducing the number of variables involved in the investigation, the integrated GPU was disabled in firmware so the system operated using only the NVIDIA GPU connected to the display.

The purpose of this change was isolation rather than assuming that the AMD device was responsible.

## Reproducing the Behaviour

Later testing suggested that window-management activity could contribute to reproducing the symptoms.

During attempts to change COSMIC tiling behaviour and manually resize windows, the desktop began to stutter before becoming unresponsive.

Logs around this period contained COSMIC and display-related events, followed later by an NVIDIA Xid event.

This produced a stronger area for investigation:

**window/display operation → compositor activity → graphical slowdown → system freeze**

This remains a hypothesis rather than a confirmed causal sequence.

## Live Monitoring

## Repeated File-Chooser Reproduction

Continued live monitoring produced a more reproducible pattern during file downloads and save operations.

Across multiple separate file downloads, the following sequence was observed:

1. A file chooser or download operation was initiated.
2. `xdg-desktop-portal-cosmic` generated Wayland/file-chooser warnings.
3. The portal reported that the relevant file chooser dialog ID was no longer available.
4. `cosmic-comp` immediately logged a DRM texture-import failure:

`Failed to render texture ... import for wrong devices`

The repeated failures shared the same significant characteristics:

- Buffer size: `1280 × 800`
- Pixel format: `AB24`
- DRM render node involved
- Same DRM buffer modifier across repeated events
- Immediate timing correlation with COSMIC file-chooser activity

The sequence was reproduced during several separate CV downloads and again during an unrelated file download.

This substantially increases confidence that the event is reproducible rather than an isolated graphics warning.

## Revised Working Hypothesis

Earlier investigation concentrated broadly on the NVIDIA graphics stack and COSMIC compositor behaviour.

The repeated file-download tests now provide a more specific investigation path:

```text
File/download operation
        ↓
xdg-desktop-portal-cosmic
        ↓
Wayland file-chooser activity
        ↓
File chooser closes / disappears
        ↓
cosmic-comp attempts texture import
        ↓
DRM "import for wrong devices" failure

## Troubleshooting Methodology

The investigation currently follows this process:

```text
Observe symptom
      ↓
Record approximate time
      ↓
Inspect system resources
      ↓
Examine logs around the event
      ↓
Identify recurring signals
      ↓
Separate likely evidence from noise
      ↓
Form a hypothesis
      ↓
Change one variable
      ↓
Attempt reproduction
      ↓
Compare behaviour and logs
