HTTP Request Logger
-------------------

An HTTP request logger, that makes it really easy to log incoming & outgoing requests and responses.

This library easily enables you to be able to log outgoing requests (using [Guzzle](http://docs.guzzlephp.org) middleware),
as well as incoming requests using the bundled PSR-compliant middleware that also works with Laravel.  

## Installation

    composer require garbetjie/http-request-logger
    
## Log incoming requests

Add the HTTP request logging middleware to your application's middleware stack. In Laravel, this can be done by adding
the middleware to your `$middleware` property in your HTTP Kernel:

```php
<?php

// app/Http/Kernel.php:

use Garbetjie\Http\RequestLogging\RequestLoggingMiddleware;

class Kernel
{
    // ...
    protected $middleware = [
        RequestLoggingMiddleware::class,
    ];
    // ...
}
```

If you are using a framework that makes use of [PSR-15](https://www.php-fig.org/psr/psr-15), then the same middleware
object can be used. The example below makes use of the Slim framework:

```php
<?php

use Garbetjie\Http\RequestLogging\RequestLoggingMiddleware;

$app = Slim\Factory\AppFactory::create();
$app->add(RequestLoggingMiddleware::class);
$app->run();
```


## Log outgoing requests

If you're using Laravel, you can simply type-hint `GuzzleHttp\ClientInterface` or `GuzzleHttp\Client` wherever Laravel
performs dependency injection, and you will receive an instance of the Guzzle client that contains the logging middleware:

```php
<?php

use GuzzleHttp\ClientInterface;

class MyController
{
    public function index(ClientInterface $client)
    {
        $client->request('GET', 'https://example.org');
    }
}
```

If you're making use of Guzzle anywhere else, it is still easy to use this middleware in Guzzle:

```php
<?php

/* @var Psr\Log\LoggerInterface $logger */

$stack = GuzzleHttp\HandlerStack::create();
$stack->push(new Garbetjie\Http\RequestLogging\RequestLoggingMiddleware($logger, 'debug'), 'logging');
$client = new GuzzleHttp\Client(['stack' => $stack]);
```
