# MiniMax Music 3: Prompting Guide (fully distilled)

Sources (read 2026-08-15):
- Comfy tutorial: https://docs.comfy.org/tutorials/audio/minimax/minimax-music-3
- Prompting guide Space: https://huggingface.co/spaces/multimodalart/minimax-music3-prompting-guide
  (static space; content extracted from `index.html`, a republication of the official
  guide bearing the note "Based on the official MiniMax Music 3 prompting guide and
  caption library")
- Official repo README: https://huggingface.co/MiniMaxAI/MiniMax-Music3

## 1. The two inputs: keep their roles separate

Every generation is driven by EXACTLY two texts; do not mix their roles.

**Lyrics**: the words to be sung + square-bracket section tags. Tags are EXECUTABLE
directives; the lyric text only carries the words and the emotional weight.

**Caption (structured)**: the music description: genre, tempo, emotional arc, vocal
performance, section-by-section arrangement. All musical control lives here; the model
follows it OVER TIME, not as a single global tag.

## 2. Lyrics and section tags

Tags: `[intro] [verse] [pre-chorus] [chorus] [post-chorus] [bridge]
[instrumental] [solo] [outro]`

RULES:
- Each tag ON ITS OWN LINE. Text on the same line as a tag is SILENTLY DELETED by the
  model's input contract: `[verse] Morning light...` → the words are lost.
- A tag can change the LOCAL arrangement of its own section (e.g. `[solo]`) but does
  not change the song's global genre.
- Structure is guidance, NOT a guarantee: tags steer the layout, and the model composes
  freely within it.
- Duration is an UPPER BOUND: the model ends naturally with a stop-token when the
  lyrics run out. Short lyrics = short song; a full 5-minute song needs a full song's
  worth of sections/words.

Example:
```
[intro]

[verse]
Morning light filtering through the pine
Every quiet street is yours and mine

[pre-chorus]
Hold your breath, the sky is opening

[chorus]
We are made of sound and time

[outro]
```

## 3. The structured caption: EXACTLY 3 sections, in this order, ~250–450 words

### Global Metadata
1. **Basic Attributes**: genre + subgenre, tempo; key/scale ONLY when truly wanted:
   `"bpm is 122. key is G, and scale is minor. Disco / Funk Pop."`
2. **Global Emotional Progression**: the song's emotional arc AS A STORY: where it
   starts, where it peaks, how it resolves.
3. **Application Scenarios & Imagery**: the scene the song belongs to (night drive,
   dimly lit room, rooftop countdown). A scene anchors mood better than adjectives.
4. **Sonics & Production Profile**: mix character: stereo width, frequency balance,
   dynamics (polished/compressed ↔ natural/uncompressed).

### Vocal Details
1. **Vocal Gender & Timbre**: always explicit (`"Singer A (Female), a warm mezzo-soprano
   with a breathy low register"`). Omitting the vocal is the #1 cause of unwanted
   instrumental drift.
2. **Vocal Style**: delivery/dynamics per section (soft close-mic in the verse, belted
   in the chorus).
3. **Harmony/Backing Vocals**: doubles, stacked harmonies, call-and-response + where.
4. **Vocal FX**: in moderation: reverb, delay throw, saturation; each one, and where.
5. For instrumental music: explicitly state that it is instrumental + name the
   instrument carrying the lead melody.

### Arrangement: a timeline, not an equipment list
1. **Instrument Lifecycle (Primary/Secondary)**: what anchors from start to finish;
   what enters, exits, transforms.
2. **Groove & Foundation Progression**: the rhythmic foundation + density evolution
   section by section: what the drums do in the verse, what drops in the chorus, what
   pulls back in the bridge.
3. **Embellishments, Textures & Spatial FX**: risers, sweeps, ear candy, reverb tails;
   only where relevant.
Write what enters/exits/changes/intensifies in every section; keep instrument behavior
continuous.

## 4. Writing rules

- **Do not invent fake precision**: don't state BPM/key unless truly wanted; a range or
  qualitative tempo ("driving", "unhurried") leaves musical room.
- **Do not silently contradict yourself**: explicit vocal gender, mandatory instrument,
  tempo bound, exclusions must survive across the entire caption.
- **Conflict priority**: explicit requirements > section-tag directives (within their
  section) > caption implications > genre defaults.
- **Write an energy arc**: a story of tension → release → breath → peak beats a static
  description.
- **Do not put song lyrics in the caption**: the caption describes the music; the words
  live in the lyrics input.

## 5. Style families (18 families: the official library router)

The ~1000 reference captions are organized into 18 families. The families are NOT the
model's limits; they are a word map for naming styles in the library's naming language.
`emotional, epic, dark, modern, cinematic` is a MODIFIER, not a genre.

| Family | Scope |
|---|---|
| general-pop-ballad | Pop, contemporary pop, pop ballad, general emotional songs |
| dance-pop-disco-funk | Dance-pop, nu-disco, funk-pop, disco revival, groove pop |
| club-edm-house-trance | EDM, house, trance, hardstyle, dubstep, techno, festival |
| electronic-synth-ambient-pop | Synth-pop, electropop, dream pop, ambient pop, darkwave, retrowave |
| modern-rnb-neo-soul | Contemporary/alt R&B, neo-soul, trap soul |
| hip-hop-rap | Hip-hop, rap, trap, drill, lo-fi hip-hop, melodic rap |
| soul-blues-gospel | Soul, blues, blues rock, gospel, worship |
| jazz-swing-big-band | Vocal jazz, jazz ballad, big band, swing, bossa nova, lounge |
| traditional-vocal-stage | Crooner, doo-wop, a cappella, musical theater, cabaret |
| pop-alternative-rock | Pop rock, alternative, indie rock, arena rock, J-rock, punk |
| metal-heavy-rock | Metalcore, power/symphonic metal, nu-metal, hard rock |
| contemporary-folk-acoustic | Indie folk, folk pop, singer-songwriter, modern acoustic pop |
| roots-traditional-global | Traditional folk, Celtic, Chinese traditional, reggae, global fusion |
| country-americana | Country, Americana, bluegrass, country rock, rockabilly |
| cinematic-pop-ballad | Cinematic pop, orchestral pop songs, soundtrack-style vocal ballad |
| cinematic-orchestral-epic | Film score, trailer, epic choir, symphonic soundtrack |
| east-asian-modern | Mando/C/Canto/J-pop; with electronic, R&B, dance or rock production |
| east-asian-ballad-heritage | East Asian ballads, guofeng pop, acoustic/orchestral East Asian pop |

Fusion: name both styles explicitly ("cinematic orchestral with Chinese folk
instrumentation") and commit in the caption to how the two palettes share the
arrangement.

## 6. Official example captions (verbatim from the library)

### Example A: Disco / Funk Pop (122 BPM, G minor, female vocal)

Global Metadata
Basic Attributes: bpm is 122. key is G, and scale is minor. Disco / Funk Pop.
Global Emotional Progression: The track launches immediately with high-energy confidence
and a celebratory atmosphere, maintaining a consistent, driving momentum through the
verses. The emotional intensity peaks during the choruses with an exuberant, dance-floor
release, while the bridge offers a brief, slightly more atmospheric respite before surging
back into a final, triumphant climax that sustains the upbeat vibe to the very end.
Application Scenarios & Imagery: A vibrant night-time cityscape with neon lights, a
retro-themed dance party, or a feel-good montage in a film depicting freedom and
self-expression.
Sonics & Production Profile: The mix is polished and high-fidelity with a wide stereo
image, placing the rhythm section centrally while spreading brass and synth elements
across the field. The frequency response is bright and punchy, emphasizing the crisp highs
of the percussion and the warmth of the bass, with a heavily compressed dynamic profile
typical of modern dance-pop to ensure constant loudness and impact.

Vocal Details
Vocal Gender & Timbre: Singer A (Female). The vocalist possesses a clear, bright, and
powerful mezzo-soprano timbre with a slight edge that cuts through the dense arrangement.
Vocal Style: The performance is energetic and assertive, utilizing a clean pop delivery
with strong rhythmic phrasing that locks tightly with the funk groove. The singer employs
dynamic belting in the choruses to convey confidence, while adding playful ad-libs and
runs in the transitions to enhance the lively character.
Harmony/Backing Vocals: Multi-tracked backing vocals appear prominently in the choruses,
providing rich, layered harmonies that thicken the texture. Call-and-response patterns are
utilized between the lead and background layers, particularly in the post-chorus sections,
adding depth and a communal feel.
Vocal FX: The lead vocal is treated with moderate reverb and delay to create space without
losing presence. Subtle pitch correction ensures a pristine pop sheen, while occasional
doubling effects are applied to specific phrases in the chorus to enhance power.

Arrangement
Instrument Lifecycle (Primary/Secondary): Primary: A funky, melodic electric bass line
drives the song from start to finish, serving as the central hook and harmonic anchor.
Secondary: A tight drum kit with a prominent snare and crisp hi-hats establishes the disco
groove immediately. Rhythmic electric guitar chops enter early, adding percussive texture.
Bright synthesizer stabs and lush pads fill out the harmonic spectrum, while a vibrant
brass section (trumpets, saxophones) punctuates the choruses and instrumental breaks with
punchy hits.
Groove & Foundation Progression: The foundation is built on a classic four-on-the-floor
kick pattern paired with a syncopated bass line and off-beat guitar strums. The energy
remains high throughout, but the density increases in the choruses with the addition of
the full brass section and layered synths. The bridge strips back to bass, drums, and
atmospheric keys before rebuilding tension for the final explosive chorus.
Embellishments, Textures & Spatial FX: Transition sweeps and risers are used to build
tension into the choruses. Short, sharp brass stabs act as rhythmic accents. Background
vocal shouts and ad-libs are panned wide to enhance the stereo width. Reverb tails on the
snare and synth pads create a sense of a large, open performance space.

### Example B: Acoustic Folk / Mandopop Ballad (146 BPM, Bb major, male vocal)

Global Metadata
Basic Attributes: bpm is 146. key is Bb, and scale is major. Acoustic Folk / Mandopop Ballad.
Global Emotional Progression: The piece opens with a solitary, contemplative mood
established by sparse instrumentation. As the arrangement gradually thickens with the
introduction of rhythm and harmony, the emotion shifts from quiet introspection to a
gentle, swelling yearning. The track maintains a consistent, tender melancholy throughout,
resolving into a peaceful, fading conclusion that mirrors the initial solitude.
Application Scenarios & Imagery: A dimly lit room at night, a moment of quiet reflection
beside a window, or a scene depicting slow passage of time in a historical drama.
Sonics & Production Profile: The soundstage is intimate and centered, prioritizing clarity
and warmth over width. The frequency response is dominated by rich mid-range tones from the
acoustic guitar and vocals, with a soft, rounded low end. The dynamic aesthetic is natural
and uncompressed, allowing for subtle breath noises and string textures to remain audible,
creating an organic listening experience.

Vocal Details
Vocal Gender & Timbre: Singer A (Male). The vocalist possesses a warm, slightly breathy
baritone timbre that conveys vulnerability and sincerity.
Vocal Style: The performance is characterized by a soft, storytelling delivery with minimal
vibrato. The singer maintains a restrained dynamic range, focusing on emotional nuance
rather than power, with a slight increase in intensity during the later repetitions of the
melodic phrase to reflect growing urgency.
Harmony/Backing Vocals: Subtle, low-register self-harmonies appear during the latter half
of the vocal phrases, adding depth and a choral texture without overpowering the lead
line. These layers are mixed quietly to enhance the atmospheric quality.
Vocal FX: The vocal is treated with a moderate amount of plate reverb to create a sense of
space and intimacy, accompanied by a very subtle delay that trails off quickly, enhancing
the lingering emotional effect.

Arrangement
Instrument Lifecycle (Primary/Secondary): Primary: A steel-string acoustic guitar plays a
finger-picked arpeggio pattern, serving as the harmonic and rhythmic backbone from the
very beginning to the end of the track. Secondary: A clean electric bass enters shortly
after the first vocal phrase, providing a warm, sustained low-end foundation that supports
the chord changes. A soft, brushed drum kit joins in the second half of the song, adding a
gentle pulse without disrupting the calm atmosphere.
Groove & Foundation Progression: The track begins without percussion, relying solely on the
guitar's rhythmic picking to establish tempo. When the drums enter, they utilize a light
kick and soft snare brush pattern, maintaining a laid-back groove that supports the vocal
melody rather than driving it aggressively. The bass locks in with the kick drum to
solidify the harmonic progression once introduced.
Embellishments, Textures & Spatial FX: Occasional high-frequency synth pads or string
swells appear faintly in the background during the chorus sections, adding an ethereal
layer that expands the sonic space. There are no abrupt transitional effects; instead, the
arrangement evolves through the gradual addition and subtraction of instrumental layers to
shape the song's dynamics.

### Example C: Vocal Jazz / Jazz Ballad (62 BPM, Bb major, female vocal)

Global Metadata
Basic Attributes: bpm is 62. key is Bb, and scale is major. Vocal Jazz / Jazz Ballad.
Global Emotional Progression: The piece opens with a tender, intimate atmosphere that feels
like a private confession. As the arrangement gradually thickens with the entrance of the
rhythm section, the emotion swells into a poignant, cinematic crescendo during the middle
sections, before receding back to a fragile, solitary state for the outro, leaving a
lingering sense of bittersweet resolution.
Application Scenarios & Imagery: A dimly lit jazz club late at night, a scene of quiet
reflection in a film noir, or a moment of saying goodbye at a harbor during twilight.
Sonics & Production Profile: The production favors a warm, organic soundstage with a
natural room ambience that places the listener close to the performers. The frequency
response is rich in the mid-range to highlight the vocal and piano warmth, while the
dynamic range is preserved to allow for significant swells in intensity without heavy
compression, creating a highly breathable listening experience.

Vocal Details
Vocal Gender & Timbre: Singer A (Female). The vocalist possesses a smoky, breathy alto
texture with a velvety smoothness that conveys deep intimacy.
Vocal Style: The performance begins with a soft, almost whispered delivery that emphasizes
vulnerability. As the song progresses, the singer employs a more resonant, chest-voice
projection with controlled vibrato during the emotional peaks, before returning to a
delicate, airy falsetto-like quality in the final lines.
Harmony/Backing Vocals: No distinct backing vocals are present; the focus remains entirely
on the solo lead performance.
Vocal FX: The vocal is treated with a lush, long-tail plate reverb that enhances the
spacious, nocturnal atmosphere, along with subtle compression to maintain presence during
the dynamic shifts from whisper to belt.

Arrangement
Instrument Lifecycle (Primary/Secondary): Primary: A grand piano serves as the harmonic and
melodic anchor throughout the entire track, providing arpeggiated figures in the intro and
shifting to fuller, rhythmic chord voicings as the arrangement builds. Secondary: An upright
bass enters shortly after the first verse, offering a warm, walking line that grounds the
harmony. A brushed drum kit joins in the second section, adding a soft, swishing texture
that evolves into a slightly more defined swing feel during the climax. A muted acoustic
brass instrument (likely a flugelhorn or trumpet) enters during the bridge and final
chorus, playing lyrical counter-melodies that weave around the vocal line.
Groove & Foundation Progression: The track begins without percussion, relying solely on the
rubato feel of the piano. When the groove establishes, it is driven by a light,
brush-on-snare pattern and a steady upright bass pulse, maintaining a slow, swaying 4/4
time signature. The energy subtly increases in the bridge as the brass enters, adding
weight to the rhythm before the instruments drop out one by one in the outro, returning to
the sparse piano-only texture.
Embellishments, Textures & Spatial FX: Subtle cymbal swells and soft hi-hat accents provide
transitional markers between sections. The acoustic brass instrument acts as a textural
embellishment, adding a layer of melancholic color in the upper register during the song's
emotional peak. The overall spatial mix places the piano slightly left, the bass
center-low, and the brass slightly right, creating a balanced, live-room stereo image.

### Example D: Cinematic Orchestral / Epic Chinese Folk Fusion (128 BPM, Eb minor, male + choir)

Global Metadata
Basic Attributes: bpm is 128. key is Eb, and scale is minor. Cinematic Orchestral / Epic
Chinese Folk Fusion.
Global Emotional Progression: The piece opens with a solitary, mournful atmosphere that
establishes a sense of ancient vastness. It gradually builds tension through rhythmic
layering, exploding into a heroic and triumphant anthem during the choruses. The bridge
offers a brief, contemplative respite before surging into a final, massive climax that
resolves with a lingering, majestic echo.
Application Scenarios & Imagery: Ideal for historical epic film trailers, video game battle
sequences set in ancient landscapes, or dramatic montages depicting natural grandeur and
human resilience. The imagery evokes sweeping mountain ranges, turbulent rivers, and
large-scale ceremonial gatherings.
Sonics & Production Profile: The production features an expansive, wide soundstage with
deep spatial depth, simulating a large concert hall or open canyon. The frequency response
is full-range, characterized by booming low-end percussion and crisp, bright highs from the
plucked strings and cymbals. The dynamic aesthetic shifts from highly breathable and
intimate in the intro to heavily compressed and wall-of-sound dense in the climactic
sections.

Vocal Details
Vocal Gender & Timbre: Singer A (Male). The vocalist possesses a powerful, resonant
baritone with a slightly gritty texture capable of sustaining high-intensity notes. His
timbre conveys both storytelling intimacy and commanding authority.
Vocal Style: The performance begins with a restrained, narrative delivery that emphasizes
lyrical clarity. As the arrangement intensifies, the style evolves into a forceful, belting
technique with strong vibrato, particularly in the choruses where the emotional weight
peaks. The delivery remains clean but aggressive, matching the epic scale of the
instrumentation.
Harmony/Backing Vocals: Massive, multi-tracked choir harmonies enter during the choruses
and the final climax, providing a broad, anthemic backdrop that reinforces the lead vocal's
melody. These backing layers are mixed wide to create a sense of a large crowd or army
chanting in unison.
Vocal FX: The lead vocal is treated with a moderate amount of plate reverb to enhance
presence without losing intelligibility. In the choruses, a subtle delay is added to the
tail end of phrases to accentuate the rhythmic drive. The choir layers feature a longer
hall reverb to blend them into the orchestral background.

Arrangement
Instrument Lifecycle (Primary/Secondary): Primary: A traditional bowed string instrument
(resembling an Erhu) carries the main melodic motif throughout the track, appearing in the
intro, bridging sections, and soaring over the chorus. It is supported by a modern
orchestral string section that provides harmonic swells and rhythmic ostinatos. Secondary:
A distinct plucked string instrument (resembling a Pipa or Guzheng) adds rapid, percussive
arpeggios during the verses and pre-choruses, driving the momentum. Synthesizer pads enter
subtly in the background to thicken the texture, while a deep sub-bass synth anchors the
low frequencies during the heavy choruses. Keyboard instruments provide harmonic support
with piano chords in the quieter sections and synthesized brass stabs in the climaxes.
Groove & Foundation Progression: The rhythm section begins with sparse, atmospheric
percussion hits. A steady, driving drum beat featuring taiko-style drums and modern
kick/snare patterns enters at the first verse, establishing a marching pulse. This groove
intensifies in the choruses with the addition of crashing cymbals and complex tom fills,
creating a sense of urgency and power. The percussion drops out briefly in the bridge for
dynamic contrast before returning with maximum force for the finale.
Embellishments, Textures & Spatial FX: Wind sound effects and reverse cymbal swells are
used as transitional elements between sections to heighten anticipation. Occasional gong
strikes punctuate key structural moments, adding a ceremonial feel. Atmospheric noise
textures, such as distant wind or rushing water, are layered low in the mix to reinforce
the thematic imagery of nature and vastness.

## 7. Pre-flight checklist

- [ ] Every section tag stands alone on its own line
- [ ] Vocal gender + timbre stated explicitly (or "instrumental" declared + the lead
      instrument named)
- [ ] The caption has EXACTLY 3 sections: Global Metadata → Vocal Details → Arrangement
      (in this order)
- [ ] The arrangement reads as a timeline: per-section entry, exit, change, density
- [ ] BPM/key present only when truly wanted; no invented precision
- [ ] No quoted song lyric lines inside the caption
- [ ] Explicit exclusions/requirements survive without contradiction across the whole
      caption
- [ ] ~250–450 words: specific but not an essay

## 8. Comfy workflow controls (audio_minimax_music_3)

- **caption**: the 3-section music description (structure above).
- **lyrics**: the words with section tags (`[Intro]`, `[Verse]`, `[Chorus]`, `[Bridge]`,
  `[Instrumental]`, `[Outro]`...).
- **max_duration**: target duration in seconds (template 60; model up to ~300 s / 5
  min). Longer song = more time + VRAM.
- **seed**: keep fixed → same song; change → different take.
- **tiled_decode**: decodes the audio VAE with overlapping tiles, lowers VRAM (for long
  songs/low VRAM). Slightly slower + small seam risk at tile boundaries; OFF for best
  quality on high VRAM.
- Output: 32 kHz 16-bit stereo, `output/audio/audio_minimax_music3.mp3`.
- Model files (under the Shared models root): `minimax_music3_dit_fp16.safetensors`
  (diffusion_models/, with an INT8 variant `minimax_music3_dit_int8_convrot.safetensors`
  for low VRAM), `minimax_music3_text_encoder_pruned_int8_convrot.safetensors`
  (text_encoders/), `minimax_music3_dav.safetensors` (vae/).

## 9. The official rewriter skill (this skill's library/ folder = that skill's library)

MiniMax's official `music-caption-rewriter` skill turns a short description + optional
tagged lyrics into a fully structured caption; it is fed by a library of 1000 reference
captions. The entire library lives in this skill's `library/` folder (genre-router.md,
18 indexes, 1000 templates). Usage pattern (from the official SKILL.md):

1. Build a Music Brief from the inputs (explicit/tagged/inferred/unspecified
   classification).
2. Resolve explicit constraints; priority: explicit user requirements > tag directives
   (within their section) > caption implications > reference features > conservative
   defaults.
3. `genre-router.md` → 1 primary (+1 secondary on fusion) family; read ONLY those
   indexes.
4. Pick max 3 references from the index cards, with different roles: Foundation
   (closest identity), Modifier (desired secondary dimension), Arrangement (timeline
   logic).
5. Open ONLY the selected template files; do not copy sentences/whole structures,
   re-synthesize around the brief. Do not inherit the template's key/BPM/vocal/story
   details.
6. Design the timeline section by section; render it with the 3 sections; verify the
   checklist.

## 10. Model limits (official README)

- Inference requires CUDA; non-streaming only.
- The text prompt is capped at ~5000 tokens.
- Audio generation max 9000 acoustic frames (25 fps → 360 s; the ComfyUI widget has a
  300 s upper bound).
- Tags/descriptions are generative control, not a symbolic GUARANTEE: tempo, key,
  instrumentation, lyrics, structure may not match exactly.
