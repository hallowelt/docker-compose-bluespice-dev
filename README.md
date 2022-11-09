# Simple Development Environment for BlueSpice

## Usage

Copy `example.env` to `.env` and set variables for your local environment

Run
```
docker composer up -d
```

## Required changes to `LocalSettings.php`

```php
$GLOBALS['bsgOverrideUEModulePDFPdfServiceURL'] = 'http://app-bshtml2pdf:8080/BShtml2PDF';
$GLOBALS['bsgOverrideESBackendHost'] = 'app-elasticsearch';

# Only required for BlueSpice 3.x development
$GLOBALS['bsgOverrideVisualDiffHtmlDiffEngineUrl'] = 'http://app-bshtmldiff:8080/BShtmlDiff';
$GLOBALS['wgVirtualRestConfig']['modules']['parsoid']['url'] = 'http://app-parsoid:8000';
$GLOBALS['wgVirtualRestConfig']['modules']['parsoid']['domain']
	= base64_encode( $_SERVER['REQUEST_SCHEME'] . '://' . $_SERVER['SERVER_ADDR'] . '/' . $GLOBALS['wgScriptPath'] );
```