# php

Prebuilt static PHP binaries as GitHub Releases. No fumbling with dependencies, no builds required.

This is a fork of [adwinying/php](https://github.com/adwinying/php) with download validation fixes. See [Differences from upstream](#differences-from-upstream) below.

## What's in the box?

This repo fetches prebuilt PHP binaries from [crazywhalecc/static-php-cli](https://github.com/crazywhalecc/static-php-cli) and publishes them as GitHub Release assets:

- **macOS and Linux**: [bulk](https://dl.static-php.dev/static-php-cli/bulk/) variant (static CLI binary with common extensions)
- **Windows**: [spc-max](https://dl.static-php.dev/static-php-cli/windows/spc-max/) variant

## Usage

Install via [mise](https://github.com/jdx/mise):

```bash
mise use github:lcatlett/php@8.4
```

Or pin a specific version:

```bash
mise use github:lcatlett/php@8.4.19
```

## Differences from upstream

The upstream [adwinying/php](https://github.com/adwinying/php) has two issues that caused all release assets from v8.4.18+ to be broken 2KB HTML error pages instead of real binaries:

1. **Double-slash URL bug** — The JSON API returns `full_path` with a leading slash (e.g., `/static-php-cli/bulk/...`). The download URL was constructed as `https://dl.static-php.dev/$filepath`, producing a double-slash (`//static-php-cli/...`) that started returning 404s when the upstream CDN stopped normalizing double slashes.

2. **No download validation** — The workflow never checked whether downloaded files were actually valid archives. HTML error pages were silently uploaded as `.tar.gz` release assets.

This fork fixes both:

- Uses `curl -f` to fail on HTTP errors
- Validates each downloaded file is a real gzip/zip archive before uploading
- Fails the workflow with clear error messages if validation fails

See [adwinying/php#3](https://github.com/adwinying/php/pull/3) for the upstream PR.
