# User Interface Overview

The Nebula client interface provides access to the media catalog, metadata editor, background tasks monitor, and rundown scheduler.

---

## 1. Asset Browser

The **Asset Browser** is the primary catalog explorer for searching and organizing your media collection.

* **Tabs, Views and Filters:** Keep several searches open in tabs, each with its own view, filters and columns. See [Asset Browser](../usage/asset-browser.md) for the details.
* **Asset Browser List:** Displays assets matching the tab's view and filters. Clicking an asset opens it in the **Asset Detail** panel.
* **Context Menu:** Right-clicking the selected assets offers:
    * *Filter by* the clicked column's value (adds a condition to the tab).
    * *Send to...*: start an action (e.g. a transcode or sending to playout) for the selection.
    * *Reset*: reload the assets' technical metadata from their files.
    * *Archive* and *Trash*.
* **Drag and Drop:** Drag assets to the Scheduler to create events, or to the Rundown to add items.

---

## 2. Asset Detail Panel

The **Asset Detail** panel acts as the main metadata editor and proxy media player.

* **Metadata Fields:** Allows users to view and edit metadata tags. The form updates dynamically based on the asset's assigned **Folder** (e.g., episode metadata fields differ from music video fields).
* **Offline Status:** When a new asset record is created without a file (a "placeholder"), its status is set to *Offline*. You can manually assign an estimated duration to schedule it. Once the physical file is matched (e.g., via watchfolders or FTP naming conventions), the status changes to *Online* and Nebula updates the database record with the exact technical duration.

---

## 3. Jobs Monitor

The **Jobs** page lists background jobs (transcoding, sending to playout...) in three views: *Active*, *Finished* and *Failed*.
* Shows each job's asset, action, service, times and status message.
* Pending and running jobs can be aborted, finished and failed ones restarted.
* Clicking a job's priority cycles it through *Hold*, *Lowest* ... *Highest*; workers pick up higher-priority jobs first.

---

## 4. Scheduler

The **Scheduler** shows a week of events for the selected channel. See [Linear Broadcasting](../usage/linear-broadcasting.md#scheduler) for the details.
* **Event Creation:** Drag assets from the Browser into the calendar, or drag the *Empty event* icon from the toolbar.
* **Moving Events:** Events have no fixed end time; drag them to a new start time.
* **Open Rundown:** Double-click an event to open its day in the Rundown, scrolled to the event.

---

## 5. Rundown

The **Rundown** is the item-by-item playlist of one channel's broadcast day. Three toggles in its toolbar add tools on top of the read-only view: **Edit** (change the rundown), **Control** (playout controls and on-air clock) and **Plugins** (playout plugins). See [Linear Broadcasting](../usage/linear-broadcasting.md#rundown).
