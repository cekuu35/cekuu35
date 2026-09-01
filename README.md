# Cenk Kurtoglu

I audit Supabase authorization boundaries. Most of my work is finding the
cross-user and cross-tenant data leaks that a linter cannot see: a permissive
policy silently cancelling a restrictive one, a membership join that is not
isolated, or a service-role key reachable from a client path. The SQL is
valid in all three cases, which is why they survive code review and pass tests.

If you arrived here from a Postgres or Supabase thread about RLS not working,
anon key exposure, or SECURITY DEFINER bypassing your policies, start with the
free tools below. The same SQL goes red on one branch and green on the other
in about two seconds. No Docker, no Supabase account needed.

I also build and maintain Next.js, React and Node.js products; those are
further down, labeled by evidence type so you can tell public source from
live products, prototypes and private code.

Based in Turkiye

**Recently confirmed in production:** a project maintainer fixed two
vulnerabilities I reported -- an anon-reachable SECURITY DEFINER function that
let any anon-key holder read any table, bypassing RLS, and a USING (true)
policy exposing every signed-in user's email -- and credited the report in a
[public commit](https://github.com/ismailsunni/onepieceofdata/commit/bd994c7b29caaf537c123c616f4f750227bbaac0),
verified against production.

**Most recent merge:** a review-driven hardening migration for
[StudentSuite/StudyMap](https://github.com/StudentSuite/StudyMap/pull/184) --
place-type CHECK constraint, lat/lng bounds shipped as NOT VALID + VALIDATE so
the rollout cannot lock the table, plus explicit WITH CHECK on the UPDATE
policies; merged after addressing two rounds of maintainer review.

> 'Cenk identified a genuine and significant Supabase RLS issue in CrewForm and
> disclosed it privately and responsibly. His report was clear, technically
> accurate, and included practical steps to verify the issue and resolve it.
> This allowed us to confirm the problem and get a fix in place quickly. He was
> professional, constructive, and easy to work with throughout the process.'
> -- **CrewForm**

> 'I built my AI assistant Victorio with Claude and was very happy with it -- not
> having ANY idea about the security aspects of this build and what it could mean
> for my data. Then I got an email from Cenk. I put it into Claude and guess what
> Claude said: Cenk is absolutely right and 100% of this email is correct.
> Claude apologized for not thinking through security correctly. I then bought
> Cenk's checklist, and with it Claude was able to plug all my security holes. I
> am very thankful to Cenk -- he is amazing, and I highly recommend all founders
> who are not technical to talk to him.'
> -- **Stan Altshuller, Founder & CEO, [Acadia.im](https://www.acadia.im)**

## Free: Supabase RLS Security SQL Queries

Run these against your own database to check for common security gaps.

- [**RLS Production Checklist**](https://gist.github.com/cekuu35/e9bed1ff71f491842552fdb2d28cbe80) -- 7 SQL queries: tables with RLS on but no policy, USING(true) on anon, SECURITY DEFINER callable by anon, missing TO clauses
- [**Service Role Detection**](https://gist.github.com/cekuu35/ce2f79a944235ad1c4894c792f97fbdb) -- find exposed service_role keys, SECURITY DEFINER functions, and direct grants
- [**SECURITY DEFINER Audit**](https://gist.github.com/cekuu35/32a0b4c3489f8406a78712585fbc3591) -- list all SECURITY DEFINER functions, check anon EXECUTE privileges, find dynamic SQL injection vectors
- [**Two-User RLS Test (no pgTAP)**](https://gist.github.com/cekuu35/35ccb80e1fc986fab088c4b804211fbe) -- prove tenant isolation on a live database with plain psql: act as user A and B via request.jwt.claims, probe every access path, self-verify the harness catches permissive policies

Also worth running Supabase's own database linter, since it is free and catches the obvious cases.

## Supabase RLS review and debugging

I help Next.js and Supabase teams verify cross-user and cross-tenant data
boundaries with evidence-first tests. The public fixture below separates the
failure signature, policy boundary, regression check and scope limitations.

Three ways in, cheapest first:

- **Free** -- [run the isolation fixture](https://github.com/cekuu35/supabase-rls-leak-demo): one test file, red on one branch and green on the other, about two seconds, no Docker and no Supabase account.
- ** -- run the audit yourself.** [The RLS Audit Kit on Gumroad](https://cengokurtoglu.gumroad.com/l/supabase-rls-audit-kit?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_kit&utm_content=top_cta): seven commented SQL audits you run against your own catalogs -- coverage, policy conflicts, write-side WITH CHECK gaps, grants, bypass paths, Storage and Realtime -- plus a role-simulation harness wrapped in BEGIN/ROLLBACK, a 60-check workflow, and report templates. Nothing leaves your database.
- **From $99 -- I run it.** [Order on Upwork](https://www.upwork.com/services/product/2083862107074689176?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_audit&utm_content=top_cta), 2-day delivery, severity-ranked findings with reproducible proof.

If the free checks come back clean, you are done and you do not need either of the paid ones.

Arrived here from a Postgres or Supabase thread and would rather describe the
problem first? Email **cnkkurtoglu@gmail.com**. Two or three sentences is
enough, and I will tell you which of the three above applies -- including when
the answer is none of them. Send sanitized schema and policy SQL only, never
production secrets, service-role keys or customer data.

- [Read the negative-test matrix](https://github.com/cekuu35/supabase-rls-leak-demo/blob/fixed/RLS_TEST_MATRIX.md)

Recent public example of the work: in a Supabase security thread about
whether the Developer role can be prevented from reading project secrets,
I argued the boundary cannot hold -- deploy permission is transitively
read-all-secrets permission, so redacting logs filters one channel while
the attacker picks another. The reporter withdrew the proposal after that
exchange, and the thread is now producing a documentation change rather
than a redaction feature.
[Read the thread](https://github.com/supabase/supabase/discussions/48795#discussioncomment-17930908)

Start with the free checks. Supabase ships a database linter that catches RLS
switched off, and RLS switched on with no policy behind it. What a linter
cannot check is whether a policy that exists is correct -- a permissive
policy silently cancelling a restrictive one, a membership join that is not
isolated, or a service-role key reachable from a client path. That is what the
audit covers.

Send only sanitized schema and policy SQL -- never production secrets,
service-role keys or customer data. The review returns risk-ranked findings,
reproducible checks and practical policy-fix recommendations. It is not a
penetration test, certification or security guarantee.

## Common Supabase security problems I find

These are the most frequent issues in production Supabase projects:

1. **RLS policies with USING(true)** -- a permissive policy that allows all rows to be read by anyone, overriding restrictive policies
2. **SECURITY DEFINER functions callable by anon** -- bypasses RLS entirely, anon-key holders can read any table
3. **service_role key in client code** -- if NEXT_PUBLIC_ prefix is on the service_role key, it is exposed to the browser
4. **Unisolated membership joins** -- cross-tenant data leaks through joins that do not filter by user_id or org_id
5. **Write-side WITH CHECK gaps** -- INSERT and UPDATE policies that allow users to create rows under any user_id
6. **Storage policies missing object-level access control** -- anyone with the bucket name can read/download files

See the Gist links above for SQL queries that detect each of these.

## Services and digital products

Three offers are currently promoted here. Each has public evidence or a real
preview and a working platform checkout path.

### Supabase RLS Security Audit -- from $99

A fixed-price review of your Supabase authorization boundary: every table and
policy enumerated, covering SELECT, INSERT, UPDATE and DELETE policy
boundaries, anonymous and authenticated roles, USING and WITH CHECK gaps, and
cross-user or cross-tenant failure paths. You get severity-ranked findings with
reproducible proof and a remediation plan with the SQL to apply.

- [Inspect the public five-test isolation fixture](https://github.com/cekuu35/supabase-rls-leak-demo)
- [Read the scope on the service page](https://cenkkurtoglu.com/supabase-rls-security-audit?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_audit&utm_content=services)
- [Order on Upwork -- from $99, 2-day delivery](https://www.upwork.com/services/product/2083862107074689176?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_audit&utm_content=services)

Contracting and payment stay on Upwork. The audit covers findings and
recommendations for one authorized project; implementation can be scoped
separately after the evidence is clear.

Already live with real users and still shipping? The one-time audit is a
snapshot; a single new table, policy or migration can silently reopen a hole
afterwards. That is the gap the monthly membership covers.

### RLS Guard -- $50/month, ongoing

A human re-audits your Supabase project every month instead of once: a fresh
read of every table's RLS status, the role each policy really targets
(including the anonymous role), SECURITY DEFINER functions, service_role
exposure, and the Storage / Realtime gaps that table RLS misses. You get a
short written report with the exact fix SQL, priority help by email during the
month, and a re-check after each deploy so a new migration does not silently
reopen a hole.

- [Read the seven-surface scope on Gumroad -- $50/month, cancel anytime](https://cengokurtoglu.gumroad.com/l/yvdwv?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_guard&utm_content=services)
- [Start free with the release checklist first](https://gist.github.com/cekuu35/e9bed1ff71f491842552fdb2d28cbe80)

Best if your app is live and holds real user data and you do not want to
become a security expert to keep shipping safely. If you are pre-launch with
no users, or happy to run the audits yourself, the one-time Audit Kit below is
the cheaper, correct tool.

### Supabase RLS Audit Kit -- $29 one-time

The same audit as a set of files you run yourself. Seven commented SQL
audits against your own catalogs: RLS coverage, policy conflicts,
write-side WITH CHECK gaps, grants, bypass paths, Storage, Realtime,
and a role-simulation harness that wraps its probes in BEGIN/ROLLBACK.
Plus a 60-check workflow and report and remediation templates.

- [Run the free five-test fixture first](https://github.com/cekuu35/supabase-rls-leak-demo)
- [Buy the kit on Gumroad -- $29](https://cengokurtoglu.gumroad.com/l/supabase-rls-audit-kit?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_kit&utm_content=services)

For projects you own or are authorized to test. It is a configuration
review workflow, not a penetration test or a security guarantee.

Already blocked on a specific table, policy, or payment integration and
need it resolved rather than another checklist? Done-for-you fixes:

- [RLS 3-Table Review -- $39, 24-hour turnaround](https://cengokurtoglu.gumroad.com/l/supabase-rls-3-table-review?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_3table&utm_content=services)
- [Payment & Compliance Integration Fix -- $79, 48-hour delivery (iyzico / PayTR / Stripe / Supabase RLS)](https://cengokurtoglu.gumroad.com/l/lpoxj?utm_source=github_profile&utm_medium=readme&utm_campaign=payfix&utm_content=services)
- [Next.js + Supabase 48-Hour Launch Review -- $79](https://cengokurtoglu.gumroad.com/l/nextjs-supabase-launch-review?utm_source=github_profile&utm_medium=readme&utm_campaign=launch_review&utm_content=services)

### AI-Ready Website Audit Kit -- $49 one-time

An evidence-led 100-point operational-readiness workflow with a dependency-free Node.js scorer, evidence ledger, 19-page playbook, regression tests, and editable client-report templates.

- [Try the free 100-point scorecard](https://cekuu35.github.io/evidence-led-website-audit/scorecard.html?utm_source=github_profile&utm_medium=referral&utm_campaign=ai_audit_kit_launch&utm_content=profile_readme)
- [Review a generated sample report](https://cekuu35.github.io/evidence-led-website-audit/sample-report.html?utm_source=github_profile&utm_medium=referral&utm_campaign=ai_audit_kit_launch&utm_content=profile_readme)
- [Review and buy on Gumroad -- $49](https://cengokurtoglu.gumroad.com/l/ai-ready-website-audit-kit?utm_source=github_profile&utm_medium=referral&utm_campaign=ai_ready_audit_kit&utm_content=profile_readme)

### Next.js + Supabase Launch Checklist -- $19 one-time

An 8-page PDF with 60 practical checks covering secrets, RLS, auth, performance, SEO, reliability, monitoring, backups, environment setup, and go-live.

- [Start free -- the 10-check pre-launch pass](https://cengokurtoglu.gumroad.com/l/nextjs-supabase-10-checks-free?utm_source=github_profile&utm_medium=referral&utm_campaign=lead_magnet&utm_content=profile_readme): a 2-page PDF, pay what you want -- enter 0 to download. The fastest way to see the format before the full 60.
- [Preview real PDF pages](https://cekuu35.github.io/nextjs-supabase-checklist-preview/?utm_source=github_profile&utm_medium=referral&utm_campaign=launch_checklist&utm_content=profile_readme)
- [Review and buy on Gumroad -- $19](https://cengokurtoglu.gumroad.com/l/xjnmxt?utm_source=github_profile&utm_medium=referral&utm_campaign=launch_checklist&utm_content=profile_readme)

These products provide tools and guidance, not import-success, ranking, security, compliance, sales, or revenue guarantees.

**Creators, consultants and agencies:** if your audience ships Supabase or Next.js apps, you can earn a **50% commission** promoting any of these -- 30-day tracking, nothing owed unless it converts. [Join the affiliate program](https://cengokurtoglu.gumroad.com/affiliates).

## Selected product work

| Project | Evidence | What can be evaluated |
|---|---|---|
| **Abonem** | [Live App Store listing](https://apps.apple.com/tr/app/abonem-subscription-tracker/id6776748341) -- [Public support repository](https://github.com/cekuu35/abonem-store) | A live iOS and iPadOS finance app for subscription and recurring-bill tracking, with optional Premium in-app purchases. |
| **Renderivo** | [Live product -- free signup](https://www.renderivo.com/?utm_source=github_profile&utm_medium=readme&utm_campaign=renderivo_relaunch) -- [Sanitized engineering case study](https://github.com/cekuu35/cekuu35/blob/main/RENDERIVO_CASE_STUDY.md) | A live text-to-3D SaaS, open for signups: type a prompt, get a GLB with an in-browser preview; a free account includes one Rapid generation. The case study documents reliability and credit-integrity decisions. The full source is also licensed as a commercial AI-SaaS starter -- see the snapshot below. |
| **llms.txt Generator** | [Public source](https://github.com/cekuu35/llms-txt-generator) -- [Live Apify Actor](https://apify.com/nacred_corner/llms-txt-generator) | A JavaScript Actor that discovers pages through sitemaps or same-domain links, extracts main content, and writes llms.txt, optional llms-full.txt, and per-page metadata. |
| **Wholesale ARV** | [Live no-login prototype](https://wholesale-arv.vercel.app/app) | A demo-mode real-estate analysis flow for conservative ARV, repair, and MAO work. The deployment visibly labels its fallback when live data keys are unavailable. Source is private. |
| **React Story Editor** | [Live interaction proof](https://react-story-editor-proof.vercel.app/) | A self-initiated single-layer editor with drag/keyboard positioning, text size/weight/preset-color controls, safe-area guidance, and local 1080 x 1920 PNG export. It is not presented as commissioned client work. |
| **Free Next.js CV Template** | [Public source](https://github.com/cekuu35/free-nextjs-cv-template) -- [Live demo](https://free-nextjs-cv-template.vercel.app/) | A config-driven, multi-page Next.js 15 / React 19 / TypeScript / Tailwind CSS 3 template with a validated contact integration point. |

## Renderivo engineering snapshot

The [sanitized case study](https://github.com/cekuu35/cekuu35/blob/main/RENDERIVO_CASE_STUDY.md) explains its recoverable asynchronous-job and transactional-credit design.

**The full source is available as a commercial AI-SaaS starter** -- Next.js (App Router) + Paddle checkout with signed webhooks, a metered credit wallet, a referral system, and an HMAC-signed fal.ai generation pipeline, with Firebase Admin auth and daily cron reconciliation. It ships with .env.example and setup docs and contains no secrets. Email **cnkkurtoglu@gmail.com** for a commercial license.

Renderivo itself is [live with open signup](https://www.renderivo.com/?utm_source=github_profile&utm_medium=readme&utm_campaign=renderivo_relaunch&utm_content=snapshot) -- a free account includes one Rapid text-to-3D generation. What the commercial license buys is the engineered codebase, not the running business; production-scale operation is not claimed.

## Public source and demos

- [llms-txt-generator](https://github.com/cekuu35/llms-txt-generator) -- public application source and public Actor listing.
- [free-nextjs-cv-template](https://github.com/cekuu35/free-nextjs-cv-template) -- public Next.js source and a live deployment.
- [nextjs-website-templates](https://github.com/cekuu35/nextjs-website-templates) -- 20 hosted starter demos with a transparent [buyer guide](https://github.com/cekuu35/nextjs-website-templates/blob/main/BUYER_GUIDE.md). Review the documented scope before considering the commercial source package.

## Current stack

Next.js -- React -- TypeScript -- JavaScript -- Node.js -- Firebase / Firestore -- Apify / Crawlee -- Vercel

## Contact

**cnkkurtoglu@gmail.com** -- the fastest way to reach me. No platform account
needed, and a question is not an order: if the honest answer is that you do not
need an audit, that is the answer you will get.

[LinkedIn](https://www.linkedin.com/in/cenk-kurtoglu) -- [Product catalog](https://cekuu35.github.io/) -- [Website](https://cenkkurtoglu.com)
