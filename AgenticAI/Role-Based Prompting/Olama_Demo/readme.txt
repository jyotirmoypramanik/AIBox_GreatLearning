╔══════════════════════════════════════════════════════════════════════╗
║         LOCAL OLLAMA + qwen3:4b — JUPYTER NOTEBOOK PROJECT          ║
║                         README & SETUP GUIDE                        ║
╚══════════════════════════════════════════════════════════════════════╝

PROJECT FILES
─────────────
  ollama_qwen3_demo.ipynb   ← Main Jupyter notebook (run this)
  requirements.txt          ← Python package dependencies
  readme.txt                ← This file

──────────────────────────────────────────────────────────────────────
 PREREQUISITES
──────────────────────────────────────────────────────────────────────

1. Python 3.9 or higher
   → Check: python --version  OR  python3 --version

2. Ollama installed on your machine
   → Download: https://ollama.com/download
   → macOS/Linux one-liner:
       curl -fsSL https://ollama.com/install.sh | sh
   → Windows: download the installer from the site above

──────────────────────────────────────────────────────────────────────
 STEP-BY-STEP SETUP
──────────────────────────────────────────────────────────────────────

STEP 1 — Clone / Download the project files
   Place all files in a folder, e.g.:
       ~/projects/ollama-demo/

STEP 2 — Create a Python virtual environment (recommended)
   cd ~/projects/ollama-demo

   # Create venv
   python -m venv venv

   # Activate it
   # macOS / Linux:
   source venv/bin/activate
   # Windows (Command Prompt):
   venv\Scripts\activate.bat
   # Windows (PowerShell):
   venv\Scripts\Activate.ps1

STEP 3 — Install Python dependencies
   pip install -r requirements.txt

STEP 4 — Start Ollama server
   # In a SEPARATE terminal window, run:
   ollama serve

   # You should see output like:
   # Listening on 127.0.0.1:11434

   NOTE: Ollama must keep running in this terminal the entire time
         you use the notebook. Do not close it.

STEP 5 — Pull the qwen3:4b model (first-time only)
   # In another terminal:
   ollama pull qwen3:4b

   # This downloads ~2.5 GB. Wait for it to complete.
   # Verify it's available:
   ollama list

STEP 6 — Launch Jupyter
   # In your project folder (with venv activated):

   # Option A — JupyterLab (modern UI, recommended):
   jupyter lab

   # Option B — Classic Notebook:
   jupyter notebook

   Your browser will open automatically.
   Navigate to and open:  ollama_qwen3_demo.ipynb

STEP 7 — Run the notebook
   • Click "Run All" (Kernel → Run All Cells)  OR
   • Press Shift+Enter on each cell to run one at a time

──────────────────────────────────────────────────────────────────────
 NOTEBOOK STRUCTURE
──────────────────────────────────────────────────────────────────────

  Cell 0 — Install Dependencies (pip install)
  Cell 1 — Configuration       (model name, host, system prompt)
  Cell 2 — Health Check        (verify Ollama is reachable)

  ── METHOD 1: ollama Python SDK ──────────────────────────────────
  Cell 3 — 1-A  Basic Chat (single response, no streaming)
  Cell 4 — 1-B  Streaming Chat (tokens printed in real-time)
  Cell 5 — 1-C  Multi-Turn Conversation (chat history maintained)

  ── METHOD 2: REST API via requests ──────────────────────────────
  Cell 6 — 2-A  /api/chat endpoint        (chat-style, non-streaming)
  Cell 7 — 2-B  /api/generate endpoint    (raw completion, streaming)
  Cell 8 — 2-C  /v1/chat/completions      (OpenAI-compatible endpoint)

  Cell 9 — Side-by-Side Comparison (timing & token count)
  Cell 10— Interactive Chat Widget (type and chat in the notebook)

──────────────────────────────────────────────────────────────────────
 QUICK EXAMPLES (what each method does)
──────────────────────────────────────────────────────────────────────

METHOD 1 — ollama SDK
  import ollama
  response = ollama.chat(
      model="qwen3:4b",
      messages=[
          {"role": "system", "content": "You are a helpful assistant."},
          {"role": "user",   "content": "What is Python?"},
      ]
  )
  print(response["message"]["content"])

METHOD 2A — REST API /api/chat
  import requests
  resp = requests.post("http://localhost:11434/api/chat", json={
      "model": "qwen3:4b",
      "messages": [
          {"role": "system", "content": "You are a helpful assistant."},
          {"role": "user",   "content": "What is Python?"},
      ],
      "stream": False,
  })
  print(resp.json()["message"]["content"])

METHOD 2B — REST API /api/generate (raw completion)
  import requests, json
  resp = requests.post("http://localhost:11434/api/generate", json={
      "model":  "qwen3:4b",
      "system": "You are a helpful assistant.",
      "prompt": "What is Python?",
      "stream": False,
  })
  print(resp.json()["response"])

METHOD 2C — OpenAI-Compatible endpoint
  import requests
  resp = requests.post("http://localhost:11434/v1/chat/completions", json={
      "model": "qwen3:4b",
      "messages": [
          {"role": "system", "content": "You are a helpful assistant."},
          {"role": "user",   "content": "What is Python?"},
      ],
  })
  print(resp.json()["choices"][0]["message"]["content"])

──────────────────────────────────────────────────────────────────────
 CUSTOMIZING THE MODEL / PROMPT
──────────────────────────────────────────────────────────────────────

Edit the Configuration cell (Cell 1) to change:

  MODEL_NAME    = "qwen3:4b"         # swap for any pulled model
  OLLAMA_HOST   = "http://localhost:11434"  # change if remote host
  SYSTEM_PROMPT = "..."              # your custom system instructions
  USER_PROMPT   = "..."             # your test user message

Other models you can try:
  ollama pull llama3.2          # Meta Llama 3.2 (3B)
  ollama pull mistral           # Mistral 7B
  ollama pull gemma3:4b         # Google Gemma 3 (4B)
  ollama pull phi4-mini         # Microsoft Phi-4 Mini

──────────────────────────────────────────────────────────────────────
 TROUBLESHOOTING
──────────────────────────────────────────────────────────────────────

❌ "Cannot reach Ollama at http://localhost:11434"
   → Make sure `ollama serve` is running in a terminal.
   → Check no firewall is blocking port 11434.

❌ "model 'qwen3:4b' not found"
   → Run: ollama pull qwen3:4b
   → Then verify: ollama list

❌ Slow responses
   → qwen3:4b runs on CPU if no GPU is detected — this is normal.
   → For GPU acceleration: ensure your CUDA/ROCm/Metal drivers are
     installed and Ollama detects your GPU.
   → Check GPU usage: ollama ps

❌ Kernel dies / memory error
   → qwen3:4b needs ~4–6 GB RAM. Close other applications.
   → Alternatively try a smaller model: ollama pull qwen3:1.7b

❌ ipywidgets not rendering (interactive cell)
   → Run: jupyter labextension install @jupyter-widgets/jupyterlab-manager
   → Or update JupyterLab: pip install --upgrade jupyterlab ipywidgets

──────────────────────────────────────────────────────────────────────
 USEFUL OLLAMA COMMANDS
──────────────────────────────────────────────────────────────────────

  ollama serve              # Start the Ollama server
  ollama list               # List all downloaded models
  ollama pull qwen3:4b      # Download qwen3:4b model
  ollama ps                 # Show running models + GPU/CPU usage
  ollama run qwen3:4b       # Quick interactive CLI chat
  ollama rm qwen3:4b        # Remove a model to free disk space

──────────────────────────────────────────────────────────────────────
 SYSTEM REQUIREMENTS
──────────────────────────────────────────────────────────────────────

  Minimum:
    RAM     : 8 GB (4 GB for model + 4 GB system)
    Storage : 5 GB free (model ~2.5 GB)
    OS      : macOS 12+, Ubuntu 20.04+, Windows 10/11

  Recommended:
    RAM     : 16 GB+
    GPU     : NVIDIA (CUDA 11+), AMD (ROCm), or Apple Silicon (Metal)
    Storage : 10 GB+ (for multiple models)

──────────────────────────────────────────────────────────────────────
