# CHANGELOG

## 2026.05.10 (session 3)

### What Changed

Confirmed the theme works on real hardware under X11 SDDM (`sddm-greeter`, Qt5.15). Merged `breeze-background` branch into master. Identified that since the theme now works with both Qt5.15 and Qt6 greeters, merging back into the original `edu-sddm-simplicity` repo is worth considering next session.

### Technical Details

- Deployed with `sudo cp -r /home/erik/EDU/edu-sddm-simplicity-qt6/usr/ /` — full path required, running from wrong dir was the earlier failure
- Fast-forward merge from `breeze-background` to master; origin/master already up to date
- Theme now confirmed working: X11 Qt5.15 greeter on dev machine, both test-mode greeters verified

### Files Modified

None — merge and deployment only.

## 2026.05.10 (session 2)

### What Changed

Made the theme compatible with both Qt5.15 (`sddm-greeter`, X11) and Qt6 (`sddm-greeter-qt6`, Wayland). Switched from unversioned imports to `2.15` versioned imports — Qt5.15 requires a version number and Qt6 accepts versioned 2.x imports via its compatibility layer. Updated PKGBUILD depends from `qt6-declarative` to `sddm`.

### Technical Details

- `import QtQuick` (unversioned) is rejected by the Qt5.15 `sddm-greeter` even though Qt 5.15 theoretically supports it — `import QtQuick 2.15` satisfies both greeters
- Qt6 accepts `import QtQuick 2.x` via a compatibility shim that maps them to the current Qt6 version
- `depends=('sddm')` is correct for an SDDM theme — Qt is a transitive dependency through the greeter binary
- Tested with both `sddm-greeter --test-mode` and `sddm-greeter-qt6 --test-mode`

### Files Modified

- `usr/share/sddm/themes/edu-simplicity/Main.qml`
- `usr/share/sddm/themes/edu-simplicity/SimpleControls/Button.qml`
- `usr/share/sddm/themes/edu-simplicity/SimpleControls/ComboBox.qml`
- `usr/share/sddm/themes/edu-simplicity/SceneBackground.qml`

## 2026.05.10 (session 1)

### What Changed

Replaced the broken background rendering with a proper multi-monitor implementation borrowed from the KDE breeze SDDM theme. Root element changed from `Rectangle` to `Item`. Background now uses `Repeater { model: screenModel }` so each physical display gets its own background instance. Work done on branch `breeze-background`.

### Technical Details

- Copied `Background.qml` from `/usr/share/sddm/themes/breeze/` — pure QtQuick, no KDE/Plasma dependencies
- Renamed to `SceneBackground.qml` to avoid name collision with `SddmComponents 2.0`'s own `Background` type
- Background image path passed via `Qt.resolvedUrl("images/background.jpg")` so the URL resolves relative to `Main.qml`
- `SddmComponents 2.0` import kept for `TextConstants` only
- PKGBUILD unchanged — `cp -r usr/` picks up `SceneBackground.qml` automatically

### Files Modified

- `usr/share/sddm/themes/edu-simplicity/Main.qml`
- `usr/share/sddm/themes/edu-simplicity/SceneBackground.qml` (new)

## 2026.05.09

### What Changed

Ported the theme from Qt5 to Qt6. The original QML used versioned imports (`QtQuick 2.12`, `QtQuick.Controls 2.12`) which fail on Qt6 SDDM builds. Replaced with unversioned imports. Also fixed the background image loading — the `SddmComponents` `Background` component resolves relative paths against its own location, not the theme directory. Replaced with a plain `Image` element using a theme-relative path.

### Technical Details

- Qt6 QML modules use unversioned imports; versioned imports are Qt5-only
- `SddmComponents.Background { source: config.background }` silently fails in Qt6 because the relative path resolves against Background.qml's own directory, not the theme root
- `depends` in PKGBUILD updated from `qt5-quickcontrols2 qt5-quickcontrols` to `qt6-declarative`
- Removed duplicate `makedepends=('git')` line from PKGBUILD

### Files Modified

- `usr/share/sddm/themes/edu-simplicity/Main.qml`
- `usr/share/sddm/themes/edu-simplicity/SimpleControls/Button.qml`
- `usr/share/sddm/themes/edu-simplicity/SimpleControls/ComboBox.qml`
