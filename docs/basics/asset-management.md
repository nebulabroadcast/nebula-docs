# Asset management

## Assets and folders

An asset is a database entry that can receive an unlimited amount of characteristics, such as associated media files, metadata, etc.

### Folders

Assets, that share the same default characteristics are of the same *folder*, such as movie, episode, song, story, jingle, etc.

Folder selection also determines, which metadata keys are displayed in the asset editor.

### Content type

Content-type is an asset attribute defining what type of content asset represents. Following content types are available:

AUDIO
VIDEO
IMAGE
TEXT
DATABROADCASTING
INTERSTITIAL
EDUCATION
APPLICATION
GAME
PACKAGE

### Media type

Assets may not be necessarily linked to media files. Special assets such as series, have media type virtual. These assets are just database records with metadata attached. Following media types are available:

VIRTUAL
FILE
URI

### View
Views serve as the main filter of assets in the browser. Each view can be configured to contain assets from specific folders, with the given status and matching certain conditions. Additionally, each view have a default set of metadata displayed as columns in the browser table.

Within the view, you can sort assets by the displayed columns and filter further using full-text search or advanced search queries.

### Actions and jobs

Action is a predefined process such as transcoding an asset to a different format or transfer to remote storage. Each action has certain conditions, which an asset must match to start the action automatically or to allow manual triggering. When triggered, a job is created. A job is therefore defined by an asset, an action, and custom settings.

You can monitor the status of a job and its and progress both in Firefly and the web interface.
