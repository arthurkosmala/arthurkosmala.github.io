# arthurkosmala.github.io

Personal academic website built with Jekyll and hosted on GitHub Pages.

## Repository structure

- `index.md`: homepage content (hero + publications)
- `_layouts/default.html`: single layout
- `assets/css/site.css`: site styling
- `assets/CV.pdf`: CV linked from the hero
- `assets/overview.gif`, `assets/reflection_coupling.mp4`: media shown on the speculative-sampling paper tile
- `_data/publications.json`: publication data rendered on the homepage

## Adding a profile photo

Drop a portrait into `assets/` (e.g. `assets/profile.jpg`) and set `author.photo`
in `_config.yml` to its path (e.g. `/assets/profile.jpg`). Leaving `author.photo`
empty renders the hero without a photo.

## Publication data

`_data/publications.json` is curated by hand. To add a new paper, see the
`update-publications` skill in `.claude/skills/`.

## Local site preview

```bash
bundle install
bundle exec jekyll serve
```

Open `http://127.0.0.1:4000`.
