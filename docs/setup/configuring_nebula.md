# Configuring Nebula

Nebula's configuration is managed through a **settings template**, which is a set of Python modules that define various system, storage, and service parameters. This template allows you to customize Nebula's behavior to suit your specific broadcast requirements.

All settings templates are stored in the `settings/` directory at the root of the repository or deployment path.

---

## Applying Configuration Changes

Whenever you modify settings templates, you must apply them to the system's database. To do this, execute `make setup` in the root of the Nebula repository (or use the Docker Compose equivalent):

=== "Bare Metal"
    ```bash
    make setup
    ```

=== "Docker Compose"
    ```bash
    docker compose exec backend make setup
    ```

!!! note "What does `make setup` do?"
    This command compiles the Python settings templates, serializes them, stores the configuration in the PostgreSQL database, and signals the backend and active workers to reload their configurations.

---

## Configuration Files Map

The following files are expected (but not required) to be present in your `settings/` directory:

| Filename | Purpose |
| --- | --- |
| `settings.py` | The main settings template defining system-wide configurations, channel mappings, and timezones. |
| `storages.py` | Defines storage volumes (production, playout, local, or shared Samba/NFS mounts). |
| `services.py` | Registers the system services (transcoders, monitors, playout controllers) assigned to workers. |
| `actions.py` | Defines specific actions such as automated transcoding, copying, or moving files. |
| `folders.py` | Customizes asset folders (e.g. Movies, Songs, Promos) and fields shown in metadata editors. |
| `cs.py` | Additional classification schemes (CS) for assets (useful for regulatory tracking). |
| `meta_types.py`| Defines custom metadata types (e.g. aspect ratio, language tracks) for assets. |
| `views.py` | Configures custom views and search filters in the asset browser. |
