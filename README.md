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

## What lives only here

- `privacy.html` — Local Mind privacy policy
- `support.html` — Local Mind support page
- `minicpm-v4.7-35b-a3b-gfx1151.md` — an early benchmark note, no longer
  referenced by `machines.json`

The first two are **not** in `yijunyu.github.io`, so
`https://yijunyu.github.io/local-mind/privacy.html` and `.../support.html`
both return 404 today. If the Mac App Store listing's Privacy Policy or
Support URL points at either, that link is broken, and the fix is to copy the
file into `yijunyu.github.io/local-mind/` — not to serve it from here, which
would collide with the same path.
