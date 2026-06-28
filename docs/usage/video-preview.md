# Video Preview & Trimming

The video preview player provides the asset detail interface with tools to view low-resolution proxies, define poster frames, trim clips, and generate sub-clips.

---

## Poster Frame

A **poster frame** is a representative frame extracted from the video file to serve as its search thumbnail (for example, in a Video-on-Demand archive).
* **How to set:** Navigate to the desired frame in the player timeline, then click the **Set poster frame** button on the toolbar.

---

## Trimming

Trimming allows you to define custom playback start and end points for an asset without altering the source file:
1. Seek to the desired start point and set the **IN** mark.
2. Seek to the desired end point and set the **OUT** mark.
3. Click **Save Marks**.

When you drag the asset onto a playlist, only the region between the IN and OUT marks will be played. VOD transcoders also respect these marks to crop output files.

---

## Sub-clips

Sub-clips allow you to non-destructively split a single parent media asset into multiple child clips (e.g. news segments, individual songs from a concert video). Sub-clips can overlap.

* **Create Sub-clip:** Set your **IN** and **OUT** marks, then click **Create sub-clip** in the toolbar.
* **Manage Sub-clips:** Rename, modify, or delete existing segments by clicking **Manage sub-clips**.

---

## Keyboard Shortcuts

The player interface keyboard shortcuts are derived from standard non-linear editors (like Avid Media Composer or Adobe Premiere), ensuring a familiar layout for video editors:

| Shortcut | Action / Description |
| :---: | --- |
| <kbd>1</kbd> or <kbd>J</kbd> | Seek backward 5 frames |
| <kbd>2</kbd> or <kbd>L</kbd> | Seek forward 5 frames |
| <kbd>3</kbd> or <kbd>Left</kbd> | Seek backward 1 frame (fine tuning) |
| <kbd>4</kbd> or <kbd>Right</kbd> | Seek forward 1 frame (fine tuning) |
| <kbd>A</kbd> or <kbd>Home</kbd> | Seek to the beginning of the file |
| <kbd>S</kbd> or <kbd>End</kbd> | Seek to the end of the file |
| <kbd>Q</kbd> | Go directly to the **IN** mark |
| <kbd>W</kbd> | Go directly to the **OUT** mark |
| <kbd>E</kbd> or <kbd>I</kbd> | Set **IN** mark |
| <kbd>R</kbd> or <kbd>O</kbd> | Set **OUT** mark |
| <kbd>D</kbd> | Clear the **IN** mark |
| <kbd>F</kbd> | Clear the **OUT** mark |
| <kbd>Space</kbd> or <kbd>K</kbd> | Toggle Play / Pause |
