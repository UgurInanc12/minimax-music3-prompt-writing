---
name: minimax-music3-prompt-writing
description: "Use when writing MiniMax Music 3 prompts; caption+lyrics."
version: 1.0.0
author: Hermes Agent
license: MIT
platforms: [windows, darwin, linux]
metadata:
  hermes:
    tags: [minimax-music-3, music-generation, prompt-engineering, comfyui, audio]
    related_skills: [comfyui-desktop-windows, ltx25-prompt-writing, zimage-prompt-writing]
---

# MiniMax Music 3 Prompt Writing (official guide + official skill library)

## When to Use

- When writing prompts to generate songs/music with MiniMax Music 3 (the ComfyUI
  `audio_minimax_music_3` workflow on this machine).
- When the user provides a song idea, style, or emotion → produce the Caption + Lyrics
  pair using this skill's procedure.

Sources (reviewed 2026-08):
- Official Comfy tutorial: `https://docs.comfy.org/tutorials/audio/minimax/minimax-music-3`
- Prompting guide (HF Space): `https://huggingface.co/spaces/multimodalart/minimax-music3-prompting-guide`
- Official `music-caption-rewriter` skill: `github.com/MiniMax-AI/MiniMax-Music3/skills/music-caption-rewriter`
  (SKILL.md + genre-router + 18 family indexes + 1000 templates → copied into this
  skill's `library/` folder)

## Model Facts (the differences that shape the prompt: do NOT confuse with LTX/Z-Image)

- **TWO separate inputs; NEVER mix their roles**:
  - **Caption** = music description (style, tempo, emotional arc, vocals, arrangement,
    production). ALL musical control lives HERE. The model follows it over time (not as
    a single global tag).
  - **Lyrics** = the words to be sung + square-bracket section tags (structural
    directives).
- **Tags must stand alone AT THE START OF THEIR LINE**: text on the same line as a
  tag, such as `[verse] Morning light...`, is SILENTLY DELETED (input contract). Each
  tag on its own line.
- **Duration is an UPPER BOUND**: the model ends naturally with a stop-token once the
  lyrics run out. Short lyrics = short song; a 5-minute song needs a full song's worth
  of sections + lyrics. It cannot exceed the `max_duration` target (max 300 s / 9000
  frames, 25 fps).
- **No vocal specified → unwanted instrumental drift** (the #1 mistake). In a song
  with vocals, ALWAYS state gender + timbre explicitly; if instrumental, say
  "instrumental" and name the instrument carrying the lead melody.
- **Language**: Caption in English (unless the user asks for another language). The
  model supports multilingual vocals; lyrics can be written in the song's language.
- **Token limit**: the text prompt is capped at ~5000 tokens. Caption target is
  250–450 words.
- Architecture: 8B Global LLM (long-range structure) + 0.6B Local LLM (acoustic
  detail) + Flow Matching synthesis. Output is 32 kHz 16-bit stereo. Tags are
  generative control, not a symbolic GUARANTEE: tempo/key/instrumentation may not
  match exactly.

## Caption Structure: EXACTLY 3 sections, in this order

### 1. Global Metadata
- **Basic Attributes**: genre + subgenre, tempo; key/scale ONLY when truly wanted
  ("bpm is 122. key is G, and scale is minor. Disco / Funk Pop.").
- **Global Emotional Progression**: the emotional arc from opening to closing AS A
  STORY: where it starts, where it peaks, how it resolves.
- **Application Scenarios & Imagery**: the scene the song belongs to (night drive,
  dimly lit room, rooftop countdown). A scene anchors mood better than adjectives do.
- **Sonics & Production Profile**: mix character: stereo width, frequency balance,
  dynamics (polished/compressed vs. natural/uncompressed).

### 2. Vocal Details
- **Vocal Gender & Timbre**: always explicit: "Singer A (Female), a warm mezzo-soprano
  with a breathy low register".
- **Vocal Style**: delivery/dynamics per section: close and soft in the verse, belted
  in the chorus.
- **Harmony/Backing Vocals**: doubles, stacked harmonies, call-and-response, and where.
- **Vocal FX**: in moderation: reverb, delay throw, saturation; each one, and where.
- Instrumental: state that the track is instrumental + name the instrument carrying
  the lead melodic role.

### 3. Arrangement: a TIMELINE, NOT an equipment list
- **Instrument Lifecycle (Primary/Secondary)**: what anchors from start to finish;
  what enters, exits, transforms.
- **Groove & Foundation Progression**: the rhythmic foundation and its density
  evolution section by section (what the drums do in the verse, what drops in the
  chorus, what pulls back in the bridge).
- **Embellishments, Textures & Spatial FX**: risers, sweeps, ear candy, reverb tails;
  only where relevant.
- Write what enters/exits/changes/intensifies for every section; keep instrument
  behavior continuous so transitions stay musical.

## Writing Rules

1. **Do NOT invent fake precision**: don't state BPM/key unless truly wanted; a range
   or qualitative tempo ("driving", "unhurried") leaves the model musical room.
2. **Do not silently contradict yourself**: explicit vocal gender, mandatory
   instrument, tempo bound, exclusions → must survive across the ENTIRE caption.
3. **Priority order** (on conflict): explicit user requirements → section-tag
   directives (within their section) → strong caption implications → genre defaults.
4. **Write an energy arc**: a story of tension → release → breath → peak produces
   better structure than a static description.
5. **Do NOT put song lyrics in the caption**: the caption describes the music; the
   words go in the lyrics input.
6. Tags in the lyrics are STRUCTURAL instructions; the lyric text only carries
   emotion. Tags can also change a section's local arrangement (`[solo]`) but not the
   song's global genre.

## Lyrics Tag Bank

`[intro]` `[verse]` `[pre-chorus]` `[chorus]` `[post-chorus]` `[bridge]`
`[instrumental]` `[solo]` `[outro]`: each one on its own line.

Default skeleton: `Intro → Verse → Pre-Chorus → Chorus → Verse → Chorus → Bridge →
Final Chorus → Outro`.

## Style Families (official library router: 18 families)

When naming a genre, use the language of these families;
`emotional/epic/dark/modern/cinematic` is a MODIFIER, not a genre. Full router table +
aliases + fusion rules: `library/genre-router.md`.

| Family | Scope |
|---|---|
| general-pop-ballad | Pop, pop ballad, general emotional songs (fallback) |
| dance-pop-disco-funk | Dance-pop, nu-disco, funk-pop, groove pop |
| club-edm-house-trance | EDM, house, trance, hardstyle, festival electronic |
| electronic-synth-ambient-pop | Synth-pop, dream pop, darkwave, retrowave |
| modern-rnb-neo-soul | Contemporary/alt R&B, neo-soul, trap soul |
| hip-hop-rap | Hip-hop, rap, trap, drill, lo-fi hip-hop |
| soul-blues-gospel | Soul, blues, gospel, worship |
| jazz-swing-big-band | Vocal jazz, big band, swing, bossa nova, lounge |
| traditional-vocal-stage | Crooner, doo-wop, a cappella, musical theater, cabaret |
| pop-alternative-rock | Pop rock, alt rock, indie rock, arena, punk |
| metal-heavy-rock | Metalcore, power/symphonic metal, nu-metal, hard rock |
| contemporary-folk-acoustic | Indie folk, folk pop, singer-songwriter |
| roots-traditional-global | Traditional folk, Celtic, reggae, global fusion |
| country-americana | Country, Americana, bluegrass, rockabilly |
| cinematic-pop-ballad | Cinematic pop, orchestral pop songs |
| cinematic-orchestral-epic | Film score, trailer, epic choir, symphonic |
| east-asian-modern | Mando/C/Canto/J-pop + electronic/R&B/rock production |
| east-asian-ballad-heritage | East Asian ballads, guofeng pop |

Fusion: name both styles explicitly ("cinematic orchestral with Chinese folk
instrumentation"); "X with Y influences" → primary X, secondary Y.

## Prompt Building Procedure (idea → final output)

1. Extract a **Music Brief** from the user's idea: genre, mood/arc, tempo, vocal
   presence/gender/timbre, instruments, section structure, exclusions. Classify every
   value as `explicit / tagged / inferred / unspecified`; do NOT invent precise values.
2. Route the genre to a family (`library/genre-router.md`); if needed, pick 1–3 cards
   from the family index (`library/index-*.md`) (Foundation/Modifier/Arrangement
   roles), and open ONLY the selected templates (`library/templates/<id>.txt`). Do NOT
   COPY sentences or whole structures from templates; re-synthesize around the brief.
3. Write the caption in English: 3 sections, ~250–450 words, timeline arrangement,
   energy arc.
4. Write the lyrics: each tag on its own line; blank lines between sections; the lyric
   text carries the emotion. If the user wants a Turkish song, the lyrics may be
   Turkish, but the caption stays English.
5. Verify against the pre-flight checklist (at the end of
   `references/prompting-guide-full.md`).
6. Place into the ComfyUI widgets: caption → `caption`, lyrics → `lyrics`,
   `max_duration` (s; template 60, max 300), `seed`.

## This machine: ComfyUI parameters (audio_minimax_music_3 workflow)

- Path: `...\ComfyUI\user\default\workflows\audio_minimax_music_3.json`
  (subgraph: "Text to Music (MiniMax Music 3)").
- Loaders: UNET `minimax_music3_dit_fp16.safetensors`, CLIP
  `minimax_music3_text_encoder_pruned_int8_convrot.safetensors` (type: minimax),
  VAE `minimax_music3_dav.safetensors`: all available under the Shared models root.
- KSampler: steps=30, cfg=1.7, euler, simple, denoise=1. TextEncode widgets:
  `max_duration=60`, `cfg_scale=1.7` (node default 1.5), `top_k=50`.
- `tiled_decode` switch: ON for VRAM on long songs (1536/64 tiles); OFF for best
  quality on short songs (zero tile-seam risk).
- 3 UnloadAllModels nodes added in the workflow (model chain before KSampler, two
  VAEDecodeAudio latent chains): do not touch.
- Output: `output/audio/audio_minimax_music3.mp3` (SaveAudioAdvanced).
- The subgraph instance contains a ready Lo-fi example caption + lyrics: it can be
  read as a format example.

## Support files

- `references/prompting-guide-full.md`: full distilled content of the HF Space guide +
  the Comfy tutorial: the two-input contract, the sub-elements of the 3 sections,
  writing rules, 4 complete official example captions, pre-flight checklist.
- `library/genre-router.md`: the official router: family table, aliases, fusion rules,
  fallback logic.
- `library/index-*.md`: 18 family indexes (compact card tables: ID, style, tempo/key,
  mood arc, vocal hint, core palette, template path).
- `library/templates/*.txt`: 1000 official reference captions (full text). Open ONLY
  via the router → index → selected card chain; do not scan all of them.
