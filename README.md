[![license](https://img.shields.io/github/license/peaceiris/actions-gh-pages.svg)](https://github.com/peaceiris/actions-gh-pages/blob/master/LICENSE)
[![release](https://img.shields.io/github/release/peaceiris/actions-gh-pages.svg)](https://github.com/peaceiris/actions-gh-pages/releases/latest)
[![GitHub release date](https://img.shields.io/github/release-date/peaceiris/actions-gh-pages.svg)](https://github.com/peaceiris/actions-gh-pages/releases)
[![GitHub Actions status](https://github.com/peaceiris/actions-gh-pages/workflows/docker%20image%20ci/badge.svg)](https://github.com/peaceiris/actions-gh-pages/actions)
[![Docker Hub Build Status](https://img.shields.io/docker/cloud/build/peaceiris/gh-pages.svg)](https://hub.docker.com/r/peaceiris/gh-pages)

<img width="400" alt="GitHub Actions for deploying to GitHub Pages with Static Site Generators" src="./images/ogp.svg">



## GitHub Actions for deploying to GitHub Pages

A GitHub Action to deploy your static site to GitHub Pages with [Static Site Generators] (Hugo, MkDocs, Gatsby, GitBook, etc.)

[Static Site Generators]: https://www.staticgen.com/

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
Table of Contents

- [Getting started](#getting-started)
  - [(1) Add ssh deploy key](#1-add-ssh-deploy-key)
  - [(2) Create `.github/workflows/gh-pages.yml`](#2-create-githubworkflowsgh-pagesyml)
    - [:star: Repository type - Project](#star-repository-type---project)
    - [:star: Repository type - User and Organization](#star-repository-type---user-and-organization)
  - [Options](#options)
    - [:star: Pull action image from Docker Hub](#star-pull-action-image-from-docker-hub)
    - [:star: `PERSONAL_TOKEN`](#star-personal_token)
    - [:star: `GITHUB_TOKEN`](#star-github_token)
- [Examples](#examples)
  - [Static Site Generators with Node.js](#static-site-generators-with-nodejs)
  - [Static Site Generators with Python](#static-site-generators-with-python)
- [License](#license)
- [About the author](#about-the-author)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->



## Getting started

### (1) Add ssh deploy key

Generate your deploy key with the following command.

```sh
ssh-keygen -t rsa -b 4096 -C "$(git config user.email)" -f gh-pages -N ""
# You will get 2 files:
#   gh-pages.pub (public key)
#   gh-pages     (private key)
```

Next, Go to **Repository Settings**

- Go to **Deploy Keys** and add your public key with the **Allow write access**
- Go to **Secrets** and add your private key as `ACTIONS_DEPLOY_KEY`

### (2) Create `.github/workflows/gh-pages.yml`

#### :star: Repository type - Project

An example yaml file with Hugo action.

- [peaceiris/actions-hugo: GitHub Actions for Hugo extended](https://github.com/peaceiris/actions-hugo)

[![peaceiris/actions-hugo - GitHub](https://gh-card.dev/repos/peaceiris/actions-hugo.svg?fullname)](https://github.com/peaceiris/actions-hugo)

![peaceiris/actions-hugo latest version](https://img.shields.io/github/release/peaceiris/actions-hugo.svg?label=peaceiris%2Factions-hugo)
![peaceiris/actions-gh-pages latest version](https://img.shields.io/github/release/peaceiris/actions-gh-pages.svg?label=peaceiris%2Factions-gh-pages)

```yaml
name: github pages

on:
  push:
    branches:
    - master

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@master

    - name: build
      uses: peaceiris/actions-hugo@v0.58.1
      with:
        args: --gc --minify --cleanDestinationDir

    - name: deploy
      uses: peaceiris/actions-gh-pages@v2.2.0
      env:
        ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
        PUBLISH_BRANCH: gh-pages
        PUBLISH_DIR: ./public
```

The above example is for [Project Pages sites]. (`<username>/<project_name>` repository)

#### :star: Repository type - User and Organization

For [User and Organization Pages sites] (`<username>/<username>.github.io` repository),
we have to set `master` branch to `PUBLISH_BRANCH`.

```yaml
on:
  push:
    branches:
    - source  # default branch

PUBLISH_BRANCH: master  # deploying branch
```

[Project Pages sites]: https://help.github.com/en/articles/user-organization-and-project-pages#project-pages-sites
[User and Organization Pages sites]: https://help.github.com/en/articles/user-organization-and-project-pages#user-and-organization-pages-sites

### Options

#### :star: Pull action image from Docker Hub

You can pull a public docker image from Docker Hub.
By pulling docker images, you can reduce the overall execution time of your workflow. In addition, `latest` tag is provided.

```diff
- uses: peaceiris/actions-gh-pages@v2.2.0
+ uses: docker://peaceiris/gh-pages:v2.2.0
```

- [peaceiris/gh-pages - Docker Hub](https://hub.docker.com/r/peaceiris/gh-pages)

```diff
- uses: peaceiris/actions-hugo@v0.58.1
+ uses: docker://peaceiris/gha-hugo:v0.58.1
```

- [peaceiris/gha-hugo - Docker Hub](https://hub.docker.com/r/peaceiris/gha-hugo)

#### :star: `PERSONAL_TOKEN`

[Generate a personal access token (`repo`)](https://github.com/settings/tokens) and add it to Secrets as `PERSONAL_TOKEN`, it works as well as `ACTIONS_DEPLOY_KEY`.

```diff
- ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
+ PERSONAL_TOKEN: ${{ secrets.PERSONAL_TOKEN }}
```

#### :star: `GITHUB_TOKEN`

> **NOTES**: This action supports `GITHUB_TOKEN` but it has some problems to deploy to GitHub Pages. See #9

```diff
- ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
+ GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```



## Examples

### Static Site Generators with Node.js

[Next.js], [Nuxt.js], [gatsby], [hexo], [gitbook], [vuepress], [react-static], [gridsome], etc.

[Next.js]: https://github.com/zeit/next.js
[Nuxt.js]: https://github.com/nuxt/nuxt.js
[gatsby]: https://github.com/gatsbyjs/gatsby
[hexo]: https://github.com/hexojs/hexo
[gitbook]: https://github.com/GitbookIO/gitbook
[vuepress]: https://github.com/vuejs/vuepress
[react-static]: https://github.com/react-static/react-static
[gridsome]: https://github.com/gridsome/gridsome

Premise: Dependencies are managed by `package.json` and `package-lock.json`

![peaceiris/actions-gh-pages latest version](https://img.shields.io/github/release/peaceiris/actions-gh-pages.svg?label=peaceiris%2Factions-gh-pages)

```yaml
name: github pages

on:
  push:
    branches:
    - master

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@master

    - name: build
      uses: actions/setup-node@v1
      with:
        node-version: '10.16'
    - run: |
        npm install
        npm run build

    - name: deploy
      uses: peaceiris/actions-gh-pages@v2.2.0
      env:
        ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
        PUBLISH_BRANCH: gh-pages
        PUBLISH_DIR: ./public
```

### Vue and Nuxt

An example for [Nuxt.js] (Vue.js) project with [create-nuxt-app]

[create-nuxt-app]: https://github.com/nuxt/create-nuxt-app

![peaceiris/actions-gh-pages latest version](https://img.shields.io/github/release/peaceiris/actions-gh-pages.svg?label=peaceiris%2Factions-gh-pages)

```yaml
name: github pages

on:
  push:
    branches:
    - master

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@master

    - name: setup node
      uses: actions/setup-node@v1
      with:
        node-version: '10.16'

    - name: install
      run: npm install

    - name: test
      run: npm test

    - name: generate
      run: npm run generate

    - name: deploy
      uses: peaceiris/actions-gh-pages@v2.2.0
      env:
        ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
        PUBLISH_BRANCH: gh-pages
        PUBLISH_DIR: ./dist
```

### Static Site Generators with Python

[pelican], [MkDocs], [sphinx], etc.

[pelican]: https://github.com/getpelican/pelican
[MkDocs]: https://github.com/mkdocs/mkdocs
[sphinx]: https://github.com/sphinx-doc/sphinx

Premise: Dependencies are managed by `requirements.txt`

![peaceiris/actions-gh-pages latest version](https://img.shields.io/github/release/peaceiris/actions-gh-pages.svg?label=peaceiris%2Factions-gh-pages)

```yaml
name: github pages

on:
  push:
    branches:
    - master

jobs:
  build-deploy:
    runs-on: ubuntu-18.04
    steps:
    - uses: actions/checkout@v1

    - name: Set up Python
      uses: actions/setup-python@v1
      with:
        python-version: '3.6'
        architecture: 'x64'

    - name: Install dependencies
      run: |
        pip install --upgrade pip
        pip install -r ./requirements.txt

    - name: Build
      run: mkdocs build

    - name: Deploy
      uses: peaceiris/actions-gh-pages@v2.2.0
      env:
        ACTIONS_DEPLOY_KEY: ${{ secrets.ACTIONS_DEPLOY_KEY }}
        PUBLISH_BRANCH: gh-pages
        PUBLISH_DIR: ./site
```



## License

- [MIT License - peaceiris/actions-gh-pages]

[MIT License - peaceiris/actions-gh-pages]: https://github.com/peaceiris/actions-gh-pages/blob/master/LICENSE



## About the author

- [peaceiris's homepage](https://peaceiris.com/)
