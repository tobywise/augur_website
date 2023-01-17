# Augur website

## How to build the site locally

### Install Hugo

The website is build using [Hugo](https://gohugo.io), a fast static site builder. You can download and install the latest version of Hugo from [here](https://gohugo.io/installation/).

### Clone the repository

Clone the repository to your local machine:

```bash
git clone https://github.com/tobywise/augur_website
```

### Add the theme as a git submodule

The website uses the Hugo theme [LoveIt](https://themes.gohugo.io/themes/loveit/).
 
The theme is stored in a separate repository. To add it as a submodule, run the following command from the root of the repository:

```bash
git submodule add https://github.com/dillonzq/LoveIt.git themes/LoveIt
```

> :warning: **Note**: It is best to avoid modifying the theme itself, and instead to override the theme's default behaviour by adding/changing relevant files within this repository.

### Run the site locally

To run the site locally, run the following command from the root of the repository:

```bash
hugo serve -D
```

Sometimes Hugo refuses to update style changes as it likes to cache them. If this is an issue you can run:

```bash
hugo serve -D --noHTTPCache --ignoreCache --disableFastRender
```

### Editing the site

The site is built using [Markdown](https://www.markdownguide.org/). The content of the site is stored in the `content` directory. The `content` directory contains a number of subdirectories, each of which corresponds to a page on the site. For example, the `content/about.md` file corresponds to the `about` page on the site.

Blog entries are stored in the `content/posts` directory. Each blog entry is represented by a separate Markdown file.

All posts begin with a YAML header, which contains metadata about the post. For example:

```yaml
---
title: "Reflections and learnings on lived experience coproduction for data-driven research in mental health"
date: 2022-12-05T11:53:46Z
draft: false
author: "Alexandra Pike"
categories:
- lived experience
tags:
- secondary data
---
```

This provides metadata that is used for indexing etc. 