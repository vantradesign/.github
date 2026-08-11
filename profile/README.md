# Vantra Design

**Governance for design systems, in CI.**

A design system fails quietly. A prop is renamed and forty consumers break. A component ships and nobody adopts it. A token is deprecated and no one knows who still uses it. Vantra answers those questions automatically, on every pull request.

---

## Repositories

| Repository | What it is |
| --- | --- |
| [**vantra-core**](https://github.com/vantradesign/vantra-core) | The primitives: AST parsing, component-graph construction, design-token schema parsing. No rules, no scoring, no CLI. Published as [`@vantra-design/core`](https://www.npmjs.com/package/@vantra-design/core). |
| [**vantra-governance-suite**](https://github.com/vantradesign/vantra-governance-suite) | Five CI governance tools plus a shared dashboard, in one Turborepo monorepo. |

---

## The five tools

| Tool | Answers |
| --- | --- |
| `health-cli` | Is the design system getting healthier or worse? |
| `breaking-change-analyzer` | If I merge this, what breaks downstream? |
| `deprecation-orchestrator` | What is deprecated, and who still uses it? |
| `zero-usage-gate` | What did we ship that nobody adopted? |
| `ownership-mapper` | Who owns this component? |

All five ask variations of one question — *what does this change do to the people downstream?* — so they all need the same input: a parsed component graph. That graph is expensive to build, so it is built **once per CI run** by `@vantra-design/core` and passed by reference to all five tools. Sharing that single parse is why the five tools live in one repository rather than five.

---

## Design principles

- **Deterministic output.** Results are sorted, paths are repository-relative with POSIX separators, and component ids are stable. A graph built on macOS is byte-identical to one built on a Linux runner — which is what makes two runs diffable to detect drift.
- **One source of truth.** Parsing lives in `vantra-core` and nowhere else. Tools consume it; they never reimplement it.
- **Warnings, not exceptions.** One malformed token file must not fail a pipeline governing an entire design system.
- **Multi-framework.** Vue SFCs, React (function, `forwardRef`, `memo`, class), and plain TypeScript. Tokens from DTCG, Style Dictionary, plain JSON and CSS custom properties.

---

## Naming

The GitHub organization is **`vantradesign`**; the npm scope is **`@vantra-design`** (with a hyphen).

---

## License

Everything here is [AGPL-3.0](https://www.gnu.org/licenses/agpl-3.0.en.html).
