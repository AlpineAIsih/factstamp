# FactStamp

Paid verification for agents: cited, dated answers about companies and about numbers you did not
compute yourself, each returned with a **signed receipt that verifies offline**. Not comfort, and not
a fetch. No model sits in the verdict path, and **unknown is a first-class answer**.

Live service: https://agents.factstamp.dev

## What this repository is

The published **machine interface** of the FactStamp service, mirrored here so it can be read,
diffed and cited without a wallet:

| File | What it is |
|---|---|
| [`openapi.json`](openapi.json) | The full OpenAPI 3.1 description of every route, fetched from the live service. |
| [`llms.txt`](llms.txt) | The plain-language map of the service: what each tool costs, what it returns, what it refuses to claim. |
| [`coverage.json`](coverage.json) | Which registers are read, per jurisdiction, section by section, with the price of each door. |

The service's private source is **not** published here. The three files above are the interface we
serve to the public, and they are the source of truth if anything in this README disagrees with them.

## Routes

| Route | Purpose | Free |
|---|---|---|
| `/mcp` | MCP endpoint — the tool surface agents call. | — |
| `/health` | Service status: name, network, judge and registry versions. | yes |
| `/openapi.json` | Machine description of every route. | yes |
| `/llms.txt` | The service explained for readers. | yes |
| `/coverage.json` | Per-jurisdiction coverage and prices. | yes |
| `/samples` | Example answers, so you can see the shape before paying. | yes |

Paid routes include `/citation`, `/package`, `/check`, `/entity`, `/entity-file`, `/bulk`,
`/document`, `/watch`, `/watch/portfolio`, and `/verify` for receipt checking. Prices are published,
per call, in the files above rather than in prose here.

## Published prices at the time of writing

Taken from `llms.txt` as served:

- figure door — US$0.05
- entity door — US$0.10
- entity file — US$0.438119 (33p in the UK, at the dated rate carried on the answer and the receipt)
- citation — US$0.02, package lookup — US$0.02
- standing work: watch US$0.50, whole document in one call US$0.25, bulk at a flat US$0.12 (one cent an item)
- company packages: US$2.00 and US$10.00, one-time, from the human page at `/company`

If a number here ever disagrees with `llms.txt` or `openapi.json` as served, the served file wins.

## Receipts

Every paid answer carries a receipt signed with **Ed25519** over the exact findings and the file's
hash, so a third party can verify it **offline** against the published issuer and key document — no
call back to us, and no trust in our uptime. A receipt proves *that FactStamp said this, on this
date*. It does not prove that the register itself is right, and nothing in this service claims it
does.

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
