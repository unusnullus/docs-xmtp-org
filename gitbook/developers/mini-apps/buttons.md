---
description: "Use the native main, secondary, back and settings buttons, show popups and control how your Mini App closes."
---

# Buttons, popups and closing

ARX draws native controls around your page. Use them instead of your own bottom buttons and back arrows, so your app follows the user's theme and behaves like the rest of ARX Messenger.

## Main and secondary buttons <a href="#main-and-secondary-buttons" id="main-and-secondary-buttons"></a>

`MainButton` and `SecondaryButton` appear in the bottom bar. They start hidden. Set a label, add a click handler and show them.

```js
const app = window.AERX.WebApp;
await app.whenInitialized();

app.MainButton.setText("Place order").onClick(placeOrder).show();
app.SecondaryButton.setParams({ text: "Save for later", position: "left", is_visible: true });
app.SecondaryButton.onClick(saveForLater);

async function placeOrder() {
  app.MainButton.showProgress();
  try {
    await submitOrder();
    app.HapticFeedback.notificationOccurred("success");
    app.close();
  } catch {
    app.MainButton.hideProgress();
    app.showAlert("We could not place your order. Try again.");
  }
}
```

`showProgress()` shows a spinner and disables the button, so a second tap does not submit twice. Pass `true` to keep it active. `hideProgress()` removes the spinner and enables the button again.

`setParams` accepts these fields:

| Field | Value |
| --- | --- |
| `text` | Up to 64 characters. Leading and trailing spaces are removed. An empty label hides the button. |
| `color`, `text_color` | `#RRGGBB`. Without them, the button uses the theme's button colors. |
| `is_visible`, `is_active`, `is_progress_visible`, `has_shine_effect` | Booleans. |
| `position` | For the secondary button, its place next to the main button: `left`, `right`, `top` or `bottom`. |

If you show a button without setting a label, ARX uses a localized default label.

## Back button <a href="#back-button" id="back-button"></a>

`BackButton` appears in the header. Show it when the user is on an inner screen, and handle the click to go back one step.

```js
const app = window.AERX.WebApp;
const screens = ["cart"];

function open(screen) {
  screens.push(screen);
  render(screen);
  app.BackButton.show();
}

app.BackButton.onClick(() => {
  screens.pop();
  render(screens[screens.length - 1]);
  if (screens.length === 1) app.BackButton.hide();
});
```

## Settings button <a href="#settings-button" id="settings-button"></a>

`SettingsButton` adds **Settings** to the Mini App menu. Turn it on for your bot with `/setappsettings` in NexoCreator, then call `SettingsButton.show()` and handle `onClick` to open your settings screen.

## Popups <a href="#popups" id="popups"></a>

Use native popups for short questions and confirmations.

```js
const app = window.AERX.WebApp;

const confirmed = await app.showConfirm("Remove this item from your cart?");
if (confirmed) removeItem();

const choice = await app.showPopup({
  title: "Delivery",
  message: "How would you like to receive your order?",
  buttons: [
    { id: "pickup", type: "default", text: "Pick up" },
    { id: "courier", type: "default", text: "Courier" },
    { type: "cancel" },
  ],
});
```

`showPopup` resolves with the `id` of the pressed button, or `null` when the user dismisses the popup. A button `type` of `ok`, `close` or `cancel` uses a localized label; `default` and `destructive` need your own `text`. A popup has a title of up to 64 characters, a message of 1 to 256 characters and one to three buttons.

Only one popup can be open at a time, and the page can open at most three popups in three seconds. Further calls reject with `RATE_LIMITED`.

## Haptic feedback <a href="#haptic-feedback" id="haptic-feedback"></a>

When `supports("haptics")` is true, use `HapticFeedback` to confirm actions: `impactOccurred("light")` for a tap, `selectionChanged()` when a selection changes, and `notificationOccurred("success")`, `"warning"` or `"error"` for results.

## Closing <a href="#closing" id="closing"></a>

Call `close()` to close the app, for example after a completed purchase.

When the user has unsaved changes, call `enableClosingConfirmation()`. ARX then asks the user to confirm before closing the app. Call `disableClosingConfirmation()` after you save. `isClosingConfirmationEnabled` shows the current state.

The user can also close or minimize the app with a downward swipe. If your page uses vertical gestures, call `disableVerticalSwipes()` while they are active.

## When the app goes to the background <a href="#when-the-app-goes-to-the-background" id="when-the-app-goes-to-the-background"></a>

When the user switches away, ARX emits `deactivated`. Button clicks stop and pending calls reject with `SESSION_EXPIRED`. When the user returns, ARX emits `activated` and sends fresh launch data. Restart sensors and repeat any call that failed with `SESSION_EXPIRED`. If the app stayed in the background for more than about two minutes, ARX starts a new launch with a new `launch_id`. Launches with a `query_id` cannot resume: ARX closes the app, and the query expires.

```js
app.onEvent("deactivated", () => pauseVideo());
app.onEvent("activated", () => resumeVideo());
```
