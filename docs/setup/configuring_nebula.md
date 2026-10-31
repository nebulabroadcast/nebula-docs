# Configuring Nebula

Nebula's configuration is managed through a **settings template**, which is a set of Python modules that define various system, storage, and service parameters. This template allows you to customize Nebula's behavior to suit your specific broadcast requirements.

Settings templates are Python files in the `settings/` directory of your deployment, mounted into the server container at `/settings`.

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

Setup loads every `.py` file in `/settings` and looks for these variables. The file names are only a convention; one variable per file of the same name keeps things easy to find:

| Variable | Conventional file | Purpose |
| --- | --- | --- |
| `SETTINGS` | `settings.py` | System settings (site name, proxy and upload storages, SSO providers, SMTP...) |
| `CHANNELS` | `channels.py` | Playout channels |
| `STORAGES` | `storages.py` | Storage volumes (local or Samba shares), see [Storages](storages.md) |
| `SERVICES` | `services.py` | Services and the worker (hostname) each one runs on |
| `ACTIONS` | `actions.py` | Actions such as transcoding or sending to playout |
| `FOLDERS` | `folders.py` | Asset folders and the fields shown in the metadata editor |
| `META_TYPES` | `meta_types.py` | Metadata types (keys, classes, defaults) |
| `CS` | `cs.py` | Additional classification schemes |
| `VIEWS` | `views.py` | Views offered in the asset browser |

!!! warning "Lists replace the defaults"
    `SETTINGS` and `META_TYPES` are dictionaries merged into the defaults, so they only need the keys you change or add. Every other variable is a list that **replaces** the built-in defaults as a whole: a `FOLDERS` list with one folder leaves the site with exactly one folder. Variables you don't define keep their defaults.
