# Nebula Concepts

## Architecture

### Site

A Nebula instance. A site may be a broadcaster or broadcast network with one or more channels sharing the configuration,
subset of assets, or users. Each site has its own database, configuration template, and users.

One site can be run on multiple servers to spread the load.

The heart of each site is a PostgreSQL, Redis and a shared storage (or multiple storages).
Nebula server and workers all access these resources.

### Server

Nebula server provides API and the web interface for Nebula. Server needs to have access to Postgres and Redis, as well as the storage,
that is used to store low-res media and uploads.

### Worker

Nebula worker is a program that starts Nebula services, which are small programs designed to perform specific tasks.

Some services extract metadata from media files, others transcode assets to different formats, control playout servers, and more.
These services can be distributed across multiple machines within the network and managed through the Nebula web interface.
Nebula is highly scalable, allowing each site to use workers to distribute media processing tasks across the network.

Additionally, it is possible to develop custom, site-specific services for very specialized tasks.


## Asset management

### Asset

An asset is a database entry that can receive an unlimited amount of characteristics, such as associated media files, metadata, etc.

### Folder

Assets, that share the same default characteristics are of the same *folder*, such as movie, episode, song, story, jingle, etc.

Folder selection also determines, which metadata keys are displayed in the asset editor.

By default, nebula comes with the following folders:

- **Movie** - A feature-length film, or stand-alone production, that is not part of a series.
- **Episode** - A single episode of a series.
- **Story** - A short-form production, such as a news story. Multiple stories can be used within a single show or program.
- **Song** - A single song or music video.
- **Fill** - A short-form production used to fill gaps in a schedule, such as a promo.
- **Trailer** - A short-form production used to promote a movie, show, or program.
- **Jingle** - A short graphic or audio production used to promote a show, program, or channel.
- **Commercial** - A single advertisement or commercial. 
- **Teleshopping** - Long-form advertisement or infomercial, typically 15 minutes or longer.
- **Dataset** - A collection of data, such as a CSV or XML file, that can be used for various purposes, such as data analysis or reporting.
- **Incoming** - A temporary folder for assets that are being uploaded or imported into the system.
- **Serie** - A collection of episodes that are part of a larger production, such as a TV series or web series.

These folders can be customized or extended to fit the specific needs of a site, but certain integrations and plugins may rely on the default folders.

### Content type

Content-type is an asset attribute defining what type of content asset represents. Following content types are available:

- AUDIO
- VIDEO
- IMAGE
- TEXT
- DATABROADCASTING
- INTERSTITIAL
- EDUCATION
- APPLICATION
- GAME
- PACKAGE

### Media type

Assets may not be necessarily linked to media files. Special assets such as series, have media type virtual. 
These assets are just database records with metadata attached. Following media types are available:

- VIRTUAL
- FILE
- URI

### View

Views serve as the main filter of assets in the browser. Each view can be configured to contain assets from specific folders, with the given status and matching certain conditions. Additionally, each view have a default set of metadata displayed as columns in the browser table.

Within the view, you can sort assets by the displayed columns and filter further using full-text search or advanced search queries.

### Actions and jobs

Action is a predefined process such as transcoding an asset to a different format or transfer to remote storage. Each action has certain conditions, which an asset must match to start the action automatically or to allow manual triggering. When triggered, a job is created. A job is therefore defined by an asset, an action, and custom settings.

You can monitor the status of a job and its and progress both in Firefly and the web interface.

## Linear Broadcasting

### Channel

Typically a playout channel. Each site can have several channels. 
In some cases such as a VOD application, Nebula may be configured without any channels.

### Event (sometimes referred as "block")

A calendar entry. In the case of linear playout scheduling, an event is an "EPG" program block. 
An event belongs to a channel and has its start time and other metadata such as title and description.

Users can view and edit events using the scheduler view in the Firefly application.

### Item

Generally, an item is an instance of an asset scheduled in an event playlist. 
Several special types of items (without relation to any asset) may be inserted to a playlist as well, such as live events, placeholders, etc.

### Rundown

List of all events and items for one day. Rundown view shows all items scheduled for a channel on a given day. 
It is used to create and edit the schedule, as well as to monitor the playout.

### As-Run

An as-run log is the actual and accurate list of items which have been played out. 
Nebula provides a mechanism to filter and export the log to various formats for clients, collective rights management organizations, etc.
