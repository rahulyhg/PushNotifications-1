# PushNotification #

... is a small php-library to wrap Apple, Google and Windows Push-Notifications into a simple syntax.

Examples:


### Android: ###

```php
use ricwein\PushNotification\PushNotification;
use ricwein\PushNotification\Handler\GCMHandler;

$push = new PushNotification(new GCMHandler());
$push->setServerToken('ExampleGooglePushToken12345678987654321');
$push->addDevice('device-token');
$push->send('message', 'title', ['payload' => 'data']);
```


### iOS: ###

```php
use ricwein\PushNotification\PushNotification;
use ricwein\PushNotification\Handler\APNSHandler;

$push = new PushNotification(new APNSHandler());
$push->setServer([
	'token' => 'path/to/cert.pem',
	'url'   => 'ssl://gateway.sandbox.push.apple.com:2195',
]);
$push->addDevice('<device-token>');
$push->send('message', 'title', ['payload' => 'data']);
```


### Windows: ###

```php
use ricwein\PushNotification\PushNotification;
use ricwein\PushNotification\Handler\WNSHandler;

$push = new PushNotification(new WNSHandler());
$push->setServer([
	'token' => 'wns-push-token',
	'url'   => 'server.url',
]);
$push->addDevice([
	'clientID' => 'clientSecret',
	'OAuth2-Token',
]);
$push->send('message', 'title', ['payload' => 'data']);
```


## usage: ##

This class uses the root-namespace `ricwein\PushNotification`.


### init ###

It's possible to init the PushNotification class with a specific push-handler:

```php
use ricwein\PushNotification\PushNotification;
use ricwein\PushNotification\Handler\GCMHandler;

$push = new PushNotification(new GCMHandler());
```

or without, and adding the push-handler later:

```php
use ricwein\PushNotification\PushNotification;
use ricwein\PushNotification\Handler\GCMHandler;

$push        = new PushNotification();
$pushHandler = new GCMHandler();
$push->setHandler($pushHandler);
```

Available push-handler are:

- Apple:   `PushNotification\Handler\APNSHandler`
- Google:  `PushNotification\Handler\GCMHandler`
- Windows: `PushNotification\Handler\WNSHandler`

They're all extending `PushNotification\PushHandler`


### configuration ###

Since all push-settings are push-handler specific, the according handler has to been added to the PushNotification class before applying the configuration at the PushNotification.

Settings as *server-token* and *server-url* can be set like:

```php
$push->setServerToken('server-token');
$push->setServerUrl('server-url');
```

or as an array:

```php
$push->setServer([
	'token' => 'server-token',
	'url'   => 'server-url',
]);
```

It's also possible to set the configuration directly at the push-handler:

```php
$pushHandler->setServerToken('server-token');
$pushHandler->setServerUrl('server-url');

// or:
$pushHandler->setServer([
	'token' => 'server-token',
	'url'   => 'server-url',
]);

// or even:
$pushHandler = new GCMHandler('server-token', 'server-url');
```

### client-devices ###

The class can send notifications to multiple devices at once. The device-push-tokens are escaped by default.

```php
$push->addDevice('push-token1');
$push->addDevice('push-token2');

// or:
$push->addDevice([
	'push-token1',
	'push-token2',
]);
```

> Note: The WNSHandler allows adding either:
>	- client-id -> client-secret pairs (as array key & value)
>	- or adding directly OAuth2-tokens
>	- and even mixed combinations of both

### sending ###

Sending a messages is as simple as that:

```php
$push->send('message');
```

Adding a title?

```php
$push->send('message', 'title');
```

It's possible to add a payload as an array:

```php
$payload = ['data'];
$push->send('message', null, $payload);
```

Sometimes the given server or device-tokens are expired or sending simply failed. To check if sending was successfully you can use the return-value of send():

```php
if (!$push->send('message')) {
	throw new \Exception('oh-no: Sending PushNotifications failed.');
}
```

### Exceptions ###

This class can throw some default \Exception or \UnexpectedValueException - mostly in case of incorrect configuration or unreachable servers.
