---
layout: post
title: "mailarchiver v0.3.0 released"
date: 2025-09-08
tags: [crystal, email, open-source, mailarchiver]
---

<style>
  html, body { background:#0d121b !important; }
</style>

Today I released version **0.3.0** of **mailarchiver**, a lightweight Crystal application that fetches, indexes, and archives email messages via POP3.  
It stores messages as `.eml` files and indexes headers in SQLite with FTS5 for fast search.

### Added
- **CLI Importer**
  - Added `import` command
  - Introduced `Messages.update_headers_from` helper
  - Introduced `Attachment.insert` helper
  - Implemented message archiving to `data/archive/yyyy-mm-dd`
  - Implemented bad message helper

👉 [View the code on GitHub](https://github.com/chrisblunt-codes/mailarchiver)

Next up: I’ll be working on **searching**, which will use FTS to quickly find messages from the CLI.
