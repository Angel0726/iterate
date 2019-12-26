---
title: 04 Extending PyTorch
toc: true
date: 2019-06-29
---
# EXTENDING PYTORCH

In this note we’ll cover ways of extending [`torch.nn`](https://pytorch.org/docs/stable/nn.html#module-torch.nn), [`torch.autograd`](https://pytorch.org/docs/stable/autograd.html#module-torch.autograd), and writing custom C extensions utilizing our C libraries.

## Extending [`torch.autograd`](https://pytorch.org/docs/stable/autograd.html#module-torch.autograd)

Adding operations to [`autograd`](https://pytorch.org/docs/stable/autograd.html#module-torch.autograd) requires implementing a new [`Function`](https://pytorch.org/docs/stable/autograd.html#torch.autograd.Function) subclass for each operation. Recall that [`Function`](https://pytorch.org/docs/stable/autograd.html#torch.autograd.Function) s are what [`autograd`](https://pytorch.org/docs/stable/autograd.html#module-torch.autograd) uses to compute the results and gradients, and encode the operation history. Every new function requires you to implement 2 methods:

- [`forward()`](https://pytorch.org/docs/stable/autograd.html#torch.autograd.Function.forward) - the code that performs the operation. It can take as many arguments as you want, with some of them being optional, if you specify the default values. All kinds of python objects are accepted here. `Tensor` arguments that track history (i.e., with `requires_grad=True`) will be converted to ones that don’t track history before the call, and their use will be registered in the graph. Note that this logic won’t traverse lists/dicts/any other data structures and will only consider `Tensor` s that are direct arguments to the call. You can return either a single `Tensor` output, or a [`tuple`](https://docs.python.org/3/library/stdtypes.html#tuple) of `Tensor` s if there are multiple outputs. Also, please refer to the docs of [`Function`](https://pytorch.org/docs/stable/autograd.html#torch.autograd.Function) to find descriptions of useful methods that can be called only from [`forward()`](https://pytorch.org/docs/stable/autograd.html#torch.autograd.Function.forward).
- [`backward()`](https://pytorch.org/docs/stable/autograd.html#torch.autograd.Function.backward) - gradient formula. It will be given as many `Tensor` arguments as there were outputs, with each of them representing gradient w.r.t. that output. It should return as many `Tensor` s as there were inputs, with each of them containing the gradient w.r.t. its corresponding input. If your inputs didn’t require gradient (`needs_input_grad` is a tuple of booleans indicating whether each input needs gradient computation), or were non-`Tensor` objects, you can return `None`. Also, if you have optional arguments to [`forward()`](https://pytorch.org/docs/stable/autograd.html#torch.autograd.Function.forward) you can return more gradients than there were inputs, as long as they’re all [`None`](https://docs.python.org/3/library/constants.html#None).

Below you can find code for a `Linear` function from [`torch.nn`](https://pytorch.org/docs/stable/nn.html#module-torch.nn), with additional comments:

```
# Inherit from Function
class LinearFunction(Function):

    # Note that both forward and backward are @staticmethods
    @staticmethod
    # bias is an optional argument
    def forward(ctx, input, weight, bias=None):
        ctx.save_for_backward(input, weight, bias)
        output = input.mm(weight.t())
        if bias is not None:
            output += bias.unsqueeze(0).expand_as(output)
        return output

    # This function has only a single output, so it gets only one gradient
    @staticmethod
    def backward(ctx, grad_output):
        # This is a pattern that is very convenient - at the top of backward
        # unpack saved_tensors and initialize all gradients w.r.t. inputs to
        # None. Thanks to the fact that additional trailing Nones are
        # ignored, the return statement is simple even when the function has
        # optional inputs.
        input, weight, bias = ctx.saved_tensors
        grad_input = grad_weight = grad_bias = None

        # These needs_input_grad checks are optional and there only to
        # improve efficiency. If you want to make your code simpler, you can
        # skip them. Returning gradients for inputs that don't require it is
        # not an error.
        if ctx.needs_input_grad[0]:
            grad_input = grad_output.mm(weight)
        if ctx.needs_input_grad[1]:
            grad_weight = grad_output.t().mm(input)
        if bias is not None and ctx.needs_input_grad[2]:
            grad_bias = grad_output.sum(0).squeeze(0)

        return grad_input, grad_weight, grad_bias
```

Now, to make it easier to use these custom ops, we recommend aliasing their `apply` method:

```
linear = LinearFunction.apply
```

Here, we give an additional example of a function that is parametrized by non-Tensor arguments:

```
class MulConstant(Function):
    @staticmethod
    def forward(ctx, tensor, constant):
        # ctx is a context object that can be used to stash information
        # for backward computation
        ctx.constant = constant
        return tensor * constant

    @staticmethod
    def backward(ctx, grad_output):
        # We return as many input gradients as there were arguments.
        # Gradients of non-Tensor arguments to forward must be None.
        return grad_output * ctx.constant, None
```

NOTE

Inputs to `backward`, i.e., `grad_output`, can also be Tensors that track history. So if `backward` is implemented with differentiable operations, (e.g., invocation of another custom `function`), higher order derivatives will work.

You probably want to check if the backward method you implemented actually computes the derivatives of your function. It is possible by comparing with numerical approximations using small finite differences:

```
from torch.autograd import gradcheck

# gradcheck takes a tuple of tensors as input, check if your gradient
# evaluated with these tensors are close enough to numerical
# approximations and returns True if they all verify this condition.
input = (torch.randn(20,20,dtype=torch.double,requires_grad=True), torch.randn(30,20,dtype=torch.double,requires_grad=True))
test = gradcheck(linear, input, eps=1e-6, atol=1e-4)
print(test)
```

See [Numerical gradient checking](https://pytorch.org/docs/stable/autograd.html#grad-check) for more details on finite-difference gradient comparisons.

## Extending [`torch.nn`](https://pytorch.org/docs/stable/nn.html#module-torch.nn)

[`nn`](https://pytorch.org/docs/stable/nn.html#module-torch.nn) exports two kinds of interfaces - modules and their functional versions. You can extend it in both ways, but we recommend using modules for all kinds of layers, that hold any parameters or buffers, and recommend using a functional form parameter-less operations like activation functions, pooling, etc.

Adding a functional version of an operation is already fully covered in the section above.

### Adding a [`Module`](https://pytorch.org/docs/stable/nn.html#torch.nn.Module)

Since [`nn`](https://pytorch.org/docs/stable/nn.html#module-torch.nn) heavily utilizes [`autograd`](https://pytorch.org/docs/stable/autograd.html#module-torch.autograd), adding a new [`Module`](https://pytorch.org/docs/stable/nn.html#torch.nn.Module) requires implementing a [`Function`](https://pytorch.org/docs/stable/autograd.html#torch.autograd.Function) that performs the operation and can compute the gradient. From now on let’s assume that we want to implement a `Linear`module and we have the function implemented as in the listing above. There’s very little code required to add this. Now, there are two functions that need to be implemented:

- `__init__` (*optional*) - takes in arguments such as kernel sizes, numbers of features, etc. and initializes parameters and buffers.
- [`forward()`](https://pytorch.org/docs/stable/nn.html#torch.nn.Module.forward) - instantiates a [`Function`](https://pytorch.org/docs/stable/autograd.html#torch.autograd.Function) and uses it to perform the operation. It’s very similar to a functional wrapper shown above.

This is how a `Linear` module can be implemented:

```
class Linear(nn.Module):
    def __init__(self, input_features, output_features, bias=True):
        super(Linear, self).__init__()
        self.input_features = input_features
        self.output_features = output_features

        # nn.Parameter is a special kind of Tensor, that will get
        # automatically registered as Module's parameter once it's assigned
        # as an attribute. Parameters and buffers need to be registered, or
        # they won't appear in .parameters() (doesn't apply to buffers), and
        # won't be converted when e.g. .cuda() is called. You can use
        # .register_buffer() to register buffers.
        # nn.Parameters require gradients by default.
        self.weight = nn.Parameter(torch.Tensor(output_features, input_features))
        if bias:
            self.bias = nn.Parameter(torch.Tensor(output_features))
        else:
            # You should always register all possible parameters, but the
            # optional ones can be None if you want.
            self.register_parameter('bias', None)

        # Not a very smart way to initialize weights
        self.weight.data.uniform_(-0.1, 0.1)
        if bias is not None:
            self.bias.data.uniform_(-0.1, 0.1)

    def forward(self, input):
        # See the autograd section for explanation of what happens here.
        return LinearFunction.apply(input, self.weight, self.bias)

    def extra_repr(self):
        # (Optional)Set the extra information about this module. You can test
        # it by printing an object of this class.
        return 'in_features={}, out_features={}, bias={}'.format(
            self.in_features, self.out_features, self.bias is not None
        )
```

## Writing custom c++ extensions

See this [PyTorch tutorial](https://pytorch.org/tutorials/advanced/cpp_extension.html) for a detailed explanation and examples.

Documentations are available at [torch.utils.cpp_extension](https://pytorch.org/docs/stable/cpp_extension.html).

## Writing custom C extensions

Example available at [this GitHub repository](https://github.com/pytorch/extension-ffi).
