# Thumbnails

Nebula shows a small picture of each video and image asset at the start of its title, in the [asset browser](../usage/asset-browser.md#thumbnails) and in the [rundown](../usage/linear-broadcasting.md#rundown). The server makes these thumbnails itself, the first time an asset's picture is asked for. There is nothing to set up or run.

---

## What a Thumbnail Shows

A thumbnail is a 320 pixels wide WebP picture of one frame:

* the asset's [poster frame](../usage/video-preview.md#poster-frame), when one is set;
* otherwise the frame 15 % into the asset, counted between its IN and OUT marks.

An image asset's thumbnail is the image itself, scaled down.

Only assets with a media file whose content type is **video** or **image** have thumbnails. Audio, text and virtual assets show a placeholder.

The picture is always made from the asset's own media file, never from its proxy: proxies may carry burn-ins such as a timecode or a logo.

---

## When a Thumbnail Is Made Again

A thumbnail belongs to the file and the frame it was made from. When the file changes (a new upload or import), or someone moves the poster frame or the marks, the old thumbnail is out of date and the next time the asset is shown a new one is made. Changing the metadata, such as the title, doesn't touch the thumbnail.

* **Offline files:** making a thumbnail needs the media file. While the file is offline (an unmounted storage, archived media), the last thumbnail made is shown, even if it is out of date. A new one is made once the file is back. An offline asset that never had a thumbnail shows the placeholder.
* **Files that can't be read:** when a file can't be read, the asset shows the placeholder, and the file isn't tried again until it changes (or [`thumbnails sync --retry-failed`](cli.md#thumbnails) is run).
* **Many at once:** each backend server makes one thumbnail at a time and shows the most recently requested ones first, so scrolling quickly through a large library doesn't queue thousands of them. Pictures that aren't ready yet show the placeholder for a moment.

---

## Storage and Backups

Thumbnails are kept in the database (the `thumbnails` table), not on a storage, so they can't go out of date or be left behind unnoticed: a thumbnail is deleted together with its asset. Each one takes a few kilobytes, so a library of half a million assets needs some gigabytes.

They can always be made again, so database backups can leave them out:

```bash
pg_dump --exclude-table-data=thumbnails nebula > nebula.sql
```

After a restore without them, thumbnails are made again as assets are viewed, or all at once with [`thumbnails sync`](cli.md#thumbnails).
