# gantry

Documentation site for the Gantry plugin, served at
**https://privateasylum.com/gantry/** as a project site under the org site in
`Private-Asylum.github.io`. Static HTML rendered with Dioxus and styled by
Yeti, built by the `pvas-docs` binary from the
[pvas-web-shared](https://github.com/Private-Asylum/pvas-web-shared)
submodule. No Node in the build; Pagefind indexes the result for search.

```bash
git clone --recurse-submodules git@github.com:Private-Asylum/gantry.git
cargo docs                 # renders into public/
cargo docs serve           # renders, then previews at http://127.0.0.1:8080/
npx pagefind --site public # optional: build the search index locally
```

`cargo docs` is an alias (`.cargo/config.toml`) for running `pvas-docs` from
the submodule. The preview serves the site at the root; deployed, it lives
under `/gantry/`.

## What is where

| Path | What |
| --- | --- |
| `docs.toml` | Title, base path, links, sidebar order, the landing page |
| `content/*.md` | Hand-written pages. TOML front matter between `+++` lines: `title`, `group`, `order`, `description` |
| `data/manifest.json` | Written by PVAS-DocGen from the plugin; the reference and concept pages come from it |
| `styles/theme.css` | Gantry's Yeti tokens (colors, type) |
| `Crates/pvas-web-shared/` | The shared renderer, as a submodule; its commit is the pin |

Markdown is GitHub-flavoured: tables, task lists, footnotes and alerts
(`> [!NOTE]`, `> [!WARNING]`). Link with site-absolute paths (`/reference/`,
`/getting-started/`); the base path is added for you. **Every internal link and
anchor is checked on build**, and a broken one fails it, as does a relative
link.

## Refreshing the reference

From the GantryExamples project, with PVAS-DocGen in its `Plugins/`:

```bash
python3 Plugins/PVAS-DocGen/Scripts/docgen.py build --config DocGen/docgen.json
cp Saved/DocGen/gantry/manifest.json /path/to/gantry/data/manifest.json
```

The manifest is already gated: DocGen only lets narrative cleared with
`// @doc` into it, so what is published is decided in the plugin source.
Review the manifest diff, then commit.

## Notes

- The repository name is the URL path. Renaming it breaks every link to the
  docs (GitHub does not redirect project sites).
- No `CNAME`: the domain belongs to the org site and is inherited by path.
- Pages source must be **GitHub Actions**. CI checks out the submodule and full
  history (for "Last updated" dates), renders with `--locked`, then runs
  Pagefind.
