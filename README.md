# azdops-nginx-site

## Introduction

This repository serves as a [MkDocs] template for deploying static websites using [azdops-nginx-aca] or [azdops-nginx-aas].

[MkDocs]: https://www.mkdocs.org/
[azdops-nginx-aca]: https://github.com/yaegashi/azdops-nginx-aca
[azdops-nginx-aas]: https://github.com/yaegashi/azdops-nginx-aas

## How to build

This repository contains multiple MkDocs configurations that support a single multi-language website.

|Site URL (`site_url`)|Source|
|-|-|
|`https://example.com/`|[docs/index.html](docs/index.html)|
|`https://example.com/en/`|[docs/en/mkdocs.yml](docs/en/mkdocs.yml)|
|`https://example.com/ja/`|[docs/ja/mkdocs.yml](docs/ja/mkdocs.yml)|

To run a development server for each language:

```
mkdocs serve -f docs/en/mkdocs.yml
mkdocs serve -f docs/ja/mkdocs.yml
```

To build all languages into the site directory (`site/en` and `site/ja`):

```
./nginxsiteops.sh site-build
```

To set up [Rclone configuration for Azure Files Storage](https://rclone.org/azurefiles/):

```
export NGINX_SITE_SAS_URL=<SAS URL>
./nginxsiteops.sh rclone-config
```

To upload the built site to Azure Files Storage:

```
./nginxsiteops.sh rclone-sync [<SITE_NAME>]
```

- `SITE_NAME` defines the website's sub-domain (`https://{SITE_NAME}.example.com`).
- When `SITE_NAME=default` (which is the default value), the website is created without a sub-domain (`https://example.com`)

## GitHub Actions workflow

The [site-publish.yml](.github/workflows/site-publish.yml) workflow runs on `push`, `pull_request`, and `workflow_dispatch` triggers to:

- Build the MkDocs sites (en, ja)
- Publish them to the server when `secrets.NGINX_SITE_SAS_URL` is configured:
  - For `push` or `workflow_dispatch` events, it publishes the website using the branch name as `SITE_NAME`.
  - For `pull_request` events, when `vars.PREVIEW_URL` is set, it publishes the website with a temporary `SITE_NAME` and adds a comment to the PR discussion with the preview URL using the GitHub Actions bot account.

|`vars.PREVIEW_URL`|Result preview URL|
|-|-|
|`https://example.com/ja/intro`|`https://pr-<NNN>-<COMMIT>.example.com/ja/intro`|
