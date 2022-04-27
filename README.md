# python3-recipes
A recipe book with handy python snippets.


## Multiprocessing
### Branched parallel
### Real time

## Profiling and tracing

### Time profiling
Monitor the time it takes to run each line within a function. Instance, decorate, run and then print stats.
```
$ pip3 install line-profiler

from line_profiler import LineProfiler
lp = LineProfiler()

@lp
def my_function():
    pass

lp.print_stats()
```
Chain these two decorators so that the stats are printed on every single function run.
```
@lp
@lp_print_every_iter
def my_function():
    pass
```


### Memory profiling

### Line tracing
This runs a script normally, printing out which line is being executed at a time. The ignore dir part is so you only see lines in YOUR code, not modules.
```
alias pytrace="python3 -m trace --ignore-dir=$(python3 -c 'import sys ; print(":".join(sys.path)[1:])') -t "
pytrace myscript.py
```
## Decorators
### Decorating a function
### Decorating a class instance
 
## Context managers
### Time limiter

## Misc.
#### UML Class diagrams
#### Pyrequirements
