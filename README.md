# Cenk Kurtoğlu

I audit Supabase authorization boundaries. Most of my work is finding the
cross-user and cross-tenant leaks that a linter cannot see: a permissive
policy silently cancelling a restrictive one, a membership join that is not
isolated, or a service-role key reachable from a client path. The SQL is
valid in all three cases, which is why they survive review and pass tests.

If you arrived here from a Postgres or Supabase thread, start with the free
fixture in the first section — the same test suite goes red on one branch and
green on the other in about two seconds. The $29 kit below it is the same
audit as SQL you run yourself, which is what most people want.

I also build and maintain Next.js, React and Node.js products; those are
further down, labeled by evidence type so you can tell public source from
live products, prototypes and private code.

Based in Türkiye

**Recently confirmed in production:** a project maintainer fixed two
vulnerabilities I reported — an anon-reachable `SECURITY DEFINER` function that
let any anon-key holder read any table, bypassing RLS, and a `USING (true)`
policy exposing every signed-in user's email — and credited the report in a
[public commit](https://github.com/ismailsunni/onepieceofdata/commit/bd994c7b29caaf537c123c616f4f750227bbaac0),
verified against production.

> "Cenk identified a genuine and significant Supabase RLS issue in CrewForm and
> disclosed it privately and responsibly. His report was clear, technically
> accurate, and included practical steps to verify the issue and resolve it.
> This allowed us to confirm the problem and get a fix in place quickly. He was
> professional, constructive, and easy to work with throughout the process."
> — **CrewForm**

## Supabase RLS review and debugging

I help Next.js and Supabase teams verify cross-user and cross-tenant data
boundaries with evidence-first tests. The public fixture below separates the
failure signature, policy boundary, regression check and scope limitations.

Three ways in, cheapest first:

- **Free** — [run the isolation fixture](https://github.com/cekuu35/supabase-rls-leak-demo): one test file, red on one branch and green on the other, about two seconds, no Docker and no Supabase account. Also worth running Supabase's own database linter before anything here, since it is free and catches the obvious cases.
- **$29 — run the audit yourself.** [The RLS Audit Kit on Gumroad](https://cengokurtoglu.gumroad.com/l/supabase-rls-audit-kit?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_kit&utm_content=top_cta): seven commented SQL audits you run against your own catalogs — coverage, policy conflicts, write-side `WITH CHECK` gaps, grants, bypass paths, Storage and Realtime — plus a role-simulation harness wrapped in `BEGIN … ROLLBACK`, a 60-check workflow, and report templates. Nothing leaves your database.
- **From $99 — I run it.** [Order on Upwork](https://www.upwork.com/services/product/2083862107074689176?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_audit&utm_content=top_cta), 2-day delivery, severity-ranked findings with reproducible proof.

If the free checks come back clean, you are done and you do not need either of the paid ones.

Arrived here from a Postgres or Supabase thread and would rather describe the
problem first? Email **cnkkurtoglu@gmail.com**. Two or three sentences is
enough, and I will tell you which of the three above applies — including when
the answer is none of them. Send sanitized schema and policy SQL only, never
production secrets, service-role keys or customer data.

- [Read the negative-test matrix](https://github.com/cekuu35/supabase-rls-leak-demo/blob/fixed/RLS_TEST_MATRIX.md)

Recent public example of the work: in a Supabase security thread about
whether the Developer role can be prevented from reading project secrets,
I argued the boundary cannot hold — deploy permission is transitively
read-all-secrets permission, so redacting logs filters one channel while
the attacker picks another. The reporter withdrew the proposal after that
exchange, and the thread is now producing a documentation change rather
than a redaction feature.
[Read the thread](https://github.com/supabase/supabase/discussions/48795#discussioncomment-17930908)

Start with the free checks. Supabase ships a database linter that catches RLS
switched off, and RLS switched on with no policy behind it. What a linter
cannot check is whether a policy that *exists* is correct — a permissive
policy silently cancelling a restrictive one, a membership join that is not
isolated, or a service-role key reachable from a client path. That is what the
audit covers.

Send only sanitized schema and policy SQL—never production secrets,
service-role keys or customer data. The review returns risk-ranked findings,
reproducible checks and practical policy-fix recommendations. It is not a
penetration test, certification or security guarantee.

## Services and digital products

Three offers are currently promoted here. Each has public evidence or a real
preview and a working platform checkout path.

### Supabase RLS Security Audit — from $99

A fixed-price review of your Supabase authorization boundary: every table and
policy enumerated, covering SELECT, INSERT, UPDATE and DELETE policy
boundaries, anonymous and authenticated roles, USING and WITH CHECK gaps, and
cross-user or cross-tenant failure paths. You get severity-ranked findings with
reproducible proof and a remediation plan with the SQL to apply.

- [Inspect the public five-test isolation fixture](https://github.com/cekuu35/supabase-rls-leak-demo)
- [Read the scope on the service page](https://cenkkurtoglu.com/supabase-rls-security-audit?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_audit&utm_content=services)
- [Order on Upwork — from $99, 2-day delivery](https://www.upwork.com/services/product/2083862107074689176?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_audit&utm_content=services)

Contracting and payment stay on Upwork. The audit covers findings and
recommendations for one authorized project; implementation can be scoped
separately after the evidence is clear.

### Supabase RLS Audit Kit — $29 one-time

The same audit as a set of files you run yourself. Seven commented SQL
audits against your own catalogs: RLS coverage, policy conflicts,
write-side `WITH CHECK` gaps, grants, bypass paths, Storage, Realtime,
and a role-simulation harness that wraps its probes in `BEGIN … ROLLBACK`.
Plus a 60-check workflow and report and remediation templates.

- [Run the free five-test fixture first](https://github.com/cekuu35/supabase-rls-leak-demo)
- [Buy the kit on Gumroad — $29](https://cengokurtoglu.gumroad.com/l/supabase-rls-audit-kit?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_kit&utm_content=services)

For projects you own or are authorized to test. It is a configuration
review workflow, not a penetration test or a security guarantee.

### AI-Ready Website Audit Kit — $49 one-time

An evidence-led 100-point operational-readiness workflow with a dependency-free Node.js scorer, evidence ledger, 19-page playbook, regression tests, and editable client-report templates.

- [Try the free 100-point scorecard](https://cekuu35.github.io/evidence-led-website-audit/scorecard.html?utm_source=github_profile&utm_medium=referral&utm_campaign=ai_audit_kit_launch&utm_content=profile_readme)
- [Review a generated sample report](https://cekuu35.github.io/evidence-led-website-audit/sample-report.html?utm_source=github_profile&utm_medium=referral&utm_campaign=ai_audit_kit_launch&utm_content=profile_readme)
- [Review and buy on Gumroad — $49](https://cengokurtoglu.gumroad.com/l/ai-ready-website-audit-kit?utm_source=github_profile&utm_medium=referral&utm_campaign=ai_ready_audit_kit&utm_content=profile_readme)

### Next.js + Supabase Launch Checklist — $19 one-time

An 8-page PDF with 60 practical checks covering secrets, RLS, auth, performance, SEO, reliability, monitoring, backups, environment setup, and go-live.

- [Preview real PDF pages](https://cekuu35.github.io/nextjs-supabase-checklist-preview/?utm_source=github_profile&utm_medium=referral&utm_campaign=launch_checklist&utm_content=profile_readme)
- [Review and buy on Gumroad — $19](https://cengokurtoglu.gumroad.com/l/xjnmxt?utm_source=github_profile&utm_medium=referral&utm_campaign=launch_checklist&utm_content=profile_readme)

These products provide tools and guidance, not import-success, ranking, security, compliance, sales, or revenue guarantees.

## Selected product work

| Project | Evidence | What can be evaluated |
|---|---|---|
| **Abonem** | [Live App Store listing](https://apps.apple.com/tr/app/abonem-subscription-tracker/id6776748341) · [Public support repository](https://github.com/cekuu35/abonem-store) | A live iOS and iPadOS finance app for subscription and recurring-bill tracking, with optional Premium in-app purchases. |
| **Renderivo** | [Public product surface](https://www.renderivo.com/) · [Sanitized engineering case study](https://github.com/cekuu35/cekuu35/blob/main/RENDERIVO_CASE_STUDY.md) | The public site exposes clearly labeled interactive GLB samples. The case study documents reliability and credit-integrity decisions. The full source is available as a commercial AI-SaaS starter — see the Renderivo engineering snapshot below. |
| **llms.txt Generator** | [Public source](https://github.com/cekuu35/llms-txt-generator) · [Live Apify Actor](https://apify.com/nacred_corner/llms-txt-generator) | A JavaScript Actor that discovers pages through sitemaps or same-domain links, extracts main content, and writes `llms.txt`, optional `llms-full.txt`, and per-page metadata. |
| **Wholesale ARV** | [Live no-login prototype](https://wholesale-arv.vercel.app/app) | A demo-mode real-estate analysis flow for conservative ARV, repair, and MAO work. The deployment visibly labels its fallback when live data keys are unavailable. Source is private. |
| **React Story Editor** | [Live interaction proof](https://react-story-editor-proof.vercel.app/) | A self-initiated single-layer editor with drag/keyboard positioning, text size/weight/preset-color controls, safe-area guidance, and local 1080 × 1920 PNG export. It is not presented as commissioned client work. |
| **Free Next.js CV Template** | [Public source](https://github.com/cekuu35/free-nextjs-cv-template) · [Live demo](https://free-nextjs-cv-template.vercel.app/) | A config-driven, multi-page Next.js 15 / React 19 / TypeScript / Tailwind CSS 3 template with a validated contact integration point. |

## Renderivo engineering snapshot

The [sanitized case study](https://github.com/cekuu35/cekuu35/blob/main/RENDERIVO_CASE_STUDY.md) explains its recoverable asynchronous-job and transactional-credit design.

**The full source is available as a commercial AI-SaaS starter** — Next.js (App Router) + Paddle checkout with signed webhooks, a metered credit wallet, a referral system, and an HMAC-signed fal.ai generation pipeline, with Firebase Admin auth and daily cron reconciliation. It ships with `.env.example` and setup docs and contains no secrets. Email **cnkkurtoglu@gmail.com** for a commercial license.

This is a pre-revenue product foundation: unrestricted public generation and production-scale operation are not claimed. What you buy is the engineered codebase, not a running business.

## Public source and demos

- [llms-txt-generator](https://github.com/cekuu35/llms-txt-generator) — public application source and public Actor listing.
- [free-nextjs-cv-template](https://github.com/cekuu35/free-nextjs-cv-template) — public Next.js source and a live deployment.
- [nextjs-website-templates](https://github.com/cekuu35/nextjs-website-templates) — 20 hosted starter demos with a transparent [buyer guide](https://github.com/cekuu35/nextjs-website-templates/blob/main/BUYER_GUIDE.md). Review the documented scope before considering the commercial source package.

## Current stack

Next.js · React · TypeScript · JavaScript · Node.js · Firebase / Firestore · Apify / Crawlee · Vercel

## Contact

**cnkkurtoglu@gmail.com** — the fastest way to reach me. No platform account
needed, and a question is not an order: if the honest answer is that you do not
need an audit, that is the answer you will get.

[LinkedIn](https://www.linkedin.com/in/cenk-kurtoglu) · [Product catalog](https://cekuu35.github.io/) · [Website](https://cenkkurtoglu.com)
