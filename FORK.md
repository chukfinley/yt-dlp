# chukfinley/yt-dlp

Fork of [yt-dlp](https://github.com/yt-dlp/yt-dlp) that can download Instagram
photos, not only videos.

## Install

```sh
pip install --user --upgrade --force-reinstall "yt-dlp[default,curl-cffi] @ git+https://github.com/chukfinley/yt-dlp"
```

`curl-cffi` is not optional here: Instagram only answers requests that carry a
browser TLS fingerprint, which is what curl-cffi provides. Verify it is
available with `yt-dlp --list-impersonate-targets`.

Do not run `yt-dlp -U`; it is refused for pip installs anyway, but it would
otherwise pull an upstream build over this one. Re-run the command above to
update.

## What is different from upstream

Photo posts and photo carousels download as images instead of failing with
`There is no video in this post`. The image candidates of a post become
formats, so `-f`, `-F` and `--format-sort` work on them the way they do on
video formats:

```sh
yt-dlp 'https://www.instagram.com/p/<shortcode>/'          # every photo, full size
yt-dlp -f 'image-1080' 'https://www.instagram.com/p/...'   # a specific size
```

Instagram's candidate list carries neither dimensions nor a usable file
extension: the URL path always points at the original upload (frequently
`.heic`) while the CDN transcodes and resizes it according to the `stp` query
parameter. Both are therefore derived from `stp`.

For video posts nothing changes; the candidates stay thumbnails there.

## Keeping up with upstream

```sh
git remote add upstream https://github.com/yt-dlp/yt-dlp   # once
git fetch upstream && git rebase upstream/master
```

The change is confined to `yt_dlp/extractor/instagram.py`, so conflicts stay
local to that file. After a rebase, bump `__version__` in `yt_dlp/version.py`
so that pip sees the new build as an upgrade.
