# User Interface Overview

The Nebula client interface provides access to the media catalog, metadata editor, background tasks monitor, and rundown scheduler.

---

## 1. Asset Browser

The **Asset Browser** is the primary catalog explorer for searching and organizing your media collection.

* **Asset Browser List:** Displays assets matching the selected view. Double-clicking an asset opens it in the **Asset Detail** panel.
* **Context Menu:** Right-clicking assets allows operators to:
    * Archive or restore assets.
    * Move assets to the trash or recover them.
    * Trigger technical metadata extraction (re-analyze files).
    * Manually dispatch assets to background workflows (e.g., "Send to Playout").
* **Drag-and-Drop Ingest/Export:** If supported by the OS and client container, you can drag media assets directly from the Browser into external applications (such as DaVinci Resolve, Adobe Premiere Pro, or local file system folders).

---

## 2. Asset Detail Panel

The **Asset Detail** panel acts as the main metadata editor and proxy media player.

* **Metadata Fields:** Allows users to view and edit metadata tags. The form updates dynamically based on the asset's assigned **Folder** (e.g., episode metadata fields differ from music video fields).
* **Offline Status:** When a new asset record is created without a file (a "placeholder"), its status is set to *Offline*. You can manually assign an estimated duration to schedule it. Once the physical file is matched (e.g., via watchfolders or FTP naming conventions), the status changes to *Online* and Nebula updates the database record with the exact technical duration.

---

## 3. Jobs Monitor

The **Jobs** module provides real-time monitoring of all active background operations:
* Displays progress percentages and operational states (Queued, Running, Completed, Failed).
* Allows administrators to abort active jobs or restart failed actions.

---

## 4. Scheduler

The **Scheduler** displays a calendar-style week overview for planning linear broadcast channels:
* **Event Creation:** Drag assets from the Browser directly into the calendar grid to schedule new programming blocks.
* **Empty Blocks:** Drag the **Calendar +** icon from the toolbar to create an empty scheduling block for manual metadata entry.
* **End-Time Flexibility:** Events do not have locked end-times. They can be moved, resized, or adjusted directly from their context menus.
* **Open Rundown:** Double-clicking an event (or selecting *Open Rundown* from the menu) switches the view to the granular, item-by-item Daily Rundown.

---

## 5. Daily Rundown

The **Rundown** module manages the daily playlist for linear playout channels. It allows operators to arrange assets, configure playout modes, and directly control live video servers.
