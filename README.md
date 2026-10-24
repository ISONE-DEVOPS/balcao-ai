# Balcão AI

> The customer speaks Kriolu. The AI organizes the order. The merchant decides what was agreed.

An order desk for small shops in Cabo Verde, where orders arrive as a stream of chat messages in Cape Verdean Creole (Kriolu): an item in one message, a quantity change in the next, a forgotten size, a delivery request with no address. Balcão AI follows the conversation, builds a draft order where every field links to the message that justifies it, and makes doubts visible instead of resolving them silently. Nothing is approved without the merchant.

Built for the Meta AI Developer Hackathon (Oct 26 – Nov 4, 2026), Indie track, theme "Build for Your World".

## Status

| Date | What |
|------|------|
| Oct 24–25 | Data only: mock catalog, test corpus format, consent form. No product code. |
| Oct 26 → | Product code starts at kickoff. |

## Design decisions

1. **Kriolu as people actually write it.** Input is accepted in free spelling (`katxupa`, `cachupa`, `catchupa`). We do **not** normalize to ALUPEC: it adds complexity without improving the demo, and normalization errors would hide where the model fails.
2. **Spelling variants live in the catalog, not in the model.** Each product carries a list of synonyms. Product lookup is deterministic; the model proposes, code matches.
3. **The model never does arithmetic.** Prices come only from `data/catalogo.json`; totals are computed by code.
4. **No invented facts.** A missing address, an ambiguous "afinal duas", an item not in the catalog: each becomes an open question on the draft, never a guess.
5. **Approval is explicit and fragile.** Any change after the merchant approves sends the order back for re-approval.
6. **One variety first.** The core test set is the Santiago variety, reviewed by native speakers. Other islands (e.g. São Vicente) are an extra, separately reported test, never presented as coverage.

## Out of scope

Real WhatsApp integration, payments, real customer data, and any code from Pagali. The demo runs in a web chat that imitates a messaging app. A "generate payment link" button may appear as roadmap only; it does nothing.

## What we will measure

- Orders interpreted correctly without intervention, and after merchant review
- Contradictions detected
- Errors that reached review
- Time for the merchant to correct and approve

No numbers will be published before the tests run.

## Repository layout

```
data/
  catalogo.json          mock shop (Kantina di Nha Bia): 20 products, fixed fictitious prices,
                         spelling variants as `aliases` on products and variants
  corpus/
    escrito.jsonl        written test messages, one per line, grouped by `conversa` and `seq`,
                         spelling reviewed by a native speaker; `esperado` is the target output
    audio/
      metadados.csv      one row per recording: speaker info, human transcription, expected order
      CONSENTIMENTO.md   consent form every speaker signs before recording
      README.md          how recordings are named and stored
```

## Day-one checks

- Official rules: what counts as pre-existing work.
- Meta model access, audio support and cost. Voice stays in scope only if it passes a usefulness threshold on consented recordings.
- Function calling in Muse Spark. Fallback: the model returns structured JSON and code executes the lookups.
