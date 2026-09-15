# Decisions for the owner

Built locally on 2026-09-15. **Not published.** Nothing has been deployed, and no account, DNS
record, Porkbun, Google Cloud or YouTube setting was touched.

The site must not go live until every yellow placeholder is replaced (section 1) and the AI
question is answered (section 2). Section 3 lists promises the privacy page makes that the
pipeline itself has to keep.

## 1. Placeholders on the pages

They show on the pages as yellow boxes such as `[OPERATOR NAME]`. In the HTML each one is
`<mark class="ph">[NAME]</mark>`: replace the whole element, from `<mark` to `</mark>`, with the
final text, so the yellow disappears.

| Placeholder | What to decide | privacy/index.html | terms/index.html |
|---|---|---|---|
| `[OPERATOR NAME]` | Who the pages say runs the channel, the site and the app: your real name, the pen name Sergio, or a business name | 1 | 1 |
| `[CONTACT EMAIL]` | The address for privacy questions, deletion requests and corrections | 3 | 2 |
| `[COUNTRY AND STATE]` | Whose law governs the terms | 0 | 1 |
| `[HOSTING PROVIDER]` | The company that will serve the site (options in README.md) | 2 | 0 |
| `[EFFECTIVE DATE]` | The date the pages take effect; the same date on both | 1 | 1 |
| `[AI PROCESSING DISCLOSURE]` | Added by the builder, not in the brief: see section 2 | 2 | 0 |

14 in total: 9 on the privacy page, 5 on the terms page, none on the home page or the 404 page.

Facts that bear on each choice (no recommendation made):

- **Operator name.** Google's API Services User Data Policy requires the app to represent
  accurately the person or organisation that manages it, and the YouTube API Services Developer
  Policies (III.D.1.a) forbid masking or misrepresenting your identity. Worth weighing when
  choosing between a real name, a pen name and a business.
- **Contact email.** It is printed on the pages, so it should be an address you read. The
  Developer Policies ask the privacy policy to say how users can contact you about privacy
  (III.A.2.i), and say YouTube may give users the Google Cloud contact address if the policy has
  none (III.D.5). An address at instrumenterror.com needs email set up for the domain first; that
  has not been done.
- **Country and state.** Used only in the governing-law sentence of the terms.
- **Hosting provider.** Name the host that actually serves the site, and change it if you move.
- **Effective date.** Change it whenever either page changes.

To confirm none are left, search the pages for `class="ph"`. There should be no match, for
example in PowerShell from this folder:

```powershell
Select-String -Path .\*.html, .\privacy\*.html, .\terms\*.html -Pattern 'class="ph"'
```

## 2. The AI question: why `[AI PROCESSING DISCLOSURE]` exists

The brief says nothing the app reads is shared, and the privacy page says so. But
`mindmatter/agents/analytics.md` says code collects the metrics and the Manager interprets them
(W13, W15, W16). The Manager is a Claude session, so channel metrics it reads are processed by
Anthropic's service.

If that happens, the privacy page should say so. The Developer Policies ask the privacy policy to
explain how user information is shared with internal or external parties (III.A.2.e), and the
Limited Use rules in the User Data Policy allow transfers only in listed cases (one of them: to
provide user-facing features, with the user's consent). Whether this counts as sharing, and how to
word it, is your call.

- If no AI service or other outside service ever reads the channel data: delete both markers,
  the one at the end of the "Nothing is sold, shared or used for advertising." line under "In short"
  and the paragraph under "7. Sharing".
- If one does: replace both with a plain sentence that names the service and what it receives,
  and make sure the "In short" line still agrees with it. Check that service's data-use settings
  first (for example, whether it may train on what it receives): Google's verification
  requirements treat transfers for AI model training as needing the user's explicit consent.

## 3. Promises on the privacy page that the pipeline must keep

The policies require these statements, so the privacy page makes them, and they have to be true in
practice. What the builder found in the repository (read only, nothing changed), with the
Manager's fixes of 2026-09-16 noted:

| The privacy page says | Policy section | Found |
|---|---|---|
| After access is revoked on Google's page, data stored under that permission is deleted within 30 days | Developer Policies III.D.2.c.ii | Still a manual step: no `mm` command deletes the stored metrics rows (the table is append-only), and a purge step is owed in the pipeline. Since 2026-09-16 a revoked grant makes the pipeline's weekly authorisation check fail, and the daily loop's owner queue raises it before 30 days have passed since the last successful check |
| A deletion request is honoured within 7 days, and deleting the app's copy does not touch YouTube | III.E.4.g | Manual, as the page says |
| Access is revoked on Google's permissions page | III.A.2.h, III.D.2.c | Found: `mm analytics auth --revoke` deleted only the local token. **Fixed 2026-09-16:** it now revokes the grant with Google, then deletes the local token, and says that a revocation through it starts a 7-day deletion duty (III.D.2.c.i: 7 calendar days after a revocation through the app, 30 after one on Google's page). The page can go on naming Google's page |
| The token is kept only while needed; channel data only as long as needed | III.E.4.a, III.E.4.b | How long data is needed is a judgement nobody has written down yet. **The 30-day confirmation is built (2026-09-16):** the pipeline confirms weekly that the sign-in still reaches the channel and records it, and the owner queue names the deadline if confirmations lapse |
| The Limited Use statement | User Data Policy, "Limited Use" | A commitment; depends on section 2 |
| Both pages are updated before the app requests any new permission, such as `youtube.upload` | III.E.3.c; User Data Policy | Stated on both pages |

Not a page statement, but relevant to the compliance audit: III.E.4.h forbids using API Data to
create new or derived data or metrics. `agents/analytics.md` describes baselines (rolling medians)
and a "performance score v1" computed from the metrics, and section III.L of the Developer Policies
describes a separate application route for audited developers who need derived metrics. Worth
reviewing before the audit.

## 4. Smaller choices the builder made (easy to change)

- **Link form.** The home page links to `privacy` and `terms`, which resolve to exactly
  `https://instrumenterror.com/privacy` and `https://instrumenterror.com/terms`. Google's Branding
  help says the privacy link on the home page should match the one entered on the Branding page.
  Side effect: opened straight from disk, those links show a folder listing (README.md explains a
  local preview).
- **Placeholder colour.** A bright yellow outside the brand palette, on purpose. The `mark.ph` rule
  in `styles.css` can stay or go once no placeholders remain.
- **Favicon.** A simple dial (off-white arc, amber needle) drawn inline as SVG. A stand-in, not a
  logo decision.
- **Home page wording.** Stays within the facts in the brief. The one inference is calling the
  app's permissions read-only, which follows from the two scope names.
- **Git author.** The commit is authored as `Instrument Error <site@instrumenterror.invalid>`, so no
  real name or address sits in the repository history (`.invalid` is reserved and can never be a
  real domain). To use another identity, set it and run `git commit --amend --reset-author`.
