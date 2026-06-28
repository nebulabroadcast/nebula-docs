# Nebula Storages

In a professional broadcast workflow, dividing high-capacity raw media from low-latency playout media is essential. Nebula separates these into distinct storage roles.

---

## Storage Types

### 1. Production Storage
* **Characteristics:** High capacity for raw footage, high-res masters, and intermediate assets. It demands good concurrent I/O performance to handle ingest and transcoding but can tolerate higher access latency than playout.
* **Implementation:** Network Attached Storage (NAS) or Storage Area Network (SAN) solutions are commonly employed, supporting standard protocols like NFS or SMB/CIFS. A hybrid array (SSDs for current projects, spinning disks for archival) provides the best balance of cost and performance.

### 2. Playout Storage
* **Characteristics:** Extremely high, consistent read performance with minimal latency. It only needs to hold media files for the active scheduling window (typically a few days or weeks).
* **Implementation:** Direct Attached Storage (DAS) using high-speed SSDs (RAID 10) directly connected to the CasparCG playout server is the preferred standard.

### Comparison Table

| Feature         | Production Storage                                | Playout Storage                                                   |
| --------------- | ------------------------------------------------- | ----------------------------------------------------------------- |
| **Purpose**     | File ingest, editing, archiving, high-res masters | Real-time playout, optimized delivery                             |
| **Capacity**    | Large (terabytes to petabytes)                    | Sufficient for active playout schedule (days/weeks)               |
| **Performance** | High I/O for ingest/transcoding, moderate latency | High read speeds, very low latency, consistent throughput         |
| **Access**      | Random read/write, large file transfers           | Sequential read, concurrent access by playout engines             |
| **Redundancy**  | High (RAID 6, distributed storage, backups)       | High (RAID 10, mirrored, fast failover)                           |
| **Connectivity**| SAN, NAS (NFS, SMB), Cloud Storage                | Direct Attached Storage (DAS), high-speed SAN/NAS                 |
| **File Formats**| Original camera formats, mezzanine codecs         | Playout-specific codecs (e.g., ProRes, DNxHD, H.264)              |

---

## Configuration

Storages in Nebula are referenced by their ID (e.g. `1`, `2`). Nebula expects storages to be mounted at `/mnt/{site_name}_{id}` on each system node (e.g. `/mnt/nebula_01`). 

When configured via settings, Nebula attempts to mount and maintain the mount point automatically. All media assets are referenced inside the database by their storage ID and relative file path.

---

## Storage Scenarios

### Local Storage
If you have a single-node deployment, you don't need to configure mounting dynamically in the database. Instead, mark the storage as local and bind-mount a host folder directly into the container.

In your `docker-compose.yml`, mount the local directory to both backend and worker services:
```yaml
services:
  backend:
    volumes:
      - ./nebula-storage:/mnt/nebula_01
      
  worker:
    volumes:
      - ./nebula-storage:/mnt/nebula_01
```

### Shared Network Storage (Samba / NFS)
To use network shares, define them in the `settings/storages.py` file:

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

After modifying the file, run `make setup` (or docker equivalent) to save this storage configuration to the database.

!!! danger "Docker Permissions for Network Mounts"
    Mounting network drives directly inside Docker containers requires administrative capabilities. The containers running the mounting tasks (such as `nebula-worker`) must be configured with `privileged: true` or the `SYS_ADMIN` capability.
    
    Add this to your `docker-compose.yml` service configuration:
    ```yaml
    privileged: true
    cap_add:
      - SYS_ADMIN
      - DAC_READ_SEARCH
    ```
