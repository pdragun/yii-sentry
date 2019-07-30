# Yii Sentry extension
Logging Extension for Yii framework for communication with Sentry logging API

## Installation

**Yii Sentry** is composer library so you can install the latest version with:

```shell
composer require websupport/yii-sentry
```

## Configuration

Add following to your application's config:

### PHP error reporting

```php
'components' => [
    'log' => [
        'class' => \CLogRouter::class,
        'routes' => [
            // your other log routers
            array(
                'class' => \Websupport\YiiSentry\LogRoute::class,
                'levels' => E_ALL,
                'enabled' => !YII_DEBUG,
            ],
        ],
    ],
    'sentry' => [
        'class' => \Websupport\YiiSentry\Client::class,
        'dsn' => '', // Your's DSN from Sentry
    ],
)
```

#### Adding OpenTracing data to Event Context
There is a possibility to store Trace Id and Span Id for trace, when some error occurred.
For this, you need to have installed also [websupport/yii-opentracing](https://github.com/websupport-sk/yii-opentracing).

```php
    'components' => [
        'sentry' => [
            'class' => \Websupport\YiiSentry\Client::class
            'opentracingId' => 'sentry' // or name of your yii-opentracing component
            ...
        ],
        'opentracing' => [ // yii-opentracing component
            'class' => \Websupport\OpenTracing\JaegerOpenTracing::class,
            ...
        ]
    ],
```

### JS error reporting

```php
'preload' => ['sentry'],
'components' => [
    'sentry' => [
        'class' => \Websupport\YiiSentry\Client::class,
        'jsDsn' => '', // Your's DSN from Sentry
    ],
]
```

#### Sending user context to JS
`\Websupport\YiiSentry\Client` component has public method: `setJsUserContext($context)` which will send `$context` to Raven JS instance.
You can call this method multiple times from any part of the system. Recommended way however is to use it in `CWebUser` class right after init.