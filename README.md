# Magento 2 Bot Verification Pack

Installs [Bot Verification](https://github.com/hryvinskyi/magento2-bot-verification) together with its Google and
Bing crawlers. The module catches requests that call themselves Googlebot or bingbot but come from an address Google or
Bing does not publish, and once *Refuse impostors* is on, it answers them with a 403 before Magento routes them.

This is a metapackage. It holds no code and only requires the packages below, each at `^1.0`.

| Package | Module | Adds |
|---|---|---|
| [`hryvinskyi/magento2-bot-verification`](https://github.com/hryvinskyi/magento2-bot-verification) | `Hryvinskyi_BotVerification` | The check, the refusal, the address lists and their refresh, the admin settings and the CLI commands |
| [`hryvinskyi/magento2-bot-verification-google`](https://github.com/hryvinskyi/magento2-bot-verification-google) | `Hryvinskyi_BotVerificationGoogle` | Google's crawlers and fetchers and their five address lists |
| [`hryvinskyi/magento2-bot-verification-bing`](https://github.com/hryvinskyi/magento2-bot-verification-bing) | `Hryvinskyi_BotVerificationBing` | bingbot and its address list |

## Why Cloudflare is not included

[`hryvinskyi/magento2-bot-verification-cloudflare`](https://github.com/hryvinskyi/magento2-bot-verification-cloudflare)
declares Cloudflare's ranges as trusted edge servers, which is right only for a shop behind Cloudflare. On any other
shop, a request from those ranges (a Cloudflare Worker, for instance) would be taken for an edge server, and the visitor
address the Worker writes into `X-Forwarded-For` would be believed. That lets an impostor through. It never refuses a
real crawler.

## Installation

```bash
composer require hryvinskyi/magento2-bot-verification-pack
bin/magento module:enable Hryvinskyi_BotVerification Hryvinskyi_BotVerificationGoogle Hryvinskyi_BotVerificationBing
bin/magento setup:upgrade
bin/magento cache:enable bot_verification
bin/magento bot-verification:refresh
```

Behind Cloudflare, add its module as well:

```bash
composer require hryvinskyi/magento2-bot-verification-cloudflare
bin/magento module:enable Hryvinskyi_BotVerificationCloudflare
bin/magento setup:upgrade
bin/magento bot-verification:refresh
```

Magento leaves a new cache type disabled until it is enabled by hand. Without `cache:enable bot_verification`, every
request that claims a crawler reads the lists from the database, and `bot-verification:status` exits non-zero.

*Refuse impostors* is off by default. Before you turn it on, open *Stores › Configuration › Hryvinskyi › Bot
Verification* and check that *This request as the shop sees it* shows your own public address. If it shows a proxy's
address, that proxy is missing from the trusted proxies, and every crawler behind it would be refused.

Configuration, the command line, the performance report and the limits are described in the
[core package's README](https://github.com/hryvinskyi/magento2-bot-verification#readme).

## Requirements

PHP 8.1 or later. Verified on Magento 2.4.6 with PHP 8.1 and Magento 2.4.9-beta1 with PHP 8.4.

## License

MIT
