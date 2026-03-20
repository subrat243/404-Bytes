# 404Byte 🔒

> Personal security notes, tool references, and articles.

**Live site:** https://subrat243.github.io/404Byte/

Built with [MkDocs Material](https://squidfunk.github.io/mkdocs-material/).

## Categories

- **Bash** — Shell scripting and automation
- **eJPT v2** — Penetration testing certification notes
- **Linux CLI** — Command line tips and tricks
- **Metasploit** — Framework usage and modules
- **Nmap** — Scanning techniques and NSE scripts
- **Python** — Security scripting
- **Theory & Techniques** — Core concepts and methodologies
- **Tools** — Cheatsheets and references

## Adding Notes

Just create a `.md` file in the right folder and push:

```bash
# Example: add a new nmap note
echo "# Nmap Stealth Scan\n..." > docs/nmap/stealth-scan.md
git add . && git commit -m "add nmap stealth scan notes" && git push
```

The site auto-deploys within ~60 seconds via GitHub Actions.

## Adding a Blog Post

Create a file in `docs/blog/posts/` with this format:

```markdown
---
date: 2025-06-01
authors:
  - subrat243
categories:
  - CTF
tags:
  - writeup
  - web
---

# My CTF Writeup

Intro paragraph shown on blog listing page.

<!-- more -->

Full content continues here...
```

## Local Preview

```bash
pip install mkdocs-material mkdocs-awesome-pages-plugin
mkdocs serve
```

Open http://localhost:8000
