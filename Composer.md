## create plugin for laravel ort other
```sh
# run this command inside of plugin directory
composer init

# on require add at least this plugins
"php": "^7.2|^8.0"
"illuminate/support": "^6.0|^7.0|^8.0"

```

> ServiceProvider.php
```php
<?php

namespace <user>\<package>;

use Illuminate\Support\ServiceProvider as sp;
class ServiceProvider extends sp
{
	public function boot()
	{
		$this->publishes([
			__DIR__ . '/../config/xxx.php' => base_path('config/xxx.php'),
		], 'config');
	}
	public function register()
	{
		$this->mergeConfigFrom(__DIR__ . '/../config/xxx.php', 'xxx');
	}

}
```


> add this to "composer.json" file in Laravel progect you want to install the package inside

> then run `composer upodate`
```php
"repositories": [
	{
		"type": "path",
		"url": "path/to/package",
		"options": {
			"symlink": true
		}
	}
],
"require": {
	"<user>/<package>": "@dev"
}
```

