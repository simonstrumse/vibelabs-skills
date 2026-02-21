---
name: transcribe
description: >
  VAD-first audio transcription pipeline with evidence-backed Whisper configuration,
  language-aware model routing, dictionary correction, and LLM post-processing.
  Use when the user wants to transcribe audio files — meetings, interviews, voice memos,
  podcasts. Handles Norwegian (NB-Whisper) and English (whisper-large-v3-turbo) with
  automatic language detection. Runs locally on Apple Silicon via MLX.
  Handles: "transcribe this audio", "transcribe meeting recording", "/transcribe",
  "convert audio to text", "transkriber", "skriv ut dette opptaket".
argument-hint: "<audio_file_path> [--language no|en|auto] [--no-llm] [--no-vad]"
allowed-tools:
  - Bash
  - Read
  - Write
  - Edit
  - Grep
  - Glob
license: MIT
---

# Audio Transcription Pipeline

VAD-first, evidence-backed Whisper transcription running locally on Apple Silicon. Produces high-quality transcripts with zero hallucinations, correct domain terminology, and LLM-polished output.

Architecture: Audio → ffmpeg (16kHz WAV) → Silero-VAD (speech segmentation) → MLX Whisper → Dictionary replacement → Claude LLM correction → Output.

## Prerequisites

- **macOS with Apple Silicon** (M1/M2/M3/M4) — required for MLX
- **Python 3.9+** — Xcode Python at `/Library/Developer/CommandLineTools/usr/bin/python3` or Homebrew Python
- **ffmpeg** — for audio preprocessing (`brew install ffmpeg`)
- **Anthropic API key** — for LLM correction step (optional but recommended)

## Setup

### Detection Order

Check if the pipeline is already installed:

1. **Check config** — `ls ~/.config/transcribe/client.py`
2. **Check Python packages** — `python3 -c "import mlx_whisper, torch, scipy, anthropic"`
3. **No installation** — Run the First-Time Setup below

### Using Existing Installation

If `~/.config/transcribe/client.py` exists, the pipeline is ready:

```bash
# CLI usage
PYTHON="/Library/Developer/CommandLineTools/usr/bin/python3"
$PYTHON ~/.config/transcribe/client.py "/path/to/audio.m4a"

# With options
$PYTHON ~/.config/transcribe/client.py "/path/to/audio.m4a" --language no --no-llm
```

Or via Python API:

```python
import sys
from pathlib import Path
sys.path.insert(0, str(Path.home() / '.config/transcribe'))
from client import transcribe

result = transcribe("/path/to/audio.m4a")
print(f"Output: {result.corrected_path}")
print(f"Words: {result.corrected_text.split().__len__()}")
print(f"Speed: {result.audio_duration_s / result.total_time_s:.1f}x realtime")
```

### First-Time Setup

#### Step 1: Identify the Python environment

MLX requires Apple Silicon. Find the right Python:

```bash
# Option A: Xcode Python (most reliable for ML packages)
PYTHON="/Library/Developer/CommandLineTools/usr/bin/python3"
$PYTHON --version

# Option B: Homebrew Python
PYTHON="python3"
$PYTHON --version
```

Use whichever has `mlx` installed, or install from scratch with one of them.

#### Step 2: Install Python packages

```bash
# Core ML packages
$PYTHON -m pip install --user mlx mlx_whisper

# VAD (Voice Activity Detection) — requires torch
$PYTHON -m pip install --user torch torchaudio

# Audio processing
$PYTHON -m pip install --user numpy scipy

# LLM correction (optional but recommended)
$PYTHON -m pip install --user anthropic
```

**Verify installation:**

```bash
$PYTHON -c "
import mlx_whisper; print('mlx_whisper OK')
import torch; print(f'torch {torch.__version__} OK')
import scipy; print('scipy OK')
import numpy; print('numpy OK')
try:
    import anthropic; print('anthropic OK')
except: print('anthropic NOT installed (LLM correction will be unavailable)')
"
```

#### Step 3: Install ffmpeg

```bash
# macOS
brew install ffmpeg

# Verify
ffmpeg -version | head -1
```

#### Step 4: Create the pipeline directory

```bash
mkdir -p ~/.config/transcribe/models
```

#### Step 5: Create configuration files

Create `~/.config/transcribe/config.json`:

```json
{
  "models": {
    "norwegian": "nb-whisper-medium-mlx",
    "norwegian_fallback": "aalst/nb-whisper-large-distil-turbo-beta-mlx",
    "english": "mlx-community/whisper-large-v3-turbo",
    "multilingual": "mlx-community/whisper-large-v3-turbo"
  },
  "local_model_dir": "~/.config/transcribe/models",
  "language": "auto",
  "auto_detect_segments": 5,
  "auto_detect_norwegian_threshold": 0.8,
  "vad_enabled": true,
  "vad_chunk_max_seconds": 28,
  "vad_padding_ms": 400,
  "llm_correction": true,
  "llm_model": "claude-sonnet-4-20250514",
  "llm_chunk_words": 500,
  "output_dir": "/tmp/transcribe",
  "whisper_params": {
    "condition_on_previous_text": false,
    "best_of": 1,
    "compression_ratio_threshold": 1.8,
    "no_speech_threshold": 0.5,
    "temperature": [0.0, 0.2, 0.4, 0.6, 0.8, 1.0],
    "hallucination_silence_threshold": 2.0
  }
}
```

Create `~/.config/transcribe/dictionary.json`:

```json
{
  "initial_prompts": {
    "no": "Velkommen til møtet. Vi diskuterer teknologi, innovasjon og prosjektledelse.",
    "en": "Welcome to the meeting. We discuss technology, innovation and project management.",
    "auto": "Meeting about technology and innovation."
  },
  "replacements": {},
  "context_hints": {
    "no": [
      "This is a Norwegian meeting transcript",
      "Fix obvious speech-to-text errors only"
    ],
    "en": [
      "This is an English meeting transcript",
      "Fix obvious speech-to-text errors only"
    ],
    "auto": [
      "This transcript may contain both Norwegian and English",
      "Fix obvious speech-to-text errors only"
    ]
  }
}
```

**Customize the dictionary** for the user's domain:
- `initial_prompts` — Natural sentences with domain vocabulary (max 224 tokens). These bias Whisper toward correct recognition.
- `replacements` — Regex-based fixes for known misrecognitions (e.g., `"cloud code": "Claude Code"`). Case-insensitive.
- `context_hints` — Instructions for the LLM correction pass describing the recording's context.

#### Step 6: Create the `.env` file (for LLM correction)

```bash
echo "ANTHROPIC_API_KEY=sk-ant-..." > ~/.config/transcribe/.env
```

The pipeline loads this automatically if `ANTHROPIC_API_KEY` is not in the shell environment.

#### Step 7: Write the pipeline script

Create `~/.config/transcribe/client.py` with the full pipeline code. The source is at:
- **If the user already has it:** Check `~/.config/transcribe/client.py`
- **If not:** The complete pipeline (~400 lines) must be written. It implements the 6-stage architecture described in the Pipeline Architecture section below.

The client.py shebang should match the Python used in Step 1:

```python
#!/Library/Developer/CommandLineTools/usr/bin/python3
```

#### Step 8: Download models (happens automatically on first run)

The first transcription will download the required Whisper model from HuggingFace (~1.5GB). Subsequent runs use the cached model.

```bash
# Pre-download Norwegian model (optional)
$PYTHON -c "import mlx_whisper; mlx_whisper.transcribe('/dev/null', path_or_hf_repo='aalst/nb-whisper-large-distil-turbo-beta-mlx')" 2>/dev/null

# Pre-download English/multilingual model (optional)
$PYTHON -c "import mlx_whisper; mlx_whisper.transcribe('/dev/null', path_or_hf_repo='mlx-community/whisper-large-v3-turbo')" 2>/dev/null
```

## Usage

### CLI

```bash
PYTHON="/Library/Developer/CommandLineTools/usr/bin/python3"

# Basic (auto language detection, VAD, LLM correction)
$PYTHON ~/.config/transcribe/client.py "/path/to/meeting.m4a"

# Force Norwegian
$PYTHON ~/.config/transcribe/client.py "/path/to/meeting.m4a" --language no

# Force English
$PYTHON ~/.config/transcribe/client.py "/path/to/meeting.m4a" --language en

# Skip LLM correction (faster, raw output only)
$PYTHON ~/.config/transcribe/client.py "/path/to/meeting.m4a" --no-llm

# Skip VAD (process as single chunk — NOT recommended)
$PYTHON ~/.config/transcribe/client.py "/path/to/meeting.m4a" --no-vad

# Re-run only LLM correction on existing transcript
$PYTHON ~/.config/transcribe/client.py "/path/to/meeting.m4a" --llm-only

# Custom output directory
$PYTHON ~/.config/transcribe/client.py "/path/to/meeting.m4a" --output-dir /path/to/output
```

### Python API

```python
import sys
from pathlib import Path
sys.path.insert(0, str(Path.home() / '.config/transcribe'))
from client import transcribe

# Full pipeline
result = transcribe("/path/to/audio.m4a")

# Access results
print(result.corrected_text)       # Final corrected transcript
print(result.raw_text)             # Raw Whisper output
print(result.corrected_path)       # Path to corrected.txt
print(result.raw_path)             # Path to raw.txt
print(result.metadata_path)        # Path to metadata.json
print(result.language)             # Detected language
print(result.model_used)           # Which model was used
print(result.audio_duration_s)     # Audio length in seconds
print(result.num_vad_segments)     # Number of VAD chunks
print(result.transcribe_time_s)    # Whisper processing time
print(result.llm_correct_time_s)   # LLM correction time
print(result.total_time_s)         # Total pipeline time

# With overrides
result = transcribe(
    "/path/to/audio.m4a",
    language="no",              # Force Norwegian
    vad_enabled=True,           # Default
    llm_correction=False,       # Skip LLM step
    output_dir="/custom/path",  # Custom output
    initial_prompt="Meeting about renewable energy at Equinor..."
)
```

### Running as Background Task

Transcription of long audio files takes significant time. Run as a background task:

```bash
PYTHON="/Library/Developer/CommandLineTools/usr/bin/python3"
$PYTHON ~/.config/transcribe/client.py "/path/to/meeting.m4a" 2>&1
```

Set `run_in_background: true` when invoking via Bash tool. Monitor output for progress:
- `[1/6] Preprocessing...` — ffmpeg conversion
- `[2/6] VAD Segmentation...` — speech detection, shows chunk count
- `[3/6] Transcribing...` — chunk-by-chunk progress
- `[5/6] LLM Correction...` — parallel API calls
- `COMPLETE` — with final stats

## Output

The pipeline saves to `{output_dir}/{audio_name}/`:

| File | Contents | When |
|------|----------|------|
| `raw.txt` | Raw Whisper output (no corrections) | Always |
| `dict_corrected.txt` | After dictionary replacements | Always |
| `corrected.txt` | After LLM correction | When LLM enabled |
| `metadata.json` | Stats, timing, config snapshot | Always |
| `input.wav` | 16kHz mono WAV (preprocessed) | Always |
| `chunks/` | Individual chunk WAVs and transcripts | Always |

`metadata.json` contains:
```json
{
  "audio_path": "/original/path.m4a",
  "audio_duration_s": 3261.8,
  "language_mode": "auto",
  "language_detected": "no",
  "model_used": "nb-whisper-medium-mlx",
  "vad_enabled": true,
  "num_vad_segments": 140,
  "llm_correction": true,
  "llm_model": "claude-sonnet-4-20250514",
  "transcribe_time_s": 298.8,
  "llm_correct_time_s": 41.2,
  "total_time_s": 340.0,
  "raw_word_count": 7005,
  "corrected_word_count": 6975
}
```

Report key stats to the user: duration, processing speed (Xx realtime), word count, output paths.

## Pipeline Architecture

### Stage 1: Preprocess (ffmpeg)

Converts any audio format (.m4a, .mp3, .wav, .ogg, .flac, etc.) to 16kHz mono WAV for Whisper. Skips if `input.wav` already exists (safe to re-run).

### Stage 2: VAD Segmentation (Silero-VAD)

**The #1 quality intervention.** Silero-VAD detects speech segments and merges them into chunks of maximum 28 seconds (matching Whisper's 30s internal window). Each chunk gets 400ms padding at boundaries.

Why this matters:
- Whisper hallucinates during silence (repeats text, generates phantom speech)
- VAD eliminates silence from Whisper's input entirely
- Reduces hallucination rate from ~20% to 0.2% (Baranski et al., 2025)
- 687 raw VAD segments → 140 merged chunks on a 54-min meeting

Audio loading uses `scipy.io.wavfile` (not torchaudio — no audio backends on macOS).

### Stage 3: Transcription (MLX Whisper)

Language-aware model routing:

| Language | Model | When |
|----------|-------|------|
| Norwegian (`no`) | NB-Whisper Medium MLX | Pure Norwegian audio |
| English (`en`) | whisper-large-v3-turbo | Pure English audio |
| Auto (default) | Detect → route | Mixed or unknown |

**Auto-detection:** Runs Whisper language detection on the first 5 VAD segments. If >80% detected as Norwegian → NB-Whisper. Otherwise → standard whisper-large-v3-turbo.

Evidence-backed Whisper parameters:
- `condition_on_previous_text=False` — prevents hallucination propagation between chunks
- No `beam_size` parameter — mlx_whisper only supports greedy decoding (beam search not implemented)
- `temperature=(0.0, 0.2, 0.4, 0.6, 0.8, 1.0)` — fallback tuple for recovery when model gets stuck
- `hallucination_silence_threshold=2.0` — marginal but helps catch remaining silence artifacts
- `initial_prompt` — natural sentence with domain vocabulary (not a term list), max 224 tokens

Memory management between chunks:
```python
mx.set_cache_limit(100_000_000)  # 100MB cache limit
mx.clear_cache()                  # Between each chunk
```

### Stage 4: Dictionary Replacement

Regex-based case-insensitive replacements from `dictionary.json`. Catches systematic ASR errors that are consistent across runs:

```json
{
  "cloud code": "Claude Code",
  "entropic": "Anthropic",
  "terroristiske": "deterministiske",
  "co-pilot": "CoPilot"
}
```

This is the only stage with 100% precision — it fixes known errors every time.

### Stage 5: LLM Correction (Claude Sonnet)

Splits text into ~500-word chunks and sends them in parallel (4 concurrent API calls) to Claude Sonnet for correction. The system prompt instructs strict error-fixing only:

- Fix obvious misrecognitions
- Fix punctuation and capitalization
- Preserve the speaker's actual meaning
- Do NOT rephrase, summarize, or restructure
- Return ONLY the corrected text

Context hints from `dictionary.json` provide domain knowledge (company names, technical terms, meeting context).

Loads `ANTHROPIC_API_KEY` from:
1. Environment variable (if set)
2. `~/.config/transcribe/.env` file (fallback)

### Stage 6: Output

Saves `raw.txt`, `dict_corrected.txt`, `corrected.txt`, and `metadata.json`. Each intermediate file serves as a resume point — if the pipeline fails at stage 5, the raw transcript is preserved.

## Configuration Reference

### config.json

| Key | Default | Description |
|-----|---------|-------------|
| `models.norwegian` | `nb-whisper-medium-mlx` | Model for Norwegian audio |
| `models.norwegian_fallback` | `aalst/nb-whisper-large-distil-turbo-beta-mlx` | Fallback if local model not converted |
| `models.english` | `mlx-community/whisper-large-v3-turbo` | Model for English audio |
| `models.multilingual` | `mlx-community/whisper-large-v3-turbo` | Model for auto-detection and mixed audio |
| `local_model_dir` | `~/.config/transcribe/models` | Where converted models are stored |
| `language` | `auto` | Default language mode (`no`, `en`, `auto`) |
| `auto_detect_segments` | `5` | Number of VAD segments used for language detection |
| `auto_detect_norwegian_threshold` | `0.8` | Ratio of Norwegian-detected segments to use NB-Whisper |
| `vad_enabled` | `true` | Enable VAD segmentation (strongly recommended) |
| `vad_chunk_max_seconds` | `28` | Maximum chunk duration in seconds |
| `vad_padding_ms` | `400` | Padding added to each side of speech segments |
| `llm_correction` | `true` | Enable Claude LLM correction pass |
| `llm_model` | `claude-sonnet-4-20250514` | Claude model for LLM correction |
| `llm_chunk_words` | `500` | Words per LLM correction chunk |
| `output_dir` | `/tmp/transcribe` | Base output directory |

### dictionary.json

| Key | Description |
|-----|-------------|
| `initial_prompts` | Per-language natural sentences with domain vocabulary (max 224 tokens) |
| `replacements` | Regex-based case-insensitive find/replace pairs |
| `context_hints` | Per-language lists of context lines for LLM correction prompts |

### Customizing for a Domain

To adapt the pipeline for a new domain:

1. **Add initial prompts** with relevant vocabulary as natural sentences:
   ```json
   "no": "Velkommen til styremøte i Equinor. Vi diskuterer havvind, karbonfangst, og produksjonsrapporter."
   ```

2. **Add replacements** for words you know Whisper consistently misrecognizes:
   ```json
   "equi nor": "Equinor",
   "havvin": "havvind"
   ```

3. **Add context hints** so the LLM correction understands the domain:
   ```json
   "no": [
     "This is a Norwegian board meeting at an energy company",
     "Key terms: havvind, karbonfangst, CCS, produksjonsvolum"
   ]
   ```

## Performance

Benchmarked on a 54-minute Norwegian meeting recording (M1 MacBook Pro 16GB):

| Metric | Value |
|--------|-------|
| Audio duration | 3,262s (54 min) |
| Transcription time | 299s |
| LLM correction time | 41s |
| Total time | 340s |
| Processing speed | 9.6x realtime |
| VAD segments | 140 chunks (from 687 raw) |
| Word count | 6,975 (corrected) |
| Hallucinations | Zero |
| Brand name accuracy | 100% (via dictionary) |

### Comparison with Other Approaches

| Approach | Hallucinations | Brand Names | Words |
|----------|:--------------:|:-----------:|------:|
| **This pipeline** | **None** | **100%** | **6,975** |
| OpenAI Whisper API | None | 0% | 7,974 |
| MLX basic (single pass) | Severe (44x repeats) | 0% | 7,973 |
| whisper-large-v3-turbo | Moderate (7x repeats) | 0% | 9,077 |

## Critical Gotchas

### 1. mlx_whisper uses kwargs, not a dict

```python
# WRONG — causes TypeError
mlx_whisper.transcribe(path, decode_options={"language": "no"})

# CORRECT — spread as kwargs
mlx_whisper.transcribe(path, language="no", best_of=1)
```

The `mlx_whisper.transcribe()` signature uses `**decode_options` (variadic kwargs), not `decode_options: dict`. The docstring is misleading.

### 2. mlx_whisper does NOT implement beam search

```python
# WRONG — raises NotImplementedError
mlx_whisper.transcribe(path, beam_size=1)

# CORRECT — omit beam_size entirely (greedy decoding only)
mlx_whisper.transcribe(path)
```

Any non-None `beam_size` triggers `NotImplementedError: Beam search decoder is not yet implemented`. Greedy decoding (the default) is equivalent to beam_size=1.

### 3. torchaudio has no audio backends on macOS

```python
# WRONG — fails with "No audio backend is available"
import torchaudio
wav, sr = torchaudio.load("audio.wav")

# CORRECT — use scipy instead
from scipy.io import wavfile
sr, audio_np = wavfile.read("audio.wav")
audio_float = audio_np.astype(np.float32) / 32768.0  # int16 → float32
wav = torch.from_numpy(audio_float)
```

`torchaudio.list_audio_backends()` returns `[]` on macOS with torchaudio 2.8.0. Use `scipy.io.wavfile` for WAV loading.

### 4. MLX cache API is deprecated

```python
# WRONG — deprecated
mx.metal.set_cache_limit(100_000_000)
mx.metal.clear_cache()

# CORRECT — new API
mx.set_cache_limit(100_000_000)
mx.clear_cache()
```

### 5. ANTHROPIC_API_KEY not in subprocess environment

The Python process spawned by the agent may not have the API key in its environment. The pipeline loads from `~/.config/transcribe/.env` as fallback. If LLM correction fails with auth errors, check:

```bash
# Verify key exists
cat ~/.config/transcribe/.env

# Or set in environment before running
export ANTHROPIC_API_KEY="sk-ant-..."
```

### 6. Temperature must be a tuple, not a float

```python
# WRONG — no fallback recovery
temperature=0.0

# CORRECT — fallback tuple for when model gets stuck
temperature=(0.0, 0.2, 0.4, 0.6, 0.8, 1.0)
```

When Whisper gets stuck on a chunk (high compression ratio), it retries with progressively higher temperatures.

### 7. initial_prompt must be natural text

```python
# WRONG — term list (Whisper treats as transcript prefix)
initial_prompt="Claude Code, Anthropic, MCP, CoPilot, Vercel"

# CORRECT — natural sentence (max 224 tokens)
initial_prompt="Velkommen til møte om teknologi. Vi diskuterer Claude Code, Anthropic, CoPilot og MCP-servere."
```

Whisper uses the initial prompt as if it were previous transcript text. A term list creates unnatural conditioning. A natural sentence biases vocabulary without distorting output.

### 8. NB-Whisper Medium MLX needs conversion

The NB-Whisper Medium model (`NbAiLab/nb-whisper-medium`) is in HuggingFace format, not MLX. Until converted, the pipeline falls back to `aalst/nb-whisper-large-distil-turbo-beta-mlx`. To convert:

```bash
$PYTHON ~/.config/transcribe/convert_model.py
```

This creates `~/.config/transcribe/models/nb-whisper-medium-mlx/`. The fallback model works well — conversion is optional but may improve Norwegian WER.

## Supported Audio Formats

Any format ffmpeg can decode: `.m4a`, `.mp3`, `.wav`, `.ogg`, `.flac`, `.aac`, `.wma`, `.webm`, `.opus`, `.amr`, `.mp4` (audio track).

## Evidence Basis

The architecture decisions are backed by empirical research:

| Decision | Evidence | Source |
|----------|----------|--------|
| VAD preprocessing | Reduces hallucinations from ~20% to 0.2% | Baranski et al., 2025 (arXiv:2501.11378) |
| 28s chunk length | Matches Whisper's 30s internal window | NB-Whisper recommendation, WhisperX design |
| Greedy decoding (beam=1) | beam=1 → 19.5% hallucination vs beam=5 → 28-37% | Baranski et al., 2025 |
| NB-Whisper for Norwegian | WER 7.2% vs OpenAI Large-v3 10.4% | NB-Whisper benchmarks |
| condition_on_previous=False | Prevents hallucination propagation between chunks | Standard Whisper best practice |
| Temperature fallback tuple | Recovery when model stuck on dialect variation | Default Whisper behavior |
