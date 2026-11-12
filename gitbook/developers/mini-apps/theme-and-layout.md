---
description: "Match the user's ARX theme and fit your Mini App to the viewport, safe areas and full screen."
---

# Theme and layout

Your Mini App should look like part of ARX Messenger. Use the theme colors ARX provides, follow the user's light or dark scheme, and keep content inside the safe areas.

## Theme colors <a href="#theme-colors" id="theme-colors"></a>

`AERX.WebApp.themeParams` contains the user's current colors as lowercase `#rrggbb` strings. `colorScheme` is `light` or `dark`. When the user changes the theme, ARX updates both and emits `themeChanged`.

| Key | Use it for |
| --- | --- |
| `bg_color` | The page background. |
| `secondary_bg_color` | Secondary surfaces, such as grouped list backgrounds. |
| `section_bg_color` | Cards and sections on top of the background. |
| `header_bg_color` | The header area. |
| `bottom_bar_bg_color` | The bottom bar behind the main and secondary buttons. |
| `text_color` | Body text. |
| `hint_color` | Hints and placeholder text. |
| `subtitle_text_color` | Secondary text. |
| `section_header_text_color` | Section titles. |
| `accent_text_color` | Accented text. |
| `link_color` | Links. |
| `button_color`, `button_text_color` | Your own primary buttons. |
| `destructive_text_color` | Destructive actions. |
| `section_separator_color` | Separators between items. |

A key can be missing. Always provide a fallback.

## CSS variables <a href="#css-variables" id="css-variables"></a>

The bridge also writes the theme and layout to CSS custom properties on the root element, so you can style the page without JavaScript.

| Variable | Value |
| --- | --- |
| `--aerx-theme-<key>` | Each theme color, with underscores replaced by hyphens, for example `--aerx-theme-bg-color`. |
| `--aerx-color-scheme` | `light` or `dark`. |
| `--aerx-viewport-height` | The current viewport height, in `px`. |
| `--aerx-viewport-stable-height` | The height after the last completed resize, in `px`. |
| `--aerx-safe-area-inset-top`, `-bottom`, `-left`, `-right` | Device safe area insets, in `px`. |
| `--aerx-content-safe-area-inset-top`, `-bottom`, `-left`, `-right` | Insets for ARX controls drawn over your content, in `px`. |

```css
body {
  margin: 0;
  background: var(--aerx-theme-bg-color, #ffffff);
  color: var(--aerx-theme-text-color, #000000);
  padding-top: calc(var(--aerx-safe-area-inset-top, 0px) + var(--aerx-content-safe-area-inset-top, 0px));
  padding-bottom: var(--aerx-safe-area-inset-bottom, 0px);
}

a {
  color: var(--aerx-theme-link-color, #2962ff);
}

.card {
  background: var(--aerx-theme-section-bg-color, #f2f2f7);
  border-bottom: 1px solid var(--aerx-theme-section-separator-color, #d1d1d6);
}
```

ARX adds a fragment to your page URL with `aerxWebAppVersion`, `aerxWebAppPlatform` and `aerxWebAppThemeParams`, so the bridge can paint the right colors before initialization. The fragment carries no launch data. If your page uses hash-based routing, ignore these keys.

## Header, background and bottom bar <a href="#header-background-and-bottom-bar" id="header-background-and-bottom-bar"></a>

Set the colors of the ARX chrome around your page so it blends with your design.

```js
const app = window.AERX.WebApp;
app.setHeaderColor("secondary_bg_color");
app.setBackgroundColor("bg_color");
app.setBottomBarColor("bottom_bar_bg_color");
```

`setHeaderColor` and `setBackgroundColor` accept `#RRGGBB`, `bg_color` or `secondary_bg_color`. `setBottomBarColor` also accepts `bottom_bar_bg_color`. A theme key follows later theme changes; a hex color stays fixed. The resolved colors are in `headerColor`, `backgroundColor` and `bottomBarColor`.

## Viewport <a href="#viewport" id="viewport"></a>

The app opens in the launch mode you chose in NexoCreator or in the link: compact, full height or full screen. A compact app can grow when the user drags it or when you call `expand()`. `isExpanded` tells you whether it uses its full height.

`viewportHeight` changes while the user drags the sheet. `viewportStableHeight` changes only when a resize completes. Size fixed elements, such as a sticky footer, with the stable height to avoid jumps. `viewportChanged` reports `isStateStable` when the resize finishes.

The reported height already excludes the native main and secondary buttons. Do not reserve space for them yourself.

## Safe areas <a href="#safe-areas" id="safe-areas"></a>

`safeAreaInset` is the area the device reserves, such as a notch or a home indicator. `contentSafeAreaInset` is the area ARX's own controls cover, such as the close capsule in full screen. Both are objects with `top`, `bottom`, `left` and `right` in CSS pixels, and both have change events: `safeAreaChanged` and `contentSafeAreaChanged`. Keep interactive content inside both.

## Full screen and orientation <a href="#full-screen-and-orientation" id="full-screen-and-orientation"></a>

Check `supports("fullscreen")` before you call `requestFullscreen()`. ARX emits `fullscreenChanged` with `isFullscreen`, or `fullscreenFailed` with `UNSUPPORTED` or `ALREADY_FULLSCREEN`. Call `exitFullscreen()` to leave.

`lockOrientation()` and `unlockOrientation()` need `orientation_lock`. `isOrientationLocked` shows the current state.

## Vertical swipes and the keyboard <a href="#vertical-swipes-and-the-keyboard" id="vertical-swipes-and-the-keyboard"></a>

By default the user can swipe down to minimize or close the app. Call `disableVerticalSwipes()` when your page uses vertical gestures, such as a map or a drawing canvas, and `enableVerticalSwipes()` afterwards. `hideKeyboard()` closes the on-screen keyboard.

## Performance <a href="#performance" id="performance"></a>

`performanceClass` is `LOW`, `AVERAGE` or `HIGH`. Reduce animations and effects on `LOW` devices.
