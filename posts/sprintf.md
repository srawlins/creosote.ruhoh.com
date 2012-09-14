---
title: sprintf now available!
date: '2012-09-14'
description: sprintf now available!
categories:
tags: [gmp, mpfr]
---

For the last \\(n\\) years, I have never gotten around to bridging the sprintf functions from GMP, or MPFR. GMP lists the format strings [here](http://gmplib.org/manual/Formatted-Output-Strings.html#Formatted-Output-Strings) and the functions [here](http://gmplib.org/manual/Formatted-Output-Functions.html#Formatted-Output-Functions). MPFR documents format strings and functions [here](http://www.mpfr.org/mpfr-current/mpfr.html#Formatted-Output-Functions).

### Methods

So basically I've implemented `GMP.sprintf` and `MPFR.sprintf` now:

```ruby
>> z127 = GMP::Z(127)
=> 127
>> z256 = GMP::Z(256)
=> 256
>> GMP.sprintf("%Zd+%d + %Zd+%d = %Zd, %s", z127, 1, z127, 1, z256, "Hooray!")
=> "127+1 + 127+1 = 256, Hooray!"
>> GMP.sprintf("%5Zd+%5d + %5Zd+%5d = %5Zd, %s", z127, 1, z127, 1, z256, "Hooray!")
=> "  127+    1 +   127+    1 =   256, Hooray!"
>> GMP.sprintf("%Zx+%x + %Zx+%x = %Zx, %s", z127, 1, z127, 1, z256, "Hooray!")
=> "7f+1 + 7f+1 = 100, Hooray!"
>> GMP.sprintf("Pi is not quite %Qd", GMP::Q(22,7))
=> "Pi is not quite 22/7"
```

And the whole reason I finally got to writing `GMP.sprintf`: I hate staring at `GMP::F#to_s` all night long. `GMP.sprintf` is so much prettier:

```ruby
>> pi = GMP::F.const_pi
=> 0.31415926535897931e+1
>> pi.to_s
=> "0.31415926535897931e+1"
>> GMP.sprintf("%Rf", pi)     # MPFR as fixed point float
=> "3.141593"
>> GMP.sprintf("%RDf", pi)    # rounding Down instead of Nearest
=> "3.141592"
>> GMP.sprintf("%1.9Rf", pi)  # and 9 digits past the decimal
=> "3.141592654"
>> GMP.sprintf("%Re", pi)     # scientific format float
=> "3.1415926535897931e+00"
>> GMP.sprintf("%1.9Re", pi)  # and 9 digits past the decimal
=> "3.141592654e+00"
```

Huzzah!

### First code in Ruby

Up until now, every module, class, method, and constant defined in GMP has been written as a C extension, entirely in C. However, trying to push the arguments of a Ruby method into any of

```c
int mpfr_fprintf (FILE *stream, const char *template, ...)
int mpfr_vfprintf (FILE *stream, const char *template, va_list ap)

int mpfr_printf (const char *template, ...)
int mpfr_vprintf (const char *template, va_list ap)

int mpfr_sprintf (char *buf, const char *template, ...)
int mpfr_vsprintf (char *buf, const char *template, va_list ap)

int mpfr_snprintf (char *buf, size_t n, const char *template, ...)
int mpfr_vsnprintf (char *buf, size_t n, const char *template, va_list ap)

int mpfr_asprintf (char **str, const char *template, ...)
int mpfr_vasprintf (char **str, const char *template, va_list ap)
```

Using

```c
VALUE r_gmpfrsg_sprintf(int argc, VALUE *argv, VALUE self) {
    ...
    rb_scan_args (argc, argv, "1*", &format, &list);
    ...
}

rb_define_method(mGMP, "sprintf", r_gmpsg_sprintf, -1)
```

seems to leave me no possible option to pass a list of arguments to a function, or to pass a `va_list` to a function. I googled around, and people in general have a sort of terrified outlook on these kinds of functions. `va_list` doesn't seem to be held in high regard...

Anyways... I wrote most of the method in Ruby, parsing out each `%` formatter, and:

* passing each `%Z`-, `%Q`-, and `%F`-like formatter to `gmp_asprintf`,
* passing each `%P`- and `%R`-like formatter to `mpfr_asprintf` if MPFR is available, and
* passing any other `%` formatters to Ruby's `String#%`.

So there it is. I've implemented a portion (79x) of GMP's tests from `tests/misc/t-printf.c` as well.
