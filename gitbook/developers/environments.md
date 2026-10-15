---
description: "Move a program you tested with a TESTBOT to a regular bot."
---

# Environments

Move your tested program from a TESTBOT to a regular bot that serves your users. Create a separate bot and configure your program with its credentials. Keep each bot's files and stored application state separate.

## Choose a test setup <a href="#choose-a-test-setup" id="choose-a-test-setup"></a>

| Setup | What you can test | Credentials and endpoint |
| --- | --- | --- |
| Local emulator | Your handlers and deterministic API interactions without an ARX account. | Use its synthetic token and loopback HTTP endpoint with explicit local permission. |
| TESTBOT | Messenger interactions, real file processing and webhook delivery with invited testers. | Use its own token and the normal Bot API endpoint. |
| Regular bot | Your service for users who start a conversation with it. | Use a separate token, files and application state. |

A TESTBOT and a regular bot use the same Bot API endpoint. A TESTBOT is a separate bot, not a URL mode. You cannot promote it into a regular bot or move its file IDs to another bot. Follow [Testing](testing.md) to create a TESTBOT and connect any SDK to the emulator.

## Configure your client <a href="#configure-your-client" id="configure-your-client"></a>

The Bot API base URL is `https://signal.arx.pro/bot-api/v1`. Set it as `ARX_BOT_API_BASE_URL` in your application's environment.

Use HTTPS for the remote Bot API. For an HTTP emulator on loopback, enable `allowInsecureLoopback: true` in TypeScript, `allow_insecure_loopback=True` in Python, or pass `true` as the third argument of Rust's `Config::new`. Keep this permission out of your live configuration.

## Prepare the regular bot <a href="#prepare-the-regular-bot" id="prepare-the-regular-bot"></a>

Create your regular bot through [NexoCreator](nexocreator.md), then save its token using the [authentication guide](authentication.md). Supply that token to a separate deployment of your program.

Configure the regular bot's profile and commands, then upload the files it needs. Check your logs and application storage before inviting users so that test data stays separate from their data.

Use the [go-live checklist](go-live-checklist.md) for the checks to run before opening your bot to users.
