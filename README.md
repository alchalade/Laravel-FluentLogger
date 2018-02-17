# laravel-fluent-logger
fluent logger for laravel
(with Monolog handler for Fluentd )

[fluentd](http://www.fluentd.org/)

[![Build Status](http://img.shields.io/travis/ytake/Laravel-FluentLogger/master.svg?style=flat-square)](https://travis-ci.org/ytake/Laravel-FluentLogger)
[![Coverage Status](http://img.shields.io/coveralls/ytake/Laravel-FluentLogger/master.svg?style=flat-square)](https://coveralls.io/r/ytake/Laravel-FluentLogger?branch=master)
[![Dependency Status](https://www.versioneye.com/user/projects/563e07fa4d415e001b0000ac/badge.svg?style=flat)](https://www.versioneye.com/user/projects/563e07fa4d415e001b0000ac)
[![Scrutinizer Code Quality](http://img.shields.io/scrutinizer/g/ytake/Laravel-FluentLogger.svg?style=flat)](https://scrutinizer-ci.com/g/ytake/Laravel-FluentLogger/?branch=master)

[![License](http://img.shields.io/packagist/l/ytake/laravel-fluent-logger.svg?style=flat-square)](https://packagist.org/packages/ytake/laravel-fluent-logger)
[![Latest Version](http://img.shields.io/packagist/v/ytake/laravel-fluent-logger.svg?style=flat-square)](https://packagist.org/packages/ytake/laravel-fluent-logger)
[![Total Downloads](http://img.shields.io/packagist/dt/ytake/laravel-fluent-logger.svg?style=flat-square)](https://packagist.org/packages/ytake/laravel-fluent-logger)
[![StyleCI](https://styleci.io/repos/45625024/shield)](https://styleci.io/repos/45625024)

[![SensioLabsInsight](https://insight.sensiolabs.com/projects/2ac5d569-39c0-4a80-900d-03760287acba/mini.png)](https://insight.sensiolabs.com/projects/2ac5d569-39c0-4a80-900d-03760287acba)

## usage

### Installation For Laravel and Lumen
Require this package with Composer

```bash
$ composer require ytake/laravel-fluent-logger
```

or composer.json

```json
"require": {
  "ytake/laravel-fluent-logger": "^3.0"
},
```

**Supported Auto-Discovery(^Laravel5.5)**

## for laravel
your config/app.php
```php
'providers' => [
    \Ytake\LaravelFluent\LogServiceProvider::class,
]
```

### publish configure

* basic

```bash
$ php artisan vendor:publish
```

* use tag option

```bash
$ php artisan vendor:publish --tag=log
```

* use provider

```bash
$ php artisan vendor:publish --provider="Ytake\LaravelFluent\LogServiceProvider"
```



### Config

edit config/fluent.php
```php
return [

    'host' => env('FLUENTD_HOST', '127.0.0.1'),

    'port' => env('FLUENTD_PORT', 24224),

    /** @see https://github.com/fluent/fluent-logger-php/blob/master/src/FluentLogger.php */
    'options' => [],

    /** @see https://github.com/fluent/fluent-logger-php/blob/master/src/PackerInterface.php */
    // specified class name
    'packer' => null,

    'tagFormat' => '{{channel}}.{{level_name}}',
];

```

added config/logging.php

```php
return [
    'channels' => [
        'stack' => [
            'driver' => 'stack',
            // always added fluentd log handler
            // 'channels' => ['single', 'fluent'],
            // fluentd only
            'channels' => ['fluent'],
        ],

        'fluent' => [
            'driver' => 'fluent',
            'level' => 'debug',
        ],
        
        'single' => [
            'driver' => 'single',
            'path' => storage_path('logs/laravel.log'),
            'level' => 'debug',
        ],

        'daily' => [
            'driver' => 'daily',
            'path' => storage_path('logs/laravel.log'),
            'level' => 'debug',
            'days' => 7,
        ],

        'slack' => [
            'driver' => 'slack',
            'url' => env('LOG_SLACK_WEBHOOK_URL'),
            'username' => 'Laravel Log',
            'emoji' => ':boom:',
            'level' => 'critical',
        ],

        'syslog' => [
            'driver' => 'syslog',
            'level' => 'debug',
        ],

        'errorlog' => [
            'driver' => 'errorlog',
            'level' => 'debug',
        ],
    ],
];

```

### All logs to fluentd

edit config/app.php
```php
'log' => 'fluent',
```

## fluentd config sample

```
## match tag=local.** (for laravel log develop)
<match local.**>
  type stdout
</match>
```

example (production)

 ```
<match production.**>
  type stdout
</match>
 ```
 and more

## for lumen
Extend \Laravel\Lumen\Application and override the  getMonologHandler() method to set up your own logging config.

example
```php
<?php

namespace App\Foundation;

use Monolog\Logger;
use Fluent\Logger\FluentLogger;
use Ytake\LaravelFluent\FluentHandler;

class Application extends \Laravel\Lumen\Application
{
    /**
     * @return FluentHandler
     */
    protected function getMonologHandler()
    {
        return new FluentHandler(
            new FluentLogger(env('FLUENTD_HOST', '127.0.0.1'), env('FLUENTD_PORT', 24224), []),
            Logger::DEBUG
        );
    }
}

```

## fluentd config sample(lumen)

```
<match lumen.**>
  type stdout
</match>
```

## Author ##

- [Yuuki Takezawa](mailto:yuuki.takezawa@comnect.jp.net) ([twitter](http://twitter.com/ex_takezawa))

## License ##

The code for laravel-fluent-logger is distributed under the terms of the MIT license.
