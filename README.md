## Analog - PHP 5.3+ logging class

* Copyright: (c) 2012 Johnny Broadway
* License: http://www.opensource.org/licenses/mit-license.php

A short and simple logging class for based on the idea of using closures for
configurability and extensibility. Functions as a static class, but you can
completely control the formatting and writing of log messages through closures.

By default, this class will write to a file named `/tmp/log.txt` using a format
`"machine - date - level - message\n"`.

I wrote this because I wanted something very small and simple like
[KLogger](https://github.com/katzgrau/KLogger), and preferably not torn out
of a wider framework if possible. After searching, I wasn't happy with the
single-purpose libraries I found. With KLogger for example, I didn't want an
object instance but rather a static class, and I wanted more flexibility in
the back-end.

I also found some that had the flexibility also had more complexity, for example
[Monolog](https://github.com/Seldaek/monolog) is 25 source files (not incl. tests).
With closures, this seemed to be a good balance of small (57 total loc without
comments) but still flexible.

> What about Analog, the logfile analyzer? Well, since it hasn't been updated
> since 2004, I think it's safe to call a single-file PHP logging class the
> same thing without it being considered stepping on toes :)

Usage:

```php
<?php

require_once ('Analog.php');

// Default logging to /tmp/log.txt
Analog::log ('Log this error', Analog::ERROR);

// Create a custom object format
Analog::format (function ($machine, $level, $message) {
  return array (
		'machine' => $machine,
		'date'    => gmdate ('Y-m-d H:i:s'),
		'level'   => $level,
		'message' => $message
	);
});

// Log to a MongoDB log collection
Analog::location (function ($message) {
	static $conn = null;
	if (! $conn) {
		$conn = new Mongo ('localhost:27017');
	}
	$conn->mydb->log->insert ($message);
});

// Log an error
Analog::log ('The sky is falling!');

// Log some debug info
Analog::log ('Debugging info', Analog::DEBUG);

?>
```
