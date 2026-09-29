# dectailwindcss

[Tailwind CSS](https://tailwindcss.com/)-specific rules for
[declint](https://github.com/thyrgle/declint) — class-usage hygiene in
the templates ESLint-based tooling cannot reach: plain HTML,
Django/Jinja templates, ERB, Vue, Svelte. Complements
prettier-plugin-tailwindcss (which sorts) and the Tailwind build (which
knows the class universe).

## Rules

24 fixtures across 8 rules. Severities: error = silent bug,
warning/hint/info = style and migration.

**Correctness**

| Rule | Severity | Catches |
|------|----------|---------|
| `duplicate-class` | error | the same class twice in one `class="..."` — a silent no-op, usually an editing accident |
| `contradicting-display` | error | `hidden flex` — conflicting display utilities, last-wins silently (per breakpoint: `hidden md:flex` is fine) |
| `contradicting-spacing` | warning | same padding/margin axis twice (`p-2 p-4`) — per variant, per axis |

**Hygiene**

| Rule | Severity | Catches |
|------|----------|---------|
| `double-space-in-class` | hint | `"btn  primary"` — consecutive spaces inside class |
| `trailing-space-in-class` | hint | `"btn "` — invisible dirt |

**Opinion (info — delete if you lean on these)**

| Rule | Severity | Catches |
|------|----------|---------|
| `arbitrary-value` | info | `[margin-top:8px]`-style arbitrary values bypass the design system |
| `important-modifier` | info | `!` important modifier — fix the specificity instead |

**Tailwind v4 readiness (info)**

| Rule | Severity | Catches |
|------|----------|---------|
| `v4-renamed-shadow` | info | bare `shadow` — removed in v4, use `shadow-sm` |
| `v4-renamed-rounded` | info | bare `rounded` — removed in v4, use `rounded-sm` |
| `v4-renamed-ring` | info | bare `ring` — removed in v4, use `ring-3` |
| `outline-none-v4` | info | `outline-none` — renamed to `outline-hidden` |

## Install

```sh
declint install gh:thyrgle/dectailwindcss          # into this project (vendored, reviewed, committed)
declint install -g gh:thyrgle/dectailwindcss       # or user-global
```

Then import it from your project config. **Note: imported fragments
cannot declare `languages` — your top-level config owns that.** Filter
to the template languages you use:

```yaml
version: 1
languages: [html, javascript, vue, svelte, erb]
import:
  - global:thyrgle/dectailwindcss
```

## Honest ceiling

* **No unknown-class detection** — validating that a class exists
  requires the full Tailwind universe and your theme. The v4 build and
  eslint-plugin-tailwindcss own that.
* **No sorting** — prettier-plugin-tailwindcss owns canonical ordering.
* **No conditional-class analysis** — `className={cond ? "a" : "b"}`
  logic is AST territory.

## Testing

The ruleset embeds its own fixtures:

```sh
declint test --config declint.yaml
```

## Trust note

Rules may contain Lua — installing a ruleset is running code.
Vendor-first exists so you review exactly what runs. Every rule in
`declint.yaml` is a short, readable function.
