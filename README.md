# HomoNet — a draft

A working name for a part of the web where what you read was, with good
reason to believe it, made by people who stand behind it. This document is a
first sketch of the idea: what it can promise, how it could work, what it
would be built from, and where it will fail.

## 1. The premise

It is not possible to prove that a text, image or program was made by a
human. Detectors are unreliable, get worse as models improve, and are
biased against non-native writers and plain styles. A system that promises
"100 % human" will either lie or reject honest people.

So the question changes from **"is this human?"** to **"who stands behind
this, what did it cost them to say so, and what happens if they are
wrong?"**. That question can be answered.

The model is the phone network rather than a detector. You trust that an
SMS comes from a real person not because the text looks human, but because:

- a number costs something and is tied to some identity,
- a sender cannot cheaply send a million messages,
- carriers have rules, are regulated, and can cut each other off,
- abuse is traceable, even if not always punished.

Kurzgesagt and Wikipedia show the other half: named responsibility,
visible sources, an editorial process, and a public record of changes.

The promise is therefore:

> Everything here was published by an accountable person, under a declared
> process, through a provider that answers for it. Mistakes happen, and
> they are visible and correctable.

"Not slop" is judged by effort and intent (does it say something specific,
is it sourced, did someone care), not by whether a tool was used.

## 2. Five layers

```
┌──────────────────────────────────────────────────────────────┐
│ 5. Consequences   stake, strikes, vouchers share the loss     │
│ 4. Providers      federated "stewards", charter, de-peering   │
│ 3. Review         AI screening → human sample → appeal        │
│ 2. Provenance     signed content, declared AI use, sources    │
│ 1. Identity       one person ≈ one costly, private identity   │
└──────────────────────────────────────────────────────────────┘
          all of it over ordinary HTTPS, DNS and web standards
```

### 1. Identity: costly, unique, private

An author identity must be expensive to create in bulk but cheap for one
honest person. Several entry routes, any one is enough:

| Route | Cost to the person | Cost to a spammer |
|---|---|---|
| Small one-time fee (e.g. 5–10 €) | low | scales linearly |
| Vouched by 2–3 existing members | a social favour | needs real accomplices |
| National eID / EU digital identity wallet | none | very high |
| In-person check at a library, school, club | an hour | very high |

The provider learns "this is a unique person who passed route X", not who
they are. Selective disclosure and blind signatures (as in the EU wallet
and anonymous credential schemes) make that possible. Authors can publish
under a pseudonym; the pseudonym is still bound to one stake.

The strongest anti-slop signal is **rate**. A person does not write fifty
considered articles a day. Every identity has a publishing budget that
grows slowly with good standing.

### 2. Provenance: signed and declared

Each published item carries a manifest (compatible with C2PA where it
applies):

- author key signature and provider countersignature,
- timestamps and a link to the previous version,
- sources and citations as structured data, not just links in text,
- a **declared assistance level**:

| Level | Meaning |
|---|---|
| 0 | No machine help beyond spelling |
| 1 | Translation, grammar, accessibility (alt text drafts, captions) |
| 2 | Machine-assisted research or structure, human-written text |
| 3 | Machine-drafted, substantially human-edited and checked |

Level 3 content is allowed but labelled; wholly generated content is not
accepted. Lying about the level is the main offence the system punishes,
more than the use itself. This keeps honest disabled writers, translators
and non-native speakers inside.

Optional process evidence (draft history, revision count, time spent) can
strengthen a claim. It is never required, because it is invasive and
fakeable.

### 3. Review: AI as a filter, people as the judge

AI is useful here as a first line, not as a verdict:

1. **Automated screening** on submission: near-duplicates across the
   network, mass-produced patterns from one identity or a ring of them,
   fluency without any specific claim, fabricated citations (check that
   sources exist and say what is cited), mismatch with the declared level.
2. **Human review** of everything flagged plus a random sample of the rest,
   by reviewers who are themselves members with standing.
3. **Appeal** to a different reviewer, with the reasoning written down.

Scores and decisions are logged (see 4). A screening model that flags too
many honest people is a defect to fix, not a cost to accept.

### 4. Providers: a network of trust, not one owner

Content is hosted by **stewards**: publishers, universities, libraries,
co-operatives, communities. Like phone carriers or email providers they
interoperate, and like certificate authorities they can lose trust.

- A steward signs a public **charter**: identity rules, review process,
  response times, data handling.
- A steward publishes to an append-only **transparency log** (the model is
  Certificate Transparency): what was published, what was removed and why,
  review statistics.
- Stewards peer with each other. A steward that lets slop through is
  first warned, then its content is shown with a warning, then de-peered.
  Nobody needs permission from a central body to stop trusting a steward;
  a reader's client can choose its own trust list.
- No steward can quietly alter or remove content: removal is itself a
  logged, signed event.

A small foundation (Wikimedia-like) can maintain the charter and the
default trust list, but the protocol must work without it.

### 5. Consequences: skin in the game

- Strikes reduce publishing budget, then suspend the identity.
- A vouched member caught lying costs their vouchers some standing. Enough
  to make vouching deliberate, not enough to make it unthinkable.
- A suspended person can come back through a slower route; permanent bans
  are for coordinated abuse only.
- Everything is proportional and appealable. The system will make
  mistakes; the goal is that they are visible and reversible.

## 3. The format: simpler than HTML, built on the web

The web is hard to implement because HTML, CSS and JavaScript carry thirty
years of compatibility. HomoNet content does not need that. It needs two
kinds of thing:

### Documents (most content)

A small, declarative, semantic document format. EVG is a good candidate:
it already has flex, grid, a CSS subset, a display list and hosts for SVG,
PDF, GL and native.

Rules that make it trustworthy and accessible by construction:

- **Semantics first.** Headings, paragraphs, lists, figures, quotes,
  tables, citations, and a small set of layout containers. Reading order is
  document order.
- **The schema rejects** an image without a text alternative, a table
  without headers, a control without a label, a document without a
  language.
- **RTL and mixed scripts:** every node can carry `lang` and `dir`; layout
  uses only logical properties (`start`/`end`, `inline`/`block`), never
  `left`/`right`; text uses the Unicode bidi algorithm; mirrored layouts
  come for free.
- **No scripts in documents.** Interactivity comes from apps.
- **Signed as a whole**, including images and fonts, so what you read is
  what the author published.
- **Maps to HTML one way.** A gateway renders any document to clean,
  accessible HTML, so ordinary browsers and search engines can read it.
  The reverse (arbitrary HTML into HomoNet) is not a goal.

### Apps

When something needs to run, it runs as **WASM or JavaScript modules** in a
sandbox with a declared capability manifest (network hosts, storage,
camera, …), shown to the user before first run, similar to mobile
permissions.

- UI is declared in the same markup family as documents (the Rave
  direction: HTML-shaped markup, routed, checked without a browser), so
  the accessibility tree comes from the markup, not from ARIA added later.
- The same package runs in a HomoNet reader, inside a normal web page
  (WASM + DOM host), and as a mobile app (EVG's iOS/Android hosts).
- Apps are signed and reviewed like any other content; their manifest is
  in the transparency log.

### Transport

Plain HTTPS, DNS, and content addressing (a hash per item) for caching and
mirroring. No new network layer is needed; the novelty is in the
signatures, the logs and the trust lists.

## 4. Where it will fail

- **Humans laundering machine text.** A real, vouched person can paste
  generated text and declare level 0. Rate limits, sampling and the social
  cost of being caught reduce this; nothing removes it.
- **Cost excludes people.** Any fee shuts out someone. Vouching, fee
  waivers and institutional sponsorship (libraries, schools) must be first
  class routes, not exceptions.
- **Detectors are unfair.** Plain, formulaic or non-native writing looks
  "machine-like". This is why AI only flags and people decide.
- **Accountability vs privacy.** Tying identity to content endangers
  whistle-blowers and people under hostile governments. Pseudonyms with
  private credentials help; some stewards may need stricter privacy
  charters.
- **Capture.** A default trust list becomes a gatekeeper. Readers must be
  able to change it, and the charter process must be open.
- **Boredom.** A trusted web nobody visits helps no one. It has to be
  good to read and easy to publish to from day one.

## 5. A first version that could be built

The smallest version that tests the idea:

1. **One steward**, invite-only, identities by vouching plus optional fee.
2. **Signed documents** in the EVG document format with the manifest
   above and a schema check for accessibility and `lang`/`dir`.
3. **A transparency log** (append-only, publicly readable) of publish and
   remove events.
4. **A reader**: EVGBrowser already renders EVG, is built around an
   allowlist, and runs in a browser, on desktop and on mobile. Its
   allowlist becomes the trust list of stewards, and it verifies
   signatures before rendering.
5. **An HTML gateway** so every document has a normal URL anyone can open.
6. **Screening** with an AI model for duplicates and citation checks only,
   plus manual review of everything in the first months.

Apps, federation between stewards and eID routes come after the document
path works.

## 6. Open questions

- What is the right fee, and who keeps it (steward, foundation, a
  deposit returned after a year of good standing)?
- How much must a steward disclose about review decisions without exposing
  authors?
- Should readers be able to see an author's declared level history, or
  only the current item's?
- Is the EVG document format expressive enough for long-form articles with
  footnotes, maths and tables, or does it need a document profile of its
  own?
- How does quoting and remixing work when both parts are signed?

## 7. On the name

"HomoNet" states the goal but reads badly in English and promises more than
can be kept. The idea is closer to accountability and craft than to
biology. Some directions:

- **Longhand** — written by hand, with care.
- **Hearth** — a place where people you can trust gather.
- **Commons** / **Trusted Commons** — shared, governed, public.
- **Signed Web** — descriptive, technical.
- **Provenance** — says exactly what the system offers.

The repository keeps the working name until one is chosen.
