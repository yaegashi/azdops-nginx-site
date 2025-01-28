# azdops-nginx-site

## Introduction

This is the [MkDocs] template repository to deploy static websites using [azdops-nginx-aca].

[MkDocs]: https://www.mkdocs.org/
[azdops-nginx-aca]: https://github.com/yaegashi/azdops-nginx-aca

## How to build

This repository contains multiple MkDocs configurations for a single multi-language website.

|Site URL (`site_url`)|Source|
|-|-|
|https://example.com/|[docs/index.html](docs/index.html)|
|https://example.com/en/|[docs/en/mkdocs.yml](docs/en/mkdocs.yml)|
|https://example.com/ja/|[docs/ja/mkdocs.yml](docs/ja/mkdocs.yml)|

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
./nginxsiteops.sh rclone-sync
```