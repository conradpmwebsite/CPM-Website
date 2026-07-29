# Making the website editable — setup guide

This package turns the site from "one file only a developer can edit" into a real
website with a login page (`yoursite.com/admin`) where non-technical staff can add
and edit **listings, districts, developments, and testimonials** — no code required.

Core Values, Services, Why Choose CPM, and Partners are intentionally left as-is in
the code, since those rarely change. If you want those editable too later, the same
pattern used here can be extended to them.

## What changed technically

- `index.html` — the listings/districts/developments/testimonials data still lives
  in the file as a fallback (so the site always displays correctly, even if opened
  directly as a file or before hosting is fully set up). But on load, it now also
  tries to fetch fresher copies from the `data/` folder — if that succeeds (which
  it will once this is deployed with the CMS in place), the page re-renders with
  whatever staff have edited through `/admin`, live.
- `data/*.json` — the actual editable content, in plain JSON. This is what the CMS
  reads and writes.
- `admin/` — the content editor itself (Decap CMS, free and open-source).

**Note:** double-clicking `index.html` and opening it locally will show the site
exactly as it looks right now — that's the fallback data working correctly. It just
won't reflect any *new* edits made through `/admin` until it's actually served over
the web (Netlify), since fetching local files that way is blocked by browsers for
security reasons. That's expected and fine — it's only relevant for previewing.

## Setup steps (one-time)

**You'll need:** a GitHub account (free) and a Netlify account (free) — both should
be created under Conrad's own email, not a personal one, so the client owns
everything from day one.

1. **Create a GitHub repository** and upload this entire folder to it (all of
   `index.html`, the `data/` folder, and the `admin/` folder, keeping the same
   structure).

2. **Create a Netlify site from that GitHub repo** — in Netlify, choose
   "Add new site" → "Import an existing project" → connect GitHub → pick the repo.
   Leave the build settings empty (there's no build step; it's a static site) and
   deploy.

3. **Point Conrad's domain at this Netlify site** — same DNS step we discussed
   earlier, just now pointing at this new deployment.

4. **Turn on Netlify Identity** — in the Netlify site dashboard: Site settings →
   Identity → "Enable Identity." Under Registration, set it to "Invite only" (so
   random people can't sign themselves up).

5. **Turn on Git Gateway** — still under Identity settings, scroll to "Services" →
   enable "Git Gateway." This is what lets the CMS actually save changes back to
   GitHub on the user's behalf without them needing their own GitHub account.

6. **Invite the people who'll manage content** — Identity tab → "Invite users" →
   enter their email. They'll get an email to set a password.

7. **They log in at `yourdomain.ae/admin`** — that's it. They'll see "Listings,"
   "Districts," "Developments," and "Testimonials" in a sidebar, click into any of
   them, add/edit/delete entries, upload photos directly, and hit "Publish." The
   change goes live within about a minute.

## A photo-upload note

When staff upload a photo through the CMS, it gets committed into the GitHub repo
under `images/uploads/`. That's a real, permanent home for it (unlike the current
placeholder photos, which are hotlinked from Wikimedia Commons) — a meaningful
upgrade on its own.

## If content edits aren't showing up after deploying

The site is built to degrade gracefully — if `data/districts.json` etc. can't be
reached for any reason, it just keeps showing its last-known content rather than
looking broken. So if a listing you added or edited through `/admin` isn't
appearing: open the browser console (F12) on the live site and check for a fetch
error, which is almost always a path issue (the `data/` folder wasn't uploaded in
the same location as `index.html`) or the JSON has a typo introduced by a manual
edit outside the CMS. The CMS itself won't let you save invalid JSON, so this
mainly matters if someone edits the files directly on GitHub instead of through
`/admin`.
