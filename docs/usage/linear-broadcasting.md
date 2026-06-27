Linear broadcasting
===================

Scheduler
---------

Scheduler module displays all scheduled events in a week view.


On the most basic level, new events can be scheduled by just dragging and dropping assets from the Browser
into the scheduler calendar. It this case, a new event is created at the desired time/position,
assets descriptive metadata such as title and description attached to the event and within the event,
a new item from the selected asset is created.

It is also possible to create an empty event by drag-and-dropping **calendar&nbsp;+** icon from the scheduler toolbar to the calendar.
In this case, a dialog window requesting metadata entry pops-up.

Note that events don't have fixed end-time. You can move events freely using drag-n-drop
(or by setting the start time in the event detail dialog accessible from the context menu).

Double-click the event to switch to the [rundown](/doc/nebula/rundown.html) view and scroll to the chosen event.
The same can be achieved by selecting *Open rundown* entry from the event pop-up menu.

Rundown
-------

The rundown module allows item-by-item scheduling of the daily playlist.
Items can be created by dragging supported assets from the browser area to the rundown.


On-air controls
---------------

Since video servers take time to load a video file for playout, Nebula always loads the next clip while playing the current one.
The clip currently playing is highlighted red, while the next one (cued) is green. When the on-air mode is active, double-clicking 
the item in the rundown view cues it (and its playback starts while the current one is finished).

Additionally, you may use buttons from the on-air section to control the playback.

- **Take** plays an item which has been cued
- **Freeze** pauses or resumes playback of the current clip
- **Retake** replays the current item from the beginning
- **Abort** stops the current clips and pause at the cued one's first frame
- **Left arrow** cue the previous item
- **Right arrow** cue the next item

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
 - **Loop**: The item plays in a loop until the *Take* button is used to advance to the next item

Special items
-------------

Icons from the middle group of the toolbar can be dragged to the rundown to create the following special items:

### Placeholders

Placeholders can be automatically replaced by items using "solve" command from the context menu. 
This functionality depends on the site configuration and may not be available.

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

