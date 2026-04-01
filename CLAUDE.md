# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

ComfyUI-llama-cpp_vlm is a ComfyUI custom node pack that runs LLM/VLM models natively via llama-cpp-python (JamePeng's fork with CUDA/Metal support). It provides nodes for model loading, text/vision inference, bounding box detection, and prompt enhancement within ComfyUI workflows.

## Setup

```bash
pip install -r requirements.txt
```

The `llama-cpp-python` dependency uses platform-specific pre-built wheels (CUDA 12.8 on Windows/Linux, Metal on macOS) from JamePeng's fork. Models (.gguf) are resolved via `update_folder_names_and_paths("llm_vl", ["text_encoders"])` which looks up `extra_model_paths.yaml` の `text_encoders` key for model directories.

## Architecture

**Single-file node system**: All ComfyUI nodes are defined in `nodes.py` and exported via `__init__.py`. There is no test suite.

### Key Components in `nodes.py`

- **`LLAMA_CPP_STORAGE`** (class-level singleton): Holds the loaded `Llama` model, chat handler, conversation history (`messages`), and system prompts. Only one model is loaded at a time; loading a new model calls `clean()` first. It monkey-patches `mm.unload_all_models` to auto-cleanup on ComfyUI's model unload.

- **Chat handler dispatch**: VLM support uses handler classes from `llama_cpp.llama_chat_format`. Each handler is imported with try/except for graceful degradation when the installed llama-cpp-python version lacks support. The `chat_handlers` list and `get_chat_handler()` match-case must be updated together when adding new model types.

- **Node classes** (12 nodes total):
  - `llama_cpp_model_loader` — loads GGUF model + optional mmproj clip, configures GPU layers/VRAM limit
  - `llama_cpp_instruct_adv` — main inference node with multi-turn chat, image input, thinking mode, preset prompts
  - `llama_cpp_parameters` — sampling parameters (temperature, top_p, top_k, stop_words, etc.). `stop_words` はカンマ区切り文字列で入力し、`process()` 内で `stop` リストに変換して `create_chat_completion()` に渡される
  - `llama_cpp_clean_states` / `llama_cpp_unload_model` — state/model management
  - `json_to_bbox`, `bbox_to_segs`, `bbox_to_mask`, `bboxes_to_bbox` — bounding box pipeline for vision detection results
  - `parse_json_node`, `remove_code_block` — output parsing utilities
  - `PromptEnhancerPreset` — manages prompt enhancement presets from `support/prompt_enhancer_preset.py`

### Support Modules (`support/`)

- `gguf_layers.py` — reads GGUF file metadata to extract layer count (used for VRAM-limited GPU layer calculation)
- `cqdm.py` — dual progress bar wrapper (ComfyUI ProgressBar + tqdm for terminal)
- `prompt_enhancer_preset.py` — large dictionary of model-specific prompt enhancement system prompts (wildcard-imported into nodes.py)

## Key Patterns

- Models are registered under the `"llm_vl"` key via `update_folder_names_and_paths()`, which resolves paths from ComfyUI's `text_encoders` folder config (supports `extra_model_paths.yaml`). Model paths are resolved with `folder_paths.get_full_path("llm_vl", model)`
- Image inputs are converted to base64 JPEG for the llama-cpp chat API
- VRAM limit mode estimates GPU layers from `(model_file_size * 1.55) / layer_count`
- Conversation state is keyed by a user-provided `unique_id` for multi-turn chat
- `@` in preset prompts is dynamically replaced with "image" or "video" based on input type
