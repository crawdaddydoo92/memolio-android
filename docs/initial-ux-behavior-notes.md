# Memolio — Initial UX & Behavior Notes
**Date:** 2026-10-20  
**Status:** Observations and ideas; no code changes made

## Observed behavior

- The app is running on the Galaxy Tab A 8.0 (2019), Android 11.
- Tapping the displayed photo activates the app’s pause behavior. Need to understand exactly what the app treats as a pause, especially when opening the full menu.
- The QR code opens a local web uploader. It supports choosing multiple individual photos, but not selecting a directory. Drag-and-drop is advertised, but did not work in the laptop test; choosing files through the picker did.
- Uploaded photos are stored in app-private storage. The upload route passes image bytes to `MediaImporter`, which writes the original, display cache, and thumbnail through `FileStorage`.
- The settings page groups slideshow/playlist options with clock and date settings. Clock/date controls may be easier to find under a separate display/overlay heading.
- Photo-fit modes behave differently:
    - Fill/crop uses the full screen but can cut off important parts of a photo.
    - Fit/bars preserves the full photo, with black bars where aspect ratios differ.
    - The current blurred-fill appearance does not produce the soft, defocused background seen in the reference frame.
- The heart and menu icons can be difficult to see over bright or busy photos.
- Existing thumbnails were shown as a baseline; no thumbnail redesign requested.

## Ideas to investigate—not decisions yet

- Add a user-friendly import-from-device workflow, potentially supporting Android’s system photo picker and microSD access, while importing copies into Memolio’s own managed storage.
- Improve blurred fill by scaling a background copy of the same photo to cover the display, applying a real blur, and placing the correctly fitted sharp photo above it.
- Consider a dark grey or black backing behind the heart and menu controls for consistent visibility.
- Review the settings grouping, perhaps separating slideshow behavior from clock/date/caption overlays.
- Determine whether opening the full menu pauses the existing slideshow or starts a new session. Treat the pop-up menu as a pause for now; timer behavior while it is open remains undecided.

## Scope reminder

These are discovery notes from testing the existing app. No implementation or design decisions have been finalized.