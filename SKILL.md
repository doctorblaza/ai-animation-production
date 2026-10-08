---
name: "ai-animation-production"
description: "End-to-end AI animation/CG production workflow: static-first image generation, image-to-video micro-motion, art-style locking, parallel batch generation, and frame-level QC. Use when producing animated CGs, game cutscenes, trailers, or any AI-generated video content."
---

# AI Animation Production

## Purpose
Produce AI-generated animation and game CGs with consistent art style and reliable quality. Covers the full pipeline from static keyframes to final video delivery, based on production experience across AVG games, trailers, and animated shorts.

## Workflow

### 1. Lock the Art Style First
Before generating anything, define the style in one sentence and enforce it on every prompt:
- Example: "realistic, slight oil-painting feel, clear lines, muted cool tones — no watercolor, no sketch"
- Generate ONE benchmark image and get it approved before batch production
- Every subsequent prompt must repeat the style keywords verbatim

### 2. Static Before Motion (Iron Rule)
**Never go directly to text-to-video.** Always:
1. Generate the static image first
2. Verify it passes QC (see below)
3. Then animate it with image-to-video

This gives you a stable, controllable base. Text-to-video is unpredictable and tears easily.

### 3. Image-to-Video: Micro-Motion for CGs
For game CGs and atmospheric scenes, use **subtle ambient motion only**:
- Rain falling, curtains swaying, dust in light beams, lamp flicker, breathing
- Do NOT add large actions, camera moves, or deformations
- Keep composition, characters, and style identical to the static
- 10 seconds per clip is the sweet spot for looping backgrounds

For dramatic/action content, describe exactly three things: character action, camera move, environment dynamics. Keep it simple — overloading the prompt causes artifacts.

### 4. Parallel Batch Generation
For large batches (e.g., 19 scenes × 2 variants = 38 videos):
- Split into 2–4 subagents, each handling a contiguous range
- Each agent generates independently and reports results
- Use consistent naming: `scene-XX-anim-a.mp4`, `scene-XX-anim-b.mp4`
- A/B variants should have clearly different motion directions

### 5. QC: The "Does It Look Right" Standard
The acceptance bar is **"does it look right"**, not "does it run". Specifically:
- **Buildings**: old but lived-in, never ruins. Walls intact, no peeling paint piles, no garbage heaps, windows unbroken. A poor family's home is modest, not abandoned.
- **Style consistency**: every image must match the benchmark at a glance. If one looks cartoonish while others are muted oil-painting, redo it.
- **No placeholders**: never ship a flawed frame hoping no one notices. If it looks wrong, regenerate.
- **Watch every clip**: play each video fully before delivery. Check for warping, extra limbs, style breaks.

### 6. Scene-to-CG Mapping
For AVG/visual-novel games, build a scene map document:
- Every script scene → its CG file
- Mark climax/scary moments for animated versions
- Animated CGs appear ONLY at high-impact moments, never with dialogue text baked in
- Static CGs for everything else

### 7. Delivery
- MP4 must have `faststart` (`moov` atom at file head) for web/GitHub playback
- Fix with: `ffmpeg -c copy -movflags faststart input.mp4 output.mp4`
- Push via Git Database API (never `git add -A` / `git push`)
- Verify the deployed URL actually loads before announcing completion

## Tooling
- `media.generate_image`: static keyframe generation (pass reference image + text)
- `media.generate_video`: image-to-video animation (pass static image + motion description)
- `muse.exec` + `ffmpeg`: format conversion, faststart, duration checks
- `~/workspace/skills/github/bin/gh-push-safe`: safe GitHub push preserving remote tree

## Operating Rules
1. **Static before motion** — no exceptions. Text-to-video direct generation is banned.
2. **One benchmark image** approved before any batch work starts.
3. **Style keywords repeated verbatim** in every generation prompt.
4. **Subtle motion only** for atmospheric CGs. Big actions need explicit direction.
5. **Watch everything** before delivery. A successful tool call proves the file exists, not that it looks right.
6. **Never ship placeholders.** If a frame looks wrong, regenerate it.
7. **Buildings are lived-in, not ruins.** This is the #1 most common failure mode.
8. **MP4s must be faststart** before upload.
9. **Verify the live URL** after every push. Never say "done" without checking.
10. When the user corrects one thing, fix only that thing. Don't touch what's already approved.
