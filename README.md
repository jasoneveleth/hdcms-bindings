# hdcms-bindings

This is a wrapper for the `hdcms` C source. It is not meant to be used as a standalone package. Use [hdcms-helper](https://github.com/jasoneveleth/hdcms-helper) instead.

The current dependency graph for the projects is as follows:

hdcms (C files source + binary) -> hdcms-bindings (C-extension/.so file) -> hdcms-helper (python functions)

This project (the middle one) isn't meant to be used as a standalone project, but I couldn't get python to generate the C-extension and export python functionality in the same package which is why I split it up. If you would like to use it however, it is available at pypi [here](https://pypi.org/project/hdcms/).

## Examples

```python
import hdcms
c9 = hdcms.filenames_to_stats_1d("data/CM1_9_1.txt,data/CM1_9_2.txt,data/CM1_9_3.txt")
c10 = hdcms.filenames_to_stats_1d("data/CM1_10_1.txt,data/CM1_10_2.txt,data/CM1_10_3.txt")
c3 = hdcms.filenames_to_stats_1d("data/CM1_3_1.txt,data/CM1_3_2.txt,data/CM1_3_3.txt")
print(hdcms.compare_compound_1d(c10, c8), "\n")
print(hdcms.compare_all_1d([c10,c8,c9]))
```

You can also use the 2d functions by changing the `_1d` suffix to
`_2d`.


# Extending

If you extend this code, think carefully about who has owner ship
over the data in a vector/matrix and the numpy reference count. The only way
this would be tricky is if a matrix struct exists in two functions, and you
can't tell one of the function's matrix that it doesn't own the data anymore,
and you give the data to a NUMPY function and it get garbage
collected and frees its data (now the matrix struct thinks it has
valid data when it doesn't). I'm pretty sure I don't make this
mistake, but it would be easy to make in the future. The fix is
just to copy the data (which will make it slightly inefficient,
but that's okay).

# References

[check pointer types](https://stackoverflow.com/questions/7774524/segfault-when-trying-to-write-to-a-numpy-array-created-within-a-c-extension)
[example](https://stackoverflow.com/questions/16585083/returning-numpy-array-from-a-c-extension)

## Official documentation

https://docs.python.org/3/c-api/arg.html#other-objects
https://docs.python.org/3/extending/extending.html#building-arbitrary-values
https://numpy.org/doc/stable/user/c-info.how-to-extend.html
https://numpy.org/doc/stable/reference/c-api/array.html#c.PyArray_SimpleNew
https://numpy.org/doc/stable/reference/c-api/dtype.html

# Changelog

0.1.0 Add documentation

0.0.8 Add keyword args: adding `desingularization` and `max_peaks` to the `filenames_to_stats` functions, and `start`, `end`, and `num_bins` to the comparison functions. They all are initalized with the default args they had before.

0.0.7 Change strlcpy to strncpy for Linux

0.0.6 Ignore headers in data files
