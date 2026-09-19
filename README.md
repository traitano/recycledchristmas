# Recycled Christmas — site, deployment and discoverability plan

Static single page. No build step, no framework, no JavaScript required to read any content — which matters, because most AI crawlers render JavaScript poorly or not at all, and server-rendered text is the thing they actually consume.

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole site. Content, styles and JSON-LD structured data inline. |
| `robots.txt` | Opens the site to search and answer-engine crawlers by name. |
| `sitemap.xml` | Canonical URL list. Update `lastmod` when content changes. |
| `llms.txt` | Plain-text fact sheet for agents that fetch it. |
| `_headers` | Cloudflare Pages caching and security headers. |

## Costs

| Item | Cost |
|---|---|
| Domain (`recycledchristmas.org`) at Cloudflare Registrar, at-cost pricing | ~$10–12 / year |
| Cloudflare Pages hosting, SSL, CDN | $0 |
| Cloudflare Email Routing (`hello@` forwards to a real inbox) | $0 |
| Google Search Console, Bing Webmaster Tools | $0 |
| **Total** | **~$12 / year** |

## Deployment — Cloudflare Pages

1. Put this folder in a GitHub repo (public is fine and slightly better for indexing of the repo itself).
2. Cloudflare dashboard → Workers & Pages → Create → Pages → Connect to Git.
3. Framework preset: **None**. Build command: blank. Output directory: `/`.
4. Deploy. You get `*.pages.dev` immediately.
5. Buy the domain in Cloudflare Registrar (same dashboard), then Pages → Custom domains → add both `recycledchristmas.org` and `www.recycledchristmas.org`. DNS and certs are automatic.
6. Pick one as canonical and redirect the other. The files here assume the apex domain is canonical, so all URLs point to `https://recycledchristmas.org/` in `index.html`, `sitemap.xml`, `robots.txt` and `llms.txt`.

Any push to `main` redeploys in about 20 seconds. GitHub Pages and Netlify work identically if you prefer; Cloudflare's registrar is the cheapest place to hold the domain because they sell at wholesale cost with no renewal markup.

## Post-launch checklist

- [ ] Google Search Console: verify via DNS TXT, submit `sitemap.xml`, request indexing of `/`.
- [ ] Bing Webmaster Tools: import from Search Console in one click. Bing feeds ChatGPT search.
- [ ] Validate structured data at `search.google.com/test/rich-results` and `validator.schema.org`.
- [ ] Add the URL to the Facebook page's About field and to every post.
- [ ] Ask the church webmaster to link `fumcah.com/uwfaith` and the event page to the new domain. An inbound link from the church's established domain is the single most valuable link you will get.
- [ ] Add photos (see below).

## Listings that matter more than the site itself

Answer engines corroborate. A single site claiming an event is weaker than the same date, address and hours appearing consistently across several independent sources. All of these are free:

- Facebook **Event** (not just a post) for each day, with the website URL in the event details
- Eventbrite, free listing, $0 tickets
- Patch — Arlington Heights, community calendar
- Daily Herald and Journal & Topics community calendars
- Nextdoor — Arlington Heights and surrounding neighborhoods
- Chicago Parent / Chicagoland holiday market roundups
- GarageSaleFinder.com, YardSaleSearch.com, EstateSales.net
- Arlington Heights Chamber of Commerce events calendar
- Arlington Heights Memorial Library community calendar
- The church's own OneChurch calendar, which already feeds the lobby signage

Use the **exact same** name, street address and hours everywhere. Inconsistency is what breaks entity resolution for both Google and LLMs.

## Why the structured data is shaped the way it is

`index.html` carries a single JSON-LD `@graph` with stable `@id` values so the nodes reference each other instead of repeating themselves:

- `SaleEvent` with a single-day schedule — correct for a one-day sale and eligible for Google's event rich results.
- `Place` with geo coordinates, referenced by `@id` from every event node.
- `Organization` with `sameAs` pointing at Facebook, the church page and uwfaith.org — this is the entity-linking signal that lets a model connect "Recycled Christmas" to a real organization rather than treating it as a floating string.
- `FAQPage` mirroring the visible Q&A. Every answer is self-contained and leads with the answer, which is the format retrieval systems extract cleanly.

On `llms.txt`: publish it, but keep expectations calibrated. Google has stated it ignores the file for Search and AI Overviews, and no major provider has committed to reading it in production. It costs nothing and Anthropic's own guidance for agent-facing sites recommends one, so it is a reasonable cheap bet — it is not the thing that will get you cited. Crawlable server-rendered text, answer-first structure, schema markup and third-party corroboration are.

## Adding photos

Drop images in an `/img` folder. Three or four wide shots of full tables beat twenty detail shots. For each:

- Export at 1600px wide, convert to WebP, keep under 200KB
- Write real `alt` text describing the actual contents ("tables of glass ornaments and nativity sets in the church fellowship hall")
- Name files descriptively: `recycled-christmas-ornament-tables.webp`
- Add an `image` property to the `SaleEvent` node in the JSON-LD with the absolute URL — Google's event rich results want 1x1, 4x3 and 16x9 crops

## Annual update — search the file for `[YEAR]`

Every dated value lives in one of six places in `index.html`:

1. `<title>` and `<meta name="description">`
2. Open Graph title and description
3. The `.when` block in the masthead
4. The footer `.fine` line
5. The `SaleEvent` node: `name`, `startDate`, `endDate`, `description`, `offers.validFrom`, and both `subEvent` entries
6. The first two `FAQPage` answers

Then update `lastmod` in `sitemap.xml` and the dates in `llms.txt`. Takes about ten minutes.

**Do not delete last year's page after the sale.** Change `eventStatus` to `https://schema.org/EventScheduled` for the new year and let the URL accumulate age and links. A domain that has ranked for three consecutive Novembers outranks a fresh one every time.

## Unconfirmed values

These are placeholders based on the sale's historical pattern. Replace before launch:

- **2026 date**: Saturday November 14. Hours adjusted to 9 am–4 pm for the single-day event.
- **Geo coordinates** for the venue: approximate. Get exact values by right-clicking the church in Google Maps.
- **Donation drop-off window**: currently a placeholder sentence.
- **Contact**: the volunteer button points at Facebook. A dedicated forwarding address is better.

## Funding update

- Proceeds support the United Women in Faith pledge to mission and local partner organizations, including Bethany House of Hospitality, Children’s Advocacy Center of North and Northwest Cook County, C.I.T.Y. of Support, Faith Feeds Food Pantry, FamilyForward, FUMCAH Youth Mission, GirlForward, Good Neighbors Network, Hot Mess Express, Journeys The Road Home, Keeping Families Covered, Kids Above All, The Kid’s Pantry, Northwest Center Against Sexual Assault, Partners for Our Communities, Shelter Youth & Family Services, and WINGS — Women In Need Growing Stronger.
