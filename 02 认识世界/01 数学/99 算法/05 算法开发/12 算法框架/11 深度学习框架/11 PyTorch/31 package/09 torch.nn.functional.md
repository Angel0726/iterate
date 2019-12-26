---
title: 09 torch.nn.functional
toc: true
date: 2019-06-29
---
# TORCH.NN.FUNCTIONAL

## Convolution functions

### conv1d

- `torch.nn.functional.``conv1d`(*input*, *weight*, *bias=None*, *stride=1*, *padding=0*, *dilation=1*, *groups=1*, *padding_mode='zeros'*) → Tensor

  Applies a 1D convolution over an input signal composed of several input planes.See [`Conv1d`](https://pytorch.org/docs/stable/nn.html#torch.nn.Conv1d) for details and output shape.NOTEIn some circumstances when using the CUDA backend with CuDNN, this operator may select a nondeterministic algorithm to increase performance. If this is undesirable, you can try to make the operation deterministic (potentially at a performance cost) by setting `torch.backends.cudnn.deterministic = True`. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.Parameters**input** – input tensor of shape (\text{minibatch} , \text{in\_channels} , iW)(minibatch,in_channels,iW)**weight** – filters of shape (\text{out\_channels} , \frac{\text{in\_channels}}{\text{groups}} , kW)(out_channels,groupsin_channels,kW)**bias** – optional bias of shape (\text{out\_channels})(out_channels). Default: `None`**stride** – the stride of the convolving kernel. Can be a single number or a one-element tuple (sW,). Default: 1**padding** – implicit paddings on both sides of the input. Can be a single number or a one-element tuple (padW,). Default: 0**dilation** – the spacing between kernel elements. Can be a single number or a one-element tuple (dW,). Default: 1**groups** – split input into groups, \text{in\_channels}in_channels should be divisible by the number of groups. Default: 1**padding_mode** – the type of paddings applied to both sided can be: zeros or circular. Default: zerosExamples:`>>> filters = torch.randn(33, 16, 3) >>> inputs = torch.randn(20, 16, 50) >>> F.conv1d(inputs, filters) `

### conv2d

- `torch.nn.functional.``conv2d`(*input*, *weight*, *bias=None*, *stride=1*, *padding=0*, *dilation=1*, *groups=1*, *padding_mode='zeros'*) → Tensor

  Applies a 2D convolution over an input image composed of several input planes.See [`Conv2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.Conv2d) for details and output shape.NOTEIn some circumstances when using the CUDA backend with CuDNN, this operator may select a nondeterministic algorithm to increase performance. If this is undesirable, you can try to make the operation deterministic (potentially at a performance cost) by setting `torch.backends.cudnn.deterministic = True`. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.Parameters**input** – input tensor of shape (\text{minibatch} , \text{in\_channels} , iH , iW)(minibatch,in_channels,iH,iW)**weight** – filters of shape (\text{out\_channels} , \frac{\text{in\_channels}}{\text{groups}} , kH , kW)(out_channels,groupsin_channels,kH,kW)**bias** – optional bias tensor of shape (\text{out\_channels})(out_channels). Default: `None`**stride** – the stride of the convolving kernel. Can be a single number or a tuple (sH, sW). Default: 1**padding** – implicit paddings on both sides of the input. Can be a single number or a tuple (padH, padW). Default: 0**dilation** – the spacing between kernel elements. Can be a single number or a tuple (dH, dW). Default: 1**groups** – split input into groups, \text{in\_channels}in_channels should be divisible by the number of groups. Default: 1**padding_mode** – the type of paddings applied to both sided can be: zeros or circular. Default: zerosExamples:`>>> # With square kernels and equal stride >>> filters = torch.randn(8,4,3,3) >>> inputs = torch.randn(1,4,5,5) >>> F.conv2d(inputs, filters, padding=1) `

### conv3d

- `torch.nn.functional.``conv3d`(*input*, *weight*, *bias=None*, *stride=1*, *padding=0*, *dilation=1*, *groups=1*, *padding_mode='zeros'*) → Tensor

  Applies a 3D convolution over an input image composed of several input planes.See [`Conv3d`](https://pytorch.org/docs/stable/nn.html#torch.nn.Conv3d) for details and output shape.NOTEIn some circumstances when using the CUDA backend with CuDNN, this operator may select a nondeterministic algorithm to increase performance. If this is undesirable, you can try to make the operation deterministic (potentially at a performance cost) by setting `torch.backends.cudnn.deterministic = True`. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.Parameters**input** – input tensor of shape (\text{minibatch} , \text{in\_channels} , iT , iH , iW)(minibatch,in_channels,iT,iH,iW)**weight** – filters of shape (\text{out\_channels} , \frac{\text{in\_channels}}{\text{groups}} , kT , kH , kW)(out_channels,groupsin_channels,kT,kH,kW)**bias** – optional bias tensor of shape (\text{out\_channels})(out_channels). Default: None**stride** – the stride of the convolving kernel. Can be a single number or a tuple (sT, sH, sW). Default: 1**padding** – implicit paddings on both sides of the input. Can be a single number or a tuple (padT, padH, padW). Default: 0**dilation** – the spacing between kernel elements. Can be a single number or a tuple (dT, dH, dW). Default: 1**groups** – split input into groups, \text{in\_channels}in_channels should be divisible by the number of groups. Default: 1**padding_mode** – the type of paddings applied to both sided can be: zeros or circular. Default: zerosExamples:`>>> filters = torch.randn(33, 16, 3, 3, 3) >>> inputs = torch.randn(20, 16, 50, 10, 20) >>> F.conv3d(inputs, filters) `

### conv_transpose1d

- `torch.nn.functional.``conv_transpose1d`(*input*, *weight*, *bias=None*, *stride=1*, *padding=0*, *output_padding=0*, *groups=1*, *dilation=1*) → Tensor

  Applies a 1D transposed convolution operator over an input signal composed of several input planes, sometimes also called “deconvolution”.See [`ConvTranspose1d`](https://pytorch.org/docs/stable/nn.html#torch.nn.ConvTranspose1d) for details and output shape.NOTEIn some circumstances when using the CUDA backend with CuDNN, this operator may select a nondeterministic algorithm to increase performance. If this is undesirable, you can try to make the operation deterministic (potentially at a performance cost) by setting `torch.backends.cudnn.deterministic = True`. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.Parameters**input** – input tensor of shape (\text{minibatch} , \text{in\_channels} , iW)(minibatch,in_channels,iW)**weight** – filters of shape (\text{in\_channels} , \frac{\text{out\_channels}}{\text{groups}} , kW)(in_channels,groupsout_channels,kW)**bias** – optional bias of shape (\text{out\_channels})(out_channels). Default: None**stride** – the stride of the convolving kernel. Can be a single number or a tuple `(sW,)`. Default: 1**padding** – `dilation * (kernel_size - 1) - padding` zero-padding will be added to both sides of each dimension in the input. Can be a single number or a tuple `(padW,)`. Default: 0**output_padding** – additional size added to one side of each dimension in the output shape. Can be a single number or a tuple `(out_padW)`. Default: 0**groups** – split input into groups, \text{in\_channels}in_channels should be divisible by the number of groups. Default: 1**dilation** – the spacing between kernel elements. Can be a single number or a tuple `(dW,)`. Default: 1Examples:`>>> inputs = torch.randn(20, 16, 50) >>> weights = torch.randn(16, 33, 5) >>> F.conv_transpose1d(inputs, weights) `

### conv_transpose2d

- `torch.nn.functional.``conv_transpose2d`(*input*, *weight*, *bias=None*, *stride=1*, *padding=0*, *output_padding=0*, *groups=1*, *dilation=1*) → Tensor

  Applies a 2D transposed convolution operator over an input image composed of several input planes, sometimes also called “deconvolution”.See [`ConvTranspose2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.ConvTranspose2d) for details and output shape.NOTEIn some circumstances when using the CUDA backend with CuDNN, this operator may select a nondeterministic algorithm to increase performance. If this is undesirable, you can try to make the operation deterministic (potentially at a performance cost) by setting `torch.backends.cudnn.deterministic = True`. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.Parameters**input** – input tensor of shape (\text{minibatch} , \text{in\_channels} , iH , iW)(minibatch,in_channels,iH,iW)**weight** – filters of shape (\text{in\_channels} , \frac{\text{out\_channels}}{\text{groups}} , kH , kW)(in_channels,groupsout_channels,kH,kW)**bias** – optional bias of shape (\text{out\_channels})(out_channels). Default: None**stride** – the stride of the convolving kernel. Can be a single number or a tuple `(sH,sW)`. Default: 1**padding** – `dilation * (kernel_size - 1) - padding` zero-padding will be added to both sides of each dimension in the input. Can be a single number or a tuple `(padH,padW)`. Default: 0**output_padding** – additional size added to one side of each dimension in the output shape. Can be a single number or a tuple `(out_padH, out_padW)`. Default: 0**groups** – split input into groups, \text{in\_channels}in_channels should be divisible by the number of groups. Default: 1**dilation** – the spacing between kernel elements. Can be a single number or a tuple `(dH, dW)`. Default: 1Examples:`>>> # With square kernels and equal stride >>> inputs = torch.randn(1, 4, 5, 5) >>> weights = torch.randn(4, 8, 3, 3) >>> F.conv_transpose2d(inputs, weights, padding=1) `

### conv_transpose3d

- `torch.nn.functional.``conv_transpose3d`(*input*, *weight*, *bias=None*, *stride=1*, *padding=0*, *output_padding=0*, *groups=1*, *dilation=1*) → Tensor

  Applies a 3D transposed convolution operator over an input image composed of several input planes, sometimes also called “deconvolution”See [`ConvTranspose3d`](https://pytorch.org/docs/stable/nn.html#torch.nn.ConvTranspose3d) for details and output shape.NOTEIn some circumstances when using the CUDA backend with CuDNN, this operator may select a nondeterministic algorithm to increase performance. If this is undesirable, you can try to make the operation deterministic (potentially at a performance cost) by setting `torch.backends.cudnn.deterministic = True`. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.Parameters**input** – input tensor of shape (\text{minibatch} , \text{in\_channels} , iT , iH , iW)(minibatch,in_channels,iT,iH,iW)**weight** – filters of shape (\text{in\_channels} , \frac{\text{out\_channels}}{\text{groups}} , kT , kH , kW)(in_channels,groupsout_channels,kT,kH,kW)**bias** – optional bias of shape (\text{out\_channels})(out_channels). Default: None**stride** – the stride of the convolving kernel. Can be a single number or a tuple `(sT,sH, sW)`. Default: 1**padding** – `dilation * (kernel_size - 1) - padding` zero-padding will be added to both sides of each dimension in the input. Can be a single number or a tuple `(padT,padH, padW)`. Default: 0**output_padding** – additional size added to one side of each dimension in the output shape. Can be a single number or a tuple `(out_padT, out_padH, out_padW)`. Default: 0**groups** – split input into groups, \text{in\_channels}in_channels should be divisible by the number of groups. Default: 1**dilation** – the spacing between kernel elements. Can be a single number or a tuple (dT, dH, dW). Default: 1Examples:`>>> inputs = torch.randn(20, 16, 50, 10, 20) >>> weights = torch.randn(16, 33, 3, 3, 3) >>> F.conv_transpose3d(inputs, weights) `

### unfold

- `torch.nn.functional.``unfold`(*input*, *kernel_size*, *dilation=1*, *padding=0*, *stride=1*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#unfold)

  Extracts sliding local blocks from an batched input tensor.WARNINGCurrently, only 4-D input tensors (batched image-like tensors) are supported.WARNINGMore than one element of the unfolded tensor may refer to a single memory location. As a result, in-place operations (especially ones that are vectorized) may result in incorrect behavior. If you need to write to the tensor, please clone it first.See [`torch.nn.Unfold`](https://pytorch.org/docs/stable/nn.html#torch.nn.Unfold) for details

### fold

- `torch.nn.functional.``fold`(*input*, *output_size*, *kernel_size*, *dilation=1*, *padding=0*, *stride=1*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#fold)

  Combines an array of sliding local blocks into a large containing tensor.WARNINGCurrently, only 4-D output tensors (batched image-like tensors) are supported.See [`torch.nn.Fold`](https://pytorch.org/docs/stable/nn.html#torch.nn.Fold) for details

## Pooling functions

### avg_pool1d

- `torch.nn.functional.``avg_pool1d`(*input*, *kernel_size*, *stride=None*, *padding=0*, *ceil_mode=False*, *count_include_pad=True*) → Tensor

  Applies a 1D average pooling over an input signal composed of several input planes.See [`AvgPool1d`](https://pytorch.org/docs/stable/nn.html#torch.nn.AvgPool1d) for details and output shape.Parameters**input** – input tensor of shape (\text{minibatch} , \text{in\_channels} , iW)(minibatch,in_channels,iW)**kernel_size** – the size of the window. Can be a single number or a tuple (kW,)**stride** – the stride of the window. Can be a single number or a tuple (sW,). Default: `kernel_size`**padding** – implicit zero paddings on both sides of the input. Can be a single number or a tuple (padW,). Default: 0**ceil_mode** – when True, will use ceil instead of floor to compute the output shape. Default: `False`**count_include_pad** – when True, will include the zero-padding in the averaging calculation. Default: `True`Examples:`>>> # pool of square window of size=3, stride=2 >>> input = torch.tensor([[[1, 2, 3, 4, 5, 6, 7]]], dtype=torch.float32) >>> F.avg_pool1d(input, kernel_size=3, stride=2) tensor([[[ 2.,  4.,  6.]]]) `

### avg_pool2d

- `torch.nn.functional.``avg_pool2d`(*input*, *kernel_size*, *stride=None*, *padding=0*, *ceil_mode=False*, *count_include_pad=True*) → Tensor

  Applies 2D average-pooling operation in kH \times kWkH×kW regions by step size sH \times sWsH×sW steps. The number of output features is equal to the number of input planes.See [`AvgPool2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.AvgPool2d) for details and output shape.Parameters**input** – input tensor (\text{minibatch} , \text{in\_channels} , iH , iW)(minibatch,in_channels,iH,iW)**kernel_size** – size of the pooling region. Can be a single number or a tuple (kH, kW)**stride** – stride of the pooling operation. Can be a single number or a tuple (sH, sW). Default: `kernel_size`**padding** – implicit zero paddings on both sides of the input. Can be a single number or a tuple (padH, padW). Default: 0**ceil_mode** – when True, will use ceil instead of floor in the formula to compute the output shape. Default: `False`**count_include_pad** – when True, will include the zero-padding in the averaging calculation. Default: `True`

### avg_pool3d

- `torch.nn.functional.``avg_pool3d`(*input*, *kernel_size*, *stride=None*, *padding=0*, *ceil_mode=False*, *count_include_pad=True*) → Tensor

  Applies 3D average-pooling operation in kT \times kH \times kWkT×kH×kW regions by step size sT \times sH \times sWsT×sH×sW steps. The number of output features is equal to \lfloor\frac{\text{input planes}}{sT}\rfloor⌊sTinput planes⌋.See [`AvgPool3d`](https://pytorch.org/docs/stable/nn.html#torch.nn.AvgPool3d) for details and output shape.Parameters**input** – input tensor (\text{minibatch} , \text{in\_channels} , iT \times iH , iW)(minibatch,in_channels,iT×iH,iW)**kernel_size** – size of the pooling region. Can be a single number or a tuple (kT, kH, kW)**stride** – stride of the pooling operation. Can be a single number or a tuple (sT, sH, sW). Default: `kernel_size`**padding** – implicit zero paddings on both sides of the input. Can be a single number or a tuple (padT, padH, padW), Default: 0**ceil_mode** – when True, will use ceil instead of floor in the formula to compute the output shape**count_include_pad** – when True, will include the zero-padding in the averaging calculation

### max_pool1d

- `torch.nn.functional.``max_pool1d`(**args*, **\*kwargs*)

  Applies a 1D max pooling over an input signal composed of several input planes.See [`MaxPool1d`](https://pytorch.org/docs/stable/nn.html#torch.nn.MaxPool1d) for details.

### max_pool2d

- `torch.nn.functional.``max_pool2d`(**args*, **\*kwargs*)

  Applies a 2D max pooling over an input signal composed of several input planes.See [`MaxPool2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.MaxPool2d) for details.

### max_pool3d

- `torch.nn.functional.``max_pool3d`(**args*, **\*kwargs*)

  Applies a 3D max pooling over an input signal composed of several input planes.See [`MaxPool3d`](https://pytorch.org/docs/stable/nn.html#torch.nn.MaxPool3d) for details.

### max_unpool1d

- `torch.nn.functional.``max_unpool1d`(*input*, *indices*, *kernel_size*, *stride=None*, *padding=0*, *output_size=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#max_unpool1d)

  Computes a partial inverse of `MaxPool1d`.See [`MaxUnpool1d`](https://pytorch.org/docs/stable/nn.html#torch.nn.MaxUnpool1d) for details.

### max_unpool2d

- `torch.nn.functional.``max_unpool2d`(*input*, *indices*, *kernel_size*, *stride=None*, *padding=0*, *output_size=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#max_unpool2d)

  Computes a partial inverse of `MaxPool2d`.See [`MaxUnpool2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.MaxUnpool2d) for details.

### max_unpool3d

- `torch.nn.functional.``max_unpool3d`(*input*, *indices*, *kernel_size*, *stride=None*, *padding=0*, *output_size=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#max_unpool3d)

  Computes a partial inverse of `MaxPool3d`.See [`MaxUnpool3d`](https://pytorch.org/docs/stable/nn.html#torch.nn.MaxUnpool3d) for details.

### lp_pool1d

- `torch.nn.functional.``lp_pool1d`(*input*, *norm_type*, *kernel_size*, *stride=None*, *ceil_mode=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#lp_pool1d)

  Applies a 1D power-average pooling over an input signal composed of several input planes. If the sum of all inputs to the power of p is zero, the gradient is set to zero as well.See [`LPPool1d`](https://pytorch.org/docs/stable/nn.html#torch.nn.LPPool1d) for details.

### lp_pool2d

- `torch.nn.functional.``lp_pool2d`(*input*, *norm_type*, *kernel_size*, *stride=None*, *ceil_mode=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#lp_pool2d)

  Applies a 2D power-average pooling over an input signal composed of several input planes. If the sum of all inputs to the power of p is zero, the gradient is set to zero as well.See [`LPPool2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.LPPool2d) for details.

### adaptive_max_pool1d

- `torch.nn.functional.``adaptive_max_pool1d`(**args*, **\*kwargs*)

  Applies a 1D adaptive max pooling over an input signal composed of several input planes.See [`AdaptiveMaxPool1d`](https://pytorch.org/docs/stable/nn.html#torch.nn.AdaptiveMaxPool1d) for details and output shape.Parameters**output_size** – the target output size (single integer)**return_indices** – whether to return pooling indices. Default: `False`

### adaptive_max_pool2d

- `torch.nn.functional.``adaptive_max_pool2d`(**args*, **\*kwargs*)

  Applies a 2D adaptive max pooling over an input signal composed of several input planes.See [`AdaptiveMaxPool2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.AdaptiveMaxPool2d) for details and output shape.Parameters**output_size** – the target output size (single integer or double-integer tuple)**return_indices** – whether to return pooling indices. Default: `False`

### adaptive_max_pool3d

- `torch.nn.functional.``adaptive_max_pool3d`(**args*, **\*kwargs*)

  Applies a 3D adaptive max pooling over an input signal composed of several input planes.See [`AdaptiveMaxPool3d`](https://pytorch.org/docs/stable/nn.html#torch.nn.AdaptiveMaxPool3d) for details and output shape.Parameters**output_size** – the target output size (single integer or triple-integer tuple)**return_indices** – whether to return pooling indices. Default: `False`

### adaptive_avg_pool1d

- `torch.nn.functional.``adaptive_avg_pool1d`(*input*, *output_size*) → Tensor

  Applies a 1D adaptive average pooling over an input signal composed of several input planes.See [`AdaptiveAvgPool1d`](https://pytorch.org/docs/stable/nn.html#torch.nn.AdaptiveAvgPool1d) for details and output shape.Parameters**output_size** – the target output size (single integer)

### adaptive_avg_pool2d

- `torch.nn.functional.``adaptive_avg_pool2d`(*input*, *output_size*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#adaptive_avg_pool2d)

  Applies a 2D adaptive average pooling over an input signal composed of several input planes.See [`AdaptiveAvgPool2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.AdaptiveAvgPool2d) for details and output shape.Parameters**output_size** – the target output size (single integer or double-integer tuple)

### adaptive_avg_pool3d

- `torch.nn.functional.``adaptive_avg_pool3d`(*input*, *output_size*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#adaptive_avg_pool3d)

  Applies a 3D adaptive average pooling over an input signal composed of several input planes.See [`AdaptiveAvgPool3d`](https://pytorch.org/docs/stable/nn.html#torch.nn.AdaptiveAvgPool3d) for details and output shape.Parameters**output_size** – the target output size (single integer or triple-integer tuple)

## Non-linear activation functions

### threshold

- `torch.nn.functional.``threshold`(*input*, *threshold*, *value*, *inplace=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#threshold)

  Thresholds each element of the input Tensor.See [`Threshold`](https://pytorch.org/docs/stable/nn.html#torch.nn.Threshold) for more details.

- `torch.nn.functional.``threshold_`(*input*, *threshold*, *value*) → Tensor

  In-place version of [`threshold()`](https://pytorch.org/docs/stable/nn.html#torch.nn.functional.threshold).

### relu

- `torch.nn.functional.``relu`(*input*, *inplace=False*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#relu)

  Applies the rectified linear unit function element-wise. See [`ReLU`](https://pytorch.org/docs/stable/nn.html#torch.nn.ReLU) for more details.

- `torch.nn.functional.``relu_`(*input*) → Tensor

  In-place version of [`relu()`](https://pytorch.org/docs/stable/nn.html#torch.nn.functional.relu).

### hardtanh

- `torch.nn.functional.``hardtanh`(*input*, *min_val=-1.*, *max_val=1.*, *inplace=False*)→ Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#hardtanh)

  Applies the HardTanh function element-wise. See [`Hardtanh`](https://pytorch.org/docs/stable/nn.html#torch.nn.Hardtanh) for more details.

- `torch.nn.functional.``hardtanh_`(*input*, *min_val=-1.*, *max_val=1.*) → Tensor

  In-place version of [`hardtanh()`](https://pytorch.org/docs/stable/nn.html#torch.nn.functional.hardtanh).

### relu6

- `torch.nn.functional.``relu6`(*input*, *inplace=False*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#relu6)

  Applies the element-wise function \text{ReLU6}(x) = \min(\max(0,x), 6)ReLU6(x)=min(max(0,x),6).See [`ReLU6`](https://pytorch.org/docs/stable/nn.html#torch.nn.ReLU6) for more details.

### elu

- `torch.nn.functional.``elu`(*input*, *alpha=1.0*, *inplace=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#elu)

  Applies element-wise, \text{ELU}(x) = \max(0,x) + \min(0, \alpha * (\exp(x) - 1))ELU(x)=max(0,x)+min(0,α∗(exp(x)−1)).See [`ELU`](https://pytorch.org/docs/stable/nn.html#torch.nn.ELU) for more details.

- `torch.nn.functional.``elu_`(*input*, *alpha=1.*) → Tensor

  In-place version of [`elu()`](https://pytorch.org/docs/stable/nn.html#torch.nn.functional.elu).

### selu

- `torch.nn.functional.``selu`(*input*, *inplace=False*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#selu)

  Applies element-wise, \text{SELU}(x) = scale * (\max(0,x) + \min(0, \alpha * (\exp(x) - 1)))SELU(x)=scale∗(max(0,x)+min(0,α∗(exp(x)−1))), with \alpha=1.6732632423543772848170429916717α=1.6732632423543772848170429916717 and scale=1.0507009873554804934193349852946scale=1.0507009873554804934193349852946.See [`SELU`](https://pytorch.org/docs/stable/nn.html#torch.nn.SELU) for more details.

### celu

- `torch.nn.functional.``celu`(*input*, *alpha=1.*, *inplace=False*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#celu)

  Applies element-wise, \text{CELU}(x) = \max(0,x) + \min(0, \alpha * (\exp(x/\alpha) - 1))CELU(x)=max(0,x)+min(0,α∗(exp(x/α)−1)).See [`CELU`](https://pytorch.org/docs/stable/nn.html#torch.nn.CELU) for more details.

### leaky_relu

- `torch.nn.functional.``leaky_relu`(*input*, *negative_slope=0.01*, *inplace=False*)→ Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#leaky_relu)

  Applies element-wise, \text{LeakyReLU}(x) = \max(0, x) + \text{negative\_slope} * \min(0, x)LeakyReLU(x)=max(0,x)+negative_slope∗min(0,x)See [`LeakyReLU`](https://pytorch.org/docs/stable/nn.html#torch.nn.LeakyReLU) for more details.

- `torch.nn.functional.``leaky_relu_`(*input*, *negative_slope=0.01*) → Tensor

  In-place version of [`leaky_relu()`](https://pytorch.org/docs/stable/nn.html#torch.nn.functional.leaky_relu).

### prelu

- `torch.nn.functional.``prelu`(*input*, *weight*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#prelu)

  Applies element-wise the function \text{PReLU}(x) = \max(0,x) + \text{weight} * \min(0,x)PReLU(x)=max(0,x)+weight∗min(0,x) where weight is a learnable parameter.See [`PReLU`](https://pytorch.org/docs/stable/nn.html#torch.nn.PReLU) for more details.

### rrelu

- `torch.nn.functional.``rrelu`(*input*, *lower=1./8*, *upper=1./3*, *training=False*, *inplace=False*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#rrelu)

  Randomized leaky ReLU.See [`RReLU`](https://pytorch.org/docs/stable/nn.html#torch.nn.RReLU) for more details.

- `torch.nn.functional.``rrelu_`(*input*, *lower=1./8*, *upper=1./3*, *training=False*)→ Tensor

  In-place version of [`rrelu()`](https://pytorch.org/docs/stable/nn.html#torch.nn.functional.rrelu).

### glu

- `torch.nn.functional.``glu`(*input*, *dim=-1*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#glu)

  The gated linear unit. Computes:\text{GLU}(a, b) = a \otimes \sigma(b)GLU(a,b)=a⊗σ(b)where input is split in half along dim to form a and b, \sigmaσ is the sigmoid function and \otimes⊗ is the element-wise product between matrices.See [Language Modeling with Gated Convolutional Networks](https://arxiv.org/abs/1612.08083).Parameters**input** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – input tensor**dim** ([*int*](https://docs.python.org/3/library/functions.html#int)) – dimension on which to split the input. Default: -1

### logsigmoid

- `torch.nn.functional.``logsigmoid`(*input*) → Tensor

  Applies element-wise \text{LogSigmoid}(x_i) = \log \left(\frac{1}{1 + \exp(-x_i)}\right)LogSigmoid(xi)=log(1+exp(−xi)1)See [`LogSigmoid`](https://pytorch.org/docs/stable/nn.html#torch.nn.LogSigmoid) for more details.

### hardshrink

- `torch.nn.functional.``hardshrink`(*input*, *lambd=0.5*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#hardshrink)

  Applies the hard shrinkage function element-wiseSee [`Hardshrink`](https://pytorch.org/docs/stable/nn.html#torch.nn.Hardshrink) for more details.

### tanhshrink

- `torch.nn.functional.``tanhshrink`(*input*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#tanhshrink)

  Applies element-wise, \text{Tanhshrink}(x) = x - \text{Tanh}(x)Tanhshrink(x)=x−Tanh(x)See [`Tanhshrink`](https://pytorch.org/docs/stable/nn.html#torch.nn.Tanhshrink) for more details.

### softsign

- `torch.nn.functional.``softsign`(*input*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#softsign)

  Applies element-wise, the function \text{SoftSign}(x) = \frac{x}{1 + |x|}SoftSign(x)=1+∣x∣xSee [`Softsign`](https://pytorch.org/docs/stable/nn.html#torch.nn.Softsign) for more details.

### softplus

- `torch.nn.functional.``softplus`(*input*, *beta=1*, *threshold=20*) → Tensor


### softmin

- `torch.nn.functional.``softmin`(*input*, *dim=None*, *_stacklevel=3*, *dtype=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#softmin)

  Applies a softmin function.Note that \text{Softmin}(x) = \text{Softmax}(-x)Softmin(x)=Softmax(−x). See softmax definition for mathematical formula.See [`Softmin`](https://pytorch.org/docs/stable/nn.html#torch.nn.Softmin) for more details.Parameters**input** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – input**dim** ([*int*](https://docs.python.org/3/library/functions.html#int)) – A dimension along which softmin will be computed (so every slice along dim will sum to 1).**dtype** (`torch.dtype`, optional) – the desired data type of returned tensor. If specified, the input tensor is casted to `dtype` before the operation is performed. This is useful for preventing data type overflows. Default: None.

### softmax

- `torch.nn.functional.``softmax`(*input*, *dim=None*, *_stacklevel=3*, *dtype=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#softmax)

  Applies a softmax function.Softmax is defined as:\text{Softmax}(x_{i}) = \frac{exp(x_i)}{\sum_j exp(x_j)}Softmax(xi)=∑jexp(xj)exp(xi)It is applied to all slices along dim, and will re-scale them so that the elements lie in the range [0, 1]and sum to 1.See [`Softmax`](https://pytorch.org/docs/stable/nn.html#torch.nn.Softmax) for more details.Parameters**input** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – input**dim** ([*int*](https://docs.python.org/3/library/functions.html#int)) – A dimension along which softmax will be computed.**dtype** (`torch.dtype`, optional) – the desired data type of returned tensor. If specified, the input tensor is casted to `dtype` before the operation is performed. This is useful for preventing data type overflows. Default: None.NOTEThis function doesn’t work directly with NLLLoss, which expects the Log to be computed between the Softmax and itself. Use log_softmax instead (it’s faster and has better numerical properties).

### softshrink

- `torch.nn.functional.``softshrink`(*input*, *lambd=0.5*) → Tensor

  Applies the soft shrinkage function elementwiseSee [`Softshrink`](https://pytorch.org/docs/stable/nn.html#torch.nn.Softshrink) for more details.

### gumbel_softmax

- `torch.nn.functional.``gumbel_softmax`(*logits*, *tau=1*, *hard=False*, *eps=1e-10*, *dim=-1*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#gumbel_softmax)

  Samples from the [Gumbel-Softmax distribution](https://arxiv.org/abs/1611.00712https://arxiv.org/abs/1611.01144) and optionally discretizes.Parameters**logits** – […, num_features] unnormalized log probabilities**tau** – non-negative scalar temperature**hard** – if `True`, the returned samples will be discretized as one-hot vectors, but will be differentiated as if it is the soft sample in autograd**dim** ([*int*](https://docs.python.org/3/library/functions.html#int)) – A dimension along which softmax will be computed. Default: -1.ReturnsSampled tensor of same shape as logits from the Gumbel-Softmax distribution. If `hard=True`, the returned samples will be one-hot, otherwise they will be probability distributions that sum to 1 across dim.NOTEThis function is here for legacy reasons, may be removed from nn.Functional in the future.NOTEThe main trick for hard is to do y_hard - y_soft.detach() + y_softIt achieves two things: - makes the output value exactly one-hot (since we add then subtract y_soft value) - makes the gradient equal to y_soft gradient (since we strip all other gradients)Examples::`>>> logits = torch.randn(20, 32) >>> # Sample soft categorical using reparametrization trick: >>> F.gumbel_softmax(logits, tau=1, hard=False) >>> # Sample hard categorical using "Straight-through" trick: >>> F.gumbel_softmax(logits, tau=1, hard=True) `

### log_softmax

- `torch.nn.functional.``log_softmax`(*input*, *dim=None*, *_stacklevel=3*, *dtype=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#log_softmax)

  Applies a softmax followed by a logarithm.While mathematically equivalent to log(softmax(x)), doing these two operations separately is slower, and numerically unstable. This function uses an alternative formulation to compute the output and gradient correctly.See [`LogSoftmax`](https://pytorch.org/docs/stable/nn.html#torch.nn.LogSoftmax) for more details.Parameters**input** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – input**dim** ([*int*](https://docs.python.org/3/library/functions.html#int)) – A dimension along which log_softmax will be computed.**dtype** (`torch.dtype`, optional) – the desired data type of returned tensor. If specified, the input tensor is casted to `dtype` before the operation is performed. This is useful for preventing data type overflows. Default: None.

### tanh

- `torch.nn.functional.``tanh`(*input*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#tanh)

  Applies element-wise, \text{Tanh}(x) = \tanh(x) = \frac{\exp(x) - \exp(-x)}{\exp(x) + \exp(-x)}Tanh(x)=tanh(x)=exp(x)+exp(−x)exp(x)−exp(−x)See [`Tanh`](https://pytorch.org/docs/stable/nn.html#torch.nn.Tanh) for more details.

### sigmoid

- `torch.nn.functional.``sigmoid`(*input*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#sigmoid)

  Applies the element-wise function \text{Sigmoid}(x) = \frac{1}{1 + \exp(-x)}Sigmoid(x)=1+exp(−x)1See [`Sigmoid`](https://pytorch.org/docs/stable/nn.html#torch.nn.Sigmoid) for more details.

## Normalization functions

### batch_norm

- `torch.nn.functional.``batch_norm`(*input*, *running_mean*, *running_var*, *weight=None*, *bias=None*, *training=False*, *momentum=0.1*, *eps=1e-05*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#batch_norm)

  Applies Batch Normalization for each channel across a batch of data.See [`BatchNorm1d`](https://pytorch.org/docs/stable/nn.html#torch.nn.BatchNorm1d), [`BatchNorm2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.BatchNorm2d), [`BatchNorm3d`](https://pytorch.org/docs/stable/nn.html#torch.nn.BatchNorm3d) for details.

### instance_norm

- `torch.nn.functional.``instance_norm`(*input*, *running_mean=None*, *running_var=None*, *weight=None*, *bias=None*, *use_input_stats=True*, *momentum=0.1*, *eps=1e-05*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#instance_norm)

  Applies Instance Normalization for each channel in each data sample in a batch.See [`InstanceNorm1d`](https://pytorch.org/docs/stable/nn.html#torch.nn.InstanceNorm1d), [`InstanceNorm2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.InstanceNorm2d), [`InstanceNorm3d`](https://pytorch.org/docs/stable/nn.html#torch.nn.InstanceNorm3d) for details.

### layer_norm

- `torch.nn.functional.``layer_norm`(*input*, *normalized_shape*, *weight=None*, *bias=None*, *eps=1e-05*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#layer_norm)

  Applies Layer Normalization for last certain number of dimensions.See [`LayerNorm`](https://pytorch.org/docs/stable/nn.html#torch.nn.LayerNorm) for details.

### local_response_norm

- `torch.nn.functional.``local_response_norm`(*input*, *size*, *alpha=0.0001*, *beta=0.75*, *k=1.0*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#local_response_norm)

  Applies local response normalization over an input signal composed of several input planes, where channels occupy the second dimension. Applies normalization across channels.See [`LocalResponseNorm`](https://pytorch.org/docs/stable/nn.html#torch.nn.LocalResponseNorm) for details.

### normalize

- `torch.nn.functional.``normalize`(*input*, *p=2*, *dim=1*, *eps=1e-12*, *out=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#normalize)

  Performs L_pLp normalization of inputs over specified dimension.For a tensor `input` of sizes (n_0, ..., n_{dim}, ..., n_k)(n0,...,ndim,...,nk), each n_{dim}ndim -element vector vv along dimension `dim` is transformed asv = \frac{v}{\max(\lVert v \rVert_p, \epsilon)}.v=max(∥v∥p,ϵ)v.With the default arguments it uses the Euclidean norm over vectors along dimension 11 for normalization.Parameters**input** – input tensor of any shape**p** ([*float*](https://docs.python.org/3/library/functions.html#float)) – the exponent value in the norm formulation. Default: 2**dim** ([*int*](https://docs.python.org/3/library/functions.html#int)) – the dimension to reduce. Default: 1**eps** ([*float*](https://docs.python.org/3/library/functions.html#float)) – small value to avoid division by zero. Default: 1e-12**out** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*,* *optional*) – the output tensor. If `out` is used, this operation won’t be differentiable.

## Linear functions

### linear

- `torch.nn.functional.``linear`(*input*, *weight*, *bias=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#linear)

  Applies a linear transformation to the incoming data: y = xA^T + by=xAT+b.Shape:Input: (N, *, in\_features)(N,∗,in_features) where * means any number of additional dimensionsWeight: (out\_features, in\_features)(out_features,in_features)Bias: (out\_features)(out_features)Output: (N, *, out\_features)(N,∗,out_features)

### bilinear

- `torch.nn.functional.``bilinear`(*input1*, *input2*, *weight*, *bias=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#bilinear)


## Dropout functions

### dropout

- `torch.nn.functional.``dropout`(*input*, *p=0.5*, *training=True*, *inplace=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#dropout)

  During training, randomly zeroes some of the elements of the input tensor with probability `p` using samples from a Bernoulli distribution.See [`Dropout`](https://pytorch.org/docs/stable/nn.html#torch.nn.Dropout) for details.Parameters**p** – probability of an element to be zeroed. Default: 0.5**training** – apply dropout if is `True`. Default: `True`**inplace** – If set to `True`, will do this operation in-place. Default: `False`

### alpha_dropout

- `torch.nn.functional.``alpha_dropout`(*input*, *p=0.5*, *training=False*, *inplace=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#alpha_dropout)

  Applies alpha dropout to the input.See [`AlphaDropout`](https://pytorch.org/docs/stable/nn.html#torch.nn.AlphaDropout) for details.

### dropout2d

- `torch.nn.functional.``dropout2d`(*input*, *p=0.5*, *training=True*, *inplace=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#dropout2d)

  Randomly zero out entire channels (a channel is a 2D feature map, e.g., the jj-th channel of the ii-th sample in the batched input is a 2D tensor \text{input}[i, j]input[i,j]) of the input tensor). Each channel will be zeroed out independently on every forward call with probability `p` using samples from a Bernoulli distribution.See [`Dropout2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.Dropout2d) for details.Parameters**p** – probability of a channel to be zeroed. Default: 0.5**training** – apply dropout if is `True`. Default: `True`**inplace** – If set to `True`, will do this operation in-place. Default: `False`

### dropout3d

- `torch.nn.functional.``dropout3d`(*input*, *p=0.5*, *training=True*, *inplace=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#dropout3d)

  Randomly zero out entire channels (a channel is a 3D feature map, e.g., the jj-th channel of the ii-th sample in the batched input is a 3D tensor \text{input}[i, j]input[i,j]) of the input tensor). Each channel will be zeroed out independently on every forward call with probability `p` using samples from a Bernoulli distribution.See [`Dropout3d`](https://pytorch.org/docs/stable/nn.html#torch.nn.Dropout3d) for details.Parameters**p** – probability of a channel to be zeroed. Default: 0.5**training** – apply dropout if is `True`. Default: `True`**inplace** – If set to `True`, will do this operation in-place. Default: `False`

## Sparse functions

### embedding

- `torch.nn.functional.``embedding`(*input*, *weight*, *padding_idx=None*, *max_norm=None*, *norm_type=2.0*, *scale_grad_by_freq=False*, *sparse=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#embedding)

  A simple lookup table that looks up embeddings in a fixed dictionary and size.This module is often used to retrieve word embeddings using indices. The input to the module is a list of indices, and the embedding matrix, and the output is the corresponding word embeddings.See [`torch.nn.Embedding`](https://pytorch.org/docs/stable/nn.html#torch.nn.Embedding) for more details.Parameters**input** (*LongTensor*) – Tensor containing indices into the embedding matrix**weight** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – The embedding matrix with number of rows equal to the maximum possible index + 1, and number of columns equal to the embedding size**padding_idx** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – If given, pads the output with the embedding vector at `padding_idx` (initialized to zeros) whenever it encounters the index.**max_norm** ([*float*](https://docs.python.org/3/library/functions.html#float)*,* *optional*) – If given, each embedding vector with norm larger than `max_norm` is renormalized to have norm `max_norm`. Note: this will modify `weight` in-place.**norm_type** ([*float*](https://docs.python.org/3/library/functions.html#float)*,* *optional*) – The p of the p-norm to compute for the `max_norm`option. Default `2`.**scale_grad_by_freq** (*boolean**,* *optional*) – If given, this will scale gradients by the inverse of frequency of the words in the mini-batch. Default `False`.**sparse** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – If `True`, gradient w.r.t. `weight` will be a sparse tensor. See Notes under [`torch.nn.Embedding`](https://pytorch.org/docs/stable/nn.html#torch.nn.Embedding) for more details regarding sparse gradients.Shape:Input: LongTensor of arbitrary shape containing the indices to extractWeight: Embedding matrix of floating point type with shape (V, embedding_dim),where V = maximum index + 1 and embedding_dim = the embedding sizeOutput: (*, embedding_dim), where * is the input shapeExamples:`>>> # a batch of 2 samples of 4 indices each >>> input = torch.tensor([[1,2,4,5],[4,3,2,9]]) >>> # an embedding matrix containing 10 tensors of size 3 >>> embedding_matrix = torch.rand(10, 3) >>> F.embedding(input, embedding_matrix) tensor([[[ 0.8490,  0.9625,  0.6753],          [ 0.9666,  0.7761,  0.6108],          [ 0.6246,  0.9751,  0.3618],          [ 0.4161,  0.2419,  0.7383]],          [[ 0.6246,  0.9751,  0.3618],          [ 0.0237,  0.7794,  0.0528],          [ 0.9666,  0.7761,  0.6108],          [ 0.3385,  0.8612,  0.1867]]])  >>> # example with padding_idx >>> weights = torch.rand(10, 3) >>> weights[0, :].zero_() >>> embedding_matrix = weights >>> input = torch.tensor([[0,2,0,5]]) >>> F.embedding(input, embedding_matrix, padding_idx=0) tensor([[[ 0.0000,  0.0000,  0.0000],          [ 0.5609,  0.5384,  0.8720],          [ 0.0000,  0.0000,  0.0000],          [ 0.6262,  0.2438,  0.7471]]]) `

### embedding_bag

- `torch.nn.functional.``embedding_bag`(*input*, *weight*, *offsets=None*, *max_norm=None*, *norm_type=2*, *scale_grad_by_freq=False*, *mode='mean'*, *sparse=False*, *per_sample_weights=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#embedding_bag)

  Computes sums, means or maxes of bags of embeddings, without instantiating the intermediate embeddings.See [`torch.nn.EmbeddingBag`](https://pytorch.org/docs/stable/nn.html#torch.nn.EmbeddingBag) for more details.NOTEWhen using the CUDA backend, this operation may induce nondeterministic behaviour in be backward that is not easily switched off. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.Parameters**input** (*LongTensor*) – Tensor containing bags of indices into the embedding matrix**weight** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – The embedding matrix with number of rows equal to the maximum possible index + 1, and number of columns equal to the embedding size**offsets** (*LongTensor**,* *optional*) – Only used when `input` is 1D. `offsets` determines the starting index position of each bag (sequence) in `input`.**max_norm** ([*float*](https://docs.python.org/3/library/functions.html#float)*,* *optional*) – If given, each embedding vector with norm larger than `max_norm` is renormalized to have norm `max_norm`. Note: this will modify `weight` in-place.**norm_type** ([*float*](https://docs.python.org/3/library/functions.html#float)*,* *optional*) – The `p` in the `p`-norm to compute for the `max_norm`option. Default `2`.**scale_grad_by_freq** (*boolean**,* *optional*) – if given, this will scale gradients by the inverse of frequency of the words in the mini-batch. Default `False`. Note: this option is not supported when `mode="max"`.**mode** (*string**,* *optional*) – `"sum"`, `"mean"` or `"max"`. Specifies the way to reduce the bag. Default: `"mean"`**sparse** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – if `True`, gradient w.r.t. `weight` will be a sparse tensor. See Notes under [`torch.nn.Embedding`](https://pytorch.org/docs/stable/nn.html#torch.nn.Embedding) for more details regarding sparse gradients. Note: this option is not supported when `mode="max"`.**per_sample_weights** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*,* *optional*) – a tensor of float / double weights, or None to indicate all weights should be taken to be 1. If specified, `per_sample_weights` must have exactly the same shape as input and is treated as having the same `offsets`, if those are not None.Shape:`input` (LongTensor) and `offsets` (LongTensor, optional)If `input` is 2D of shape (B, N),it will be treated as `B` bags (sequences) each of fixed length `N`, and this will return `B` values aggregated in a way depending on the `mode`. `offsets` is ignored and required to be `None` in this case.If `input` is 1D of shape (N),it will be treated as a concatenation of multiple bags (sequences). `offsets` is required to be a 1D tensor containing the starting index positions of each bag in `input`. Therefore, for `offsets` of shape (B), `input`will be viewed as having `B` bags. Empty bags (i.e., having 0-length) will have returned vectors filled by zeros.`weight` (Tensor): the learnable weights of the module of shape (num_embeddings, embedding_dim)`per_sample_weights` (Tensor, optional). Has the same shape as `input`.`output`: aggregated embedding values of shape (B, embedding_dim)Examples:`>>> # an Embedding module containing 10 tensors of size 3 >>> embedding_matrix = torch.rand(10, 3) >>> # a batch of 2 samples of 4 indices each >>> input = torch.tensor([1,2,4,5,4,3,2,9]) >>> offsets = torch.tensor([0,4]) >>> F.embedding_bag(embedding_matrix, input, offsets) tensor([[ 0.3397,  0.3552,  0.5545],         [ 0.5893,  0.4386,  0.5882]]) `

### one_hot

- `torch.nn.functional.``one_hot`(*tensor*, *num_classes=0*) → LongTensor

  Takes LongTensor with index values of shape `(*)` and returns a tensor of shape `(*, num_classes)`that have zeros everywhere except where the index of last dimension matches the corresponding value of the input tensor, in which case it will be 1.See also [One-hot on Wikipedia](https://en.wikipedia.org/wiki/One-hot) .Parameters**tensor** (*LongTensor*) – class values of any shape.**num_classes** ([*int*](https://docs.python.org/3/library/functions.html#int)) – Total number of classes. If set to -1, the number of classes will be inferred as one greater than the largest class value in the input tensor.ReturnsLongTensor that has one more dimension with 1 values at the index of last dimension indicated by the input, and 0 everywhere else.Examples`>>> F.one_hot(torch.arange(0, 5) % 3) tensor([[1, 0, 0],         [0, 1, 0],         [0, 0, 1],         [1, 0, 0],         [0, 1, 0]]) >>> F.one_hot(torch.arange(0, 5) % 3, num_classes=5) tensor([[1, 0, 0, 0, 0],         [0, 1, 0, 0, 0],         [0, 0, 1, 0, 0],         [1, 0, 0, 0, 0],         [0, 1, 0, 0, 0]]) >>> F.one_hot(torch.arange(0, 6).view(3,2) % 3) tensor([[[1, 0, 0],          [0, 1, 0]],         [[0, 0, 1],          [1, 0, 0]],         [[0, 1, 0],          [0, 0, 1]]]) `

## Distance functions

### pairwise_distance

- `torch.nn.functional.``pairwise_distance`(*x1*, *x2*, *p=2.0*, *eps=1e-06*, *keepdim=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#pairwise_distance)

  See [`torch.nn.PairwiseDistance`](https://pytorch.org/docs/stable/nn.html#torch.nn.PairwiseDistance) for details

### cosine_similarity

- `torch.nn.functional.``cosine_similarity`(*x1*, *x2*, *dim=1*, *eps=1e-8*) → Tensor

  Returns cosine similarity between x1 and x2, computed along dim.\text{similarity} = \dfrac{x_1 \cdot x_2}{\max(\Vert x_1 \Vert _2 \cdot \Vert x_2 \Vert _2, \epsilon)}similarity=max(∥x1∥2⋅∥x2∥2,ϵ)x1⋅x2Parameters**x1** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – First input.**x2** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – Second input (of size matching x1).**dim** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – Dimension of vectors. Default: 1**eps** ([*float*](https://docs.python.org/3/library/functions.html#float)*,* *optional*) – Small value to avoid division by zero. Default: 1e-8Shape:Input: (\ast_1, D, \ast_2)(∗1,D,∗2) where D is at position dim.Output: (\ast_1, \ast_2)(∗1,∗2) where 1 is at position dim.Example:`>>> input1 = torch.randn(100, 128) >>> input2 = torch.randn(100, 128) >>> output = F.cosine_similarity(input1, input2) >>> print(output) `

### pdist

- `torch.nn.functional.``pdist`(*input*, *p=2*) → Tensor

  Computes the p-norm distance between every pair of row vectors in the input. This is identical to the upper triangular portion, excluding the diagonal, of torch.norm(input[:, None] - input, dim=2, p=p). This function will be faster if the rows are contiguous.If input has shape N \times MN×M then the output will have shape \frac{1}{2} N (N - 1)21N(N−1).This function is equivalent to scipy.spatial.distance.pdist(input, ‘minkowski’, p=p) if p \in (0, \infty)p∈(0,∞). When p = 0p=0 it is equivalent to scipy.spatial.distance.pdist(input, ‘hamming’) * M. When p = \inftyp=∞, the closest scipy function is scipy.spatial.distance.pdist(xn, lambda x, y: np.abs(x - y).max()).Parameters**input** – input tensor of shape N \times MN×M.**p** – p value for the p-norm distance to calculate between each vector pair \in [0, \infty]∈[0,∞].

## Loss functions

### binary_cross_entropy

- `torch.nn.functional.``binary_cross_entropy`(*input*, *target*, *weight=None*, *size_average=None*, *reduce=None*, *reduction='mean'*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#binary_cross_entropy)

  Function that measures the Binary Cross Entropy between the target and the output.See [`BCELoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.BCELoss) for details.Parameters**input** – Tensor of arbitrary shape**target** – Tensor of the same shape as input**weight** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*,* *optional*) – a manual rescaling weight if provided it’s repeated to match input tensor shape**size_average** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Deprecated (see `reduction`). By default, the losses are averaged over each loss element in the batch. Note that for some losses, there multiple elements per sample. If the field `size_average` is set to `False`, the losses are instead summed for each minibatch. Ignored when reduce is `False`. Default: `True`**reduce** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Deprecated (see `reduction`). By default, the losses are averaged or summed over observations for each minibatch depending on `size_average`. When `reduce` is `False`, returns a loss per batch element instead and ignores `size_average`. Default: `True`**reduction** (*string**,* *optional*) – Specifies the reduction to apply to the output: `'none'` | `'mean'` | `'sum'`. `'none'`: no reduction will be applied, `'mean'`: the sum of the output will be divided by the number of elements in the output, `'sum'`: the output will be summed. Note: `size_average` and `reduce` are in the process of being deprecated, and in the meantime, specifying either of those two args will override `reduction`. Default: `'mean'`Examples:`>>> input = torch.randn((3, 2), requires_grad=True) >>> target = torch.rand((3, 2), requires_grad=False) >>> loss = F.binary_cross_entropy(F.sigmoid(input), target) >>> loss.backward() `

### binary_cross_entropy_with_logits

- `torch.nn.functional.``binary_cross_entropy_with_logits`(*input*, *target*, *weight=None*, *size_average=None*, *reduce=None*, *reduction='mean'*, *pos_weight=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#binary_cross_entropy_with_logits)

  Function that measures Binary Cross Entropy between target and output logits.See [`BCEWithLogitsLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.BCEWithLogitsLoss) for details.Parameters**input** – Tensor of arbitrary shape**target** – Tensor of the same shape as input**weight** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*,* *optional*) – a manual rescaling weight if provided it’s repeated to match input tensor shape**size_average** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Deprecated (see `reduction`). By default, the losses are averaged over each loss element in the batch. Note that for some losses, there multiple elements per sample. If the field `size_average` is set to `False`, the losses are instead summed for each minibatch. Ignored when reduce is `False`. Default: `True`**reduce** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Deprecated (see `reduction`). By default, the losses are averaged or summed over observations for each minibatch depending on `size_average`. When `reduce` is `False`, returns a loss per batch element instead and ignores `size_average`. Default: `True`**reduction** (*string**,* *optional*) – Specifies the reduction to apply to the output: `'none'` | `'mean'` | `'sum'`. `'none'`: no reduction will be applied, `'mean'`: the sum of the output will be divided by the number of elements in the output, `'sum'`: the output will be summed. Note: `size_average` and `reduce` are in the process of being deprecated, and in the meantime, specifying either of those two args will override `reduction`. Default: `'mean'`**pos_weight** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*,* *optional*) – a weight of positive examples. Must be a vector with length equal to the number of classes.Examples:`>>> input = torch.randn(3, requires_grad=True) >>> target = torch.empty(3).random_(2) >>> loss = F.binary_cross_entropy_with_logits(input, target) >>> loss.backward() `

### poisson_nll_loss

- `torch.nn.functional.``poisson_nll_loss`(*input*, *target*, *log_input=True*, *full=False*, *size_average=None*, *eps=1e-08*, *reduce=None*, *reduction='mean'*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#poisson_nll_loss)

  Poisson negative log likelihood loss.See [`PoissonNLLLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.PoissonNLLLoss) for details.Parameters**input** – expectation of underlying Poisson distribution.**target** – random sample target \sim \text{Poisson}(input)target∼Poisson(input).**log_input** – if `True` the loss is computed as \exp(\text{input}) - \text{target} * \text{input}exp(input)−target∗input, if `False` then loss is \text{input} - \text{target} * \log(\text{input}+\text{eps})input−target∗log(input+eps). Default: `True`**full** – whether to compute full loss, i. e. to add the Stirling approximation term. Default: `False` \text{target} * \log(\text{target}) - \text{target} + 0.5 * \log(2 * \pi * \text{target})target∗log(target)−target+0.5∗log(2∗π∗target).**size_average** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Deprecated (see `reduction`). By default, the losses are averaged over each loss element in the batch. Note that for some losses, there multiple elements per sample. If the field `size_average` is set to `False`, the losses are instead summed for each minibatch. Ignored when reduce is `False`. Default: `True`**eps** ([*float*](https://docs.python.org/3/library/functions.html#float)*,* *optional*) – Small value to avoid evaluation of \log(0)log(0) when`log_input`=``False``. Default: 1e-8**reduce** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Deprecated (see `reduction`). By default, the losses are averaged or summed over observations for each minibatch depending on `size_average`. When `reduce` is `False`, returns a loss per batch element instead and ignores `size_average`. Default: `True`**reduction** (*string**,* *optional*) – Specifies the reduction to apply to the output: `'none'` | `'mean'` | `'sum'`. `'none'`: no reduction will be applied, `'mean'`: the sum of the output will be divided by the number of elements in the output, `'sum'`: the output will be summed. Note: `size_average` and `reduce` are in the process of being deprecated, and in the meantime, specifying either of those two args will override `reduction`. Default: `'mean'`

### cosine_embedding_loss

- `torch.nn.functional.``cosine_embedding_loss`(*input1*, *input2*, *target*, *margin=0*, *size_average=None*, *reduce=None*, *reduction='mean'*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#cosine_embedding_loss)

  See [`CosineEmbeddingLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.CosineEmbeddingLoss) for details.

### cross_entropy

- `torch.nn.functional.``cross_entropy`(*input*, *target*, *weight=None*, *size_average=None*, *ignore_index=-100*, *reduce=None*, *reduction='mean'*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#cross_entropy)

  This criterion combines log_softmax and nll_loss in a single function.See [`CrossEntropyLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.CrossEntropyLoss) for details.Parameters**input** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – (N, C)(N,C) where C = number of classes or (N, C, H, W)(N,C,H,W) in case of 2D Loss, or (N, C, d_1, d_2, ..., d_K)(N,C,d1,d2,...,dK) where K \geq 1K≥1 in the case of K-dimensional loss.**target** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – (N)(N) where each value is 0 \leq \text{targets}[i] \leq C-10≤targets[i]≤C−1, or (N, d_1, d_2, ..., d_K)(N,d1,d2,...,dK) where K \geq 1K≥1 for K-dimensional loss.**weight** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*,* *optional*) – a manual rescaling weight given to each class. If given, has to be a Tensor of size C**size_average** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Deprecated (see `reduction`). By default, the losses are averaged over each loss element in the batch. Note that for some losses, there multiple elements per sample. If the field `size_average` is set to `False`, the losses are instead summed for each minibatch. Ignored when reduce is `False`. Default: `True`**ignore_index** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – Specifies a target value that is ignored and does not contribute to the input gradient. When `size_average` is `True`, the loss is averaged over non-ignored targets. Default: -100**reduce** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Deprecated (see `reduction`). By default, the losses are averaged or summed over observations for each minibatch depending on `size_average`. When `reduce` is `False`, returns a loss per batch element instead and ignores `size_average`. Default: `True`**reduction** (*string**,* *optional*) – Specifies the reduction to apply to the output: `'none'` | `'mean'` | `'sum'`. `'none'`: no reduction will be applied, `'mean'`: the sum of the output will be divided by the number of elements in the output, `'sum'`: the output will be summed. Note: `size_average` and `reduce` are in the process of being deprecated, and in the meantime, specifying either of those two args will override `reduction`. Default: `'mean'`Examples:`>>> input = torch.randn(3, 5, requires_grad=True) >>> target = torch.randint(5, (3,), dtype=torch.int64) >>> loss = F.cross_entropy(input, target) >>> loss.backward() `

### ctc_loss

- `torch.nn.functional.``ctc_loss`(*log_probs*, *targets*, *input_lengths*, *target_lengths*, *blank=0*, *reduction='mean'*, *zero_infinity=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#ctc_loss)

  The Connectionist Temporal Classification loss.See [`CTCLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.CTCLoss) for details.NOTEIn some circumstances when using the CUDA backend with CuDNN, this operator may select a nondeterministic algorithm to increase performance. If this is undesirable, you can try to make the operation deterministic (potentially at a performance cost) by setting `torch.backends.cudnn.deterministic = True`. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.NOTEWhen using the CUDA backend, this operation may induce nondeterministic behaviour in be backward that is not easily switched off. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.Parameters**log_probs** – (T, N, C)(T,N,C) where C = number of characters in alphabet including blank, T = input length, and N = batch size. The logarithmized probabilities of the outputs (e.g. obtained with [`torch.nn.functional.log_softmax()`](https://pytorch.org/docs/stable/nn.html#torch.nn.functional.log_softmax)).**targets** – (N, S)(N,S) or (sum(target_lengths)). Targets cannot be blank. In the second form, the targets are assumed to be concatenated.**input_lengths** – (N)(N). Lengths of the inputs (must each be \leq T≤T)**target_lengths** – (N)(N). Lengths of the targets**blank** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – Blank label. Default 00.**reduction** (*string**,* *optional*) – Specifies the reduction to apply to the output: `'none'` | `'mean'` | `'sum'`. `'none'`: no reduction will be applied, `'mean'`: the output losses will be divided by the target lengths and then the mean over the batch is taken, `'sum'`: the output will be summed. Default: `'mean'`**zero_infinity** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Whether to zero infinite losses and the associated gradients. Default: `False` Infinite losses mainly occur when the inputs are too short to be aligned to the targets.Example:`>>> log_probs = torch.randn(50, 16, 20).log_softmax(2).detach().requires_grad_() >>> targets = torch.randint(1, 20, (16, 30), dtype=torch.long) >>> input_lengths = torch.full((16,), 50, dtype=torch.long) >>> target_lengths = torch.randint(10,30,(16,), dtype=torch.long) >>> loss = F.ctc_loss(log_probs, targets, input_lengths, target_lengths) >>> loss.backward() `

### hinge_embedding_loss

- `torch.nn.functional.``hinge_embedding_loss`(*input*, *target*, *margin=1.0*, *size_average=None*, *reduce=None*, *reduction='mean'*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#hinge_embedding_loss)

  See [`HingeEmbeddingLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.HingeEmbeddingLoss) for details.

### kl_div

- `torch.nn.functional.``kl_div`(*input*, *target*, *size_average=None*, *reduce=None*, *reduction='mean'*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#kl_div)

  The [Kullback-Leibler divergence](https://en.wikipedia.org/wiki/Kullback-Leibler_divergence) Loss.See [`KLDivLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.KLDivLoss) for details.Parameters**input** – Tensor of arbitrary shape**target** – Tensor of the same shape as input**size_average** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Deprecated (see `reduction`). By default, the losses are averaged over each loss element in the batch. Note that for some losses, there multiple elements per sample. If the field `size_average` is set to `False`, the losses are instead summed for each minibatch. Ignored when reduce is `False`. Default: `True`**reduce** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Deprecated (see `reduction`). By default, the losses are averaged or summed over observations for each minibatch depending on `size_average`. When `reduce` is `False`, returns a loss per batch element instead and ignores `size_average`. Default: `True`**reduction** (*string**,* *optional*) – Specifies the reduction to apply to the output: `'none'` | `'batchmean'` | `'sum'` | `'mean'`. `'none'`: no reduction will be applied `'batchmean'`: the sum of the output will be divided by the batchsize `'sum'`: the output will be summed `'mean'`: the output will be divided by the number of elements in the output Default: `'mean'`NOTE`size_average` and `reduce` are in the process of being deprecated, and in the meantime, specifying either of those two args will override `reduction`.NOTE:attr:`reduction` = `'mean'` doesn’t return the true kl divergence value, please use :attr:`reduction` = `'batchmean'` which aligns with KL math definition. In the next major release, `'mean'` will be changed to be the same as ‘batchmean’.

### l1_loss

- `torch.nn.functional.``l1_loss`(*input*, *target*, *size_average=None*, *reduce=None*, *reduction='mean'*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#l1_loss)

  Function that takes the mean element-wise absolute value difference.See [`L1Loss`](https://pytorch.org/docs/stable/nn.html#torch.nn.L1Loss) for details.

### mse_loss

- `torch.nn.functional.``mse_loss`(*input*, *target*, *size_average=None*, *reduce=None*, *reduction='mean'*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#mse_loss)

  Measures the element-wise mean squared error.See [`MSELoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.MSELoss) for details.

### margin_ranking_loss

- `torch.nn.functional.``margin_ranking_loss`(*input1*, *input2*, *target*, *margin=0*, *size_average=None*, *reduce=None*, *reduction='mean'*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#margin_ranking_loss)

  See [`MarginRankingLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.MarginRankingLoss) for details.

### multilabel_margin_loss

- `torch.nn.functional.``multilabel_margin_loss`(*input*, *target*, *size_average=None*, *reduce=None*, *reduction='mean'*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#multilabel_margin_loss)

  See [`MultiLabelMarginLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.MultiLabelMarginLoss) for details.

### multilabel_soft_margin_loss

- `torch.nn.functional.``multilabel_soft_margin_loss`(*input*, *target*, *weight=None*, *size_average=None*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#multilabel_soft_margin_loss)

  See [`MultiLabelSoftMarginLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.MultiLabelSoftMarginLoss) for details.

### multi_margin_loss

- `torch.nn.functional.``multi_margin_loss`(*input*, *target*, *p=1*, *margin=1.0*, *weight=None*, *size_average=None*, *reduce=None*, *reduction='mean'*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#multi_margin_loss)

  multi_margin_loss(input, target, p=1, margin=1, weight=None, size_average=None,reduce=None, reduction=’mean’) -> TensorSee [`MultiMarginLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.MultiMarginLoss) for details.

### nll_loss

- `torch.nn.functional.``nll_loss`(*input*, *target*, *weight=None*, *size_average=None*, *ignore_index=-100*, *reduce=None*, *reduction='mean'*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#nll_loss)

  The negative log likelihood loss.See [`NLLLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.NLLLoss) for details.Parameters**input** – (N, C)(N,C) where C = number of classes or (N, C, H, W)(N,C,H,W) in case of 2D Loss, or (N, C, d_1, d_2, ..., d_K)(N,C,d1,d2,...,dK) where K \geq 1K≥1 in the case of K-dimensional loss.**target** – (N)(N) where each value is 0 \leq \text{targets}[i] \leq C-10≤targets[i]≤C−1, or (N, d_1, d_2, ..., d_K)(N,d1,d2,...,dK) where K \geq 1K≥1 for K-dimensional loss.**weight** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*,* *optional*) – a manual rescaling weight given to each class. If given, has to be a Tensor of size C**size_average** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Deprecated (see `reduction`). By default, the losses are averaged over each loss element in the batch. Note that for some losses, there multiple elements per sample. If the field `size_average` is set to `False`, the losses are instead summed for each minibatch. Ignored when reduce is `False`. Default: `True`**ignore_index** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – Specifies a target value that is ignored and does not contribute to the input gradient. When `size_average` is `True`, the loss is averaged over non-ignored targets. Default: -100**reduce** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Deprecated (see `reduction`). By default, the losses are averaged or summed over observations for each minibatch depending on `size_average`. When `reduce` is `False`, returns a loss per batch element instead and ignores `size_average`. Default: `True`**reduction** (*string**,* *optional*) – Specifies the reduction to apply to the output: `'none'` | `'mean'` | `'sum'`. `'none'`: no reduction will be applied, `'mean'`: the sum of the output will be divided by the number of elements in the output, `'sum'`: the output will be summed. Note: `size_average` and `reduce` are in the process of being deprecated, and in the meantime, specifying either of those two args will override `reduction`. Default: `'mean'`Example:`>>> # input is of size N x C = 3 x 5 >>> input = torch.randn(3, 5, requires_grad=True) >>> # each element in target has to have 0 <= value < C >>> target = torch.tensor([1, 0, 4]) >>> output = F.nll_loss(F.log_softmax(input), target) >>> output.backward() `

### smooth_l1_loss

- `torch.nn.functional.``smooth_l1_loss`(*input*, *target*, *size_average=None*, *reduce=None*, *reduction='mean'*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#smooth_l1_loss)

  Function that uses a squared term if the absolute element-wise error falls below 1 and an L1 term otherwise.See [`SmoothL1Loss`](https://pytorch.org/docs/stable/nn.html#torch.nn.SmoothL1Loss) for details.

### soft_margin_loss

- `torch.nn.functional.``soft_margin_loss`(*input*, *target*, *size_average=None*, *reduce=None*, *reduction='mean'*) → Tensor[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#soft_margin_loss)

  See [`SoftMarginLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.SoftMarginLoss) for details.

### triplet_margin_loss

- `torch.nn.functional.``triplet_margin_loss`(*anchor*, *positive*, *negative*, *margin=1.0*, *p=2*, *eps=1e-06*, *swap=False*, *size_average=None*, *reduce=None*, *reduction='mean'*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#triplet_margin_loss)

  See [`TripletMarginLoss`](https://pytorch.org/docs/stable/nn.html#torch.nn.TripletMarginLoss) for details

## Vision functions

### pixel_shuffle

- `torch.nn.functional.``pixel_shuffle`()

  Rearranges elements in a tensor of shape (*, C \times r^2, H, W)(∗,C×r2,H,W) to a tensor of shape (*, C, H \times r, W \times r)(∗,C,H×r,W×r).See [`PixelShuffle`](https://pytorch.org/docs/stable/nn.html#torch.nn.PixelShuffle) for details.Parameters**input** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – the input tensor**upscale_factor** ([*int*](https://docs.python.org/3/library/functions.html#int)) – factor to increase spatial resolution byExamples:`>>> input = torch.randn(1, 9, 4, 4) >>> output = torch.nn.functional.pixel_shuffle(input, 3) >>> print(output.size()) torch.Size([1, 1, 12, 12]) `

### pad

- `torch.nn.functional.``pad`(*input*, *pad*, *mode='constant'*, *value=0*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#pad)

  Pads tensor.Padding size:The padding size by which to pad some dimensions of `input` are described starting from the last dimension and moving forward. \left\lfloor\frac{\text{len(pad)}}{2}\right\rfloor⌊2len(pad)⌋ dimensions of `input` will be padded. For example, to pad only the last dimension of the input tensor, then [`pad`](https://pytorch.org/docs/stable/nn.html#torch.nn.functional.pad) has the form (\text{padding\_left}, \text{padding\_right})(padding_left,padding_right); to pad the last 2 dimensions of the input tensor, then use (\text{padding\_left}, \text{padding\_right},(padding_left,padding_right, \text{padding\_top}, \text{padding\_bottom})padding_top,padding_bottom); to pad the last 3 dimensions, use (\text{padding\_left}, \text{padding\_right},(padding_left,padding_right, \text{padding\_top}, \text{padding\_bottom}padding_top,padding_bottom \text{padding\_front}, \text{padding\_back})padding_front,padding_back).Padding mode:See [`torch.nn.ConstantPad2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.ConstantPad2d), [`torch.nn.ReflectionPad2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.ReflectionPad2d), and[`torch.nn.ReplicationPad2d`](https://pytorch.org/docs/stable/nn.html#torch.nn.ReplicationPad2d) for concrete examples on how each of the padding modes works. Constant padding is implemented for arbitrary dimensions. Replicate padding is implemented for padding the last 3 dimensions of 5D input tensor, or the last 2 dimensions of 4D input tensor, or the last dimension of 3D input tensor. Reflect padding is only implemented for padding the last 2 dimensions of 4D input tensor, or the last dimension of 3D input tensor.NOTEWhen using the CUDA backend, this operation may induce nondeterministic behaviour in be backward that is not easily switched off. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.Parameters**input** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – N-dimensional tensor**pad** ([*tuple*](https://docs.python.org/3/library/stdtypes.html#tuple)) – m-elements tuple, where \frac{m}{2} \leq2m≤ input dimensions and mm is even.**mode** – `'constant'`, `'reflect'`, `'replicate'` or `'circular'`. Default: `'constant'`**value** – fill value for `'constant'` padding. Default: `0`Examples:`>>> t4d = torch.empty(3, 3, 4, 2) >>> p1d = (1, 1) # pad last dim by 1 on each side >>> out = F.pad(t4d, p1d, "constant", 0)  # effectively zero padding >>> print(out.data.size()) torch.Size([3, 3, 4, 4]) >>> p2d = (1, 1, 2, 2) # pad last dim by (1, 1) and 2nd to last by (2, 2) >>> out = F.pad(t4d, p2d, "constant", 0) >>> print(out.data.size()) torch.Size([3, 3, 8, 4]) >>> t4d = torch.empty(3, 3, 4, 2) >>> p3d = (0, 1, 2, 1, 3, 3) # pad by (0, 1), (2, 1), and (3, 3) >>> out = F.pad(t4d, p3d, "constant", 0) >>> print(out.data.size()) torch.Size([3, 9, 7, 3]) `

### interpolate

- `torch.nn.functional.``interpolate`(*input*, *size=None*, *scale_factor=None*, *mode='nearest'*, *align_corners=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#interpolate)

  Down/up samples the input to either the given `size` or the given `scale_factor`The algorithm used for interpolation is determined by `mode`.Currently temporal, spatial and volumetric sampling are supported, i.e. expected inputs are 3-D, 4-D or 5-D in shape.The input dimensions are interpreted in the form: mini-batch x channels x [optional depth] x [optional height] x width.The modes available for resizing are: nearest, linear (3D-only), bilinear, bicubic (4D-only), trilinear(5D-only), areaParameters**input** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – the input tensor**size** ([*int*](https://docs.python.org/3/library/functions.html#int) *or* *Tuple**[*[*int*](https://docs.python.org/3/library/functions.html#int)*] or* *Tuple**[*[*int*](https://docs.python.org/3/library/functions.html#int)*,* [*int*](https://docs.python.org/3/library/functions.html#int)*] or* *Tuple**[*[*int*](https://docs.python.org/3/library/functions.html#int)*,* [*int*](https://docs.python.org/3/library/functions.html#int)*,* [*int*](https://docs.python.org/3/library/functions.html#int)*]*) – output spatial size.**scale_factor** ([*float*](https://docs.python.org/3/library/functions.html#float) *or* *Tuple**[*[*float*](https://docs.python.org/3/library/functions.html#float)*]*) – multiplier for spatial size. Has to match input size if it is a tuple.**mode** ([*str*](https://docs.python.org/3/library/stdtypes.html#str)) – algorithm used for upsampling: `'nearest'` | `'linear'` | `'bilinear'` | `'bicubic'` | `'trilinear'` | `'area'`. Default: `'nearest'`**align_corners** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Geometrically, we consider the pixels of the input and output as squares rather than points. If set to `True`, the input and output tensors are aligned by the center points of their corner pixels. If set to `False`, the input and output tensors are aligned by the corner points of their corner pixels, and the interpolation uses edge value padding for out-of-boundary values. This only has effect when `mode` is `'linear'`, `'bilinear'`, `'bicubic'`, or `'trilinear'`. Default: `False`WARNINGWith `align_corners = True`, the linearly interpolating modes (linear, bilinear, and trilinear) don’t proportionally align the output and input pixels, and thus the output values can depend on the input size. This was the default behavior for these modes up to version 0.3.1. Since then, the default behavior is `align_corners = False`. See [`Upsample`](https://pytorch.org/docs/stable/nn.html#torch.nn.Upsample) for concrete examples on how this affects the outputs.NOTEWhen using the CUDA backend, this operation may induce nondeterministic behaviour in be backward that is not easily switched off. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.

### upsample

- `torch.nn.functional.``upsample`(*input*, *size=None*, *scale_factor=None*, *mode='nearest'*, *align_corners=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#upsample)

  Upsamples the input to either the given `size` or the given `scale_factor`WARNINGThis function is deprecated in favor of [`torch.nn.functional.interpolate()`](https://pytorch.org/docs/stable/nn.html#torch.nn.functional.interpolate). This is equivalent with `nn.functional.interpolate(...)`.NOTEWhen using the CUDA backend, this operation may induce nondeterministic behaviour in be backward that is not easily switched off. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.The algorithm used for upsampling is determined by `mode`.Currently temporal, spatial and volumetric upsampling are supported, i.e. expected inputs are 3-D, 4-D or 5-D in shape.The input dimensions are interpreted in the form: mini-batch x channels x [optional depth] x [optional height] x width.The modes available for upsampling are: nearest, linear (3D-only), bilinear, bicubic (4D-only), trilinear (5D-only)Parameters**input** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – the input tensor**size** ([*int*](https://docs.python.org/3/library/functions.html#int) *or* *Tuple**[*[*int*](https://docs.python.org/3/library/functions.html#int)*] or* *Tuple**[*[*int*](https://docs.python.org/3/library/functions.html#int)*,* [*int*](https://docs.python.org/3/library/functions.html#int)*] or* *Tuple**[*[*int*](https://docs.python.org/3/library/functions.html#int)*,* [*int*](https://docs.python.org/3/library/functions.html#int)*,* [*int*](https://docs.python.org/3/library/functions.html#int)*]*) – output spatial size.**scale_factor** ([*float*](https://docs.python.org/3/library/functions.html#float) *or* *Tuple**[*[*float*](https://docs.python.org/3/library/functions.html#float)*]*) – multiplier for spatial size. Has to be an integer.**mode** (*string*) – algorithm used for upsampling: `'nearest'` | `'linear'` | `'bilinear'`| `'bicubic'` | `'trilinear'`. Default: `'nearest'`**align_corners** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Geometrically, we consider the pixels of the input and output as squares rather than points. If set to `True`, the input and output tensors are aligned by the center points of their corner pixels. If set to `False`, the input and output tensors are aligned by the corner points of their corner pixels, and the interpolation uses edge value padding for out-of-boundary values. This only has effect when `mode` is `'linear'`, `'bilinear'`, `'bicubic'` or `'trilinear'`. Default: `False`WARNINGWith `align_corners = True`, the linearly interpolating modes (linear, bilinear, and trilinear) don’t proportionally align the output and input pixels, and thus the output values can depend on the input size. This was the default behavior for these modes up to version 0.3.1. Since then, the default behavior is `align_corners = False`. See [`Upsample`](https://pytorch.org/docs/stable/nn.html#torch.nn.Upsample) for concrete examples on how this affects the outputs.

### upsample_nearest

- `torch.nn.functional.``upsample_nearest`(*input*, *size=None*, *scale_factor=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#upsample_nearest)

  Upsamples the input, using nearest neighbours’ pixel values.WARNINGThis function is deprecated in favor of [`torch.nn.functional.interpolate()`](https://pytorch.org/docs/stable/nn.html#torch.nn.functional.interpolate). This is equivalent with `nn.functional.interpolate(..., mode='nearest')`.Currently spatial and volumetric upsampling are supported (i.e. expected inputs are 4 or 5 dimensional).Parameters**input** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – input**size** ([*int*](https://docs.python.org/3/library/functions.html#int) *or* *Tuple**[*[*int*](https://docs.python.org/3/library/functions.html#int)*,* [*int*](https://docs.python.org/3/library/functions.html#int)*] or* *Tuple**[*[*int*](https://docs.python.org/3/library/functions.html#int)*,* [*int*](https://docs.python.org/3/library/functions.html#int)*,* [*int*](https://docs.python.org/3/library/functions.html#int)*]*) – output spatia size.**scale_factor** ([*int*](https://docs.python.org/3/library/functions.html#int)) – multiplier for spatial size. Has to be an integer.NOTEWhen using the CUDA backend, this operation may induce nondeterministic behaviour in be backward that is not easily switched off. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.

### upsample_bilinear

- `torch.nn.functional.``upsample_bilinear`(*input*, *size=None*, *scale_factor=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#upsample_bilinear)

  Upsamples the input, using bilinear upsampling.WARNINGThis function is deprecated in favor of [`torch.nn.functional.interpolate()`](https://pytorch.org/docs/stable/nn.html#torch.nn.functional.interpolate). This is equivalent with `nn.functional.interpolate(..., mode='bilinear',align_corners=True)`.Expected inputs are spatial (4 dimensional). Use upsample_trilinear fo volumetric (5 dimensional) inputs.Parameters**input** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – input**size** ([*int*](https://docs.python.org/3/library/functions.html#int) *or* *Tuple**[*[*int*](https://docs.python.org/3/library/functions.html#int)*,* [*int*](https://docs.python.org/3/library/functions.html#int)*]*) – output spatial size.**scale_factor** ([*int*](https://docs.python.org/3/library/functions.html#int) *or* *Tuple**[*[*int*](https://docs.python.org/3/library/functions.html#int)*,* [*int*](https://docs.python.org/3/library/functions.html#int)*]*) – multiplier for spatial sizeNOTEWhen using the CUDA backend, this operation may induce nondeterministic behaviour in be backward that is not easily switched off. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.

### grid_sample

- `torch.nn.functional.``grid_sample`(*input*, *grid*, *mode='bilinear'*, *padding_mode='zeros'*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#grid_sample)

  Given an `input` and a flow-field `grid`, computes the `output` using `input` values and pixel locations from `grid`.Currently, only spatial (4-D) and volumetric (5-D) `input` are supported.In the spatial (4-D) case, for `input` with shape (N, C, H_\text{in}, W_\text{in})(N,C,Hin,Win) and `grid` with shape (N, H_\text{out}, W_\text{out}, 2)(N,Hout,Wout,2), the output will have shape (N, C, H_\text{out}, W_\text{out})(N,C,Hout,Wout).For each output location `output[n, :, h, w]`, the size-2 vector `grid[n, h, w]` specifies `input`pixel locations `x` and `y`, which are used to interpolate the output value `output[n, :, h, w]`. In the case of 5D inputs, `grid[n, d, h, w]` specifies the `x`, `y`, `z` pixel locations for interpolating`output[n, :, d, h, w]`. `mode` argument specifies `nearest` or `bilinear` interpolation method to sample the input pixels.`grid` specifies the sampling pixel locations normalized by the `input` spatial dimensions. Therefore, it should have most values in the range of `[-1, 1]`. For example, values `x = -1, y = -1` is the left-top pixel of `input`, and values `x = 1, y = 1` is the right-bottom pixel of `input`.If `grid` has values outside the range of `[-1, 1]`, the corresponding outputs are handled as defined by `padding_mode`. Options are`padding_mode="zeros"`: use `0` for out-of-bound grid locations,`padding_mode="border"`: use border values for out-of-bound grid locations,`padding_mode="reflection"`: use values at locations reflected by the border for out-of-bound grid locations. For location far away from the border, it will keep being reflected until becoming in bound, e.g., (normalized) pixel location `x = -3.5` reflects by border `-1` and becomes `x' =1.5`, then reflects by border `1` and becomes `x'' = -0.5`.NOTEThis function is often used in building [Spatial Transformer Networks](https://arxiv.org/abs/1506.02025) .NOTEWhen using the CUDA backend, this operation may induce nondeterministic behaviour in be backward that is not easily switched off. Please see the notes on [Reproducibility](https://pytorch.org/docs/stable/notes/randomness.html) for background.Parameters**input** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – input of shape (N, C, H_\text{in}, W_\text{in})(N,C,Hin,Win) (4-D case) or (N, C, D_\text{in}, H_\text{in}, W_\text{in})(N,C,Din,Hin,Win) (5-D case)**grid** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – flow-field of shape (N, H_\text{out}, W_\text{out}, 2)(N,Hout,Wout,2) (4-D case) or (N, D_\text{out}, H_\text{out}, W_\text{out}, 3)(N,Dout,Hout,Wout,3) (5-D case)**mode** ([*str*](https://docs.python.org/3/library/stdtypes.html#str)) – interpolation mode to calculate output values `'bilinear'` | `'nearest'`. Default: `'bilinear'`**padding_mode** ([*str*](https://docs.python.org/3/library/stdtypes.html#str)) – padding mode for outside grid values `'zeros'` | `'border'` | `'reflection'`. Default: `'zeros'`Returnsoutput TensorReturn typeoutput ([Tensor](https://pytorch.org/docs/stable/tensors.html#torch.Tensor))

### affine_grid

- `torch.nn.functional.``affine_grid`(*theta*, *size*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/functional.html#affine_grid)

  Generates a 2d flow field, given a batch of affine matrices `theta`. Generally used in conjunction with [`grid_sample()`](https://pytorch.org/docs/stable/nn.html#torch.nn.functional.grid_sample) to implement Spatial Transformer Networks.Parameters**theta** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – input batch of affine matrices (N \times 2 \times 3N×2×3)**size** (*torch.Size*) – the target output image size (N \times C \times H \times WN×C×H×W). Example: torch.Size((32, 3, 24, 24))Returnsoutput Tensor of size (N \times H \times W \times 2N×H×W×2)Return typeoutput ([Tensor](https://pytorch.org/docs/stable/tensors.html#torch.Tensor))

## DataParallel functions (multi-GPU, distributed)

### data_parallel

- `torch.nn.parallel.``data_parallel`(*module*, *inputs*, *device_ids=None*, *output_device=None*, *dim=0*, *module_kwargs=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/parallel/data_parallel.html#data_parallel)

  Evaluates module(input) in parallel across the GPUs given in device_ids.This is the functional version of the DataParallel module.Parameters**module** ([*Module*](https://pytorch.org/docs/stable/nn.html#torch.nn.Module)) – the module to evaluate in parallel**inputs** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – inputs to the module**device_ids** (*list of python:int* *or* [*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device)) – GPU ids on which to replicate module**output_device** (*list of python:int* *or* [*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device)) – GPU location of the output Use -1 to indicate the CPU. (default: device_ids[0])Returnsa Tensor containing the result of module(input) located on output_device
