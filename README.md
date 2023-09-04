# Simple Development Environment for BlueSpice

## Features
- Connect to your test wikis via HTTPS. Use `https://localhost/<path/to/your/wiki>`
- Connect to the DB with your favorite client, using `localhost:3306`
- Inspect the search indexes through `http://localhost:5601/`
- Read mails through `http://localhost:8025/`

## Usage

Copy `example.env` to `.env` and set variables for your local environment.

Run
```
docker composer up -d
```
## The workspace

In your `$WORKSPACE` you will need to have the following directories:
- `db/` -> Contains all databases you create in your environment
- `elasticsearch/` -> Contains all search indexes you create in your environment
- `ssl/` -> Contains the certificate files for HTTPS connection
- `www/` -> Contains the actual codebases you are working on

## Required changes to `LocalSettings.php`

In order to wire up your local codebase with the environment you will need to set

```php
$GLOBALS['bsgOverrideUEModulePDFPdfServiceURL'] = 'http://app-bshtml2pdf:8080/BShtml2PDF';
$GLOBALS['bsgOverrideESBackendHost'] = 'app-elasticsearch';

$GLOBALS['wgEmergencyContact'] = $GLOBALS['wgDBname'] . "@wiki.local";
$GLOBALS['wgPasswordSender'] = $GLOBALS['wgDBname'] . "@wiki.local";
$GLOBALS['wgSMTP'] = [
	'host' => 'util-mailhog',
	'port' => 1025,
	'auth' => false
];


# Only required for BlueSpice 3.x development
$GLOBALS['bsgOverrideVisualDiffHtmlDiffEngineUrl'] = 'http://app-bshtmldiff:8080/BShtmlDiff';
$GLOBALS['wgVirtualRestConfig']['modules']['parsoid']['url'] = 'http://app-parsoid:8000';
$GLOBALS['wgVirtualRestConfig']['modules']['parsoid']['domain']
	= base64_encode( $_SERVER['REQUEST_SCHEME'] . '://' . $_SERVER['SERVER_ADDR'] . '/' . $GLOBALS['wgScriptPath'] );
```

in your `LocalSettings.php` file.

## Troubleshooting

If you are suffering from `low disk watermark [...] exceeded on [...] ...`

You can run
```shell
curl -X PUT "localhost:9200/_cluster/settings" -H 'Content-Type: application/json' -d'
{
  "persistent": {
    "cluster.routing.allocation.disk.watermark.low": "30mb",
    "cluster.routing.allocation.disk.watermark.high": "20mb",
    "cluster.routing.allocation.disk.watermark.flood_stage": "10mb",
    "cluster.info.update.interval": "1m"
  }
}
'
```

You will probably need to re-create the indexes-

### Composer dependencies
If you are running local `composer update` in your Mediawiki/BlueSpice project and running into php (Version 8) dependency issues,
you can create and edit composer config (`vim  ~/.config/composer/config.json`) and add following config:

```
  {
        "config": {
                "minimum-stability": "RC",
                "platform":{
                        "php": "8",
                        "ext-ldap": "8",
                        "ext-intl": "8",
                        "ext-pdo_sqlite": "8",
                        "ext-dom": "8",
                        "ext-simplexml": "8",
                        "ext-xml": "8",
                        "ext-xmlreader": "8",
                        "ext-xmlwriter": "8",
                        "ext-mongodb": "8",
                        "ext-gd": "8",
                        "ext-zip": "8"
                },
                "allow-plugins": {
                        "composer/installers": true
                }
        }
  }
```
