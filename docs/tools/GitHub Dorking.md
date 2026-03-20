**GitHub dorking** is the practice of using GitHub’s search (and advanced query operators) to locate specific files, patterns, and exposed content across public repositories. It’s used by security researchers and maintainers for reconnaissance and discovery (both defensive and offensive), but it’s dual-use — it can reveal accidentally leaked data or be abused by attackers. Use only for authorized, ethical testing and defensive discovery. ([InfoSec Write-ups](https://infosecwriteups.com/how-i-earned-200-using-github-dorking-a-manual-and-automated-guide-to-finding-sensitive-ebbbfcad7296?utm_source=chatgpt.com "How I Earned $200 Using GitHub Dorking: A Manual and ..."))

---

## Table of contents

- What is GitHub dorking?
    
- Why it matters / risks
    
- Legal & ethical rules (short)
    
- Common GitHub search operators (cheat sheet)
    
- Safe usage example (web UI + `gh` CLI)
    
- Remediation & mitigation (what to do when you find something)
    
- Further reading & resources
    

---

## What is GitHub dorking?

GitHub dorking means crafting targeted search queries (sometimes called “dorks”) with GitHub’s advanced search operators to find files, configuration snippets, keys, endpoints, or patterns of interest across repositories. Security researchers use it to discover exposed assets or misconfigurations; attackers may use it to find secrets or vulnerabilities. ([InfoSec Write-ups](https://infosecwriteups.com/how-i-earned-200-using-github-dorking-a-manual-and-automated-guide-to-finding-sensitive-ebbbfcad7296?utm_source=chatgpt.com "How I Earned $200 Using GitHub Dorking: A Manual and ..."))

---

## Why it matters / risks

- Public repositories can unintentionally contain API keys, credentials, private configuration, or insecure code patterns. Large studies and industry reports show a significant volume of accidentally committed secrets and that attackers/abusers may scan public code for those secrets. ([arXiv](https://arxiv.org/abs/2502.01020?utm_source=chatgpt.com "RiskHarvester: A Risk-based Tool to Prioritize Secret Removal Efforts in Software Artifacts"))
    
- Finding secrets in public repos can lead to account compromise, data breaches, or supply-chain attacks. That’s why platforms and vendors provide secret-scanning and other protections. ([GitHub Docs](https://docs.github.com/code-security/secret-scanning/about-secret-scanning?utm_source=chatgpt.com "About secret scanning"))
    

---

## Legal & ethical rules (short)

- Only search public repositories unless you have explicit authorization to test private repositories.
    
- If you discover active credentials or secrets, **do not** use them. Follow responsible disclosure: notify repository owners and relevant providers, and recommend revocation/rotation. Many providers and GitHub offer formal reporting or removal processes. ([GitHub Docs](https://docs.github.com/en/site-policy/content-removal-policies/github-private-information-removal-policy?utm_source=chatgpt.com "GitHub Private Information Removal Policy"))
    

---

## Common GitHub search operators (cheat sheet)

Use these in the GitHub search box or in the Code Search endpoint (`cs.github.com`) / `gh` CLI:

- `org:ORG` — restrict to an organization.
    
- `user:USERNAME` — restrict to a user.
    
- `repo:OWNER/REPO` — restrict to a single repository.
    
- `filename:NAME` — search by file name.
    
- `path:PATH` — match file path.
    
- `in:file` / `in:path` / `in:readme` — choose where to match terms.
    
- `language:LANG` or `extension:ext` — filter by language or extension.
    
- `created:YYYY-MM-DD` / `pushed:YYYY-MM-DD` — filter by dates.
    
- `stars:>N` — filter repositories by stars.
    
- Boolean operators: `AND`, `OR`, `NOT` and parentheses for grouping.  
    These operators are part of GitHub’s code search and advanced search features. ([GitHub](https://github.com/features/code-search?utm_source=chatgpt.com "Code Search"))
    

---

## Safe usage example

> **Goal (safe / non-sensitive):** list Dockerfiles inside an organization to review container configurations.

### Web UI (quick)

1. Open `https://github.com` and sign in.
    
2. In the top search box enter:
    

```
org:octocat filename:Dockerfile
```

3. Switch the results tab to **Code** (or use Code Search at `https://cs.github.com/` to widen scope).  
    The results show `Dockerfile` occurrences across repos in `octocat`. This is harmless and useful for auditing container build practices. ([GitHub](https://github.com/orgs/community/discussions/35148?utm_source=chatgpt.com "Search for filenames · community · Discussion #35148"))
    

### `gh` CLI (code search)

If you prefer CLI, `gh` provides `search code`:

```bash
# search code across GitHub (example: Dockerfiles in an org)
gh search code 'org:octocat filename:Dockerfile' --limit 50
```

Or use specific flags where available:

```bash
gh search code 'Dockerfile' --owner octocat --limit 50
```

See `gh search code` in the GitHub CLI manual for flags and JSON output options. ([GitHub CLI](https://cli.github.com/manual/gh_search_code?utm_source=chatgpt.com "gh search code"))

---

## What to do if you find sensitive data (remediation & responsible next steps)

1. **Assume the secret is compromised.** Immediately recommend rotation/revocation of the key or credential. ([Blog | iamluminousmen](https://luminousmen.com/post/delete-sensitive-data-from-git/?utm_source=chatgpt.com "Delete Sensitive Data From Git"))
    
2. **Inform the repository owner** via a private channel (issues are public — prefer email or the project’s security contact). If the leak is high risk, use GitHub’s private information removal process or contact the vendor. ([GitHub Docs](https://docs.github.com/en/site-policy/content-removal-policies/github-private-information-removal-policy?utm_source=chatgpt.com "GitHub Private Information Removal Policy"))
    
3. **Remove the secret from history** (if you have permission to fix the repo). Use `git-filter-repo` (recommended) or BFG to purge secrets from all commits, then force-push and coordinate with contributors. Document the steps so contributors can rebase or reclone. ([GitHub Docs](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository?utm_source=chatgpt.com "Removing sensitive data from a repository"))
    
4. **Enable prevention**: enable GitHub’s secret scanning / push protection and adopt pre-commit hooks (Talisman, pre-commit, etc.) and automated scanners (GitHub Advanced Security, GitGuardian) to catch secrets before they’re committed. ([GitHub Docs](https://docs.github.com/code-security/secret-scanning/about-secret-scanning?utm_source=chatgpt.com "About secret scanning"))
    

---

## Defensive recommendations (for maintainers)

- Add pre-commit checks and secret detection (e.g., pre-commit, Talisman).
    
- Use environment config (secrets managers) instead of hardcoding secrets.
    
- Rotate keys regularly and use least privilege for tokens.
    
- Enable GitHub secret scanning and push protection for organization repos. ([GitHub Docs](https://docs.github.com/code-security/secret-scanning/about-secret-scanning?utm_source=chatgpt.com "About secret scanning"))
    

---

## Further reading & curated resources

- Practical write-ups and walkthroughs on GitHub dorking and discovery: InfoSec Writeups and Intigriti blog posts. ([InfoSec Write-ups](https://infosecwriteups.com/how-i-earned-200-using-github-dorking-a-manual-and-automated-guide-to-finding-sensitive-ebbbfcad7296?utm_source=chatgpt.com "How I Earned $200 Using GitHub Dorking: A Manual and ..."))
    
- Public collections of GitHub dorks (used for research / auditing): `techgaun/github-dorks`, `Proviesec/github-dorks`, `jcesarstef/ghhdb`. Use these responsibly (they contain search patterns and lists). ([GitHub](https://github.com/techgaun/github-dorks?utm_source=chatgpt.com "techgaun/github-dorks: Find leaked secrets via github search"))
    
- GitHub Secret Scanning docs (how to enable and what it detects). ([GitHub Docs](https://docs.github.com/code-security/secret-scanning/about-secret-scanning?utm_source=chatgpt.com "About secret scanning"))
    
- Removing sensitive data from a repository (official GitHub guide). ([GitHub Docs](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/removing-sensitive-data-from-a-repository?utm_source=chatgpt.com "Removing sensitive data from a repository"))
    

---

## Short checklist (for reviewers)

-  Only run searches against public data or with explicit permission.
    
-  If you find secrets: do **not** use them; notify owner; recommend rotation. ([GitHub Docs](https://docs.github.com/en/site-policy/content-removal-policies/github-private-information-removal-policy?utm_source=chatgpt.com "GitHub Private Information Removal Policy"))
    
-  Add automated secret scanning to CI and pre-commit. ([GitHub Docs](https://docs.github.com/en/code-security/secret-scanning/enabling-secret-scanning-features?utm_source=chatgpt.com "Enabling secret scanning features"))
    

---
