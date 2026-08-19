# OpenClaw Memory Demo

A reproducible demo comparing OpenClaw's default memory behaviour against the Mem0 plugin

The same two-session task is run twice — once with no memory plugin, once with Mem0. Session 1 is identical in both cases. Session 2 shows the difference.

---

## Prerequisites

- An Anthropic API key
- [Ollama](https://ollama.ai) installed locally
- Docker or Podman (for Qdrant)

---

## Setup

**1. Start Qdrant**

```bash
docker run -d --name qdrant -p 6333:6333 qdrant/qdrant
```

**2. Pull Ollama models** (required for Mem0 — run once)

```bash
ollama pull llama3.1:8b
ollama pull nomic-embed-text
```

**3. Install and onboard OpenClaw**

```bash
npm install -g openclaw@latest
openclaw onboard \
  --anthropic-api-key YOUR_API_KEY \
  --install-daemon \
  --non-interactive \
  --accept-risk
```

---

## The prompts

Use these two prompts for both tests.

**Session 1:**

```bash
Research the top free weather APIs available today and recommend the best one for a hobby project called Beacon. I want something with a generous free tier that's easy to use.
```

Type `/new` to end the session, then send the Session 2 prompt.

**Session 2:**

```bash
Continue working on the Beacon project — start scaffolding it using the API we decided on.
```

---

## Test 1 — Default (no memory plugin)

```bash
cp config/default/openclaw.json ~/.openclaw/openclaw.json
openclaw gateway restart
openclaw chat
```

Send Session 1, then `/new`, then Session 2. The agent will have no memory of Session 1 and will ask you to repeat the details.

---

## Test 2 — With Mem0

Install the plugin (once):

```bash
openclaw plugins install @mem0/openclaw-mem0
```

```bash
cp config/mem0/openclaw.json ~/.openclaw/openclaw.json
openclaw gateway restart
openclaw chat
```

Send the same Session 1 prompt, then `/new`. Check what Mem0 captured:

```bash
openclaw mem0 list --user-id demo-user
```

Note: The mem0 plugin may take a few seconds (~10s)  to update its memory database. Wait for a few seconds before checking the "mem0 list", retry a few times if needed. Then send the Session 2 prompt. The agent will recall the project and API choice without being told. Since these are rapdily evolving projects, in case you still do not get the memories being created, check the mem0 documentation on the latest configuration options and functionality for memory auto-creation.

---
