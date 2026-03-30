# Hero Animation — Glass Column Gradient Blob

## Summary
A full-screen WebGL hero animation inspired by Raycast.com — flowing gradient blobs morph organically behind invisible vertical glass columns that refract the color. Features a real-time controls panel for adjusting all visual parameters and mouse attraction. Built for Brightbase as a reusable hero section component.

## Tech Stack
- **Rendering:** Vanilla WebGL (no Three.js or dependencies)
- **Shader:** GLSL fragment shader with Ashima Arts simplex noise
- **UI:** Plain HTML/CSS — no frameworks
- **Build:** None — single HTML file, no bundler
- **Deploy:** Cloudflare Pages at gta.bbase.ai

## File Structure
```
hero-animation/
├── index.html        ← v3: full controls panel + mouse attraction (current)
├── index-v1.html     ← v1: basic glass column refraction
├── index-v2.html     ← v2: color editor + grain + edge distortion
├── PROJECT.md        ← this file
└── .claude/
    └── launch.json   ← local dev server config (python3 http.server :8090)
```

## Architecture
Everything lives in a single `index.html`:

1. **CSS** — Dark theme UI for editor panel (collapsible sections, color pickers, range sliders)
2. **HTML** — Canvas element + hero text overlay + editor panel with Colors and Controls sections
3. **JavaScript** — WebGL setup, shader compilation, mouse tracking, animation loop, preset save/load
4. **GLSL Fragment Shader** — All visual rendering:
   - `snoise()` — Ashima Arts 2D simplex noise
   - `sampleGradient()` — gradient blob with noise-driven position, mouse attraction, radial falloff
   - `grain()` — film grain via sin-based hash
   - `main()` — column system, edge distortion, per-column refraction, vignette, gamma correction

## Key Concepts

### Glass Column System
Vertical columns divide the screen into equal slats. Each column has a unique hash-based UV offset that displaces where it samples the gradient — simulating glass refraction. Columns are invisible when no gradient passes through them.

### Edge Distortion
At column boundaries, two noise layers create organic glass-like bending. Controlled by `u_edgeBend`.

### Blob Morphing
A concentrated radial blob (smoothstep falloff from `u_blobSize`) defines where color appears. The blob center is driven by simplex noise and attracted toward the mouse position via `mix(noiseCenter, mouse, u_mouseAttract)`.

### Domain Warping
Low-frequency noise warps the input coordinates before sampling, creating organic flowing movement.

## Shader Uniforms

### Colors (vec3, RGB 0-1)
| Uniform | Default | Role |
|---------|---------|------|
| `u_highlight` | #EB1A1F | Brightest accent |
| `u_accent` | #FA5230 | Secondary warm tone |
| `u_mid` | #CC1A40 | Mid-range fill |
| `u_shadow` | #73080F | Dark edge color |
| `u_bg` | #050304 | Background/negative space |

### Parameters (float)
| Uniform | Default | Range | Role |
|---------|---------|-------|------|
| `u_speed` | 1.0 | 0.1–3.0 | Animation speed multiplier |
| `u_columns` | 12.0 | 2–24 | Number of glass columns |
| `u_grain` | 0.08 | 0–0.2 | Film grain intensity |
| `u_edgeBend` | 1.0 | 0–3.0 | Column edge distortion |
| `u_blobSize` | 0.75 | 0.2–1.5 | Blob radius |
| `u_refraction` | 1.0 | 0–2.0 | Per-column UV offset strength |
| `u_vignette` | 0.5 | 0–1.0 | Edge darkening |
| `u_mouseAttract` | 0.3 | 0–1.0 | Blob follows mouse strength |

## Controls UI
- **Colors section** — 5 color pickers (Highlight, Accent, Mid, Shadow, Background)
- **Controls section** — 8 range sliders with live value display
- **Save Preset** — exports colors + params as JSON file
- **Load Preset** — imports JSON to restore full state
- Both sections are collapsible

## Local Development
```bash
cd ~/Dropbox/Claude/Projects/hero-animation
python3 -m http.server 8090
# Open http://localhost:8090
```
Or use the Claude Preview server (`hero-animation` config in launch.json).

## Deployment
- **Live URL:** https://gta.bbase.ai
- **Hosting:** Cloudflare Pages (auto-deploy from GitHub)
- **Repo:** github.com/bbagent-01/gta
- All three versions accessible: `/index.html`, `/index-v1.html`, `/index-v2.html`

## Version History
- **v1** (`index-v1.html`) — Initial glass column refraction with flowing gradients
- **v2** (`index-v2.html`) — Added color editor panel, film grain, edge distortion, save/load palettes
- **v3** (`index.html`) — Renamed color labels (generic), added 8 parameter sliders, mouse attraction, combined preset save/load
