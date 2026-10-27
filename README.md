# local-mind

Mirror. **This repo does not serve the site.**

`https://yijunyu.github.io/local-mind/` is served from the `local-mind/`
directory of the [`yijunyu.github.io`](https://github.com/yijunyu/yijunyu.github.io)
repo. Edit the page there. A commit pushed here changes nothing a visitor can
see, which is easy to get wrong, because the two trees hold files of the same
names and this one answers to the name in the URL.

## What is mirrored

These are copied from `yijunyu.github.io/local-mind/` and are downstream — if
they disagree with that directory, that directory is right:

- `index.html` — the landing page
- `machines.json` — the data behind the machine/model tables
- `releases.html`
- `ds4cuda-en.html`, `ds4cuda-zh.html`
- `vllm-pivot-en.html`, `vllm-pivot-zh.html`
- `rocm-performance-en.html`
- `privacy.html` — Local Mind privacy policy
- `support.html` — Local Mind support page

`privacy.html` and `support.html` originated here and existed *only* here
until 2026-10-27, which meant
`https://yijunyu.github.io/local-mind/privacy.html` and `.../support.html`
returned 404 while the landing page beside them returned 200. They have since
been copied upstream and both serve 200. They are listed above because
upstream is now the authoritative copy of them too — edit them there.

**Outstanding:** nobody has yet confirmed that the Mac App Store listing's
Privacy Policy and Support URLs actually point at those two paths. The 404 is
fixed at `yijunyu.github.io/local-mind/`, but if the listing names a different
address then the broken link is still broken and the fix went somewhere the
listing never looks. That check lives in App Store Connect, not in any repo.

## What lives only here

- `minicpm-v4.7-35b-a3b-gfx1151.md` — an early benchmark note, no longer
  referenced by `machines.json`. Kept because deleting it gains nothing.
