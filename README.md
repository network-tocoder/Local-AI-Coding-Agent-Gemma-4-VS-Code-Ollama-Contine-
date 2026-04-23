# 🤖 Local AI Coding Agent — Gemma 4 + VS Code

![Platform](https://img.shields.io/badge/Platform-Windows%20%7C%20Mac%20%7C%20Linux-blue?style=for-the-badge)
![Cost](https://img.shields.io/badge/Cost-Zero-brightgreen?style=for-the-badge)
![Cloud](https://img.shields.io/badge/Cloud-Not%20Required-red?style=for-the-badge)
![VS Code](https://img.shields.io/badge/VS%20Code-1.89+-007ACC?style=for-the-badge&logo=visualstudiocode)
![Ollama](https://img.shields.io/badge/Ollama-Local%20Server-black?style=for-the-badge)
![Gemma 4](https://img.shields.io/badge/Gemma%204-Google-4285F4?style=for-the-badge&logo=google)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

> **Replace GitHub Copilot with a fully local, free, autonomous coding agent running on your own hardware.**

---

## 📺 Watch the Full Video
[![YouTube](https://img.shields.io/badge/YouTube-Watch%20Now-FF0000?style=for-the-badge&logo=youtube&logoColor=white)](https://www.youtube.com/watch?v=2qFcbRK1qDE)

> *I built a complete startup landing page — 3 files, features, pricing, animations — in under 15 minutes. Zero cost. 100% offline. This repo is the full setup guide.*

---

## ⚡ What Is This?

This repo contains everything you need to set up a **fully autonomous local AI coding agent** inside VS Code using:

| Tool | Role |
|------|------|
| 🦙 **Ollama** | Local model server — runs at `localhost:11434` |
| 🤖 **Gemma 4** | Google's latest open source AI model |
| 💻 **VS Code** | Your code editor |
| ⚙️ **Continue** | AI agent extension — replaces GitHub Copilot |

---

## 🖥️ Hardware Requirements

| Spec | Minimum | Recommended |
|------|---------|-------------|
| RAM | 16 GB | 24 GB+ |
| CPU | 4-core | 8-core |
| GPU | Not required | Nvidia (optional, for faster inference) |
| Storage | 20 GB free | SSD recommended |

---

## 🧠 Which Gemma 4 Model Should You Pick?

Gemma 4 comes in two categories:

### Edge Models (Low-end / Modest Hardware)
> The **E** stands for **effective parameters** — compute saving, not RAM saving. All weights still load into memory.

| Model | RAM Required | Download Size | Context | Best For |
|-------|-------------|---------------|---------|----------|
| `gemma4:e2b` | 8–12 GB | 7.2 GB | 128K | Very low-end systems |
| `gemma4:e4b` | 12–16 GB | 9.6 GB | 128K | Most laptops ✅ |

### Workstation Models (High-end Hardware)
| Model | RAM Required | Download Size | Context | Architecture |
|-------|-------------|---------------|---------|-------------|
| `gemma4:26b` | 24 GB+ | 18 GB | 256K | MoE — recommended ✅ |
| `gemma4:31b` | 32 GB+ | 20 GB | 256K | Dense — most powerful |

> 💡 **Simple rule:** Under 16GB RAM → `gemma4:e4b`. Have 24GB+ → `gemma4:26b`

> ⚠️ **MoE vs Dense:** The 26B uses Mixture of Experts — only 4 billion parameters active per token generated. Think of it like a smart team where only the relevant people get called in for each task. Same talent pool, fraction of the effort. The 31B is dense — full team every time. More consistent but slower and heavier.

---

## 🚀 Installation Guide

### Step 1 — Install Ollama

<details>
<summary>🪟 Windows</summary>

```powershell
irm https://ollama.com/install.ps1 | iex
```

Verify install:
```powershell
ollama --version
```

Confirm server is running — open in browser:
```
http://localhost:11434
```
</details>

<details>
<summary>🍎 macOS</summary>

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Verify:
```bash
ollama --version
```
</details>

<details>
<summary>🐧 Linux</summary>

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Start Ollama service:
```bash
sudo systemctl start ollama
sudo systemctl enable ollama
```

Verify:
```bash
ollama --version
```
</details>

---

### Step 2 — Download Gemma 4

```bash
# For most laptops (under 16GB RAM)
ollama run gemma4:e4b

# For 24GB+ RAM systems — recommended
ollama run gemma4:26b
```

Test the model in REPL, then exit:
```bash
/bye
```

Check model is loaded:
```bash
ollama ps
```

---

### Step 3 — Create Modelfile (Set Context Permanently)

> Default context is 4096 — too small for agentic tasks. We bake 16384 context into a named model variant so it persists across sessions.

<details>
<summary>🪟 Windows (PowerShell)</summary>

```powershell
New-Item Modelfile -Value "FROM gemma4:26b`nPARAMETER num_ctx 16384"
ollama create gemma4-agent -f Modelfile
ollama run gemma4-agent
```
</details>

<details>
<summary>🍎 macOS / 🐧 Linux</summary>

```bash
cat > Modelfile << EOF
FROM gemma4:26b
PARAMETER num_ctx 16384
EOF

ollama create gemma4-agent -f Modelfile
ollama run gemma4-agent
```
</details>

Verify context is applied:
```bash
ollama ps
# Should show: gemma4-agent | 19GB | 100% CPU | 16384
```

---

### Step 4 — Install Continue in VS Code

1. Open VS Code
2. Press `Ctrl+Shift+X` (Windows/Linux) or `Cmd+Shift+X` (Mac)
3. Search **Continue**
4. Click **Install**
5. Click the Continue icon in the sidebar
6. Skip the initial setup wizard

---

### Step 5 — Configure Continue

Open `config.yaml` and replace contents with:

```yaml
name: Local Config
version: 1.0.0
schema: v1
models:
  - name: gemma4-agent
    provider: ollama
    model: gemma4-agent:latest
```

> 📁 Config file location:
> - **Windows:** `C:\Users\<username>\.continue\config.yaml`
> - **Mac/Linux:** `~/.continue/config.yaml`

---

### Step 6 — Set Tool Permissions

1. Click the **gear icon** ⚙️ in the Continue panel
2. Go to **Tools**
3. Set every built-in tool to **Automatic**

> ⚠️ Without this step Continue will generate code but won't create files automatically.

---

### Step 7 — Switch to Agent Mode

At the bottom of the Continue panel — click **Chat** and switch to **Agent** mode.

> Agent mode enables full autonomous action — file creation, editing, terminal commands — without manual approval on every step.

---

## 🔍 Check Generation Speed

```powershell
# Windows PowerShell
$response = Invoke-WebRequest -Uri "http://localhost:11434/api/generate" -Method POST -ContentType "application/json" -Body '{"model": "gemma4-agent", "prompt": "hello", "stream": false}' -UseBasicParsing
$data = $response.Content | ConvertFrom-Json
Write-Host "Tokens generated: $($data.eval_count)"
Write-Host "Total time (sec): $([math]::Round($data.total_duration / 1e9, 2))"
Write-Host "Tokens per second: $([math]::Round($data.eval_count / ($data.eval_duration / 1e9), 2))"
```

```bash
# Mac / Linux
curl -s -X POST http://localhost:11434/api/generate \
  -H "Content-Type: application/json" \
  -d '{"model": "gemma4-agent", "prompt": "hello", "stream": false}' | \
  python3 -c "
import json, sys
d = json.load(sys.stdin)
print(f'Tokens generated: {d[\"eval_count\"]}')
print(f'Total time (sec): {round(d[\"total_duration\"] / 1e9, 2)}')
print(f'Tokens per second: {round(d[\"eval_count\"] / (d[\"eval_duration\"] / 1e9), 2)}')
"
```

---

## 🎯 Demo — Test Your Setup

Open VS Code in a new folder and send this prompt to Continue in Agent mode:

```
Create a landing page for a startup called PitchZero inside the current workspace.

Create these files:
- index.html
- style.css
- app.js

PitchZero is an AI-powered investor pitch scoring platform. The landing page should include:
- Hero section with headline and CTA button
- Features section showing 3 key features
- How it works section with 3 steps
- Pricing section with 3 tiers
- Dark professional theme with purple accents
```

Open `index.html` in your browser to see the result.

---

## 🔧 How It Works

```
You type a prompt in Continue
        ↓
Continue sends HTTP POST to localhost:11434/api/chat
        ↓
Ollama passes request to Gemma 4 (loaded in RAM)
        ↓
Gemma 4 generates response + decides next action
        ↓
Continue acts on workspace — creates / edits files
        ↓
Loop continues until task is complete
```

> Everything runs on your machine. No data leaves. No API key required.

---

## 💡 Tips

- **Slow generation?** Switch from `gemma4:26b` to `gemma4:e4b` — lighter compute
- **Running out of RAM?** Reduce context: change `num_ctx 16384` to `num_ctx 8192` in Modelfile
- **Files not being created?** Make sure you're in **Agent mode**, not Chat mode
- **Context too small?** Increase `num_ctx` in Modelfile and recreate with `ollama create`
- **Check RAM usage:** Run `ollama ps` to see model size and context in use

---

## 📊 Tested Hardware

| Machine | RAM | GPU | Model Used | Result |
|---------|-----|-----|------------|--------|
| HP Gaming Laptop | 24 GB | Intel UHD 630 (integrated) | gemma4:26b | ✅ Works great |
| Any laptop | 16 GB | None | gemma4:e4b | ✅ Works fine |
| Workstation | 32 GB | Nvidia RTX | gemma4:31b | ✅ Best quality |

---

## 📁 Project Structure

```
your-workspace/
├── Modelfile          # Custom context configuration
├── README.md          # This file
└── projects/
    └── pitch-scorecard/
        ├── index.html
        ├── style.css
        └── app.js
```

---

## 🤝 Contributing

Found an issue or want to improve this? Pull requests welcome.

---

## 📄 License

MIT License — free to use, modify and share.

---

## 🔗 Links

[![YouTube](https://img.shields.io/badge/YouTube-NetworkCoder-red?style=for-the-badge&logo=youtube)](https://youtube.com/@kskroyal)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-black?style=for-the-badge&logo=github)](https://github.com/kskroyal)

---

<p align="center">
  <strong>Zero Cost. 100% Offline. No API Key.</strong><br/>
  <em>Built with Gemma 4 + Ollama + Continue + VS Code</em>
</p>
