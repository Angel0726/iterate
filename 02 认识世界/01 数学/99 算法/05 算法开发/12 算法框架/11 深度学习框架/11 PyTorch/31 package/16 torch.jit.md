---
title: 16 torch.jit
toc: true
date: 2019-06-29
---
# TORCHSCRIPT

- [Creating TorchScript Code](https://pytorch.org/docs/stable/jit.html#creating-torchscript-code)
- [Mixing Tracing and Scripting](https://pytorch.org/docs/stable/jit.html#mixing-tracing-and-scripting)
- [TorchScript Language Reference](https://pytorch.org/docs/stable/jit.html#torchscript-language-reference)
  - [Types](https://pytorch.org/docs/stable/jit.html#types)
    - [Default Types](https://pytorch.org/docs/stable/jit.html#default-types)
    - [Optional Type Refinement](https://pytorch.org/docs/stable/jit.html#optional-type-refinement)
    - [Classes](https://pytorch.org/docs/stable/jit.html#classes)
  - [Expressions](https://pytorch.org/docs/stable/jit.html#expressions)
    - [Literals](https://pytorch.org/docs/stable/jit.html#literals)
      - [List Construction](https://pytorch.org/docs/stable/jit.html#list-construction)
      - [Tuple Construction](https://pytorch.org/docs/stable/jit.html#tuple-construction)
      - [Dict Construction](https://pytorch.org/docs/stable/jit.html#dict-construction)
    - [Variables](https://pytorch.org/docs/stable/jit.html#variables)
    - [Arithmetic Operators](https://pytorch.org/docs/stable/jit.html#arithmetic-operators)
    - [Comparison Operators](https://pytorch.org/docs/stable/jit.html#comparison-operators)
    - [Logical Operators](https://pytorch.org/docs/stable/jit.html#logical-operators)
    - [Subscripts](https://pytorch.org/docs/stable/jit.html#subscripts)
    - [Function Calls](https://pytorch.org/docs/stable/jit.html#function-calls)
    - [Method Calls](https://pytorch.org/docs/stable/jit.html#method-calls)
    - [Ternary Expressions](https://pytorch.org/docs/stable/jit.html#ternary-expressions)
    - [Casts](https://pytorch.org/docs/stable/jit.html#casts)
    - [Accessing Module Parameters](https://pytorch.org/docs/stable/jit.html#accessing-module-parameters)
  - [Statements](https://pytorch.org/docs/stable/jit.html#statements)
  - [Variable Resolution](https://pytorch.org/docs/stable/jit.html#variable-resolution)
  - [Use of python Values](https://pytorch.org/docs/stable/jit.html#use-of-python-values)
    - [Functions](https://pytorch.org/docs/stable/jit.html#functions)
    - [Attribute Lookup On python Modules](https://pytorch.org/docs/stable/jit.html#attribute-lookup-on-python-modules)
    - [python-defined Constants](https://pytorch.org/docs/stable/jit.html#python-defined-constants)
    - [Module Attributes](https://pytorch.org/docs/stable/jit.html#module-attributes)
  - [Debugging](https://pytorch.org/docs/stable/jit.html#debugging)
    - [Disable JIT for Debugging](https://pytorch.org/docs/stable/jit.html#disable-jit-for-debugging)
    - [Inspecting Code](https://pytorch.org/docs/stable/jit.html#inspecting-code)
    - [Interpreting Graphs](https://pytorch.org/docs/stable/jit.html#interpreting-graphs)
    - [Tracing Edge Cases](https://pytorch.org/docs/stable/jit.html#tracing-edge-cases)
    - [Automatic Trace Checking](https://pytorch.org/docs/stable/jit.html#automatic-trace-checking)
    - [Tracer Warnings](https://pytorch.org/docs/stable/jit.html#tracer-warnings)
- [Frequently Asked Questions](https://pytorch.org/docs/stable/jit.html#frequently-asked-questions)
  - [Builtin Functions](https://pytorch.org/docs/stable/jit.html#builtin-functions)



TorchScript is a way to create serializable and optimizable models from PyTorch code. Any code written in TorchScript can be saved from a python process and loaded in a process where there is no python dependency.

We provide tools to incrementally transition a model from a pure python program to a TorchScript program that can be run independently from python, for instance, in a standalone c++ program. This makes it possible to train models in PyTorch using familiar tools and then export the model via TorchScript to a production environment where it is not a good idea to run models as python programs for performance and multi-threading reasons.

## [Creating TorchScript Code](https://pytorch.org/docs/stable/jit.html#id1)

- *CLASS*`torch.jit.``ScriptModule`(*optimize=True*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/jit.html#ScriptModule)

  The core data structure in TorchScript is the `ScriptModule`. It is an analogue of torch’s `nn.Module`and represents an entire model as a tree of submodules. Like normal modules, each individual module in a `ScriptModule` can have submodules, parameters, and methods. In `nn.Module`s methods are implemented as python functions, but in `ScriptModule`s methods are implemented as TorchScript functions, a statically-typed subset of python that contains all of PyTorch’s built-in Tensor operations. This difference allows your ScriptModules code to run without the need for a python interpreter.`ScriptModule`s be created in two ways:**Tracing:**Using `torch.jit.trace`, you can turn an existing module or python function into a TorchScript program. You must provide example inputs, and we run the function, recording the operations performed on all the tensors. We turn the resulting recording into a TorchScript method that is installed as the `forward` method of a `ScriptModule`. This module also contains any parameters that the original module had as well.Example (tracing a function):`import torch def foo(x, y):     return 2 * x + y traced_foo = torch.jit.trace(foo, (torch.rand(3), torch.rand(3))) `NOTETracing a function will construct a `ScriptModule` with a single `forward` method that implements the function. The resulting `ScriptModule` has no parameters or attributes.Example (tracing an existing module):`import torch import torchvision traced_net = torch.jit.trace(torchvision.models.resnet18(),                              torch.rand(1, 3, 224, 224)) `NOTETracing only records operations done when the given function is run on the given tensors. Therefore, the returned `ScriptModule` will always run the same traced graph on any input. This has some important implications when your module is expected to run different sets of operations, depending on the input and/or the module state. For example,Tracing will not record any control-flow like if-statements or loops. When this control-flow is constant across your module, this is fine and it often inlines the control-flow decisions. But sometimes the control-flow is actually part of the model itself. For instance, a recurrent network is a loop over the (possibly dynamic) length of an input sequence.In the returned `ScriptModule`, operations that have different behaviors in `training` and `eval`modes will always behave as if it is in the mode it was in during tracing, no matter which mode the `ScriptModule` is in.In cases like these, tracing would not be appropriate and scripting is a better choice.**Scripting:**You can write TorchScript code directly using python syntax. You do this using the `@torch.jit.script` decorator (for functions) or `@torch.jit.script_method` decorator (for methods) on subclasses of `ScriptModule`. With this decorator the body of the annotated function is directly translated into TorchScript. TorchScript itself is a subset of the python language, so not all features in python work, but we provide enough functionality to compute on tensors and do control-dependent operations.Example (scripting a function):`import torch @torch.jit.script def foo(x, y):     if x.max() > y.max():         r = x     else:         r = y     return r `NOTEA `@torch.jit.script` decorator will construct a `ScriptModule` with a single `forward` method that implements the function. The resulting `ScriptModule` has no parameters or attributes.Example (scripting a simple module with a Parameter):`import torch class MyModule(torch.jit.ScriptModule):     def __init__(self, N, M):         super(MyModule, self).__init__()         self.weight = torch.nn.Parameter(torch.rand(N, M))      @torch.jit.script_method     def forward(self, input):         return self.weight.mv(input) `Example (scripting a module with traced submodules):`import torch import torch.nn as nn import torch.nn.functional as F  class MyScriptModule(torch.jit.ScriptModule):     def __init__(self):         super(MyScriptModule, self).__init__()         # torch.jit.trace produces a ScriptModule's conv1 and conv2         self.conv1 = torch.jit.trace(nn.Conv2d(1, 20, 5), torch.rand(1, 1, 16, 16))         self.conv2 = torch.jit.trace(nn.Conv2d(20, 20, 5), torch.rand(1, 20, 16, 16))      @torch.jit.script_method     def forward(self, input):       input = F.relu(self.conv1(input))       input = F.relu(self.conv2(input))       return input `

- `torch.jit.``save`(*m*, *f*, *_extra_files=ExtraFilesMap{}*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/jit.html#save)

  Save an offline version of this module for use in a separate process. The saved module serializes all of the methods, submodules, parameters, and attributes of this module. It can be loaded into the c++ API using `torch::jit::load(filename)` or into the python API with `torch.jit.load(filename)`.To be able to save a module, it must not make any calls to native python functions. This means that all submodules must be subclasses of `torch.jit.ScriptModule` as well.DANGERAll modules, no matter their device, are always loaded onto the CPU during loading. This is different from [`torch.load()`](https://pytorch.org/docs/stable/torch.html#torch.load)’s semantics and may change in the future.Parameters**m** – a ScriptModule to save**f** – a file-like object (has to implement write and flush) or a string containing a file name**_extra_files** – Map from filename to contents which will be stored as part of ‘f’WARNINGIf you are using python 2, `torch.save` does NOT support `StringIO.StringIO` as a valid file-like object. This is because the write method should return the number of bytes written; `StringIO.write()` does not do this.Please use something like `io.BytesIO` instead.Example:`m = torch.jit.ScriptModule()  # Save to file torch.jit.save(m, 'scriptmodule.pt')  # Save to io.BytesIO buffer buffer = io.BytesIO() torch.jit.save(m, buffer)  # Save with extra files extra_files = torch._C.ExtraFilesMap() extra_files['foo.txt'] = 'bar' torch.jit.save(m, 'scriptmodule.pt', _extra_files=extra_files) `

- `torch.jit.``load`(*f*, *map_location=None*, *_extra_files=ExtraFilesMap{}*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/jit.html#load)

  Load a `ScriptModule` previously saved with [`save`](https://pytorch.org/docs/stable/jit.html#torch.jit.save)All previously saved modules, no matter their device, are first loaded onto CPU, and then are moved to the devices they were saved from. If this fails (e.g. because the run time system doesn’t have certain devices), an exception is raised. However, storages can be dynamically remapped to an alternative set of devices using the map_location argument. Comparing to [`torch.load()`](https://pytorch.org/docs/stable/torch.html#torch.load), map_location in this function is simplified, which only accepts a string (e.g., ‘cpu’, ‘cuda:0’), or torch.device (e.g., torch.device(‘cpu’))Parameters**f** – a file-like object (has to implement read, readline, tell, and seek), or a string containing a file name**map_location** – can a string (e.g., ‘cpu’, ‘cuda:0’), a device (e.g., torch.device(‘cpu’))**_extra_files** – map from filename to content. The extra filenames given in the map would be loaded and their content would be stored in the provided map.ReturnsA `ScriptModule` object.Example:`torch.jit.load('scriptmodule.pt')  # Load ScriptModule from io.BytesIO object with open('scriptmodule.pt', 'rb') as f:     buffer = io.BytesIO(f.read())  # Load all tensors to the original device torch.jit.load(buffer)  # Load all tensors onto CPU, using a device torch.jit.load(buffer, map_location=torch.device('cpu'))  # Load all tensors onto CPU, using a string torch.jit.load(buffer, map_location='cpu')  # Load with extra files. files = {'metadata.json' : ''} torch.jit.load('scriptmodule.pt', _extra_files = files) print (files['metadata.json']) `

- `torch.jit.``trace`(*func*, *example_inputs*, *optimize=True*, *check_trace=True*, *check_inputs=None*, *check_tolerance=1e-05*, *_force_outplace=False*, *_module_class=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/jit.html#trace)

  Trace a function and return an executable `ScriptModule` that will be optimized using just-in-time compilation.WARNINGTracing only correctly records functions and modules which are not data dependent (e.g., do not have conditionals on data in tensors) and do not have any untracked external dependencies (e.g., perform input/output or access global variables). If you trace such models, you may silently get incorrect results on subsequent invocations of the model. The tracer will try to emit warnings when doing something that may cause an incorrect trace to be produced.Parameters**func** (*callable* *or* [*torch.nn.Module*](https://pytorch.org/docs/stable/nn.html#torch.nn.Module)) – a python function or `torch.nn.Module` that will be run with `example_inputs`. arguments and returns to `func` must be tensors or (possibly nested) tuples that contain tensors.**example_inputs** ([*tuple*](https://docs.python.org/3/library/stdtypes.html#tuple)) – a tuple of example inputs that will be passed to the function while tracing. The resulting trace can be run with inputs of different types and shapes assuming the traced operations support those types and shapes. `example_inputs` may also be a single Tensor in which case it is automatically wrapped in a tupleKeyword Arguments**optimize** ([*bool*](https://pytorch.org/docs/stable/storage.html#torch.FloatStorage.bool)*,* *optional*) – whether or not to apply optimizations. Default: `True`.**check_trace** ([*bool*](https://pytorch.org/docs/stable/storage.html#torch.FloatStorage.bool)*,* *optional*) – check if the same inputs run through traced code produce the same outputs. Default: `True`. You might want to disable this if, for example, your network contains non- deterministic ops or if you are sure that the network is correct despite a checker failure.**check_inputs** (*list of tuples**,* *optional*) – A list of tuples of input arguments that should be used to check the trace against what is expected. Each tuple is equivalent to a set of input arguments that would be specified in `example_inputs`. For best results, pass in a set of checking inputs representative of the space of shapes and types of inputs you expect the network to see. If not specified, the original `example_inputs` are used for checking**check_tolerance** ([*float*](https://docs.python.org/3/library/functions.html#float)*,* *optional*) – Floating-point comparison tolerance to use in the checker procedure. This can be used to relax the checker strictness in the event that results diverge numerically for a known reason, such as operator fusion.ReturnsA `ScriptModule` object with a single `forward()` method containing the traced code. When `func` is a `torch.nn.Module`, the returned `ScriptModule` will have the same set of sub-modules and parameters as `func`.Example:`def f(x):     return x * 2 traced_f = torch.jit.trace(f, torch.rand(1)) `

## [Mixing Tracing and Scripting](https://pytorch.org/docs/stable/jit.html#id2)

In many cases either tracing or scripting is an easier approach for converting a model to TorchScript. We allow you to compose tracing and scripting to suit the particular requirements of a part of a model.

Scripted functions can call traced functions. This is particularly useful when you need to use control-flow around a simple feed-forward model. For instance the beam search of a sequence to sequence model will typically be written in script but can call an encoder module generated using tracing.

Example:

```
import torch

def foo(x, y):
    return 2 * x + y
traced_foo = torch.jit.trace(foo, (torch.rand(3), torch.rand(3)))

@torch.jit.script
def bar(x):
    return traced_foo(x, x)
```

Traced functions can call script functions. This is useful when a small part of a model requires some control-flow even though most of the model is just a feed-forward network. Control-flow inside of a script function called by a traced function is preserved correctly:

Example:

```
import torch

@torch.jit.script
def foo(x, y):
    if x.max() > y.max():
        r = x
    else:
        r = y
    return r


def bar(x, y, z):
    return foo(x, y) + z

traced_bar = torch.jit.trace(bar, (torch.rand(3), torch.rand(3), torch.rand(3)))
```

This composition also works for `ScriptModule`s as well, where it can be used to generate a submodule using tracing that can be called from the methods of a script module:

Example:

```
import torch
import torchvision

class MyScriptModule(torch.jit.ScriptModule):
    def __init__(self):
        super(MyScriptModule, self).__init__()
        self.means = torch.nn.Parameter(torch.tensor([103.939, 116.779, 123.68])
                                        .resize_(1, 3, 1, 1))
        self.resnet = torch.jit.trace(torchvision.models.resnet18(),
                                      torch.rand(1, 3, 224, 224))

    @torch.jit.script_method
    def forward(self, input):
        return self.resnet(input - self.means)
```

## [TorchScript Language Reference](https://pytorch.org/docs/stable/jit.html#id3)

TorchScript is a statically typed subset of python that can either be written directly (using the `@torch.jit.script` decorator) or generated automatically from python code via tracing. When using tracing, code is automatically converted into this subset of python by recording only the actual operators on tensors and simply executing and discarding the other surrounding python code.

When writing TorchScript directly using `@torch.jit.script` decorator, the programmer must only use the subset of python supported in TorchScript. This section documents what is supported in TorchScript as if it were a language reference for a stand alone language. Any features of python not mentioned in this reference are not part of TorchScript.

As a subset of python any valid TorchScript function is also a valid python function. This makes it possible to remove the `@torch.jit.script` decorator and debug the function using standard python tools like `pdb`. The reverse is not true: there are many valid python programs that are not valid TorchScript programs. Instead, TorchScript focuses specifically on the features of python that are needed to represent neural network models in Torch.

- `PYTORCH_JIT=1`

  Setting the environment variable `PYTORCH_JIT=0` will disable all script and tracing annotations. If there is hard-to-debug error in one of your ScriptModules, you can use this flag to force everything to run using native python. This allows the use of tools like `pdb` to debug code.

### [Types](https://pytorch.org/docs/stable/jit.html#id4)

The largest difference between TorchScript and the full python language is that TorchScript only supports a small set of types that are needed to express neural net models. In particular, TorchScript supports:

| Type                 | Description                                                  |
| -------------------- | ------------------------------------------------------------ |
| `Tensor`             | A PyTorch tensor of any dtype, dimension, or backend         |
| `Tuple[T0, T1, ...]` | A tuple containing subtypes `T0`, `T1`, etc. (e.g. `Tuple[Tensor, Tensor]`) |
| `bool`               | A boolean value                                              |
| `int`                | A scalar integer                                             |
| `float`              | A scalar floating point number                               |
| `List[T]`            | A list of which all members are type `T`                     |
| `Optional[T]`        | A value which is either None or type `T`                     |
| `Dict[K, V]`         | A dict with key type `K` and value type `V`. Only `str`, `int`, and `float` are allowed as key types. |

Unlike python, each variable in TorchScript function must have a single static type. This makes it easier to optimize TorchScript functions.

Example (a type mismatch):

```
@torch.jit.script
def an_error(x):
    if x:
        r = torch.rand(1)
    else:
        r = 4
    return r # Type mismatch: r is set to type Tensor in the true branch
             # and type int in the false branch
```

#### [Default Types](https://pytorch.org/docs/stable/jit.html#id5)

By default, all parameters to a TorchScript function are assumed to be Tensor. To specify that an argument to a TorchScript function is another type, it is possible to use MyPy-style type annotations using the types listed above:

Example:

```
@torch.jit.script
def foo(x, tup):
    # type: (int, Tuple[Tensor, Tensor]) -> Tensor
    t0, t1 = tup
    return t0 + t1 + x

print(foo(3, (torch.rand(3), torch.rand(3))))
```

NOTE

It is also possible to annotate types with python 3 type annotations. In our examples, we use comment-based annotations to ensure python 2 compatibility as well.

An empty list is assumed to be `List[Tensor]` and empty dicts `Dict[str, Tensor]`. To instantiate an empty list or dict of other types, use `torch.jit.annotate`.

Example:

```
import torch
from torch.jit import Tensor
from typing import List, Tuple

class EmptyDataStructures(torch.jit.ScriptModule):
    def __init__(self):
        super(EmptyDataStructures, self).__init__()

    @torch.jit.script_method
    def forward(self, x):
        # type: (Tensor) -> Tuple[List[Tuple[int, float]], Dict[str, int]]

        # This annotates the list to be a `List[Tuple[int, float]]`
        my_list = torch.jit.annotate(List[Tuple[int, float]], [])
        for i in range(10):
            my_list.append((x, x))

        my_dict = torch.jit.annotate(Dict[str, int], {})
        return my_list, my_dict
```

#### [Optional Type Refinement](https://pytorch.org/docs/stable/jit.html#id6)

TorchScript will refine the type of a variable of type `Optional[T]` when a comparison to `None` is made inside the conditional of an if-statement. The compiler can reason about multiple `None` checks that are combined with `and`, `or`, and `not`. Refinement will also occur for else blocks of if-statements that are not explicitly written.

The expression must be emitted within the conditional; assigning a `None` check to a variable and using it in the conditional will not refine types.

Example:

```
@torch.jit.script
def optional_unwrap(x, y, z):
  # type: (Optional[int], Optional[int], Optional[int]) -> int
  if x is None:
    x = 1
  x = x + 1

  if y is not None and z is not None:
    x = y + z
  return x
```

#### [Classes](https://pytorch.org/docs/stable/jit.html#id7)

python classes can be used in TorchScript if they are annotated with `@torch.jit.script`, similar to how you would declare a TorchScript function:

```
@torch.jit.script
class Foo:
  def __init__(self, x, y)
    self.x = x

  def aug_add_x(self, inc):
    self.x += inc
```

This subset is restricted:

- All functions must be valid TorchScript functions (including `__init__()`)

- Classes must be new-style classes, as we use `__new__()` to construct them with pybind11

- TorchScript classes are statically typed. Members are declared by assigning to self in the `__init__()`method

  > For example, assigning outside of the `__init__()` method:
  >
  > ```
  > @torch.jit.script
  > class Foo:
  >   def assign_x(self):
  >     self.x = torch.rand(2, 3)
  > ```
  >
  > Will result in:
  >
  > ```
  > RuntimeError:
  > Tried to set nonexistent attribute: x. Did you forget to initialize it in __init__()?:
  > def assign_x(self):
  >   self.x = torch.rand(2, 3)
  >   ~~~~~~~~~~~~~~~~~~~~~~~~ <--- HERE
  > ```

- No expressions except method definitions are allowed in the body of the class

- No support for inheritance or any other polymorphism strategy, except for inheriting from object to specify a new-style class

After a class is defined, it can be used in both TorchScript and python interchangeably like any other TorchScript type:

```
@torch.jit.script
class Pair:
  def __init__(self, first, second)
    self.first = first
    self.second = second

@torch.jit.script
def sum_pair(p):
  # type : (Pair) -> Tensor
  return p.first + p.second

p = Pair(torch.rand(2, 3), torch.rand(2, 3)
print(sum_pair(p))
```

### [Expressions](https://pytorch.org/docs/stable/jit.html#id8)

The following python Expressions are supported

#### [Literals](https://pytorch.org/docs/stable/jit.html#id9)

> `True`, `False`, `None`, `'string literals'`, `"string literals"`, number literals `3` (interpreted as int) `3.4` (interpreted as a float)

##### [List Construction](https://pytorch.org/docs/stable/jit.html#id10)

> ```
> [3, 4]`, `[]`, `[torch.rand(3), torch.rand(4)]
> ```
>
> NOTE
>
> An empty list is assumed have type `List[Tensor]`. The types of other list literals are derived from the type of the members. To denote an empty list of another type, use `torch.jit.annotate`.

##### [Tuple Construction](https://pytorch.org/docs/stable/jit.html#id11)

> ```
> (3, 4)`, `(3,)
> ```

##### [Dict Construction](https://pytorch.org/docs/stable/jit.html#id12)

> ```
> {'hello': 3}`, `{}`, `{'a': torch.rand(3), 'b': torch.rand(4)}
> ```
>
> NOTE
>
> An empty dict is assumed have type `Dict[str, Tensor]`. The types of other dict literals are derived from the type of the members. To denote an empty dict of another type, use `torch.jit.annotate`.

#### [Variables](https://pytorch.org/docs/stable/jit.html#id13)

> ```
> my_variable_name
> ```
>
> NOTE
>
> See [Variable Resolution](https://pytorch.org/docs/stable/jit.html#variable-resolution) for how variables are resolved.

#### [Arithmetic Operators](https://pytorch.org/docs/stable/jit.html#id14)

> ```
> a + b
> a - b
> a * b
> a / b
> a ^ b
> a @ b
> ```

#### [Comparison Operators](https://pytorch.org/docs/stable/jit.html#id15)

> ```
> a == b
> a != b
> a < b
> a > b
> a <= b
> a >= b
> ```

#### [Logical Operators](https://pytorch.org/docs/stable/jit.html#id16)

> ```
> a and b
> a or b
> not b
> ```

#### [Subscripts](https://pytorch.org/docs/stable/jit.html#id17)

> ```
> t[0]
> t[-1]
> t[0:2]
> t[1:]
> t[:1]
> t[:]
> t[0, 1]
> t[0, 1:2]
> t[0, :1]
> t[-1, 1:, 0]
> t[1:, -1, 0]
> t[i:j, i]
> ```

#### [Function Calls](https://pytorch.org/docs/stable/jit.html#id18)

> Calls to built-in functions: `torch.rand(3, dtype=torch.int)`
>
> Calls to other script functions:
>
> ```
> import torch
>
> @torch.jit.script
> def foo(x):
>   return x + 1
>
> @torch.jit.script
> def bar(x):
>   return foo(x)
> ```

#### [Method Calls](https://pytorch.org/docs/stable/jit.html#id19)

> Calls to methods of builtin types like tensor: `x.mm(y)`
>
> When defining a Script method inside of a ScriptModule, the `@script_method` annotation is used. Inside of these methods it is possible to call other methods of this class or access methods on the submodules.
>
> Calling a submodule directly (e.g. `self.resnet(input)`) is equivalent to calling its `forward` method (e.g. `self.resnet.forward(input)`)
>
> ```
> import torch
>
> class MyScriptModule(torch.jit.ScriptModule):
>     def __init__(self):
>         super(MyScriptModule, self).__init__()
>         self.means = torch.nn.Parameter(torch.tensor([103.939, 116.779, 123.68])
>                                         .resize_(1, 3, 1, 1))
>         self.resnet = torch.jit.trace(torchvision.models.resnet18(),
>                                       torch.rand(1, 3, 224, 224))
>
>     @torch.jit.script_method
>     def helper(self, input):
>       return self.resnet(input - self.means)
>
>     @torch.jit.script_method
>     def forward(self, input):
>         return self.helper(input)
> ```

#### [Ternary Expressions](https://pytorch.org/docs/stable/jit.html#id20)

> ```
> x if x > y else y
> ```

#### [Casts](https://pytorch.org/docs/stable/jit.html#id21)

> ```
> float(ten)
> int(3.5)
> bool(ten)
> ```

#### [Accessing Module Parameters](https://pytorch.org/docs/stable/jit.html#id22)

> ```
> self.my_parameter
> self.my_submodule.my_parameter
> ```

### [Statements](https://pytorch.org/docs/stable/jit.html#id23)

TorchScript supports the following types of statements:

- Simple Assignments

  `a = b a += b # short-hand for a = a + b, does not operate in-place on a a -= b `

- Pattern Matching Assignments

  `a, b = tuple_or_list a, b, *c = a_tuple `

Print Statements

> ```
> print("the result of an add:", a + b)
> ```

If Statements

> ```
> if a < 4:
>     r = -a
> elif a < 3:
>     r = a + a
> else:
>     r = 3 * a
> ```

In addition to bools, floats, ints, and Tensors can be used in a conditional and will be implicitly casted to a boolean.

While Loops

> ```
> a = 0
> while a < 4:
>     print(a)
>     a += 1
> ```

For loops with `range`

> ```
> x = 0
> for i in range(10):
>     x *= i
> ```

For loops over tuples:

> ```
> tup = (3, torch.rand(4))
> for x in tup:
>     print(x)
> ```
>
> NOTE
>
> for loops over tuples will unroll the loop, generating a body for each member of the tuple. The body must type-check correctly for each member.

For loops over constant `torch.nn.ModuleList`

> ```
> class SubModule(torch.jit.ScriptModule):
>     def __init__(self):
>         super(Sub, self).__init__()
>         self.weight = nn.Parameter(torch.randn(2))
>
>     @torch.jit.script_method
>     def forward(self, input):
>         return self.weight + input
>
> class MyModule(torch.jit.ScriptModule):
>     __constants__ = ['mods']
>
>     def __init__(self):
>         super(MyModule, self).__init__()
>         self.mods = torch.nn.ModuleList([SubModule() for i in range(10)])
>
>     @torch.jit.script_method
>     def forward(self, v):
>         for module in self.mods:
>             v = m(v)
>         return v
> ```
>
> NOTE
>
> To use a `nn.ModuleList` inside a `@script_method` it must be marked constant by adding the name of the attribute to the `__constants__` list for the type. For loops over a `nn.ModuleList` will unroll the body of the loop at compile time, with each member of the constant module list.

- Return

  `return a, b`NOTETorchScript allows returns in the following circumstances:At the end of a functionIn an if-statement where <true> and <false> both returnIn an if-statement where <true> returns and <false> is empty (an early return)

### [Variable Resolution](https://pytorch.org/docs/stable/jit.html#id24)

TorchScript supports a subset of python’s variable resolution (i.e. scoping) rules. Local variables behave the same as in python, except for the restriction that a variable must have the same type along all paths through a function. If a variable has a different type on different sides of an if statement, it is an error to use it after the end of the if statement.

Similarly, a variable is not allowed to be used if it is only *defined* along some paths through the function.

Example:

```
@torch.jit.script
def foo(x):
    if x < 0:
        y = 4
    print(y) # Error: undefined value y
```

Non-local variables are resolved to python values at compile time when the function is defined. These values are then converted into TorchScript values using the rules described in [Use of python Values](https://pytorch.org/docs/stable/jit.html#use-of-python-values).

### [Use of python Values](https://pytorch.org/docs/stable/jit.html#id25)

To make writing TorchScript more convenient, we allow script code to refer to python values in the surrounding scope. For instance, any time there is a reference to `torch`, the TorchScript compiler is actually resolving it to the `torch` python module when the function is declared. These python values are not a first class part of TorchScript. Instead they are de-sugared at compile-time into the primitive types that TorchScript supports. This depends on the dynamic type of the python valued referenced when compilation occurs. This section describes the rules that are used when accessing python values in TorchScript.

#### [Functions](https://pytorch.org/docs/stable/jit.html#id26)

> TorchScript can call python functions. This functionality is very useful when incrementally converting a model to TorchScript. The model can be moved function-by-function to TorchScript, leaving calls to python functions in place. This way you can incrementally check the correctness of the model as you go.
>
> Example:
>
> ```
> def foo(x):
>   print("I am called with {}".format(x))
>   import pdb; pdb.set_trace()
>   return x
>
> @torch.jit.script
> def bar(x)
>   return foo(x + 1)
> ```
>
> Attempting to call `save` on a ScriptModule that contains calls to python functions will fail. The intention is that this pathway is used for debugging and the calls removed or turned into script functions before saving. If you want to export a module with a python function, add the `@torch.jit.ignore` decorator to the function which will replace these function calls with an exception when the model is saved:
>
> ```
> class M(torch.jit.ScriptModule):
>   def __init__(self):
>     super(M, self).__init__()
>
>   @torch.jit.script_method
>   def forward(self, x):
>     self.ignored_code(x)
>     return x + 2
>
>   @torch.jit.ignore
>   def ignored_code(self, x):
>     # non-TorchScript code
>     import pdb; pdb.set_trace()
>
> m = M()
> # Runs, makes upcall to python to run `ignored_code`
> m(torch.ones(2, 2))
>
> # Replaces all calls to `ignored_code` with a `raise`
> m.save("m.pt")
> loaded = torch.jit.load("m.pt")
>
> # This runs `ignored_code` after saving which will raise an Exception!
> loaded(torch.ones(2, 2))
> ```

#### [Attribute Lookup On python Modules](https://pytorch.org/docs/stable/jit.html#id27)

> TorchScript can lookup attributes on modules. Builtin functions like `torch.add` are accessed this way. This allows TorchScript to call functions defined in other modules.

#### [python-defined Constants](https://pytorch.org/docs/stable/jit.html#id28)

> TorchScript also provides a way to use constants that are defined in python. These can be used to hard-code hyper-parameters into the function, or to define universal constants. There are two ways of specifying that a python value should be treated as a constant.
>
> 1. Values looked up as attributes of a module are assumed to be constant. Example: `math.pi`
>
> 2. Attributes of a ScriptModule can be marked constant by listing them as a member of the `__constants__` property of the class:
>
>    Example:
>
>    ```
>    class Foo(torch.jit.ScriptModule):
>        __constants__ = ['a']
>
>        def __init__(self):
>            super(Foo, self).__init__(False)
>            self.a = 1 + 4
>
>       @torch.jit.script_method
>       def forward(self, input):
>           return self.a + input
>    ```
>
> Supported constant python Values are
>
> - `int`
> - `float`
> - `bool`
> - `torch.device`
> - `torch.layout`
> - `torch.dtype`
> - tuples containing supported types
> - `torch.nn.ModuleList` which can be used in a TorchScript for loop

#### [Module Attributes](https://pytorch.org/docs/stable/jit.html#id29)

The `torch.nn.Parameter` wrapper and `register_buffer` can be used to assign tensors to a `ScriptModule`. In a similar vein, attributes of any type can be assign on a `ScriptModule` by wrapping them with `torch.jit.Attribute` and specifying the type. All types available in TorchScript are supported. These attributes are mutable and are saved in a separate archive in the serialized model binary. Tensor attributes are semantically the same as buffers.

Example:

```
class Foo(torch.jit.ScriptModule):
  def __init__(self, a_dict):
    super(Foo, self).__init__(False)
    self.words = torch.jit.Attribute([], List[str])
    self.some_dict = torch.jit.Attribute(a_dict, Dict[str, int])

  @torch.jit.script_method
  def forward(self, input):
    # type: (str) -> int
    self.words.append(input)
    return self.some_dict[input]
```

### [Debugging](https://pytorch.org/docs/stable/jit.html#id30)

#### [Disable JIT for Debugging](https://pytorch.org/docs/stable/jit.html#id31)

> If you want to disable all JIT modes (tracing and scripting) so you can debug your program in raw python, you can use the `PYTORCH_JIT`environment variable. `PYTORCH_JIT` can be used to globally disable the JIT by setting its value to `0`. Given an example script:
>
> ```
> @torch.jit.script
> def scripted_fn(x : torch.Tensor):
>     for i in range(12):
>         x = x + x
>     return x
>
>
> def fn(x):
>     x = torch.neg(x)
>     import pdb; pdb.set_trace()
>     return scripted_fn(x)
>
> traced_fn = torch.jit.trace(fn, (torch.rand(4, 5),))
>
> traced_fn(torch.rand(3, 4))
> ```
>
> Debugging this script with PDB works except for when we invoke the `@torch.jit.script` function. We can globally disable JIT, so that we can call the `@torch.jit.script` function as a normal python function and not compile it. If the above script is called `disable_jit_example.py`, we can invoke it like so:
>
> ```
> $ PYTORCH_JIT=0 python disable_jit_example.py
> ```
>
> and we will be able to step into the `@torch.jit.script` function as a normal python function.

#### [Inspecting Code](https://pytorch.org/docs/stable/jit.html#id32)

> TorchScript provides a code pretty-printer for all `ScriptModule`instances. This pretty-printer gives an interpretation of the script method’s code as valid python syntax. For example:
>
> ```
> @torch.jit.script
> def foo(len):
>     # type: (int) -> torch.Tensor
>     rv = torch.zeros(3, 4)
>     for i in range(len):
>         if i < 10:
>             rv = rv - 1.0
>         else:
>             rv = rv + 1.0
>         return rv
>
> print(foo.code)
> ```
>
> A `ScriptModule` with a single `forward` method will have an attribute`code`, which you can use to inspect the `ScriptModule`’s code. If the `ScriptModule` has more than one method, you will need to access`.code` on the method itself and not the module. We can inspect the code of a method named `bar` on a ScriptModule by accessing `.bar.code`.
>
> The example script above produces the code:
>
> ```
> def forward(self,
>             len: int) -> Tensor:
>     rv = torch.zeros([3, 4], dtype=None, layout=None, device=None)
>     rv0 = rv
>     for i in range(len):
>         if torch.lt(i, 10):
>             rv1 = torch.sub(rv0, 1., 1)
>         else:
>             rv1 = torch.add(rv0, 1., 1)
>         rv0 = rv1
>     return rv0
> ```
>
> This is TorchScript’s compilation of the code for the `forward` method. You can use this to ensure TorchScript (tracing or scripting) has captured your model code correctly.

#### [Interpreting Graphs](https://pytorch.org/docs/stable/jit.html#id33)

> TorchScript also has a representation at a lower level than the code pretty- printer, in the form of IR graphs.
>
> TorchScript uses a static single assignment (SSA) intermediate representation (IR) to represent computation. The instructions in this format consist of ATen (the c++ backend of PyTorch) operators and other primitive operators, including control flow operators for loops and conditionals. As an example:
>
> ```
> @torch.jit.script
> def foo(len):
>   # type: (int) -> torch.Tensor
>   rv = torch.zeros(3, 4)
>   for i in range(len):
>     if i < 10:
>         rv = rv - 1.0
>     else:
>         rv = rv + 1.0
>   return rv
>
> print(foo.graph)
> ```
>
> `.graph` follows the same rules described in the [Inspecting Code](https://pytorch.org/docs/stable/jit.html#inspecting-code)section with regard to `forward` method lookup.
>
> The example script above produces the graph:
>
> ```
> graph(%len : int) {
>   %15 : int = prim::Constant[value=1]()
>   %9 : bool = prim::Constant[value=1]()
>   %7 : Device = prim::Constant[value="cpu"]()
>   %6 : int = prim::Constant[value=0]()
>   %5 : int = prim::Constant[value=6]()
>   %1 : int = prim::Constant[value=3]()
>   %2 : int = prim::Constant[value=4]()
>   %11 : int = prim::Constant[value=10]()
>   %14 : float = prim::Constant[value=1]()
>   %4 : int[] = prim::ListConstruct(%1, %2)
>   %rv.1 : Tensor = aten::zeros(%4, %5, %6, %7)
>   %rv : Tensor = prim::Loop(%len, %9, %rv.1)
>     block0(%i : int, %13 : Tensor) {
>       %12 : bool = aten::lt(%i, %11)
>       %rv.4 : Tensor = prim::If(%12)
>         block0() {
>           %rv.2 : Tensor = aten::sub(%13, %14, %15)
>           -> (%rv.2)
>         }
>         block1() {
>           %rv.3 : Tensor = aten::add(%13, %14, %15)
>           -> (%rv.3)
>         }
>       -> (%9, %rv.4)
>     }
>   return (%rv);
> }
> ```
>
> Take the instruction `%rv.1 : Dynamic = aten::zeros(%3, %4, %5,%6)` for example. `%rv.1 : Dynamic` means we assign the output to a (unique) value named `rv.1`, and that value is of `Dynamic` type, i.e. we do not know its concrete shape. `aten::zeros` is the operator (equivalent to `torch.zeros`) and the input list `(%3, %4, %5, %6)`specifies which values in scope should be passed as inputs. The schema for built-in functions like `aten::zeros` can be found at [Builtin Functions](https://pytorch.org/docs/stable/jit.html#builtin-functions).
>
> Notice that operators can also have associated `blocks`, namely the`prim::Loop` and `prim::If` operators. In the graph print-out, these operators are formatted to reflect their equivalent source code forms to facilitate easy debugging.
>
> Graphs can be inspected as shown to confirm that the computation described by a `ScriptModule` is correct, in both automated and manual fashion, as described below.

#### [Tracing Edge Cases](https://pytorch.org/docs/stable/jit.html#id34)

> There are some edge cases that exist where the trace of a given python function/module will not be representative of the underlying code. These cases can include:
>
> - Tracing of control flow that is dependent on inputs (e.g. tensor shapes)
> - Tracing of in-place operations of tensor views (e.g. indexing on the left-hand side of an assignment)
>
> Note that these cases may in fact be traceable in the future.

#### [Automatic Trace Checking](https://pytorch.org/docs/stable/jit.html#id35)

> One way to automatically catch many errors in traces is by using `check_inputs` on the `torch.jit.trace()` API. `check_inputs` takes a list of tuples of inputs that will be used to re-trace the computation and verify the results. For example:
>
> ```
> def loop_in_traced_fn(x):
>     result = x[0]
>     for i in range(x.size(0)):
>         result = result * x[i]
>     return result
>
> inputs = (torch.rand(3, 4, 5),)
> check_inputs = [(torch.rand(4, 5, 6),), (torch.rand(2, 3, 4),)]
>
> traced = torch.jit.trace(loop_in_traced_fn, inputs, check_inputs=check_inputs)
> ```
>
> - Gives us the following diagnostic information::
>
>   ERROR: Graphs differed across invocations! Graph diff:`  graph(%x : Tensor) {     %1 : int = prim::Constant[value=0]()     %2 : int = prim::Constant[value=0]()     %result.1 : Tensor = aten::select(%x, %1, %2)     %4 : int = prim::Constant[value=0]()     %5 : int = prim::Constant[value=0]()     %6 : Tensor = aten::select(%x, %4, %5)     %result.2 : Tensor = aten::mul(%result.1, %6)     %8 : int = prim::Constant[value=0]()     %9 : int = prim::Constant[value=1]()     %10 : Tensor = aten::select(%x, %8, %9) -   %result : Tensor = aten::mul(%result.2, %10) +   %result.3 : Tensor = aten::mul(%result.2, %10) ?          ++     %12 : int = prim::Constant[value=0]()     %13 : int = prim::Constant[value=2]()     %14 : Tensor = aten::select(%x, %12, %13) +   %result : Tensor = aten::mul(%result.3, %14) +   %16 : int = prim::Constant[value=0]() +   %17 : int = prim::Constant[value=3]() +   %18 : Tensor = aten::select(%x, %16, %17) -   %15 : Tensor = aten::mul(%result, %14) ?     ^                                 ^ +   %19 : Tensor = aten::mul(%result, %18) ?     ^                                 ^ -   return (%15); ?             ^ +   return (%19); ?             ^   } `
>
> This message indicates to us that the computation differed between when we first traced it and when we traced it with the `check_inputs`. Indeed, the loop within the body of `loop_in_traced_fn` depends on the shape of the input `x`, and thus when we try another `x` with a different shape, the trace differs.
>
> In this case, data-dependent control flow like this can be captured using script instead:
>
> ```
> def fn(x):
>     result = x[0]
>     for i in range(x.size(0)):
>         result = result * x[i]
>     return result
>
> inputs = (torch.rand(3, 4, 5),)
> check_inputs = [(torch.rand(4, 5, 6),), (torch.rand(2, 3, 4),)]
>
> scripted_fn = torch.jit.script(fn)
> print(scripted_fn.graph)
>
> for input_tuple in [inputs] + check_inputs:
>     torch.testing.assert_allclose(fn(*input_tuple), scripted_fn(*input_tuple))
> ```
>
> Which produces:
>
> ```
> graph(%x : Tensor) {
>   %5 : bool = prim::Constant[value=1]()
>   %1 : int = prim::Constant[value=0]()
>   %result.1 : Tensor = aten::select(%x, %1, %1)
>   %4 : int = aten::size(%x, %1)
>   %result : Tensor = prim::Loop(%4, %5, %result.1)
>     block0(%i : int, %7 : Tensor) {
>       %10 : Tensor = aten::select(%x, %1, %i)
>       %result.2 : Tensor = aten::mul(%7, %10)
>       -> (%5, %result.2)
>     }
>   return (%result);
> }
> ```

#### [Tracer Warnings](https://pytorch.org/docs/stable/jit.html#id36)

> The tracer produces warnings for several problematic patterns in traced computation. As an example, take a trace of a function that contains an in-place assignment on a slice (a view) of a Tensor:
>
> ```
> def fill_row_zero(x):
>     x[0] = torch.rand(*x.shape[1:2])
>     return x
>
> traced = torch.jit.trace(fill_row_zero, (torch.rand(3, 4),))
> print(traced.graph)
> ```
>
> Produces several warnings and a graph which simply returns the input:
>
> ```
> fill_row_zero.py:4: TracerWarning: There are 2 live references to the data region being modified when tracing in-place operator copy_ (possibly due to an assignment). This might cause the trace to be incorrect, because all other views that also reference this data will not reflect this change in the trace! On the other hand, if all other views use the same memory chunk, but are disjoint (e.g. are outputs of torch.split), this might still be safe.
>   x[0] = torch.rand(*x.shape[1:2])
> fill_row_zero.py:6: TracerWarning: Output nr 1. of the traced function does not match the corresponding output of the python function. Detailed error:
> Not within tolerance rtol=1e-05 atol=1e-05 at input[0, 1] (0.09115803241729736 vs. 0.6782537698745728) and 3 other locations (33.00%)
>   traced = torch.jit.trace(fill_row_zero, (torch.rand(3, 4),))
> graph(%0 : Float(3, 4)) {
>   return (%0);
> }
> ```
>
> We can fix this by modifying the code to not use the in-place update, but rather build up the result tensor out-of-place with torch.cat:
>
> ```
> def fill_row_zero(x):
>     x = torch.cat((torch.rand(1, *x.shape[1:2]), x[1:2]), dim=0)
>     return x
>
> traced = torch.jit.trace(fill_row_zero, (torch.rand(3, 4),))
> print(traced.graph)
> ```

## [Frequently Asked Questions](https://pytorch.org/docs/stable/jit.html#id37)

Q: I would like to train a model on GPU and do inference on CPU. What are the best practices?

> First convert your model from GPU to CPU and then save it, like so:
>
> ```
> cpu_model = gpu_model.cpu()
> sample_input_cpu = sample_input_gpu.cpu()
> traced_cpu = torch.jit.trace(traced_cpu, sample_input_cpu)
> torch.jit.save(traced_cpu, "cpu.pth")
>
> traced_gpu = torch.jit.trace(traced_gpu, sample_input_gpu)
> torch.jit.save(traced_gpu, "gpu.pth")
>
> # ... later, when using the model:
>
> if use_gpu:
>   model = torch.jit.load("gpu.pth")
> else:
>   model = torch.jit.load("cpu.pth")
>
> model(input)
> ```
>
> This is recommended because the tracer may witness tensor creation on a specific device, so casting an already-loaded model may have unexpected effects. Casting the model *before* saving it ensures that the tracer has the correct device information.

Q: How do I store attributes on a `ScriptModule`?

> Say we have a model like:
>
> ```
> class Model(torch.jit.ScriptModule):
>   def __init__(self):
>     super(Model, self).__init__()
>     self.x = 2
>
>   @torch.jit.script_method
>   def forward(self):
>     return self.x
> ```
>
> If `Model` is instantiated it will result in a compilation error since the compiler doesn’t know about `x`. There are 4 ways to inform the compiler of attributes on `ScriptModule`:
>
> \1. `nn.Parameter` - values wrapped in `nn.Parameter` will work as they do on `nn.Module`s
>
> \2. `register_buffer` - values wrapped in `register_buffer` will work as they do on `nn.Module`s
>
> \3. `__constants__` - adding a list called `__constants__` at the class definition level will mark the contained names as constants. Constants are saved directly in the code of the model. See [python-defined Constants](https://pytorch.org/docs/stable/jit.html#python-defined-constants).
>
> \4. `torch.jit.Attribute` - values wrapped in `torch.jit.Attribute`can be any `TorchScript` type, be mutated and are saved outside of the code of the model. See [Module Attributes](https://pytorch.org/docs/stable/jit.html#module-attributes).

### [Builtin Functions](https://pytorch.org/docs/stable/jit.html#id38)

TorchScript supports a subset of the builtin tensor and neural network functions that PyTorch provides. Most methods on Tensor as well as functions in the `torch` namespace, all functions in `torch.nn.functional` and all modules from `torch.nn` are supported in TorchScript, excluding those in the table below. For unsupported modules, we suggest using [`torch.jit.trace()`](https://pytorch.org/docs/stable/jit.html#torch.jit.trace).

Unsupported `torch.nn` Modules

```
torch.nn.modules.adaptive.AdaptiveLogSoftmaxWithLoss
torch.nn.modules.normalization.CrossMapLRN2d
torch.nn.modules.fold.Fold
torch.nn.modules.fold.Unfold
torch.nn.modules.rnn.GRU
torch.nn.modules.rnn.RNN
```
