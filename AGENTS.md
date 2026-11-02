# AGENTS.md

Guidance for AI agents working in this repo. The site is the plain-text recipe database at https://recipes.downing.us. It's a Jekyll site built by GitHub Pages from the `gh-pages` branch.

## Repo map

| Path | What it is |
| --- | --- |
| `_recipes/` | One Markdown file per recipe (the `recipes` collection). **Most work happens here.** |
| `_components/` | Sub-recipes used by component-based recipes (the `components` collection). |
| `images/` | Recipe photos, referenced by filename from frontmatter. |
| `_old_recipes/` | Retired recipes. Jekyll doesn't build this folder; don't add new recipes here. |
| `_layouts/recipe.html` | Renders a recipe page. It's the source of truth for which frontmatter fields do anything. |
| `_data/nutrients.yml` | Allowed keys and units for the optional `nutrition` block. |
| `search.json`, `recipes.json` | Generated from the collection. Never edit by hand. |
| `Rakefile` | `rake new` scaffolds a recipe file. |
| `bin/dev` | Local dev server. |

## Recipe schema

A recipe is a Markdown file with YAML frontmatter, then a free-form body after the closing `---`.

```markdown
---

layout: recipe
title: "Instant Pot Chili"
image: "instant-pot-chili.jpg"
tags: dinner, instant-pot

ingredients:
- 1 1/2 Pounds Ground Beef
- 1 Can Kidney Beans, 15 ounces, drained
- For Garnish
- Sour Cream

directions:
- Turn your instant pot to sauté and add the bacon.
- Cook until crisp, stirring often to cook evenly.

---

Notes, story, tips, variations, storage/freezing instructions.
```

### Fields

| Field | Required | Type | Notes |
| --- | --- | --- | --- |
| `layout` | yes | string | Always `recipe`. |
| `title` | yes | quoted string | Title Case, e.g. `"Instant Pot Creamy Ziti"`. |
| `image` | yes (every current recipe has one) | string | Filename only, relative to `images/` (e.g. `"stromboli.jpg"`). The file must exist in `images/`, or the page shows a broken image. |
| `tags` | strongly recommended | comma-separated string | Lowercase. Reuse existing tags (see below). |
| `ingredients` | yes* | YAML list of strings | One ingredient per item, quantity first. A plain item with no quantity works as a section heading (e.g. `- DRY INGREDIENTS`, `- For Garnish`). Each item goes through Markdown, so `*`, `_` and `[]` are interpreted. |
| `directions` | yes | YAML list of strings | One step per item, imperative sentences ending in a period. |
| `components` | only for component recipes | YAML list of strings | Each item must exactly match the `title` of a file in `_components/`. Used *instead of* `ingredients`. |
| `imagecredit` | optional | URL | Source link for a photo you didn't take. A camera icon links to it. |
| `nutrition` | optional | map | Keys from `_data/nutrients.yml`: `servingSize` (text), `calories` (kcal), `carbohydrateContent`, `fatContent`, `fiberContent`, `proteinContent`, `saturatedFatContent`, `sugarContent`, `transFatContent`, `unsaturatedFatContent` (all g), `cholesterolContent`, `sodiumContent` (both mg). Give numbers only; the layout adds the unit. |

\* A component recipe uses `components` instead of `ingredients`.

Anything after the second `---` is the body. It renders above the ingredients as the recipe description. Use it for notes, sourcing, scaling ("This recipe has been doubled from the original."), alternate cooking methods and storage. It can be a single short line (`Fluffy and tasty!`).

### YAML gotchas

- Quote any list item that starts with a special character or contains `: `, like `- "Note: chill overnight"`. An unquoted `: ` breaks the build.
- Fractions are written `1 1/2`, `3/4`, not Unicode `½`.
- Keep the blank line after the opening `---`. Every existing file has it, and `rake new` generates it.

## Creating a new recipe (house style)

1. **Pick the slug.** Use lowercase kebab-case from the title, e.g. `Instant Pot Chili` → `instant-pot-chili`. Older files use underscores; new files use hyphens. The slug sets the URL: collection pages are served at `/<collection>/<slug>.html`, so `_recipes/instant-pot-chili.md` is at `/recipes/instant-pot-chili.html`. Check `_recipes/` first so you don't create a duplicate.
2. **Scaffold it**, either way works:
   ```bash
   bundle exec rake new TITLE="Instant Pot Chili"
   ```
   or copy an existing recipe such as `_recipes/instant-pot-chili.md` and replace its contents. Delete the `tags, go, here` placeholder from the rake template.
3. **Fill in frontmatter** in this key order: `layout`, `title`, `image`, `tags`, blank line, `ingredients`, blank line, `directions`, blank line, closing `---`, blank line, body.
4. **Ingredients style:** quantity, then unit, then item, then prep after a comma: `1 Red Onion, chopped`, `2 cups parmesan cheese, shredded`. Keep the source's casing and units, and stay consistent within a recipe. Group with heading items (`- WET INGREDIENTS`) where the source groups them.
5. **Directions style:** one action per step, written for the cook, with times and temperatures inline (`Set your Instant Pot to Manual for 6 minutes.`). Don't number the steps; the list renders as a checklist.
6. **Body:** move tips, substitutions, freezer and slow-cooker/stovetop variants, and attributions here instead of cramming them into directions.
7. **Image:** save it as `images/<slug>.jpg`. Existing photos are roughly 1000–1500 px on the long edge and 30–350 KB. Resize anything much bigger before committing, e.g. `sips -Z 1500 images/<slug>.jpg`. Make sure the file really is a JPEG: `images/instant-pot-chili.jpg` is actually WebP data, which most browsers still display but is easy to trip on. Check with `file images/<slug>.jpg`.
8. **Tags:** reuse the existing set: `breakfast`, `bread`, `dinner`, `desserts`, `cake`, `cookies`, `pasta`, `soup`, `pizza`, `drink`, `picnic`, `holidays`, `christmas`, `instant-pot`. Don't invent near-duplicates. The repo already has some (`dessert`/`desserts`, `bread`/`breads`, and the typo `insant-pot`); leave existing files alone unless asked to clean them up.
9. **Commit message:** existing commits use the recipe title alone (`Instant Pot Chili`). Commit the recipe and its image together.

### Component-based recipes

For a dish built from sub-recipes (e.g. a cheesecake = crust + filling + topping):

1. Put each sub-recipe in `_components/<slug>.md` with the normal schema (`layout: recipe`, `title`, `image`, `ingredients`, `directions`).
2. Create the parent in `_recipes/` with a `components:` list of the sub-recipe **titles** (exact string match) instead of `ingredients`, plus its own `directions` for assembly.

Current components: `Graham Cracker Crust`, `Red Berry Dessert Topping`, `Vanilla Custard Filling`.

## Testing locally

**Requirements:** Ruby **3.2.2** (pinned in `Gemfile`) and Bundler. The macOS system Ruby (2.6) won't work. Install Ruby 3.2.2 with a version manager (rbenv, asdf, mise or chruby), then:

```bash
bundle install
```

```bash
bin/dev
```

`bin/dev` runs `bundle exec jekyll serve --livereload --baseurl ""`. Open http://127.0.0.1:4000/ and the new recipe at `http://127.0.0.1:4000/recipes/<slug>.html`.

Before pushing, confirm that:

- `bundle exec jekyll build` finishes with no errors (YAML mistakes show up here).
- The recipe page renders its title, image, ingredients, directions and body.
- The recipe appears on the home page and in search (`/search`; the index comes from `search.json`).
- Nothing new is untracked by accident: `_site/` and `.jekyll-cache/` are gitignored and must not be committed.

If Ruby 3.2.2 isn't available, at least lint the frontmatter. This uses the system Ruby, which is fine for parsing YAML:

```bash
ruby -ryaml -e 'ARGV.each { |f| YAML.safe_load(File.read(f, encoding: "UTF-8").split(/^---\s*$/)[1]) && puts("ok #{f}") }' _recipes/<slug>.md
```

Also check that the image exists: `ls images/<slug>.jpg`. Tell the user the site wasn't built locally.

## Deploying (push to GitHub)

Deployment is automatic. The default branch is **`gh-pages`**, and GitHub Pages rebuilds the site on every push to it. The custom domain comes from `CNAME` (`recipes.downing.us`). Don't edit `CNAME` or `_config.yml`'s `baseurl` unless asked.

```bash
git checkout gh-pages
```

```bash
git pull --ff-only
```

```bash
git add _recipes/<slug>.md images/<slug>.jpg
```

```bash
git commit -m "<Recipe Title>"
```

```bash
git push origin gh-pages
```

- Only commit and push when the user asks. Pushing publishes to the live site.
- Recipe additions go straight to `gh-pages`; that's how the existing history works. For layout, CSS or config changes, propose a branch and PR instead.
- GitHub Pages uses the `github-pages` gem's plugin allowlist. Don't add Jekyll plugins that aren't on it; they work locally but fail on Pages.
- After pushing, the site usually updates within a couple of minutes. Check `https://recipes.downing.us/recipes/<slug>.html`. If it doesn't appear, look at the repo's **Actions → pages-build-deployment** run (or `gh run list --workflow pages-build-deployment` if `gh` is installed) for the build error.
