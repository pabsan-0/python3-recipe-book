# python3-recipes  
A recipe book with handy python snippets.  

## Multiprocessing  
#### Branched parallel  
#### Real time  

## Profiling and tracing  
### Time profiling   
- Install profiler  
    ```  
    $ pip3 install line-profiler   
    ```  
- Basic usage  
    ```  
    from line_profiler import LineProfiler  
    lp = LineProfiler()  

    @lp  
    def my_function():  
        pass  
  
    lp.print_stats()  
    ```  
- Print report on each function call  
    ```  
    def lp_print_every_iter(function):
        """ LineProfiler results on function call rather than at the end """
        def wrapper(*args, **kwarg):
            out = function(*args, **kwarg)
            lp.print_stats()
            return out
        return wrapper

    @lp
    @lp_print_every_iter
    def my_function():
        pass
    ``` 
### Memory profiling  
- Install profiler: `$ pip3 install memory-profiler`
- Basic usage
    ```
    from memory_profiler import profile  

    @profile  
    def my_function():  
        pass  
    ```

### Line tracing  
- Runs a script printing out which line is being executed at a time. The ignore dir part is so you only see lines in your code, not modules.
    ```
    $ alias pytrace="python3 -m trace --ignore-dir=$(python3 -c 'import sys ; print(":".join(sys.path)[1:])') -t "
    $ pytrace myscript.py
    ```
    
## Decorators  
### Decorating a function  
### Decorating a class instance  


## Misc.  
### UML Class diagrams  
Within a dir with python code, do:  
- Scan all scripts as a whole: `$ pyreverse -o png .*py`  
- Scan only classes within each: `$ for I in $(ls *.py); do pyreverse -o png $I && mv classes.png $I-classes.png; done`  

### Pyrequirements  
- Install package: `$ pip3 install pipreqs`  
- Generate requirements file: `$ pipreqs --mode no-pin .`   
- To install from file: `$ pip3 install -r requirements.txt`  
