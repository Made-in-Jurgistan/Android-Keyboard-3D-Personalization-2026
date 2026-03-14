# Android Keyboard 3D & Personalization Guide 2026 — Extension Volume

> A comprehensive companion to the Android Keyboard Design Guide 2026. Covers every technique for building deeply customized keyboards — from OpenGL ES 3D rendering and Filament PBR shading to per-user dynamic themes, game engine bridges, AI-driven personalization, and production performance budgets.

---

## Overview

This is **Volume 2** in a two-part series. It assumes you have the IME fundamentals from Volume 1 and focuses exclusively on the visual, rendering, and personalization systems that make a keyboard exceptional.

| Volume | Focus |
|---|---|
| [Vol. 1 — Design Guide](../android-keyboard-design-guide/) | UX, architecture, accessibility, QA, design tokens, Kotlin code |
| **Vol. 2 — This guide** | OpenGL ES, Filament PBR, particle systems, custom themes, game engines |

This guide ships as a **single-file HTML document** — no build step, no dependencies. Open it directly in a browser or serve it statically. 31 sections, 2,900+ lines, with full Kotlin, GLSL, and JSON code throughout.

---

## What's Inside

### Orientation (§01–§03)
Scope, prerequisites, role-based reading paths, and a complete comparison of every rendering pipeline available on Android — from standard Canvas to Vulkan, Filament PBR, and AGSL shaders. Annotated index of all open-source reference IMEs used throughout this guide.

### 3D Foundations (§04–§08)
OpenGL ES version matrix, the Android ↔ NDC coordinate system flip, `GLSurfaceView` integration in an `InputMethodService` with correct `onResume`/`onPause` lifecycle wiring and EGL context preservation. Procedural bevel key mesh generation with VBO/IBO layout, instanced drawing for all keys in a single draw call, and a complete GLSL ES 3.0 shader pair implementing press-sinkage animation and Blinn-Phong lighting with analytical AO.

### Filament PBR (§09–§12)
Filament engine lifecycle in IME (Engine, Renderer, View, SwapChain, SurfaceHolder callbacks). Authoring `.mat` material files for plastic, metallic, and glass key surfaces and compiling them with `matc`. Loading and hot-swapping material instances per-key from Kotlin. SceneView + Compose bridge for a higher-level API. IBL environment lighting using `cmgen`-generated KTX files from HDR panoramas.

### Custom Rendering (§13–§16)
Hardware-accelerated Canvas pseudo-3D using `LinearGradient`, `BlurMaskFilter`, and `LAYER_TYPE_HARDWARE`. AGSL `RuntimeShader` / `ShaderBrush` in Jetpack Compose for animated noise, refraction, and neon effects (API 33+). `TextureView` overlay architecture for live 3D or video backgrounds beneath a Canvas key layer. Canvas ripple effect system and a full GPU compute shader particle system (OpenGL ES 3.1) with a 256-particle budget.

### Deep Personalization (§17–§22)
Custom layout XML schema with Kotlin parser — `KeyDef`, `KeyShape`, `ParticlePreset` data models. Non-rectangular key hit detection (hexagonal, circular, diamond, SVG free-form) with honeycomb layout positioning math. Fully typed `KeyboardTheme` Kotlin data class backed by JSON, with Material You integration. User-uploaded background pipeline (safe downscaling, RenderScript blur, GDPR-conscious storage). `SoundPool`-based sound pack engine with pitch randomization and `VibrationEffect` composition. Variable font loading with `Typeface.Builder.setFontVariationSettings()`.

### Game Engine Bridges (§23–§25)
Unity Android Library integration in an IME (with detailed caveats about the unsupported nature of the approach). Godot 4.x Android Library via `GodotFragment` with a `GDScript` signal bridge for key events. ARCore + Sceneform AR keyboard with horizontal plane detection and opt-in gating.

### Advanced (§26–§28)
TensorFlow Lite theme recommendation model with on-device-only inference and federated learning principles. Live wallpaper color extraction via `WallpaperManager` with `ACTION_WALLPAPER_CHANGED` broadcast listener. Performance budget framework: GPU frame time budgets, draw call limits, thermal throttle detection with `PowerManager.addThermalStatusListener`, and a quality degradation ladder (HIGH → MEDIUM → Canvas fallback).

### QA & Reference (§29–§31)
120-point visual QA checklist covering 3D correctness, shader quality, particle system integrity, theme engine, accessibility parity, and performance. Complete annotated open-source project index. Full bibliography with official documentation, rendering library docs, and tutorial references.

---

## Quick Start

```bash
# Clone the repo
git clone https://github.com/yourusername/android-keyboard-3d-personalization.git

# Open directly — no server needed
open android-keyboard-3d-personalization/Android_Keyboard_3D_Personalization_Guide.html
```

Or serve it:

```bash
python3 -m http.server 8000
# → http://localhost:8000/Android_Keyboard_3D_Personalization_Guide.html
```

---

## Reading Paths

| Role | Recommended Path |
|---|---|
| **Designer / Theme Author** | §17 Layout XML → §19 Theme Engine → §20 Backgrounds → §22 Fonts |
| **Android Engineer** | §04 OpenGL ES → §05 GLSurfaceView → §13 Canvas → §28 Performance |
| **3D / Graphics Engineer** | §07 GLSL Shaders → §09 Filament → §12 IBL → §25 AR Keyboard |
| **Game Developer** | §23 Unity → §24 Godot → §14 Compose Shaders |

---

## Rendering Pipelines Covered

| Pipeline | Sections | Capability |
|---|---|---|
| Android Canvas (hardware-accelerated) | §13 | Pseudo-3D, gradients, blur, layer compositing |
| OpenGL ES 2.0 / 3.x | §04–§08, §16 | Full 3D geometry, custom GLSL shaders, particles |
| Filament PBR | §09–§12 | Physically based materials, IBL, metallic/glass surfaces |
| AGSL (Android 13+) | §14 | Compose-native shaders: noise, distortion, animated effects |
| TextureView overlay | §15 | Live video / OpenGL background beneath Canvas keys |
| Unity Android Library | §23 | Full Unity scene as keyboard renderer |
| Godot Android Library | §24 | Godot scene embedded via GodotFragment |
| ARCore + Sceneform | §25 | Augmented reality floating keyboard |

---

## Code Examples

All code is **Kotlin** (IME layer) + **GLSL ES 3.0** (shaders) + **JSON** (theme and sound pack schemas). Highlights:

- `GLSurfaceView` in `InputMethodService` with `setPreserveEGLContextOnPause(true)` and correct lifecycle wiring
- Procedural bevel key mesh generator returning `FloatArray` with position + normal + UV
- Instanced draw call rendering all 40+ keyboard keys in a single `glDrawArraysInstanced`
- Complete GLSL ES 3.0 vertex + fragment shader pair with press sinkage animation and Blinn-Phong specular
- Filament `Engine` / `SwapChain` / `SurfaceHolder.Callback` lifecycle in a `Service` context
- `.mat` material files for plastic, chrome, and glass surfaces with `matc` compilation commands
- AGSL `RuntimeShader` with 2D value noise animated over time, gated behind API 33 check
- GPU compute shader (GLSL ES 3.1) for particle simulation with SSBO-based particle pool
- `KeyboardTheme` data class with `@Serializable` annotation, full JSON round-trip
- `BackgroundImagePipeline` with `inSampleSize` safe-downscaling and GDPR storage notes
- `ThermalAwareRenderer` with `PowerManager.addThermalStatusListener` quality degradation
- TFLite theme advisor with 7-feature input tensor and privacy-preserving design rationale

---

## Performance Contract

This guide enforces a strict GPU budget throughout:

| Metric | Budget | Test Device |
|---|---|---|
| GPU frame time (steady state) | ≤ 2ms | Snapdragon 480 |
| GPU frame time (thermal soak, 10 min) | ≤ 2ms | Snapdragon 480 |
| CPU GL thread time | ≤ 1ms | — |
| Draw calls per frame | ≤ 3 | Use instancing |
| Active particles | ≤ 256 | Compute shader |
| GPU memory | ≤ 32MB | Textures + buffers |
| Thermal response | Quality degradation, not crash | All devices |

Tested against: Snapdragon 480 (Nokia G60), MediaTek Helio G85 (Samsung Galaxy A32), Unisoc Tiger T618 (Lenovo Tab M10).

---

## Open Source Projects Referenced

| Project | License | Usage in this Guide |
|---|---|---|
| [FlorisBoard](https://github.com/florisboard/florisboard) | Apache 2.0 | Snygg theme engine architecture, Compose IME wiring |
| [AnySoftKeyboard](https://github.com/AnySoftKeyboard/AnySoftKeyboard) | Apache 2.0 | 3D theme pack, external APK theme loading pattern |
| [OpenBoard](https://github.com/openboard-team/openboard) | GPL 3.0 | LatinIME rendering pipeline reference |
| [kBoard (LiteKite/Android-IME)](https://github.com/LiteKite/Android-IME) | Apache 2.0 | MVVM IME architecture |
| [DonBrody/Android-CustomKeyboard](https://github.com/DonBrody/Android-CustomKeyboard) | MIT | Simplest possible custom keyboard starting point |
| [shutterscripter/Custom_Android_Keyboard](https://github.com/shutterscripter/Custom_Android_Keyboard) | MIT | Layout XML customization patterns |
| [Filament (Google)](https://github.com/google/filament) | Apache 2.0 | Full PBR renderer, materials, IBL |
| [SceneView](https://github.com/SceneView/sceneview-android) | Apache 2.0 | Filament + Compose bridge, AR keyboard |
| [Godot Android Library](https://docs.godotengine.org/en/stable/tutorials/platform/android/android_library.html) | MIT | Godot scene in IME |
| [TensorFlow Lite](https://www.tensorflow.org/lite) | Apache 2.0 | On-device theme recommendation model |

---

## External Resources Consulted

- [Android Custom Keyboard Layout — AndroidDvlpr](https://androiddvlpr.com/android-custom-keyboard-layout/)
- [Custom Android Keyboard Practical Example — CodezUp](https://codezup.com/custom-android-keyboard-practical-example/)
- [Custom Keyboard IMF Deep Dive — CodezUp](https://codezup.com/custom-android-keyboard-input-methods-framework/)
- [Custom Keyboard in Android Studio (Java) — CodezUp](https://codezup.com/custom-android-keyboard-android-studio-java/)
- [Control and Animate the Software Keyboard — Android Developers](https://developer.android.com/develop/ui/views/layout/sw-keyboard)
- [Handle Keyboard Input Style — Android Developers](https://developer.android.com/develop/ui/views/touch-and-input/keyboard-input/style)
- [Filament Design Documentation](https://google.github.io/filament/Filament.html)
- [GLSL ES 3.20 Specification — Khronos](https://registry.khronos.org/OpenGL/specs/es/3.2/)
- [Godot Android Library — Official Docs](https://docs.godotengine.org/en/stable/tutorials/platform/android/android_library.html)
- [Unity as an Android Library — Unity Docs](https://docs.unity3d.com/Manual/UnityasaLibrary-Android.html)

---

## Prerequisites

This guide assumes familiarity with:

- `InputMethodService`, `onCreateInputView()`, and the IME lifecycle (covered in [Vol. 1](../android-keyboard-design-guide/))
- Kotlin coroutines and basic Jetpack Compose
- Android View system: invalidate/draw cycle, hardware acceleration
- For §04–§12: basic familiarity with coordinate systems and matrix math (all math explained inline)

---

## Document Structure

```
Android_Keyboard_3D_Personalization_Guide.html
│
├── Sidebar navigation (sticky, 31 sections, 8 groups)
├── Cover page with metadata pills
├── Badge legend (Beginner / Intermediate / Expert)
│
├── §01–§03   Orientation & Architecture Overview
├── §04–§08   3D Foundations (OpenGL ES, Shaders, Lighting)
├── §09–§12   Filament PBR (Engine, Materials, IBL)
├── §13–§16   Custom Rendering (Canvas, AGSL, TextureView, Particles)
├── §17–§22   Deep Personalization (Layout, Themes, Sounds, Fonts)
├── §23–§25   Game Engine Bridges (Unity, Godot, AR)
├── §26–§28   Advanced (AI, Live Wallpaper, Performance)
└── §29–§31   QA Checklist, Project Index, References
```

---

## Printing

The guide includes a fully tuned `@media print` stylesheet targeting A4. Sidebar hidden, URLs inlined, page breaks before chapters.

```
File → Print → Save as PDF
Margins: 18mm top/bottom × 16mm sides
```

---

## Contributing

Issues and pull requests are welcome. When contributing code examples:

1. Test on at least one low-end device (Snapdragon 4xx series or equivalent)
2. Note the minimum API level required
3. Include a performance note if the technique has non-trivial GPU cost
4. GLSL shaders must compile on Qualcomm Adreno, ARM Mali, and PowerVR

---

## License

Released under the [MIT License](LICENSE). Fork, adapt, and redistribute freely with attribution.

---

## Series

This guide is **Volume 2** of two. For IME architecture, UX principles, accessibility, design tokens, and the full QA/release framework, see:

➜ **[Android Keyboard Design Guide 2026 — Complete Edition](../android-keyboard-design-guide/)**

---

*Made in Jurgistan · 2026 Extension Volume · 31 Sections · OpenGL ES · Filament PBR · AGSL · Particle Systems · Full Kotlin/GLSL Code*
