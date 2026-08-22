# crowq3

The website for a Quake III Arena server, hosted at
**https://luci0n.github.io/crowq3/**

Two pages. `index.html` is the join page — what a player is given when they are
invited. `dev.html` is a reference covering how a Quake 3 server is run and
modified: an overview, console and config files, bots, and Excessive Plus
gameplay settings. The reference material is general to Quake 3 and Excessive
Plus rather than to this particular server.

## How it is built

Hand-written static HTML. There is no build step, no framework, no package
manager and no dependencies — the files in this repository are exactly the
files a browser receives. Each page carries its own CSS and JavaScript inline,
so a page is one request plus its images.

```
index.html        join page
dev.html          reference, all four sections in one file
404.html          unknown addresses
img/              background art
robots.txt
.nojekyll         serve the tree verbatim, no Jekyll processing
```

Every path in the HTML is relative, which is what lets the site sit under the
`/crowq3/` prefix a GitHub Pages project site is served from without any of the
links being rewritten for it.

## How the reference page works

All four sections of `dev.html` exist in the document at once. Selecting a tab
toggles a class to hide one section and show another, rewrites the address bar
to `#commands`, `#bots` and so on, and crossfades the banner art. Nothing is
fetched when a tab is clicked, so switching is instant.

Because the section is held in the fragment, `dev.html#weapons` opens directly
to that section and the browser's back button moves between sections.

This is about seventy lines of plain JavaScript at the foot of the file. It
degrades rather than breaks: the overview section is marked visible in the
markup itself, so if the script never runs the page still renders with content
and every link still resolves.

## The artwork

`img/` holds two files per banner — an animated `g_*.gif` and a still
`b_*.jpg` cut from it — plus `backdrop.jpg`, which sits behind every page and
stays fixed while the page scrolls.

The gifs began as clips from the game's own `gifs` folder, at one to five
megabytes each. Cutting them to a strip, dropping them to eight or ten frames a
second, desaturating, darkening and reducing them to 48 or 64 colors brought
them to between 200 and 560 KB. They are meant to read as texture behind a
title rather than as footage, so the aggressive treatment costs nothing.

Loading is staged. A banner paints its jpg immediately as a background, then
constructs an `Image` for the gif and only swaps it in on the `load` event, so
the animation appears over a picture rather than over an empty box on a slow
connection. On the reference page each section's art is requested the first
time that section is opened, so arriving at the page costs one clip rather than
four.

The CSS darkens whatever it is given, so replacement art does not need to be
prepared to match. Banner strips are roughly 520–1000 px wide by 300 tall and
other sizes are cropped to fill.

## Addresses that used to exist

The reference was once four separate pages, and the site was once served by
Netlify, where a `netlify.toml` mapped the old addresses onto the merged page.
Static hosting has no equivalent, so the same job is done by files:
`dev/index.html` and `dev/{commands,bots,weapons}/index.html` hold a redirect
into the matching section, as do the older `bots.html`, `weapons.html` and
`dev-*.html` at the top level. Each carries both a `meta refresh` and a
`location.replace`, and is marked `noindex`.

## Hosting

GitHub Pages builds from `main` on push. `404.html` is served for any address
that does not resolve, at any depth, which is why its links are absolute.

The server address players are given appears exactly once, in the `.addrbox`
block of `index.html`.
