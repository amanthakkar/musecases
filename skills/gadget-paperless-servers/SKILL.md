---
name: gadget-paperless-servers
description: >-
  Search and read documents in an existing self-hosted Paperless-ngx archive
  using Muse Home Link — full-text search over OCR'd content, filtered by
  correspondent, tag, type, and date. Read-only by default; document content
  is sensitive household data; uploads only on explicit request, deletes
  never. Not Paperless setup, not archive administration, and not a reason
  to expose the archive to the internet.
---

# Paperless-ngx Servers — Document Search and Reading via the Local API

Use this skill when the home already runs Paperless-ngx (typically on a
Raspberry Pi or home server) and the user asks where a document is, when a
letter arrived, or what a filed document says. Paperless is the household's
paper memory: scanned mail, statements, letters, and records, OCR'd and
filed under correspondents, document types, and tags. This skill searches
that archive instead of asking the user to dig through a filing cabinet —
and treats everything it finds as what it is: the household's private paper.

## Identify the Setup

- Confirm the Paperless-ngx base URL on the home network and that the API
  answers: an authenticated `GET /api/documents/?page_size=1` returns a
  count and a first result. A reachable web UI alone does not prove API
  access — prove it with an authenticated read before answering questions.
- Inventory before answering: `GET /api/correspondents/`,
  `GET /api/document_types/`, and `GET /api/tags/` list how this archive is
  actually organised. Correspondent and tag names are the owner's filing
  system, not generic categories — match the user's words ("the gas bill")
  to the archive's real correspondents and types, and confirm the mapping
  when more than one fits.
- Agree the scope with the user up front: which Paperless user this skill
  acts as, and that answers stay inside what that user may see. An archive
  usually holds a whole household's documents; whose documents the skill may
  surface is part of the scope, not an afterthought.

## Guided Setup Check

If Paperless or API access is missing, stop and give the user these steps —
do not install Paperless, create users, or mint tokens on their behalf.

1. **Paperless-ngx installed and owned.** The owner runs Paperless-ngx on
   their hardware, can sign in as an administrator, and the archive,
   correspondents, and tags already exist.
2. **A dedicated, limited user — not the admin.** Create (in the admin UI)
   or pick a non-admin user for this skill. Current Paperless-ngx versions
   support per-object permissions; grant view access to the document sets
   the user wants Muse to search, and nothing broader by default.
3. **Credential, owner-created.** An API token belonging to that limited
   user — the owner generates it from their own profile / token settings,
   and calls use it as `Authorization: Token <token>`. The token is a
   secret with the run of that user's archive: never paste it into chat, a
   skill file, or a repo.
4. **Stay local.** Reach Paperless over the home network via Home Link. Do
   not ask the user to port-forward or publish the archive to the internet
   for this skill — a document archive is the last thing a home should
   expose; remote access they already run is their choice, not a setup step.

## Prerequisites

Read and follow the Home Link networking and safety rules in `~/docs/devices/home_link.md`.

- The guided setup check passes: base URL reachable, authenticated read
  works (`GET /api/documents/` returns 200), filing vocabulary confirmed
  with the user.
- Know the two dates before answering anything time-shaped: `created` is
  the date *on* the document (which the owner or Paperless's matching
  chose), `added` is when it entered the archive. "The March statement" is
  a `created` question; "what arrived last week" is an `added` question.
  Confusing them files the answer in the wrong month.

## Workflow

1. **Search first, read second.** Full-text search runs over the OCR'd
   `content` of every document: `GET /api/documents/?query=<terms>`.
   Narrow with the archive's own structure before reading anything —
   `correspondent__id`, `document_type__id`, `tags__id__in`,
   `created__date__gt` / `created__date__lt` — and only open the documents
   that survive the filters. A search that returns 40 letters has not yet
   answered "the letter from the bank"; it has found the haystack.
2. **Answer from metadata, quote content sparingly.** Title, correspondent,
   type, tags, and dates (`GET /api/documents/<id>/`) answer most
   questions. When the user asks what a document *says*, read its `content`
   and quote the minimum excerpt that answers — a due date, an amount, a
   deadline — not the whole page. Full OCR text is for the owner reading
   their own document on request, not for narrating unprompted.
3. **Redact identifiers in summaries.** Account, policy, ID, and case
   numbers appear throughout an archive's documents. When reporting a
   document's contents, refer to it by correspondent and date and give the
   figures asked for; do not read full identifier numbers into chat or
   summaries unless the user explicitly asks for that number from that
   document.
4. **Download — only on explicit request.** `GET
   /api/documents/<id>/download/` returns the file (the archived version;
   `?original=true` for the original). A download moves a sensitive
   document out of the archive: do it only when the user asks for that
   document, deliver it through the path they chose, and never generate a
   public or shareable link to it.
5. **Upload — only on explicit request, then verify.** Adding a document
   is `POST /api/documents/post_document/` with the file and optional
   title/correspondent/tags. Consumption is asynchronous: check
   `GET /api/tasks/` for the consume task, then find the document by search
   before claiming it was filed. Paperless detects exact duplicates by
   checksum — if a consume task reports a duplicate, say so; do not force
   a second copy in.
6. **Never administer the archive:** no deletes (not even "tidying"
   duplicates), no correspondent/tag/type creation or renaming, no
   permission or workflow changes, no reprocessing from a skill session.
   Filing-structure changes belong to the owner in the Paperless UI; if a
   document looks misfiled, report it and let the owner fix it.

## Verify the Result

- A search returning nothing is a finding, not a failure to hide: report
  the terms and filters used, and offer the broader search (fewer filters,
  different correspondent) rather than silently widening it — a widened
  search can cross into document sets the user did not mean to open.
- After an upload, the proof is the document retrievable by search with
  the expected title and correspondent — not the 200 from the POST, and
  not the consume task alone.
- OCR is evidence with error bars: names, amounts, and dates read from
  `content` can be mis-OCRed, especially on scans of scans. When a figure
  is load-bearing (a deadline, a payment amount), say it came from OCR and
  offer the page image or download so the user can eyeball the original.

## Limits

- Read-only by default; downloads and uploads are explicit, per-request,
  and scoped to documents the user named. This skill searches an archive —
  it is not a pipe for bulk export, and a request to "send me everything
  tagged X" is a conversation about scope and sensitivity first, not a loop
  over downloads.
- **Document content is sensitive household data.** An archive concentrates
  exactly the documents a household least wants narrated: IDs, financial
  statements, medical and legal letters, other members' mail. Report the
  asking user's own documents; another member's documents are theirs to
  share, not this skill's to summarise.
- No credential handling beyond using the owner-provided token through the
  Home Link path; no creating users, tokens, or permissions on anyone's
  behalf; no exposing the archive beyond the local network.
- Search quality is the archive's, not the skill's: a document that was
  never consumed, or whose OCR failed, will not be found by content search.
  If a document the user is sure exists does not appear, say the archive
  may not hold a searchable copy — do not invent its contents from the
  title.

## Sources

- [Paperless-ngx — API documentation](https://docs.paperless-ngx.com/api/)
- [Paperless-ngx — usage (search, correspondents, tags, document types)](https://docs.paperless-ngx.com/usage/)
- [Paperless-ngx — configuration and tokens](https://docs.paperless-ngx.com/configuration/)
