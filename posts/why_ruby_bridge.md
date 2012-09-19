---
title: Why a Ruby Bridge for a C Library
date: '2012-09-18'
description: Why a Ruby Bridge for a C Library
categories:
tags: [mpc]
---

When bridging a C library like GNU MPC to Ruby, I ask myself what I can do to _improve_ the interface. It should not just be a blind 1-1 mapping of the functions. The bridge should improve the interface where possible. There are many different ways we can do this without even working too hard. Let's look at a few (pending) examples that are coming in the MPC gem:

### MPC initialization

There are 2 initialization functions in GNU MPC:

```c
void mpc_init2 (mpc_t z, mpfr_prec_t prec)
void mpc_init3 (mpc_t z, mpfr_prec_t prec_r, mpfr_prec_t prec_i)
```

Both of these require explicit precision parameters. We can write our C extension to use a default precision (`mpfr_get_default_prec()`) if one is not provided:

```ruby
MPC.new(0)       #=> MPC object with value (+0 +0) and default precision
MPC.new(0, 128)  #=> MPC object with value (+0 +0) and precision 128
```

We can already change this default precision with the existing `GMP::F.default_prec=` bridge to `mpfr_set_default_prec(mpfr_prec_t prec)` function. Win!

### MPC Assignment

There are only 10 functions that allow us to set the real and imaginary parts separately. When you filter out the unsigned varieties, integer and double varieties, and `mpf_t` variety, we have the following 5 functions:

```c
int mpc_set_si_si (mpc_t rop, long int op1, long int op2, mpc_rnd_t rnd)
int mpc_set_d_d (mpc_t rop, double op1, double op2, mpc_rnd_t rnd)
int mpc_set_z_z (mpc_t rop, mpz_t op1, mpz_t op2, mpc_rnd_t rnd)
int mpc_set_q_q (mpc_t rop, mpq_t op1, mpq_t op2, mpc_rnd_t rnd)
int mpc_set_fr_fr (mpc_t rop, mpfr_t op1, mpfr_t op2, mpc_rnd_t rnd)
```

So I can use these functions in the following way, as an example:

```ruby
MPC.new([3.14, 3.14])
```

But I cannot try to initialize an MPC with mixed types. Gah! This is of course, because this ability would require 100 functions, rather than our existing 10. _However,_ Ruby will let us write these with ease:

```ruby
MPC.new([3.14, GMP::F(1.414)])       #=> 3.14 (a Float) can be promoted to a GMP::F
MPC.new([GMP::Z(3), GMP::F(1.414)])  #=> GMP::Z(3) can be promoted to a GMP::F
MPC.new([3.14, GMP::Z(2)])           #=> This one is a little harder, but it shouldn't be
                                     #   too much work to discover that 3.14 (a Float),
                                     #   and GMP::Z(2) can each be promoted to GMP::F.
```

### Rounding Modes

I hand-wove the rounding modes above, but we're going to use the same concept of defaults:

```ruby
MPC.new(GMP::F.const_pi, 48)                  #=> (3.141592653589797 +0) with precision 48 and default
                                              #   rounding mode (MPC_RNDNN)
MPC.new(GMP::F.const_pi, 48, MPC::MPC_RNDZZ)  #=> (3.141592653589782 +0) with precision 48 and default
                                              #   rounding mode (MPC_RNDZZ)
```

### Option Hashes

OK, so far we've just looked at default values and the joys of dynamic types. Let's look at a more enjoyable Rubyism: Option Hashes as method arguments:

```ruby
g = GMP::F.const_euler
# The following will all create the same object
MPC.new(g, 48, MPC::MPC_RNDZZ)
# The order of arguments might be easy to forget, so...
MPC.new(g, :prec => 48, :rounding => MPC::MPC_RNDZZ)  # The last argument can just be a Hash
MPC.new(g, :prec => 48, :rounding => :rndzz)          # We can use a symbol to represent the rounding mode
MPC.new(g, prec: 48, rounding: :rndzz)                # Ruby 1.9 syntax
```

This applies to `#to_s` as another good example:

```ruby
g = GMP::F.const_euler
g.to_s                               # Assumes defaults: base = 10, sig figs = 0, rounding = MPC::MPC_RNDNN
g.to_s(16)                           # base = 16; assumes defaults: sig figs = 0, rounding = MPC::MPC_RNDNN
g.to_s(16, 12)                       # base = 16, sig figs = 12; assumes default: rounding = MPC::MPC_RNDNN
g.to_s(16, 12, MPC::MPC_RNDDD)       # base = 16, sig figs = 12, rounding mode = MPC::MPC_RNDDD
# Lamesauce, if I want to specify the rounding, I have to specify the base and the sig figs?
g.to_s(:rounding => MPC::MPC_RNDDD)  # rounding = MPC::MPC_RNDDD; assumes default: base = 10, sig figs = 0
g.to_s(:sig_figs => 12, :base => 8)  # base = 8, sig figs = 12; assumes default: rounding = MPC::MPC_RNDNN
g.to_s(sig_figs: 12, base: 8)        # Ruby 1.9 syntax
```

### Configurable Defaults

In all of the examples above, I have chosen defaults for the user, such as base = 10. I can additionally make these as configurable as the rounding mode and precision:

```ruby
g = GMP::F.const_euler
MPC.new([g, g]).to_s  #=> (5.7721566490153287e-1 5.7721566490153287e-1)
MPC.default_base = 16
MPC.new([g, g]).to_s  #=> (9.3c467e37db0c8p-4 9.3c467e37db0c8p-4)
```

### Localized Configurations

How about blocks? Can we have fun here?

```ruby
# Original default precision is 53
GMP::F.const_log2  #=> 0.69314718055994529e+0
GMP::F.default_prec = 128 do
  # Inside here, default precision is 128
  GMP::F.const_log2  #=> 0.6931471805599453094172321214581765680748e+0
  # ...
end
# Aaand back to default precision of 53
GMP::F.const_log2  #=> 0.69314718055994529e+0
```

I'm sure we can use blocks more but I haven't really thought about it that much...
