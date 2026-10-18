<div align="center">

# PyOpengl FPS Demo

**A compact first-person graphics playground built with Python, OpenGL 3.3 and Dear ImGui.**

*Walk around. Move the sun. Watch the shadows change.*

![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=flat-square&logo=python&logoColor=white)
![OpenGL](https://img.shields.io/badge/OpenGL-3.3-5586A4?style=flat-square)
![GLSL](https://img.shields.io/badge/GLSL-330-505B78?style=flat-square)
![License](https://img.shields.io/badge/License-MIT-40A778?style=flat-square)
![Status](https://img.shields.io/badge/Status-Prototype-F0A34C?style=flat-square)

**[Features](#-features) · [Quick start](#-quick-start) · [Controls](#-controls) · [How it works](#-render-pipeline) · [License](#-license) · [Technical](#-implemented-methods)**

</div>

---

<div align="center">
  <img src="assets/preview.png" alt="FPS Arena gameplay: open test range, real-time directional lighting and ImGui controls" width="100%" />
  <p><sub>Actual in-engine screenshot · OpenGL 3.3 · Directional lighting · Real-time shadow mapping · ImGui controls</sub></p>
</div>

## ✦ Overview

**PyOpenGL FPS DEmo** is a small, single-file FPS test environment and rendering sandbox. This project is made for practise building 3d app using Opengl API with python bindings. Almost everything is deliberately simple: cubes, AABB collision, a free-look camera, Lambertian lighting, and classic depth-texture shadow mapping.

The point is to build on a real-time 3D foundation without introducing a full game engine, external 3D models, or separate GLSL files.

<table>
  <tr>
    <td width="50%" valign="top">
      <strong>🎮 First-person controller</strong><br/>
      Mouse-look camera, WASD movement, smoothed velocity, frame-time-aware updates, and solid collisions with wall sliding.
    </td>
    <td width="50%" valign="top">
      <strong>☀️ Directional light</strong><br/>
      Real-time azimuth, elevation, brightness, color, and ambient light adjustments using surface normals.
    </td>
  </tr>
  <tr>
    <td width="50%" valign="top">
      <strong>▧ Classic shadow mapping</strong><br/>
      Orthographic light camera, 2048² depth texture, depth bias, and 3×3 PCF filtering.
    </td>
    <td width="50%" valign="top">
      <strong>⚙️ Live ImGui settings</strong><br/>
      Tune controller speed and lighting without restarting. Press Q to release the cursor for UI interaction.
    </td>
  </tr>
</table>

## ✨ Features

| System | Implementation |
| :-- | :-- |
| Rendering | OpenGL 3.3 Core, GLSL vertex and fragment shaders |
| Geometry | Procedural cube mesh, model/view/projection matrices |
| Level | Open arena, perimeter walls, boxes |
| Camera | Mouse-controlled yaw/pitch, perspective projection |
| Movement | WASD, delta time, acceleration smoothing |
| Collision | Axis-aligned bounding boxes, substeps, X/Z sliding |
| Lighting | Surface-normal diffuse directional sunlight + ambient |
| Shadows | Depth framebuffer, orthographic projection, 3×3 PCF |
| Debug UI | Dear ImGui controls, FPS and player coordinates |

## 🚀 Quick start

**Requirements:** Python 3.10+, a graphics driver supporting **OpenGL 3.3 Core**, and an existing Python environment.

1. Open this folder in VS Code and select your **existing** Python interpreter.
2. Install packages into that same environment **only if missing**:

   ```bash
   python -m pip install -r requirements.txt
   ```

   This selects **ImGui Bundle**. If your environment already uses **pyimgui** instead, use `requirements-pyimgui.txt` rather than installing both:

   ```bash
   python -m pip install -r requirements-pyimgui.txt
   ```

   If all dependencies are already installed, **skip installation altogether**.

3. Start the prototype:

   ```bash
   python main.py
   ```

> [!NOTE]
> The project does not create or modify a virtual environment. The install commands above use whichever Python interpreter your terminal currently points to. Use `python -m pip --version` if you need to check that interpreter before installing.

## 🎮 Controls

| Input | Action |
| :-- | :-- |
| `W` / `A` / `S` / `D` | Move forward / left / backward / right |
| `Mouse` | Look around when captured |
| `Q` | Toggle captured/free cursor (one action per press) |
| `Esc` | Close application |

**To adjust the settings:** press **Q**, move the free cursor over the ImGui panel, change the values, then press **Q** again to resume first-person mouse-look.

## 🧱 Scene layout

```text
             NORTH / TARGET RANGE (-Z)
             X: -30        0         +30
    +-----------------------------------------+
-27 |              T  ###T###  T              |
-24 |     ###        MM     MM          MMM   |
-21 |     MMM        MM     MM      #####     |
-18 |     ###  MMM   =   =   =   MMM          |
-15 |          MMM       T       MMM          |
-12 |           ######                        |
 -9 |      CC          #####         CCCCC    |
 -6 |      CC                        CCCCC    |
 -3 |        CCC                 ####         |
 +0 |                                         |
 +3 |       :     :      :      :     :       |
 +6 |       :     :      :      :     :       |
 +9 |       :     :      :      :     :       |
+12 |       : ####:      :      :#####:       |
+15 |                                         |
+18 |    CCC             ^                    |
+21 |    CCC                          CCC     |
+24 |             ###         ###     CCC     |
+27 |                                         |
    +-----------------------------------------+
              SOUTH / SPAWN (+Z)
```

The diagram is schematic rather than to scale. Geometry and positions are defined in `make_arena()` inside `main.py`.

## 📚 Implemented Methods

This project implements established computer graphics and real-time rendering techniques using Python, OpenGL 3.3, and GLSL.

### Research-Based Implementations

| Technique | Research Paper | Implementation |
|---|---|---|
| **Shadow Mapping** | [Lance Williams — *Casting Curved Shadows on Curved Surfaces* (1978)](https://doi.org/10.1145/965139.807402) | Real-time directional shadow mapping using a 2048×2048 depth texture |
| **Percentage-Closer Filtering (PCF)** | [Reeves et al. — *Rendering Antialiased Shadows with Depth Maps* (1987)](https://graphics.pixar.com/library/ShadowMaps/paper.pdf) | Simplified 3×3 PCF with nine depth comparisons per fragment |

### Additional Implemented Methods

| Technique | Description |
|---|---|
| **Lambertian Diffuse Lighting** | Surface-normal-based directional lighting |
| **AABB Collision Detection** | Axis-aligned collision detection with wall sliding |
| **Perspective & Orthographic Projection** | Camera and directional light projection matrices |
| **Frame-Rate-Independent Smoothing** | Exponential velocity interpolation |
| **Exponential-Squared Fog** | Distance-based atmospheric color blending |
| **Depth Bias & Polygon Offset** | Reduction of shadow mapping artifacts |

> **Note:** These implementations are simplified adaptations of established techniques, not complete reproductions of the original research papers.

For mathematical explanations, paper references, and source-code details, see **[Research & Technical Implementation](docs/research.md)**.

## 🗂️ Repository structure

```text
pyopengl-fps-arena/
├── main.py                    # Complete FPS arena + GLSL shaders
├── README.md                  # Project guide and showcase
├── LICENSE                    # MIT license for this project
├── THIRD_PARTY_NOTICES.md     # Dependency license references
├── requirements-core.txt     # NumPy + GLFW + PyOpenGL
├── requirements.txt          # Default: core + ImGui Bundle
├── requirements-pyimgui.txt  # Alternative: core + pyimgui
├── .gitignore
├── assets/
│   └── preview.png           # Gameplay screenshot
├── tests/
│   └── test_engine.py        # Non-GPU math/collision checks
└── .github/workflows/
    └── ci.yml                 # Syntax + unit tests
```

## 🛠️ Customize

| Want to change... | Edit... |
| :-- | :-- |
| Arena objects and layout | `make_arena()` |
| Spawn position | `FPSPlayer.__init__()` |
| Mouse sensitivity | `MOUSE_SENSITIVITY` |
| Shadow texture resolution | `SHADOW_SIZE` |
| GLSL shading and fog | `SCENE_VS` / `SCENE_FS` |
| GUI controls | `ArenaApp.draw_imgui()` |

## ✅ Tests

Run the simple CPU-side geometry and collision tests after the dependencies are available:

```bash
python -m unittest discover -s tests -v
```

The repository's automated checks **do not test GPU rendering**. A real window and OpenGL 3.3 driver are required to validate the live visuals, ImGui integration, and shadow appearance.

## 📜 License

The project source is released under the **MIT License** — see [LICENSE](LICENSE).

Dependencies are installed separately, are **not vendored into this repository**, and keep their own licenses. See [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md) for credits and license links.

---

<div align="center">

<sub>Made for experimenting with Python, GLSL, rasterization and real-time graphics.</sub>

</div>
