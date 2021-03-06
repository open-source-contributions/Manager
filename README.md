<p align="center">
<img src="https://i.ibb.co/vq97y2t/laravel-manager.jpg" width="300">
</p>

<p align="center">
<a href="https://travis-ci.org/DeGraciaMathieu/manager"><img src="https://travis-ci.org/DeGraciaMathieu/manager.svg?branch=master" alt="Build Status"></a>
<a href="https://scrutinizer-ci.com/g/DeGraciaMathieu/manager/?branch=master"><img src="https://scrutinizer-ci.com/g/DeGraciaMathieu/manager/badges/coverage.png?b=master" alt="Code Coverage"></a>
<img src="https://img.shields.io/travis/php-v/DeGraciaMathieu/manager.svg" alt="PHP range"> 
<a href="https://packagist.org/packages/degraciamathieu/manager"><img src="https://img.shields.io/packagist/v/degraciamathieu/manager.svg?style=flat-square" alt="Latest Version on Packagist"></a>
<a href='https://packagist.org/packages/degraciamathieu/manager'><img src='https://img.shields.io/packagist/dt/degraciamathieu/manager.svg?style=flat-square' /></a> 
</p>

# DeGraciaMathieu/Manager

Implementation of the Manager pattern existing in Laravel framework.

* [Installation](#installation)
* [Usage](#usage)
* [Work with singleton](#work-with-singleton)
* [Example with Laravel](#example-with-laravel)

## Installation
 
```
composer require degraciamathieu/manager
```

## Usage

This package offers an abstract class `Manager` which needs to be extended to implement the creation of various Driver classes.

```php
<?php

use DeGraciaMathieu/Manager/Manager;

class LoggerManager extends Manager {

    public function createMonologDriver(): LoggerDriver
    {
        return new MonologDriver();
    }

    public function createMockDriver(): LoggerDriver
    {
        return new MockDriver();
    }

    public function getDefaultDriver()
    {
        return 'monolog';
    }
}
```

The `getDefaultDriver` method should also be implemented in your class `Manager`, in order to determine which driver has to be created by default. It's also the right spot to determine the default driver from an environment variable, or a configuration.

```php
<?php

public function getDefaultDriver()
{
    return env('MANAGER_LOGGER_DEFAULT_DRIVER');
}
```
In a matter of consistency, all Driver creations (`createClientDriver`, `createMockDriver`...) should return a class which itself implements the same interface, the LoggerDriver contract in this here case.

```php
<?php

interface LoggerDriver {
    public function doAnything();
}

class MonologDriver implements LoggerDriver {

    public function doAnything()
    {
        echo 'i do anything from the monolog driver';
    }
}

class MockDriver implements LoggerDriver {

    public function doAnything()
    {
        echo 'i do anything from the mock driver';
    }
}
```

From now on, it's possible to use your `Manager`, either by using the default driver:

```php
<?php

(new LoggerManager())->doAnything(); // i do anything from the monolog driver
```

Or by simply specify the driver which needs to be instantiated.

```php
<?php

(new LoggerManager())->driver('monolog')->doAnything(); // i do anything from the monolog driver
(new LoggerManager())->driver('mock')->doAnything(); // i do anything from the mock driver
```
## Work with singleton

You can also cache the creation of Drivers with the `$singleton` property

```php
<?php

use DeGraciaMathieu/Manager/Manager;

class LoggerManager extends Manager {

    /**
     * @var boolean
     */
    protected $singleton = true;

    public function createMonologDriver(): LoggerDriver
    {
        return new MonologDriver();
    }    
}
```

With the `singleton` property you will only create one instance of `MonologDriver`

```php
<?php

$loggerManager = new LoggerManager();

$loggerManager->driver('monolog')->doAnything();
$loggerManager->driver('monolog')->doAnything();
$loggerManager->driver('monolog')->doAnything();
```

## Example with Laravel

[Usage example](https://github.com/DeGraciaMathieu/manager-examples) of the pattern manager in a Laravel project.
