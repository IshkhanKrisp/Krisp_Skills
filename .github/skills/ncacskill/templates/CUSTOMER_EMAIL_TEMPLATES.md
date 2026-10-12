# Customer Email Templates

Use this library with the [NCAC troubleshooting skill](../SKILL.md). Match a macro to verified investigation findings and select the device-based or email-based variant appropriate to the case. Preserve Zendesk placeholders unless case data is explicitly available. Do not include mutually exclusive options together; choose the one supported by the evidence.

## 1. Setup issue

**Macro:** `Technical::(CX - device) Setup issue` or `Technical::(CX-email) Setup issue`  
**Description:** Krisp Speaker/Microphone is not selected in the softphone, causing NC/AC/VT/AA to fail to activate.

### CX (device-based)

Hi there,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

After investigating the log files, I can see that Krisp is not being used during calls, so the audio stream is not being processed by Krisp. As a result, app features may not activate, which can cause the described issue.
To resolve the issue, please make sure that Krisp is selected as the preferred device in your calling application.

If the issue persists after the setup, please ask the user to submit a new problem report and mention the exact date/time of the issue so we can review the updated logs.

Looking forward to hearing about how things go on your end.
Kind regards,

### CX (email-based)

Hi there, {{ticket.requester.first_name}},

Thanks for reaching out! This is {{current_user.first_name}} with the Krisp Customer Engineering team.

After investigating the log files, I can see that Krisp is not being used during calls, so the audio stream is not being processed by Krisp. As a result, app features may not activate, which can cause the described issue.
To resolve the issue, please make sure that Krisp is selected as the preferred device in your calling application.

If the issue persists after the setup, please submit a new problem report and mention the exact date/time of the issue so we can review the updated logs.

Keep us posted on how things go after making the necessary adjustments.
Kind regards,

## 2. Volume issue

**Macro:** `Technical::(CX - device) Volume issue` or `Technical::(CX - email) Volume issue`  
**Description:** Microphone volume is too low, causing the user to become inaudible to the caller.

### CX (device-based)

Hi there,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

After investigating the log files, I can see that the microphone volume is too low. To resolve the issue, please adjust the microphone volume by following the steps below:
1. Open Sound settings > Input and increase the physical microphone/headset volume.
2. In the same Sound settings, increase the Krisp Microphone volume as well.

If the issue persists after the volume adjustment, please ask the user to submit a new problem report with audio samples attached (~30-40 seconds) so we can investigate further with updated logs.

Looking forward to hearing back from you!
Kind regards,

### CX (email-based)

Hi there, {{ticket.requester.first_name}},

Thanks for reaching out! This is {{current_user.first_name}} with the Krisp Customer Engineering team.

After investigating the log files, I can see that the microphone volume is too low. To resolve the issue, please adjust the microphone volume by following the steps below:
1. Open Sound settings > Input and increase the physical microphone/headset volume.
2. In the same Sound settings, increase the Krisp Microphone volume as well.

If the issue persists after the volume adjustment, please submit a new problem report with audio samples attached (~30-40 seconds) so we can investigate further with updated logs.

Let us know how things go after the changes.
Kind regards,

## 3. CPU overload

**Macro:** `Technical::(CX - device) CPU overload` or `Technical::(CX - email) CPU overload`  
**Description:** The CPU does not meet minimum requirements or is overloaded, causing app performance or audio issues.

### CX (device-based)

Hi there,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

After reviewing the logs, I can see that the device is overloaded. When system resources are constrained, the described issue can occur. To help stabilize performance, please try the steps below:
1. Quit any unused applications.
2. Close unnecessary browser tabs/windows.
3. Pause CPU-intensive tasks (e.g., large file exports, streaming, background sync) while on calls.

If the issue persists after reducing the system load, please ask the user to submit a new problem report with a short audio sample (~30-40 seconds) so we can review updated logs and assist further.

Let us know how things go after making the necessary adjustments.
Kind regards,

### CX (email-based)

Hey {{ticket.requester.first_name}},

Hope you're doing well! This is {{current_user.first_name}} with the Krisp Customer Engineering team.

After reviewing the logs, I can see that the device is overloaded. When system resources are constrained, the described issue can occur. To help stabilize performance, please try the steps below:
1. Quit any unused applications.
2. Close unnecessary browser tabs/windows.
3. Pause CPU-intensive tasks (e.g., large file exports, streaming, background sync) while on calls.

If the issue persists after reducing the system load, please submit a new problem report with a short audio sample (~30-40 seconds) so we can review updated logs and assist further.

Keep us posted on your progress so we can assist you further if need be.
Sincerely,

## 4. Forward to Sales

**Macro:** `General::(CX) Forward to Sales`  
**Description:** Pricing inquiry from a new customer, POC request, or upsell opportunity.

Hi {{ticket.requester.first_name}},

This is {{current_user.first_name}} with the Krisp Customer Engineering team. Thanks for reaching out!

I'm looping in our Sales team ([sales@krisp.ai](mailto:sales@krisp.ai)) to help with your inquiry. They'll take it from here and will follow up with you directly as soon as possible.

In the meantime, if you have any technical questions about the product, feel free to share them here; we'll be happy to help.

Best regards,

## 5. Voice Translation whitelisting

**Macro:** `Technical::(CX - device) Voice Translation whitelisting` or `Technical::(CX - email) Voice Translation whitelisting`  
**Description:** Voice Translation fails to start due to misconfiguration on the customer's firewall/network proxy settings.

### CX (email-based team)

Hey team,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

Thanks for reaching out. This is {{current_user.first_name}} with the Krisp Customer Engineering team.

Voice Translation runs over WebRTC. If your organization decrypts traffic through a firewall or network management tools, please ask your IT/network team to bypass SSL/certificate decryption and allow-list the domains below, so end-to-end encryption stays intact, and the feature works correctly.

Krisp media infrastructure: allow-list the domains listed here - Krisp domains.
Daily.co: allow-list the domains listed here - Daily.co domains.

If the issue persists after the changes, please have the user submit a new report from the same device so we can verify the updated behavior in the logs.

Looking forward to your updates.

Kind regards,

### CX (device-based team)

Hey team,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

Voice Translation runs over WebRTC. If your organization decrypts traffic through a firewall or network management tools, please ask your IT/network team to bypass SSL/certificate decryption and allow-list the domains below, so end-to-end encryption stays intact, and the feature works correctly.

Krisp media infrastructure: allow-list the domains listed here - Krisp domains.
Daily.co: allow-list the domains listed here - Daily.co domains.

If the issue persists after the changes, please have the user submit a new report from the same device so we can verify the updated behavior in the logs.

Looking forward to your updates.

Kind regards,

## 6. Redirecting to Chase

**Macro:** `Technical::(CX) Redirecting to Chase`  
**Description:** Extension users from Chase who are redirected to internal support.

Hello {{ticket.requester.first_name}},

Thanks for reaching out! This request needs to be handled through your company's internal Krisp support channel. Please contact your team internally by either:

- emailing [krisp.support.log@chase.com](mailto:krisp.support.log@chase.com), or
- reaching out to your Krisp team admin so they can coordinate next steps on your behalf.

Kind regards,

## 7. Report not downloadable

**Macro:** `Technical::(CX - device) Report not downloadable` or `Technical::(CX - email) Report not downloadable`  
**Description:** The report is not downloadable due to an issue on the customer's side.

### CX (email-based)

Hi {{ticket.requester.first_name}},

Hope you're doing well! This is {{current_user.first_name}} with the Krisp Customer Engineering team.

Thanks for sending the report. Unfortunately, the logs attached to it are not downloadable, so we're unable to review the log files on our side.

This can happen if:

- The network connection is unstable during the upload.
- The report submission window is closed before the upload is fully finalized.
- A firewall/proxy is blocking the upload.

**Next steps**

1. Make sure that you're connected to a stable network when sending the report.
2. When submitting, please keep the submission window open until it fully completes.
3. If you're on a managed network, please ask your IT team to [allow-list our domains](https://help.krisp.ai/hc/en-us/articles/360016915219-Configuring-network-firewall-or-proxy-server-settings?ti=0%3A1#h_01EN5NS0N8GWYDMCEQ0PT4SBWR) so the report can upload successfully.

**Alternative option**
If the report still doesn't upload properly, you can manually collect Krisp logs and send them here:

- [How to collect Krisp manual logs on Windows](https://help.krisp.ai/hc/en-us/articles/8031270145948-How-to-collect-Krisp-manual-logs-on-Windows)
- [How to collect Krisp manual logs on Mac](https://help.krisp.ai/hc/en-us/articles/8068305398940-How-to-collect-Krisp-manual-logs-on-Mac)

Looking forward to receiving the new report.
Kind regards,

### CX (device-based)

Hey team,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

Unfortunately, the logs attached to it are not downloadable, so we're unable to review the log files on our side. This can happen if:

- The network connection is unstable during the upload.
- The report submission window is closed before the upload is fully finalized.
- A firewall/proxy is blocking the upload.

**Next steps**

1. Make sure that the user is connected to a stable network when sending the report.
2. When submitting, please ask the user to keep the submission window open until it is fully completed.
3. If the user is on a managed network, please ask the IT team to [allow-list our domains](https://help.krisp.ai/hc/en-us/articles/360016915219-Configuring-network-firewall-or-proxy-server-settings?ti=0%3A1), so the report can upload successfully.

**Alternative option**
If reports continue failing, the user can manually collect Krisp logs and share them with us following these guides:

- [How to collect Krisp manual logs on Windows](https://help.krisp.ai/hc/en-us/articles/8031270145948-How-to-collect-Krisp-manual-logs-on-Windows)
- [How to collect Krisp manual logs on Mac](https://help.krisp.ai/hc/en-us/articles/8068305398940-How-to-collect-Krisp-manual-logs-on-Mac)

Once the new report comes through, we'll review it and continue troubleshooting.
Kind regards,

## 8. Trial Cancellation

**Macro:** `Subscription::(CX) Trial Cancellation`  
**Description:** Requests to cancel a CX trial.

Hello {{ticket.requester.first_name}},

Hope you're doing well! This is {{current_user.first_name}} with the Krisp Customer Engineering team.

I'm writing to confirm the details of your trial cancellation. The Call Center AI paid trial ($100 for 14 days) is non-renewable. Your current trial subscription is set to automatically expire on INSERT_DATE.

After this date, the subscription will be cancelled with no further renewals or charges, unless you choose to initiate a full subscription by contacting our Sales team through your Admin Portal.

We hope you found the trial experience valuable. We would love to hear any feedback you have regarding the product or your experience with the trial so far, as it helps us improve our services.

If you have any other questions, feel free to reach out!
Kind regards,

## 9. Driver issue

**Macro:** `Technical::(CX-device) Driver issue` or `Technical::(CX-email) Driver issue`  
**Description:** Drivers go missing after a fresh installation or an update. Use for versions under 2.73.

### CX (email-based team)

Hi {{ticket.requester.first_name}},

This is {{current_user.first_name}} with the Krisp Customer Engineering team. Thanks for the report.

Please try the steps below and let us know if the problem gets resolved:
1. Uninstall Krisp.
2. Restart the computer.
3. Complete all Windows updates.
   - Settings → Windows Update → Check for updates.
   - Install everything available, then restart again if prompted.
4. Download and install the latest Krisp version.
   - Download from: account.krisp.ai
   - Install, then open Krisp and check if the Microphone/Speaker dropdowns show relevant devices again.

If the issue persists after reinstalling, please submit a new problem report so we can investigate further.

Looking forward to hearing back from you!
Kind regards,

### CX (device-based team)

Hey team,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

Could you please follow these steps on the user's device?
1. Uninstall Krisp.
2. Restart the computer.
3. Complete all Windows updates (install everything available → restart if prompted).
4. Install the latest Krisp version from account.krisp.ai.

If the issue persists after these steps, please have the user submit a new report from the same device so we can check the updated system/driver details in the logs.

Thanks in advance for your time!
Kind regards,

## 10. Issue resolved

**Macro:** `General::(CX) Issue resolved`  
**Description:** Use when the user confirms that their problem has been resolved. Fill tags based on the previous recommendations.

Thanks for confirming, {{ticket.requester.first_name}},

I'm glad to hear everything is working as expected now! If you run into anything else or have any questions about Krisp, feel free to reply to this email anytime; we'll be happy to help.

Have a great day,

## 11. Clarification request (email)

**Macro:** `Technical::(CX-email) Clarification request`  
**Description:** There are not enough details to understand the user's inquiry.

Hello {{ticket.requester.first_name}},

Thanks for reaching out. My name is {{current_user.first_name}}, and I'm with the Krisp Customer Engineering team.

To make sure we understand your request correctly and resolve it as quickly as possible, could you share a bit more detail about the issue you're running into? Anything you can tell us about what's happening, when it started, and the steps that lead up to it will help us get to the bottom of it faster.

Once we have those details, we'll take it from there.

Best regards,

## 12. Clarification request (device)

**Macro:** `Technical::(CX-device) Clarification request`  
**Description:** There are not enough details to understand the user's inquiry.

Hi there,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

To make sure we understand the issue correctly and resolve it as quickly as possible, could you help us gather a bit more detail from the affected user? Anything about what's happening, when it started, and the steps that lead up to it will help us investigate faster.

Once we have those details, we'll take it from there.

Kind regards,

## 13. Case escalation

**Macro:** `Technical::(CX) Case escalation`  
**Description:** Transfer the case to QA/Engineering for review and evaluation; usually link a Jira. Assumes this is not a first-touch ticket.

Thank you for your patience, {{ticket.requester.first_name}}!

I wanted to let you know that we've escalated your case to the relevant team for a deeper review. We'll keep monitoring this internally and will follow up with you as soon as we have an update.

In the meantime, if anything changes on your side (new error message, updated behavior, or additional examples), please reply here and share the details, as it can help speed things up.

Best regards,

## 14. Report request

**Macro:** `Technical::(CX-device) Report request` or `Technical::(CX-email) Report request`  
**Description:** Request a new report with more details, such as audio samples or precise timeframes.

### CX (email-based team)

Hi {{ticket.requester.first_name}},

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

To investigate this properly, we'll need a fresh report captured while the issue is occurring. Please use the relevant steps below to submit an updated report.

#### [GENERAL]
1. Open the Krisp app.
2. Click on your profile badge > Report a problem.
3. Fill in the relevant details, including screenshots and the last time the issue occurred.
4. Make sure to include the system information for debugging, then submit the report.
Full guide: [Report a problem about Krisp](https://help.krisp.ai/hc/en-us/articles/4424885115292-Report-a-problem-about-Krisp)

#### [VT]
1. Open the Krisp app.
2. Click on your profile badge > Test Voice Translation.
3. Record an audio sample where the issue is reproduced, then click Send report.
4. Add a detailed description of what went wrong.
5. Include the last time the issue occurred, with the exact date, time, and timezone.
6. Make sure to include the system information for debugging, then submit the report.
Full guide: [Report a problem about Voice Translation](https://help.krisp.ai/hc/en-us/articles/25678542052508-Report-a-problem-about-Voice-Translation)

#### [NC]
1. Open the Krisp app.
2. Click on your profile badge > Test Noise Cancellation.
3. Record an audio sample of about 30 seconds where the issue is reproduced, then click Report a problem.
4. Add a detailed description of what went wrong.
5. Include the last time the issue occurred, with the exact date, time, and timezone.
6. Make sure to include the system information for debugging, then submit the report.
Full guide: [Test Krisp Noise Cancellation](https://help.krisp.ai/hc/en-us/articles/4420135589020-Test-Krisp-Noise-Cancellation)

#### [AC]
1. Open the Krisp app.
2. Click on Test my voice and record your voice for at least 30-40 seconds.
3. Click Report a problem.
4. Add a detailed description of what went wrong.
5. Include the last time the issue occurred, with the exact date, time, and timezone.
6. Make sure to include the system information for debugging, then submit the report.
Full guide: [Test Krisp Accent Conversion](https://help.krisp.ai/hc/en-us/articles/18307699689500-Test-Krisp-Accent-Conversion)

Important: When submitting the report, please keep the affected app(s) and tools running so the report captures the same environment where the issue occurs.

Looking forward to receiving the updated report.
Kind regards,

### CX (device-based team)

Hey team,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

To investigate this properly, we'll need a fresh report captured while the issue is occurring. Please ask the user to follow the relevant steps below to submit an updated report.

#### [GENERAL]
1. Open the Krisp app.
2. Click on the profile badge > Report a problem.
3. Fill in the relevant details, including screenshots and the last time the issue occurred.
4. Make sure to include the system information for debugging, then submit the report.
Full guide: [Report a problem about Krisp](https://help.krisp.ai/hc/en-us/articles/4424885115292-Report-a-problem-about-Krisp)

#### [VT]
1. Open the Krisp app.
2. Click on the profile badge > Test Voice Translation.
3. Record an audio sample where the issue is reproduced, then click Send report.
4. Add a detailed description of what went wrong.
5. Include the last time the issue occurred, with the exact date, time, and timezone.
6. Make sure to include the system information for debugging, then submit the report.
Full guide: [Report a problem about Voice Translation](https://help.krisp.ai/hc/en-us/articles/25678542052508-Report-a-problem-about-Voice-Translation)

#### [NC]
1. Open the Krisp app.
2. Click on the profile badge > Test Noise Cancellation.
3. Record an audio sample of about 30 seconds where the issue is reproduced, then click Report a problem.
4. Add a detailed description of what went wrong.
5. Include the last time the issue occurred, with the exact date, time, and timezone.
6. Make sure to include the system information for debugging, then submit the report.
Full guide: [Test Krisp Noise Cancellation](https://help.krisp.ai/hc/en-us/articles/4420135589020-Test-Krisp-Noise-Cancellation)

#### [AC]
1. Open the Krisp app.
2. Click on Test my voice and record for at least 30-40 seconds.
3. Click Report a problem.
4. Add a detailed description of what went wrong.
5. Include the last time the issue occurred, with the exact date, time, and timezone.
6. Make sure to include the system information for debugging, then submit the report.
Full guide: [Test Krisp Accent Conversion](https://help.krisp.ai/hc/en-us/articles/18307699689500-Test-Krisp-Accent-Conversion)

Important: When submitting the report, please keep the affected app(s) and tools running so the report captures the same environment where the issue occurs.

Looking forward to receiving the updated report.
Kind regards,

## 15. Microphone issue

**Macro:** `Technical::(CX-device) Microphone issue` or `Technical::(CX-email) Microphone issue`  
**Description:** The original input audio is poor quality or the headset is in an unhealthy state. Select one option supported by the logs/audio; include attachments only when available.

### CX (email-based)

Hi {{ticket.requester.first_name}},

Thanks for reaching out! This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We appreciate the report and audio samples.

#### OPTION 1 (include attachments)
Our investigation shows that the original microphone capture quality is already low, which limits what Krisp can improve. You can find the original and processed audio files attached for reference. Please switch to a different headset and let us know if the audio quality improves.

If the problem persists after switching, please submit a new report with audio samples so we can compare results.

#### OPTION 2 (include attachments)
After analyzing the submitted audio files, we can hear that your microphone doesn't capture any audio, even before Krisp starts processing it. Since there may be a problem with the headset, please check your connectors and switch to a different headset to see if the problem gets resolved.

In case it persists after switching, please submit a new report with audio samples so we can compare results.

#### OPTION 3
Based on the logs, the headset is in an unhealthy state, which can cause unstable or distorted microphone audio. Please switch to a different headset and let us know how things go.

If the issue persists, please submit a new report with audio samples, so we can re-check the updated logs.

Looking forward to hearing back from you!
Best regards,

### CX (device-based)

Hey team,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

#### OPTION 1 (include attachments)
We've reviewed the log files and the audio samples. Our investigation shows that the original microphone capture quality is already low, which limits what Krisp can improve. You can find the original and processed audio files attached for reference. Please ask the user to switch to a different headset and let us know if the audio quality improves.

If the problem persists after switching, please ask them to submit a new report with audio samples so we can compare results.

#### OPTION 2 (include attachments)
After analyzing the submitted audio files, we can hear that the user's microphone doesn't capture any audio, even before Krisp starts processing it. Since there may be a problem with the headset, please check the connectors on the user's device or ask them to switch to a different headset to see if the problem gets resolved.

In case it persists after switching, please submit a new report with audio samples so we can compare results.

#### OPTION 3
Based on the logs, the headset on the user's device is in an unhealthy state, which can cause unstable or distorted microphone audio. Please help them switch to a different headset and let us know if the issue is resolved.

If the issue persists, please have them submit a new report with audio samples, so we can re-check the updated logs.

Awaiting your response!
Best regards,

## 16. Windows ducking issue

**Macro:** `Technical::(CX-device) Windows ducking issue` or `Technical::(CX-email) Windows ducking issue`  
**Description:** Windows Communications setting decreases Krisp Speaker volume so the other end is not audible.

### CX (device-based)

Hi team,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

The logs indicate that the Windows Communications setting on this device is set to Reduce by 80%. When enabled and set to 80%, this setting forces the OS to detect when you're on a call and automatically adjusts the volume of secondary audio sources on your PC. Krisp is considered a secondary source.

Please disable the setting following the steps below:

- Open **Start** > Search for **Sound Settings**
- Scroll down and click on **More sound settings**
- Open the **Communications** tab > Select **"Do nothing"** > Click on **Apply/OK**.

Let us know how things go after the adjustment.
Best regards,

### CX (email-based)

Hi there, {{ticket.requester.first_name}},

Thank you for reaching out! This is {{current_user.first_name}} with the Krisp Customer Engineering team.

The logs indicate that the Windows Communications setting on your device is set to Reduce by 80%. When enabled and set to 80%, this setting forces the OS to detect when you're on a call and automatically adjusts the volume of secondary audio sources on your PC. Krisp is considered a secondary source.

Please disable the setting following the steps below:

- Open **Start** > Search for **Sound Settings**
- Scroll down and click on **More sound settings**
- Open the **Communications** tab > Select **"Do nothing"** > Click on **Apply/OK**.

Let us know how things go after the adjustment.
Best regards,

## 17. Mistranslation context request

**Macro:** `Technical::(CX-device) Mistranslation context request` or `Technical::(CX-email) Mistranslation context request`  
**Description:** Request an audio snippet, Widget screenshot, and actual vs. expected output for Voice Translation misinterpretation/mistranslation reports.

### CX (device-based)

Hi all,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

To assess whether this was a Voice Translation model issue and try to reproduce it on our end, we need a bit more context. Could you help us collect the following from the affected user:

- A short audio snippet from the moment the mistranslation occurred.
- A screenshot of the translation in the Krisp Widget, along with the actual output and the expected output.

Once we have these details, we'll be able to investigate further.

Best regards,

### CX (email-based)

Hello {{ticket.requester.first_name}},

Thanks for reaching out. This is {{current_user.first_name}} with the Krisp Customer Engineering team.

To assess whether this was a Voice Translation model issue and try to reproduce it on our end, we need a bit more context. Could you share the following:

- A short audio snippet from the moment the mistranslation occurred.
- A screenshot of the translation in the Krisp Widget, along with the actual output and the expected output.

Once we have these details, we'll be able to assist you further.

Kind regards,

## 18. Krisp CX Bridge extension resync

**Macro:** `Technical::(CX-device) Krisp CX Bridge extension resync` or `Technical::(CX-email) Krisp CX Bridge extension resync`  
**Description:** Resolve a sync issue between the browser extension and desktop app with a network check, softphone refresh, account sync, and extension resync.

### CX (device-based)

Hey team,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

This looks like a sync issue between the Krisp CX Bridge extension and the desktop app. To get things back in sync, please have the user run through the following:
1. Confirm they're on a stable network connection.
2. Refresh the softphone's browser page.
3. Sync their Krisp account from the desktop app.
4. Sync the extension, then start a new call.
For background on how the integration works, here's an overview: [Krisp CX Bridge](https://help.krisp.ai/hc/en-us/articles/27981087536796-Krisp-CX-Bridge).

Let us know how things go afterward.

Best,

### CX (email-based)

Hi {{ticket.requester.first_name}},

Thanks for reaching out. This is {{current_user.first_name}} with the Krisp Customer Engineering team.

This looks like a sync issue between the Krisp CX Bridge extension and the desktop app. To get things back in sync, please run through the following:
1. Confirm you're on a stable network connection.
2. Refresh your softphone's browser page.
3. Sync your Krisp account from the desktop app.
4. Sync the extension, then start a new call.
For background on how the integration works, here's an overview: [Krisp CX Bridge](https://help.krisp.ai/hc/en-us/articles/27981087536796-Krisp-CX-Bridge).

Let us know how things go afterward.

Kind regards,

## 19. Headset bandwidth adjustment

**Macro:** `Technical::(CX-device) Headset bandwidth adjustment` or `Technical::(CX-email) Headset bandwidth adjustment`  
**Description:** Accent Conversion issue traced to the headset capturing below the required 8 kHz bandwidth (capped at 4 kHz).

### CX (device-based)

Hello,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

Our Research team reviewed the logs and audio samples from the report and found that the headset is capturing audio at a lower bandwidth than expected. This doesn't mean the headset can't support a higher bandwidth, as Windows can sometimes cap it. Accent Conversion needs 8 kHz bandwidth to process correctly, but in this case it's limited to 4 kHz. To readjust it, please have the user:
1. Open Sound settings, go to More sound settings, then the Recording tab.
2. Double-click the headset's microphone, then open the Advanced tab.
3. Under Default Format, select a higher option, then click Apply/OK.
Let us know if this resolves the issue.

Best,

### CX (email-based)

Hi {{ticket.requester.first_name}},

Thanks for reaching out. This is {{current_user.first_name}} with the Krisp Customer Engineering team.

Our Research team reviewed the logs and audio samples from your report and found that your headset is capturing audio at a lower bandwidth than expected. This doesn't mean the headset can't support a higher bandwidth, as Windows can sometimes cap it. Accent Conversion needs 8 kHz bandwidth to process correctly, but in this case it's limited to 4 kHz. To readjust it, please:
1. Open Sound settings, go to More sound settings, then the Recording tab.
2. Double-click your headset's microphone, then open the Advanced tab.
3. Under Default Format, select a higher option, then click Apply/OK.
Let us know if this resolves the issue.

Kind regards,

## 20. Accent Conversion robotic-sounding output

**Macro:** `Technical::(CX-device) Accent Conversion robotic sounding output` or `Technical::(CX-email) Accent Conversion robotic sounding output`  
**Description:** Robotic or unnatural output traced to raw captured audio, including microphone position or flat vocal delivery.

### CX (device-based)

Hi there,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

We carefully reviewed the audio samples from the report and noticed a couple of things in the raw captured audio (before processing) that may be affecting how the converted voice comes across. Please share the following with the user:

- Microphone position: the microphone appears to be quite close to the mouth, which can affect the naturalness of the captured audio. Moving it slightly further away may make a noticeable difference.
- Flat delivery: the voice in the recording sounds fairly flat in tone and rhythm. We all have our own speaking style, but adding a bit more variation in pitch and pace can go a long way toward a more conversational, natural result.

Let us know if either adjustment improves things.

Best,

### CX (email-based)

Hello {{ticket.requester.first_name}},

Thanks for reaching out. This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We carefully reviewed the audio samples you shared and noticed a couple of things in the raw captured audio (before processing) that may be affecting how your converted voice comes across:

- Microphone position: your microphone appears to be positioned quite close to your mouth, which can affect the naturalness of the captured audio. Moving it slightly further away may make a noticeable difference.
- Flat delivery: the recording sounds fairly flat in tone and rhythm. We all have our own speaking style, but adding a bit more variation in pitch and pace can go a long way toward sounding more conversational and natural.

Let us know if either adjustment improves things.

Kind regards,

## 21. CPU below minimum requirements

**Macro:** `Technical::(CX-device) CPU below minimum requirements` or `Technical::(CX-email) CPU below minimum requirements`  
**Description:** CPU does not meet the Krisp Call Center AI minimum requirements. Replace `INSERT_CPU_MODEL` and provide the system-requirements article link if available.

### CX (device-based)

Hey team,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

After reviewing the logs, we can see that the CPU on the device (INSERT_CPU_MODEL) doesn't meet our minimum requirements. When system resources are constrained, the reported behavior can occur. Freeing up resources may help, but this processor isn't designed for local audio processing, so we'd recommend running Krisp on updated hardware.

You can find the full specifications for each feature here: [System requirements for Krisp Call Center AI](https://help.krisp.ai/hc/en-us/articles/27188150457116-System-requirements-for-Krisp-Call-Center-AI).

Let us know if you have any questions.

Best,

### CX (email-based)

Hi {{ticket.requester.first_name}},

Thanks for reaching out. This is {{current_user.first_name}} with the Krisp Customer Engineering team.

After reviewing the logs, we can see that the CPU on your device (INSERT_CPU_MODEL) doesn't meet our minimum requirements. When system resources are constrained, the behavior you're seeing can occur. Freeing up resources may help, but this processor isn't designed for local audio processing, so we'd recommend running Krisp on updated hardware.

You can find the full specifications for each feature here: [System requirements for Krisp Call Center AI](https://help.krisp.ai/hc/en-us/articles/27188150457116-System-requirements-for-Krisp-Call-Center-AI).

Let us know if you have any questions.

Kind regards,

## 22. Voice Translation activation failure (408)

**Macro:** `Technical::(CX-device) VT activation failure 408 error` or `Technical::(CX-email) VT activation failure 408 error`  
**Description:** Voice Translation activation failed because a customer-side network connection error caused requests to fail.

### CX (device-based)

Hi there,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

The logs indicate a network connection error that caused the relevant requests to fail, which prevented Voice Translation from activating. Could you check with your IT team and confirm there were no blockages that would cause this?

Once verified, please have the user try Voice Translation again and let us know how it goes.

If the problem persists, please submit a new report so we can review the updated system details.

Kind regards,

### CX (email-based)

Hi {{ticket.requester.first_name}},

Thanks for reaching out. This is {{current_user.first_name}} with the Krisp Customer Engineering team.

The logs indicate a network connection error that caused the relevant requests to fail. Could you check with your IT team and confirm there were no blockages that would cause this?

Once verified, please try Voice Translation again and let me know how it goes.

If the problem persists, submit a new report so I can review the updated system details.

Best regards,

## 23. Update to the latest version

**Macro:** `Technical::(CX-device) Update to the latest version` or `Technical::(CX-email) Update to the latest version`  
**Description:** Logs show the device is running an outdated Krisp version. Replace `INSERT_VERSION` with the observed version and, when confirmed, `INSERT_LATEST_VERSION` with the current latest version. If the latest version is unknown, remove the comparison clause and refer to the latest release. Recommend updating without presenting the older version as the proven cause.

### CX (device-based)

Hello,

This is {{current_user.first_name}} with the Krisp Customer Engineering team.

We have received a report from one of your users from the device under the hostname {{ticket.requester.name}}, which is part of the {{ticket.ticket_field_13167261768092}} Krisp team, with the following message: "{{ticket.ticket_field_13166922449692}}".

The logs show the device is running an older version of Krisp (INSERT_VERSION), while the latest version is INSERT_LATEST_VERSION. A number of fixes and improvements have shipped since then, so the reported issue may already be resolved in the latest release. Please have the user update and retest:
1. Open the Krisp app and check the current version under Settings.
2. Download and install the latest version from [account.krisp.ai](https://account.krisp.ai/).
3. Restart the app and try again.
If the issue persists after updating, please submit a new report so we can take another look.

Best,

### CX (email-based)

Hi {{ticket.requester.first_name}},

Thanks for reaching out. This is {{current_user.first_name}} with the Krisp Customer Engineering team.

The logs show your device is running an older version of Krisp (INSERT_VERSION), while the latest version is INSERT_LATEST_VERSION. A number of fixes and improvements have shipped since then, so the issue may already be resolved in the latest release. Please update and retest:
1. Open the Krisp app and check your current version under Settings.
2. Download and install the latest version from [account.krisp.ai](https://account.krisp.ai/).
3. Restart the app and try again.
If the issue persists after updating, please submit a new report so we can take another look.

Kind regards,
