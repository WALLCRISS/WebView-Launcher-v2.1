# WebView Launcher v2.1: Update Notes

What changed between **v1.4** and **v2.1**: new features, adjustments to existing behavior, and differences in permissions and structure.

| | v1.4 | v2.1 |
|---|---|---|
| **Version / versionCode** | 1.4 / 14 | 2.1 / 21 |
| **Package** | `com.webviewlauncher.app` | `com.webviewlauncher.app` |
| **Min / Target SDK** | 21 / 34 | 21 / 34 |
| **Language** | Indonesian | Indonesian (default) + English toggle |

---

## Overview

v1.4 was a launcher for HTML projects (`.zip` / `.html`). v2.1 keeps that and grows it into a small offline web-development toolkit: a built-in **file manager and code editor**, a **device scanner**, a **library** of reusable assets, **themes**, **language switching**, and **explicit control over internet access**.

---

## What's New in v2.1

### 1. Two main menus
The main screen now has two tabs (bottom bar, flat style, no shadow):
- **Grid Launcher**: the original menu from v1.4 (add `.zip`/`.html`, run from a grid).
- **Text Editor**: new file manager and code editor (see below).

### 2. Text Editor (file manager + code editor)
- **Main folder** chosen through Android's Storage Access Framework, so no "all files" permission is needed for this feature. A **Change main folder** button appears at the main folder level.
- **Import** (top left): bring in any type of file, multiple at once, into the currently open folder.
- **Export .zip** (top right): export all files and assets of the current folder to the Download folder, named after the folder.
- **Last 3 edited files** shown as a horizontally scrollable row above the folder contents.
- **Add button**: *Add file* (free name and extension, opens straight into the editor) and *Add folder*. Rename or delete through the item's action menu.
- **Full-screen editor** with:
  - line numbers and syntax highlighting (lightweight, only the visible part is colored, handles tens of thousands of lines)
  - find & replace (case option and regex)
  - format HTML / CSS / JS
  - undo / redo, copy / paste / clear (clear has two modes: undoable, or permanent)
  - go to line, duplicate / delete / move line, comment toggle, indent / outdent
  - auto-complete for `{ } ( ) [ ] " ' \``, closing HTML tags, `</`, and `<!--`
  - quick symbol row, font size control, auto-save with a status indicator
  - color badge per file type
- **Run** button:
  - `.html` / `.htm` / `.xhtml` / `.svg` render visually (the folder is temporarily copied so relative CSS/JS/images load)
  - `.js` is executed and console output is shown as text
  - other files (`.py`, `.css`, etc.) are shown as text only, not executed
- Long lines wrap downward instead of scrolling sideways; line numbers stay correct.
- Files larger than 20 MB, and binary files, are not opened in the editor.

### 3. Code viewer
- A **View Code** button in File Information. For `.zip` projects, folders are listed first and any text file inside can be picked.
- Custom canvas-based viewer (`CodeLinesView`) that stays smooth on very large files, with go-to-line, fling, and a fast scroller.

### 4. Device Scan
- **Scan Device** finds folders that contain `html` / `css` / `js` / `zip` files and lists only those.
- Available in *Add HTML* (html/zip) and in *Library* (html/css/js/zip, multi-select).
- Requires "All files access" (Android 11+) or storage read permission (Android 10 and below). Used only for scanning.

### 5. Live preview from other apps
- WebView Launcher now appears in the **Open with** list for `.html`, `.htm`, `.css`, `.js`, and `.mjs` files (`PreviewActivity`).

### 6. Navigation menu (hamburger, top right)
Animated panel containing **Library & Assets**, **Theme**, **Language**, **Internet Access**, **Background Activity**, and the app version.

### 7. Library & Assets
- Store CSS, JS (e.g. Bootstrap), images, video, fonts, `.zip`, and any other file inside the app.
- In your HTML just write the file name (for example `<link href="bootstrap.min.css">` or `<img src="logo.png">`) as if it were in the same folder. `.zip` files are extracted into folders.
- Files stay safe even if the originals are deleted from the device.

### 8. Theme
- **Mode:** dark (default) or light.
- **Color:** Purple (default), Orange, Brown, Cyan, Red, Blue, Yellow, Silver, or **Custom** (HSV color wheel, brightness slider, RGB fields, and hex field).
- Saved in the app's preferences.

### 9. Language toggle
- Indonesian (default) / English. The header "WebView Launcher" does not change.

### 10. Internet Access control
- **Off by default.** When off, the app is 100% offline: all outgoing `http` / `https` / `ws` requests are blocked, including CDNs and localhost.
- When on, pages may load CDNs, open localhost, and browse. If the device has no connection, it behaves as if off.
- A warning dialog appears the first time you turn it on.
- Turns itself off again when you leave the app, and every new process starts in the off state.

### 11. Background Activity
- When on, a foreground service (with a notification) keeps the app alive while a page is open, and the Internet toggle does not auto-turn-off when you leave the app.
- Android or device-maker battery savers may still restrict the app.

### 12. Responsive UI
- Single-line labels shrink automatically on small screens (`android:tag="fit"`).
- UI guidelines added in `docs/UI_GUIDELINES.md`, plus a layout checker: `python3 tools/check_layouts.py`.

---

## Adjustments to Existing Behavior

- The main screen is no longer a single list: it is now a **tabbed** layout (Grid Launcher / Text Editor), using ViewPager2.
- The **Grid Launcher** menu itself works as in v1.4: add `.zip`/`.html`, optional name, automatic name and icon, long-press actions (Info / Delete), File Information dialog.
- **File Information** gains a **Code** row with the **View Code** button.
- **Add HTML** gains the **Scan Device** shortcut.
- The web game screen now **respects the Internet Access toggle**: external requests are blocked while it is off.
- The web game screen can resolve **Library** files by name.

---

## Permissions: v1.4 vs v2.1

v1.4 declared no extra permissions. v2.1 declares the following:

| Permission | Why |
|---|---|
| `INTERNET`, `ACCESS_NETWORK_STATE` | Internet Access toggle (blocked by the app while off) |
| `FOREGROUND_SERVICE`, `FOREGROUND_SERVICE_SPECIAL_USE`, `POST_NOTIFICATIONS` | Background Activity service and its notification |
| `READ_EXTERNAL_STORAGE` (Android 12L and below) and `MANAGE_EXTERNAL_STORAGE` | **Scan Device** only |
| `WRITE_EXTERNAL_STORAGE` (Android 9 and below) | Exporting `.zip` to the Download folder (Android 10+ uses MediaStore, no permission) |

Other technical changes: cleartext (plain `http`) traffic is allowed at the system level so localhost and `http` sites can open when Internet Access is on; the app still blocks all outgoing requests when the toggle is off.

---

## Quick Comparison

| Feature | v1.4 | v2.1 |
|---|---|---|
| Run `.zip` / `.html` projects | Yes | Yes |
| Automatic name and icon | Yes | Yes |
| File Information dialog | Yes | Yes + View Code |
| Splash screen, adaptive icon | Yes | Yes |
| Built-in code editor / file manager | No | **Yes** |
| Run code from the editor | No | **Yes** |
| Export folder as `.zip` | No | **Yes** |
| Code viewer | No | **Yes** |
| Device Scan | No | **Yes** |
| Open with (`.html`/`.css`/`.js`) | No | **Yes** |
| Library & Assets | No | **Yes** |
| Theme (dark/light + colors) | No | **Yes** |
| Language switch (ID / EN) | No | **Yes** |
| Internet Access toggle | No | **Yes** |
| Background Activity | No | **Yes** |

---

## Upgrade Notes

- The app now asks for more permissions than v1.4 (see table). Storage-related permissions are only requested when you use **Scan Device** or **Export**.
- Internet Access starts **off**. Turn it on from the navigation menu if your project needs a CDN or localhost.
- The Text Editor needs you to pick a **main folder** the first time. Android does not allow the Download folder or the storage root to be chosen directly, so create or pick a subfolder.
- `.py` files can be edited but are not executed.

---

## Build

Standard Android Studio project (Gradle, pure Java, 49 source files).

1. Open the `WebViewLauncher` folder in **Android Studio**.
2. Wait for Gradle sync (internet needed the first time).
3. `Build` → `Build Bundle(s) / APK(s)` → `Build APK(s)`.
4. Output: `app/build/outputs/apk/debug/app-debug.apk`. Or run `./gradlew assembleDebug`.

Dependencies: AndroidX Core, AppCompat, Material, ConstraintLayout, ViewPager2, RecyclerView, Fragment, Activity, WebKit.

---

*The v1.4 side of this comparison comes from the v1.4 APK (manifest, resources, class list). The v2.1 side comes from the v2.1 source code.*
