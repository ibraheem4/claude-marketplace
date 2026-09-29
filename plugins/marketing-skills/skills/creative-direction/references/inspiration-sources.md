# Inspiration sources

Tested 2026-09-24 from a headless Chrome and `curl`. Re-test before relying on a result; galleries
change their bot protection often.

| Source | What it is good for | Reachable | How |
|---|---|---|---|
| **Awwwards** (`awwwards.com/websites/sites_of_the_day/`) | Award-level craft, motion, WebGL; each entry links the live site | Yes, plain `curl` | Parse `/sites/<slug>` links; each slug page contains the live URL. Open those sites |
| **Godly** (`godly.website`) | Curated modern web and product design, motion-heavy | `curl` 403; **headless Chrome works** | Render the feed; open individual entries |
| **Dribbble** (`dribbble.com/search/<term>`) | Concept shots: hero animations, AI product UI, 3D | `curl` returns an empty 202 challenge; **headless Chrome works** | Search by term ("hero animation", "ai dashboard"); treat shots as concepts, not shipped sites |
| **Siteinspire** (`siteinspire.com`) | Calm, typographic, editorial sites | Headless Chrome works | Filter by category (Agencies & Consultancies, Minimal, Typographic) |
| **Landingfolio** (`landingfolio.com`) | Landing page sections by type (hero, pricing, features) | Headless Chrome works | Browse by section and industry (SaaS, Technology) |
| **Screenlane** (`screenlane.com`) | Real app and web UI flows | Landing page only without an account | Useful for product UI references, not heroes |
| **Mobbin** (`mobbin.com`) | Real iOS and web app screens and flows | Sign-up wall | Needs the user's account |
| **UI Sources** (`uisources.com`) | Real app flows with video | Sign-up modal over the content | Needs the user's account |
| **Behance** (`behance.net`) | Brand identity and case studies | **HTTP 400 even in headless Chrome** | Needs a logged-in session or the user's own links |
| **Figma Community** (`figma.com/community`) | Templates, kits, hero sections | **CloudFront 403 in headless Chrome** | Needs the user's session or direct file links |

## Practical notes

- Kill leftover Chrome processes between renders; a timed-out run can hold the debugging port and
  make the next render fail silently.
- In zsh, `set -- $pair` does not split on spaces; pass name and URL as separate arguments.
- A 200 is not proof of content. Check the title and look at the screenshot; a challenge page can
  return 200 or 202.
