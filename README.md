<div align="center">

```text
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

[**→ Visit the Site 🚀**](https://subrat243.github.io/404Byte/)

</div>

---

## 🚀 What is this?

**404>Bytes** is my personal knowledge base for security research, CTF writeups, and tool references. Built with MkDocs Material and auto-deployed to GitHub Pages on every push — zero manual steps.

---

## ⚙️ How it works

The deployment pipeline is fully automated. You just write markdown, and GitHub Actions takes care of the rest.

```mermaid
graph TD
    %% Define styles
    classDef write fill:#0f5132,stroke:#198754,color:#ffffff,stroke-width:1px
    classDef step fill:#212529,stroke:#495057,color:#ffffff,stroke-width:1px
    classDef build fill:#3f2071,stroke:#6610f2,color:#ffffff,stroke-width:1px
    classDef live fill:#995c00,stroke:#fd7e14,color:#ffffff,stroke-width:1px
    classDef feature fill:#1a1d20,stroke:#343a40,color:#ffffff,stroke-width:1px

    %% Flow
    Write["✍️ <b>You write .md files</b><br/><small>docs/nmap/scan.md</small>"]:::write
    Push["🚀 <b>git push</b><br/><small>to main branch</small>"]:::step
    Actions["⚙️ <b>GitHub Actions</b><br/><small>deploy.yml triggers</small>"]:::step
    Build["🔨 <b>MkDocs builds site</b><br/><small>.md → HTML + CSS</small>"]:::build
    Live["🌐 <b>GitHub Pages live</b><br/><small>~60 seconds later</small>"]:::live

    Write --> Push --> Actions --> Build --> Live
    
    %% Features Subgraph
    subgraph "✨ MkDocs Material Generates"
        F1["🧭 <b>Auto navigation</b><br/><small>folders → sidebar</small>"]:::feature
        F2["🔍 <b>Full-text search</b><br/><small>instant, offline</small>"]:::feature
        F3["📝 <b>Blog plugin</b><br/><small>posts sorted by date</small>"]:::feature
        F4["🏷️ <b>Tags page</b><br/><small>auto-grouped</small>"]:::feature
        F5["🌙 <b>Dark theme</b><br/><small>custom neon CSS</small>"]:::feature
        F6["💻 <b>Code highlight</b><br/><small>100+ languages</small>"]:::feature
    end

    Build -.->|runs| F1
    Build -.->|runs| F2
    Build -.->|runs| F3
    Build -.->|runs| F4
    Build -.->|runs| F5
    Build -.->|runs| F6
```

---

## 📂 Categories

The notes are organized into logical directories. They cover foundational command line skills, scripting, penetration testing methodologies and tool usage, and core theoretical concepts. There is also a dedicated space for full-length articles and CTF writeups mapped seamlessly via MkDocs' awesome pages plugin.

- 💻 **Foundations**
- 🛡️ **Penetration Testing**
- 📚 **Techniques**
- 🛠️ **Tools**
- 📝 **Articles**

---

## 📝 Adding Notes

```bash
# Add a note to an existing category
echo "# My Note" > docs/nmap/stealth-scan.md

# Add a brand new category
mkdir docs/wireshark
echo "# Wireshark" > docs/wireshark/index.md

# Push — site updates automatically
git add . && git commit -m "add: wireshark notes" && git push
```

---

## ✍️ Writing a Blog Post

Create a file in `docs/blog/posts/` with the required Frontmatter:

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

## 💻 Local Preview

Do you want to preview the site locally before pushing?

```bash
# Install dependencies
pip install mkdocs-material mkdocs-awesome-pages-plugin

# Serve locally with live reload
mkdocs serve
```

Open [http://localhost:8000](http://localhost:8000) inside your web browser.

---

## 🛠️ Tech Stack

- [**MkDocs Material**](https://squidfunk.github.io/mkdocs-material/) — theme and framework
- [**mkdocs-awesome-pages**](https://github.com/lukasgeiter/mkdocs-awesome-pages-plugin) — auto navigation from folder structure
- [**GitHub Actions**](https://github.com/features/actions) — CI/CD pipeline
- [**GitHub Pages**](https://pages.github.com/) — free hosting

---

<div align="center">
<sub>Made by <a href="https://github.com/subrat243">subrat243</a> · <a href="LICENSE">MIT License</a></sub>
</div>