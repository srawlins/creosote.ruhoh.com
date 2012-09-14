---
title: New Rake Tasks
date: '2012-09-10'
description:
categories:
tags: [gmp, mpfr, development]
---

For the last few years, I have exclusively used hand-written shell scripts and Windows batch files to automate compilation and testing across different Ruby VMs, versions of GMP, and versions of MPFR. These are checked into git and will probably stay for a while. The primary script I use on Unix systems to test is `ext/ccmt.sh`. The name stands for "clean, compile, make, and test."

It's usage isn't great, but it has always been super simple for me:

```bash
$ ./ccmt.sh                    # clean, configure, make, and test using current Ruby, and
                               # GMP and MPFR libraries available in standard paths.
$ ./ccmt.sh 1.8.7              # use Ruby 1.8.7 instead, via `rvm use 1.8.7`
$ ./ccmt.sh --no-mpfr          # do not include MPFR.
$ ./ccmt.sh 1.8.7 5.0.2        # use Ruby 1.8.7 and pass
                               # "--with-gmp-dir=/usr/local/gmp-5.0.2" to extconf.rb.
$ ./ccmt.sh 1.9.2 4.3.2 2.4.2  # use Ruby 1.9.2, pass "--with-gmp-dir=/usr/local/gmp-4.3.2"
                               # and "--with-mpfr-dir=/usr/local/mpfr-2.4.2" to extconf.rb.
```

You can see that a lot of this script includes hard-coded ideas like `/usr/local` being my installation base for C libraries, and RVM being my source of Ruby installations. When moving this stuff to rake tasks, I'm going to try to hard code less, although I don't know how I'm going to allow for a different prefix... Anyways, here is what is now available through rake:

### rake clean

```bash
$ rake clean
cd ext && make clean
```

### rake extconf

```bash
$ rake extconf
cd ext && make clean
cd ext && ruby extconf.rb --with-gmp-dir=/usr/local/gmp-5.0.5 --with-mpfr-dir=/usr/local/mpfr-3.1.1
checking for gmp.h... yes
checking for __gmpz_init() in -lgmp... yes
checking for mpfr.h... yes
checking for mpf2mpfr.h... yes
checking for mpfr_init() in -lmpfr... yes
checking for SIZEOF_INTPTR_T... yes
creating Makefile
```

You can see that I now have a default GMP and MPFR version. I wanted these _always_ specifically passed in to `extconf.rb` so that I _always_ know what versions I am compiling against. I used to explicitly test `extconf.rb` without passing any directories, but I think that this amounts to testing _MKMF_, which I should assume is functional.

### rake make

```bash
craig:gmp sam$ rake make
cd ext && make clean
cd ext && ruby extconf.rb --with-gmp-dir=/usr/local/gmp-5.0.5 --with-mpfr-dir=/usr/local/mpfr-3.1.1
checking for gmp.h... yes
checking for __gmpz_init() in -lgmp... yes
checking for mpfr.h... yes
checking for mpf2mpfr.h... yes
checking for mpfr_init() in -lmpfr... yes
checking for SIZEOF_INTPTR_T... yes
creating Makefile
cd ext && make
compiling gmp.c
compiling gmpbench_timing.c
compiling gmpf.c
compiling gmpq.c
compiling gmprandstate.c
compiling gmpz.c
compiling mprnd.c
linking shared-object gmp.bundle
```

This one sounds weird, but I think that this is typically because `make` is usually the end-all task for a C project. In a Ruby C extension, however, it is just one step among many, so I have no issue automating it with rake.

### rake test

```bash
$ rake test
cd ext && make clean
cd ext && ruby extconf.rb --with-gmp-dir=/usr/local/gmp-5.0.5 --with-mpfr-dir=/usr/local/mpfr-3.1.1
checking for gmp.h... yes
checking for __gmpz_init() in -lgmp... yes
checking for mpfr.h... yes
checking for mpf2mpfr.h... yes
checking for mpfr_init() in -lmpfr... yes
checking for SIZEOF_INTPTR_T... yes
creating Makefile
cd ext && make
compiling gmp.c
compiling gmpbench_timing.c
compiling gmpf.c
compiling gmpq.c
compiling gmprandstate.c
compiling gmpz.c
compiling mprnd.c
linking shared-object gmp.bundle
ld: warning: duplicate dylib /usr/local/gmp-5.0.5/lib/libgmp.10.dylib
cd test && ruby unit_tests.rb
Run options: 

# Running tests:

............................................................................................
Suppressing error that should be fixed with a recent version of Test::Unit
installed.
...........................

Finished tests in 2.603890s, 45.7009 tests/s, 6300.1893 assertions/s.

119 tests, 16405 assertions, 0 failures, 0 errors, 0 skips
```

That's the big one. `rake test` relies on clean, extconf, and make. I'm going to work on using rake's file intelligence so that it doesn't run all of the prerequisite jobs if the files have not been modified.

How do we set different GMP and MPFR vresions?

### rake test GMP=4.3.2 MPFR=2.4.2

```bash
$ rake test GMP=4.3.2 MPFR=2.4.2
cd ext && make clean
cd ext && ruby extconf.rb --with-gmp-dir=/usr/local/gmp-4.3.2 --with-mpfr-dir=/usr/local/mpfr-2.4.2
checking for gmp.h... yes
checking for __gmpz_init() in -lgmp... yes
checking for mpfr.h... yes
checking for mpf2mpfr.h... yes
checking for mpfr_init() in -lmpfr... yes
checking for SIZEOF_INTPTR_T... yes
creating Makefile
cd ext && make
compiling gmp.c
compiling gmpbench_timing.c
compiling gmpf.c
gmpf.c: In function ‘r_gmpfsg_mpfr_buildopt_tls_p’:
gmpf.c:1160: warning: implicit declaration of function ‘mpfr_buildopt_tls_p’
gmpf.c: In function ‘r_gmpfsg_mpfr_buildopt_decimal_p’:
gmpf.c:1166: warning: implicit declaration of function ‘mpfr_buildopt_decimal_p’
compiling gmpq.c
compiling gmprandstate.c
compiling gmpz.c
gmpz.c: In function ‘r_gmpz_div’:
gmpz.c:1262: warning: implicit conversion shortens 64-bit value into a 32-bit value
compiling mprnd.c
linking shared-object gmp.bundle
cd test && ruby unit_tests.rb
Run options: 

# Running tests:

............................................................................................
Suppressing error that should be fixed with a recent version of Test::Unit
installed.
...........................

Finished tests in 2.907325s, 40.9311 tests/s, 5638.1725 assertions/s.

119 tests, 16392 assertions, 0 failures, 0 errors, 0 skips
```

We're just tapping into environment variables. Simple. Tried. True. And to hide MPFR?

### rake test MPFR=no-mpfr

```bash
$ rake test MPFR=no-mpfr
cd ext && make clean
cd ext && ruby extconf.rb --with-gmp-dir=/usr/local/gmp-5.0.5 --no-mpfr
checking for gmp.h... yes
checking for __gmpz_init() in -lgmp... yes
checking for SIZEOF_INTPTR_T... yes
creating Makefile
cd ext && make
compiling gmp.c
compiling gmpbench_timing.c
compiling gmpf.c
gmpf.c: In function ‘r_gmpf_initialize’:
gmpf.c:78: warning: unused variable ‘base’
compiling gmpq.c
compiling gmprandstate.c
compiling gmpz.c
gmpz.c: In function ‘r_gmpz_div’:
gmpz.c:1262: warning: implicit conversion shortens 64-bit value into a 32-bit value
compiling mprnd.c
linking shared-object gmp.bundle
cd test && ruby unit_tests.rb
Run options: 

# Running tests:

.........................................................................
Suppressing error that should be fixed with a recent version of Test::Unit
installed.
...........................

Finished tests in 2.427958s, 41.1869 tests/s, 2111.6510 assertions/s.

100 tests, 5127 assertions, 0 failures, 0 errors, 0 skips
```

You can see that we run far fewer tests when MPFR is not present.

### Different rubies?

And how do we test with different Ruby VMs???

We don't. :(  I mean, certainly:

```bash
$ rvm use 1.8.7
$ rake test
```

works, but this is not automated. I am looking into `rvm do`, which is documented [here](https://rvm.io/set/tests/). I will likely script this or something to generate nice reports.

Oh, I have one more fun rake task:

### rake dependencies:list

```bash
$ rake dependencies:list
GMP packages installed into /usr/local:
gmp-4.3.2
gmp-5.0.1
gmp-5.0.2
gmp-5.0.5

MPFR packages installed /usr/local:
mpfr-2.4.2
mpfr-3.0.0
mpfr-3.0.1-rc1
mpfr-3.1.0
mpfr-3.1.1
```

This is moderately useful... It replaces a simple `ls` command, but I think I'll keep it around.
