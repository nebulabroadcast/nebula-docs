# Nebula interface

### Browser

An Asset browser is a catalog, which provides (based on the user rights) access to all assets within the system.

Clicking an asset in the browser opens it in the detail tab, 
while right-click brings a context menu with options to trash/un-trash,
 archive/unarchive the asset(s), force technical metadata refresh and to create a new job (send to).

Browser module also allows supported assets (e.g. assets with associated media files)
to be dragged and dropped outside the Application - for example to an NLE, image editor or a local folder.

### Asset detail

Provides viewer/editor of metadata associated with the asset, as well as a low-res video player.

Metadata editor allows users to enter modify or view
a collection of related metadata of the selected asset.

Fields available in the editor are based on the folder the asset belongs to. 
When the folder is changed using the select widget on the top of the editor, form changes accordingly.

When a new "blank" asset is created using *New asset* menu option, its status is offline until the media file 
is associated with the database entry (for example using a filename matching asset's ID). 

When offline, asset duration can be changed manually to the expected value and the asset can be scheduled for playback. 
Once it's turned online, Nebula updates the duration to match the actual value.

### Jobs

Lists jobs including progress and status and allows their aborting and restarting.

### Scheduler

Plan and schedule events in EPG/Calendar style.

### Rundown

This module displays all  events and items of a linear playout channel for a chosen day
Jand provides an interface for item scheduling) and to control the playout server.

