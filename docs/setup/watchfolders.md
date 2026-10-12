# Watch Folders

Using watch folders is the most convenient way to automate media file ingestion in Nebula. 

Watch folders monitor specific storage directories and automatically create corresponding database assets in Nebula as soon as new files are detected. This eliminates the need for manual asset creation and ensures that your media library is always up-to-date.

To enable watch folders, you must configure and run a single instance of the `watch` service in your setup.

---

## Minimal Configuration

In your service settings template (`settings/services.py`), define a watch service configuration with monitored storage paths. 

For each detected file, a database asset will be created in the default **Incoming** folder (usually ID `12`):

```xml
<service>
    <folder id_storage="1" path="media.dir/movies"/>
    <folder id_storage="2" path="media.dir/episodes"/>
</service>
```

---

## Customizing Folder Behavior

You can customize how files are scanned, matched, and processed using XML attributes within the `<folder>` tag:

| Attribute | Type | Default | Description |
| --- | --- | --- | --- |
| `id_storage` | Required | - | The target storage ID to write metadata or files to. |
| `path` | Required | - | The subdirectory path within that storage to monitor. |
| `id_folder` | Optional | `12` | The Nebula folder ID (e.g. Movie, Episode, Song) where new assets will be created. |
| `recursive` | Optional | `TRUE` | If `TRUE`, scans and watches subdirectories recursively. |
| `hidden` | Optional | `FALSE` | If `TRUE`, files and folders starting with a dot (`.`) are ignored. |
| `case_sensitive_exts`| Optional | `FALSE`| Enables case-sensitive matching for monitored file extensions. |

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
