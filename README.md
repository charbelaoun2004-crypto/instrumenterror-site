# instrumenterror.com

A small static website for the YouTube channel Instrument Error
([youtube.com/@instrumenterror](https://www.youtube.com/@instrumenterror)): a home page, a privacy
policy and terms of service. Plain HTML and CSS: no build step, no scripts, no cookies, no forms,
no external requests.

**Status: built locally, not published.** Replace the placeholders and settle the open questions in
[DECISIONS.md](DECISIONS.md) before it goes anywhere.

## Why it exists

- The channel's OAuth app in Google Cloud (project "Instrument Error", id `instrument-error`) needs
  an application home page, a privacy policy link, a terms of service link and an authorised domain
  on its Branding page before it can leave Testing. While an external app is in Testing, Google
  issues refresh tokens that expire after 7 days (Google's OAuth 2.0 documentation, "Refresh token
  expiration").
- Later, a YouTube API Services compliance audit reads the privacy policy and terms against the
  YouTube API Services Developer Policies.

## Files

| File | Served at | What it is |
|---|---|---|
| `index.html` | `/` | The channel, how its videos are made, and the private Instrument Error app |
| `privacy/index.html` | `/privacy` | Privacy policy for the website and the app |
| `terms/index.html` | `/terms` | Terms of service |
| `404.html` | any missing address | Not-found page. Its styles are inline and its links start at the site root, because hosts serve it at any depth |
| `styles.css` | `/styles.css` | Shared styles, following the channel's brand guide v1 palette and type roles |
| `robots.txt` | `/robots.txt` | Allows all crawlers |
| `README.md`, `DECISIONS.md` | not for upload | These notes |

Upload only `index.html`, `404.html`, `styles.css`, `robots.txt` and the `privacy` and `terms`
folders. Leave out the two `.md` files and the `.git` folder, which would otherwise be publicly
readable.

## Preview on this computer

Opening `index.html` directly shows the home page, but its Privacy and Terms links then open a
folder listing. They are written for a web server, so that the live addresses are exactly
`/privacy` and `/terms`. To click through as on the live site, serve this folder with any local
static server, for example Python's built-in `-m http.server` started inside the folder, and open
the address it prints. (Use a real Python interpreter: the plain `python` command on this PC is the
Windows Store stub.)

## Before going live

1. Replace every placeholder, then run the search in DECISIONS.md section 1 to confirm none remain.
2. Answer DECISIONS.md section 2 and read section 3.
3. Put the site on a host that serves instrumenterror.com over HTTPS (options below). The domain
   shows the registrar's parking page today; pointing it at a host is a DNS change at Porkbun.
4. Check the live site: `https://instrumenterror.com`, `https://instrumenterror.com/privacy` and
   `https://instrumenterror.com/terms` load over HTTPS, and a made-up address shows the not-found
   page.

## Hosting options

Neutral notes from each provider's documentation, read on 2026-09-15. Nothing has been set up with
any of them. Plans, prices and steps change, so check the linked pages.

**Porkbun Static Hosting** (the registrar's own hosting). Files go up through Porkbun's editor, by
FTP, or from a connected GitHub repository, and a domain registered at Porkbun connects to it
through DNS. It is a hosting plan, so check its price.
[Product page](https://porkbun.com/products/webhosting/staticHosting) ·
[setup guide](https://kb.porkbun.com/article/137-how-to-set-up-static-hosting)

**GitHub Pages.** Publishes a site from a GitHub repository. For an apex domain such as
instrumenterror.com, GitHub's guide lists A records `185.199.108.153`, `185.199.109.153`,
`185.199.110.153` and `185.199.111.153` (AAAA records `2606:50c0:8000::153` to
`2606:50c0:8003::153`), recommends verifying the domain with GitHub before adding it, and notes
that the "Enforce HTTPS" option can take up to 24 hours to become available. Whether the repository
has to be public depends on your GitHub plan; a public repository also publishes these notes and the
commit history.
[Custom domain guide](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)

**Cloudflare Pages.** Publishes by direct upload or from a Git repository. For an apex domain,
Cloudflare's guide says the domain must be added to Cloudflare as a zone, which means moving its
nameservers from Porkbun to Cloudflare; a subdomain only needs a CNAME record. Pages serves the
nearest `404.html` for a missing page.
[Custom domains guide](https://developers.cloudflare.com/pages/configuration/custom-domains/) ·
[How Pages serves files](https://developers.cloudflare.com/pages/configuration/serving-pages/)

## Google Cloud Branding page, once the site is live

On the Branding page of the Google Auth Platform for project `instrument-error`, under App domain
and Authorised domains:

| Field | Value |
|---|---|
| Application home page | `https://instrumenterror.com` |
| Application privacy policy link | `https://instrumenterror.com/privacy` |
| Application terms of service link | `https://instrumenterror.com/terms` |
| Authorised domain | `instrumenterror.com` |

What Google's help pages say about these fields (read on 2026-09-15):

- The home page must be publicly accessible and describe the app's functionality, and the privacy
  policy link on the home page should match the one entered on the Branding page. The home page
  links to `privacy`, which a browser resolves to exactly `https://instrumenterror.com/privacy`.
  ([Manage OAuth App Branding](https://support.google.com/cloud/answer/15549049))
- The privacy policy must be hosted on the same domain as the home page, and every authorised domain
  must be verified in Google Search Console by a Google account that is an owner or editor of the
  project.
  ([Submit for brand verification](https://developers.google.com/identity/protocols/oauth2/production-readiness/brand-verification))
- Branding changes are saved as a draft. After Verify Branding passes, Publish branding has to be
  clicked within 7 days, or the check must be run again. (both pages above)
- The brand verification page lists personal use, where you are the only user of the app, among
  the cases that do not need verification. (same page, "Exceptions to verification requirements")

## Policies the pages were written against

Read on 2026-09-15. Re-read them before the compliance audit; they change.

- [YouTube API Services Developer Policies](https://developers.google.com/youtube/terms/developer-policies),
  page last updated 2026-09-14
- [Google API Services User Data Policy](https://developers.google.com/terms/api-services-user-data-policy),
  last updated 15 February 2024
- [YouTube API Services Terms of Service](https://developers.google.com/youtube/terms/api-services-terms-of-service),
  page last updated 2026-09-14 (sections 7 and 8)

## Keeping it right

- Change the effective date on a page whenever that page changes.
- Update both policy pages before the app requests any new permission, such as `youtube.upload`;
  both pages promise this.
- If the host changes, update the host's name on the privacy page.
