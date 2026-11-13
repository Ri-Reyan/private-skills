---
name: kinetic-video-editor
description: Autonomous, production-grade video production skill for creating 1080x1920 (9:16 vertical, 30 FPS, H.264 MP4) kinetic typography, Reels, Shorts, and faceless storytelling videos using Remotion (React + TypeScript). Engineered for low-spec hardware (Intel Core i3, integrated graphics, limited RAM) with lightweight CSS transforms, SVG, and spring physics.
---

# Kinetic Video Editor Skill

A self-contained, portable, production-grade system for architecting, composing, and rendering vertical short-form videos (Reels, TikToks, YouTube Shorts) in Remotion. It merges the disciplines of kinetic typography, Swiss graphic lockups, analog collage zine aesthetics, and "Faceless POV Desk & Notebook" storytelling.

---

## 1. Hardware Execution Profile (Low-Spec Safe)

This skill is strictly calibrated for resource-constrained systems (e.g., dual-core **Intel Core i3-3220 @ 3.30GHz, 7.1GB RAM, Intel HD Graphics 2500, no AVX2 / AES-NI**):

1. **Rendering Concurrency**:
   - Always run rendering with `--concurrency=2` (maximum 2 worker threads). Setting `--concurrency=4` or omitting it on a 7GB machine risks browser process OOM kills.
2. **Compositor-Only Animations**:
   - Strictly animate `transform: translate3d(x, y, 0)`, `scale()`, `rotate()`, and `opacity`.
   - Never animate layout properties (`top`, `left`, `width`, `height`, `margin`, `padding`). These cause continuous CPU layout recalculations (reflow) on every single frame, choking dual-core CPUs.
3. **Zero WebGL / 3D Canvas Shaders**:
   - Strictly prohibit Three.js, WebGL renderers, and heavy multi-pass Canvas blur filters. All visual effects must be achieved via clean SVG vector paths, CSS drop shadows, and pre-baked textures.
4. **Pre-Scaled Assets**:
   - Pre-scale all photographic, paper texture, and sticker image assets to **maximum 1080px width** before importing them into Remotion. Decoding uncompressed 4K/8K images inside Chromium exhausts system RAM instantly.
5. **Standard Render Command**:
   ```bash
   npx remotion render Root <CompositionId> out/<output_name>.mp4 \
     --concurrency=2 \
     --pixel-format=yuv420p \
     --codec=h264 \
     --crf=20 \
     --image-format=jpeg \
     --jpeg-quality=90
   ```

---

## 2. Platform Delivery Specification (9:16 Vertical)

Every project targets the universal vertical standard:
- **Dimensions**: `1080 x 1920` pixels (9:16 portrait aspect ratio)
- **Frame Rate**: `30 FPS` (constant frame rate, CFR)
- **Video Codec**: `H.264` (`libx264`, `yuv420p`, progressive)
- **Audio Codec**: `AAC` stereo, `44.1 kHz`, `192 kbps`, normalized to `-14.0 LUFS` (True Peak `-1.0 dB`)
- **Container**: `MP4` with `+faststart` (web-optimized MOOV atom placed at head)

### Safe Zone Constraints (Do Not Violate)
Interface overlays on Instagram Reels, TikTok, and YouTube Shorts will cover text placed outside these bounds:
- **Top Safe Margin**: `192px` (status bar, sound pills, platform navigation)
- **Bottom Safe Margin**: `422px` (profile handle, caption text, music title, progress bar)
- **Right Safe Margin**: `152px` (like, comment, bookmark, share icons)
- **Left Safe Margin**: `64px` (safety buffer)
- **Content Safe Box**: `x ∈ [64, 928]`, `y ∈ [192, 1498]`. All kinetic typography and punch words must sit inside this box.

Detailed diagram and coordinates: see [references/style-dna.md](references/style-dna.md#7-platform-safe-zones--framing-specs).

---

## 3. End-to-End Production Workflow

Follow this linear, deterministic pipeline for every video:

```
[Phase 1: Ingestion & Audio Prep]
       ↓
[Phase 2: Storyboard & Frame Budget]
       ↓
[Phase 3: Component Implementation]
       ↓
[Phase 4: Non-Destructive Review Loop]
       ↓
[Phase 5: Render, Master & Verification]
```

### Phase 1: Ingestion & Audio Prep
1. **Analyze Brief & Voiceover**:
   - Ingest script, core thesis, hook, and audio track (speech or music bed).
   - Probe audio file using `ffprobe` to determine exact duration in seconds and sample rate.
   - Calculate total composition frames: `Math.round(durationSeconds * 30)`.
2. **Audio Loudness Normalization**:
   - Master voiceover/music to `-14 LUFS` and `-1.0 dB True Peak` using the two-pass EBU R128 pipeline described in [references/remotion-patterns.md](references/remotion-patterns.md#8-blueprint-f-ffmpeg-audio-syncing--mastering-pipeline).

### Phase 2: Storyboard & Frame Budgeting
Divide the timeline into distinct scenes using the beat pacing principles in [references/style-dna.md](references/style-dna.md#2-beat-pacing--cut-rhythm):
- **Scene 1: The Hook** (0.8s – 1.4s / 24 – 42 frames) — Arrest scroll with high-impact typographic punch or tactile POV action.
- **Scene 2 to N-1: Narrative Beats & Demo** (1.2s – 2.2s each / 36 – 66 frames) — Alternate between physical desk POV, kinetic typography lockups, and screen demo splits.
- **Scene N: Climax & CTA Hold** (1.8s – 2.5s / 54 – 75 frames) — Resolution and persistent call to action before loop point.

### Phase 3: Component Implementation
Assemble the composition using the pure React blueprints in [references/remotion-patterns.md](references/remotion-patterns.md):
- Use `<KineticLockup>` for title and phrase reveals.
- Use `<KaraokeCaptions>` for word-by-word synchronized speech.
- Use `<SplitDeskDemo>` for combining analog desk POV with screen recording.
- Use `<SvgUnderline>` and `<HighlighterSweep>` for hand-drawn emphasis on punch words.
- Use `<RippedPaperTransition>` for analog zine-style scene changes.
- Apply typography and color tokens strictly from [references/style-dna.md](references/style-dna.md#5-curated-color-palettes--contrast-ratios).

### Phase 4: Non-Destructive Review Loop
Never blindly render an entire video without verifying static composition and layout geometry:
1. **Freeze-Frame Inspection**:
   - Extract key test frames (e.g. frame 15, frame 45, frame 90) directly using Remotion still render:
     ```bash
     npx remotion still Root <CompositionId> out/preview_frame.png --frame=45
     ```
   - Visually inspect the generated image for:
     - Safe zone compliance (no text clipped by bottom 422px or right 152px).
     - Color contrast and typographic hierarchy.
     - Element overlap or awkward wrapping.
2. **Timing & Physics Tuning**:
   - Adjust spring damping/stiffness in `SPRING_PRESETS` without altering DOM structure.
   - Adjust stagger offsets (1–3 frames) to align with audio transients.

### Phase 5: Render, Master & Verification
1. Render video stream with Remotion CLI (`--concurrency=2`).
2. Losslessly mux with mastered audio track via `ffmpeg -c:v copy -c:a aac`.
3. Verify final artifact using `ffprobe`:
   ```bash
   ffprobe -v error -select_streams v:0 -show_entries stream=width,height,r_frame_rate,duration -of json final_reel.mp4
   ```

---

## 4. Non-Destructive Iteration Protocol

When iterating based on feedback:
1. **Do Not Rewrite Component Hierarchy**:
   - If font size, color, or position needs tuning, edit the props or CSS tokens, not the component logic.
2. **Preserve Previous Render Until Replacement Passes QA**:
   - Always export to `out/<name>_v2.mp4` rather than overwriting `out/<name>.mp4` directly.
3. **Verify Static Layout Before Motion Playback**:
   - Inspect still frames first. If text is misaligned while frozen, tuning easing curves will not fix it.

---

## 5. Quality Assurance Checklist

Before marking any video complete, ensure every check passes:
- [ ] **Resolution**: Exactly `1080 x 1920` (9:16).
- [ ] **FPS**: Exactly `30` (constant frame rate).
- [ ] **Safe Zones**: Zero text or critical graphics inside the top 192px, bottom 422px, or right 152px.
- [ ] **Pacing**: No scene or static text card remains static for longer than 2.2 seconds.
- [ ] **Contrast**: All text meets WCAG AA (minimum 4.5:1 ratio against background).
- [ ] **Audio Sync**: Punch word animations land within ±2 frames (66ms) of the spoken syllable.
- [ ] **Audio Master**: Loudness measures between -13.5 and -14.5 LUFS with True Peak ≤ -1.0 dB.
- [ ] **Hardware Guard**: Memory usage during render stayed within bounds; no CPU layout reflow properties were animated.

---

## 6. In-Package Reference Library

- [references/style-dna.md](references/style-dna.md): Distilled aesthetic principles, typography systems, curated color palettes, beat pacing math, and storytelling structures.
- [references/remotion-patterns.md](references/remotion-patterns.md): Lightweight Remotion React + TypeScript blueprints, SVG annotations, spring presets, and FFmpeg audio mastering scripts.
