---
description: "Prepare your bot for real users and recoverable failures."
---

# Go-live checklist

Check these behaviors before inviting users to your bot. Use a [TESTBOT](testing.md#testbot) for Messenger checks, then repeat the essential flows with your regular bot's configuration.

- Create the regular bot separately from your TESTBOT. Configure its profile, commands and privacy policy through [NexoCreator](nexocreator.md).
- Store its token securely, set the correct API base URL, and confirm its identity with `me.get`. Follow [Authentication & tokens](authentication.md).
- Start the conversation from a user account and verify that replies use the incoming chat ID. Check commands and localized profile text with [Commands & profile](commands-and-profile.md).
- Run one poller, or configure an HTTPS webhook that verifies the raw request body. Complete the setup in [Polling and webhooks](updates.md).
- Save accepted work before acknowledging delivery. Test repeated updates, a handler failure, a restart and graceful shutdown without losing or repeating important effects.
- Persist idempotency keys for actions you may resume after a restart. Check [Making requests](making-requests.md) before retrying an uncertain outcome.
- Load effective sending limits and handle server retry delays. Confirm that a slow or blocked chat does not stop unrelated work. See [Limits](limits.md).
- Test the content types you actually use, including file readiness, invalid input and permission failures. Follow [Testing](testing.md).
- Verify that blocking stops new interaction and that your privacy policy explains storage, deletion and external providers. Read [Privacy](privacy.md) with your user-facing text in mind.
- Practice token rotation, monitor NexoCreator notices, and prepare a report without secrets using [Support](support.md).

Once these checks pass, keep watching [Alerts](alerts.md) and the [changelog](changes.md) as your bot serves users.
