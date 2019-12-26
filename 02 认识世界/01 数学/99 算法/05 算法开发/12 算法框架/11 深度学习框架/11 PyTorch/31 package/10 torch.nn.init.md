---
title: 10 torch.nn.init
toc: true
date: 2019-06-29
---
# TORCH.NN.INIT

- `torch.nn.init.``calculate_gain`(*nonlinearity*, *param=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/init.html#calculate_gain)

  Return the recommended gain value for the given nonlinearity function. The values are as follows:nonlinearitygainLinear / Identity11Conv{1,2,3}D11Sigmoid11Tanh\frac{5}{3}35ReLU\sqrt{2}2Leaky Relu\sqrt{\frac{2}{1 + \text{negative\_slope}^2}}1+negative_slope22Parameters**nonlinearity** – the non-linear function (nn.functional name)**param** – optional parameter for the non-linear functionExamples`>>> gain = nn.init.calculate_gain('leaky_relu') `

- `torch.nn.init.``uniform_`(*tensor*, *a=0.0*, *b=1.0*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/init.html#uniform_)

  Fills the input Tensor with values drawn from the uniform distribution \mathcal{U}(a, b)U(a,b).Parameters**tensor** – an n-dimensional torch.Tensor**a** – the lower bound of the uniform distribution**b** – the upper bound of the uniform distributionExamples`>>> w = torch.empty(3, 5) >>> nn.init.uniform_(w) `

- `torch.nn.init.``normal_`(*tensor*, *mean=0.0*, *std=1.0*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/init.html#normal_)

  Fills the input Tensor with values drawn from the normal distribution \mathcal{N}(\text{mean}, \text{std})N(mean,std).Parameters**tensor** – an n-dimensional torch.Tensor**mean** – the mean of the normal distribution**std** – the standard deviation of the normal distributionExamples`>>> w = torch.empty(3, 5) >>> nn.init.normal_(w) `

- `torch.nn.init.``constant_`(*tensor*, *val*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/init.html#constant_)

  Fills the input Tensor with the value \text{val}val.Parameters**tensor** – an n-dimensional torch.Tensor**val** – the value to fill the tensor withExamples`>>> w = torch.empty(3, 5) >>> nn.init.constant_(w, 0.3) `

- `torch.nn.init.``eye_`(*tensor*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/init.html#eye_)

  Fills the 2-dimensional input Tensor with the identity matrix. Preserves the identity of the inputs in Linear layers, where as many inputs are preserved as possible.Parameters**tensor** – a 2-dimensional torch.TensorExamples`>>> w = torch.empty(3, 5) >>> nn.init.eye_(w) `

- `torch.nn.init.``dirac_`(*tensor*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/init.html#dirac_)

  Fills the {3, 4, 5}-dimensional input Tensor with the Dirac delta function. Preserves the identity of the inputs in Convolutional layers, where as many input channels are preserved as possible.Parameters**tensor** – a {3, 4, 5}-dimensional torch.TensorExamples`>>> w = torch.empty(3, 16, 5, 5) >>> nn.init.dirac_(w) `

- `torch.nn.init.``xavier_uniform_`(*tensor*, *gain=1.0*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/init.html#xavier_uniform_)

  Fills the input Tensor with values according to the method described in Understanding the difficulty of training deep feedforward neural networks - Glorot, X. & Bengio, Y. (2010), using a uniform distribution. The resulting tensor will have values sampled from \mathcal{U}(-a, a)U(−a,a) wherea = \text{gain} \times \sqrt{\frac{6}{\text{fan\_in} + \text{fan\_out}}}a=gain×fan_in+fan_out6Also known as Glorot initialization.Parameters**tensor** – an n-dimensional torch.Tensor**gain** – an optional scaling factorExamples`>>> w = torch.empty(3, 5) >>> nn.init.xavier_uniform_(w, gain=nn.init.calculate_gain('relu')) `

- `torch.nn.init.``xavier_normal_`(*tensor*, *gain=1.0*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/init.html#xavier_normal_)

  Fills the input Tensor with values according to the method described in Understanding the difficulty of training deep feedforward neural networks - Glorot, X. & Bengio, Y. (2010), using a normal distribution. The resulting tensor will have values sampled from \mathcal{N}(0, \text{std})N(0,std) where\text{std} = \text{gain} \times \sqrt{\frac{2}{\text{fan\_in} + \text{fan\_out}}}std=gain×fan_in+fan_out2Also known as Glorot initialization.Parameters**tensor** – an n-dimensional torch.Tensor**gain** – an optional scaling factorExamples`>>> w = torch.empty(3, 5) >>> nn.init.xavier_normal_(w) `

- `torch.nn.init.``kaiming_uniform_`(*tensor*, *a=0*, *mode='fan_in'*, *nonlinearity='leaky_relu'*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/init.html#kaiming_uniform_)

  Fills the input Tensor with values according to the method described in Delving deep into rectifiers: Surpassing human-level performance on ImageNet classification - He, K. et al. (2015), using a uniform distribution. The resulting tensor will have values sampled from \mathcal{U}(-\text{bound}, \text{bound})U(−bound,bound) where\text{bound} = \sqrt{\frac{6}{(1 + a^2) \times \text{fan\_in}}}bound=(1+a2)×fan_in6Also known as He initialization.Parameters**tensor** – an n-dimensional torch.Tensor**a** – the negative slope of the rectifier used after this layer (0 for ReLU by default)**mode** – either `'fan_in'` (default) or `'fan_out'`. Choosing `'fan_in'` preserves the magnitude of the variance of the weights in the forward pass. Choosing `'fan_out'`preserves the magnitudes in the backwards pass.**nonlinearity** – the non-linear function (nn.functional name), recommended to use only with `'relu'` or `'leaky_relu'` (default).Examples`>>> w = torch.empty(3, 5) >>> nn.init.kaiming_uniform_(w, mode='fan_in', nonlinearity='relu') `

- `torch.nn.init.``kaiming_normal_`(*tensor*, *a=0*, *mode='fan_in'*, *nonlinearity='leaky_relu'*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/init.html#kaiming_normal_)

  Fills the input Tensor with values according to the method described in Delving deep into rectifiers: Surpassing human-level performance on ImageNet classification - He, K. et al. (2015), using a normal distribution. The resulting tensor will have values sampled from \mathcal{N}(0, \text{std})N(0,std) where\text{std} = \sqrt{\frac{2}{(1 + a^2) \times \text{fan\_in}}}std=(1+a2)×fan_in2Also known as He initialization.Parameters**tensor** – an n-dimensional torch.Tensor**a** – the negative slope of the rectifier used after this layer (0 for ReLU by default)**mode** – either `'fan_in'` (default) or `'fan_out'`. Choosing `'fan_in'` preserves the magnitude of the variance of the weights in the forward pass. Choosing `'fan_out'`preserves the magnitudes in the backwards pass.**nonlinearity** – the non-linear function (nn.functional name), recommended to use only with `'relu'` or `'leaky_relu'` (default).Examples`>>> w = torch.empty(3, 5) >>> nn.init.kaiming_normal_(w, mode='fan_out', nonlinearity='relu') `

- `torch.nn.init.``orthogonal_`(*tensor*, *gain=1*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/init.html#orthogonal_)

  Fills the input Tensor with a (semi) orthogonal matrix, as described in Exact solutions to the nonlinear dynamics of learning in deep linear neural networks - Saxe, A. et al. (2013). The input tensor must have at least 2 dimensions, and for tensors with more than 2 dimensions the trailing dimensions are flattened.Parameters**tensor** – an n-dimensional torch.Tensor, where n \geq 2n≥2**gain** – optional scaling factorExamples`>>> w = torch.empty(3, 5) >>> nn.init.orthogonal_(w) `

- `torch.nn.init.``sparse_`(*tensor*, *sparsity*, *std=0.01*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/nn/init.html#sparse_)

  Fills the 2D input Tensor as a sparse matrix, where the non-zero elements will be drawn from the normal distribution \mathcal{N}(0, 0.01)N(0,0.01), as described in Deep learning via Hessian-free optimization - Martens, J. (2010).Parameters**tensor** – an n-dimensional torch.Tensor**sparsity** – The fraction of elements in each column to be set to zero**std** – the standard deviation of the normal distribution used to generate the non-zero valuesExamples`>>> w = torch.empty(3, 5) >>> nn.init.sparse_(w, sparsity=0.1)`
