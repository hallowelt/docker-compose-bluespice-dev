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