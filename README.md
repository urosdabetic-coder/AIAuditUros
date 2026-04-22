# AuditFlow AI — MVP

An ultra-modern, Apple/Vercel-inspired internal audit management platform with two AI agents:

1. **Spark AI — Drafting Assistant.** Transforms raw auditor notes into an IIA 5-C structured finding (Condition, Criteria, Cause, Effect, Recommendation).
2. **AI Compliance Checker.** Reviews a workpaper against IIA standards and firm methodology, returning color-coded insights.

## Tech Stack

- **Framework**: Next.js 14 (App Router) + TypeScript
- **Styling**: Tailwind CSS with shadcn/ui design tokens
- **UI primitives**: Radix UI + custom shadcn/ui components (Button, Card, Badge, Sheet, Avatar, Separator, Textarea)
- **Icons**: Lucide React
- **Typography**: Inter via `next/font`

## Getting started

```bash
npm install
npm run dev
```

Open <http://localhost:3000>.

## Project structure

```
app/
  layout.tsx                    Root layout with sidebar + topbar shell
  page.tsx                      Dashboard (Audit Universe, Kanban)
  globals.css                   Theme tokens, shimmer + fade-in animations
  workpapers/[id]/page.tsx      Workpaper detail (server wrapper)
  api/ai/drafting/route.ts      Mock drafting LLM endpoint (2s delay)
  api/ai/compliance/route.ts    Mock compliance LLM endpoint (1.8s delay)

components/
  layout/
    sidebar.tsx                 Fixed left navigation (Vercel-style)
    topbar.tsx                  Sticky search header with ⌘K hint
  dashboard/
    stats-row.tsx               KPI strip
    kanban-column.tsx           Column wrapper + empty state
    audit-card.tsx              Premium card with progress, risk, team
  workpaper/
    workpaper-view.tsx          Main interactive client component
    status-badge.tsx            Workpaper status pill
    drafting-skeleton.tsx       5-section shimmer loading state
    drafting-result.tsx         Rendered IIA 5-C finding
    compliance-sheet.tsx        Slide-out panel with insights
  ui/                           shadcn primitives

lib/
  utils.ts                      cn() classname merger
  mock-data.ts                  6 audits + 3 workpapers (realistic content)

types/
  index.ts                      Domain types (Audit, Workpaper, …)
```

## Design principles applied

- **Generous whitespace** (py-10, gap-3, border-border/60) instead of dense UIs
- **Refined micro-interactions**: `-translate-y-0.5` on card hover, gradient sheen on the Spark AI CTA, `animate-fade-in` with staggered delays on Kanban columns and finding sections
- **Monospace only for identifiers** (audit code, workpaper reference, rule refs) — never for body copy
- **Tabular numerals** for all counts and percentages (`tabular-nums`)
- **Color discipline**: neutrals dominate; color reserved for status (risk dots, severity chips) and never decorative

## Replacing the mocks with real LLMs

Both API routes (`app/api/ai/drafting/route.ts` and `app/api/ai/compliance/route.ts`) are isolated. To go live:

1. Add `ANTHROPIC_API_KEY` (or your provider) to `.env.local`.
2. Replace the `setTimeout` + static payload with an Anthropic `messages.create` call.
3. For drafting: pass `rawNotes` in the user turn and a system prompt that defines the IIA 5-C framework and required JSON schema.
4. For compliance: pass the full workpaper payload and a system prompt with your firm's quality rules.

The response types (`DraftedFinding`, `ComplianceInsight`) are already locked in `types/index.ts`, so the UI doesn't need to change.
