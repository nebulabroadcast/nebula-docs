# Asset Validators

A validator is a site-specific Python plugin that runs every time an object is created or updated through the API, for example when a user saves an asset in the web interface. It is the place for your own rules: required fields, values derived from other fields, and where an asset's media file should live.

---

## Why You Need an Asset Validator

When an asset is created by a [watch folder](watchfolders.md), its file already exists, so Nebula knows the storage and path. When a user creates an asset **manually** (an *Offline* placeholder waiting for its media), Nebula has no file to take them from.

**The asset validator is responsible for setting the asset's storage (`id_storage`) and path (`path`) when the asset is first saved.** They tell Nebula where the media file will be expected:

* [media upload](import.md) stores the uploaded file there,
* the [import service](import.md#the-import-service) writes the transcoded file there,
* the `meta` service turns the asset *Online* when a file appears there (for example copied by hand).

An asset without a storage and path can't receive media: uploads and imports for it fail.

!!! warning "Don't overwrite an existing path"
    Only set the path if the asset doesn't have one yet. Assets created by watch folders already point to their file, and the path of an asset with media must not change on a later save.

---

## Where Validators Live

Validators are Python files in the `validator/` subdirectory of the backend's plugin directory (`/plugins` in the backend container, configurable with the `NEBULA_PLUGIN_DIR` environment variable). There is one file per object type:

```text
plugins/
└── validator/
    ├── asset.py
    ├── item.py
    ├── bin.py
    ├── event.py
    └── user.py
```

All files are optional. An object type without a validator is saved as sent.

Validators are loaded once, on first use. **Restart the backend after changing a validator.**

---

## Writing a Validator

A validator file defines an async `validate` function:

```python
async def validate(
    asset: nebula.Asset,  # The existing asset, or a new empty one
    meta: dict[str, Any],  # Metadata sent in the request
    connection: nebula.DB,  # Database connection (kept for compatibility)
    user: nebula.User,  # User who made the request
) -> None:
    ...
```

Things to keep in mind:

* **Apply the request yourself.** With a validator in place, Nebula doesn't copy the request metadata to the object; call `asset.update(meta)` (usually first) and then adjust what you need.
* **Don't save at the end.** Nebula saves the object after `validate` returns. Call `await asset.save()` inside the validator only when you need the ID of a new asset, e.g. to build a path from it.
* **Reject with an exception.** The validator runs in a database transaction. Raising `ValidationException` aborts the whole operation (nothing is saved, including an asset you saved inside the validator) and shows the message to the user.
* **Validators run for API requests only.** Changes made by the worker, such as assets created by watch folders or metadata updated by the `meta` service, don't go through validators.

---

## Example

This validator requires a title, gives new assets a main identifier, and assigns the storage and path where the media file is expected:

```python
from typing import Any

import nebula
from nebula.enum import ContentType, MediaType, ObjectStatus
from nebula.exceptions import ValidationException


async def validate(
    asset: nebula.Asset,
    meta: dict[str, Any],
    connection: nebula.DB,
    user: nebula.User,
) -> None:
    asset.update(meta)

    if not asset["title"]:
        raise ValidationException("Title is required")

    # A new asset needs an ID before we can build its path from it.
    # This happens inside the transaction, so a later exception
    # still rolls it back.
    if not asset.id:
        await asset.save()

    if not asset["id/main"]:
        asset["id/main"] = f"{asset.id:06x}"

    asset["media_type"] = MediaType.FILE
    asset["content_type"] = ContentType.VIDEO

    # Where the media file will be expected. Never change the path
    # of an asset that already has one (e.g. from a watch folder).
    if not asset["path"]:
        asset["id_storage"] = 1
        asset["path"] = f"media.dir/{asset['id/main']}.mxf"
        asset["status"] = ObjectStatus.OFFLINE
```

The file extension of the path should match the format your media arrives in. With the import service, use the container of the import profile (`.mxf` for the XDCAM profiles). With direct upload, the extension is updated to match the uploaded file.

!!! tip "Organising the storage"
    The path is built in Python, so it can use any asset metadata, for example a subdirectory per folder. Keep the paths unique, ideally based on an identifier that never changes.
