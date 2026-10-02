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
| **CodePen** (`codepen.io/{user}/pen/{slug}`) | Working interactions with source: drag, spin, cursor fields, canvas and WebGL | **codepen.io 403s `curl` and hangs headless Chrome** (Cloudflare); `cdpn.io` works with a CodePen referer | Find pens by WebSearch limited to `codepen.io`; fetch and render via `cdpn.io`, below |

## CodePen

Tested 2026-10-02. A pen is the one reference you can run *and* read, so drive it and open the
source rather than judging a still.

- **Find:** WebSearch with `allowed_domains: ["codepen.io"]`. Search the mechanism ("quaternion
  drag", "fibonacci sphere") as well as the look; pen titles are short and literal.
- **Fetch:** `curl -sL -A "$UA" -e "https://codepen.io/" "https://cdpn.io/{user}/fullpage/{slug}"`.
  This returns the compiled HTML, CSS and JS inline; the pen's own script is
  `<script id="rendered-js">`, libraries are separate `<script src>` tags, and
  `stopExecutionOnTimeout` is CodePen's loop guard. Without the referer the body is a
  `referer-warning` notice and the JS is missing — check `grep -c referer-warning` is 0.
- **Render:** the same URL in Playwright with `extraHTTPHeaders: { Referer: 'https://codepen.io/' }`.
  Screenshot at rest, drive the input (mouse down, move in steps, up), screenshot again.
- **No change after a drag?** Compare the moved element's computed `transform`, and read the
  listeners in the source, before calling the pen broken; a drag on one tested pen left the frame
  unchanged while a click on another moved it.
- **Judge:** technique (CSS 3D, canvas, WebGL), whether the rAF loop runs always or only while
  moving, `devicePixelRatio` and resize handling, and `prefers-reduced-motion` (usually absent).
- **Licence** (blog.codepen.io/docs/licensing, read 2026-10-02): public pens are MIT, and reuse
  keeps `Copyright (c) YEAR - AUTHOR - URL TO ORIGINAL` with the MIT text; private pens grant
  nothing. Assets or libraries a pen pulls in keep their own terms (inferred, not on that page).
  Prefer taking the mechanism and writing it fresh.

## Practical notes

- Kill leftover Chrome processes between renders; a timed-out run can hold the debugging port and
  make the next render fail silently.
- In zsh, `set -- $pair` does not split on spaces; pass name and URL as separate arguments.
- A 200 is not proof of content. Check the title and look at the screenshot; a challenge page can
  return 200 or 202.
