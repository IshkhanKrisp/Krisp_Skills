---
name: ncacskill
description: Investigate Krisp Noise Cancellation (NC) support reports using customer descriptions, Krisp logs, and optional audio recordings; explain evidence-backed findings in clear customer-facing language.
---

# Krisp Noise Cancellation Log Troubleshooting

## Purpose

Use this skill to investigate a customer's report about Krisp Noise Cancellation or related microphone and speaker behavior. Review the customer's description alongside the provided logs, identify the most likely cause supported by the evidence, and explain it clearly and empathetically to the customer.

Do not infer a cause from the symptom alone. Distinguish observed log facts from likely explanations, and state when the available evidence is inconclusive.

## Inputs and log files

- Start with the customer's report and exact symptom (for example, others cannot hear them, they cannot hear others, NC seems ineffective, low volume, or choppy audio).
- Review any supplied audio recording when it is relevant and available. Treat it as supporting evidence; do not claim to have listened to a recording that was not provided or could not be reviewed.
- Logs rotate by age. `kr_app.log`, `kr_audio_dm.log`, and `kr_media_sp.log` are the current logs; `.1` is the next older file, continuing through `.10`. Search across relevant rotated files when the reported call is not in the current log, and correlate events by timestamp and call/session.
- Use `systeminfo.json` or `system.json` when supplied for device, OS, hardware, and memory context. Review `userConfigs.json` only when the symptom points to a user-setting issue.

## Investigation workflow

### 1. Start with `kr_app` logs and identify the call

Begin with the most recent `kr_app.log`, then search older rotated files (`kr_app.1.log` through `kr_app.10.log`) if the reported call is not there. Correlate events by timestamp and call ID.

#### `[CALL_START]`

Find the `[CALL_START]` entries for the reported period. Microphone and speaker usage are logged separately, so there may be two entries for a call: one for the Krisp microphone and one for the Krisp speaker. For each entry, check:

- `AppName`: identify the application using the Krisp device. Determine whether it is the actual calling app. If it is not a calling app, inspect other `[CALL_START]` entries to identify the calling app where the Krisp microphone or speaker was used.
- `InitialNcState` and `InitialMuteState`: establish the initial NC-toggle and mute states. If the customer says NC did not work and `InitialNcState` is false, this may explain the report. If the customer says others could not hear them and the microphone's `InitialMuteState` is true, the microphone being muted may explain it. Check the matching microphone or speaker entry; do not assume the same state applies to both.
- Device: identify the selected actual microphone or speaker. If Krisp is using a built-in device instead of the customer's headset, this may affect audio quality or explain the symptom.
- `isMuted`: check the mute state recorded for the device.
- `bvcCompatibilityState`: check whether Background Voice Cancellation (BVC), also called Voice Isolation, is available for the selected device. If the customer describes unwanted voices or noise and the state is not `ALLOWED`, this may be relevant: customers may describe secondary voices as noise. Treat it as a possible explanation and consider the device and symptom context; do not present it as proven solely from this field.

Krisp microphone and Krisp speaker are virtual devices. The customer selects the actual microphone and speaker in the Krisp app, while selecting Krisp microphone and/or Krisp speaker as the corresponding input/output in the calling app. Confirm both sides of this route where the logs allow it.

#### `[CALL_END]`

Match the `[CALL_END]` to its `[CALL_START]` using `CALLid`. Review the final values of the same relevant properties, including device, NC state, mute state, and `isMuted`, to see whether the customer changed settings or devices during the call. Distinguish the initial state from the final state.

#### In-call actions and stream/device events

- `[UI_ACTION]`: identify actions or setting changes made in the Krisp app during the call and correlate them with the symptom and other events.
- `[STREAM_START]` and `[STREAM_END]`: establish whether and when a stream started or ended for the Krisp microphone or speaker.
- `DeviceManager.dumpStreams`: inspect the available streams. `Type: 0` is the microphone and `Type: 1` is the speaker; entries may show either or both.
- `DeviceManager.dumpDeviceList`: inspect the devices available to Krisp.
- `DeviceManager.dumpDeviceListChanges`: identify device changes during the session, such as a switch from a headset to a built-in microphone or speaker, which may be relevant to a change in audio quality.

Use timestamps across these records to build the sequence of events. Other `kr_app.log` records may also be relevant; this list is the initial checklist, not an exhaustive set of evidence.

#### BVC / Voice Isolation device compatibility

The Voice Isolation compatibility guidance applies to BVC. Krisp says Voice Isolation works with most headsets, with best results expected from a USB headset (USB-A or USB-C) with a half- to full-length boom microphone. Bluetooth and other headset types may work, but are not the recommended setup for best results. Compatible devices may be selected automatically; an unrecognized device may allow manual activation in **Settings > Noise Cancellation**, but voice or NC quality may be degraded. The device coverage list uses names reported by the operating system, and some entries cover models sharing a name prefix.

When a device's `bvcCompatibilityState` is not `ALLOWED`, use the customer's symptom and selected device to decide whether compatibility is a likely factor. See the [Voice Isolation compatible devices article](https://help.krisp.ai/hc/en-us/articles/7270378194972-Voice-Isolation-compatible-devices) for the approved device list and setup details.

### 2. Check `kr_audio_dm` volume events

In the matching `kr_audio_dm` logs, inspect `[VOLUME_CHANGED]` for microphone and speaker volume changes. Use `type: 0` for microphone and `type: 1` for speaker. Each event may identify two devices: the Krisp virtual device and the actual microphone or speaker. Use `dm_reporting.txt`, when provided, to map device IDs to device names and details.

Compare the levels for both devices and relate changes to the reported symptom: low speaker volume may explain why the customer could not hear others, while low microphone volume may explain why others could not hear the customer. This is supporting evidence; correlate it with the call route, mute state, and other logs.

In `kr_app.log`, also check `volumeControlMode`. `SYNC_WITH_KRISP` means the headset's volume is synchronized with Krisp's volume, so a reduction in the headset volume may also reduce the Krisp volume.

For low or changing volume, inspect `EnergyStats()` in `kr_media_sp` for input/output energy. For “I cannot hear others” or very quiet speaker audio, check Ducking Mode. The expected setting in these troubleshooting notes is `nothing`; a setting such as `ReduceBy80` may reduce speaker volume and should be reported as a possible contributing factor, not a proven cause by itself.

### 3. Check `kr_media_sp` energy and frame handling

#### `EnergyStats()`

Review inbound (speaker) and outbound (microphone) energy before and after Krisp processing. Compare the before/after values in the same stream and at the relevant time:

- If energy is present before processing and remains similar afterward, NC may be disabled or the audio may not be undergoing the expected processing. Verify the call's NC state, stream route, and processing path before concluding.
- If energy is present before processing but becomes zero or very small afterward, Krisp may have removed the sound. Compare with the customer's symptom and recording, if available, to determine whether this is expected noise suppression or unwanted loss of desired audio.
- If energy is zero before processing, there may be no signal reaching Krisp at that time. For the speaker this can mean no incoming audio; for the microphone it can mean the customer was not speaking, or there may be an input-device or routing problem. Check other timestamps and device/stream evidence before deciding.

#### Frame and queue statistics

Review the stream's frame and queue statistics, including `Len`, `Write`, `Read`, `Empty`, and especially `Overwrite`:

```text
Len: 38400 Avg: 5111
Write - Cnt: 584, Sz: 1979520, Avg: 3389
Overwrite - Cnt: 0, Bytes: 0, Avg: 0
Read - Cnt: 515, Sz: 1977600, Avg: 3840
Empty - Cnt: 0, Sz: 0, Avg: 0
```

An increasing or high `Overwrite` count indicates that frames are being overwritten before normal processing/consumption and may point to processing delays or dropped audio. Assess the count over the affected interval and correlate it with the customer's symptom; do not treat a nonzero count alone as proof of a cause. Investigate system capacity, especially CPU and memory, and network conditions only when the surrounding evidence makes them relevant.

Check available RAM in `systeminfo.json` when supplied. Krisp's echo cancellation requires at least 1.5 GB of free memory. A high overwrite count together with low available memory or hard system overload strengthens the case for a resource-related problem.

### 4. Check overload and profile/settings context in `kr_app`

- Inspect `[SYSTEM_OVERLOAD]`. `FilterId: Hard` with `State: Overloaded` is a concern and may correlate with dropped frames or processing issues. Soft overload is generally less concerning. Correlate the event's timestamp with media-stream statistics and the reported symptom.
- Review `AccountService.getProfileData` for the settings/profile information relevant to the issue. Use it as context alongside the call's actual initial/final states and UI actions, not as a replacement for evidence of what happened during the call.
- Compare overload events with available RAM information in `systeminfo.json` or `system.json`.

### 5. Validate device, system, and model context

- Check the last two calls' microphone and speaker devices when the issue may be device-specific.
- Use `systeminfo.json` to review the computer and device setup. Check headset compatibility if Background Voice Cancellation (BVC) is relevant.
- A 48 kHz sample rate and sufficient bandwidth are important for clean audio. For pronunciation or degraded-audio reports, device enhancements may be worth disabling as a troubleshooting test.
- Echo cancellation can operate without NC and may require the Krisp speaker path. BVC requires a compatible headset. Echo cancellation requires at least 1.5 GB of free memory.
- If logs suggest a model did not load, check a new session before concluding that model availability caused the issue. Record the selected model and observed quality/performance behavior.
- NC issues are commonly memory-related; Accent Conversion issues are more commonly CPU-related. Treat these as investigation hints, not conclusions.

### 6. Decide whether evidence is sufficient

Summarize the relevant evidence in time order and connect it to the reported symptom. State:

1. What the customer reported.
2. What the logs or recording directly show, with timestamps and filenames when possible.
3. The most likely explanation and how strongly the evidence supports it.
4. Any uncertainty or additional information needed.

If the logs do not explain the symptom, say so and request a relevant recording or targeted details (such as the affected call time and selected microphone/speaker). Reinstallations and updates can remove or reset `kr_app.log` history, so mention missing history when it affects the investigation.

## Reference cases

Use these cases as symptom-oriented examples, not as automatic diagnoses. Verify the affected call, device type, timestamps, and any relevant state changes in the customer's own logs.

| Customer report | Log evidence to look for | Interpretation / next check |
| --- | --- | --- |
| “I cannot hear the customer.” | `[CALL_START]` shows the speaker device is a monitor/display rather than the customer's headset. | The selected output may not be the intended speaker. Confirm the actual speaker selection and Krisp/calling-app route. |
| “Noise Cancellation doesn't work.” | `[CALL_START]` has `InitialNcState: false`. | NC was off at call start and may explain the report. Check `[UI_ACTION]` and `[CALL_END]` to see whether it was enabled later. |
| “Noise Cancellation doesn't work” or unwanted background voices are audible. | `[CALL_START]` has `bvcCompatibilityState: "BLOCKED"`. | The selected device may not support BVC/Voice Isolation. Customers may describe secondary voices as noise; confirm the device and symptom before attributing the issue to compatibility. |
| “I've recorded myself but can't hear the audio when I play it back.” | `[CALL_START]` shows the selected recording/input device is a display/monitor rather than the intended microphone. | Check the selected microphone and audio route for the recording. |
| “The customer can barely hear me.” | `[CALL_START]` shows a built-in microphone; `[VOLUME_CHANGED]` in `kr_audio_dm.log` shows low Krisp microphone volume. | Both the selected input device and Krisp microphone level may contribute. Confirm the device IDs with `dm_reporting.txt` and compare relevant volume events. |
| “I cannot hear the customer, Krisp is properly selected in Google Meet.” | `AudioService:onGraphInitError` reports `The working device is unhealthy` for `streamType: krisp_spk_inbound`. | The active speaker-side device/stream is reported unhealthy. Recommend changing the affected device, then verify the route and whether the error recurs. |
| “The buttons do not work.” | `AccountService.getProfileData` indicates that Noise Cancellation toggles are enforced as always on. | Account-level enforcement may prevent the customer from changing those toggles. Review the relevant enforcement settings at [account.krisp.ai](https://account.krisp.ai/) and confirm the intended agent/customer policy with the account administrator before recommending a change. |
| “The customer's voice is going in and out when I use Krisp.” | `AudioService:onAudioStreamCreated` reports `Ducking mode: ReduceBy80`; the volume-control log changes from `LOCK_ON_OPTIMAL_LEVEL` to `SYNC_WITH_KRISP`. | Check whether ducking is reducing speaker volume and whether the volume-control mode changed during the affected period. `LOCK_ON_OPTIMAL_LEVEL` is the expected mode in this troubleshooting workflow to help prevent AGC or other software volume reductions from affecting audio. Correlate the mode change with volume events before identifying it as the cause. |

## Core concepts and interpretation

- Audio is processed in 10 ms chunks.
- Noise Cancellation includes Background Voice Cancellation (BVC), Echo Cancellation, and Automatic Gain Control (AGC).
- The Media Stream Processor's Capturer creates predefined 10 ms streams and places them in the cache for processing. The Stream Manager and Device Manager are key components; follow the stream lifecycle: Create → Start → Stop → Delete.
- Use `kr_media_sp` frame and queue information to investigate dropped frames, overwrites, and processing delays.

## Customer response

Use clear, empathetic language and avoid internal jargon unless it helps explain the next step. Do not expose speculative diagnoses as facts. Tailor the response to the evidence; do not send a generic list of every possible cause.

### Confirmed muted Krisp microphone

When logs confirm that the Krisp microphone is muted during the call, explain that this is why the customer's voice is not being transmitted. A concise response may be:

> Your Krisp Microphone is muted, so your voice is not being sent to the meeting. Please unmute it and try another call.

Only use the present tense when the logs confirm that it remains muted. If the logs show only `InitialMuteState: true`, say it was muted at the start of the call; do not claim it remained muted unless later events establish that.

### Other findings or inconclusive logs

Adapt this structure to the case:

```text
Hi [Customer name],

Thanks for sharing the details and logs. We reviewed the logs for the call around [time]. They show [specific finding]. This [explains / may have contributed to] [reported symptom].

Please try [specific next step]. If the issue continues, please send us [targeted additional evidence or details].

Best,
Krisp Support
```

For additional Krisp Call Center AI product information, use the [Krisp Call Center AI Help Center](https://help.krisp.ai/hc/en-us/categories/4550555112860-Krisp-Call-Center-AI).
