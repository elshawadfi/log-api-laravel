# Log Api Laravel


## create middleware 


```bash
php artisan make:middleware LogRequests
```

## Middleware code

```
<?php
namespace App\Http\Middleware;
use Closure;
use Illuminate\Support\Facades\Log;
class LogRequests
{
public function handle($request, Closure $next)
{
$request->start = microtime(true);
return $next($request);
}
public function terminate($request, $response)
{
$request->end = microtime(true);
$this->log($request,$response);
}
protected function log($request,$response)
{
$duration = $request->end - $request->start;
$url = $request->fullUrl();
$method = $request->getMethod();
$ip = $request->getClientIp();
$req = $request->all();
$req = json_encode($req);
$headers = json_encode(\Request::header());
$log = "{$ip}: {$method}@{$url} - {$duration}ms \n".
"Request : {[$req]} \n".
"Headers : {[$headers]} \n".
"Response : {$response->getContent()} \n";
Log::info($log);
}
}
```

## To run the middleware you should register your middleware on `app/Http/Kernel.php` file.

```
protected $middlewareGroups = [
...,
'api' => [
'bindings',
'request_logger'
],
];
```

```
protected $routeMiddleware = [
...
'request_logger' => \App\Http\Middleware\LogRequests::class
];
```

API requests should be logged in `storage/logs/laravel.log` file








