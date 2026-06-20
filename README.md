![LLMWS_alman-os_local_workspace](https://github.com/alman-os/llm_local_workspace/blob/main/LLWS_image_2.jpeg)

# LLM Local Workspace (LLWS)

LLM Local Workspace is a beginner-friendly folder for running your first local GGUF model with `llama.cpp` on macOS Apple Silicon.

This repo does not include model files. You bring your own `.gguf` download, put it in this folder, start `llama-server`, and chat with the model in your browser at `http://127.0.0.1:8080`.

## Table of Contents

- [What Does This Do](#what-does-this-do)
- [Before You Start](#before-you-start)
- [Installation](#installation)
- [Choose a Model](#choose-a-model)
- [Usage](#usage)
- [What Can You Do With This](#what-can-you-do-with-this)
- [Troubleshooting](#troubleshooting)
- [Privacy](#privacy)
- [Tech Details](#tech-details)
- [Contributing](#contributing)
- [Useful Links](#useful-links)

## What Does This Do

- Gives you a clean local workspace for GGUF models.
- Uses `llama.cpp`, a lightweight local inference engine.
- Starts a local web UI through `llama-server`.
- Keeps large model files out of Git.
- Targets macOS Apple Silicon first.
- Teaches the smallest useful workflow before adding optional pieces.

## Before You Start

Local models are useful, but they are not magic.

**Smarter cloud models are usually more convenient.** They are also not private in the same way a local model can be.

**Local models are resource intensive.** On a mid-tier Mac, close heavy apps before starting a model. If your Mac slows down, use a smaller model or a lower context size.

**Do not treat model output as guaranteed truth.** Local LLMs can summarize, rewrite, draft, classify, and transform text. They can also invent confident wrong answers, especially on exact facts, decisions, and step-by-step technical instructions.

## Installation

Install Homebrew if you do not already have it:

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

Install `llama.cpp`:

```bash
brew install llama.cpp
```

Check that `llama-server` is available:

```bash
llama-server --version
```

You do not need Python for the basic local server workflow. Install `llama-cpp-python` only if you plan to write Python scripts that call models directly:

```bash
python3 -m pip install llama-cpp-python
```

## Choose a Model

Start smaller than you think. The first win is getting a model to answer once.

| Computer | Good first target |
| --- | --- |
| Low tier | 2B to 3B, Q4 |
| Mid tier | 4B to 8B, Q4 |
| High tier | 9B to 14B, Q4 |
| Research tier | 27B+ or larger, only if you know your RAM budget |

For a first download:

1. Open the Hugging Face GGUF model browser: <https://huggingface.co/models?library=gguf>
2. Pick a model repo that provides `.gguf` files.
3. Open the repo's **Files and versions** tab.
4. Download one `Q4_K_M.gguf` file if available.
5. Put the file in this repo folder.

If the repo includes a matching `mmproj` file, download it only when you are using a multimodal model or image input. Text-only models usually do not need it.

Your folder should look roughly like this:

```text
llm_local_workspace/
  README.md
  your-downloaded-ggufs-go-here.md
  your-model.Q4_K_M.gguf
  mmproj-your-model.gguf
```

The exact model filename does not matter. The command you run must match the file you downloaded.

## Usage

Go to the repo folder:

```bash
cd /path/to/llm_local_workspace
```

Start a text-only model:

```bash
llama-server -m ./your-model.Q4_K_M.gguf --alias local --host 127.0.0.1 --port 8080 -c 4096
```

Open the local web UI:

```text
http://127.0.0.1:8080
```

Try a short first prompt:

```text
Say hello in one sentence. Then tell me what model file you are running from.
```

If you downloaded a matching multimodal projector, include it:

```bash
llama-server -m ./your-model.Q4_K_M.gguf --mmproj ./mmproj-your-model.gguf --alias local --host 127.0.0.1 --port 8080 -c 4096
```

If you prefer an API check, keep the server running and use another terminal:

```bash
curl http://127.0.0.1:8080/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model":"local","messages":[{"role":"user","content":"Say hello in one sentence."}],"max_tokens":64}'
```

Stop the server with `Control-C` in the terminal where it is running.

## What Can You Do With This

- Run a local writing and rewriting assistant.
- Summarize private notes without sending them to a hosted chatbot.
- Test small models before building an app around them.
- Learn the difference between model files, quantization, context, and local serving.
- Use an OpenAI/Claude-compatible local endpoint for experiments.
- Build confidence before trying larger tools like UI wrappers, agents, or custom Python scripts.

## Troubleshooting

**`brew: command not found`**

Install Homebrew first, then reopen Terminal.

**`llama-server: command not found`**

Run `brew install llama.cpp`. If it still fails, reopen Terminal and run `brew doctor`.

**The model will not load**

Check the filename and path. If the download was interrupted, delete the partial file and download it again.

**You downloaded `.safetensors`, `.bin`, or `.pt` files**

Those are not the beginner path for this repo. Download a `.gguf` file instead.

**Your Mac slows down or runs out of memory**

Use a smaller model, close other apps, or lower the context size:

```bash
llama-server -m ./your-model.Q4_K_M.gguf --alias local --host 127.0.0.1 --port 8080 -c 2048
```

**Port 8080 is already in use**

Use another port:

```bash
llama-server -m ./your-model.Q4_K_M.gguf --alias local --host 127.0.0.1 --port 8081 -c 4096
```

Then open `http://127.0.0.1:8081`.

**`mmproj` errors**

Remove `--mmproj` for text-only models. For multimodal models, make sure the projector file came from the same model repo or matching release.

**The model gives wrong answers**

That is normal. Use local LLMs for drafts, transformations, and experiments. Verify facts before relying on them.

## Privacy

When you run `llama-server` on `127.0.0.1`, the server listens on your own machine. Your prompts are processed locally by the model file you downloaded.

Privacy still depends on your behavior. Do not paste sensitive text into hosted websites, remote notebooks, or third-party tools unless you intend to send that data out.

## Tech Details

**Engine:** `llama.cpp`

**Model format:** GGUF

**Server command:** `llama-server`

**Default local URL:** `http://127.0.0.1:8080`

**Primary target:** macOS Apple Silicon

**Model storage:** local files in this repo folder or a folder you choose

**Git policy:** model binaries should be ignored; docs and placeholders should be committed

## Contributing

Beginner improvements are welcome.

Useful contributions include:

- clearer setup steps
- screenshots of the Hugging Face download flow
- tested hardware notes
- common error messages and fixes
- safer default commands

## Useful Links

- llama.cpp: <https://github.com/ggml-org/llama.cpp>
- llama-server docs: <https://github.com/ggml-org/llama.cpp/tree/master/tools/server>
- Homebrew llama.cpp formula: <https://formulae.brew.sh/formula/llama.cpp>
- Hugging Face GGUF docs: <https://huggingface.co/docs/hub/en/gguf>
- Hugging Face GGUF model browser: <https://huggingface.co/models?library=gguf>

**Very important**
If you read all the way here, that means you're an OG and you definitely want to get started.
So another important caveat to know:
  - Local models are not as polished as the public models, so sometimes their thinking process can be excessive.
  - It is advisable to use these flags with your prompts:
    - $do-not-do-a-final-check=true
    - $stop-overthinking=true
    - $just-give-me-a-usable-new-list=true
    - $dont-be-perfectionistic-give-me-usability=true

| Model | Link | Why recommended | Honest Caveat |
| --- | --- | --- |  --- |
| Phi-4-mini-reasoning-abliterated-Q4_K_M-GGUF | <https://huggingface.co/dev-196/Phi-4-mini-reasoning-abliterated-Q4_K_M-GGUF> | Mechanical tasks, slot-generator, mix words out of existing reference-point; simplest model to experience the local freedom. | Sometimes has a trailing problem / infinite-looping at the end. |
| Qwen3.5-4B-Uncensored-HauhauCS-Aggressive-Q4_K_M.gguf | <https://huggingface.co/HauhauCS/Qwen3.5-4B-Uncensored-HauhauCS-Aggressive> | Smarter randomized generations, can actually synthesize new combinations. | Sometimes prone to overthinking. |
