Monolog module for Zend Framework
=================================

Module to integrate Monolog with Zend Framework projects.

[![Build Status](https://img.shields.io/travis/neeckeloo/MonologModule.svg?style=flat-square)](http://travis-ci.org/neeckeloo/MonologModule)
[![Latest Stable Version](http://img.shields.io/packagist/v/neeckeloo/monolog-module.svg?style=flat-square)](https://packagist.org/packages/neeckeloo/monolog-module)
[![Total Downloads](http://img.shields.io/packagist/dt/neeckeloo/monolog-module.svg?style=flat-square)](https://packagist.org/packages/neeckeloo/monolog-module)
[![Coverage Status](http://img.shields.io/coveralls/neeckeloo/MonologModule.svg?style=flat-square)](https://coveralls.io/r/neeckeloo/MonologModule)
[![Dependencies Status](https://www.versioneye.com/user/projects/5465c709f8a4ae1c9900010d/badge.svg?style=flat-square)](https://www.versioneye.com/user/projects/5465c709f8a4ae1c9900010d)

## Requirements

* PHP 5.6, PHP 7.0 or higher
* [monolog/monolog ^1.11](http://www.github.com/Seldaek/monolog)
* [zendframework/zend-servicemanager ^2.7.5 || ^3.0.3](https://github.com/zendframework/zend-servicemanager)

## Installation

MonologModule must be installed through Composer. For Composer documentation, please refer to [getcomposer.org](http://getcomposer.org).

You can install the module from command line:

```sh
$ composer require neeckeloo/monolog-module:~0.1
```

Enable the module by adding `MonologModule` key in your `application.config.php` file.

## Usage

### Configuring a logger

This is the configuration of a logger that can be retrieved with key ```Log\App``` in the service manager. A channel name ```default``` is also defined to identify to which part of the application a record is related.

```php
return [
    'monolog' => [
        'loggers' => [
            'Log\App' => [
                'name' => 'default',
            ],
        ],
    ],
];
```

### Adding a handler

The logger itself does not know how to handle a record. It delegates it to some handlers. The code above registers two handlers in the stack to allow handling records in two different ways.

```php
return [
    'monolog' => [
        'loggers' => [
            'Log\App' => [
                'name' => 'default',
                'handlers' => [
                    'stream' => [
                        'name' => StreamHandler::class,
                        'options' => [
                            'path'   => 'data/log/application.log',
                            'level'  => Logger::DEBUG,
                        ],
                    ],
                    'fire_php' => [
                        'name' => FirePHPHandler::class,
                    ],
                ],
            ],
        ],
    ],
];
```

### Using processors

If you want to add extra information (tags, user IP, ...) to the records before they are handled, you should add some processors. The code above adds two processors that add an unique identifier and the current request URI, request method and client IP to a log record.

```php
return [
    'monolog' => [
        'loggers' => [
            'Log\App' => [
                'name' => 'default',
                'handlers' => [
                    'default' => [
                        'name' => StreamHandler::class,
                        'options' => [
                            'path'   => 'data/log/application.log',
                            'level'  => Logger::DEBUG,
                        ],
                    ],
                ],
                'processors' => [
                    UidProcessor::class,
                    WebProcessor::class,
                ],
            ],
        ],
    ],
];
```

### Retrieving a logger

Once the configuration is complete, you can retrieve an instance of the logger as below:

```php
$logger = $serviceManager->get('Log\App');
$logger->debug('debug message');
```

## Testing

``` bash
$ vendor/bin/phpunit
```

## Credits

- [Nicolas Eeckeloo](https://github.com/neeckeloo)
- [All Contributors](https://github.com/RiskioFr/idempotency-module/contributors)

## License

The MIT License (MIT). Please see [License File](https://github.com/RiskioFr/tactician-module/blob/master/LICENSE) for more information.
