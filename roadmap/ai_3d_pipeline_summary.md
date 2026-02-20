# AI 3D Asset Generator — PoC Summary
> Open-source Meshy-like pipeline for indie game development

---

## 🧱 Core Concept

Build a **pipeline of specialized tools** (not one magic model) to generate game-ready 3D assets from text prompts — outputting FBX files with basic animations.

---

## 🛠️ The Stack

### Mesh Generation
| Tool | License | Best For | VRAM |
|---|---|---|---|
| **Hunyuan3D-2** (Tencent) | Open source | Best quality, text/image-to-3D, PBR textures | ~12GB |
| **TripoSR** (Stability AI) | MIT | Fast, hard surface props, image-to-3D | Low |
| **Shap-E** (OpenAI) | MIT | Fast placeholders, simple shapes | Low |

### Automation Layer
- **Blender** (headless via CLI + Python `bpy`) — import, clean, rig, animate, export FBX
- **Python agent** — orchestrates the full pipeline end to end

### For Stylized / Anime Characters
- **ComfyUI + Illustrious-XL or NovelAI** — anime-style texture generation
- **Mixamo** (Adobe, free) — auto-rigging + animation library (walk, attack, idle, etc.)
- **Rigify** (built into Blender) — manual rigging with more control

---

## 🔄 Pipeline Architecture

```
Text Prompt
    ↓
[AI Model: Hunyuan3D-2 / TripoSR]
    → raw mesh + textures (.GLB / .OBJ)
    ↓
[Blender headless]
    → import → decimate → fix normals
    → apply PBR or toon textures
    → add armature / rig
    → bake animations (idle, walk, attack)
    ↓
[Output: game-ready .FBX + texture maps]
```

### For Characters (Anime/Cartoon Style)
```
Text Prompt
    ↓
[Stable Diffusion - Illustrious-XL]
    → character sheet (front/back/side)
    ↓
[Base mesh: Mixamo template / chibi rig]
    → pre-rigged, clean topology
    ↓
[Hunyuan3D-2]
    → accessories / armor / weapons (separate)
    ↓
[Blender - texture baking + toon shader]
    ↓
[Mixamo auto-rig + animation library]
    ↓
[Export FBX]
```

---

## ⏱️ PoC Milestones

| Week | Goal |
|---|---|
| 1 | Get Hunyuan3D-2 generating GLBs locally, validate 10 prompts |
| 2 | Blender headless pipeline: import → decimate → export FBX |
| 3 | Add animation baking, test import in Unity/Godot |
| 4 | CLI or simple web UI, batch-generate first game asset set |

---

## 🎮 Asset Quality by Type

### Props & Environment
| Asset | Quality | Time |
|---|---|---|
| Props (barrels, crates, rocks) | ⭐⭐⭐⭐ Good | Minimal cleanup |
| Weapons / hard surface | ⭐⭐⭐ Decent | Some cleanup |
| Environment / dungeon pieces | ⭐⭐⭐ Decent | UV fixes |

### Characters (Cartoon / Anime Style)
| Character Type | Quality | Time per Asset |
|---|---|---|
| Chibi / SD characters | ⭐⭐⭐⭐ Very good | 20–40 min |
| Simple monsters (slime, golem) | ⭐⭐⭐⭐ Good | 15–30 min |
| Humanoid NPCs (anime proportions) | ⭐⭐⭐ Decent | 40–60 min + review |
| Detailed main characters | ⭐⭐ Rough | Manual polish needed |
| Facial animation / lip sync | ❌ Not viable yet | Manual work required |

---

## ✅ Why Cartoon / Japanese Style is the Smart Choice

- **Simplified anatomy** is expected (chibi proportions, big heads)
- **Flat/cel shading hides bad geometry** far better than PBR
- **Low poly is aesthetic**, not a flaw
- **Fewer facial features** = less to get wrong
- Cel shaders in Godot/Unity URP are free and transform mediocre geometry

---

## 🔑 Key Recommendations

1. Use **template meshes** for characters — don't generate humanoid geometry from scratch
2. Use **Stable Diffusion (anime models)** for textures and style, not PBR
3. Use **Mixamo** to skip manual rigging entirely
4. Apply a **cel/toon shader** in your engine — biggest single quality multiplier
5. Focus AI generation on **props, environment, and accessories** (always great results)
6. Reserve manual Blender time only for **main characters**

---

## 📦 Full Tool Reference

| Tool | Purpose | Cost |
|---|---|---|
| Hunyuan3D-2 | Text/image → 3D mesh + PBR textures | Free |
| TripoSR | Image → 3D mesh (fast) | Free (MIT) |
| Shap-E | Text → 3D (simple shapes) | Free (MIT) |
| Blender | Mesh cleanup, rigging, animation, FBX export | Free |
| ComfyUI | Stable Diffusion workflow orchestration | Free |
| Illustrious-XL | Anime-style image generation | Free |
| Mixamo | Auto-rigging + mocap animation library | Free |
| Rigify | Advanced rigging inside Blender | Free (built-in) |
| Rokoko Motion Library | Free mocap animation clips | Free |
| Godot / Unity URP | Toon shader rendering in-engine | Free |

---

## ⏳ Timeline Estimate

A team of **1–2 people** can build a functional asset pipeline in **4–6 weeks** that produces indie-game-quality cartoon characters and monsters consistently.

> **Bottom line:** For a cartoon or Japanese-style indie game, this pipeline is genuinely viable. The style choice is not a workaround — it's the smart technical decision given where open-source tools are today.
