<h3 align="center">Build and Tag action</h3>

<p align="center"><a href="https://github.com/JasonEtco/build-and-tag-action"><img alt="GitHub Actions status" src="https://github.com/JasonEtco/build-and-tag-action/workflows/CI/badge.svg"></a> <a href="https://codecov.io/gh/JasonEtco/build-and-tag-action/"><img src="https://badgen.now.sh/codecov/c/github/JasonEtco/build-and-tag-action" alt="Codecov"></a></p>

A GitHub Action for publishing JavaScript Actions! It's designed to act on new releases, and updates the tag with a compiled JS file, using [`@zeit/ncc`](https://github.com/zeit/ncc). The process looks like this:

- Runs the `setup` input (default: `npm ci && npm run build`)
- Reads the `main` property in your `package.json`
- Force pushes `action.yml` and the above file to the release's tag
- Force pushes to the major version tag (ex: `v1.0.0` -> `v1`)

<img width="1005" alt="image" src="https://user-images.githubusercontent.com/10660468/82084147-d894ca00-96b8-11ea-9a14-1640d6963213.png">

This repository even uses it! `@zeit/ncc` supports TypeScript out of the box 😍

## Usage

```yaml
name: Publish

on:
  release:
    types: [published, edited]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          ref: ${{ github.event.release.tag_name }}
      - uses: JasonEtco/build-and-tag-action@v1
        env:
          GITHUB_TOKEN: ${{ github.token }}
```

## Options

**setup**

You can customize the `setup` script that's run before pushing the files to the tag.

```yaml
- uses: JasonEtco/build-and-tag-action@v1
  with:
    setup: 'npm ci && npm run super-build'
  env:
    GITHUB_TOKEN: ${{ github.token }}
```

The default is `npm ci && npm run build --if-present`. You can disable it entirely by setting `setup: ''`!

## Motivation

The [guide to JavaScript Actions](https://help.github.com/en/actions/building-actions/creating-a-javascript-action) recommends including `node_modules` in your repository, and manual steps to [following the versioning recommendations](https://github.com/actions/toolkit/blob/master/docs/action-versioning.md#versioning). There are anti-patterns there that just don't sit right with me; so we can enable the same workflow, automatically!

This Action is heavily inspired by [mheap/github-action-auto-compile-node](https://github.com/mheap/github-action-auto-compile-node) & [Actions-R-Us/actions-tagger](https://github.com/Actions-R-Us/actions-tagger). This is more or less a combination of those two Actions, meant to work together.