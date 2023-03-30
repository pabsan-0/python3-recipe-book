# python3-recipes  
A recipe book with handy python snippets.  

## Obscure callback tricks 

### Pass a callback that does not take arguments with arguments
- Using a wrapper function
```
def wrapper(arg):  
    def private_implementation_proxy():
        print("here is your %d!" % arg)
        return arg * 2 
    return private_implementation_proxy
   
object.method_taking_no_args = wrapper(2)
```

- Use the __get__ method of a function
```
def my_callback(arg):
    return 2*arg

object.method_taking_no_args = my_callback.__get__(2)

# "normal" function call 
my_callback.__get__(2)()
```


## Multiprocessing  
#### Branched parallel  
#### Real time  
```
import multiprocessing as mp
from queue import PriorityQueue
import time
import queue    ## gets you Queue, Full, Empty


def tasker1(arg, qin, qout):
    # Setup: flush queue
    while not qin.empty():
        qin.get_nowait()

    # Real-time mode: loop indefinitely
    while 1:
        a = qin.get()
        a += arg
        print("Tasker1:", a)
        qout.put(a)
        time.sleep(1)


def tasker2(arg, qin, qout):
    # Setup: flush queue
    while not qin.empty():
        qin.get_nowait()

    # Real-time mode: loop indefinitely
    while 1:
        try:
            a = qin.get_nowait(timeout=0.1)
        except queue.Empty:
            pass
        else:
            a -= arg
            print("Tasker2:", a)
            qout.put(a)
            time.sleep(1)
            

class tasker3:
    def __init__(self, qsignal):
        mode = 0
        while 1:
            mode = qsignal.get()
            if mode == 1:
                a = self.subtask1()
            else:
                a = self.subtask2()
            print(a)

    @staticmethod
    def subtask1():
        return 1
        
    @staticmethod
    def subtask2():
        return 2

if __name__ == '__main__':
    q1 = mp.Queue()
    q2 = mp.Queue()
    qs = queue.Queue()

    pTasker1 = mp.Process(target=tasker1, args=(1,q1,q2))
    pTasker2 = mp.Process(target=tasker2, args=(1,q2,q1))
    pTasker3 = mp.Process(target=tasker3, args=(qs,))

    for process in [pTasker1, pTasker2, pTasker3]:
        process.start()

```

## Profiling and tracing  
### Time profiling   
- Install profiler  
    ```  
    $ pip3 install line-profiler   
    ```  
- Basic usage: call script normally
    ```  
    from line_profiler import LineProfiler  
    lp = LineProfiler()  

    @lp  
    def my_function():  
        pass  
  
    lp.print_stats()  
    ```  
- Print report on each function call, bit overkill
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
    
- More advanced usage: now is when it gets interesting

    ```
    # These you need for the profiling
    import io
    from line_profiler import LineProfiler
    lp = LineProfiler()

    # These are custom arbitrary modules
    from . import Foo
    from .bar import Bar

    # Overwrite functions to monitor, even if they are module-deep
    # This saves you decorating source code
    Foo.mainloop = lp(Foo.mainloop)
    Bar.snapshot = lp(Bar.snapshot)
    Bar.__call__ = lp(Bar.__call__)

    # Start the actual machinery (blocking call)
    try:
        node.start() 

    # Store the profiler output to a file rather than stdout
    except KeyboardInterrupt:
        s = io.StringIO()
        lp.print_stats(stream=s)
        with open('time-profiler-results.txt', 'w+') as f:
            f.write(s.getvalue())

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
