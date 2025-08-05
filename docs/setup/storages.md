Nebula storages
===============

Storage Types
-------------

### Production Storage

#### Characteristics

This environment requires high capacity to store large volumes of raw footage, master files, and intermediate assets. 
It needs good read/write performance to handle concurrent ingest and transcoding jobs, but can tolerate slightly 
higher latency compared to playout. 

Robust backup and archiving capabilities are also essential for long-term asset preservation.

#### Implementation

Network Attached Storage (NAS) or Storage Area Network (SAN) solutions are commonly employed, 
supporting standard network file protocols like NFS or Server Message Block (SMB)/Common Internet File System (CIFS).

A hybrid approach combining spinning disks for capacity and SSDs for frequently accessed media or work-in-progress projects 
can offer a balance of cost and performance.

### Playout Storage

#### Characteristics

This storage demands extremely high, consistent read performance with very low latency to ensure uninterrupted playback 
and prevent dropped frames. 

Capacity requirements are typically lower than production storage, as it only needs to hold media for the active broadcast schedule 
(e.g., a few days or weeks of content).

#### Implementation

Direct Attached Storage (DAS) with high-speed Solid State Drives (SSDs) directly connected to the CasparCG server 
is often preferred for maximum performance and lowest latency. 

Alternatively, a dedicated, high-performance volume on a SAN, specifically tuned for sequential reads, can be utilized. 
Redundancy mechanisms like RAID 10 or mirroring are essential to ensure continuous operation in the event of drive failure.


The table below summarizes the key differences and requirements for production and playout storage:


| Feature         | Production Storage                                | Playout Storage                                                   |
| --------------- | ------------------------------------------------- | ----------------------------------------------------------------- |
| Purpose         | File ingest, editing, archiving, high-res masters | Real-time playout, optimized delivery                             |
| Capacity        | Large (terabytes to petabytes)                    | Sufficient for active playout schedule (days/weeks)               |
| Performance     | High I/O for ingest/transcoding, moderate latency | High read speeds, very low latency, consistent throughput         |
| Access Patterns | Random read/write, large file transfers           | Sequential read, concurrent access by playout engines             |
| Redundancy      | High (RAID 6, distributed storage, backups)       | High (RAID 10, mirrored, fast failover)                           |
| Connectivity    | SAN, NAS (NFS, SMB), Cloud Storage                | Direct Attached Storage (DAS), high-speed SAN/NAS                 |
| File Formats    | Original camera formats, mezzanine codecs         | Playout-specific codecs (e.g., ProRes, DNxHD, H.264)              |


Configuration
-------------

Storages in Nebula are referenced by their ID (e.g., `1`, `2`, etc.) and storages are expected to be mounted in 
`/mnt/{site_name}_{id}` directory of the node (e.g., `/mnt/nebula_01`). When a storage is configured using a settings template,
its configuration is stored in the database and Nebula attempts to mount it on each node at startup (and keeps it mounted).

All media files in Nebula are then referenced by their storage ID and path relative to the storage mount point.

### Local storage

When a storage is configured as local, Nebula doesn't attempt to mount it on the node, 
but rather expects it to be mounted at the specified path. Local storages don't need to be explicitly defined in the database.

This is useful for single-node deployments, where a local directory is bind-mounted to the node's `/mnt/{site_name}_{id}` directory
as a Docker volume or a Kubernetes Persistent Volume (PV).


#### Configuration Example

In `docker-compose.yml`, you can define a local storage by adding a volume mount to the backend and worker services:

```yaml
services:
  backend:
    volumes:
      - ./nebula-storage:/mnt/nebula_01
      
  worker:
    volumes:
      - ./nebula-storage:/mnt/nebula_01
```

### Shared storages

Samba or NFS storages require a valid configuration in the settings template. 

Before you begin, you should have the following:

 - A network share that you want to use for Nebula storage
 - Access to the server where Nebula is installed
 - Basic knowledge of Docker and Docker Compose

#### Configuration Example

In the settings directory of the Nebula repository, create `storages.py` file with the following contents (modify the values as needed):

```python
from nebula.settings.models import StorageSettings

STORAGES = [
    StorageSettings(
        id=1,
        name="production",
        protocol="samba",
        path="//server/share",
        options={
            "login": "user",
            "password": "password",
        },
    )
]
```

Then run `make setup` to apply the settings.


#### Docker considerations

In order to mount shared storages in Docker, the container must be configured to run in "privileged mode". 
This setting grants the container extensive access to the host's resources, including the crucial ability to mount external volumes.

Specifically, the `docker-compose.yml` configuration for Nebula server and nodes should include `privileged: true` directive under the service definition. 

```yaml
    cap_add:
        - SYS_ADMIN
        - DAC_READ_SEARCH
    privileged: true
```
