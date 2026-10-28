# FactStamp

Paid verification for agents, and a **public record** of every paid answer. Each call returns a cited,
dated finding with a **signed receipt you can verify offline**, without asking us. Each settled call is
written into a public log, and each day's root is stamped onto the **Base blockchain** whenever that day
held a payment from someone other than us. When a check has to stay confidential there is a **private
option**: a flat **US$25** on top of the call price seals the request, so the world can see that a call
happened and prove it later, but never learn what was asked. No model sits in the verdict path, and
**unknown is a first-class answer**.

Live service: https://agents.factstamp.dev

## What this repository is

The published **machine interface** of the FactStamp service, mirrored here so it can be read,
diffed and cited without a wallet:

| File | What it is |
|---|---|
| [`openapi.json`](openapi.json) | The full OpenAPI 3.1 description of every route, fetched from the live service. |
| [`llms.txt`](llms.txt) | The plain-language map of the service: what each tool costs, what it returns, what it refuses to claim. |
| [`coverage.json`](coverage.json) | Which registers are read, per jurisdiction, section by section, with the price of each door. |
| [`server.json`](server.json) | The MCP server manifest: name, version, and the remote endpoint agents connect to. |

The service's private source is **not** published here. The files above are the interface we serve to
the public, and they are the source of truth if anything in this README disagrees with them.

## Routes

| Route | Purpose | Free |
|---|---|---|
| `/mcp` | MCP endpoint — the tool surface agents call. | — |
| `/health` | Service status: name, network, judge and registry versions. | yes |
| `/openapi.json` | Machine description of every route. | yes |
| `/llms.txt` | The service explained for readers. | yes |
| `/coverage.json` | Per-jurisdiction coverage and prices. | yes |
| `/samples` | Example answers, so you can see the shape before paying. | yes |
| `/ledger/root` | The public record: today's root, the last 30 daily roots, and each day's verdict. | yes |
| `/ledger/zero-knowledge` | The private-request option: what it is, what it publishes, what it costs. | yes |

Paid routes include `/citation`, `/package`, `/check`, `/entity`, `/entity-file`, `/bulk`,
`/document`, `/watch`, `/watch/portfolio`, and `/verify` for receipt checking. Prices are published,
per call, in the files above rather than in prose here.

## Published prices at the time of writing

Taken from `/health` and `/llms.txt` as served:

- figure door — US$0.05
- **entity door — US$0.10 to US$0.25, set by the depth of the country you ask about**: US$0.10 for a
  country publishing 1–4 verifiable items, US$0.15 for 5–6, US$0.20 for 7–9, US$0.25 for Australia's
  fifteen. The quote is shown in the payment challenge, before any money moves, and a country whose
  register cannot answer today is not sold at all — it is refused with no price rather than charged
  for an "I don't know".
- entity file — US$0.438119 (33p in the UK, at the dated rate carried on the answer and the receipt)
- citation — US$0.02, package lookup — US$0.02
- standing work: watch US$0.50, whole document in one call US$0.25, bulk at a flat US$0.12
- company packages: US$2.00 and US$10.00, one-time, from the human page at `/company`

If a number here ever disagrees with `llms.txt` or `openapi.json` as served, the served file wins.

## Receipts

Every paid answer carries a receipt signed with **Ed25519** over the exact findings and the file's
hash, so a third party can verify it **offline** against the published issuer and key document — no
call back to us, and no trust in our uptime. The key document at `/.well-known/factstamp-issuer.json`
states exactly which bytes the key identifier is derived from and carries a worked example, so the
derivation can be reproduced in any hash tool. A receipt proves *that FactStamp said this, on this
date*. It does not prove that the register itself is right, and nothing in this service claims it
does.

## The public record: the ledger, and what is stamped on Base

Every **settled paid call**, over HTTP and over MCP alike, appends one entry to a public log. The log
is an [RFC 6962](https://www.rfc-editor.org/rfc/rfc6962) Merkle tree; each UTC day's log reduces to a
single root, published at `/ledger/root` along with the last thirty days.

A day's root is written to the **Base** blockchain — one small data-only transaction, magic
`0x46535401` followed by the 32-byte root — **only when that day held at least one settlement paid by
someone other than us.** Our own test payments never anchor a day. The rule exists so that an anchor
means something: a day is stamped because a stranger paid, not because we were busy.

Days already written (verifiable on Base, chain id 8453):

| Day | Transaction |
|---|---|
| 2026-10-23 | `0x3bd942ae61c053aa7218ebfe44c25db2d9726412a67ec75073267cfb376d38f3` |
| 2026-10-25 | `0xa80b18f7d829fe535d86a700daa98f7f69f183877f158f1f0b2094dd2a94138f` |

A day that held no outside payment is published with the verdict that says so, rather than being
quietly left blank. **The honest limit:** an anchor proves the root was committed publicly on the day
the transaction landed, not on the day of the payment — the payment itself is already a public record,
because it is a transfer on the same chain. The anchor adds our side of the story: that the answers we
gave were fixed before anyone asked to see them.

## Private requests: the sealed-entry option

By default a paid call's entry is published in full — the door, the price, the verdict, and the fact
that the call happened. For a caller who needs the call itself to stay confidential there is a
**`zero_knowledge`** option, declared on the request.

- **Price: US$25.00 in addition to the call's own price.** A US$0.10 entity check declared private
  costs US$25.10. The payment challenge quotes the total before anything is paid.
- **What is published:** an `entry_id`, the day, the price, the scheme (`salted_commitment_v1`) and
  the network. Nothing else.
- **What is sealed:** the question, the answer and the verdict. The entry is committed to with a
  salted hash, so its existence and its position in the log are public while its contents are not.
- **What that buys:** you can prove later that a specific call happened on a specific day — by
  revealing the salt to whoever needs it — without ever having published what you asked.

The private option is documented at `/ledger/zero-knowledge`, with its own root at
`/ledger/zero-knowledge/root` and its entries at `/ledger/zero-knowledge/entry/{entry_id}`. It is an
option on the paid doors, not a separate service.

## What this service is not

- **Not a KYB provider**, and not a substitute for one. It does not perform customer due diligence
  on anyone's behalf.
- **Not complete by assertion.** Any register we did not consult is named on the answer as not
  consulted. A register we could not read returns `unknown`, never `not found`.
- **Not a fetcher of records you can get free.** You are paying for the citation, the check and the
  receipt, not for the register's own data.

## Issues

Corrections about a claim we make are welcome as issues here. If we have said something about a
company, a number or a register that is wrong, that is the fastest way to tell us.
