# Explaning `channels.py`

This will be a deeper explanation of the `channels.py` settings file and some options available.

---

```python
from nebula.settings.models import PlayoutChannelSettings, AcceptModel

# Define folder constraints for the calendar/rundowns
scheduler_accepts = AcceptModel(folders=[1, 2])
rundown_accepts = AcceptModel(folders=[1, 3, 4, 5, 6, 7, 8, 9, 10])

channel1 = PlayoutChannelSettings(
    id=1,                                 # ID of the channel
    name="Channel 1",                     # Visable name from nebula GUI for the channel
    fps=25.0,                             # Framerate is used for clock information, SEEK and LENGTH parameters in nebula
    plugins=[],                           # Specifies name of plugins to use on this channel
    solvers=[],
    day_start=(7, 0),                     # Sets when the broadcast day (start of the rundown) starts. The first number sets the hour, and the second number sets the minutes. In this example the 7 means 07:00 am and the 0 means it starts at the top of the hour. 
    scheduler_accepts=scheduler_accepts,  # schedular_accepts are features implemented into the EOL firefly client but not yet added to the web GUI. This also defines the value to the line at the top so you don't have to edit every channels value.
    rundown_accepts=rundown_accepts,      # rundown_accepts are features implemented into the EOL firefly client but not yet added to the web GUI. This also defines the value to the line at the top so you don't have to edit every channels value.
    rundown_columns=[],
    send_action=2,                        # Triggers the action that is responsible for transfer to the playout storage. The psm (playout storage monitor) service will be started when the asset is not found on the playout storage 24 hours before the assets scheduled run.
    engine="casparcg",                    # Defines the playout engine (conti or casparcg or liquidsoap)
    allow_remote=False,
    controller_host="worker",             # Defines th ename of the nebula worker instance to control the playout of this channel.
    controller_port=42101,                # Unique port per channel defining what nebula worker communicates to.
    playout_storage=3,                    # ID of the storage where assets will be played from. More information in storages.md
    playout_dir="media",                  # Sets the directory within the playout storage for the media to be played from.
    playout_container="mxf",              # Video format for FFMPEG to encode the asset to when being sent to playout storage.
    config={
        "caspar_host": "192.168.1.100",   # IP address of your CasparCG server
        "caspar_port": 5250,              # Default AMCP port same for all channels on each CasparCG server
        "caspar_osc_port": 6251,          # Unique OSC feedback UDP port
        "caspar_channel": 1,              # Defines CasparCG channel
        "caspar_feed_layer": 10,          # Defines CasparCG layer on selected channel
        "live_source": "DECKLINK 2",      # Defines what will be the live source
    },
)

CHANNELS = [channel1]                     # Add all channels configured before the "PlayoutChannelSettings" at the top with a comma separating each one.
```