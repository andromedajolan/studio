## Lyrics timing (`P.lyrics`)

Added by the Lyrics timing panel (Project ▸ Lyrics timing…). The key does not exist in a project until the panel is
used, so older projects are unchanged and load exactly as before. It is stored inside the normal
`projects/<project id>/project.json` (so it is covered by autosave and the recovery snapshots).

```json
"lyrics": {
  "v": 1,
  "title": "Freaks and Outcasts",
  "track": "3",
  "source": { "kind": "mix", "id": null },
  "offset": 0,
  "songRef": { "app": "stilus", "id": "s1", "title": "Freaks and Outcasts" },
  "lines": [
    { "type": "heading", "text": "Verse 1", "start": null, "end": null },
    { "type": "cue", "text": "We were never meant to blend in", "start": 1.55, "end": 2.45 }
  ]
}
```

| Field | Meaning |
|---|---|
| `v` | Format version of this block (currently `1`). Readers should ignore unknown fields. |
| `title`, `track` | Used for the export file name: `NN - Title.srt` (`NN` = `track` padded to two digits; omitted if blank). |
| `source` | What the times are measured against: `kind` is `mix` (all audible tracks), `track` or `clip`; `id` is the track or clip id (`null` for `mix`). Time 0 is the start of that audio (for a clip: the clip start). |
| `offset` | Seconds added to every cue when exporting. Negative = earlier. Not baked into `start`/`end` until “Apply to cues”. |
| `songRef` | Optional link to the song in Stilus (`app`, `id`, `title`) when the lyrics came from a Stilus backup. |
| `lines[]` | In reading order. `type` is `cue` (timed) or `heading` (a section name; never timed, never exported). |
| `lines[].start`, `.end` | Seconds from time 0, millisecond precision, or `null` if not timed yet. |

Export rules: only `cue` lines with a `start` and non-empty text are written; a cue with no `end` ends 1 ms before the
next cue starts (last cue: 3 s, capped at the end of the audio); cues never overlap; times are `max(0, time + offset)`.
`.srt` is UTF-8 without BOM, CRLF line endings, `HH:MM:SS,mmm`, numbered from 1. `.lrc` is `[mm:ss.xx]text` per cue
with a `[ti:]` tag.

Browser storage: the Start / End key bindings are saved per browser (not per project) in `localStorage` key
`daw.lyrics.keys` as `{"start":"ControlLeft","end":"AltLeft"}` (values are `KeyboardEvent.code` names).
