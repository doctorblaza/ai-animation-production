# Animation Production: Detailed Reference

## Micro-Motion CG Prompt Patterns

### Image-to-video call structure
```
prompt: [
  {kind: "image", value: "/path/to/static.png"},
  {kind: "text", value: "Subtle ambient motion only: [specific motion]. Keep composition, characters, and art style identical. No camera movement, no deformation."}
]
```

### Motion vocabulary by scene type
| Scene | A-variant motion | B-variant motion |
|-------|-----------------|-----------------|
| Rain | Rain streaks falling | Ripples on puddles / umbrella tremble |
| Interior night | Chandelier sway / curtain drift | Dust motes / lamp glow breathing |
| Train | Lights receding outside window | Passenger breathing / light flicker |
| Courtyard | Leaves swaying | Light/shadow shift |
| Photo on wall | Light play across frame | Dust floating in beam |
| Attic/ceiling | Dust in light beam | Curtain drift |

### What NOT to do
- Don't describe character actions for atmospheric shots (causes warping)
- Don't request camera moves (push/pull/pan) for looping backgrounds
- Don't stack more than 2 motion elements per clip

## Art Style Locking

### The one-sentence style lock
Write it once, paste it into every prompt:
> "realistic, slight oil-painting feel, clear lines, muted [warm/cool] tones — no watercolor, no sketch, no cartoon"

### Common style breaks and fixes
| Symptom | Cause | Fix |
|---------|-------|-----|
| One image looks cartoonish | Missing style keywords | Re-add full style sentence, regenerate |
| Walls look like ruins | Model defaults to "abandoned" | Explicitly say "clean and intact, lived-in, no peeling paint, no garbage" |
| Colors too saturated | Style drift | Add "desaturated" to style lock |
| Faces differ between shots | No character reference | Pass character sheet as reference image |

### The building rule (most common failure)
AI models love to make old buildings look abandoned. Always specify:
- Walls: intact, normal aging, no peeling/mottled patches
- Windows: unbroken, with curtains
- Ground: clean, no garbage piles
- Signs of life: bicycles, laundry lines, potted plants, lit windows

## Batch Production Checklist

- [ ] Benchmark image approved by stakeholder
- [ ] Style sentence finalized
- [ ] Scene list with CG mapping complete
- [ ] Batch split into parallel workers (contiguous ranges)
- [ ] Naming convention set (`scene-XX-anim-a.mp4`)
- [ ] Each worker verifies its own outputs before reporting
- [ ] Parent does final visual pass on all outputs
- [ ] faststart confirmed on all MP4s
- [ ] Pushed and live URL verified

## Subagent Brief Template

```
Generate micro-motion CGs for [project].

Source images: [directory], files scene-XX.png
Art style (repeat verbatim in every prompt): [style sentence]

Your range: scene-XX to scene-YY (N images)
For each: generate 2 variants (a/b with different motion) via media.generate_video
- kind:image = source PNG path
- kind:text = "Subtle ambient motion only: [motion]. Keep composition/characters/style identical. No camera movement."
- output_dir="workspace/[project]/animated", name="scene-XX-anim-a"

Motion suggestions per scene: [list]

Verify each output plays correctly before reporting.
Report: which files generated, any failures.
```

## Delivery Verification

```bash
# Check faststart (moov before mdat)
ffmpeg -v trace -i file.mp4 2>&1 | grep -m2 "type:'moov'\|type:'mdat'"

# Fix if needed (lossless)
ffmpeg -c copy -movflags faststart input.mp4 output.mp4

# Check duration
ffprobe -v error -show_entries format=duration -of default=noprint_wrappers=1 file.mp4
```
