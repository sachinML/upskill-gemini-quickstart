below is the current readme

# UPskill × Gemini Quickstart

Run UPskill using **Google Gemini** — no Anthropic key needed.

---

## Why this folder exists

UPskill (`v0.2.1` on PyPI) is currently configured to work primarily with Anthropic models.

Running it with Gemini requires a few fixes:

| Issue | Fix |
|------|-----|
| Test generator uses Anthropic Opus by default | Replace with `google.gemini-2.5-pro` |
| Gemini rejects `additionalProperties` in JSON schema | Strip it from `TestCaseSuite.model_json_schema()` |
| FastAgent falls back to OpenAI if no default model is set | Set `default_model: google.gemini-2.5-flash` |

This folder provides a minimal patch + config to make UPskill work with Gemini.

> This patch modifies the Hugging Face Upskill project (Apache 2.0 licensed)
> to enable compatibility with Gemini-based models.
>
> The original project remains unchanged; users are expected to install it
> separately and apply this patch.

Tested with:
- upskill v0.2.1 (PyPI)
- Gemini 2.5 Pro / Flash

---

**The concept:**
- Gemini 2.5 Pro (teacher) writes a skill guide once
- Gemini 2.5 Flash (student) is tested without the skill
- Gemini 2.5 Flash is tested with the skill
- You see the before/after lift in your terminal

---

## Prerequisites

- Python 3.13+
- A Google Gemini API key

---

## Setup

### 1. Create and activate a virtual environment

```bash
python3 -m venv venv
source venv/bin/activate      # Windows: venv\Scripts\activate
```

> All commands below assume the venv is active.

### 2. Install upskill

```bash
pip install upskill
```

### 3. Go into this folder

All commands must run from inside `upskill-gemini-quickstart/` upskill looks for
config files in the current directory.

```bash
cd upskill-gemini-quickstart
```

### 4. Add your Gemini API key

```bash
cp fastagent.secrets.yaml.example fastagent.secrets.yaml
```

Open `fastagent.secrets.yaml` and replace the placeholder with your key:

```yaml
google:
  api_key: "..."
```

> `fastagent.secrets.yaml` is gitignored — your key will never be committed.

### 5. Apply the Gemini compatibility patch (one-time)

```bash
python patch.py
```

This fixes two things in the installed package:
- Replaces the hardcoded Anthropic model in the test generator agent card
- Strips `additionalProperties` from JSON schemas so Gemini's structured output works

Safe to re-run the script detects existing changes and skips if already applied.

---

## Run

Pick any task you want to evaluate skill generation on:

```bash
upskill generate "YOUR TASK HERE" \
  --model google.gemini-2.5-pro \
  --eval-model google.gemini-2.5-flash

After running, view the history chart:

```bash
upskill runs
```

Evaluate an existing skill again on a different model:

```bash
upskill eval ./skills/<skill-name>/ -m google.gemini-2.5-flash
```

List all generated skills:

```bash
upskill list
```

---

## What good output looks like

Example task used:
- "Write Python docstrings in Google style format"

```
python-google-style-docstrings

google.gemini-2.5-flash
  baseline   █████░░░░░░░░░░░░░░░    25%
  with skill ███████████████░░░░░    75%  (+50%)

Recommendation: keep skill ✅
```

---

## How it works

| Step | Model | Role |
|------|-------|------|
| Skill generation | Gemini 2.5 Pro | Teacher writes the `SKILL.md` guide once |
| Test generation | Gemini 2.5 Pro | Creates evaluation test cases |
| Evaluation (baseline) | Gemini 2.5 Flash | Student runs task *without* the skill |
| Evaluation (with skill) | Gemini 2.5 Flash | Student runs task *with* the skill |

---

## Files

```
upskill-gemini-quickstart/
├── README.md                        ← you are here
├── fastagent.config.yaml            ← FastAgent config (sets default_model — required)
├── fastagent.secrets.yaml.example   ← API key template → copy to fastagent.secrets.yaml
├── upskill.config.yaml              ← UPskill config (teacher/student models, local executor)
├── patch.py                         ← one-time Gemini compatibility fix
└── .gitignore                       ← keeps fastagent.secrets.yaml out of git
```

---

## Troubleshooting

**`Anthropic API key not configured`**
→ You are not running from inside `upskill-gemini-quickstart/`. The config files must be
  in the current directory. Run `cd upskill-gemini-quickstart` then retry.

**`additionalProperties is not supported in the Gemini API`**
→ Run `python patch.py`.

**`Google API key not configured`**
→ Check that `fastagent.secrets.yaml` exists with a real key (not the placeholder).

**`upskill: command not found`**
→ Activate your venv first: `source venv/bin/activate`

**Skill lift is 0% or negative**
→ The task may be too easy for the student model — it already knows the answer.
  Try a more specific or niche task where Flash genuinely struggles without guidance.
  Avoid common tasks like "write git commits" — all models already do those well.
