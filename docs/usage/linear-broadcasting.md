# Linear Broadcasting

## Scheduler

The scheduler shows a week of events of the selected channel.

The quickest way to schedule is to drag an asset from the browser into the calendar. A new event is created at the drop position, with the asset's title and description, and the asset becomes the event's first item. Which folders can be dropped is set per channel (`scheduler_accepts`).

To create an empty event, drag the *Empty event* icon from the scheduler toolbar into the calendar; a dialog asks for its metadata.

Events have no fixed end time: an event lasts until the next one starts. Drag an event to move it, or change its start time with *Edit* in its context menu. *Delete* removes it.

Holding <kbd>Ctrl</kbd> while dropping an episode of a series opens *Schedule series*: the following episodes are scheduled into a recurring slot (daily or weekly on chosen days, until a date or for a number of episodes).

The toolbar also has *Apply template*, which fills the week from a scheduling template (if the channel has any), and *Delete unaired*, which removes the week's events that haven't been broadcast yet.

Double-click an event to open its day in the [rundown](#rundown), scrolled to the event.

Editing the schedule requires the `scheduler_edit` permission for the channel.

## Rundown

The rundown is the item-by-item playlist of one broadcast day. With all toggles off, it shows the day's events and items with their scheduled times. Three toggles in the toolbar add tools; they can be combined, and each is offered only to users with the permission for the current channel:

| Toggle | Adds | Permission |
| --- | --- | --- |
| **Edit** | Dragging assets from the browser into events, reordering items, the special items toolbar | `rundown_edit` |
| **Control** | On-air controls with the clock and progress, cueing by double-click | `mcr` |
| **Plugins** | The channel's playout plugins | `mcr` |

The toggles are remembered in the browser. Items and events can also be changed from their context menu (edit, run mode, delete).

## On-air controls

Since video servers take time to load a video file for playout, Nebula always loads the next clip while playing the current one.
The clip currently playing is highlighted red, the next one (cued) green. With **Control** on, double-clicking an item cues it; it starts when the current one finishes.

The on-air panel shows the current and the cued item with their remaining times, and these buttons:

- **Take** plays the cued item now
- **Freeze** pauses or resumes the current clip
- **Retake** replays the current item from the beginning
- **Abort** stops the current clip and pauses on the first frame of the cued one
- **Cue prev** / **Cue next** cue the previous or next item

By default, the application plays items one after another, but this behavior can be changed to create loops within the blocks,
wait conditions and so on using *run modes* both for events and items.

### Event run modes

 - **Auto**: Playback of the block starts right after the last clip of the previous block is finished. 
 - **Manual**: The previous block plays in a loop until an item from this block is manually cued.
 - **Soft**: The previous block plays in a loop until the start time of this block is reached. 
   After that, the first item of the block is cued, and starts when the currently playing clip is finished.
 - **Hard**: Similar to the *soft* mode, but when the event start time is reached, hard cut is used instead 
   waiting for the current clip to be finished.

### Item run modes

 - **Auto**: Playback starts automatically right after the last clip is finished.
 - **Manual**: The previous item stops at its last frame until *take* or *abort* button is pressed
   to advance to the next item.
 - **Skip**: The item won't be played and its duration is not accounted to the rundown computed time.
   This mode is useful for scheduling "backup" clips for the case live event ends earlier than expected.

## Special items

With **Edit** on, a toolbar with these special items appears; drag them into the rundown:

### Placeholders

A placeholder reserves time for content that isn't known yet. If the channel has solvers configured, *Solve using ...* in the placeholder's context menu replaces it with real items.

### Live events

When playback reaches the live event, the playout server is switched to its configured live source
(for example the feed from the studio).

### Lead in/out

Special markers to be used in looped blocks.
Lead-in marks the point, where the playback continues after reaching the end of the loop,
lead-out marks the end of the looped part.

#### Example

When the run mode of the next block is set to *soft*, the part before lead-in marker is played, then the part between both
markers plays in the loop and when the start time of the next block is reached, the current clip finishes,
then the part after the lead-out marker is played and finally, the next block starts.

