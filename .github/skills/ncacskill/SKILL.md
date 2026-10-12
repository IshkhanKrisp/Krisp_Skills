---
name: ncacskill
description: Investigate Krisp Noise Cancellation and related support reports using customer descriptions, Krisp logs, and optional audio recordings; provide technical findings and a complete customer email based on the matching support template.
---

# Krisp Noise Cancellation Log Troubleshooting

## Purpose

Use this skill to investigate customer reports covered by its troubleshooting guidance and template library, including Noise Cancellation, Accent Conversion, Voice Translation, and related microphone, speaker, and CX Bridge behavior. Review the customer's description alongside the provided logs and any relevant audio, identify the most likely cause supported by the evidence, and provide both internal technical findings and a complete, clear, empathetic customer email.

Do not infer a cause from the symptom alone. Distinguish observed log facts from likely explanations, and state when the available evidence is inconclusive.

## Inputs and log files

- Start with the customer's report and exact symptom (for example, others cannot hear them, they cannot hear others, NC seems ineffective, low volume, or choppy audio).
- Review any supplied audio recording when it is relevant and available. Treat it as supporting evidence; do not claim to have listened to a recording that was not provided or could not be reviewed.
- Logs rotate by age. `kr_app.log`, `kr_audio_dm.log`, and `kr_media_sp.log` are the current logs; `.1` is the next older file, continuing through `.10`. Search across relevant rotated files when the reported call is not in the current log, and correlate events by timestamp and call/session.
- Use `systeminfo.json` or `system.json` when supplied for device, OS, hardware, and memory context. Review `userConfigs.json` only when the symptom points to a user-setting issue.

## Common Accent Conversion issue patterns

Use these as symptom-oriented patterns rather than automatic diagnoses. Always confirm the affected call, timestamps, and logging context before settling on a cause.

| Issue pattern | Typical customer wording | Common causes to rule in/out | Typical evidence to check |
| --- | --- | --- | --- |
| Robotic or “AI” voice | “caller ask if i am a real person,” “sounds robotic,” “AI voice” | Monotone speech, CPU overload, model or profile issue, overprocessing | `kr_app.log` call state, AC mode/profile selection, `[SYSTEM_OVERLOAD]`, `kr_media_sp.log` energy/processing stats, audio sample review |
| Audio cutting in and out | “my voice is breaking/cracking while I am talking,” “line breaking in and out” | Overprocessed audio, BVC/NC processing too aggressive, low-quality headset or microphone, bad device route, CPU overload | `kr_app.log` device route, `kr_audio_dm.log` volume changes, `kr_media_sp.log` overwrite/dropped frames, `[SYSTEM_OVERLOAD]` |
| Audio crackling / static | “bad connection,” “static line,” “crackling sound,” “extreme static” | Poor original mic capture, headset not compatible with Voice Isolation, weak input device, bad route or processing | Device compatibility state, mic model info, audio sample before/after processing, BVC compatibility result |
| Muffled / underwater voice | “I sound robotic and with static as if I was on a radio station,” “I sound underwater” | Unhealthy or low-quality headset, rare model issue, processing too aggressive | Headset compatibility state, microphone model info, model path, audio sample review |
| Low/unstable volume | “I sound too low,” “customers say my volume is impossible to understand,” “my audio keeps dropping in and out” | Mic volume too low, AGC, wrong volume-control mode, headset volume sync | `kr_app.log` `setVolumeControlMode`, `kr_audio_dm.log` `[VOLUME_CHANGED]`, device and selected mic state |
| No audio from customer | “No audio coming from the customer,” “sometimes during the call, caller cannot hear me” | Mute state, wrong selected mic/speaker, mic route issue, device failure | `[CALL_START]`, `[CALL_END]`, mute state, selected mic/speaker, stream start/end |
| Audio delay | “there is a strong delay that can lead to talking over each other” | Poor network, wireless headset, overloaded system or overprocessed audio | Check network quality when available, `kr_app.log` system state, call route and processing state |
| Incorrect phone conversion / distorted words | “some words come out not right,” “I am sounding too robotic,” “words get lost” | Low bandwidth, pronunciation/tone issues, overprocessing, wrong model selection | Audio sample before processing, `kr_media_sp.log` energy stats, device bandwidth and model info |
| Accent Conversion not applied / wrong profile | “Accent Conversion turned off,” “switching from Voice Profiles to Voice Preservation,” “test AC recording doesn’t sound right” | Incorrect mode selected, profile/model not loaded, route mismatch, user config or feature toggle changed | `getProfileData`, `onChangeAccentProcessorMode`, `changeAccentProcessorSettings`, model path, mode selection, test recording details |
| CPU or memory pressure | “the call is choppy,” “quality drops during heavy CPU use” | Hard or soft overload, high utilization, low memory, GPU/CPU competition | `kr_app.log` `[SYSTEM_OVERLOAD]`, `kr_native_utils.log` utilization dumps, `kr_media_sp.log` frame overwrites |

## Log checks we usually do for Accent Conversion tickets

Check the logs in the order below, then connect them to the affected call window. Use timestamps to link the data to call start and end events and to make sure you are sharing only relevant findings with the customer.

### 1. Device and compatibility state

Check the following in `kr_app.log` and related device logs:

- `[CALL_START]` and `[CALL_END]`: match the call start/end using the call ID; confirm which actual microphone/speaker was selected, and whether Krisp or the app was using the intended route.
- `InitialNcState`, `InitialMuteState`, `isMuted`: verify whether NC or mute was on at the start of the call and whether it changed during the call.
- `bvcCompatibilityState`: look for `ALLOWED`, `BLOCKED`, or `USER_CHOICE_ALLOWED`. If the customer is on a headset that is not supported, it can explain voice quality or unwanted secondary voices.
- `DeviceInfo`, actual device name, and the selected model: confirm that the selected microphone or speaker is the one the customer expects to use.

Example checks:

- `DeviceInfo: {"modelId":"...","manufacturer":"...","bvcCompatibilityState":"BLOCKED"}`
- `AudioService:onChangeAccentProcessorMode` and `AccentProcessorState` changes
- `AccountService.getProfileData` showing `"accent_in"` / `"accent_out"` or `"voiceProfiles"` enabled/disabled

### 2. Accent Conversion mode, profile, and model state

For Accent Conversion, inspect the model and profile state at call start, during the call, and in the test AC flow.

Look for:

- `selectAccentVoice`, `changeAccentProcessorMode`, `changeAccentProcessorSettings`
- `accentState`, `switcherState`, `modelName`, `voiceName`, `voicePitch`, `voiceEmbedding`, `noiseSuppressionLevel`
- `Voice Profiles` mode vs `Voice Preservation`, and whether the selected profile is actually available
- The model path and model name saved on the device, especially if a specific model was loaded or changed

Examples:

- `AudioService:onAccentProcessorStateChange: accent available: true, accentState: {"switcherState":"false","switcherDisabled":"false"}`
- `changeProcessorSettings ... modelName: "voicePitch" / "voiceEmbedding" / "noiseSuppressionLevel"`
- `CreateARProcessor() ... modelPath: ...\resources\models\audio\air_inbound_wb_c_3_9_2_3_0.kef`

If the customer reports a profile or model issue, check whether the user changed the mode or profile halfway through the call and whether the test AC flow also shows the same result.

### 3. Volume, AGC, and volume-control checks

Check `kr_audio_dm.log` for `[VOLUME_CHANGED]` and `kr_app.log` for the volume-control mode.

Typical checks:

- `AudioService:setVolumeControlMode: deviceType: 0, volumeControlMode: LOCK_ON_OPTIMAL_LEVEL` or `SYNC_WITH_KRISP`
- `[VOLUME_CHANGED]` events firing repeatedly for the microphone or speaker
- `Automatic Gain Control` being active on the microphone or in the app
- Whether the user is on `LOCK_ON_OPTIMAL_LEVEL` versus `SYNC_WITH_KRISP`

Important note: if `[VOLUME_CHANGED]` is spammed, AGC is very likely involved. Also check whether the system is reducing or changing volume after processing rather than before it. 

### 4. Overload, CPU, RAM, and GPU checks

Check for hard or soft overloads and utilization patterns in `kr_app.log` and `kr_native_utils.log`.

Look for:

- `[SYSTEM_OVERLOAD]` in `kr_app.log`
- `AudioService:handleModeAutoSwitching` to `BestPerformance`
- `DumpUtilizationData` and CPU/RAM/GPU percentages in `kr_native_utils.log`
- `GetPowerScheme`, `GetPerformanceModeState`, and system battery or power settings

For Accent Conversion, do not assume a CPU limitation without confirming the device capability. Use the CPU specs and system requirements as supporting evidence. The practical thresholds to compare against are:

- Single-thread rating: 3000 is the best-practice target; 2500 is acceptable; below 2500 is increasingly concerning and may be considered insufficient for smooth Accent Conversion performance.
- Clock speed: roughly 3 GHz or above is the usual expectation for stable AC performance.
- Typical TDP: 35 W or above is the usual guideline; lower-power systems may still work, but they are more likely to show quality degradation or processor switching under load.

Interpretation:

- A hard overload or repeated auto-switching to `BestPerformance` can affect audio quality.
- If the CPU is below the stated thresholds, it does not automatically rule out CPU as the cause; the total available processing headroom still matters, and the inherent CPU power of the device can affect the audio quality despite low load levels.
- If the CPU is near or below the minimum range, cite that as a likely contributor, not as a proven root cause, unless the rest of the evidence lines up with the symptom and the system behavior.

Useful references:

- [Krisp AI Accent Conversion user guide](https://help.krisp.ai/hc/en-us/articles/26429444363548-Krisp-AI-Accent-Conversion-user-guide#h_01KMZCRT3KF33YBC6DNKW6N3Z8)
- [System requirements for Krisp Call Center AI](https://help.krisp.ai/hc/en-us/articles/27188150457116-System-requirements-for-Krisp-Call-Center-AI#h_ac)
- [How automatic gain control affects Krisp audio performance](https://help.krisp.ai/hc/en-us/articles/27939278357020-How-automatic-gain-control-affects-Krisp-audio-performance)

### 5. Frame handling and media-stream quality

Check `kr_media_sp.log` for dropped frames, overwrites, and energy stats. This is especially important when the symptom is choppy or inconsistent audio.

Look for:

- `Overwrite - Cnt` and `DroppedFrameCount`
- `Len`, `Write`, `Read`, `Empty`
- `Graph:EnergyStats()` before and after processing
- `Write - Cnt` vs `Overwrite - Cnt` to tell whether frames are being skipped during processing

Interpretation:

- Nonzero `Overwrite` or dropped-frame counts can indicate CPU pressure or processing delays.
- If energy is present before processing, then zero afterward, check whether the sound is being removed by processing instead of being captured normally.
- If the audio sample itself is weak or inconsistent before processing, do not assume the issue is caused by Krisp alone.

### 6. Audio sample analysis and quality before processing

Review the real capture and processed sample when available. Pay attention to:

- audio quality before processing is applied
- bandwidth and sample integrity
- speaker tone, intonation, and cadence
- pronunciation and naturalness

Do not ignore inconsistencies between the original audio sample and the processed sample. The original capture is the baseline for evaluation. If the original audio is already poor, the issue may be caused by the device, microphone, or environment rather than by Accent Conversion itself.

### 7. Test AC recording and toggles

For AC test recordings, inspect the test flow and check the related states and settings:

- test AC recording started / stopped
- selected device used for the test AC recording
- `modelName`, `voiceName`, `modeName`, profile selection, and toggle state
- whether the Krisp toggle was enabled or disabled during the test

These checks often help confirm whether the issue is tied to the generic call route or specifically to the model/profile state in Accent Conversion.

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

### Template library

Before drafting the response, consult [`templates/CUSTOMER_EMAIL_TEMPLATES.md`](templates/CUSTOMER_EMAIL_TEMPLATES.md). Select the closest matching macro based on the evidence and symptom, not just the customer's wording. The library contains device-based and email-based variants where provided.

- Use the device-based variant when the ticket/report is addressed to a team about an affected user's device; use the email-based variant when replying directly to the requester. If the ticket channel is unclear, use the email-based variant and identify that choice in the output.
- Include the selected macro name and its device/email variant.
- Reproduce the chosen template as a complete, send-ready email. Replace template placeholders only with values established by the case. Preserve Zendesk variables when the email will be pasted into Zendesk; do not invent names, dates, CPU models, links, or log findings.
- Where a macro offers mutually exclusive options (for example microphone issue Options 1–3 or report-request sections [GENERAL]/[VT]/[NC]/[AC]), include only the option/section supported by the case. Do not send all alternatives.
- If no supplied macro fits, use the closest applicable clarification, report-request, or general investigation response, or write a narrowly tailored email in the same style. State when no exact macro exists.
- Keep internal log details, confidence assessments, and technical speculation out of the customer email unless the selected template explicitly calls for a customer-safe explanation. Describe uncertain causes as possible contributors, never as confirmed facts.

### Required response structure

Every investigation response must contain these two sections, in this order:

1. **Technical findings (internal)** — identify the symptom and selected macro; summarize direct evidence with log filename and timestamp/call ID when available; explain the technical mechanism and distinguish confirmed facts from likely contributors; state confidence and any evidence gaps or additional checks. Do not claim evidence that was not supplied or reviewed. If logs are inconclusive, say so plainly.
2. **Customer email** — state the selected macro name and variant, then provide the complete email body ready to send. Preserve appropriate template formatting, links, and Zendesk placeholders. Never omit the email merely because the technical findings are inconclusive; use a clarification or report-request macro when needed.

Use clear, empathetic language and avoid internal jargon unless it helps explain the next step. Do not expose speculative diagnoses as facts. Tailor the response to the evidence; do not send a generic list of every possible cause.

### Confirmed muted Krisp microphone

When logs confirm that the Krisp microphone is muted during the call, use the matching setup/clarification guidance in the template library or a concise, case-specific email to explain that this is why the customer's voice is not being transmitted. A concise customer-facing explanation may be:

> Your Krisp Microphone is muted, so your voice is not being sent to the meeting. Please unmute it and try another call.

Only use the present tense when the logs confirm that it remains muted. If the logs show only `InitialMuteState: true`, say it was muted at the start of the call; do not claim it remained muted unless later events establish that.

### Other findings or inconclusive logs

Use the closest evidence-supported template. If there is no matching macro, adapt a concise email to the case using only verified findings, a specific next step, and a targeted request for any missing evidence.

For additional Krisp Call Center AI product information, use the [Krisp Call Center AI Help Center](https://help.krisp.ai/hc/en-us/categories/4550555112860-Krisp-Call-Center-AI).
