---
title: Using Ruby's IO in a C Extension
date: '2012-11-12'
description:
categories:
---

Using Ruby's IO in a C extension was not altogether straightforward. Specifically, I was tripped up while trying to use `Data_Get_Struct` against an IO. It turns out you can just cast a Ruby IO object (a `VALUE`) to `RFile`, and go from there.

In the following example, `value` is a `VALUE` passed to a method, and `params` is a struct with an `os` field, that is type `FILE*`:

```
if (TYPE (value) != T_FILE) {
  rb_raise (rb_eTypeError, "os must be an IO.");
}
FILE *fd = rb_io_stdio_file(RFILE(value)->fptr);
params->os = fd;
```

There are only two interesting parts to this code. First, taking the `value`'s `fptr`. I found this in Ruby's `include/ruby/ruby.h`:

```c
struct RFile {
    struct RBasic basic;
    struct rb_io_t *fptr;
};
```

I knew to grab the `fptr` by looking at the `struct rb_io_t` and seeing that it contained a `FILE*` (in `include/ruby/io.h`):

```c
typedef struct rb_io_t {
    int fd;                     /* file descriptor */
    FILE *stdio_file;		/* stdio ptr for read/write if available */
    int mode;			/* mode flags: FMODE_XXXs */
    rb_pid_t pid;		/* child's pid (for pipes) */
    int lineno;			/* number of lines read */
    VALUE pathv;		/* pathname for file */
    void (*finalize)(struct rb_io_t*,int); /* finalize proc */

    /* many more fields */
} rb_io_t;
```

I was tempted to just grab that `stdio_file` handle, but I had seen this other method in the `IO.new` code, and decided to use it as a safer route:

```c
FILE *
rb_io_stdio_file(rb_io_t *fptr)
{
    if (!fptr->stdio_file) {
        int oflags = rb_io_fmode_oflags(fptr->mode);
        fptr->stdio_file = rb_fdopen(fptr->fd, rb_io_oflags_modestr(oflags));
    }
    return fptr->stdio_file;
}
```

Bata boom bata bing.
