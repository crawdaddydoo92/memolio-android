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

---

## Photo Library, Uploading, and Frame/Sender Direction

### Findings and design direction

- The current QR uploader uses a local `192.168.x.x` URL, so it operates over the local network. The tablet can serve as the frame’s own server; a cloud middleman is not needed for local phone-to-frame transfers.
- The current QR upload experience is not a preferred long-term workflow. In particular, it does not accept directory uploads, and the user must navigate to the QR flow to send photos.
- The desired long-term product is a **frame/sender setup**:
  - **Frame mode:** Hosts the photo library and slideshow, and receives photos from sender devices.
  - **Sender mode:** Connects to a frame on the same network and sends photos to it.
  - Keep the roles separate in the initial implementation. A single app can offer the role choice during setup.
- For local sending, the frame tablet can host the server and the sender phone can connect directly over Wi-Fi. Remote sending is a separate future feature and may require a relay or other remote-access architecture.
- The existing QR uploader may serve as a development fallback, but should not define the final sender experience.

### Photo library and album organization

- Explore storing the frame’s library in a user-accessible location such as `Pictures/Memolio/`, with album subfolders—for example, `Pictures/Memolio/Vacation/`.
- Users should be able to add photos and organize folders through Android’s file manager or Windows File Explorer over USB.
- The frame should discover the library and its album folders, rather than requiring photos to exist only in Memolio’s private app storage.
- Album creation already exists in the app. Inspect its validation and naming behavior before designing new collision-handling logic.
- Android storage access needs investigation: creating a public folder does not automatically grant the app unrestricted access. Determine how to obtain and retain permission to the selected folder, including child folders and write access for uploads.
- Check how the chosen storage approach interacts with Android Gallery indexing and sharing. A goal is for users to be able to browse and share frame photos through familiar Android tools.

### Sender experience and upload behavior

- The desired sender workflow is to choose a frame, choose an existing album or create a new one, then send selected photos into that album.
- The QR landing page’s existing album-selection concept may inform the sender UI, but the native sender app should ultimately replace the QR step.
- Multi-photo sending is important. Whole-directory transfer would be useful, but support varies across Android file pickers and browsers; investigate rather than assume it is universally available.
- Ensure incoming photos are written to the same library that the frame scans, avoiding a separate hidden import destination or unnecessary duplicate library.
- Refreshing the frame’s library after an upload is desirable, but is not a primary interaction requirement for the current QR workflow: the QR page is not normally accessible while the slideshow is running.
- “Add from anywhere” or remote sending is a possible later feature, not part of the initial local-network scope.

### Sharing photos *from* the frame

- The user’s sharing interest is primarily **getting photos from the frame to other devices**, not making Memolio a target in Android’s Share menu for sending photos to the frame.
- Prefer a user-accessible library that can be browsed and shared using Android’s existing file manager, Gallery, and sharing mechanisms.
- Making Memolio itself a Share-menu destination for incoming photos is not currently a priority; it introduces separate Android integration and destination-selection complexity.

### Items to investigate

1. How the existing album-creation code validates names and handles duplicates.
2. How the current Ktor upload server selects its destination and how that could target a user-authorized library folder.
3. Whether the existing `FileStorage` abstraction can support a folder granted through Android’s Storage Access Framework, or needs a storage abstraction that handles document URIs.
4. How the frame discovers album folders and notices newly added, removed, or changed photos.
5. How original photos, display caches, and thumbnails should be stored, and which derived files can be regenerated.
6. A simple, secure local-network discovery/pairing method for sender-to-frame transfers.

These are design notes and investigation targets, not implementation decisions yet.