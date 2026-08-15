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

# MiniMax Music 3 Prompt Yazma (resmi rehber + resmi skill kütüphanesi)

## When to Use

- MiniMax Music 3 ile şarkı/müzik üretirken prompt yazılacaksa (bu makinede ComfyUI
  `audio_minimax_music_3` workflow'u).
- Kullanıcı şarkı fikri, tarz veya duygu verdiğinde → bu skill'in prosedürüyle
  Caption + Lyrics ikilisini üret.

Kaynaklar (2026-08'de incelendi):
- Comfy resmi tutorial: `https://docs.comfy.org/tutorials/audio/minimax/minimax-music-3`
- Prompting guide (HF Space): `https://huggingface.co/spaces/multimodalart/minimax-music3-prompting-guide`
- Resmi `music-caption-rewriter` skill'i: `github.com/MiniMax-AI/MiniMax-Music3/skills/music-caption-rewriter`
  (SKILL.md + genre-router + 18 family index + 1000 template → bu skill'in `library/` klasörüne kopyalandı)

## Model Gerçekleri (prompt'u şekillendiren farklar — LTX/Z-Image ile KARIŞTIRMA)

- **İKİ ayrı giriş, rolleri ASLA karıştırma**:
  - **Caption** = müzik tanımı (tarz, tempo, duygu yayı, vokal, aranjman, prodüksiyon).
    Tüm müzikal kontrol BURADA. Model onu zaman içinde takip eder (tek global etiket değil).
  - **Lyrics** = söylenecek sözler + köşeli parantez bölüm tag'leri (yapı direktifleri).
- **Tag'ler SATIR BAŞINDA yalnız durmalı**: `[verse] Morning light...` gibi tag ile aynı
  satırdaki metin SESSİZCE SİLİNİR (input contract). Her tag kendi satırında.
- **Süre ÜST SINIRDIR**: model sözler bitince stop-token ile doğal biter. Kısa lyrics =
  kısa şarkı; 5 dakikalık şarkı için tam şarkılık bölüm + söz gerekir. `max_duration`
  hedefi aşamaz (max 300 sn / 9000 frame, 25 fps).
- **Vokal belirtilmezse → istenmeyen enstrümantal drift** (#1 hata). Vokalli şarkıda
  cinsiyet + tını MUTLAKA açıkça yaz; enstrümantal ise "instrumental" de ve lead
  melodiyi taşıyan enstrümanı adlandır.
- **Dil**: Caption İngilizce (kullanıcı başka dil istemedikçe). Model çok dilli vokal
  destekler; lyrics şarkının dilinde yazılabilir.
- **Token limiti**: metin prompt'u ~5000 token ile sınırlı. Caption hedefi 250–450 kelime.
- Mimari: 8B Global LLM (uzun yapı) + 0.6B Local LLM (akustik detay) + Flow Matching
  sentez. Çıktı 32 kHz 16-bit stereo. Tag'ler sembolik GARANTİ değil, üretken kontroldür:
  tempo/ton/enstrüman birebir tutmayabilir.

## Caption Yapısı — TAM 3 başlık, bu sırada

### 1. Global Metadata
- **Basic Attributes** — genre + subgenre, tempo; key/scale SADECE gerçekten isteniyorsa
  ("bpm is 122. key is G, and scale is minor. Disco / Funk Pop.").
- **Global Emotional Progression** — açılıştan kapanışa duygu yayı HİKAYE olarak:
  nerede başlar, nerede zirve yapar, nasıl çözülür.
- **Application Scenarios & Imagery** — şarkının ait olduğu sahne (gece sürüşü, loş oda,
  çatı geri sayımı). Sahne, sıfatlardan daha iyi mood çıpalar.
- **Sonics & Production Profile** — mix karakteri: stereo genişliği, frekans dengesi,
  dinamikler (polished/compressed vs. natural/uncompressed).

### 2. Vocal Details
- **Vocal Gender & Timbre** — daima açıkça: "Singer A (Female), a warm mezzo-soprano
  with a breathy low register".
- **Vocal Style** — bölüm başına delivery/dinamik: verse'te yakın ve yumuşak,
  chorus'ta belted.
- **Harmony/Backing Vocals** — double'lar, stacked harmoniler, call-and-response, nerede.
- **Vocal FX** — ölçülü: reverb, delay throw, saturation; her biri nerede.
- Enstrümantal: parça enstrümantal olduğunu söyle + lead melodik rolü taşıyan enstrümanı adlandır.

### 3. Arrangement — EKİPMAN LİSTESİ DEĞİL, TIMELINE
- **Instrument Lifecycle (Primary/Secondary)** — baştan sona ne çıpalar; ne girer, çıkar,
  dönüşür.
- **Groove & Foundation Progression** — ritmik temel ve yoğunluk evrimi bölüm bölüm
  (verse'te davullar ne yapar, chorus'ta ne iner, bridge'te ne çekilir).
- **Embellishments, Textures & Spatial FX** — riser, sweep, ear candy, reverb kuyrukları;
  sadece ilgili yerlerde.
- Her bölüm için ne girer/çıkar/değişir/yoğunlaşır yaz; enstrüman davranışı sürekli
  olsun ki geçişler müzikal kalsın.

## Yazım Kuralları

1. **Sahte hassasiyet UYDURMA**: BPM/key'i gerçekten istemiyorsan yazma; aralık veya
   niteliksel tempo ("driving", "unhurried") modele müzikal alan bırakır.
2. **Kendinle sessizce çelişme**: açık vokal cinsiyeti, zorunlu enstrüman, tempo sınırı,
   dışlama → caption'ın TAMAMINDA hayatta kalmalı.
3. **Öncelik sırası** (çelişkide): açık kullanıcı gereksinimleri → bölüm-tag'i direktifleri
   (kendi bölümünde) → caption'ın güçlü ima'ları → genre varsayılanları.
4. **Enerji yayı yaz**: gerilim → çözülme → nefes → zirve hikayesi, statik tanımdan
   daha iyi yapı üretir.
5. **Şarkı sözünü caption'a KOYMA**: caption müziği anlatır; sözler lyrics girişinde.
6. Lyrics'teki tag'ler YAPISAL talimattır; söz metni sadece duygu taşır. Tag'ler ayrıca
   bölümün lokal aranjmanını değiştirebilir (`[solo]`) ama global genre'ı değiştirmez.

## Lyrics Tag Bankası

`[intro]` `[verse]` `[pre-chorus]` `[chorus]` `[post-chorus]` `[bridge]`
`[instrumental]` `[solo]` `[outro]` — her biri kendi satırında.

Varsayılan iskelet: `Intro → Verse → Pre-Chorus → Chorus → Verse → Chorus →
Bridge → Final Chorus → Outro`.

## Style Families (resmi kütüphane router'ı — 18 aile)

Tür adı verirken bu ailelerin dilini kullan; `emotional/epic/dark/modern/cinematic`
genre değil MODIFIER'dır. Tam router tablosu + alias'lar + füzyon kuralları:
`library/genre-router.md`.

| Aile | Kapsam |
|---|---|
| general-pop-ballad | Pop, pop balad, genel duygusal şarkılar (fallback) |
| dance-pop-disco-funk | Dance-pop, nu-disco, funk-pop, groove pop |
| club-edm-house-trance | EDM, house, trance, hardstyle, festival electronic |
| electronic-synth-ambient-pop | Synth-pop, dream pop, darkwave, retrowave |
| modern-rnb-neo-soul | Contemporary/alt R&B, neo-soul, trap soul |
| hip-hop-rap | Hip-hop, rap, trap, drill, lo-fi hip-hop |
| soul-blues-gospel | Soul, blues, gospel, worship |
| jazz-swing-big-band | Vocal jazz, big band, swing, bossa nova, lounge |
| traditional-vocal-stage | Crooner, doo-wop, a cappella, müzikal, cabaret |
| pop-alternative-rock | Pop rock, alt rock, indie rock, arena, punk |
| metal-heavy-rock | Metalcore, power/symphonic metal, nu-metal, hard rock |
| contemporary-folk-acoustic | Indie folk, folk pop, singer-songwriter |
| roots-traditional-global | Geleneksel folk, Celtic, reggae, global füzyon |
| country-americana | Country, Americana, bluegrass, rockabilly |
| cinematic-pop-ballad | Cinematic pop, orkestral pop şarkıları |
| cinematic-orchestral-epic | Film müziği, trailer, epik koro, senfonik |
| east-asian-modern | Mando/C/Canto/J-pop + elektronik/R&B/rock prodüksiyon |
| east-asian-ballad-heritage | Doğu Asya baladları, guofeng pop |

Füzyon: iki tarzı da açıkça adlandır ("cinematic orchestral with Chinese folk
instrumentation"); "X with Y influences" → birincil X, ikincil Y.

## Prompt İnşa Prosedürü (fikir → final çıktı)

1. Kullanıcının fikrinden **Music Brief** çıkar: tür, mood/yay, tempo, vokal varlığı/
   cinsiyet/tını, enstrümanlar, bölüm yapısı, dışlamalar. Her değeri `explicit / tagged /
   inferred / unspecified` olarak sınıfla; hassas değer UYDURMA.
2. Türü aileye yönlendir (`library/genre-router.md`); gerekirse family index'inden
   (`library/index-*.md`) 1–3 kart seç (Foundation/Modifier/Arrangement rolleri),
   SADECE seçilen template'leri aç (`library/templates/<id>.txt`). Template'ten cümle
   veya tam yapı KOPYALAMA; brief etrafında yeniden sentezle.
3. İngilizce caption yaz: 3 başlık, ~250–450 kelime, timeline aranjman, enerji yayı.
4. Lyrics yaz: her tag kendi satırında; bölümler arası boş satır; söz metni duygu taşır.
   Kullanıcı Türkçe şarkı isterse lyrics Türkçe olabilir, caption yine İngilizce.
5. Pre-flight checklist'i (`references/prompting-guide-full.md` sonunda) doğrula.
6. ComfyUI widget'larına yerleştir: caption → `caption`, lyrics → `lyrics`,
   `max_duration` (sn; şablon 60, max 300), `seed`.

## Bu makine — ComfyUI parametreleri (audio_minimax_music_3 workflow'u)

- Path: `...\ComfyUI\user\default\workflows\audio_minimax_music_3.json`
  (subgraph: "Text to Music (MiniMax Music 3)").
- Loader'lar: UNET `minimax_music3_dit_fp16.safetensors`, CLIP
  `minimax_music3_text_encoder_pruned_int8_convrot.safetensors` (type: minimax),
  VAE `minimax_music3_dav.safetensors` — hepsi Shared models kökünde mevcut.
- KSampler: steps=30, cfg=1.7, euler, simple, denoise=1. TextEncode widget'ları:
  `max_duration=60`, `cfg_scale=1.7` (node default 1.5), `top_k=50`.
- `tiled_decode` switch'i: uzun şarkılarda VRAM için ON (1536/64 tile); kısa şarkıda
  en iyi kalite için OFF (tile dikişi riski sıfır).
- Workflow'ta 3 UnloadAllModels node'u ekli (KSampler öncesi model zinciri, iki
  VAEDecodeAudio latent zinciri) — dokunma.
- Çıktı: `output/audio/audio_minimax_music3.mp3` (SaveAudioAdvanced).
- Subgraph instance'ında hazır bir Lo-fi örnek caption + lyrics var — format örneği olarak okunabilir.

## Destek dosyaları

- `references/prompting-guide-full.md` — HF Space rehberi + Comfy tutorial'ın tam
  distillenmiş içeriği: iki giriş kontratı, 3 başlığın alt elemanları, yazım kuralları,
  4 tam resmi örnek caption, pre-flight checklist.
- `library/genre-router.md` — resmi router: aile tablosu, alias'lar, füzyon kuralları,
  fallback mantığı.
- `library/index-*.md` — 18 aile index'i (kompakt kart tabloları: ID, tarz, tempo/key,
  mood yayı, vokal ipucu, core palette, template yolu).
- `library/templates/*.txt` — 1000 resmi referans caption (tam metin). SADECE router →
  index → seçilen kart zincirinden aç; tümünü tarama.
