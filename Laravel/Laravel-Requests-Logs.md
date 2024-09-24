# Statistics for Laravel Requests

## Steps

```bash
php artisan make:middleware LogRequestData
```

> app/Http/Middleware/LogRequestData.php
```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;

class LogRequestData
{
    public function handle(Request $request, Closure $next)
    {
        $data = [
            'ip' => $request->ip(),
            'method' => $request->method(),
            'url' => $request->fullUrl(),
            'user_agent' => $request->header('User-Agent'),
            'headers' => $request->headers->all(),
            'query' => $request->query(),
            'body' => $request->all(),
            'proxy_ip' => $request->server('HTTP_X_FORWARDED_FOR'),
        ];

		// Guardar en el log como una sola línea de texto
		$jsonData = json_encode($data, JSON_UNESCAPED_SLASHES);
		Log::channel('requests')->info($jsonData);

        return $next($request);
    }
}
```

> app/Http/Kernel.php
```php
// Add the middleware to the global middleware array
protected $middleware = [
    // ...
    \App\Http\Middleware\LogRequestData::class,
];

// or in the $routeMiddleware array
protected $routeMiddleware = [
    // ...
    'log.request' => \App\Http\Middleware\LogRequestData::class,
];
```

> routes/web.php
```php
// in case you want to only one route to be logged 
Route::get('/dashboard', 'DashboardController@index')->middleware('log.request');
```

> config/logging.php
```php
'channels' => [
    // ...
    'requests' => [
        'driver' => 'daily',
        'path' => storage_path('logs/requests.log'),
        'level' => 'info',
        'days' => 14,
    ],
],
```
