---
description: "Collect the information needed to resolve a bot integration problem."
---

# Support

Get help with a failing request by identifying the API error and preparing a small reproduction. Start with [Errors](errors.md), which explains what to change and which failures you can retry.

## Prepare a useful report <a href="#prepare-a-useful-report" id="prepare-a-useful-report"></a>

Record the operation name, HTTP status, `error.code`, `error.request_id`, approximate UTC time, SDK language and version. Describe the result you expected and the result you received. Include the smallest request or handler that reproduces the problem, with personal data removed.

For delivery problems, say whether you use polling or a webhook and whether the issue persists after restarting a single worker. Include the relevant update ID only when needed to identify the delivery. Avoid copying a whole conversation into a report.

Never include a bot token, Authorization header, webhook secret, signature key, private download URL or real user content. Replace these values in code samples before sharing them.

## Contact support <a href="#contact-support" id="contact-support"></a>

Open `@NEXOCREATOR`, send `/help` and choose **Support**. Include the information from your report so support can identify the failing request.

If you received a health notice, you can also use its **Support** action to report the associated problem. Follow [Alerts](alerts.md) to check the notice and recovery controls.

If you suspect your token has leaked, follow [Security](security.md) immediately. For routine questions, check the [FAQ](faq.md) or return to [Making requests](making-requests.md) to inspect the request and response.
