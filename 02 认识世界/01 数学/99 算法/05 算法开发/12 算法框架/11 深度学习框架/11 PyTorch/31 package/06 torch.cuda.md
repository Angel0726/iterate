---
title: 06 torch.cuda
toc: true
date: 2019-06-30
---
# TORCH.CUDA

This package adds support for CUDA tensor types, that implement the same function as CPU tensors, but they utilize GPUs for computation.

It is lazily initialized, so you can always import it, and use [`is_available()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.is_available) to determine if your system supports CUDA.

[CUDA semantics](https://pytorch.org/docs/stable/notes/cuda.html#cuda-semantics) has more details about working with CUDA.

- `torch.cuda.``current_blas_handle`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#current_blas_handle)

  Returns cublasHandle_t pointer to current cuBLAS handle

- `torch.cuda.``current_device`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#current_device)

  Returns the index of a currently selected device.

- `torch.cuda.``current_stream`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#current_stream)

  Returns the currently selected [`Stream`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.Stream) for a given device.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns the currently selected [`Stream`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.Stream) for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).

- `torch.cuda.``default_stream`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#default_stream)

  Returns the default [`Stream`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.Stream) for a given device.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns the default [`Stream`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.Stream) for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).

- *CLASS*`torch.cuda.``device`(*device*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#device)

  Context-manager that changes the selected device.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)) – device index to select. It’s a no-op if this argument is a negative integer or `None`.

- `torch.cuda.``device_count`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#device_count)

  Returns the number of GPUs available.

- *CLASS*`torch.cuda.``device_of`(*obj*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#device_of)

  Context-manager that changes the current device to that of given object.You can use both tensors and storages as arguments. If a given object is not allocated on a GPU, this is a no-op.Parameters**obj** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor) *or* *Storage*) – object allocated on the selected device.

- `torch.cuda.``empty_cache`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#empty_cache)

  Releases all unoccupied cached memory currently held by the caching allocator so that those can be used in other GPU application and visible in nvidia-smi.NOTE[`empty_cache()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.empty_cache) doesn’t increase the amount of GPU memory available for PyTorch. See [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

- `torch.cuda.``get_device_capability`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#get_device_capability)

  Gets the cuda capability of a device.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – device for which to return the device capability. This function is a no-op if this argument is a negative integer. It uses the current device, given by[`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).Returnsthe major and minor cuda capability of the deviceReturn type[tuple](https://docs.python.org/3/library/stdtypes.html#tuple)([int](https://docs.python.org/3/library/functions.html#int), [int](https://docs.python.org/3/library/functions.html#int))

- `torch.cuda.``get_device_name`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#get_device_name)

  Gets the name of a device.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – device for which to return the name. This function is a no-op if this argument is a negative integer. It uses the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).

- `torch.cuda.``init`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#init)

  Initialize PyTorch’s CUDA state. You may need to call this explicitly if you are interacting with PyTorch via its C API, as python bindings for CUDA functionality will not be until this initialization takes place. Ordinary users should not need this, as all of PyTorch’s CUDA methods automatically initialize CUDA state on-demand.Does nothing if the CUDA state is already initialized.

- `torch.cuda.``ipc_collect`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#ipc_collect)

  Force collects GPU memory after it has been released by CUDA IPC.NOTEChecks if any sent CUDA tensors could be cleaned from the memory. Force closes shared memory file used for reference counting if there is no active counters. Useful when the producer process stopped actively sending tensors and want to release unused memory.

- `torch.cuda.``is_available`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#is_available)

  Returns a bool indicating if CUDA is currently available.

- `torch.cuda.``max_memory_allocated`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#max_memory_allocated)

  Returns the maximum GPU memory occupied by tensors in bytes for a given device.By default, this returns the peak allocated memory since the beginning of this program. [`reset_max_memory_allocated()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.reset_max_memory_allocated) can be used to reset the starting point in tracking this metric. For example, these two functions can measure the peak allocated memory usage of each iteration in a training loop.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns statistic for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).NOTESee [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

- `torch.cuda.``max_memory_cached`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#max_memory_cached)

  Returns the maximum GPU memory managed by the caching allocator in bytes for a given device.By default, this returns the peak cached memory since the beginning of this program. [`reset_max_memory_cached()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.reset_max_memory_cached) can be used to reset the starting point in tracking this metric. For example, these two functions can measure the peak cached memory amount of each iteration in a training loop.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns statistic for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).NOTESee [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

- `torch.cuda.``memory_allocated`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#memory_allocated)

  Returns the current GPU memory occupied by tensors in bytes for a given device.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns statistic for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).NOTEThis is likely less than the amount shown in nvidia-smi since some unused memory can be held by the caching allocator and some context needs to be created on GPU. See [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

- `torch.cuda.``memory_cached`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#memory_cached)

  Returns the current GPU memory managed by the caching allocator in bytes for a given device.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns statistic for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).NOTESee [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

- `torch.cuda.``reset_max_memory_allocated`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#reset_max_memory_allocated)

  Resets the starting point in tracking maximum GPU memory occupied by tensors for a given device.See [`max_memory_allocated()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.max_memory_allocated) for details.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns statistic for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).NOTESee [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

- `torch.cuda.``reset_max_memory_cached`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#reset_max_memory_cached)

  Resets the starting point in tracking maximum GPU memory managed by the caching allocator for a given device.See [`max_memory_cached()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.max_memory_cached) for details.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns statistic for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).NOTESee [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

- `torch.cuda.``set_device`(*device*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#set_device)

  Sets the current device.Usage of this function is discouraged in favor of [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device). In most cases it’s better to use `CUDA_VISIBLE_DEVICES` environmental variable.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)) – selected device. This function is a no-op if this argument is negative.

- `torch.cuda.``stream`(*stream*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#stream)

  Context-manager that selects a given stream.All CUDA kernels queued within its context will be enqueued on a selected stream.Parameters**stream** ([*Stream*](https://pytorch.org/docs/stable/cuda.html#torch.cuda.Stream)) – selected stream. This manager is a no-op if it’s `None`.NOTEStreams are per-device. If the selected stream is not on the current device, this function will also change the current device to match the stream.

- `torch.cuda.``synchronize`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#synchronize)

  Waits for all kernels in all streams on a CUDA device to complete.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – device for which to synchronize. It uses the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).

## Random Number Generator

- `torch.cuda.``get_rng_state`(*device=device(type='cuda')*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/random.html#get_rng_state)

  Returns the random number generator state of the current GPU as a ByteTensor.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – The device to return the RNG state of. Default: `torch.device('cuda')` (i.e., the current CUDA device).WARNINGThis function eagerly initializes CUDA.

- `torch.cuda.``get_rng_state_all`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/random.html#get_rng_state_all)

  Returns a tuple of ByteTensor representing the random number states of all devices.

- `torch.cuda.``set_rng_state`(*new_state*, *device=device(type='cuda')*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/random.html#set_rng_state)

  Sets the random number generator state of the current GPU.Parameters**new_state** ([*torch.ByteTensor*](https://pytorch.org/docs/stable/tensors.html#torch.ByteTensor)) – The desired state**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – The device to set the RNG state. Default: `torch.device('cuda')` (i.e., the current CUDA device).

- `torch.cuda.``set_rng_state_all`(*new_states*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/random.html#set_rng_state_all)

  Sets the random number generator state of all devices.Parameters**new_state** (*tuple of torch.ByteTensor*) – The desired state for each device

- `torch.cuda.``manual_seed`(*seed*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/random.html#manual_seed)

  Sets the seed for generating random numbers for the current GPU. It’s safe to call this function if CUDA is not available; in that case, it is silently ignored.Parameters**seed** ([*int*](https://docs.python.org/3/library/functions.html#int)) – The desired seed.WARNINGIf you are working with a multi-GPU model, this function is insufficient to get determinism. To seed all GPUs, use [`manual_seed_all()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.manual_seed_all).

- `torch.cuda.``manual_seed_all`(*seed*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/random.html#manual_seed_all)

  Sets the seed for generating random numbers on all GPUs. It’s safe to call this function if CUDA is not available; in that case, it is silently ignored.Parameters**seed** ([*int*](https://docs.python.org/3/library/functions.html#int)) – The desired seed.

- `torch.cuda.``seed`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/random.html#seed)

  Sets the seed for generating random numbers to a random number for the current GPU. It’s safe to call this function if CUDA is not available; in that case, it is silently ignored.WARNINGIf you are working with a multi-GPU model, this function will only initialize the seed on one GPU. To initialize all GPUs, use [`seed_all()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.seed_all).

- `torch.cuda.``seed_all`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/random.html#seed_all)

  Sets the seed for generating random numbers to a random number on all GPUs. It’s safe to call this function if CUDA is not available; in that case, it is silently ignored.

- `torch.cuda.``initial_seed`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/random.html#initial_seed)

  Returns the current random seed of the current GPU.WARNINGThis function eagerly initializes CUDA.

## Communication collectives

- `torch.cuda.comm.``broadcast`(*tensor*, *devices*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/comm.html#broadcast)

  Broadcasts a tensor to a number of GPUs.Parameters**tensor** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – tensor to broadcast.**devices** (*Iterable*) – an iterable of devices among which to broadcast. Note that it should be like (src, dst1, dst2, …), the first element of which is the source device to broadcast from.ReturnsA tuple containing copies of the `tensor`, placed on devices corresponding to indices from `devices`.

- `torch.cuda.comm.``broadcast_coalesced`(*tensors*, *devices*, *buffer_size=10485760*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/comm.html#broadcast_coalesced)

  Broadcasts a sequence tensors to the specified GPUs. Small tensors are first coalesced into a buffer to reduce the number of synchronizations.Parameters**tensors** (*sequence*) – tensors to broadcast.**devices** (*Iterable*) – an iterable of devices among which to broadcast. Note that it should be like (src, dst1, dst2, …), the first element of which is the source device to broadcast from.**buffer_size** ([*int*](https://docs.python.org/3/library/functions.html#int)) – maximum size of the buffer used for coalescingReturnsA tuple containing copies of the `tensor`, placed on devices corresponding to indices from `devices`.

- `torch.cuda.comm.``reduce_add`(*inputs*, *destination=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/comm.html#reduce_add)

  Sums tensors from multiple GPUs.All inputs should have matching shapes.Parameters**inputs** (*Iterable**[*[*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*]*) – an iterable of tensors to add.**destination** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – a device on which the output will be placed (default: current device).ReturnsA tensor containing an elementwise sum of all inputs, placed on the `destination` device.

- `torch.cuda.comm.``scatter`(*tensor*, *devices*, *chunk_sizes=None*, *dim=0*, *streams=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/comm.html#scatter)

  Scatters tensor across multiple GPUs.Parameters**tensor** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – tensor to scatter.**devices** (*Iterable**[*[*int*](https://docs.python.org/3/library/functions.html#int)*]*) – iterable of ints, specifying among which devices the tensor should be scattered.**chunk_sizes** (*Iterable**[*[*int*](https://docs.python.org/3/library/functions.html#int)*]**,* *optional*) – sizes of chunks to be placed on each device. It should match `devices` in length and sum to `tensor.size(dim)`. If not specified, the tensor will be divided into equal chunks.**dim** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – A dimension along which to chunk the tensor.ReturnsA tuple containing chunks of the `tensor`, spread across given `devices`.

- `torch.cuda.comm.``gather`(*tensors*, *dim=0*, *destination=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/comm.html#gather)

  Gathers tensors from multiple GPUs.Tensor sizes in all dimension different than `dim` have to match.Parameters**tensors** (*Iterable**[*[*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*]*) – iterable of tensors to gather.**dim** ([*int*](https://docs.python.org/3/library/functions.html#int)) – a dimension along which the tensors will be concatenated.**destination** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – output device (-1 means CPU, default: current device)ReturnsA tensor located on `destination` device, that is a result of concatenating `tensors` along `dim`.

## Streams and events

- *CLASS*`torch.cuda.``Stream`[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Stream)

  Wrapper around a CUDA stream.A CUDA stream is a linear sequence of execution that belongs to a specific device, independent from other streams. See [CUDA semantics](https://pytorch.org/docs/stable/notes/cuda.html#cuda-semantics) for details.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – a device on which to allocate the stream. If [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default) or a negative integer, this will use the current device.**priority** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – priority of the stream. Lower numbers represent higher priorities.`query`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Stream.query)Checks if all the work submitted has been completed.ReturnsA boolean indicating if all kernels in this stream are completed.`record_event`(*event=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Stream.record_event)Records an event.Parameters**event** ([*Event*](https://pytorch.org/docs/stable/cuda.html#torch.cuda.Event)*,* *optional*) – event to record. If not given, a new one will be allocated.ReturnsRecorded event.`synchronize`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Stream.synchronize)Wait for all the kernels in this stream to complete.NOTEThis is a wrapper around `cudaStreamSynchronize()`: see [`CUDA documentation`_](https://pytorch.org/docs/stable/cuda.html#id4)for more info.`wait_event`(*event*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Stream.wait_event)Makes all future work submitted to the stream wait for an event.Parameters**event** ([*Event*](https://pytorch.org/docs/stable/cuda.html#torch.cuda.Event)) – an event to wait for.NOTEThis is a wrapper around `cudaStreamWaitEvent()`: see [`CUDA documentation`_](https://pytorch.org/docs/stable/cuda.html#id6) for more info.This function returns without waiting for `event`: only future operations are affected.`wait_stream`(*stream*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Stream.wait_stream)Synchronizes with another stream.All future work submitted to this stream will wait until all kernels submitted to a given stream at the time of call complete.Parameters**stream** ([*Stream*](https://pytorch.org/docs/stable/cuda.html#torch.cuda.Stream)) – a stream to synchronize.NOTEThis function returns without waiting for currently enqueued kernels in [`stream`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.stream): only future operations are affected.

- *CLASS*`torch.cuda.``Event`[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Event)

  Wrapper around a CUDA event.CUDA events are synchronization markers that can be used to monitor the device’s progress, to accurately measure timing, and to synchronize CUDA streams.The underlying CUDA events are lazily initialized when the event is first recorded or exported to another process. After creation, only streams on the same device may record the event. However, streams on any device can wait on the event.Parameters**enable_timing** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – indicates if the event should measure time (default: `False`)**blocking** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – if `True`, [`wait()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.Event.wait) will be blocking (default: `False`)**interprocess** () – if `True`, the event can be shared between processes (default: `False`)`elapsed_time`(*end_event*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Event.elapsed_time)Returns the time elapsed in milliseconds after the event was recorded and before the end_event was recorded.*CLASSMETHOD* `from_ipc_handle`(*device*, *handle*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Event.from_ipc_handle)Reconstruct an event from an IPC handle on the given device.`ipc_handle`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Event.ipc_handle)Returns an IPC handle of this event. If not recorded yet, the event will use the current device.`query`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Event.query)Checks if all work currently captured by event has completed.ReturnsA boolean indicating if all work currently captured by event has completed.`record`(*stream=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Event.record)Records the event in a given stream.Uses `torch.cuda.current_stream()` if no stream is specified. The stream’s device must match the event’s device.`synchronize`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Event.synchronize)Waits for the event to complete.Waits until the completion of all work currently captured in this event. This prevents the CPU thread from proceeding until the event completes.NOTEThis is a wrapper around `cudaEventSynchronize()`: see [`CUDA documentation`_](https://pytorch.org/docs/stable/cuda.html#id8) for more info.`wait`(*stream=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/streams.html#Event.wait)Makes all future work submitted to the given stream wait for this event.Use `torch.cuda.current_stream()` if no stream is specified.

## Memory management

- `torch.cuda.``empty_cache`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#empty_cache)

  Releases all unoccupied cached memory currently held by the caching allocator so that those can be used in other GPU application and visible in nvidia-smi.NOTE[`empty_cache()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.empty_cache) doesn’t increase the amount of GPU memory available for PyTorch. See [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

- `torch.cuda.``memory_allocated`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#memory_allocated)

  Returns the current GPU memory occupied by tensors in bytes for a given device.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns statistic for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).NOTEThis is likely less than the amount shown in nvidia-smi since some unused memory can be held by the caching allocator and some context needs to be created on GPU. See [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

- `torch.cuda.``max_memory_allocated`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#max_memory_allocated)

  Returns the maximum GPU memory occupied by tensors in bytes for a given device.By default, this returns the peak allocated memory since the beginning of this program. [`reset_max_memory_allocated()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.reset_max_memory_allocated) can be used to reset the starting point in tracking this metric. For example, these two functions can measure the peak allocated memory usage of each iteration in a training loop.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns statistic for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).NOTESee [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

- `torch.cuda.``reset_max_memory_allocated`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#reset_max_memory_allocated)

  Resets the starting point in tracking maximum GPU memory occupied by tensors for a given device.See [`max_memory_allocated()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.max_memory_allocated) for details.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns statistic for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).NOTESee [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

- `torch.cuda.``memory_cached`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#memory_cached)

  Returns the current GPU memory managed by the caching allocator in bytes for a given device.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns statistic for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).NOTESee [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

- `torch.cuda.``max_memory_cached`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#max_memory_cached)

  Returns the maximum GPU memory managed by the caching allocator in bytes for a given device.By default, this returns the peak cached memory since the beginning of this program. [`reset_max_memory_cached()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.reset_max_memory_cached) can be used to reset the starting point in tracking this metric. For example, these two functions can measure the peak cached memory amount of each iteration in a training loop.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns statistic for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).NOTESee [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

- `torch.cuda.``reset_max_memory_cached`(*device=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda.html#reset_max_memory_cached)

  Resets the starting point in tracking maximum GPU memory managed by the caching allocator for a given device.See [`max_memory_cached()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.max_memory_cached) for details.Parameters**device** ([*torch.device*](https://pytorch.org/docs/stable/tensor_attributes.html#torch.torch.device) *or* [*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – selected device. Returns statistic for the current device, given by [`current_device()`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.current_device), if [`device`](https://pytorch.org/docs/stable/cuda.html#torch.cuda.device) is `None` (default).NOTESee [Memory management](https://pytorch.org/docs/stable/notes/cuda.html#cuda-memory-management) for more details about GPU memory management.

## NVIDIA Tools Extension (NVTX)

- `torch.cuda.nvtx.``mark`(*msg*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/nvtx.html#mark)

  Describe an instantaneous event that occurred at some point.Parameters**msg** (*string*) – ASCII message to associate with the event.

- `torch.cuda.nvtx.``range_push`(*msg*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/nvtx.html#range_push)

  Pushes a range onto a stack of nested range span. Returns zero-based depth of the range that is started.Parameters**msg** (*string*) – ASCII message to associate with range

- `torch.cuda.nvtx.``range_pop`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/cuda/nvtx.html#range_pop)

  Pops a range off of a stack of nested range spans. Returns the zero-based depth of the range that is ended.
