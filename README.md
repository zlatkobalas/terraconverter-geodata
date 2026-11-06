# Android address index

This folder holds `Address.mobile.sqlite` (+ `.gz`) — the Android-only
address index consumed by `core/geocoding/dgu_mobile.py` and
`AddressIndexManager.kt`, Android's address search/reverse-geocoding
feature.

## Why this is separate from `external_data/dgu_addresses/`

That folder is the **desktop** DGU index and its raw GML source
(`core/geocoding/dgu.py`, FTS5-based). This one is a derived, Android-only
reshape of that index — no FTS5 (Chaquopy's bundled SQLite build doesn't
have it compiled in), plain B-tree prefix index instead, smaller. Nothing
on the desktop/CLI side reads this file.

## Why it is not in git

Same reason as `dgu_addresses/Address.sqlite`: too large for git, and
much too large to bundle in the Android APK besides. It also isn't
something a desktop user ever needs to set up — only relevant when
building/testing the Android app.

## Setting it up

1. Make sure the desktop DGU index is built
   (`external_data/dgu_addresses/Address.sqlite` — see that folder's
   README).
2. Run `uv run python scripts/build_android_address_index.py`. This
   writes `Address.mobile.sqlite` and `Address.mobile.sqlite.gz` here.

In the real Android app, this file isn't bundled locally at all — it's
downloaded on-device by `AddressIndexManager.kt` from GitHub Releases
(https://github.com/zlatkobalas/terraconverter-geodata). Building it here
is only needed to produce the file you then upload there, or to test the
Android address feature against a local copy without going through a
download.

## Uploading a new version

See `github.com/zlatkobalas/terraconverter-geodata`'s own README for the
release process (tag = data version, attach `Address.mobile.sqlite.gz` +
a `.sha256` checksum sidecar generated with
`sha256sum Address.mobile.sqlite.gz > Address.mobile.sqlite.gz.sha256`).
