# Recipes

A simple, plaintext recipe database

# Getting Started

This is a [Jekyll](https://jekyllrb.com/) build.

Setup:

```bundle install```

Test:

```bin/dev```

With default settings, you should be able to view the site locally at `http://127.0.0.1:4000/`

# Writing a Recipe

The recipes are stored in the collection "Recipes" (the folder /_recipes).

They are written in Markdown and contain a few special sections:

- The frontmatter, which contains:
 - Title, Image, and Layout (which is "recipe")
 - Ingredients (a list of things in the dish)
 - Directions (a list of steps for the dish)
- Body content (for intros, stories, written detail)

If you need help with Markdown, here's a [handy cheatsheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet).

# Writing a component recipe

A component recipe is a special recipe made up of other recipes. To make a new component recipe:

- place your smaller, single recipes into the /_components folder
- make a new recipe like normal in the /_recipes folders
- in the frontmatter of this new recipe, include your recipes from the /_components folder (instead of the usual Ingredeints list)

# Tags

```
{% assign sorted_tags = site.recipes | map: 'tags' | join: ','  | split: ',' | uniq | sort %}
<ul>
{% for tag in sorted_tags %}
  <li>
      {{ tag }}
  </li>
{% endfor %}
</ul>
```
