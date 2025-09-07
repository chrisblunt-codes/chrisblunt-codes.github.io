---
layout: post
title: "Fixing Crystal MIME: Attachments + Charset Handling"
date: 2025-09-06
tags: [crystal, email, mime, crystal-mime, open-source, mailarchiver]
---

When you start dealing with real-world email, you quickly learn that the happy-path examples never survive contact with the messy stuff people actually send.

I ran head-first into this while building my **MailVault** project (a lightweight mail archiver). I needed to parse `.eml` files, extract text cleanly, and save attachments reliably. The stock [`crystal-mime`](https://github.com/aluminumio/crystal-mime) shard handled basic text bodies, but it fell a bit short on two fronts:

* **Attachments weren’t surfaced properly** — non-text parts were ignored or lumped into the body.
* **Charset handling was missing** — anything outside UTF-8/ASCII turned into garbage characters.

So, I forked the library and patched in the features I needed.

---

## What Was Missing

1. **Attachments**

   * Multiparts weren’t recursed deeply enough.
   * Binary parts (PDFs, images, etc.) weren’t exposed as real attachments.
   * Filenames encoded in headers were ignored.

2. **Charset Decoding**

   * Emails in ISO-8859-1, Windows-1252, and other common charsets broke.
   * Headers with encoded words (RFC2047) weren’t decoded consistently.
   * RFC2231 “filename\*=” values (percent-encoded, charset-aware) were dropped.

This meant a huge chunk of real mail was either unreadable or missing critical context.

---

## What I Added

Here’s what my fork introduces:

* ✅ Recursive multipart parsing (including nested multiparts).
* ✅ `attachments` array with filename, content type, and decoded content.
* ✅ RFC2231 support (`filename*=` with percent-encoding and charset).
* ✅ RFC2047 fallback for legacy encoded headers.
* ✅ Charset detection + decoding:
* ✅ UTF-8 / ASCII → pass through.
* ✅ ISO-8859-1, Windows-1252 → mapped to proper Unicode.
* ✅ Others → left raw with a warning.
* ✅ Safe header lookups (`from`, `to`, `subject` all optional).

---

## Example

Before (stock `crystal-mime`):

```crystal
email = MIME.mail_object_from_raw(raw)
pp email.body_text   #=> "Attachment: <garbled characters>"
pp email.attachments #=> nil
```

After (my fork):

```crystal
email = MIME.mail_object_from_raw(raw)

puts email.body_text
#=> "Please see attached document."

pp email.attachments
# [Attachment(
#   filename: "report.pdf",
#   content_type: "application/pdf",
#   size: 23456
# )]
```

Now attachments show up as first-class citizens, and text in common charsets actually renders correctly.

---

## Why It Matters

This isn’t just cosmetic. For anything like:

* Archiving emails,
* Searching across message bodies,
* Indexing attachments, or
* Building compliance tools,

…you need predictable parsing. Losing filenames or botching encodings makes your data worthless. 

---

## The Fork

I’ve pushed this to my fork of `crystal-mime`:

👉 [GitHub: chrisblunt-codes/crystal-mime](https://github.com/chrisblunt-codes/crystal-mime)

If you need these features today, grab the fork. If you maintain the original shard, maybe consider merging.

---

## Lessons Learned

* **Email is ugly.** The RFCs are a jungle, and real messages bend the rules constantly.
* **Crystal is fast.** Once the logic is in place, MIME parsing runs like lightning.
* **Solve your pain first.** I didn’t set out to “fix Crystal MIME” — I just needed my project to work. That motivation was enough.

This fork is just one piece of a larger puzzle: my **MailVault** project, where I’m building a robust, searchable mail archive with Crystal, SQLite, and FTS5. More on that soon.

---

*Posted by [Chris Blunt](https://www.chrisblunt.codes) — tinkering with Crystal, email, and the messy overlap between them.*


