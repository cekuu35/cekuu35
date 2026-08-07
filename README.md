# Cenk Kurtoğlu

I audit Supabase authorization boundaries. Most of my work is finding the
cross-user and cross-tenant leaks that a linter cannot see: a permissive
policy silently cancelling a restrictive one, a membership join that is not
isolated, or a service-role key reachable from a client path. The SQL is
valid in all three cases, which is why they survive review and pass tests.

If you arrived here from a Postgres or Supabase thread, the audit is the
first section below and the reproducible fixture is public — the same test
suite goes red on one branch and green on the other in about two seconds.

I also build and maintain Next.js, React and Node.js products; those are
further down, labeled by evidence type so you can tell public source from
live products, prototypes and private code.

Based in Türkiye

## Supabase RLS review and debugging

I help Next.js and Supabase teams verify cross-user and cross-tenant data
boundaries with evidence-first tests. The public fixture below separates the
failure signature, policy boundary, regression check and scope limitations.

- [Run the Supabase RLS isolation fixture](https://github.com/cekuu35/supabase-rls-leak-demo)
- [Read the negative-test matrix](https://github.com/cekuu35/supabase-rls-leak-demo/blob/fixed/RLS_TEST_MATRIX.md)
- [Order the audit on Upwork — from $99, 2-day delivery](https://www.upwork.com/services/product/2083862107074689176?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_audit&utm_content=top_cta)

Recent public example of the work: in a Supabase security thread about
whether the Developer role can be prevented from reading project secrets,
I argued the boundary cannot hold — deploy permission is transitively
read-all-secrets permission, so redacting logs filters one channel while
the attacker picks another. Supabase's security review reached the same
conclusion, and the thread is now producing a documentation change rather
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
| **Renderivo** | [Public product surface](https://www.renderivo.com/) · [Sanitized engineering case study](https://github.com/cekuu35/cekuu35/blob/main/RENDERIVO_CASE_STUDY.md) | The public site exposes clearly labeled interactive GLB samples. The case study documents reliability and credit-integrity decisions without publishing operational details. The production source remains private; unrestricted public generation is not claimed. |
| **llms.txt Generator** | [Public source](https://github.com/cekuu35/llms-txt-generator) · [Live Apify Actor](https://apify.com/nacred_corner/llms-txt-generator) | A JavaScript Actor that discovers pages through sitemaps or same-domain links, extracts main content, and writes `llms.txt`, optional `llms-full.txt`, and per-page metadata. |
| **Wholesale ARV** | [Live no-login prototype](https://wholesale-arv.vercel.app/app) | A demo-mode real-estate analysis flow for conservative ARV, repair, and MAO work. The deployment visibly labels its fallback when live data keys are unavailable. Source is private. |
| **React Story Editor** | [Live interaction proof](https://react-story-editor-proof.vercel.app/) | A self-initiated single-layer editor with drag/keyboard positioning, text size/weight/preset-color controls, safe-area guidance, and local 1080 × 1920 PNG export. It is not presented as commissioned client work. |
| **Free Next.js CV Template** | [Public source](https://github.com/cekuu35/free-nextjs-cv-template) · [Live demo](https://free-nextjs-cv-template.vercel.app/) | A config-driven, multi-page Next.js 15 / React 19 / TypeScript / Tailwind CSS 3 template with a validated contact integration point. |

## Renderivo engineering snapshot

Renderivo's production repository remains private. The [sanitized case study](https://github.com/cekuu35/cekuu35/blob/main/RENDERIVO_CASE_STUDY.md) explains its recoverable asynchronous-job and transactional-credit design at a deliberately non-operational level. It omits provider identities, exact limits, security parameters, internal schemas, deployment identifiers, and proprietary source.

This is a pre-revenue product foundation. Unrestricted public generation, completed live billing, and production-scale operation are not claimed.

## Public source and demos

- [llms-txt-generator](https://github.com/cekuu35/llms-txt-generator) — public application source and public Actor listing.
- [free-nextjs-cv-template](https://github.com/cekuu35/free-nextjs-cv-template) — public Next.js source and a live deployment.
- [nextjs-website-templates](https://github.com/cekuu35/nextjs-website-templates) — 20 hosted starter demos with a transparent [buyer guide](https://github.com/cekuu35/nextjs-website-templates/blob/main/BUYER_GUIDE.md). Review the documented scope before considering the commercial source package.

## Current stack

Next.js · React · TypeScript · JavaScript · Node.js · Firebase / Firestore · Apify / Crawlee · Vercel

## Contact

[LinkedIn](https://www.linkedin.com/in/cenk-kurtoglu) · [Product catalog](https://cekuu35.github.io/) · [Website](https://cenkkurtoglu.com)
