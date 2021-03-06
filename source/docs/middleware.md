---
title: Middleware
description: Middleware Guide for the OriginPHP Framework
extends: _layouts.documentation
section: content
---
# Middleware

Middleware is a convenient way to intercept HTTP requests before they reach your application, from there you can modify the request and response that is generated from the requests.

## Creating Middleware

If you need to use middleware for your app, doing so is straightforward, just create the file in `Middleware` folder and make sure both the name and class ends with `Middleware`, for example, the foo middleware would be `FooMiddleware`.

Using the generate command you can quickly create a new Middleware class.

```linux
$ bin/console generate middleware Foo
```

This is what it will look like

```php
namespace App\Middleware;
use Origin\Http\Request;
use Origin\Http\Response;
use Origin\Http\Middleware;

class FooMiddleware extends Middleware
{
    /**
     * Handles the request. This is run on all middlewares first.
     *
     * @param \Origin\Http\Request $request
     */
    public function startup(Request $request) 
    {
        $request->data('foo','bar'); // Change the request data
    }
     /**
     * Processes the response. This is run on all middlewares after the
     * request has been handled.
     *
     * @param \Origin\Http\Request $request
     * @param \Origin\Http\Response $response
     */
    public function shutdown(Request $request,Response $response)
    {
        $response->cookie('foo',$request->data('foo')); // Changes the response
    }
}
```

## Loading Middleware

To load the middleware, you need to call `loadMiddleware` in the initialize method of `src/Application.php` file. When web requests are run, the middlewares will be run, first startup will be called (handle) by each middleware, and modify the request object, then once it has finished, each middleware will run the shutdown (process) using the modified request object.

```php
public function initialize()
{
    $this->loadMiddleware('RequestModifier');
}
```

You can also load middlewares from plugin folders.

```php
public function initialize()
{
    $this->loadMiddleware('MyPlugin.RequestModifier');
}
```

## CSRF Protection Middleware

OriginPHP comes with the CSRF Protection Middleware which is enabled by default, when running the PHPUnit tests, the validation is disabled automatically.

How this works, is it creates a secure long token, then it inserts this into every form when you call Form::create, and a cookie is also created. When data is posted, the CSRF Protection Middleware, checks that the cookie exists and the values match up.

> If you are creating a form without the FormHelper, then you can call Form->csrf() to create the field manually.

The CSRF token is stored in the request parameters which you can access using the request object.

```php
$token = $this->request->params('csrfToken');
```

The CSRF Protection Middleware will also check the request headers for `X-CSRF-Token`, so you can tell libraries such as Jquery to send this automatically.

```js
$.ajaxSetup({
    headers: {
        'X-CSRF-Token': <?= $this->request->params('csrfToken') ?>
    }
});
```