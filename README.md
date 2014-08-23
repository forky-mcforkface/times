**NOTE:**  
This library will not be maintained any further.  Version 0.7 has been
rewritten to provide the same functionality, but internally implemented on top
of the excellent (and much more powerful) [Arrow](http://crsmithdev.com/arrow/)
library, by [Chris Smith](https://github.com/crsmithdev).  If you still use
Times, consider using Arrow instead.


Times
=====

Build status:
[![Build Status](https://travis-ci.org/nvie/times.svg?branch=master)](http://travis-ci.org/nvie/times)
[![Coverage Status](https://img.shields.io/coveralls/nvie/times.svg)](https://coveralls.io/r/nvie/times?branch=master)


Times is a small, minimalistic, Python library for dealing with time
conversions to and from timezones, for once and for all.

It is designed to be simple and clear, but also opinionated about good and bad
practices.


Rationale
---------

Python's `datetime` library and the `pytz` library are powerful, but because
they don't prescribe a standard practice of working with dates, everybody is
free to pick his or her own way.

`times` tries to make working with times and timezones a little less of
a clusterfuck and hopefully set a standard of some sort.

It still uses `datetime` and `pytz` under the covers, but as long as you never
use any timezone related stuff outside `times`, you should be safe.


Accepting time
--------------

Never work with _local_ times.  Whenever you must accept local time input (e.g.
from a user), convert it to universal time immediately:

```pycon
>>> times.to_universal(local_time, 'Europe/Amsterdam')
datetime.datetime(2012, 2, 1, 10, 31, 45, 781262)
```

The second argument can be a `pytz.timezone` instance, or a timezone string.

If the `local_time` variable already holds timezone info, you _must_ leave out
the source timezone from the call.

To enforce best practices, `times` will never implicitly convert times for you,
even if that would technically be possible.


Date Strings
------------
If you want to accepting datetime representations in string form (for example,
from JSON APIs), you can convert them to universal datetimes easily:

```pycon
>>> import time, times
>>> print times.to_universal('2012-02-03 11:59:03-0500')   # auto-detects source timezone
```

`Times` utilizes the string parsing routines available in [dateutil][3].  Note
that the source timezone is auto-detected from the string.  If the string
contains a timezone offset, you are not allowed to explicitly specify one.

If the string does not contain any timezone offset, you _must_ specify the
source timezone explicitly:

```pycon
>>> print times.to_universal('2012-02-03 11:59:03', 'Europe/Amsterdam')
```

This is the inverse of `times.format()`.


POSIX timestamps
----------------
If you prefer working with UNIX (POSIX) timestamps, you can convert them to
safe datetime representations easily:

```pycon
>>> import time, times
>>> print times.to_universal(time.time())
2012-02-03 11:59:03.588419
```

Note that `to_universal` auto-detects that you give it a UNIX timestamp.

To get the UNIX timestamp representation of a universal datetime, use:

```pycon
>>> print times.to_unix(universal_time)
```


Current time
------------

When you want to record the current time, you can use this convenience method:

```pycon
>>> import times
>>> print times.now()
datetime.datetime(2012, 2, 1, 11, 51, 27, 621491)
```


Presenting times
----------------

To _present_ times to the end user of your software, you should explicitly
format your universal time to your user's local timezone.

```pycon
>>> import times
>>> now = times.now()
>>> print times.format(now, 'CET')
2012-02-01 21:32:10+0100
```

As with the `to_universal` function, the second argument may be either
a timezone instance or a timezone string.

**Note**: It _is_ possible to convert universal times to local times, using
`to_local`).  However, you probably shouldn't do it, unless you want to
`strftime()` the resulting local date multiple times.  In any other case, you
are advised to use `times.format()` directly instead.

[3]: http://labix.org/python-dateutil#head-c0e81a473b647dfa787dc11e8c69557ec2c3ecd2
