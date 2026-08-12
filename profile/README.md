# Vantra Design

**Governance for design systems, in CI.**

A design system fails quietly. A prop is renamed and forty consumers break. A component ships and nobody adopts it. A token is deprecated and no one knows who still uses it. Vantra is being built to answer those questions automatically, on every pull request.

Around that sits the rest of the practice: a maturity self-assessment, an accessibility extension that proposes fixes rather than only reporting faults, and ten single-purpose design utilities.

---

## Repositories

Five repositories. One publishes to npm today; the rest are pre-release.

| Repository | What it is | State |
| --- | --- | --- |
| [**vantra-core**](https://github.com/vantradesign/vantra-core) | The primitives: AST parsing, component-graph construction, design-token schema parsing. No rules, no scoring, no CLI. | **Published** — [`@vantra-design/core`](https://www.npmjs.com/package/@vantra-design/core) `0.1.2`, ESM + CJS + types, npm provenance |
| [**vantra-governance-suite**](https://github.com/vantradesign/vantra-governance-suite) | Five CI governance tools plus a Nuxt dashboard and a Supabase schema, in one Turborepo monorepo. | Scaffolded — tool packages are typed placeholders; dashboard and schema exist, auth does not |
| [**vantra-maturity-check**](https://github.com/vantradesign/vantra-maturity-check) | Design-system maturity self-assessment: 24 sourced questions across documentation, versioning, governance and adoption, as a CLI and a static web app. | Feature-complete, unpublished — CLI, engine and web app all build |
| [**vantra-a11y-fixer**](https://github.com/vantradesign/vantra-a11y-fixer) | Browser extension that finds contrast and ARIA issues *and* proposes the concrete fix. Entirely local, `connect-src 'none'`. | v0.1 in development — not yet in the Chrome Web Store |
| [**vantra-site**](https://github.com/vantradesign/vantra-site) | [vantra.design](https://vantra.design) — the product site, plus ten browser-based design utilities. | Pre-launch — built and prerendered; editorial imagery outstanding |

---

## The five governance tools

One Turborepo, in [`vantra-governance-suite`](https://github.com/vantradesign/vantra-governance-suite).

| Tool | Answers | State |
| --- | --- | --- |
| `health-cli` | Is the design system getting healthier or worse? | placeholder |
| `breaking-change-analyzer` | If I merge this, what breaks downstream? | placeholder |
| `deprecation-orchestrator` | What is deprecated, and who still uses it? | placeholder |
| `zero-usage-gate` | What did we ship that nobody adopted? | placeholder |
| `ownership-mapper` | Who owns this component? | placeholder |

*Placeholder* is precise: each package exports a `GovernanceTool` with the final shape and an empty result, so the contract, the wiring and the dashboard are real while the rules are not yet written. Logic arrives one dedicated task per tool.

All five ask variations of one question — *what does this change do to the people downstream?* — so they all need the same input: a parsed component graph. That graph is expensive to build, so it is built **once per CI run** by `@vantra-design/core`, behind a single import boundary (`packages/shared/src/core.ts`), and passed by reference to all five tools. Sharing that one parse is why the five tools live in one repository rather than five.

Supporting workspaces: `packages/shared` (Zod contract, typed Supabase client, the core wrapper) and `apps/dashboard` (Nuxt 4 · Tailwind v4 · shadcn-vue). The dashboard is **not deployed**; its server routes have no auth gate yet, and the Supabase RLS policies are still permissive placeholders.

---

## What you can use today

| Surface | How to reach it |
| --- | --- |
| `@vantra-design/core` | `pnpm add @vantra-design/core` — Node ≥ 18.18 |
| Maturity Check CLI | `npx @vantra-design/maturity-check` *(pending first publish)* |
| Ten design utilities | `vantra.design/tools` — contrast, aspect ratio, font pairing, type scale, spacing scale, shade & tint, easing curves, unit converter, radius & shadow, `clamp()` |
| a11y-fixer extension | build from source, load unpacked |

---

## Design principles

- **Deterministic output.** Results are sorted, paths are repository-relative with POSIX separators, and component ids are stable. A graph built on macOS is byte-identical to one built on a Linux runner — which is what makes two runs diffable to detect drift.
- **One source of truth.** Parsing lives in `vantra-core` and nowhere else. Tools consume it; they never reimplement it.
- **Warnings, not exceptions.** One malformed token file must not fail a pipeline governing an entire design system.
- **Multi-framework.** Vue SFCs, React (function, `forwardRef`, `memo`, class), and plain TypeScript. Tokens from DTCG, Style Dictionary, plain JSON and CSS custom properties.
- **Local-first where the data is yours.** The Maturity Check keeps answers in `localStorage` and exports JSON; the a11y extension is barred from the network by its own manifest. Neither has an account, a backend or telemetry.

---

## Toolchain

Node 24 on CI (`vantra-core` supports ≥ 18.18 at runtime), pnpm 11, TypeScript in strict mode everywhere. Vue and Nuxt for every UI. Every repo runs lint, typecheck, test and build in GitHub Actions; releases go through changesets.

---

## Naming

The GitHub organization is **`vantradesign`**; the npm scope is **`@vantra-design`** (with a hyphen).

---

## License

Each project is licensed individually; the licence is stated in its repository.

| Repository | Licence | Why |
| --- | --- | --- |
| `vantra-core` | [AGPL-3.0-only](https://www.gnu.org/licenses/agpl-3.0.en.html) | governance engine — improvements stay open |
| `vantra-governance-suite` | [AGPL-3.0](https://www.gnu.org/licenses/agpl-3.0.en.html) | same |
| `vantra-maturity-check` | MIT | meant to be forked and re-catalogued |
| `vantra-a11y-fixer` | [MPL-2.0](https://mozilla.org/MPL/2.0/) | matches bundled `axe-core`, which is MPL-2.0 with Exhibit B |
| `vantra-site` | MIT (code) | editorial copy, imagery and the Vantra name are all rights reserved |
