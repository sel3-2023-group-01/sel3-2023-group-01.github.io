# sel3-2023-group-01.github.io

This is our site to host additional documentation about the project, hosted over at [sel3-2023-group-01.github.io](https://sel3-2023-group-01.github.io/). The site was created using Hugo and a modified version of the `hello-friend-ng` theme.

## Editing pages

The content of the pages is stored in `/content`, in plain Markdown files. It's possible to use additional Hugo features like shortcodes, but you don't have to if they scare you. For images and videos, though, you may have to (or just ping Stijn on Discord).

## Starting the server

```shell
hugo server -D
```

## Creating new pages

You generally shouldn't have to do this, but if you want to split your team's page up into multiple smaller pages you can create new pages using the following command:

```shell
hugo new <path>/index.md  # Ex: hugo new simulation-environment/terrain-generation/index.md
```

When you're ready to create a PR for the page, make sure to set `draft` to `false` in the front matter (metadata at the top of the file).
