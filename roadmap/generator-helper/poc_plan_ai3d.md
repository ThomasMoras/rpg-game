# PoC Plan — AI 3D Asset Generator for Indie Games
**Role:** Senior ML Engineer · **Style:** Cartoon / Japanese · **Format:** Plan & Steps Only

> **Philosophy:** Prove value fast, stay modular, avoid over-engineering. A PoC is a learning tool — not a product.

---

## 1. Goals & Success Criteria

### What this PoC must answer
- Can we generate a game-ready FBX asset from a text prompt with no manual steps?
- Is the output quality acceptable for a cartoon/chibi stylized indie game?
- Can we swap the generation model without touching pipeline logic?
- Can we batch-generate assets overnight on a single local GPU?

### Definition of Done (PoC)
- At least 70% of generated props are usable without manual fix
- At least 50% of cartoon characters are usable with only toon shader applied
- One model swap demonstrated in under 5 minutes (config change only)
- Batch of 20 assets runs unattended

### Explicit Non-Goals (PoC phase)
- No web UI
- No cloud infrastructure
- No automated mesh quality scoring
- No facial animation / lip sync
- No multiplayer or collaborative features

---

## 2. High-Level Architecture (conceptual)

The pipeline is a **linear sequence of independent stages**. Each stage reads from disk and writes to disk — making every stage independently testable and replaceable.

```
[Text Prompt]
      ↓
[Stage 1 — Image Generation]
   text → 2D character sheet / reference image (PNG)
      ↓
[Stage 2 — Mesh Generation]
   image → raw 3D mesh + textures (GLB)
      ↓
[Stage 3 — Blender Processing]
   GLB → clean mesh → rig → animation → export (FBX)
      ↓
[Output — game-ready .fbx + texture maps]
```

**Key design rule:** stages communicate via files on disk. No shared state. No direct function calls between stages. This makes each stage independently runnable during development and debugging.

**Backend registry:** a central registry maps a name (`"hunyuan3d"`, `"triposr"`) to an implementation. Switching model = one line in a YAML config.

---

## 3. Tech Stack Decisions

| Layer | Chosen Tool | Why |
|---|---|---|
| **CLI** | Typer | FastAPI-style, clean, widely used in ML tooling |
| **Config & validation** | Pydantic v2 + YAML | Type-safe, industry standard, no surprises |
| **Logging** | Structlog | Structured JSON logs, easy to grep and extend later |
| **Package manager** | uv | Fast, modern, replaces pip + virtualenv in one tool |
| **Testing** | Pytest + pytest-mock | Industry default, backends always mocked in unit tests |
| **Mesh generation** | Hunyuan3D-2 (default) | Best open-source quality today, swappable |
| **Image generation** | diffusers (HuggingFace) | Standard in industry, supports many models |
| **3D processing** | Blender 4.x headless | Only mature free tool for the full processing pipeline |
| **Auto-rigging** | Mixamo (free, web API) | Saves weeks of manual rigging work |
| **Task queue** (future) | Celery + Redis | Don't add yet — design for it, introduce in Phase 2 |

> **Deliberately excluded:** LangChain (overkill for a sequential pipeline), FastAPI (no API needed at PoC), any database (flat files are enough), Docker (local GPU first, containerize in Phase 2).

---

## 4. Project Structure (planned)

```
ai3d-poc/
│
├── config/
│   ├── default.yaml          # active pipeline configuration
│   └── models.yaml           # catalogue of all registered backends
│
├── src/
│   └── ai3d/
│       ├── pipeline.py       # orchestrates stages in sequence
│       ├── stages/           # one file per pipeline stage
│       │   ├── base.py       # abstract Stage interface (contract)
│       │   ├── image_gen.py
│       │   ├── mesh_gen.py
│       │   └── blender.py
│       ├── backends/         # one file per model integration
│       │   ├── registry.py   # factory + swap mechanism
│       │   ├── hunyuan3d.py
│       │   ├── triposr.py
│       │   └── shap_e.py
│       └── utils/
│           ├── fs.py         # file/path helpers
│           └── logger.py     # logging setup
│
├── scripts/
│   ├── blender_process.py    # runs inside Blender headless context
│   └── batch_generate.py    # reads a prompt list, runs pipeline N times
│
├── tests/
│   ├── test_pipeline.py      # pipeline logic (all stages mocked)
│   ├── test_backends.py      # backend contract tests
│   └── fixtures/             # sample GLB/PNG for offline tests
│
├── outputs/                  # all generated assets land here
├── prompts/
│   └── asset_list.txt        # batch input: one prompt per line
├── pyproject.toml
├── .env.example
└── README.md
```

---

## 5. Implementation Steps (in order)

### ✅ Step 1 — Environment & Project Bootstrap
*Goal: anyone can clone and run in under 10 minutes*

- Install `uv`, create virtual environment, set up `pyproject.toml`
- Set up folder structure (empty files, `__init__.py` placeholders)
- Set up `structlog` logger utility
- Set up Pydantic config schema + `default.yaml`
- Write a smoke test that imports the package without error
- **Checkpoint:** `uv run pytest` passes on zero real code (structure only)

---

### ✅ Step 2 — Abstract Stage Interface + Pipeline Orchestrator
*Goal: define the contract before any real implementation*

- Define `Stage` abstract base class with `run(input) → output` signature
- Define `StageInput` and `StageOutput` dataclasses
- Implement `Pipeline` orchestrator that chains stages and stops on failure
- Write unit tests with **fully mocked stages** (no GPU, no models needed)
- **Checkpoint:** pipeline test passes with two mock stages chained together

---

### ✅ Step 3 — Backend Registry
*Goal: make model swapping a config change, not a code change*

- Implement `@register("name")` decorator pattern
- Implement `get_backend(name)` factory function
- Define `MeshBackend` and `ImageBackend` Protocol interfaces (type safety)
- Register placeholder backends that return fixture files (for offline testing)
- **Checkpoint:** swapping `mesh_backend: hunyuan3d` → `mesh_backend: triposr` in YAML and running the test suite requires zero code changes

---

### ✅ Step 4 — Blender Headless Stage
*Goal: prove Blender automation works before integrating real models*

- Write and test the `blender_process.py` script manually with a sample GLB from the internet
- Script must: import GLB → decimate mesh → add root armature → bake idle animation → export FBX
- Wrap the Blender subprocess call in the `BlenderStage` class
- Test with the fixture GLB file (no real generation needed yet)
- **Checkpoint:** given a GLB file on disk, `BlenderStage.run()` produces a valid FBX

---

### ✅ Step 5 — First Real Backend: Hunyuan3D-2
*Goal: first real model integration*

- Install Hunyuan3D-2 dependencies (separate requirements, lazy imports)
- Implement `Hunyuan3DBackend` class registered as `"hunyuan3d"`
- Test with 5 simple prompts manually, evaluate GLB output quality
- Log generation time and VRAM usage
- **Checkpoint:** `MeshGenStage` with `hunyuan3d` backend produces a valid GLB from an image input

---

### ✅ Step 6 — Image Generation Stage
*Goal: complete the text → image → mesh chain*

- Implement `ImageGenStage` using `diffusers` (SDXL or Illustrious-XL for anime style)
- Use a character sheet prompt template (front/side/back view)
- Connect to `MeshGenStage` via the file-on-disk convention
- Test full chain: text → PNG → GLB
- **Checkpoint:** one prompt runs end-to-end without manual steps, producing a GLB

---

### ✅ Step 7 — First Full End-to-End Run
*Goal: text → FBX in one command*

- Wire all stages into the pipeline: `ImageGen → MeshGen → Blender`
- Implement the Typer CLI: `ai3d generate "a chibi orc warrior"`
- Run on 10 diverse prompts (mix of props and characters)
- Manually review outputs in Blender and Godot
- Document what worked and what failed (keep a rejection log)
- **Checkpoint:** CLI works, at least 5/10 outputs are usable as-is

---

### ✅ Step 8 — Second Backend: TripoSR
*Goal: validate the swap mechanism works in practice*

- Implement `TripoSRBackend` class, register as `"triposr"`
- Run the same 10 prompts with `--backend triposr`
- Compare quality and generation speed vs Hunyuan3D-2
- **Checkpoint:** switching backends requires only a CLI flag or one YAML line change — confirmed working

---

### ✅ Step 9 — Batch Generation Script
*Goal: prove unattended overnight runs work*

- Implement `batch_generate.py`: reads `prompts/asset_list.txt`, runs pipeline per line
- Add basic retry (max 2 attempts per asset on failure)
- Add summary report at end: X succeeded, Y failed, total time
- Test with 20 prompts
- **Checkpoint:** 20 assets generated unattended, summary report produced

---

### ✅ Step 10 — Validate in Game Engine
*Goal: make sure outputs actually work in Godot*

- Import a batch of FBX outputs into Godot 4
- Apply built-in toon/cel shader to all assets
- Verify animations play correctly (idle, at minimum)
- Note any import issues (scale, axis, missing textures)
- **Checkpoint:** at least 70% of props and 50% of cartoon characters import and render correctly with toon shader

---

### ✅ Step 11 — PoC Review & Go/No-Go Decision
*Goal: produce a clear recommendation for next phase*

- Compile quality metrics from rejection log + engine validation
- Estimate cost per asset (time + hardware)
- Identify top 3 bottlenecks
- Write a short decision document: continue → Phase 2, pivot, or stop
- **Checkpoint:** team aligned on next step

---

## 6. Milestones & Timeline

```
Week 1:  Steps 1–3   → skeleton, contracts, registry (no GPU needed)
Week 2:  Steps 4–5   → Blender pipeline + first real model
Week 3:  Steps 6–7   → full end-to-end, first real outputs
Week 4:  Steps 8–11  → backend swap, batch run, engine validation, review
```

**Team size:** 1–2 engineers. Steps 1–3 can run in parallel if 2 engineers.

---

## 7. Future Steps (Post-PoC Roadmap)

### Phase 2 — Quality & Reliability
- Automated mesh QA using `trimesh` (watertight check, poly count, UV coverage)
- Retry with modified prompt if QA score is below threshold
- Prompt template library (tested patterns per asset category)
- Mixamo auto-rig API integration to automate character rigging

### Phase 3 — Scale & API
- Wrap pipeline in Celery async tasks with Redis broker
- Expose a FastAPI REST endpoint: `POST /generate` → returns job ID
- Move heavy generation to RunPod or Modal (on-demand GPU, pay-per-use)
- Add asset storage: local S3-compatible (MinIO) + metadata JSON per asset

### Phase 4 — Game Engine Integration
- Godot editor plugin: drop a prompt → asset auto-imported with toon shader
- Asset versioning with DVC or simple S3 + metadata
- Fine-tune a lighter model on your accepted outputs once you have 100+ samples

---

## 8. Key Risks & Mitigations

| Risk | Likelihood | Mitigation |
|---|---|---|
| Raw character geometry too broken to use | High | Use template base meshes + SD textures; generate accessories separately |
| Blender headless script breaks on version update | Medium | Pin Blender to 4.1 in README; add version check at startup |
| VRAM insufficient locally | Medium | Fallback to TripoSR; or use RunPod for heavy runs |
| Mixamo auto-rig fails on AI-generated meshes | Medium | Pre-process to T-pose before upload; document known failures |
| Generation too slow for interactive use | Low (PoC) | Acceptable at PoC; async queue solves in Phase 3 |
| Prompt quality inconsistent | High | Build prompt template library in Week 1; maintain rejection log from day 1 |

---

## 9. Proposals & Recommendations

**Start with props, not characters.**
Barrels, crates, rocks, weapons — these work well today. Build confidence in the pipeline with easy wins before tackling characters.

**Run manual prompt testing before writing code.**
Spend 2–3 hours in Hunyuan3D-2's web demo. Find which prompt patterns produce clean outputs for your art style. Encode those as YAML templates. This saves more time than any code optimization.

**Keep a rejection log from day 1.**
Every time you discard an output, note the prompt and the reason (bad topology, wrong proportions, broken UVs, etc.). After 20–30 entries, patterns will surface that directly inform your QA rules and prompt templates.

**Import into Godot after every milestone.**
Don't wait until the end. Seeing results in the real engine gives faster and more honest quality feedback than reviewing in Blender. Apply the toon shader immediately — it makes outputs look meaningfully better.

**Separate the two sub-pipelines conceptually.**
Props pipeline: `text → mesh → FBX`. Character pipeline: `text → SD image → template mesh → texture bake → rig → FBX`. These are different enough that treating them as one pipeline will cause friction. Keep them as two named pipeline configs.

**Don't add a database or UI until Phase 2.**
Flat files in `/outputs` with structured folder names (`outputs/2024-02-19_chibi-orc/`) are perfectly sufficient for a PoC. Adding more infrastructure now slows you down without adding PoC value.

---

*PoC target: functional batch pipeline on local GPU · 4 weeks · 1–2 engineers · go/no-go decision at end of Week 4*
