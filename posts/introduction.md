---
title: Introduction
date: '2012-09-05'
description: an introduction to Creosote and the existing ruby bindings
categories:
tags: [gmp, mpfr, msieve]
---

The GMP gem is currently available, and allows us to work with multiple-precision **integers**, **rationals**, and **floats**. Here are some examples of what the GMP gem can do today:

### Computing the GCD of \\(2^{64}-1\\) and \\(2^{68}-1\\):

```ruby
?> m = GMP::Z(2) ** 64
=> 18446744073709551616
>> n = GMP::Z(2) ** 68
=> 295147905179352825856
>> (m-1).gcd(n-1)
=> 15
```

### Generate uniformly distributed random numbers between \\(0\\) and \\(2^{128}-1\\), inclusive:

```ruby
?> r = GMP::RandState.new
=> #<GMP::RandState:0x00000100a303d0>
>> r.urandomb(128)
=> 157222500695008773376422121395749431412
>> r.urandomb(128)
=> 134920890566235299823285762767211707848
>> r.urandomb(128)
=> 79824420962299636329402026765963393123
```

### \\(\frac{2^{100}}{2^{102}} - \frac{2^{98}}{2^{102}}\\)

```ruby
>> GMP::Q(GMP::Z(2)**100, GMP::Z(2)**102) - GMP::Q(GMP::Z(2)**98, GMP::Z(2)**102)
=> 3/16
```

### Divide the 100th Fibonacci number by the 99th:

```ruby
>> f1 = GMP::F(GMP::Z.fib(100))
=> 0.35422484817926193e+21
>> f99 = GMP::F(GMP::Z.fib(99))
=> 0.21892299583455517e+21
>> f1 / f99
=> 0.16180339887498949e+1
```

Speed
-----

Of course, Ruby has a _Bignum_ class, so why bother with this C extension? As with most C extensions, we're going this route for the speed:

```ruby
>> Benchmark.bm do |x|
?>   x.report("Bignum") { 400.times { (1..10000).inject(:*) } }
>>   x.report("GMP::Z") { 400.times { GMP::Z.fac(10000) } }
>> end; nil
       user     system      total        real
Bignum 35.140000   4.230000  39.370000 ( 39.420715)
GMP::Z  0.310000   0.000000   0.310000 (  0.311770)
```

That little benchmark shows GMP computing 1000! 400 times in 0.3 seconds, while Bignum takes 35 seconds to do the same task.

How does GMP attain such high speeds? Part of the reason is very low level functions that handle the smaller multiplications, and part of it is simply the [algorithms](http://gmplib.org/manual/Algorithms.html#Algorithms) that GMP employs. From the [GMP homepage](http://gmplib.org/):

> GMP is carefully designed to be as fast as possible, both for small operands and for huge operands. The speed is achieved by using fullwords as the basic arithmetic type, by using fast algorithms, with highly optimised assembly code for the most common inner loops for a lot of CPUs, and by a general emphasis on speed.

The other component of the GMP Ruby gem is the [MPFR](http://www.mpfr.org/) library. Bindings for MPFR are compiled into the GMP gem if MPFR is available when the gem is built. This library provides a fantastic array of operations for floating point numbers, all with correct rounding:

### We have trigonometric functions, logarithmic, and lots of number-theoretical functions:

```ruby
>> GMP::F(10).log2
=> 0.33219280948873622e+1
>> GMP::F(1).asin
=> 0.15707963267948966e+1
```

### We can grab various constants, to different precisions:

```ruby
>> GMP::F.default_prec
=> 53
>> GMP::F.const_pi
=> 0.31415926535897931e+1
>> GMP::F.default_prec = 128
=> 128
>> GMP::F.const_pi
=> 0.3141592653589793238462643383279502884195e+1
```

What does Creosote do to bring these libraries together?
--------------------------------------------------------

Let's look at another C library, currently available as a Ruby gem, called Msieve. Msieve is a super fast integor factorization library:

```ruby
>> Msieve.new(2*3*4*5*6*7*8*9*10).factor!
=> [2, 2, 2, 2, 2, 2, 2, 2, 3, 3, 3, 3, 5, 5, 7]
```

OK, that's great. We can create a new Msieve number from a Ruby _Bignum_, and get back factors, as _Fixnums_ or _Bignums_. But what if we want Msieve to work with _GMP::Z_? _GMP::Z_ and Msieve know nothing about each other. But I want to be able to require both GMP and Msieve, and run something like the following:

```ruby
>> p = GMP.new(2)**127 - 1
=> 170141183460469231731687303715884105727
>> p.factor!
=> [170141183460469231731687303715884105727]
```

In this mock up, `#factor!` is returning an array of _GMP::Z_ integers. Here's what would be going on under the covers:

1. When Creosote is loaded, it will `require` all of the mathematics gems that are installed.
2. Creosote will see that the GMP and Msieve gems have each been `required`, and toss in some new methods.
   * In particular, it will define a `#factor!` method on _GMP::Z_ that converts the receiver to a Ruby _Bignum_, and then to an _Msieve_. `#factor!` will then be called on this _Msieve_ object. The resulting array will then be converted into an array of _GMP::Z_ integers.

We will be seeing lots of work like this in the _near_ future. Be excited!
