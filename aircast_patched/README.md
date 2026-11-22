# AirCast (patched) – local test build

aircast v1.12.5-snorbik12 (add-on 5.2.3-snorbik14), built for aarch64 (static) from:

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
- local additions (snorbik8):
  - libraop: a second AirPlay sender takes the speaker over (newest wins, like a
    real AirPlay receiver). Before, only one RTSP connection was served and the
    second sender waited until it gave up. The current session is ended only when
    the new sender sends ANNOUNCE, so a probe does not interrupt playback.
  - diagnostics in the log: `sender sent N ms of silence` (the iPhone kept sending
    packets but they were silent) and `receiver is not reading` / `receiver reading
    again after N ms` (the speaker stopped pulling the HTTP stream).
- local additions (snorbik9): finer diagnostics – `sender sent N ms of very quiet
  audio (peak P)` for stretches below -40 dBFS and an `audio level last 10 s` line,
  to tell a quiet recording from a speaker that mutes loud audio.
- local additions (snorbik10): when the speaker stops on its own, the log says why
  (`Cast stopped by other remote, reason FINISHED|CANCELLED|INTERRUPTED|ERROR`)
  with the media status the speaker sent.
- add-on option `codec` (flac|wav|mp3): audio format sent to the speakers, passed as `-c`.
- snorbik11/12: option `http_mode` – `default` (HTTP/1.0, no length), `chunked` (HTTP/1.1 chunked) or `length` (fake 2 GB Content-Length), passed as `-H`.
- snorbik12/13: option `receiver` – force the Cast receiver app id (e.g. `46C1A819`), passed as `-R`; empty = automatic (CC1AD845 for audio devices).
- add-on 5.2.3-snorbik14: `codec` also offers `mp3:320`, `aac` and `aac:256`.
