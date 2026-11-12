---
description: "Register your origins, Main Mini App, named Mini Apps and menu button in NexoCreator."
---

# Register a Mini App

You register Mini Apps for your bot in NexoCreator. ARX opens a Mini App only when its URL belongs to an origin registered for that bot, so start with your origins.

Open `@NEXOCREATOR` with the account that owns the bot. You can use the commands below, or open your bot in NexoCreator and choose **Mini Apps**. Both change the same settings.

| Command | What it configures |
| --- | --- |
| `/setapporigins` | Allowed origins. |
| `/setmainapp` | The Main Mini App: URL and launch mode. |
| `/newapp`, `/listapps`, `/editapp`, `/deleteapp` | Named Mini Apps, each with its own direct link. |
| `/setmenubutton` | The menu button next to the message field. |
| `/setsplash` | The launch screen: icon and colors shown while the page loads. |
| `/setpreviews` | The preview gallery shown before people open the app. |
| `/setappsettings` | Whether the Mini App menu shows **Settings**. |
| `/setattach` | Attachment and side menu placement. This requires review by ARX. |

## Allowed origins <a href="#allowed-origins" id="allowed-origins"></a>

An origin is a scheme and host, with an optional port, and no path: `https://app.example.com`. Every Mini App URL you use, in NexoCreator or in a Bot API button, must belong to one of your bot's origins.

- Each bot can register up to 20 origins.
- Production bots require HTTPS origins with public DNS names; IP addresses, single-label hosts and private suffixes such as `.local`, `.internal`, `.test` or `.example` are rejected. A TESTBOT can also register private or loopback IP addresses, single-label hosts and private DNS suffixes. ARX Messenger opens Mini Apps only over HTTPS, so use HTTPS for these too. Do not register an `http://` origin: while one exists, none of your bot's Mini Apps open.
- URLs cannot contain a user name, password or fragment.
- Saving a Main Mini App or named Mini App URL registers its origin automatically.

Remove an origin only when no app, button or menu still uses it.

### Origin protection <a href="#origin-protection" id="origin-protection"></a>

Your page can navigate to other HTTPS sites, for example during a sign-in flow. The bridge works fully only on your registered origins.

By default, ARX ignores bridge calls from a page outside your origins. They get no answer and time out with `NETWORK_ERROR`, and `whenInitialized()` does not resolve.

You can relax protection in NexoCreator's **Mini Apps** page. NexoCreator asks you to confirm the change. With relaxed protection, an HTTPS page outside your origins can use presentation methods, such as theme, buttons and popups. Every other method rejects with `ORIGIN_NOT_ALLOWED`. ARX never delivers launch data to a page outside your origins.

## Main Mini App <a href="#main-mini-app" id="main-mini-app"></a>

The Main Mini App is your bot's primary app. Set its URL and its launch mode with `/setmainapp`.

| Launch mode | Effect |
| --- | --- |
| `fullsize` | Opens at full height. |
| `compact` | Opens in a shorter sheet that the user can expand. |
| `fullscreen` | Opens in full screen. |

People open the Main Mini App with `https://www.arx.pro/bot/<HANDLE>?startapp=`. Its launch data has `launch_kind` set to `main_app`. A Main Mini App is also required for the preview gallery, the store listing and the join request guard.

## Named Mini Apps <a href="#named-mini-apps" id="named-mini-apps"></a>

A named Mini App has its own short name, URL, title, description and cover. Each one gets a direct link: `https://www.arx.pro/bot/<HANDLE>/<short_name>`. Its launch data has `launch_kind` set to `direct_link` and `app` set to the short name.

A bot can have up to 20 named Mini Apps. You cannot change a short name after you create the app.

| Field | Rule |
| --- | --- |
| Short name | 3 to 32 characters: lowercase letters, digits and underscores, starting with a letter. Names that start with `arx` or contain reserved words such as `admin`, `support` or `official` are rejected. |
| Title | 1 to 64 UTF-16 units. Required. |
| Description | Up to 512 UTF-16 units. |
| Launch mode | `fullsize`, `compact` or `fullscreen`. |
| Age rating | `4+`, `9+`, `12+` or `17+`. |
| Request messaging permission | When on, opening the app can grant your bot permission to message the user. |
| Translations | Up to 100 languages, each with its own title and description. |

## Menu button <a href="#menu-button" id="menu-button"></a>

The menu button sits next to the message field in a private chat with your bot. Set it with `/setmenubutton`, or from your bot with `menu_button.set`. A Mini App menu button opens its URL with `launch_kind` set to `menu_button`. [Launch a Mini App](launch.md#menu-button) shows the Bot API call.

## Launch screen and previews <a href="#launch-screen-and-previews" id="launch-screen-and-previews"></a>

`/setsplash` sets the icon and the light and dark background and header colors shown while your page loads. Colors use `#RRGGBB`.

`/setpreviews` manages up to 12 preview images or videos per language. People see them before they open your Main Mini App.

## Attachment menu, side menu and store listing <a href="#attachment-menu-side-menu-and-store-listing" id="attachment-menu-side-menu-and-store-listing"></a>

You can offer your app in the attachment menu of private chats and in the side menu. ARX reviews production placement before it appears. The store listing helps people discover your Mini App and also requires review. Review checks previews, privacy policy, support URL, age rating and bot eligibility. TESTBOTs cannot submit a store listing.

## Errors <a href="#errors" id="errors"></a>

NexoCreator explains a rejected value next to the field. In the Bot API, a button or menu URL on an unregistered origin returns HTTP 400 `mini_app_origin_not_registered`, and a Mini App button for a bot without Mini Apps returns HTTP 403 `capability_not_available`.

Next, [launch your Mini App](launch.md) from a menu button, a keyboard button or a link.
