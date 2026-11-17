# Nebula Broadcast Automation & MAM

**Nebula** is a professional, open-source media asset management (MAM) and broadcast automation system engineered from the ground up for demanding, 24/7 broadcast environments.

It serves as the central orchestrator for media assets, scheduling, and playout control, offering broadcast operators a unified, lightning-fast workflow.

---

<div class="grid cards" markdown>

-   **:material-rocket-launch: Getting Started**
    
    Understand the architecture, main system services (Server, Worker, Services), and user interfaces.
    
    [:octicons-arrow-right-24: Learn Core Concepts](basics/concepts.md) · [Tour the UI](basics/interface.md)

-   **:material-docker: Deployment**
    
    Get Nebula up and running quickly using Docker Compose. Set up database, queue, server, and workers.
    
    [:octicons-arrow-right-24: View Installation Guide](installation/docker.md)

-   **:material-cog: Configuration**
    
    Customize settings templates, register local/shared storage points, and set up watch folders.
    
    [:octicons-arrow-right-24: Settings System](setup/configuring_nebula.md) · [Configure Storages](setup/storages.md)

-   **:material-play-circle: Playout Integration**
    
    Integrate with CasparCG Server for high-performance graphic overlays, audio, and video playout.
    
    [:octicons-arrow-right-24: Playout Setup](setup/casparcg.md) · [Linear Rundowns](usage/linear-broadcasting.md)

</div>

---

## What Nebula Can Do

Everything below happens in one web interface, which updates live as
colleagues and background services change things.

### Manage media

* **Find anything fast:** Browse the library in [tabs](usage/asset-browser.md#tabs), with [thumbnails](setup/thumbnails.md) if you like, narrow it down with [search](usage/asset-browser.md#searching) and [filters](usage/asset-browser.md#filters), and keep the combinations you use often as [saved views](usage/asset-browser.md#views).
* **Describe it your way:** [Folders](basics/concepts.md#folder) give each kind of content (movies, episodes, commercials, jingles...) its own metadata form, and [links between folders](usage/asset-browser.md#links-between-folders) connect related assets, such as a series and its episodes.
* **Preview and trim in the browser:** Play a proxy of any asset with frame accuracy, [trim](usage/video-preview.md#trimming) it, mark [sub-clips](usage/video-preview.md#sub-clips) and pick a [poster frame](usage/video-preview.md#poster-frame), all [from the keyboard](usage/video-preview.md#keyboard-shortcuts) if you like.
* **Transcription (coming soon):** Automatic speech-to-text for your assets, so you can search what was said and select a passage straight from the transcript.

### Get media in

* **Watch folders:** Files dropped on a share [become assets automatically](setup/watchfolders.md), with their technical metadata filled in.
* **Upload and import:** [Upload from the browser](setup/import.md#uploading-from-the-web-interface) or let the [import service](setup/import.md#the-import-service) transcode incoming files to your house format.
* **Background jobs:** [Actions](basics/concepts.md#actions-jobs) create proxies, transcode and deliver media without anyone clicking, and the [jobs monitor](basics/interface.md#3-jobs-monitor) shows their progress.

### Schedule and play out

* **Plan the week:** Lay out programme blocks in the [scheduler](usage/linear-broadcasting.md#scheduler) calendar, by hand or from a scheduling template.
* **Build the rundown:** Drag assets from the browser into the [rundown](usage/linear-broadcasting.md#rundown). Use [placeholders](usage/linear-broadcasting.md#placeholders) for media that has not arrived yet, add [live events](usage/linear-broadcasting.md#live-events) and [lead-ins and lead-outs](usage/linear-broadcasting.md#lead-inout).
* **Go on air:** Control the channel with the [on-air controls](usage/linear-broadcasting.md#on-air-controls), and decide per event and per item whether it starts automatically or waits for the operator.
* **Know what aired:** Every played item lands in the [as-run log](basics/concepts.md#as-run-log).
* **Playout engines:** Production playout runs on [CasparCG](setup/casparcg.md). Conti, a lightweight software engine, is also available, but it is meant for testing only.

### Fit it to your station

* **Configuration as code:** Channels, folders, metadata fields and services are defined in [settings files](setup/configuring_nebula.md) you can keep under version control.
* **Your own rules:** [Validators](setup/validators.md) check and complete metadata whenever an asset is saved.
* **Scale out:** [Workers](basics/concepts.md#worker) on several machines share transcoding, metadata extraction and playout control.
* **Open source and open API:** Everything the interface does goes through a documented HTTP API, so other systems can do it too.
