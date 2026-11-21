# AirCast (patched) – local test build

aircast v1.12.5-snorbik7, built for aarch64 (static) from:

- philippe44/AirConnect master af345de (1.12.5, not released yet)
  - volume timestamp wrap (#605)
  - PLAY with metadata is non-blocking (replaces #606)
  - libraop c14088d: EISCONN fix (#57) and Audio-Latency above 2267 ms (#58)
  - crosstools 306aa41 (#603 ping_host fix)
- local additions (snorbik5):
  - `if (MetaData && !(Ctx->caps & 0x01)) return;` at the start of CastPlay (suggested by the author in #606)
  - `p->Volume = Volume;` after a device-side volume change (#608)
- local additions (snorbik6):
  - yield: when another app (e.g. Spotify Cast) takes the speaker over while AirPlay
    is still streaming, aircast no longer grabs it back on the next AirPlay play.
    It waits until the speaker is free again (receiver status polled every 30 s)
    (checked after a 5 s grace) or 10 minutes have passed. While yielded, AirPlay volume and metadata are
    ignored and speaker volume changes are not sent to the iPhone.
  - libraop: warning `no remote control known ..., dropping 'stop'` instead of
    silently dropping play/pause/stop when the iPhone's remote was not found.
- local additions (snorbik7):
  - on takeover, no `stop` is sent to the AirPlay sender any more: iOS delivers it
    to the app playing on the phone, so Spotify moving from AirPlay to Cast arrived
    paused. Yield alone prevents the fight.
