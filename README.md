# Cenk Kurtoglu

I audit Supabase authorization boundaries. Most of my work is finding the
cross-user and cross-tenant data leaks that a linter cannot see: a permissive
policy silently cancelling a restrictive one, a membership join that is not
isolated, or a service-role key reachable from a client path. The SQL is valid
in all three cases, which is why they survive code review and pass tests.

I also build and maintain Next.js, React and Node.js products -- the long list
is at the bottom, labeled by evidence type.

Based in TÃ¼rkiye Â· [Website](https://cenkkurtoglu.com) Â· [Product catalog](https://cekuu35.github.io/) Â· [LinkedIn](https://www.linkedin.com/in/cenk-kurtoglu)

## Verified in production

**A maintainer fixed two vulnerabilities I reported** -- an anon-reachable
SECURITY DEFINER function that let any anon-key holder read any table,
bypassing RLS, and a `USING (true)` policy exposing every signed-in user's
email -- and credited the report in a
[public commit](https://github.com/ismailsunni/onepieceofdata/commit/bd994c7b29caaf537c123c616f4f750227bbaac0),
verified against production.

**Most recent merge:** a review-driven hardening migration for
[StudentSuite/StudyMap](https://github.com/StudentSuite/StudyMap/pull/184) --
place-type CHECK constraint, lat/lng bounds shipped as NOT VALID + VALIDATE so
the rollout cannot lock the table, plus explicit WITH CHECK on the UPDATE
policies; merged after two rounds of maintainer review.

> 'Cenk identified a genuine and significant Supabase RLS issue in CrewForm and
> disclosed it privately and responsibly. His report was clear, technically
> accurate, and included practical steps to verify the issue and resolve it.'
> -- **CrewForm**

> 'I built my AI assistant Victorio with Claude and was very happy with it --
> not having ANY idea about the security aspects of this build. Then I got an
> email from Cenk. I put it into Claude and guess what Claude said: Cenk is
> absolutely right and 100% of this email is correct. I then bought Cenk's
> checklist, and with it Claude was able to plug all my security holes. I am
> very thankful to Cenk -- I highly recommend all founders who are not
> technical to talk to him.'
> -- **Stan Altshuller, Founder & CEO, [Acadia.im](https://www.acadia.im)**

## Latest writing -- Supabase security, the practical version

- [7 ways your Supabase app still leaks data with RLS enabled](https://cenkkurtoglu.com/blog/7-supabase-rls-leak-surfaces) -- the leak surfaces that pass an "is RLS on?" glance, each with a read-only SQL check
- [Why Supabase RLS returns nothing or 42501 (auth.uid() is null)](https://cenkkurtoglu.com/blog/supabase-rls-auth-uid-null-server) -- the server request running as anon, and how to diagnose it
- [Supabase October 30: new tables throw 42501 until you GRANT](https://cenkkurtoglu.com/blog/supabase-oct30-table-grants-42501) -- the grants census, the per-table fix, and the ALTER DEFAULT PRIVILEGES gotcha
- [Your UPDATE policy passes because it has no WITH CHECK](https://cenkkurtoglu.com/blog/supabase-rls-write-leak-with-check) -- USING checks where the row starts; WITH CHECK checks where it ends
- [Supabase Storage upload fails with RLS (403)](https://cenkkurtoglu.com/blog/supabase-storage-rls-upload-403) -- works with service_role, 403s with the signed-in user: the policy and role that fix it
- [How to test Supabase RLS policies](https://cenkkurtoglu.com/blog/how-to-test-supabase-rls-policies) -- a cross-user and cross-tenant test matrix with an executable fixture
- [Supabase auth on Vercel: the double-login fix](https://cenkkurtoglu.com/blog/supabase-auth-vercel-double-login-fix) -- the SSR cookie gap in middleware.ts and the logout redirect loop it also causes

## Free checks first -- Supabase RLS security SQL

Run these against your own database. The same SQL goes red on one branch and
green on the other in about two seconds. No Docker, no Supabase account.

- [**RLS Production Checklist**](https://gist.github.com/cekuu35/e9bed1ff71f491842552fdb2d28cbe80) -- 7 SQL queries: RLS on with no policy, USING(true) on anon, SECURITY DEFINER callable by anon, missing TO clauses
- [**Service Role Detection**](https://gist.github.com/cekuu35/ce2f79a944235ad1c4894c792f97fbdb) -- exposed service_role keys, SECURITY DEFINER functions, direct grants
- [**SECURITY DEFINER Audit**](https://gist.github.com/cekuu35/32a0b4c3489f8406a78712585fbc3591) -- all definer functions, anon EXECUTE privileges, dynamic SQL injection vectors
- [**Two-User RLS Test (no pgTAP)**](https://gist.github.com/cekuu35/35ccb80e1fc986fab088c4b804211fbe) -- prove tenant isolation on a live database with plain psql

Also worth running [Supabase's own database linter](https://supabase.com/docs/guides/database/database-linter) -- free, catches the obvious cases. What it cannot check is whether a policy that exists is *correct*. That is what the audits below cover.

## The path, cheapest first

1. **Free** -- [run the isolation fixture](https://github.com/cekuu35/supabase-rls-leak-demo): one test file, red on `broken`, green on `fixed`, ~2 seconds. [Negative-test matrix](https://github.com/cekuu35/supabase-rls-leak-demo/blob/fixed/RLS_TEST_MATRIX.md).
2. **$29, you run it** -- [The RLS Audit Kit](https://cengokurtoglu.gumroad.com/l/supabase-rls-audit-kit?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_kit&utm_content=top_cta): seven commented SQL audits (coverage, policy conflicts, write-side WITH CHECK gaps, grants, bypass paths, Storage, Realtime), a role-simulation harness wrapped in BEGIN/ROLLBACK, a 60-check workflow, report templates. Nothing leaves your database.
3. **From $99, I run it** -- [Order on Upwork](https://www.upwork.com/services/product/2083862107074689176?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_audit&utm_content=top_cta): 2-day delivery, severity-ranked findings with reproducible proof. [Read the scope on the service page](https://cenkkurtoglu.com/supabase-rls-security-audit?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_audit&utm_content=top_cta). Contracting and payment stay on Upwork.

If the free checks come back clean, you are done and you do not need either of
the paid ones.

Arrived from a Postgres or Supabase thread and would rather describe the
problem first? Email **cnkkurtoglu@gmail.com** -- two or three sentences is
enough, and I will tell you which of the three above applies, including when
the answer is none of them. Send sanitized schema and policy SQL only, never
production secrets, service-role keys or customer data.

App is live with real users and still shipping? A one-time audit is a
snapshot; a single new migration can silently reopen a hole. That gap is
covered by a **monthly re-audit**: a fresh human pass over your policies and
grants, a written report with fix SQL, and a re-check after each deploy.
Email **cnkkurtoglu@gmail.com** to arrange it -- tell me the migration cadence
and I will tell you what it covers before you commit to anything.

<details>
<summary><strong>Other common Supabase security problems I find</strong></summary>

1. **RLS policies with USING(true)** -- a permissive policy allowing all rows to anyone, overriding restrictive policies
2. **SECURITY DEFINER functions callable by anon** -- bypasses RLS entirely
3. **service_role key in client code** -- NEXT_PUBLIC_ prefix on a service_role key exposes it to the browser
4. **Unisolated membership joins** -- cross-tenant leaks through joins that do not filter by user_id or org_id
5. **Write-side WITH CHECK gaps** -- INSERT/UPDATE policies allowing rows under any user_id
6. **Storage policies missing object-level access control** -- anyone with the bucket name can read/download files

See the Gist links above for SQL that detects each of these.
</details>

<details>
<summary><strong>More fixed-scope offers</strong></summary>

- [Next.js + Supabase Launch Checklist -- $19](https://cengokurtoglu.gumroad.com/l/xjnmxt?utm_source=github_profile&utm_medium=readme&utm_campaign=launch_checklist&utm_content=more): 8-page PDF, 60 checks. [Start with the free 10-check pre-launch pass](https://cengokurtoglu.gumroad.com/l/nextjs-supabase-10-checks-free?utm_source=github_profile&utm_medium=readme&utm_campaign=lead_magnet&utm_content=more) or [preview real pages](https://cekuu35.github.io/nextjs-supabase-checklist-preview/).
- [Multi-Tenant RLS Self-Audit Pack -- $39](https://cengokurtoglu.gumroad.com/l/mlzoou?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_selfaudit&utm_content=more): the exact SQL checks behind every multi-tenant audit -- 7 isolation bug classes, each with a FIND query and ready-to-run verification.
- [Supabase RLS Re-Scan -- $99, 48h](https://cengokurtoglu.gumroad.com/l/supabase-rls-rescan?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_rescan&utm_content=more): for after you have rotated a leaked key -- re-checks exposure, audits RLS policy by policy, and returns a written before/after note.
- [Supabase RLS Leak Alert -- free n8n workflow](https://cengokurtoglu.gumroad.com/l/n8n-supabase-rls-leak-alert?utm_source=github_profile&utm_medium=readme&utm_campaign=rls_leak_alert&utm_content=more): a read-only workflow that emails you when a Supabase service_role key appears in public GitHub code.
- [AI-Ready Website Audit Kit -- $49](https://cengokurtoglu.gumroad.com/l/ai-ready-website-audit-kit?utm_source=github_profile&utm_medium=readme&utm_campaign=ai_audit_kit&utm_content=more): 100-point workflow, dependency-free Node.js scorer, [free scorecard](https://cekuu35.github.io/evidence-led-website-audit/scorecard.html), [sample report](https://cekuu35.github.io/evidence-led-website-audit/sample-report.html).

These products provide tools and guidance, not import-success, ranking,
security, compliance, sales, or revenue guarantees.

**Creators, consultants and agencies:** earn a **50% commission** promoting
any of these -- 30-day tracking, nothing owed unless it converts.
[Join the affiliate program](https://cengokurtoglu.gumroad.com/affiliates).
</details>

<details>
<summary><strong>Selected product work</strong></summary>

| Project | Evidence | What it is |
|---|---|---|
| **Abonem** | [App Store listing](https://apps.apple.com/tr/app/abonem-subscription-tracker/id6776748341) Â· [support repo](https://github.com/cekuu35/abonem-store) | Live iOS/iPadOS subscription-tracker finance app with optional Premium IAP |
| **Renderivo** | [Live product](https://www.renderivo.com/) Â· [case study](RENDERIVO_CASE_STUDY.md) | Text-to-3D SaaS, open signups: prompt â†’ GLB with in-browser preview |
| **AI SaaS Starter** | [Gumroad -- $99](https://cengokurtoglu.gumroad.com/l/epwlkh?utm_source=github_profile&utm_medium=readme&utm_campaign=ai_saas_starter&utm_content=work) | Renderivo's full source as a commercial starter: Next.js App Router, Polar MoR checkout (works from ~46+ Stripe-unsupported countries), signed webhooks, metered credit wallet, fal.ai pipeline, Firebase Admin auth, cron reconciliation. No secrets, .env.example included. |
| **llms.txt Generator** | [source](https://github.com/cekuu35/llms-txt-generator) Â· [Apify Actor](https://apify.com/nacred_corner/llms-txt-generator) | Actor that discovers pages via sitemaps/links, extracts content, writes llms.txt |
| **Wholesale ARV** | [live prototype](https://wholesale-arv.vercel.app/app) | Demo-mode real-estate ARV/repair/MAO analysis, visibly labeled fallback |
| **React Story Editor** | [live proof](https://react-story-editor-proof.vercel.app/) | Single-layer editor, drag/keyboard positioning, 1080Ã—1920 PNG export |
| **Free Next.js CV Template** | [source](https://github.com/cekuu35/free-nextjs-cv-template) Â· [demo](https://free-nextjs-cv-template.vercel.app/) | Next.js 15 / React 19 / TS / Tailwind, config-driven, validated contact point |
| **Next.js Website Templates** | [source](https://github.com/cekuu35/nextjs-website-templates) Â· [buyer guide](https://github.com/cekuu35/nextjs-website-templates/blob/main/BUYER_GUIDE.md) | 20 hosted starter demos with a transparent buyer guide; commercial source package optional |

</details>

<details>
<summary><strong>Recent public example of the work</strong></summary>

In a Supabase security thread about whether a Developer role can be prevented
from reading project secrets, I argued the boundary cannot hold -- deploy
permission is transitively read-all-secrets permission, so redacting logs
filters one channel while the attacker picks another. The reporter withdrew
the proposal, and the thread produced a documentation change rather than a
redaction feature. [Read the thread](https://github.com/supabase/supabase/discussions/48795#discussioncomment-17930908).

</details>

## Templates & Kits

- **[Supabase October 30 Migration Kit](https://cengokurtoglu.gumroad.com/l/supabase-oct30-migration-kit)** -- ready-to-use GRANT templates, the audit census query, the dry-run script and the verification queries for the October 30, 2026 grants change (changelog 45329).
- **[RLS Audit Kit](https://cengokurtoglu.gumroad.com/l/supabase-rls-audit-kit-v2)** -- the per-identity access matrix, the SQL smoke tests and the review prompts behind the audits above, as a self-serve pass.
- **[Get-Paid Pack](https://cengokurtoglu.gumroad.com/l/get-paid-pack)** -- the invoicing and late-payment chasing system I run for client work.
## Contact

**cnkkurtoglu@gmail.com** -- fastest way to reach me. A question is not an
order: if the honest answer is that you do not need an audit, that is the
answer you will get.

## Support

All the security work above -- public repo scans, the free RLS check, vulnerability
disclosures to maintainers -- is free, no strings. If it saved you some pain, you can
[buy me a coffee](https://www.buymeacoffee.com/cenkKurtoglu) -- entirely optional.

Stack: Next.js Â· React Â· TypeScript Â· Node.js Â· Firebase/Firestore Â· Apify/Crawlee Â· Vercel
