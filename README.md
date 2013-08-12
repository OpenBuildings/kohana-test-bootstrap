# Kohana Test Bootstrap

For testing kohana modules individaully - a composer package holding all the kohana framework system and core modules.

If you want to write a module and test it independantly (via some Cloud CI for example) - then you need the kohana environment, and as its not yet in composer, it could be problematic. This adds all the kohana autoloading and cascading filesystem, as well as the core modules, which you can enable as needed. 

## Usage

As it is intended for testing its recommended that you place it in your require-dev section of the composer.json like this:

  "require-dev": {
    "openbuildings/kohana-test-bootsrap": "dev-master"
  }

After that you should enter the create a "test bootstrap file", in your tests folder like this:

	tests/bootstrap.php

```php
require_once __DIR__.'/../vendor/autoload.php';

Kohana::modules(array(
	// Add some core kohaan modules
	'database' => MODPATH.'database',
	'cache'    => MODPATH.'cache',
	// Your own module - this is where the module you are testing will appear in kohana's cascading filesystem
	'functest' => __DIR__.'/..',
	// Any other kohana modules, required with composer.json can be added like this
	'test'     => __DIR__.'/../tests/testmodule',
));

// Add some custom configuration, to be used only in testing - for example we adda a database config.
Kohana::$config
	->load('database')
		->set(Kohana::TESTING, array(
			'type'       => 'MySQL',
			'connection' => array(
				'hostname'   => 'localhost',
				'database'   => 'test-functest',
				'username'   => 'root',
				'password'   => '',
				'persistent' => TRUE,
			),
			'table_prefix' => '',
			'charset'      => 'utf8',
			'caching'      => FALSE,
		));
```

An example phpunit would be:

	phpunit.xml

```xml
<phpunit colors="true" bootstrap="tests/bootstrap.php">
	<testsuites>
		<testsuite>
			<directory>tests/tests</directory>
		</testsuite>
	</testsuites>
	<filter>
		<blacklist>
			<directory suffix=".php">vendor/</directory>
		</blacklist>
	</filter>
</phpunit>
```

which assigns tests/bootstrap.php as the phpunit's bootstrap file.

## Author

Developed by Ivan Kerin as part of [clippings.com](http://clippings.com)