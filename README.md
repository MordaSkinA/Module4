# Module 4 — Look Dev: Toon Shading

A stylized sunset scene built in Unity 6 (URP) to develop the visual language of a fantasy sandbox RPG. The look is a soft, melancholic anime-fantasy style, and the scene doubles as a visual test bench for the shader.

The scene was built from my own hand-drawn concept sketch 

| Sunset | Unity scene |
|---|---|
| ![Sunset animation](docs/sunset.gif) | ![Unity scene](docs/screenshot.png) |

## What's in it

- **Custom toon shader (`Toon_Base`)**, built in Shader Graph
  - Hard light/shadow split driven by the main directional light, with real-time shadows from other objects
  - Light tint: the light's color is blended into the base color at an adjustable strength
  - Rim light on silhouettes (Fresnel)
  - Per-material shadow color, so shadows are a dark version of the material instead of one shared grey-blue
- **Sunset lighting**: low warm directional light and a custom procedural skybox
- **Post-processing**: Bloom and Color Adjustments through a global Volume
- **Blockout scene**: water, a cliff island, a tree and a house, all made from simple primitives

## How the shader works

1. `MainLight` (a Custom Function node in `ToonLighting.hlsl`) reads the main light's direction, color and shadow attenuation through URP
2. The dot product of the surface normal and the light direction goes through a `Step`, giving a hard light/shadow mask. The `Step` edge is set to -0.3, so surfaces slightly turned away from the sun still count as lit
3. A `Lerp` picks between `ShadowColor` and the lit color using that mask
4. The lit color is `Lerp(Multiply(LightColor, BaseColor), BaseColor, LightTintStrength)`, which keeps the warm light from muddying cool colors
5. A Fresnel-based rim term (`RimColor` × `RimIntensity`, sharpened by `RimPower`) is added on top

## Material parameters

| Property | Type | What it does |
|---|---|---|
| `BaseColor` | Color | Lit color of the surface |
| `ShadowColor` | Color | Color in shadow |
| `LightTintStrength` | Float (0–1) | How strongly the light's color tints the base color |
| `RimColor` | Color | Color of the silhouette light |
| `RimPower` | Float | Higher values give a thinner rim |
| `RimIntensity` | Float | Strength of the rim light |

Large flat surfaces (water, grass) use a low or zero `RimIntensity`, because Fresnel lights up almost the whole plane when it is seen at a grazing angle

## Requirements

- Unity 6 (developed on 6.6)
- Universal Render Pipeline

## Known limitations

- Rim light is always visible around the whole silhouette. A version that appears only on the light-facing side is planned but not decided.
- The `Step` threshold is shared by all materials.


