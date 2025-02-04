# azdops-nginx-site

## Introduction

This is the [MkDocs] template repository to deploy static websites using [azdops-nginx-aca].

[MkDocs]: https://www.mkdocs.org/
[azdops-nginx-aca]: https://github.com/yaegashi/azdops-nginx-aca

## How to build

This repository contains multiple MkDocs configurations for a single multi-language website.

|Site URL (`site_url`)|Source|
|-|-|
|`https://example.com/`|[docs/index.html](docs/index.html)|
|`https://example.com/en/`|[docs/en/mkdocs.yml](docs/en/mkdocs.yml)|
|`https://example.com/ja/`|[docs/ja/mkdocs.yml](docs/ja/mkdocs.yml)|

Run development server for each language:

```
mkdocs serve -f docs/en/mkdocs.yml
mkdocs serve -f docs/ja/mkdocs.yml
```

Build all languages into the site directory (`site/en`, `site/ja`, etc):

```
./nginxsiteops.sh site-build
```

Set up [Rclone configuration for Azure Files Storage](https://rclone.org/azurefiles/):

```
export NGINX_SITE_SAS_URL=<SAS URL>
./nginxsiteops.sh rclone-config
```

Upload the built site to the Azure Files Storage:

```
./nginxsiteops.sh rclone-sync <SITE_NAME>
```

- `SITE_NAME` is used to create the website with a sub-domain (`https://{SITE_NAME}.epxample.com`).
- `SITE_NAME=main` (default) creates the website without sub-domains (`https://example.com`)

## GitHub Actions workflow

The [site-publish.yml](.github/workflows/site-publish.yml) workflow runs on `push`, `pull_request`, and `workflow_dispatch` triggers.

- Builds the MkDocs sites (en, ja)
- Publishes them to the server only when `secrets.NGINX_SITE_SAS_URL` is set.
  - On `push` or `workflow_dispatch`, it publishes the website with the branch name as `SITE_NAME`.
  - On `pull_pullrequest`, and when `vars.PREVIEW_URL` is set, it publishes the website with a temporary `SITE_NAME` and posts the URL for previewing in the PR discussion comment by the github-actions bot account.

|`vars.PREVIEW_URL`|URL to publish previews|
|-|-|
|`https://example.com/ja/intro`|`https://pr-<NNN>-<COMMIT>.example.com/ja/intro`|
