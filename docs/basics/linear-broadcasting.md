# Scheduling and playout

## Channel

Typically a playout channel. Each site can have several channels. In some cases such as a VOD application, Nebula may be configured without any channels.

## Event

A calendar entry. In the case of linear playout scheduling, an event is an “EPG” program block. An event belongs to a channel and has its start time and other metadata such as title and description.

Users can view and edit events using the scheduler view in the Firefly application.

## Item

Generally, an item is an instance of an asset scheduled in an event playlist. Several special types of items (without relation to any asset) may be inserted to a playlist as well, such as live events, placeholders, etc.

## Rundown

List of all events and items for one day. A Firefly rundown module displays complete daily playlist in a single view.

## As-Run

An as-run log is the actual and accurate list of items which have been played out. Nebula provides a mechanism to filter and export the log to various formats for clients, collective rights management organizations, etc.
