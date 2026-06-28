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
    
    [:octicons-arrow-right-24: Setup settings.py](setup/configuring_nebula.md) · [Configure Storages](setup/storages.md)

-   **:material-play-circle: Playout Integration**
    
    Integrate with CasparCG Server for high-performance graphic overlays, audio, and video playout.
    
    [:octicons-arrow-right-24: Playout Setup](setup/casparcg.md) · [Linear Rundowns](usage/linear-broadcasting.md)

</div>

---

## Key Features

* **Modular Architecture:** Distributed Nebula Workers allow you to scale transcoding, metadata extraction, and playout processes across multiple machines.
* **Unified Web Interface:** Manage media ingest, edit complex asset metadata schemes, and oversee active background jobs.
* **Linear Playout Control:** Full rundown and EPG scheduling in real-time, with tight integration into playout engines like CasparCG.
* **Smart Ingest:** Automatically ingest assets from multiple network shares using customizable watch folders with post-processing scripts.
