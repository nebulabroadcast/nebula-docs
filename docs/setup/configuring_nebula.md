# Configuring Nebula

Nebula's configuration is managed through a **settings template**, which is a set of Python modules that define various system, storage, and service parameters. This template allows you to customize Nebula's behavior to suit your specific broadcast requirements.

All settings templates are stored in the `settings/` directory at the root of the repository or deployment path.

---

## Applying Configuration Changes

Whenever you modify settings templates, you must apply them to the system's database. With the [nebula-tutorial](https://github.com/nebulabroadcast/nebula-tutorial) setup, run `make setup` in its root directory, or call the commands it wraps directly:

=== "make"
    ```bash
    make setup
    ```

=== "Docker Compose"
    ```bash
    docker compose exec server python -m setup
    docker compose exec server ./manage reload
    ```

!!! note "What does `make setup` do?"
    `python -m setup` loads the Python settings templates, validates them and stores the configuration in the PostgreSQL database. `./manage reload` then reloads the server so it picks up the new settings. Workers read their settings when they start: restart them (`make restart`) after changing channels, services or actions.

---

## Configuration Files Map

The following files are expected (but not required) to be present in your `settings/` directory:

| Filename | Purpose |
| --- | --- |
| `settings.py` | The main settings template defining system-wide configurations |
| `channels.py` | Playout channels configuration |
| `storages.py` | Defines storage volumes (production, playout, local, or shared Samba/NFS mounts). |
| `services.py` | Registers the system services (transcoders, monitors, playout controllers) assigned to workers. |
| `actions.py` | Defines specific actions such as automated transcoding, copying, or moving files. |
| `folders.py` | Customizes asset folders (e.g. Movies, Songs, Promos) and fields shown in metadata editors. |
| `cs.py` | Additional classification schemes (CS) for assets (useful for regulatory tracking). |
| `meta_types.py`| Defines custom metadata types (e.g. aspect ratio, language tracks) for assets. |
| `views.py` | Configures custom views and search filters in the asset browser. |
