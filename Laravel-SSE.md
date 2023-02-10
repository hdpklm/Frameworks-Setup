# TODO: se tiene que mejorar

> install redis client for laravel
```sh
composer require predis/predis --ignore-platform-req=ext-sodium
```

> route/web
```php
Route::get('/sse', function () {return view('sse');});
```

> route/api
```php
Route::get("/sse", [App\Http\Controllers\SSE::class, "index"]);
```

> app\Http\Controllers\SSE.php
  ```php
  <?php

  namespace App\Http\Controllers;

  use DB;
  use Illuminate\Http\Request;
  use Symfony\Component\HttpFoundation\StreamedResponse;
  use Illuminate\Support\Facades\Redis;

  class SSE extends Controller
  {
  	private $inddex = 0;

  	private function msg($msg)
  	{
  		echo "id: {$this->inddex}\n";
  		echo "event: message\n";
  		echo "data: {$msg}\n\n";
  		ob_flush();
  		flush();

  		$this->inddex++;
  	}

  	public function index(Request $request)
  	{
  		// Redis::set('user:1', '{"name":"Hassan Daoud"}');
  		// $values = Redis::lrange('company', 5, 10);

  		$func = function () use ($request) {
  			$wait = true;
  			$sleep = 200 * 1000;

  			while ($wait) {
  				$user = Redis::get('user:2');
  				// $user = json_decode($user, true);
  				// $name = $user['name'] ?? 'x-unknown';

  				$this->msg("{$user}");

  				usleep($sleep);
  			}

  			return "close";
  		};

  		$headers = [
  			'Content-Type' => 'text/event-stream',
  			'X-Accel-Buffering' => 'no',
  			'Cach-Control' => 'no-cache',
  		];

  		return new StreamedResponse($func, 200, $headers);
  	}
  }
  ```


> resources\views\sse.blade.php
  ```html
  <header class="bmd-layout-header">
      <button onclick="startConnection()">Start/Stop</button>
      <div class="navbar navbar-dark fixed-top" style="background:#263238;  height: 100%;overflow:auto">
          <ul class="nav justify-content-end" id="list" style="color:#FFFFFF;   display:flex; flex-direction: column-reverse;"></ul>
      </div>
  </header>
  
  <script>
      var init = null;
      var source = null;
      window.onbeforeunload = () => {
          source.close()
      };
  
      function startConnection() {
          if (init) {
              source.close()
              init = null;
              return;
          }
  
          init = true;
          if (typeof(EventSource) !== "undefined") {
              const list = document.querySelector('#list');
  
              source = new EventSource("/api/sse");
              source.addEventListener('message', event => {
                  list.innerHTML += `<li class="nav-item">${event.data}</li>`
              }, false);
              source.addEventListener('error', event => {
                  if (event.readyState == EventSource.CLOSED) {
                      console.log('Event was closed');
                      console.log(EventSource);
                  }
              }, false);
          } else {
              console.log("Sorry, your browser does not support server-sent   events...");
          }
      }
  </script>
  ```



