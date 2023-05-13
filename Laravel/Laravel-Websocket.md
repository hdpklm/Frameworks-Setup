## install composer 
```sh
composer require beyondcode/laravel-websockets
```

## setup
```sh
php artisan vendor:publish --provider="BeyondCode\LaravelWebSockets\WebSocketsServiceProvider" --tag="migrations"
php artisan vendor:publish --provider="BeyondCode\LaravelWebSockets\WebSocketsServiceProvider" --tag="config"
php artisan migrate
```

## config
> env
```env
PUSHER_APP_ID=12345			<--- any app id
PUSHER_APP_KEY=[link-key]	<--- should use when client connect to server
PUSHER_APP_SECRET=asd		<--- any secret code
PUSHER_APP_CLUSTER=mt1		<--- don't change
```

> config\app.php
```php
// in providers, uncommit this line 
App\Providers\BroadcastServiceProvider::class,
```

> config\broadcasting.php
```php
// in set "connections > pusher" link this
'pusher' => [
	'driver' => 'pusher',
	'key' => env('PUSHER_APP_KEY'),
	'secret' => env('PUSHER_APP_SECRET'),
	'app_id' => env('PUSHER_APP_ID'),
	'options' => [
		'cluster' => env('PUSHER_APP_CLUSTER'),
		// 'useTLS' => true,
		// 'encrypted' => true,
		'host' => '127.0.0.1',
		'port' => 6001,
		'scheme' => 'http'
	],
],
```

> [routes/api.php] or [routes/web.php]
```php 
// add this route to authenticate user connect to web socket
Route::middleware('auth:api')->post('/broadcasting/auth', '\Illuminate\Broadcasting\BroadcastController@authenticate');
```

## create & config channels
> routes/channels.php
```php
// verify if user have access to "test" channel
// if return id "true" then user has access, deny otherwise
Broadcast::channel('test-{id}', function ($user, $id) {
	return (int) $user->id == (int) $id;
});
```

```sh
# websocket monitor link
# http://127.0.0.1:8000/laravel-websockets
php artisan make:event [Test]
```

> app\Events\Test.php
```php
class Test {
	// add properties to store data and congif
	//    you want to send to client
	public $msg;

	// setup properties
	public function __construct($msg) {
		$this->msg = $msg;
	}

	// create event for channel
	public function broadcastOn() {
		return new PrivateChannel('test');
	}

	// name of event or channel
	public function broadcastAs() {
		return 'private-test-1';
	}

	// then message is sended
	public function broadcastWith() {
		return [
			"time" => time(),
			"message" => $this->msg
		];
	}

}

```


## run server
```sh
php artisan websockets:serve
```

## connect via javascript
```js
// change this values
const CHANNEL = "test";
const TOKEN = "123123123"
const APP_KEY = "link-key"
const WEB_HOST = "http://127.0.0.1:8000";
const WS_HOST = "ws://127.0.0.1:6001/app/";
const WS_ON_MESSAGE = (msg) => {
	console.log(msg)
}

// no need change
const AUTH_URL = `${WEB_HOST}/api/broadcasting/auth`;
const WS_URL = `${WS_HOST}${APP_KEY}?protocol=7&client=js&version=7.0.3&flash=false`

const AUTH_OPT = {
    "method": "post",
    "headers": {
		"Authorization": "Bearer " + TOKEN,
		"content-type": "application/x-www-form-urlencoded"
    },
    "body": "channel_name=" + CHANNEL + "&socket_id="
}

function on_auth_msg(data) {
    const auth = JSON.parse(data.data)
	AUTH_OPT.body += auth.socket_id

    fetch(AUTH_URL, AUTH_OPT)
        .then(r=>r.json())
        .then(r=>
			webSck.send(JSON.stringify({
				"event": "pusher:subscribe",
                "data": {
                    "auth": r.auth,
                    "channel": CHANNEL
                }
            })
        )
	)
}

function onReceive (res) {
    const data = JSON.parse(res.data)
    if (data.event === "pusher:connection_established")
		on_auth_msg(data)
	else
		(typeof WS_ON_MESSAGE === "function") &&  WS_ON_MESSAGE(data);
}

const webSck = new WebSocket(WS_URL)
webSck.onmessage=onReceive
```
