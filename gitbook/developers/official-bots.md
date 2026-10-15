---
description: "Use Nexo AI, File Studio, Trivia, Reminders, and Weather and places."
---

# Official bots

You can use ARX's official bots to ask questions, work with files, play quizzes, schedule reminders and look up weather or nearby places. Use ARX search to find each bot by name and open its chat. In each bot, `/start` or `/help` explains its controls.

These bots work in private chats. Their default daily allowance is 100 requests per user for each bot. The bot tells you when it cannot accept more work. Read its profile privacy policy before sharing personal data.

## Nexo AI <a href="#nexo-ai" id="nexo-ai"></a>

Send a text question to receive an AI reply. Nexo AI uses recent conversation context for follow-up questions. Send `/reset` or choose Clear context to discard the saved context for that chat. `/help` and `/reset` do not use the question allowance.

| Limit | Default |
| --- | --- |
| Question | Up to 4,096 UTF-16 units. |
| Retained context | Up to 20 messages, within 64 KiB of text including the system prompt. Older complete exchanges are removed first. |
| Answer | Up to 1,024 model output tokens. Long replies can be split across messages. |

Usage and spending allowances can stop new questions before the daily request allowance is reached. If a question is interrupted, check the conversation before submitting it again.

Nexo AI sends your question and recent context to OpenRouter, which forwards it to the model provider. Reset clears the bot's saved conversation context; it does not recall data already sent to a provider or delete your ARX chat history.

## File Studio <a href="#file-studio" id="file-studio"></a>

Send an image or PDF, then choose an action from the bot's buttons. You can return the original file, convert PNG or JPEG images, resize an image, extract PDF text or render PDF pages as images. JPEG output fills transparent areas with white.

Choose Cancel for one source file or send `/cancel` to cancel your unfinished work in that chat. A result already delivered remains in the chat.

| Limit | Default |
| --- | --- |
| Input | Up to 10 MiB. |
| Output | Up to 20 MiB. |
| Image resize | Fits within 1,600 pixels and leaves smaller images unchanged. |
| PDF | Up to 20 pages. It must be valid and unencrypted. |
| Source action buttons | The source input expires after 15 minutes. Send it again to start new work. |

Image and PDF processing runs within the bot service. These conversions do not send your file to an external processing provider. The bot receives the file you share and creates result files that you can download from the chat.

## Trivia <a href="#trivia" id="trivia"></a>

| Command | What you can do |
| --- | --- |
| `/quiz` | Start a quiz round and answer using the poll controls. |
| `/next` | Skip the current question. |
| `/cancel` | End the round and keep points already earned. |
| `/ranking` | See the top ten players, their points and completed rounds. |
| `/poll` | Create an anonymous survey that allows multiple answers and revoting. |

A round defaults to five questions, 30 seconds per question and ten points for a correct answer. The round keeps the rules it started with. Only the person who started it earns points for its correct answers. Repeated answers and votes on older questions do not add points. Surveys do not change quiz scores.

Trivia stores quiz progress and scores, including the user identity needed to award points. The ranking exposes player results to people who request it. Trivia does not use an external quiz provider.

## Reminders <a href="#reminders" id="reminders"></a>

| Command | How to use it |
| --- | --- |
| `/remind` | `/remind +10m Review the draft` |
| `/reminders` | List your unfinished reminders in this chat, ten per page. |
| `/edit` | Send the reminder ID, new time and replacement text, or use Change time beside it in `/reminders`. |
| `/delete` | Send the reminder ID, or use Delete beside it in `/reminders`. |

Use the buttons beside a reminder in `/reminders` to change its time or delete it without typing an ID. Relative times use a positive whole number followed by `s`, `m`, `h` or `d`. For an absolute reminder, use a future RFC3339 timestamp with an explicit offset or `Z` and whole-second precision. Confirmations and lists display UTC.

The default allowance is 100 open reminders per user for the bot, with a maximum delay of 365 days. Reminder text allows 1,000 Unicode characters and at most 4,000 UTF-8 bytes. You can manage only your own reminders in the current chat. Edit or cancel before delivery begins.

Reminders stores the text and time until it sends or closes the reminder, and does not use an external scheduling provider. After a service interruption, overdue reminders can arrive late. If delivery remains unconfirmed for 24 hours, the bot asks you to inspect the chat before creating a replacement. Deleting a reminder cannot retract a message already sent.

## Weather and places <a href="#weather-and-places" id="weather-and-places"></a>

Send `/start`, then share a location using the request-location button or ARX's location controls. Read the forecast and choose nearby places, a static map or live sharing. `/cancel` or Stop ends a live session.

Nearby results default to five places. The location used by action buttons expires after 15 minutes. Live sharing lasts five minutes, and edits do not extend its original end time. Updates occur no more often than once every five seconds.

For a forecast, the bot sends coordinates truncated to four decimal places to MET Norway. For nearby places, it sends an area query around those coordinates to the Overpass API, which serves OpenStreetMap data. The provider queries do not include your ARX user ID or chat messages. The bot receives the location you share, so choose a less precise location if that suits your request.

Weather data comes from [MET Norway under CC BY 4.0](https://docs.api.met.no/doc/License.html). Nearby place data is [© OpenStreetMap contributors, under the ODbL](https://www.openstreetmap.org/copyright). Keep this attribution when you reuse their results.

For details about what any bot can receive and how blocking or deletion affects your data, continue with [Privacy in bot chats](privacy.md).
