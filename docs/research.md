# Research & Technical Implementation

This document describes the rendering algorithms, mathematical foundations, and implementation decisions used in **PyOpenGL FPS Demo**.

The project is a small real-time 3D rendering prototype using Python, OpenGL 3.3, GLSL, GLFW, and Dear ImGui.

The implementations are simplified adaptations of established computer graphics techniques, not full reproductions of the cited research papers.

---

## 1. Shadow Mapping

**Reference:** Lance Williams, *Casting Curved Shadows on Curved Surfaces*, SIGGRAPH 1978.

[Research paper](https://doi.org/10.1145/965139.807402)

### Principle

Shadow mapping determines whether a visible surface point is occluded from a light source.

The implementation uses two rendering passes:

1. **Shadow pass:** Render scene geometry from the directional light's viewpoint into a depth texture.
2. **Camera pass:** Transform visible surface points into light space and compare their depths against the stored shadow map.

A fragment is shadowed when its light-space depth exceeds the stored depth, accounting for a small bias.

### Implementation

- Orthographic light projection
- 2048 × 2048 shadow map
- 24-bit depth texture (`GL_DEPTH_COMPONENT24`)
- Depth-only framebuffer
- Shadow bias and polygon offset
- Real-time updates when the directional light changes

**Source code:**

- [`light_space_matrix()`](../main.py#L491-L497)
- [`render_depth()`](../main.py#L534-L552)
- [`calculateShadow()`](../main.py#L117-L136)

---

## 2. Percentage-Closer Filtering (PCF)

**Reference:** William T. Reeves, David H. Salesin, Robert L. Cook, *Rendering Antialiased Shadows with Depth Maps*, SIGGRAPH 1987.

[Research paper (PDF)](https://graphics.pixar.com/library/ShadowMaps/paper.pdf)

### Principle

A single shadow-map depth comparison produces hard, potentially aliased shadow boundaries.

Percentage-Closer Filtering reduces this aliasing by performing multiple depth comparisons at neighboring shadow-map coordinates and averaging the results.

This project implements a simplified **3 × 3 PCF filter** with nine comparisons per fragment.

The approximate shadow factor is:

$$
S(p)=\frac{1}{9}\sum_{i=-1}^{1}\sum_{j=-1}^{1}V(p+\Delta_{ij})
$$

Where:

- `S(p)` is the fraction of samples classified as shadowed.
- `V` returns 1 when the depth comparison indicates occlusion, otherwise 0.
- `Δ` represents neighboring texel offsets.

The resulting shadow factor attenuates the directional diffuse-light contribution.

**Source code:** [`SCENE_FS — calculateShadow()`](../main.py#L117-L136)

**Limitation:** This is a fixed-size PCF kernel, not a complete reproduction of all filtering approaches discussed in the original paper.

---

## 3. Lambertian Diffuse Lighting

The renderer uses a basic Lambertian diffuse model with a directional light.

$$
L_d = \max(0,\mathbf{N}\cdot\mathbf{L})
$$

Where:

- **N** is the normalized surface normal.
- **L** is the normalized direction toward the light.

The project combines this with a constant ambient term and the shadow factor:

$$
C = C_{\mathrm{base}}\left(A+(1-S)L_d I C_{\mathrm{light}}\right)
$$

Here, `A` represents ambient illumination and `I` represents light intensity.

This is a simplified real-time lighting model, not a complete physically based BRDF implementation.

**Source code:** [`SCENE_FS — lighting`](../main.py#L138-L146)

---

## 4. FPS Camera and Projection

The first-person camera uses:

- Yaw and pitch for orientation
- A perspective projection matrix
- A look-at view matrix
- Mouse-relative camera rotation
- Delta-time-based movement

**Source code:**

- [`perspective()`](../main.py#L194-L204)
- [`look_at()`](../main.py#L217-L228)
- [`FPSPlayer`](../main.py#L336-L394)

---

## 5. AABB Collision Detection

The player uses simplified axis-aligned bounding-box collision detection against solid scene objects.

Collision resolution is performed separately along the X and Z axes, permitting sliding along walls.

Movement is also divided into substeps to reduce the possibility of passing through thin obstacles at higher movement speeds.

The player currently moves on a fixed ground plane. Gravity, jumping, slopes, and general rigid-body dynamics are not implemented.

**Source code:** [`FPSPlayer.collides()` and `FPSPlayer.move()`](../main.py#L355-L394)

---

## 6. Frame-Rate-Independent Motion Smoothing

The FPS controller uses exponential interpolation for movement velocity.

The interpolation factor is:

$$
\alpha = 1-e^{-k\Delta t}
$$

The current implementation uses `k = 15`.

This produces smooth acceleration and deceleration while accounting for variable frame durations.

**Source code:** [`FPSPlayer.move()`](../main.py#L367-L394)

---

## 7. Exponential-Squared Distance Fog

A lightweight atmospheric fog approximation is used to create a sense of distance.

$$
f(d)=1-e^{-kd^2}
$$

Where `d` is the distance from the camera to the fragment and `k` controls fog density.

The fog factor is clamped and used to blend the surface color toward a constant sky color.

**Source code:** [`SCENE_FS — distance fog`](../main.py#L147-L152)

---

## 8. Limitations and Future Research

The current implementation does **not** include:

- Global illumination or indirect-light simulation
- Screen-space ambient occlusion (SSAO)
- Ray tracing or path tracing
- Physically based materials
- Dynamic rigid-body simulation
- Cascaded shadow maps
- Temporal anti-aliasing

Potential future improvements include shadow filtering, more advanced geometry and collision handling, and physically motivated indirect illumination.

---

## References

1. Williams, L. (1978). *Casting Curved Shadows on Curved Surfaces*. SIGGRAPH. https://doi.org/10.1145/965139.807402
2. Reeves, W. T., Salesin, D. H., & Cook, R. L. (1987). *Rendering Antialiased Shadows with Depth Maps*. SIGGRAPH. https://graphics.pixar.com/library/ShadowMaps/paper.pdf

**Project source:** [PyOpengl-FPS-Demo](https://github.com/OrhanPolatHzl/PyOpengl-FPS-Demo)
