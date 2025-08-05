Watch folders
=============

Using watch folders is the most convenient way to import media files into Nebula.

Watch folders allow you to automatically create assets in Nebula when new media files are added to specific directories. 

This eliminates the need for manual asset creation and ensures that your media library is always up-to-date.

To use watch folders, use a single instance of the `watch` service. 
Service configuration may contain one or more watch folders.

### Minimal configuration

The following example shows a minimal configuration for a watch folder service,
monitoring two directories. For each detected file, an asset will be created in the
default "Incoming" nebula folder (ID 12).

```xml
<service>
    <folder id_storage="1" path="media.dir/movies"/>
    <folder id_storage="2" path="media.dir/episodes"/>
</service>
```

### Customizing watch folder behavior

The behavior of each watch folder can be customized using the following attributes within the `<folder>` tag.

| Attribute           | Type     | Default | Description                                                                                                                             |
| ------------------- | -------- | ------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| id_storage          | required | \-      | Nebula storage id.                                                                                                                      |
| path                | required | \-      | The path to the directory that will be monitored.                                                                                       |
| id_folder           | optional | 12      | The ID of the Nebula folder where new assets will be created.                                                                           |
| recursive           | optional | TRUE    | If true, the watch folder will scan subdirectories.                                                                                     |
| hidden              | optional | FALSE   | If true, files and folders beginning with a . will be ignored.                                                                          |
| quarantine_time     | optional | 10      | The number of seconds a file must remain unchanged before it is processed. This prevents incomplete file transfers from being imported. |
| case_sensitive_exts | optional | FALSE   | If true, file extensions will be matched with case sensitivity.                                                                         |


### Advanced scripting

For more advanced workflows, you can execute a Python script to modify the metadata of a newly created asset. 
This is useful for tasks like loading metadata from a sidecar file or generating a custom asset identifier.

The script is defined within a `<post>` tag inside the `<folder>` tag. 
The newly created asset is available as the asset variable.

#### Example: Creating a Custom Identifier

This example uses the `shortuuid` library to generate a unique, 
short identifier for the asset's `id/main` metadata field.

```xml
<service>
  <folder id_storage="1" path="media.dir" recursive="1" id_folder="1">
    <post>
<![CDATA[
import shortuuid
asset["id/main"] = shortuuid.uuid()
]]>
    </post>
  </folder>
</service>
```
