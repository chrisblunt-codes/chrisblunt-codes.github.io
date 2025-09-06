---
layout: post
title: "mailarchiver v0.2.0 released"
date: 2025-09-06
tags: [crystal, email, open-source, mailarchiver]
---

Today I released version **0.2.0** of **mailarchiver**, a lightweight Crystal application that fetches, indexes, and archives email messages via POP3.  
It stores messages as `.eml` files and indexes headers in SQLite with FTS5 for fast search.

### Added
- **CLI Fetcher**
  - Added `fetch` command with UIDL-based pipeline
  - Supports conditional `DELE` only after successful spool + DB insert
  - Introduced `Message.exists?` and `Message.insert_stub` helpers
  - Implemented atomic spool write (`.part` → rename) to `spool/incoming/`

👉 [View the code on GitHub](https://github.com/chrisblunt-codes/mailarchiver)

Next up: I’ll be working on the **importer**, which will parse headers, enrich the SQLite index, and move messages from the spool into the archive folder structure.
