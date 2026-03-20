<div align="center">

```
 ██╗  ██╗ ██████╗ ██╗  ██╗██████╗ ██╗   ██╗████████╗███████╗
 ██║  ██║██╔═████╗██║  ██║██╔══██╗╚██╗ ██╔╝╚══██╔══╝██╔════╝
 ███████║██║██╔██║███████║██████╔╝ ╚████╔╝    ██║   █████╗  
 ╚════██║████╔╝██║╚════██║██╔══██╗  ╚██╔╝     ██║   ██╔══╝  
      ██║╚██████╔╝     ██║██████╔╝   ██║      ██║   ███████╗
      ╚═╝ ╚═════╝      ╚═╝╚═════╝    ╚═╝      ╚═╝   ╚══════╝
```

**Personal security notes, tool references & articles**

[![Deploy](https://github.com/subrat243/404Byte/actions/workflows/deploy.yml/badge.svg)](https://github.com/subrat243/404Byte/actions/workflows/deploy.yml)
[![Pages](https://img.shields.io/badge/Live-subrat243.github.io%2F404Byte-cyan?style=flat-square&logo=github)](https://subrat243.github.io/404Byte/)
[![MkDocs](https://img.shields.io/badge/Built%20with-MkDocs%20Material-blueviolet?style=flat-square)](https://squidfunk.github.io/mkdocs-material/)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)](LICENSE)

[**→ Visit the Site**](https://subrat243.github.io/404Byte/)

</div>

---

## What is this?

**404Byte** is my personal knowledge base for security research, CTF writeups, and tool references. Built with MkDocs Material and auto-deployed to GitHub Pages on every push — zero manual steps.

---

## Categories

The notes are organized into several core directories. They cover foundational command line skills, scripting, penetration testing methodologies and tool usage, and core theoretical concepts. There is also a dedicated space for full-length articles and CTF writeups.

---

## How it works

```
Write .md file  →  git push  →  GitHub Actions  →  MkDocs builds  →  Live in ~60s
```

The `mkdocs-awesome-pages` plugin reads your folder structure automatically — no config changes needed when you add new categories.

---

## Adding Notes

```bash
# Add a note to an existing category
echo "# My Note" > docs/nmap/stealth-scan.md

# Add a brand new category
mkdir docs/wireshark
echo "# Wireshark" > docs/wireshark/index.md

# Push — site updates automatically
git add . && git commit -m "add: wireshark notes" && git push
```

## Writing a Blog Post

Create a file in `docs/blog/posts/` with this frontmatter:

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

# My Post Title

Short preview shown on the blog listing page.

<!-- more -->

Full content continues here...
```

---

## Local Preview

```bash
# Install dependencies
pip install mkdocs-material mkdocs-awesome-pages-plugin

# Serve locally with live reload
mkdocs serve
```

Open [http://localhost:8000](http://localhost:8000)

---

## Tech Stack

- [MkDocs Material](https://squidfunk.github.io/mkdocs-material/) — theme and framework
- [mkdocs-awesome-pages](https://github.com/lukasgeiter/mkdocs-awesome-pages-plugin) — auto navigation from folder structure
- [GitHub Actions](https://github.com/features/actions) — CI/CD pipeline
- [GitHub Pages](https://pages.github.com/) — free hosting

---

<div align="center">
<sub>Made by <a href="https://github.com/subrat243">subrat243</a> · <a href="LICENSE">MIT License</a></sub>
</div>