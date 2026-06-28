# Core Concepts

Understanding the core objects and architectural components of Nebula is key to designing efficient workflows and system integrations.

---

## System Architecture

### Site
A **Site** represents a single Nebula installation. A site can represent a broadcaster or a broadcast network with one or more channels sharing settings, users, and asset catalogs.
* Each site has its own standalone database schema, configuration templates, and user access levels.
* To distribute heavy workloads, a single site can run across multiple physical or virtual servers.
* The backend foundation of a site consists of a **PostgreSQL** database, a **Redis** queue, and one or more **shared storages**.

### Server
The **Nebula Server** provides the core Web API and serves the client-side user interface. 
* The server must have direct network access to the PostgreSQL database, the Redis message broker, and the low-res media storage used for proxy files and user uploads.

### Worker
A **Worker** is a background manager daemon that spawns and controls Nebula **Services** (small, task-specific programs).
* Services perform automated tasks such as technical metadata extraction, transcoding, watchfolder monitoring, and playout engine control.
* Workers are highly scalable and can be distributed across different machines on your network, allowing media processing tasks (like rendering and transcoding) to scale out horizontally.
* You can also develop custom, site-specific services to integrate specialized broadcast hardware or proprietary APIs.

---

## Asset Management

### Asset
An **Asset** is the primary database record in Nebula. It represents a single logical unit of media or metadata and can hold an unlimited number of metadata attributes, technical details, and associated physical media files.

### Folder
Assets sharing identical default properties and metadata fields belong to the same **Folder**. Examples include:

* **Movie:** A feature-length, standalone film.
* **Episode:** An individual episode belonging to a larger series.
* **Story:** A short-form news item or package.
* **Song:** A single music track or music video.
* **Fill:** Short-form interstitial media (e.g., promotional clips) used to bridge programming gaps.
* **Trailer:** A promotional teaser for movies or shows.
* **Jingle:** A brief channel identifier or audio watermark.
* **Commercial / Teleshopping:** Paid advertisements (short-form or infomercial formats).
* **Dataset:** A container for non-video files (such as XML schedule imports, CSV logs).
* **Incoming:** The default temporary landing folder for newly detected, unclassified assets.
* **Serie:** A parent virtual asset grouping multiple child episodes together.

!!! tip "Custom Folders"
    Although Nebula permits customization of folders, some default plugins and external automation integrations expect the default folders list to remain present.

### Content Type
The physical nature of an asset's data is defined by its **Content Type**:

* `AUDIO` 
* `VIDEO` 
* `IMAGE` 
* `TEXT`
* `DATABROADCASTING` 
* `INTERSTITIAL`
* `EDUCATION` 
* `APPLICATION` 
* `GAME` 
* `PACKAGE`

### Media Type
An asset doesn't need to link to a physical media file. This is determined by its **Media Type**:

* **FILE:** Represents a physical file on one of the registered storages.
* **URI:** Points to an external stream or network URL.
* **VIRTUAL:** Contains no physical media files; it acts purely as a metadata container (e.g. series metadata).

### View

**Views** are customized asset filters used in the Nebula Browser. Each view configures which folders, asset statuses, and metadata columns (such as duration, date, or author) are displayed. Within a view, you can execute full-text searches and advanced queries.

### Actions & Jobs

* **Action:** A predefined, reusable process (e.g., "Transcode to MXF for Playout", "Archive to LTO Tape"). Actions define the conditions under which an asset can be processed.
* **Job:** A specific execution of an action applied to an asset. Jobs can be triggered automatically (e.g., on watchfolder ingest) or manually.

---

## Linear Broadcasting

### Channel
A **Channel** represents a linear playout output. A single Nebula site can manage multiple playout channels concurrently. For installations dedicated solely to Video-on-Demand (VOD) or media archiving, Nebula can run without any channels.

### Event
A calendar entry corresponding to an EPG block. An **Event** belongs to a channel and has a fixed start time and descriptive metadata (Title, Description, Category).

### Item
An **Item** is an instance of an asset scheduled inside an Event rundown playlist. You can also insert special items that do not reference assets, such as live inputs, graphic overlays, or timing placeholders.

### Rundown
A chronological list of all events and playlist items scheduled for a channel on a specific calendar day. The **Rundown** is the primary view used by broadcast operators to control and monitor the active playout.

### As-Run Log
An automatic audit trail documenting the exact times that items were played. Nebula compiles this log for compliance reports, copyright auditing, and commercial billing exports.
