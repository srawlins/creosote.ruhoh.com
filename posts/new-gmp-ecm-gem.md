---
title: New GMP-ECM gem
date: '2012-11-11'
description:
categories: [gmp, gmp_ecm, development]
---

The [gmp_ecm](https://github.com/srawlins/gmp-ecm) provides Ruby bindings for the GMP-ECM integer factorization C library.

Here's how this one works: The API for GMP-ECM contains essentially one function: `ecm_factor`. This function takes a GMP integer, a value for B1, and an `ecm_params` struct, and searches for a factor of the integer. So the Ruby bindings work essentially. `ecm_factor` is exposed to Ruby via a `GMP::Z#ecm_factor` method. This method, acting on a `GMP::Z` integer, just takes an optional B1 value (default is one million), and a hash of values for the `ecm_params` argument.

```ruby
z = GMP::Z(2)**71 - 1 #=> 2361183241434822606847
z.ecm_factor(1_000_000)  #=> [1, 2361183241434822606847]
z.ecm_factor(1_000_000)  #=> [1, 11091312221959]
z.ecm_factor(1_000_000)  #=> [1, 2361183241434822606847]
z.ecm_factor(1_000_000)  #=> [1, 2361183241434822606847]
z.ecm_factor(1_000_000)  #=> [1, 48639942238007]
```

`GMP::Z#ecm_factor` returns a pair. The first element tells whether or not a factor was found:

* 1 means a factor was found in step 1.
* 2 means a factor was found in step 2.
* 0 means no factor was found.
* a negative value means an error occurred.

Since this `ecm_params` struct contains, like 1000 fields, I'm adding those as I go. They are easy enough to add, but for each field, I need to:

1. Parse it out of the hash passed in. I  want to be flexible, so, for example, a `mpz_t` field, can be passed in as a Ruby Fixnum, Bignum, or `GMP::Z` instance.
2. Document the method in the Yardoc.
3. Minimally write some sanity tests, that the parameters can be passed in.

Additionally, I want to write some functional tests, but this is harder than it seemed. GMP-ECM itself doesn't really have unit tests or functional tests that can show, for example, that a parameter is being parsed and used appropriately.

This is it for now.
