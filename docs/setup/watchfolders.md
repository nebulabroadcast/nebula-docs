# Watch Folders

Using watch folders is the most convenient way to automate media file ingestion in Nebula. 

Watch folders monitor specific storage directories and automatically create corresponding database assets in Nebula as soon as new files are detected. This eliminates the need for manual asset creation and ensures that your media library is always up-to-date.

To enable watch folders, you must configure and run a single instance of the `watch` service in your setup.

---

## Minimal Configuration

In `settings/services.py`, add a service of type `watch` whose settings list the watched directories. 

For each new file, an asset is created in the folder given by `id_folder` (by default *Incoming*):

```xml
<service>
    <folder id_storage="1" path="media.dir/movies" id_folder="1"/>
    <folder id_storage="1" path="media.dir/episodes" id_folder="2"/>
</service>
```

Register it with the XML as its settings:

```python
from nebula.settings.models import ServiceSettings

WATCH = """
<service>
    <folder id_storage="1" path="media.dir/movies" id_folder="1"/>
</service>
"""

SERVICES = [
    ServiceSettings(id=2, type="watch", name="watch", host="worker", settings=WATCH),
    # ... the other services
]
```

---

## Customizing Folder Behavior

You can customize how files are scanned, matched, and processed using XML attributes within the `<folder>` tag:

| Attribute | Required | Default | Description |
| --- | --- | --- | --- |
| `id_storage` | yes | - | ID of the storage to watch. |
| `path` | yes | - | Directory to watch, relative to the storage root. |
| `id_folder` | no | `12` | Folder new assets are created in (`12` is *Incoming* in the default folders). |
| `recursive` | no | off | Watch subdirectories too. |
| `hidden` | no | off | Also pick up hidden files and directories (names starting with a dot). |

`recursive` and `hidden` take `TRUE`/`FALSE` (also `1`/`0`, `yes`/`no`; case doesn't matter).

!!! warning "Older worker images"
    nebula-worker 6.1.0 and older turn these attributes on with any value, `recursive="FALSE"` included. On those, leave the attribute out to keep it off.

!!! note "Files still being written"
    A new asset is created as soon as its file appears, even while it is still being copied (e.g. over FTP). Its status stays *Creating* until the file hasn't changed for 15 seconds; only then does the `meta` service turn it *Online*. The former `quarantine_time` attribute is no longer used and is ignored.

---

## Advanced Python Post-Processing

For complex workflows, you can write inline Python scripts inside a `<post>` block to programmatically inspect the file, extract sidecar metadata, or generate custom identifiers.

The newly created asset is exposed to the script via the `asset` variable.

### Example: Generating a Custom Identifier

This script runs after a file is matched. It uses the `shortuuid` library to set a unique key in the asset's main identifier:

```xml
<service>
  <folder id_storage="1" path="media.dir" recursive="TRUE" id_folder="1">
    <post>
<![CDATA[
import shortuuid
asset["id/main"] = shortuuid.uuid()
]]>
    </post>
  </folder>
</service>
```

!!! warning "Script Execution Sandbox"
    Post-processing scripts run with the privileges of the Nebula worker process. Ensure you only execute verified scripts to avoid security risks.
