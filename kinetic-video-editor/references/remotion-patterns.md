# Remotion Blueprints & Animation Patterns

> Production-ready, lightweight React + TypeScript components and audio-sync pipelines for Remotion 4.x. Optimized specifically for low-spec systems (Intel Core i3, integrated graphics, limited RAM) using pure CSS compositor transforms and deterministic frame math.

---

## 1. Hardware-Safe Execution Guidelines

### Hardware Profile
- **CPU**: Intel Core i3-3220 (2 cores, 4 threads @ 3.30GHz, AVX 1.0, NO AVX2, NO AES-NI)
- **RAM**: 7.1 GiB (strictly bounded memory budget)
- **GPU**: Integrated Intel HD Graphics 2500 (Software/Mesa OpenGL)

### Golden Performance Rules
1. **Compositor-Only Motion**:
   - **Allowed**: `transform: translate3d(x, y, 0)`, `scale()`, `rotate()`, and `opacity`.
   - **Forbidden**: Animating `top`, `left`, `bottom`, `right`, `width`, `height`, `margin`, `padding`, or `border-width`. These force CPU layout recalculation (reflow) on every frame and will severely throttle render speeds or cause Chromium out-of-memory crashes.
2. **No WebGL / Three.js Shaders**:
   - Rely strictly on lightweight CSS vector rendering, pre-rendered SVG filters, and HTML Canvas 2D when necessary. Never invoke 3D shaders or WebGL renderers on integrated Intel HD 2500 graphics.
3. **Asset Dimension Discipline**:
   - Downscale all raster images (photos, stickers, background textures) to **maximum 1080px width** before rendering. Decoding 4K/8K photos into a Remotion composition exhausts RAM instantly on 7GB systems.
4. **Remotion Render CLI Flags**:
   - Always run rendering with `--concurrency=2` to ensure stability without exhausting RAM.
   - Use `--pixel-format=yuv420p --codec=h264 --crf=20 --image-format=jpeg`.
   ```bash
   npx remotion render Root MyComp out/video.mp4 --concurrency=2 --image-format=jpeg --jpeg-quality=90
   ```

---

## 2. Deterministic Math & Spring Presets

In Remotion, never use `Date.now()`, `performance.now()`, or unseeded `Math.random()`. All animations must be a pure mathematical function of `useCurrentFrame()`.

```typescript
import { spring, interpolate, useCurrentFrame, useVideoConfig } from 'remotion';

// Spring Physics Presets for Low-Spec Performance
export const SPRING_PRESETS = {
  // Snappy punch: rapid acceleration with subtle settle (text entrances, stickers)
  snappy: { damping: 14, stiffness: 220, mass: 0.7 },
  
  // Bouncy pop: visible overshoot for punch words and badges
  pop: { damping: 10, stiffness: 180, mass: 0.9 },
  
  // Smooth cinematic: gentle deceleration for camera zooms, split screens
  gentle: { damping: 20, stiffness: 110, mass: 1.2 },
  
  // Instant trigger: ultra-tight for micro-staggers
  stiff: { damping: 18, stiffness: 300, mass: 0.5 },
} as const;

// Helper: safe frame progress (0.0 to 1.0 clamped)
export const getProgress = (
  frame: number,
  startFrame: number,
  durationFrames: number
): number => {
  return interpolate(frame, [startFrame, startFrame + durationFrames], [0, 1], {
    extrapolateLeft: 'clamp',
    extrapolateRight: 'clamp',
  });
};
```

---

## 3. Blueprint A: Kinetic Typography Lockup Component

A multi-font graphic block composition where words arrive through an `overflow: hidden` mask using staggered spring physics.

```tsx
import React from 'react';
import { interpolate, spring, useCurrentFrame, useVideoConfig } from 'remotion';
import { SPRING_PRESETS } from './math';

interface LockupWord {
  text: string;
  fontFamily: string;
  fontSize: number;
  fontWeight: number | string;
  color: string;
  letterSpacing?: string;
  fontStyle?: 'normal' | 'italic';
  textTransform?: 'uppercase' | 'lowercase' | 'capitalize' | 'none';
  customStyle?: React.CSSProperties;
}

interface KineticLockupProps {
  startFrame: number;
  words: LockupWord[];
  staggerFrames?: number; // Delay between word appearances (default: 2 frames)
  containerStyle?: React.CSSProperties;
}

export const KineticLockup: React.FC<KineticLockupProps> = ({
  startFrame,
  words,
  staggerFrames = 2,
  containerStyle,
}) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  return (
    <div
      style={{
        display: 'flex',
        flexDirection: 'column',
        alignItems: 'center',
        justifyContent: 'center',
        width: '100%',
        maxWidth: 864, // Clamped to Platform Safe Zone
        margin: '0 auto',
        ...containerStyle,
      }}
    >
      {words.map((w, index) => {
        const wordStart = startFrame + index * staggerFrames;
        
        // Pure spring physics for masked rise
        const spr = spring({
          frame: frame - wordStart,
          fps,
          config: SPRING_PRESETS.snappy,
        });

        // Translate from +115% below the mask to 0%
        const translateY = interpolate(spr, [0, 1], [115, 0]);
        const opacity = interpolate(spr, [0, 0.2, 1], [0, 0.8, 1]);

        return (
          <div
            key={index}
            style={{
              overflow: 'hidden',
              lineHeight: 0.95,
              padding: '2px 8px',
            }}
          >
            <span
              style={{
                display: 'inline-block',
                fontFamily: w.fontFamily,
                fontSize: w.fontSize,
                fontWeight: w.fontWeight,
                fontStyle: w.fontStyle || 'normal',
                letterSpacing: w.letterSpacing || '-0.03em',
                textTransform: w.textTransform || 'none',
                color: w.color,
                transform: `translate3d(0, ${translateY}%, 0)`,
                opacity,
                willChange: 'transform, opacity',
                ...w.customStyle,
              }}
            >
              {w.text}
            </span>
          </div>
        );
      })}
    </div>
  );
};
```

---

## 4. Blueprint B: Fast-Paced Karaoke Subtitle Engine

Synchronizes with speech audio, placing words securely inside the Safe Zone and popping active words with scale and accent color.

```tsx
import React from 'react';
import { interpolate, spring, useCurrentFrame, useVideoConfig } from 'remotion';
import { SPRING_PRESETS } from './math';

export interface CaptionCue {
  id: string;
  word: string;
  startFrame: number;
  endFrame: number;
  isPunchWord?: boolean;
}

interface KaraokeCaptionsProps {
  cues: CaptionCue[];
  activeColor?: string;     // e.g. '#ff4d00' (Signal Orange) or '#ffd700' (Yellow)
  inactiveColor?: string;   // e.g. '#f4f1eb'
  punchColor?: string;      // e.g. '#c72424' (Crimson)
  fontSize?: number;        // e.g. 52
}

export const KaraokeCaptions: React.FC<KaraokeCaptionsProps> = ({
  cues,
  activeColor = '#ff4d00',
  inactiveColor = '#f4f1eb',
  punchColor = '#ffd700',
  fontSize = 52,
}) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  // Find cues in current visible window (render 3-5 words at a time)
  const activeCue = cues.find((c) => frame >= c.startFrame && frame <= c.endFrame);
  if (!activeCue) return null;

  // Window: 1 cue before, active cue, 2 cues after
  const activeIndex = cues.indexOf(activeCue);
  const startIdx = Math.max(0, activeIndex - 1);
  const endIdx = Math.min(cues.length, activeIndex + 3);
  const visibleCues = cues.slice(startIdx, endIdx);

  return (
    <div
      style={{
        position: 'absolute',
        bottom: 460, // Above Platform Bottom Safe Margin (422px)
        left: 64,
        right: 152, // Avoid right-side platform buttons (152px)
        display: 'flex',
        flexWrap: 'wrap',
        justifyContent: 'center',
        alignItems: 'center',
        gap: '12px 16px',
        textAlign: 'center',
      }}
    >
      {visibleCues.map((cue) => {
        const isActive = frame >= cue.startFrame && frame <= cue.endFrame;
        const isPast = frame > cue.endFrame;

        // Pop spring on word trigger
        const popSpr = spring({
          frame: frame - cue.startFrame,
          fps,
          config: SPRING_PRESETS.pop,
        });

        const scale = isActive ? interpolate(popSpr, [0, 0.4, 1], [1.0, 1.18, 1.08]) : 1.0;
        const color = isActive
          ? (cue.isPunchWord ? punchColor : activeColor)
          : isPast
          ? inactiveColor
          : 'rgba(244, 241, 235, 0.45)';

        const fontWeight = isActive || cue.isPunchWord ? 800 : 600;

        return (
          <span
            key={cue.id}
            style={{
              fontFamily: "'Inter', sans-serif",
              fontSize,
              fontWeight,
              color,
              transform: `translate3d(0, 0, 0) scale(${scale})`,
              transition: 'color 0.1s ease',
              textShadow: '0 3px 12px rgba(0, 0, 0, 0.85)',
              willChange: 'transform, color',
            }}
          >
            {cue.word}
          </span>
        );
      })}
    </div>
  );
};
```

---

## 5. Blueprint C: Faceless POV Desk & Screen Demo Component

Splits the vertical viewport into an overhead analog desk shot and a crisp screen demo, joined by a monospace HUD timecode bar.

```tsx
import React from 'react';
import { Img, interpolate, spring, useCurrentFrame, useVideoConfig } from 'remotion';
import { SPRING_PRESETS } from './math';

interface SplitDeskDemoProps {
  topImageSrc: string;     // Desk POV / Notebook overhead
  bottomImageSrc: string;  // Screen capture / Code demo
  timestampText: string;   // e.g. "14:00" or "DAY 01 // 09:30"
  badgeLabel?: string;     // e.g. "MISSION: 90"
  startFrame: number;
}

export const SplitDeskDemo: React.FC<SplitDeskDemoProps> = ({
  topImageSrc,
  bottomImageSrc,
  timestampText,
  badgeLabel = 'POV // WORKFLOW',
  startFrame,
}) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  // Entrance spring
  const enterSpr = spring({
    frame: frame - startFrame,
    fps,
    config: SPRING_PRESETS.gentle,
  });

  const dividerScaleX = interpolate(enterSpr, [0, 1], [0, 1]);
  const contentOpacity = interpolate(enterSpr, [0, 0.3, 1], [0, 0.5, 1]);

  return (
    <div
      style={{
        position: 'absolute',
        inset: 0,
        backgroundColor: '#0b0b0c',
        display: 'flex',
        flexDirection: 'column',
        width: 1080,
        height: 1920,
        opacity: contentOpacity,
      }}
    >
      {/* Top Half: Physical POV Desk */}
      <div
        style={{
          flex: 1,
          position: 'relative',
          overflow: 'hidden',
          borderBottom: '2px solid rgba(244, 241, 235, 0.15)',
        }}
      >
        <Img
          src={topImageSrc}
          style={{
            width: '100%',
            height: '100%',
            objectFit: 'cover',
          }}
        />
        {/* Top Safe Badge */}
        <div
          style={{
            position: 'absolute',
            top: 210, // Clamped inside Safe Zone (Top > 192)
            left: 64,
            padding: '6px 14px',
            backgroundColor: 'rgba(11, 11, 12, 0.85)',
            border: '1px solid rgba(244, 241, 235, 0.2)',
            borderRadius: 4,
            fontFamily: "'JetBrains Mono', monospace",
            fontSize: 22,
            fontWeight: 600,
            color: '#f4f1eb',
            letterSpacing: '0.08em',
          }}
        >
          {badgeLabel}
        </div>
      </div>

      {/* Center Divider & Timestamp Bar */}
      <div
        style={{
          height: 60,
          backgroundColor: '#141416',
          borderTop: '1px solid rgba(244, 241, 235, 0.1)',
          borderBottom: '1px solid rgba(244, 241, 235, 0.1)',
          display: 'flex',
          alignItems: 'center',
          justifyContent: 'center',
          position: 'relative',
          zIndex: 10,
        }}
      >
        <div
          style={{
            position: 'absolute',
            inset: 0,
            borderBottom: '2px solid #ff4d00',
            transform: `scaleX(${dividerScaleX})`,
            transformOrigin: 'center center',
          }}
        />
        <span
          style={{
            fontFamily: "'JetBrains Mono', monospace",
            fontSize: 26,
            fontWeight: 700,
            color: '#ffffff',
            letterSpacing: '0.12em',
            zIndex: 11,
          }}
        >
          {timestampText}
        </span>
      </div>

      {/* Bottom Half: Screen Demo / Code */}
      <div
        style={{
          flex: 1,
          position: 'relative',
          overflow: 'hidden',
        }}
      >
        <Img
          src={bottomImageSrc}
          style={{
            width: '100%',
            height: '100%',
            objectFit: 'cover',
          }}
        />
      </div>
    </div>
  );
};
```

---

## 6. Blueprint D: Animated Hand-Drawn SVG Annotations

Tactile hand-drawn strokes that animate dynamically as keywords land, using pure SVG with zero third-party library overhead.

```tsx
import React from 'react';
import { interpolate, spring, useCurrentFrame, useVideoConfig } from 'remotion';
import { SPRING_PRESETS } from './math';

interface SvgUnderlineProps {
  startFrame: number;
  width?: number;        // px width (default: 320)
  color?: string;        // default: '#c72424' (Crimson) or '#ff4d00'
  strokeWidth?: number;  // default: 5
}

export const SvgUnderline: React.FC<SvgUnderlineProps> = ({
  startFrame,
  width = 320,
  color = '#c72424',
  strokeWidth = 5,
}) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  const spr = spring({
    frame: frame - startFrame,
    fps,
    config: SPRING_PRESETS.snappy,
  });

  // Stroke draw: strokeDashoffset from pathLength to 0
  const pathLength = width + 40;
  const strokeOffset = interpolate(spr, [0, 1], [pathLength, 0]);

  return (
    <div style={{ width, height: 28, overflow: 'visible', marginTop: 4 }}>
      <svg
        width={width}
        height="28"
        viewBox={`0 0 ${width} 28`}
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
      >
        <path
          d={`M 4,14 Q ${width * 0.45},24 ${width - 6},8`}
          stroke={color}
          strokeWidth={strokeWidth}
          strokeLinecap="round"
          strokeDasharray={pathLength}
          strokeDashoffset={strokeOffset}
          style={{ willChange: 'stroke-dashoffset' }}
        />
      </svg>
    </div>
  );
};

interface HighlighterSweepProps {
  startFrame: number;
  width: number;
  height?: number;
  color?: string; // default: 'rgba(255, 215, 0, 0.35)' (Golden Yellow)
}

export const HighlighterSweep: React.FC<HighlighterSweepProps> = ({
  startFrame,
  width,
  height = 42,
  color = 'rgba(255, 215, 0, 0.35)',
}) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  const spr = spring({
    frame: frame - startFrame,
    fps,
    config: SPRING_PRESETS.snappy,
  });

  const scaleX = interpolate(spr, [0, 1], [0, 1]);

  return (
    <div
      style={{
        position: 'absolute',
        width,
        height,
        backgroundColor: color,
        transform: `scaleX(${scaleX})`,
        transformOrigin: 'left center',
        borderRadius: 3,
        zIndex: -1,
        willChange: 'transform',
      }}
    />
  );
};
```

---

## 7. Blueprint E: SVG Ripped Paper Edge Transition

Lightweight SVG clip-path divider providing an analog zine paper tear between two video scenes without heavy raster mask textures.

```tsx
import React from 'react';
import { interpolate, spring, useCurrentFrame, useVideoConfig } from 'remotion';
import { SPRING_PRESETS } from './math';

interface RippedPaperTransitionProps {
  startFrame: number;
  children: React.ReactNode;
}

export const RippedPaperTransition: React.FC<RippedPaperTransitionProps> = ({
  startFrame,
  children,
}) => {
  const frame = useCurrentFrame();
  const { fps } = useVideoConfig();

  const spr = spring({
    frame: frame - startFrame,
    fps,
    config: SPRING_PRESETS.snappy,
  });

  // Translate tear reveal down screen
  const translateY = interpolate(spr, [0, 1], [-100, 0]);

  return (
    <div
      style={{
        position: 'absolute',
        inset: 0,
        transform: `translate3d(0, ${translateY}%, 0)`,
        willChange: 'transform',
      }}
    >
      {/* Visual Ripped White Paper Edge */}
      <div
        style={{
          position: 'absolute',
          bottom: -16,
          left: 0,
          right: 0,
          height: 32,
          backgroundColor: '#eae0d5',
          clipPath:
            'polygon(0% 0%, 5% 75%, 10% 25%, 15% 85%, 20% 30%, 25% 90%, 30% 20%, 35% 80%, 40% 35%, 45% 95%, 50% 25%, 55% 85%, 60% 40%, 65% 90%, 70% 30%, 75% 80%, 80% 20%, 85% 75%, 90% 35%, 95% 85%, 100% 0%, 100% 100%, 0% 100%)',
          boxShadow: '0 8px 24px rgba(0,0,0,0.4)',
        }}
      />
      {children}
    </div>
  );
};
```

---

## 8. Blueprint F: FFmpeg Audio Syncing & Mastering Pipeline

A self-contained Python / FFmpeg workflow to inspect, loudness-normalize (-14 LUFS, True Peak -1.0 dB), and mux audio with Remotion exports losslessly.

```python
#!/usr/bin/env python3
"""
Audio-video mastering helper for kinetic video projects.
Standard: 1080x1920, 30 FPS, H.264 + AAC, -14 LUFS (EBU R128 / Platform Target).
"""

import subprocess
import json
import sys
import os

def normalize_audio(input_audio: str, output_audio: str):
    """Two-pass EBU R128 loudness normalization using loudnorm."""
    print(f"[*] Analyzing loudness for {input_audio}...")
    
    # Pass 1: Measure
    cmd_pass1 = [
        "ffmpeg", "-i", input_audio,
        "-af", "loudnorm=I=-14:TP=-1.0:LRA=7:print_format=json",
        "-f", "null", "-"
    ]
    res1 = subprocess.run(cmd_pass1, capture_output=True, text=True)
    
    # Parse JSON from stderr
    stderr_lines = res1.stderr.splitlines()
    json_lines = []
    inside_json = False
    for line in stderr_lines:
        if line.strip() == "{":
            inside_json = True
        if inside_json:
            json_lines.append(line)
        if line.strip() == "}":
            inside_json = False
            
    stats = json.loads("".join(json_lines))
    print(f"[*] Measured: {stats['input_i']} LUFS, TP: {stats['input_tp']} dB")
    
    # Pass 2: Apply normalization with measured stats
    cmd_pass2 = [
        "ffmpeg", "-y", "-i", input_audio,
        "-af", (
            f"loudnorm=I=-14:TP=-1.0:LRA=7:"
            f"measured_I={stats['input_i']}:"
            f"measured_TP={stats['input_tp']}:"
            f"measured_LRA={stats['input_lra']}:"
            f"measured_thresh={stats['input_thresh']}:"
            f"offset={stats['target_offset']}"
        ),
        "-ar", "44100",
        "-c:a", "pcm_s16le",
        output_audio
    ]
    subprocess.run(cmd_pass2, check=True)
    print(f"[✓] Normalized audio written to {output_audio}")

def mux_remotion_lossless(video_mp4: str, audio_file: str, final_output: str):
    """
    Losslessly mux Remotion H.264 video stream with mastered audio stream.
    Zero video re-encoding (preserves 100% quality and runs in under 1 second).
    """
    cmd = [
        "ffmpeg", "-y",
        "-i", video_mp4,
        "-i", audio_file,
        "-c:v", "copy",          # Stream copy video (instantaneous)
        "-c:a", "aac",           # Encode audio to AAC
        "-b:a", "192k",
        "-ar", "44100",
        "-shortest",
        "-movflags", "+faststart",
        final_output
    ]
    subprocess.run(cmd, check=True)
    print(f"[✓] Final Reel exported: {final_output}")

if __name__ == "__main__":
    if len(sys.argv) < 4:
        print("Usage: python3 master.py <video.mp4> <audio.wav> <out.mp4>")
        sys.exit(1)
    
    v_in, a_in, out = sys.argv[1], sys.argv[2], sys.argv[3]
    norm_wav = "/tmp/audio_norm.wav"
    try:
        normalize_audio(a_in, norm_wav)
        mux_remotion_lossless(v_in, norm_wav, out)
    finally:
        if os.path.exists(norm_wav):
            os.remove(norm_wav)
```
