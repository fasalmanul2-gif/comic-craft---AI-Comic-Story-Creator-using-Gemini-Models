# ComicCraft — AI Comic Story Creator

ComicCraft is a local FastAPI web app that converts a story idea into a five-panel comic. Gemini generates the structured outline and narration/dialogue, Stable Diffusion generates panel artwork, Jinja2 renders the browser preview, and FPDF2 exports the finished comic as a PDF.

## Features

- Exactly five ordered comic panels
- Story prompt, character, setting, tone, and art-style controls
- Gemini outline + story generation
- Stable Diffusion illustrations with a local placeholder fallback
- Comic-style responsive browser preview
- Downloadable PDF export
- JSON API for programmatic comic generation
- Safe validation and user-facing error handling
- Developer image-test endpoint that is disabled by default

## Requirements

- Python 3.11 or newer
- A Google Gemini API key
- Optional NVIDIA GPU for faster Stable Diffusion generation
- Enough disk space to download the Stable Diffusion model on first use

## Setup

### 1. Create a virtual environment

Windows:

```bash
python -m venv .venv
.venv\Scripts\activate
```

macOS/Linux:

```bash
python -m venv .venv
source .venv/bin/activate
```

### 2. Install dependencies

```bash
pip install -r requirements.txt
```

### 3. Configure environment variables

Copy `.env.example` to `.env` and set your Gemini key:

```env
GEMINI_API_KEY=your_api_key_here
```

The default models are:

- Outline: `gemini-3.5-flash-lite`
- Story: `gemini-3.8-flash`
- Images: `runwayml/stable-diffusion-v1-5`

If CUDA is not available, set:

```env
DEVICE=cpu
```

CPU image generation works but may be much slower.

### 4. Run ComicCraft

```bash
uvicorn app.main:app --reload
```

Open:

- App: http://127.0.0.1:8000
- API docs: http://127.0.0.1:8000/docs

## Suggested Demo Input

- **Story:** A brave fox exploring an enchanted forest.
- **Character:** Fenn
- **Setting:** Enchanted Forest
- **Tone:** Adventure
- **Art Style:** Comic Book

## API

### `POST /generate-comic/json`

Example JSON:

```json
{
  "story_prompt": "A brave fox exploring an enchanted forest.",
  "character_name": "Fenn",
  "setting": "Enchanted Forest",
  "tone": "Adventure",
  "art_style": "Comic Book"
}
```

### Other routes

- `GET /` — browser UI
- `POST /generate` — browser comic generation
- `GET /health` — health check
- `GET /export-success` — export confirmation page
- `POST /test-image` — developer-only image test; disabled unless `ENABLE_TEST_IMAGE=true`

## Generated Files

- Panel images: `static/panels/`
- PDF exports: `static/exports/`
- Image-generation fallback: `static/placeholders/panel-placeholder.png`

Generated panel/PDF files are ignored by Git.

## Error Behavior

- Invalid user input is rejected before AI calls.
- Malformed Gemini JSON is rejected with a controlled generation error.
- If one image fails, ComicCraft uses the local placeholder and continues.
- If PDF export fails, the browser preview still displays the full comic and shows an export warning.

## Run Tests

```bash
pytest -v
```

The automated suite uses mocked provider boundaries, so it does not require Gemini credentials or a Stable Diffusion model download.

## Notes for First Real Run

The first image generation can be slow because Hugging Face must download and initialize the Stable Diffusion model. Keep your API key only in `.env`; never commit it to Git.
