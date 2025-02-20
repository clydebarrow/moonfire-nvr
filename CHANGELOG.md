# Moonfire NVR change log

Below are some highlights in each release. For a full description of all
changes, see Git history.

Each release is tagged in Git and on the Docker repository
[`scottlamb/moonfire-nvr`](https://hub.docker.com/r/scottlamb/moonfire-nvr).

## unreleased

*   trim whitespace when detecting time zone by reading `/etc/timezone`.
*   upgrade to Retina 0.33, with better `TEARDOWN` handling
    (see [scottlamb/retina#34](https://github.com/scottlamb/retina/34)).
    This means faster recovery after an error when using UDP or when the
    camera's firmware is based on an old live555 release.

## `v0.6.6` (2021-09-23)

*   fix [#146](https://github.com/scottlamb/moonfire-nvr/issues/146): "init
    segment fetch error" when browsers have cached data from `v0.6.4` and
    before.
*   fix [#147](https://github.com/scottlamb/moonfire-nvr/issues/147): confusing
    `nvr init` failures when using very old versions of SQLite.
*   fix [#157](https://github.com/scottlamb/moonfire-nvr/issues/157): broken
    live view when using multi-view and selecting the first listed camera
    then selecting another camera for the upper left grid square.
*   support `--rtsp-transport=udp`, which may work better with cameras that
    use old versions of the live555 library, including many Reolink models.
*   send RTSP `TEARDOWN` requests on UDP or with old live555 versions; wait out
    stale sessions before reconnecting to the same camera. This may improve
    reliability with old live555 versions when using TCP also.
*   improve compatibility with cameras that send non-compliant SDP, including
    models from Geovision and Anpviz.
*   fix [#117](https://github.com/scottlamb/moonfire-nvr/issues/117): honor
    shutdown requests when out of disk space, instead of retrying forever.
*   shut down immediately on a second `SIGINT` or `SIGTERM`. The normal
    "graceful" shutdown will still be slow in some cases, eg when waiting for a
    RTSP UDP session to time out after a `TEARDOWN` failure. This allows the
    impatient to get fast results with ctrl-C when running interactively, rather
    than having to use `SIGKILL` from another terminal.

## `v0.6.5` (2021-08-13)

*   UI: improve video aspect ratio handling. Live streams formerly worked
    around a Firefox pixel aspect ratio bug by forcing all videos to 16:9, which
    dramatically distorted 9:16 camera views. Playback didn't have the same
    workaround, so anamorphic videos looked correct on Chrome but slightly
    stretched on Firefox. Now both live streams and playback are fully correct
    on all browsers.
*   UI: better error messages on live view when browser is unsupported,
    `sub` stream is unconfigured, or `sub` stream is not set to record.
*   upgrade to retina v0.1.0, which uses `SET_PARAMETERS` rather than
    `GET_PARAMETERS` as a RTSP keepalive. GW Security cameras would ignored
    the latter, causing Moonfire NVR to drop the connection every minute.

## `v0.6.4` (2021-06-28)

*   Default to a new pure-Rust RTSP library, `retina`. If you hit problems, you
    can switch back via `--rtsp-library=ffmpeg`. Please report a bug if this
    helps!
*   Correct the pixel aspect ratio of 9:16 sub streams (eg a standard 16x9
    camera rotated 90 degrees) in the same way as 16:9 sub streams.

## `v0.6.3` (2021-03-31)

*   New user interface! Besides a more modern appearance, it has better
    error handling and an experimental live view UI.
*   Compile fix for nightly rust 2021-03-14 and beyond.
*   Fix incorrect `prev_media_duration_90k` calculation. No current impact.
    This field is intended to be used in an upcoming scrub bar UI, and when
    not calculated properly there might be unexpected gaps or overlaps in
    playback.

## `v0.6.2` (2021-03-12)

*   Fix panics when a stream's PTS has extreme jumps
    ([#113](https://github.com/scottlamb/moonfire-nvr/issues/113))
*   Improve logging. Console log output is now color-coded. ffmpeg errors
    and panics are now logged in the same way as other messages.
*   Fix an error that could prevent the
    `moonfire-nvr check --delete-orphan-rows` command from actually deleting
    rows.

## `v0.6.1` (2021-02-16)

*   Improve the server's error messages on the console and in logs.
*   Switch the UI build from the `yarn` package manager to `npm`.
    This makes Moonfire NVR a bit easier to build from scratch.
*   Extend the `moonfire-nvr check` command to clean up several problems that
    can be caused by filesystem corruption.
*   Set the page size to 16 KiB on `moonfire-nvr init` and
    `moonfire-nvr upgrade`. This improves performance.
*   Fix mangled favicons
    ([#105](https://github.com/scottlamb/moonfire-nvr/issues/105))

## `v0.6.0` (2021-01-22)

This is the first tagged version and first Docker image release. I chose the
version number 0.6.0 to match the current schema version 6.
