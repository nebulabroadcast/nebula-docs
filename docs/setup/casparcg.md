# CasparCG Playout

[CasparCG Server](https://casparcg.com/) is a professional-grade, open-source software available for both Windows and Linux, dedicated to playing out graphics, audio, and video to multiple outputs.

Nebula leverages CasparCG's stability and speed for real-time, on-air playout. Additionally, Nebula's playout control service supports custom plug-ins for secondary events (e.g., character generation, routers, studio switchers, recorders).

---

## Prerequisites

Before configuring CasparCG playout, ensure:
1. **CasparCG Server** (v2.2 or later) is installed and running on a local machine or server.
2. A fast media drive is allocated for playout assets.
3. Network connectivity is open between the Nebula worker host and the CasparCG host.

---

## Controlling CasparCG from Docker

If your Nebula `play` service runs inside a Docker container, you must configure bi-directional communication between the service and the playout server:
* **Outbound control:** Nebula commands CasparCG via AMCP protocol (TCP port `5250` by default).
* **Inbound feedback:** CasparCG streams state feedback back to the Nebula worker via OSC protocol (UDP ports).

When running in Docker, you must expose the container UDP ports so CasparCG can connect and stream OSC feedback.

### 1. Configure the Playout Channel
In your site channel settings file (`settings/channels.py`), register a `PlayoutChannelSettings` entry with a unique `controller_port` and `caspar_osc_port`:

```python
from nebula.settings.models import PlayoutChannelSettings, AcceptModel

# Define folder constraints for the calendar/rundowns
scheduler_accepts = AcceptModel(folders=[1, 2])
rundown_accepts = AcceptModel(folders=[1, 3, 4, 5, 6, 7, 8, 9, 10])

channel1 = PlayoutChannelSettings(
    id=1,
    name="Channel 1",
    fps=25.0,
    plugins=[],
    solvers=[],
    day_start=(7, 0),
    scheduler_accepts=scheduler_accepts,
    rundown_accepts=rundown_accepts,
    rundown_columns=[],
    send_action=2,
    engine="casparcg",
    allow_remote=False,
    controller_host="worker",
    controller_port=42101,
    playout_storage=3,
    playout_dir="media",
    playout_container="mxf",
    config={
        "caspar_host": "192.168.1.100",  # IP address of your CasparCG server
        "caspar_port": 5250,              # Default AMCP port
        "caspar_osc_port": 6251,          # Unique OSC feedback UDP port
        "caspar_channel": 1,
        "caspar_feed_layer": 10,
        "live_source": "DECKLINK 2",
    },
)

CHANNELS = [channel1]
```

### 2. Configure the Playout Service
In your `settings/services.py`, bind the channel ID to a playout service running on a specific worker:

```python
from nebula.settings.models import ServiceSettings

PLAY1 = "<settings><id_channel>1</id_channel></settings>"

SERVICES = [
   ServiceSettings(id=11, type="play", name="play1", host="worker", settings=PLAY1),
]
```

### 3. Expose Ports in Docker Compose
In your `docker-compose.yml`, expose the UDP ports you specified in your channel config for the worker container:

```yaml
services:
  worker:
    ports:
      - "6251:6251/udp"
```

### 4. Enable OSC in `casparcg.config`
On your CasparCG Server machine, open the `casparcg.config` configuration file and add the worker IP and OSC port to the predefined clients section:

```xml
<osc>
  <predefined-clients>
    <predefined-client>
      <address>IP_ADDRESS_OF_DOCKER_HOST</address>
      <port>6251</port>
    </predefined-client>
  </predefined-clients>
</osc>
```

---

## Media Directory Setup

Our recommended layout is to host a `media.dir` folder on the playout server's high-speed media drive (e.g. `D:\media.dir` on Windows). Share this folder on the network so it is accessible as a path (like `\\playoutserver\playout`).

In the server's `casparcg.config` file, set the `<media-path>` to point directly to your local media directory.

---

## Send-to-Playout Ingestion

To copy files automatically or manually from production storage to playout storage, configure a transcode/copy Action inside `settings/actions.py`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<settings>
    <allow_if>True</allow_if>
    <task mode="ffmpeg">
        <param name="filter:a">"loudnorm=I=-23"</param>
        <param name="ar">48000</param>
        <param name="c:v">"copy"</param>
        <param name="c:a">"pcm_s16le"</param>
        <output storage="asset.get_playout_storage(1)" direct="1"><![CDATA[asset.get_playout_path(1)]]></output>
    </task>
</settings>
```

---

## Playout Storage Monitor (PSM)

The **PSM Service** monitors the active playlist schedules and automatically queues transfer/transcode jobs to the playout server. 
* **Timing:** By default, it schedules files to transfer **24 hours** before their scheduled broadcast time.
* **Scaling:** Only one PSM instance is needed per site installation. It manages playout queues for all configured channels. No additional parameters are required.

---

## Live Sources

When a rundown block is set to `LIVE`, the playout controller instructs CasparCG to play the source defined under the channel's `live_source` configuration (e.g. a DeckLink physical input, a network NDI source, or an IP stream).

!!! info "Changing Live Feeds"
    The `live_source` parameter is static and cannot be changed dynamically during a live show. If you need to switch between multiple inputs, either use an upstream hardware switcher (such as a Blackmagic ATEM) or route different video sources onto secondary CasparCG channels and command routing at the playout layer.
