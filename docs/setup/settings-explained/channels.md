channels.py

This will be a deeper explanation of the `channels.py` file and some options available.


```python
from nebula.settings.models import PlayoutChannelSettings, AcceptModel

# Define folder constraints for the calendar/rundowns
scheduler_accepts = AcceptModel(folders=[1, 2])
rundown_accepts = AcceptModel(folders=[1, 3, 4, 5, 6, 7, 8, 9, 10])

channel1 = PlayoutChannelSettings(
    id=1,                                 # ID of the channel
    name="Channel 1",                     # Visable name from nebula GUI for the channel
    fps=25.0,                             # Framerate used for 
    plugins=[],
    solvers=[],
    day_start=(7, 0),                     # Means that the broadcast day (start of the rundown) starts. In this example the 7 means 07:00 and the 0 is a offset of zero. 
    scheduler_accepts=scheduler_accepts,  # schedular_accepts are features implemented into the EOL firefly client but not yet added to the web GUI. This also defines the value to the line at the top so you don't have to edit every channels value.
    rundown_accepts=rundown_accepts,      # rundown_accepts are features implemented into the EOL firefly client but not yet added to the web GUI. This also defines the value to the line at the top so you don't have to edit every channels value.
    rundown_columns=[],
    send_action=2,                        # Sets what action to trigger when running from actions.py
    engine="casparcg",                    # Defines the playout engine (conti or casparcg)
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