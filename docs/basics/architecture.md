# Architecture

## Site

A Nebula instance. A site may be a broadcaster or broadcast network with one or more channels sharing the configuration,
subset of assets, or users. Each site has its own database, configuration template, and users.

One site can be run on multiple servers to spread the load.

The heart of each site is a PostgreSQL, Redis and a shared storage (or multiple storages).
Nebula server and workers all access these resources.

## Server

Nebula server provides API and the web interface for Nebula. Server needs to have access to Postgres and Redis, as well as the storage,
that is used to store low-res media and uploads.

## Worker

Nebula worker is a program that starts Nebula services, which are small programs designed to perform specific tasks.

Some services extract metadata from media files, others transcode assets to different formats, control playout servers, and more.
These services can be distributed across multiple machines within the network and managed through the Nebula web interface.
Nebula is highly scalable, allowing each site to use workers to distribute media processing tasks across the network.

Additionally, it is possible to develop custom, site-specific services for very specialized tasks.
