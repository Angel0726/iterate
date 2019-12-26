---
title: 13 torch.distributed
toc: true
date: 2019-06-29
---
# DISTRIBUTED COMMUNICATION PACKAGE - TORCH.DISTRIBUTED

## Backends

`torch.distributed` supports three backends, each with different capabilities. The table below shows which functions are available for use with CPU / CUDA tensors. MPI supports CUDA only if the implementation used to build PyTorch supports it.

| Backend    | `gloo` | `mpi` | `nccl` |      |      |      |
| ---------- | ------ | ----- | ------ | ---- | ---- | ---- |
| Device     | CPU    | GPU   | CPU    | GPU  | CPU  | GPU  |
| send       | ✓      | ✘     | ✓      | ?    | ✘    | ✘    |
| recv       | ✓      | ✘     | ✓      | ?    | ✘    | ✘    |
| broadcast  | ✓      | ✓     | ✓      | ?    | ✘    | ✓    |
| all_reduce | ✓      | ✓     | ✓      | ?    | ✘    | ✓    |
| reduce     | ✓      | ✘     | ✓      | ?    | ✘    | ✓    |
| all_gather | ✓      | ✘     | ✓      | ?    | ✘    | ✓    |
| gather     | ✓      | ✘     | ✓      | ?    | ✘    | ✘    |
| scatter    | ✓      | ✘     | ✓      | ?    | ✘    | ✘    |
| barrier    | ✓      | ✘     | ✓      | ?    | ✘    | ✓    |

### Backends that come with PyTorch

PyTorch distributed currently only supports Linux. By default, the Gloo and NCCL backends are built and included in PyTorch distributed (NCCL only when building with CUDA). MPI is an optional backend that can only be included if you build PyTorch from source. (e.g. building PyTorch on a host that has MPI installed.)

### Which backend to use?

In the past, we were often asked: “which backend should I use?”.

- Rule of thumb
  - Use the NCCL backend for distributed **GPU** training
  - Use the Gloo backend for distributed **CPU** training.
- GPU hosts with InfiniBand interconnect
  - Use NCCL, since it’s the only backend that currently supports InfiniBand and GPUDirect.
- GPU hosts with Ethernet interconnect
  - Use NCCL, since it currently provides the best distributed GPU training performance, especially for multiprocess single-node or multi-node distributed training. If you encounter any problem with NCCL, use Gloo as the fallback option. (Note that Gloo currently runs slower than NCCL for GPUs.)
- CPU hosts with InfiniBand interconnect
  - If your InfiniBand has enabled IP over IB, use Gloo, otherwise, use MPI instead. We are planning on adding InfiniBand support for Gloo in the upcoming releases.
- CPU hosts with Ethernet interconnect
  - Use Gloo, unless you have specific reasons to use MPI.

### Common environment variables

#### Choosing the network interface to use

By default, both NCCL and Gloo backends will try to find the network interface to use for communication. However, this is not always guaranteed to be successful from our experiences. Therefore, if you encounter any problem on either backend not being able to find the correct network interface. You can try to set the following environment variables (each one applicable to its respective backend):

- **NCCL_SOCKET_IFNAME**, for example `export NCCL_SOCKET_IFNAME=eth0`
- **GLOO_SOCKET_IFNAME**, for example `export GLOO_SOCKET_IFNAME=eth0`

#### Other NCCL environment variables

NCCL has also provided a number of environment variables for fine-tuning purposes.

Commonly used ones include the following for debugging purposes:

- `export NCCL_DEBUG=INFO`
- `export NCCL_DEBUG_SUBSYS=ALL`

For the full list of NCCL environment variables, please refer to [NVIDIA NCCL’s official documentation](https://docs.nvidia.com/deeplearning/sdk/nccl-developer-guide/docs/env.html)



## Basics

The torch.distributed package provides PyTorch support and communication primitives for multiprocess parallelism across several computation nodes running on one or more machines. The class [`torch.nn.parallel.DistributedDataParallel()`](https://pytorch.org/docs/stable/nn.html#torch.nn.parallel.DistributedDataParallel) builds on this functionality to provide synchronous distributed training as a wrapper around any PyTorch model. This differs from the kinds of parallelism provided by [Multiprocessing package - torch.multiprocessing](https://pytorch.org/docs/stable/multiprocessing.html) and [`torch.nn.DataParallel()`](https://pytorch.org/docs/stable/nn.html#torch.nn.DataParallel) in that it supports multiple network-connected machines and in that the user must explicitly launch a separate copy of the main training script for each process.

In the single-machine synchronous case, torch.distributed or the[`torch.nn.parallel.DistributedDataParallel()`](https://pytorch.org/docs/stable/nn.html#torch.nn.parallel.DistributedDataParallel) wrapper may still have advantages over other approaches to data-parallelism, including [`torch.nn.DataParallel()`](https://pytorch.org/docs/stable/nn.html#torch.nn.DataParallel):

- Each process maintains its own optimizer and performs a complete optimization step with each iteration. While this may appear redundant, since the gradients have already been gathered together and averaged across processes and are thus the same for every process, this means that no parameter broadcast step is needed, reducing time spent transferring tensors between nodes.
- Each process contains an independent python interpreter, eliminating the extra interpreter overhead and “GIL-thrashing” that comes from driving several execution threads, model replicas, or GPUs from a single python process. This is especially important for models that make heavy use of the python runtime, including models with recurrent layers or many small components.

## Initialization

The package needs to be initialized using the [`torch.distributed.init_process_group()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group) function before calling any other methods. This blocks until all processes have joined.

- `torch.distributed.``init_process_group`(*backend*, *init_method=None*, *timeout=datetime.timedelta(0*, *1800)*, *world_size=-1*, *rank=-1*, *store=None*, *group_name=''*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#init_process_group)

  Initializes the default distributed process group, and this will also initialize the distributed package.There are 2 main ways to initialize a process group:Specify `store`, `rank`, and `world_size` explicitly.Specify `init_method` (a URL string) which indicates where/how to discover peers. Optionally specify `rank` and `world_size`, or encode all required parameters in the URL and omit them.If neither is specified, `init_method` is assumed to be “env://”.Parameters**backend** ([*str*](https://docs.python.org/3/library/stdtypes.html#str) *or* [*Backend*](https://pytorch.org/docs/stable/distributed.html#torch.distributed.Backend)) – The backend to use. Depending on build-time configurations, valid values include `mpi`, `gloo`, and `nccl`. This field should be given as a lowercase string (e.g., `"gloo"`), which can also be accessed via [`Backend`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.Backend) attributes (e.g., `Backend.GLOO`). If using multiple processes per machine with `nccl` backend, each process must have exclusive access to every GPU it uses, as sharing GPUs between processes can result in deadlocks.**init_method** ([*str*](https://docs.python.org/3/library/stdtypes.html#str)*,* *optional*) – URL specifying how to initialize the process group. Default is “env://” if no `init_method` or `store` is specified. Mutually exclusive with `store`.**world_size** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – Number of processes participating in the job. Required if `store` is specified.**rank** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – Rank of the current process. Required if `store` is specified.**store** (*Store**,* *optional*) – Key/value store accessible to all workers, used to exchange connection/address information. Mutually exclusive with `init_method`.**timeout** (*timedelta**,* *optional*) – Timeout for operations executed against the process group. Default value equals 30 minutes. This is only applicable for the `gloo` backend.**group_name** ([*str*](https://docs.python.org/3/library/stdtypes.html#str)*,* *optional**,* *deprecated*) – Group name.To enable `backend == Backend.MPI`, PyTorch needs to built from source on a system that supports MPI. The same applies to NCCL as well.

- *CLASS*`torch.distributed.``Backend`[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#Backend)

  An enum-like class of available backends: GLOO, NCCL, and MPI.The values of this class are lowercase strings, e.g., `"gloo"`. They can be accessed as attributes, e.g., `Backend.NCCL`.This class can be directly called to parse the string, e.g., `Backend(backend_str)` will check if `backend_str` is valid, and return the parsed lowercase string if so. It also accepts uppercase strings, e.g., `Backend("GLOO")` returns `"gloo"`.NOTEThe entry `Backend.UNDEFINED` is present but only used as initial value of some fields. Users should neither use it directly nor assume its existence.

- `torch.distributed.``get_backend`(*group=<object object>*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#get_backend)

  Returns the backend of the given process group.Parameters**group** (*ProcessGroup**,* *optional*) – The process group to work on. The default is the general main process group. If another specific group is specified, the calling process must be part of `group`.ReturnsThe backend of the given process group as a lower case string.

- `torch.distributed.``get_rank`(*group=<object object>*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#get_rank)

  Returns the rank of current process groupRank is a unique identifier assigned to each process within a distributed process group. They are always consecutive integers ranging from 0 to `world_size`.Parameters**group** (*ProcessGroup**,* *optional*) – The process group to work onReturnsThe rank of the process group -1, if not part of the group

- `torch.distributed.``get_world_size`(*group=<object object>*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#get_world_size)

  Returns the number of processes in the current process groupParameters**group** (*ProcessGroup**,* *optional*) – The process group to work onReturnsThe world size of the process group -1, if not part of the group

- `torch.distributed.``is_initialized`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#is_initialized)

  Checking if the default process group has been initialized

- `torch.distributed.``is_mpi_available`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#is_mpi_available)

  Checks if the MPI backend is available.

- `torch.distributed.``is_nccl_available`()[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#is_nccl_available)

  Checks if the NCCL backend is available.

------

Currently three initialization methods are supported:

### TCP initialization

There are two ways to initialize using TCP, both requiring a network address reachable from all processes and a desired `world_size`. The first way requires specifying an address that belongs to the rank 0 process. This initialization method requires that all processes have manually specified ranks.

Note that multicast address is not supported anymore in the latest distributed package. `group_name` is deprecated as well.

```
import torch.distributed as dist

# Use address of one of the machines
dist.init_process_group(backend, init_method='tcp://10.1.1.20:23456',
                        rank=args.rank, world_size=4)
```

### Shared file-system initialization

Another initialization method makes use of a file system that is shared and visible from all machines in a group, along with a desired `world_size`. The URL should start with `file://` and contain a path to a non-existent file (in an existing directory) on a shared file system. File-system initialization will automatically create that file if it doesn’t exist, but will not delete the file. Therefore, it is your responsibility to make sure that the file is cleaned up before the next [`init_process_group()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group) call on the same file path/name.

Note that automatic rank assignment is not supported anymore in the latest distributed package and `group_name` is deprecated as well.

WARNING

This method assumes that the file system supports locking using `fcntl` - most local systems and NFS support it.

WARNING

This method will always create the file and try its best to clean up and remove the file at the end of the program. In other words, each initialization with the file init method will need a brand new empty file in order for the initialization to succeed. If the same file used by the previous initialization (which happens not to get cleaned up) is used again, this is unexpected behavior and can often cause deadlocks and failures. Therefore, even though this method will try its best to clean up the file, if the auto-delete happens to be unsuccessful, it is your responsibility to ensure that the file is removed at the end of the training to prevent the same file to be reused again during the next time. This is especially important if you plan to call [`init_process_group()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group) multiple times on the same file name. In other words, if the file is not removed/cleaned up and you call [`init_process_group()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group) again on that file, failures are expected. The rule of thumb here is that, make sure that the file is non-existent or empty everytime [`init_process_group()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group) is called.

```
import torch.distributed as dist

# rank should always be specified
dist.init_process_group(backend, init_method='file:///mnt/nfs/sharedfile',
                        world_size=4, rank=args.rank)
```

### Environment variable initialization

This method will read the configuration from environment variables, allowing one to fully customize how the information is obtained. The variables to be set are:

- `MASTER_PORT` - required; has to be a free port on machine with rank 0
- `MASTER_ADDR` - required (except for rank 0); address of rank 0 node
- `WORLD_SIZE` - required; can be set either here, or in a call to init function
- `RANK` - required; can be set either here, or in a call to init function

The machine with rank 0 will be used to set up all connections.

This is the default method, meaning that `init_method` does not have to be specified (or can be `env://`).

## Groups

By default collectives operate on the default group (also called the world) and require all processes to enter the distributed function call. However, some workloads can benefit from more fine-grained communication. This is where distributed groups come into play. [`new_group()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.new_group) function can be used to create new groups, with arbitrary subsets of all processes. It returns an opaque group handle that can be given as a `group`argument to all collectives (collectives are distributed functions to exchange information in certain well-known programming patterns).

Currently torch.distributed does not support creating groups with different backends. In other words, each group being created will use the same backend as you specified in [`init_process_group()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.init_process_group).

- `torch.distributed.``new_group`(*ranks=None*, *timeout=datetime.timedelta(0*, *1800)*, *backend=None*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#new_group)

  Creates a new distributed group.This function requires that all processes in the main group (i.e. all processes that are part of the distributed job) enter this function, even if they are not going to be members of the group. Additionally, groups should be created in the same order in all processes.Parameters**ranks** ([*list*](https://docs.python.org/3/library/stdtypes.html#list)*[*[*int*](https://docs.python.org/3/library/functions.html#int)*]*) – List of ranks of group members.**timeout** (*timedelta**,* *optional*) – Timeout for operations executed against the process group. Default value equals 30 minutes. This is only applicable for the `gloo` backend.**backend** ([*str*](https://docs.python.org/3/library/stdtypes.html#str) *or* [*Backend*](https://pytorch.org/docs/stable/distributed.html#torch.distributed.Backend)*,* *optional*) – The backend to use. Depending on build-time configurations, valid values are `gloo` and `nccl`. By default uses the same backend as the global group. This field should be given as a lowercase string (e.g., `"gloo"`), which can also be accessed via [`Backend`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.Backend) attributes (e.g., `Backend.GLOO`).ReturnsA handle of distributed group that can be given to collective calls.

## Point-to-point communication

- `torch.distributed.``send`(*tensor*, *dst*, *group=<object object>*, *tag=0*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#send)

  Sends a tensor synchronously.Parameters**tensor** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – Tensor to send.**dst** ([*int*](https://docs.python.org/3/library/functions.html#int)) – Destination rank.**group** (*ProcessGroup**,* *optional*) – The process group to work on**tag** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – Tag to match send with remote recv

- `torch.distributed.``recv`(*tensor*, *src=None*, *group=<object object>*, *tag=0*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#recv)

  Receives a tensor synchronously.Parameters**tensor** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – Tensor to fill with received data.**src** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – Source rank. Will receive from any process if unspecified.**group** (*ProcessGroup**,* *optional*) – The process group to work on**tag** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – Tag to match recv with remote sendReturnsSender rank -1, if not part of the group

[`isend()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.isend) and [`irecv()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.irecv) return distributed request objects when used. In general, the type of this object is unspecified as they should never be created manually, but they are guaranteed to support two methods:

- `is_completed()` - returns True if the operation has finished
- `wait()` - will block the process until the operation is finished. `is_completed()` is guaranteed to return True once it returns.

- `torch.distributed.``isend`(*tensor*, *dst*, *group=<object object>*, *tag=0*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#isend)

  Sends a tensor asynchronously.Parameters**tensor** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – Tensor to send.**dst** ([*int*](https://docs.python.org/3/library/functions.html#int)) – Destination rank.**group** (*ProcessGroup**,* *optional*) – The process group to work on**tag** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – Tag to match send with remote recvReturnsA distributed request object. None, if not part of the group

- `torch.distributed.``irecv`(*tensor*, *src*, *group=<object object>*, *tag=0*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#irecv)

  Receives a tensor asynchronously.Parameters**tensor** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – Tensor to fill with received data.**src** ([*int*](https://docs.python.org/3/library/functions.html#int)) – Source rank.**group** (*ProcessGroup**,* *optional*) – The process group to work on**tag** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – Tag to match recv with remote sendReturnsA distributed request object. None, if not part of the group

## Synchronous and asynchronous collective operations

Every collective operation function supports the following two kinds of operations:

synchronous operation - the default mode, when `async_op` is set to False. when the function returns, it is guaranteed that the collective operation is performed (not necessarily completed if it’s a CUDA op since all CUDA ops are asynchronous), and any further function calls depending on the data of the collective operation can be called. In the synchronous mode, the collective function does not return anything

asynchronous operation - when `async_op` is set to True. The collective operation function returns a distributed request object. In general, you don’t need to create it manually and it is guaranteed to support two methods:

- `is_completed()` - returns True if the operation has finished
- `wait()` - will block the process until the operation is finished.

## Collective functions

- `torch.distributed.``broadcast`(*tensor*, *src*, *group=<object object>*, *async_op=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#broadcast)

  Broadcasts the tensor to the whole group.`tensor` must have the same number of elements in all processes participating in the collective.Parameters**tensor** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – Data to be sent if `src` is the rank of current process, and tensor to be used to save received data otherwise.**src** ([*int*](https://docs.python.org/3/library/functions.html#int)) – Source rank.**group** (*ProcessGroup**,* *optional*) – The process group to work on**async_op** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Whether this op should be an async opReturnsAsync work handle, if async_op is set to True. None, if not async_op or if not part of the group

- `torch.distributed.``all_reduce`(*tensor*, *op=ReduceOp.SUM*, *group=<object object>*, *async_op=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#all_reduce)

  Reduces the tensor data across all machines in such a way that all get the final result.After the call `tensor` is going to be bitwise identical in all processes.Parameters**tensor** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – Input and output of the collective. The function operates in-place.**op** (*optional*) – One of the values from `torch.distributed.ReduceOp` enum. Specifies an operation used for element-wise reductions.**group** (*ProcessGroup**,* *optional*) – The process group to work on**async_op** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Whether this op should be an async opReturnsAsync work handle, if async_op is set to True. None, if not async_op or if not part of the group

- `torch.distributed.``reduce`(*tensor*, *dst*, *op=ReduceOp.SUM*, *group=<object object>*, *async_op=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#reduce)

  Reduces the tensor data across all machines.Only the process with rank `dst` is going to receive the final result.Parameters**tensor** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – Input and output of the collective. The function operates in-place.**dst** ([*int*](https://docs.python.org/3/library/functions.html#int)) – Destination rank**op** (*optional*) – One of the values from `torch.distributed.ReduceOp` enum. Specifies an operation used for element-wise reductions.**group** (*ProcessGroup**,* *optional*) – The process group to work on**async_op** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Whether this op should be an async opReturnsAsync work handle, if async_op is set to True. None, if not async_op or if not part of the group

- `torch.distributed.``all_gather`(*tensor_list*, *tensor*, *group=<object object>*, *async_op=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#all_gather)

  Gathers tensors from the whole group in a list.Parameters**tensor_list** ([*list*](https://docs.python.org/3/library/stdtypes.html#list)*[*[*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*]*) – Output list. It should contain correctly-sized tensors to be used for output of the collective.**tensor** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – Tensor to be broadcast from current process.**group** (*ProcessGroup**,* *optional*) – The process group to work on**async_op** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Whether this op should be an async opReturnsAsync work handle, if async_op is set to True. None, if not async_op or if not part of the group

- `torch.distributed.``gather`(*tensor*, *gather_list*, *dst*, *group=<object object>*, *async_op=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#gather)

  Gathers a list of tensors in a single process.Parameters**tensor** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – Input tensor.**gather_list** ([*list*](https://docs.python.org/3/library/stdtypes.html#list)*[*[*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*]*) – List of appropriately-sized tensors to use for received data. Required only in the receiving process.**dst** ([*int*](https://docs.python.org/3/library/functions.html#int)) – Destination rank. Required in all processes except the one that is receiveing the data.**group** (*ProcessGroup**,* *optional*) – The process group to work on**async_op** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Whether this op should be an async opReturnsAsync work handle, if async_op is set to True. None, if not async_op or if not part of the group

- `torch.distributed.``scatter`(*tensor*, *scatter_list*, *src*, *group=<object object>*, *async_op=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#scatter)

  Scatters a list of tensors to all processes in a group.Each process will receive exactly one tensor and store its data in the `tensor` argument.Parameters**tensor** ([*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)) – Output tensor.**scatter_list** ([*list*](https://docs.python.org/3/library/stdtypes.html#list)*[*[*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*]*) – List of tensors to scatter. Required only in the process that is sending the data.**src** ([*int*](https://docs.python.org/3/library/functions.html#int)) – Source rank. Required in all processes except the one that is sending the data.**group** (*ProcessGroup**,* *optional*) – The process group to work on**async_op** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Whether this op should be an async opReturnsAsync work handle, if async_op is set to True. None, if not async_op or if not part of the group

- `torch.distributed.``barrier`(*group=<object object>*, *async_op=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#barrier)

  Synchronizes all processes.This collective blocks processes until the whole group enters this function, if async_op is False, or if async work handle is called on wait().Parameters**group** (*ProcessGroup**,* *optional*) – The process group to work on**async_op** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Whether this op should be an async opReturnsAsync work handle, if async_op is set to True. None, if not async_op or if not part of the group

- *CLASS*`torch.distributed.``ReduceOp`

  An enum-like class of available reduce operations: `SUM`, `PRODUCT`, `MIN`, and `MAX`.The values of this class can be accessed as attributes, e.g., `ReduceOp.SUM`. They are used in specifying strategies for reduction collectives, e.g., [`reduce()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.reduce), [`all_reduce_multigpu()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.all_reduce_multigpu), etc.Members:SUMPRODUCTMINMAX

- *CLASS*`torch.distributed.``reduce_op`[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#reduce_op)

  Deprecated enum-like class for reduction operations: `SUM`, `PRODUCT`, `MIN`, and `MAX`.[`ReduceOp`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.ReduceOp) is recommended to use instead.

## Multi-GPU collective functions

If you have more than one GPU on each node, when using the NCCL and Gloo backend,[`broadcast_multigpu()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.broadcast_multigpu) [`all_reduce_multigpu()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.all_reduce_multigpu) [`reduce_multigpu()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.reduce_multigpu) and [`all_gather_multigpu()`](https://pytorch.org/docs/stable/distributed.html#torch.distributed.all_gather_multigpu)support distributed collective operations among multiple GPUs within each node. These functions can potentially improve the overall distributed training performance and be easily used by passing a list of tensors. Each Tensor in the passed tensor list needs to be on a separate GPU device of the host where the function is called. Note that the length of the tensor list needs to be identical among all the distributed processes. Also note that currently the multi-GPU collective functions are only supported by the NCCL backend.

For example, if the system we use for distributed training has 2 nodes, each of which has 8 GPUs. On each of the 16 GPUs, there is a tensor that we would like to all-reduce. The following code can serve as a reference:

Code running on Node 0

```
import torch
import torch.distributed as dist

dist.init_process_group(backend="nccl",
                        init_method="file:///distributed_test",
                        world_size=2,
                        rank=0)
tensor_list = []
for dev_idx in range(torch.cuda.device_count()):
    tensor_list.append(torch.FloatTensor([1]).cuda(dev_idx))

dist.all_reduce_multigpu(tensor_list)
```

Code running on Node 1

```
import torch
import torch.distributed as dist

dist.init_process_group(backend="nccl",
                        init_method="file:///distributed_test",
                        world_size=2,
                        rank=1)
tensor_list = []
for dev_idx in range(torch.cuda.device_count()):
    tensor_list.append(torch.FloatTensor([1]).cuda(dev_idx))

dist.all_reduce_multigpu(tensor_list)
```

After the call, all 16 tensors on the two nodes will have the all-reduced value of 16

- `torch.distributed.``broadcast_multigpu`(*tensor_list*, *src*, *group=<object object>*, *async_op=False*, *src_tensor=0*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#broadcast_multigpu)

  Broadcasts the tensor to the whole group with multiple GPU tensors per node.`tensor` must have the same number of elements in all the GPUs from all processes participating in the collective. each tensor in the list must be on a different GPUOnly nccl and gloo backend are currently supported tensors should only be GPU tensorsParameters**tensor_list** (*List**[*[*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*]*) – Tensors that participate in the collective operation. If `src`is the rank, then the specified `src_tensor` element of `tensor_list`(`tensor_list[src_tensor]`) will be broadcast to all other tensors (on different GPUs) in the src process and all tensors in `tensor_list` of other non-src processes. You also need to make sure that `len(tensor_list)` is the same for all the distributed processes calling this function.**src** ([*int*](https://docs.python.org/3/library/functions.html#int)) – Source rank.**group** (*ProcessGroup**,* *optional*) – The process group to work on**async_op** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Whether this op should be an async op**src_tensor** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – Source tensor rank within `tensor_list`ReturnsAsync work handle, if async_op is set to True. None, if not async_op or if not part of the group

- `torch.distributed.``all_reduce_multigpu`(*tensor_list*, *op=ReduceOp.SUM*, *group=<object object>*, *async_op=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#all_reduce_multigpu)

  Reduces the tensor data across all machines in such a way that all get the final result. This function reduces a number of tensors on every node, while each tensor resides on different GPUs. Therefore, the input tensor in the tensor list needs to be GPU tensors. Also, each tensor in the tensor list needs to reside on a different GPU.After the call, all `tensor` in `tensor_list` is going to be bitwise identical in all processes.Only nccl and gloo backend is currently supported tensors should only be GPU tensorsParameters**list** (*tensor*) – List of input and output tensors of the collective. The function operates in-place and requires that each tensor to be a GPU tensor on different GPUs. You also need to make sure that `len(tensor_list)` is the same for all the distributed processes calling this function.**op** (*optional*) – One of the values from `torch.distributed.ReduceOp` enum. Specifies an operation used for element-wise reductions.**group** (*ProcessGroup**,* *optional*) – The process group to work on**async_op** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Whether this op should be an async opReturnsAsync work handle, if async_op is set to True. None, if not async_op or if not part of the group

- `torch.distributed.``reduce_multigpu`(*tensor_list*, *dst*, *op=ReduceOp.SUM*, *group=<object object>*, *async_op=False*, *dst_tensor=0*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#reduce_multigpu)

  Reduces the tensor data on multiple GPUs across all machines. Each tensor in `tensor_list` should reside on a separate GPUOnly the GPU of `tensor_list[dst_tensor]` on the process with rank `dst` is going to receive the final result.Only nccl backend is currently supported tensors should only be GPU tensorsParameters**tensor_list** (*List**[*[*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*]*) – Input and output GPU tensors of the collective. The function operates in-place. You also need to make sure that `len(tensor_list)` is the same for all the distributed processes calling this function.**dst** ([*int*](https://docs.python.org/3/library/functions.html#int)) – Destination rank**op** (*optional*) – One of the values from `torch.distributed.ReduceOp` enum. Specifies an operation used for element-wise reductions.**group** (*ProcessGroup**,* *optional*) – The process group to work on**async_op** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Whether this op should be an async op**dst_tensor** ([*int*](https://docs.python.org/3/library/functions.html#int)*,* *optional*) – Destination tensor rank within `tensor_list`ReturnsAsync work handle, if async_op is set to True. None, otherwise

- `torch.distributed.``all_gather_multigpu`(*output_tensor_lists*, *input_tensor_list*, *group=<object object>*, *async_op=False*)[[SOURCE\]](https://pytorch.org/docs/stable/_modules/torch/distributed/distributed_c10d.html#all_gather_multigpu)

  Gathers tensors from the whole group in a list. Each tensor in `tensor_list` should reside on a separate GPUOnly nccl backend is currently supported tensors should only be GPU tensorsParameters**output_tensor_lists** (*List**[**List**[*[*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*]**]*) –Output lists. It should contain correctly-sized tensors on each GPU to be used for output of the collective, e.g. `output_tensor_lists[i]` contains the all_gather result that resides on the GPU of `input_tensor_list[i]`.Note that each element of `output_tensor_lists` has the size of `world_size *len(input_tensor_list)`, since the function all gathers the result from every single GPU in the group. To interpret each element of `output_tensor_lists[i]`, note that`input_tensor_list[j]` of rank k will be appear in `output_tensor_lists[i][k *world_size + j]`Also note that `len(output_tensor_lists)`, and the size of each element in `output_tensor_lists` (each element is a list, therefore `len(output_tensor_lists[i])`) need to be the same for all the distributed processes calling this function.**input_tensor_list** (*List**[*[*Tensor*](https://pytorch.org/docs/stable/tensors.html#torch.Tensor)*]*) – List of tensors(on different GPUs) to be broadcast from current process. Note that `len(input_tensor_list)` needs to be the same for all the distributed processes calling this function.**group** (*ProcessGroup**,* *optional*) – The process group to work on**async_op** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – Whether this op should be an async opReturnsAsync work handle, if async_op is set to True. None, if not async_op or if not part of the group

## Launch utility

The torch.distributed package also provides a launch utility in torch.distributed.launch. This helper utility can be used to launch multiple processes per node for distributed training. This utility also supports both python2 and python3.



torch.distributed.launch is a module that spawns up multiple distributed training processes on each of the training nodes.

The utility can be used for single-node distributed training, in which one or more processes per node will be spawned. The utility can be used for either CPU training or GPU training. If the utility is used for GPU training, each distributed process will be operating on a single GPU. This can achieve well-improved single-node training performance. It can also be used in multi-node distributed training, by spawning up multiple processes on each node for well-improved multi-node distributed training performance as well. This will especially be benefitial for systems with multiple Infiniband interfaces that have direct-GPU support, since all of them can be utilized for aggregated communication bandwidth.

In both cases of single-node distributed training or multi-node distributed training, this utility will launch the given number of processes per node (`--nproc_per_node`). If used for GPU training, this number needs to be less or euqal to the number of GPUs on the current system (`nproc_per_node`), and each process will be operating on a single GPU from *GPU 0 to GPU (nproc_per_node - 1)*.

**How to use this module:**

1. Single-Node multi-process distributed training

```
>>> python -m torch.distributed.launch --nproc_per_node=NUM_GPUS_YOU_HAVE
           YOUR_TRAINING_SCRIPT.py (--arg1 --arg2 --arg3 and all other
           arguments of your training script)
```

1. Multi-Node multi-process distributed training: (e.g. two nodes)

Node 1: *(IP: 192.168.1.1, and has a free port: 1234)*

```
>>> python -m torch.distributed.launch --nproc_per_node=NUM_GPUS_YOU_HAVE
           --nnodes=2 --node_rank=0 --master_addr="192.168.1.1"
           --master_port=1234 YOUR_TRAINING_SCRIPT.py (--arg1 --arg2 --arg3
           and all other arguments of your training script)
```

Node 2:

```
>>> python -m torch.distributed.launch --nproc_per_node=NUM_GPUS_YOU_HAVE
           --nnodes=2 --node_rank=1 --master_addr="192.168.1.1"
           --master_port=1234 YOUR_TRAINING_SCRIPT.py (--arg1 --arg2 --arg3
           and all other arguments of your training script)
```

1. To look up what optional arguments this module offers:

```
>>> python -m torch.distributed.launch --help
```

**Important Notices:**

\1. This utilty and multi-process distributed (single-node or multi-node) GPU training currently only achieves the best performance using the NCCL distributed backend. Thus NCCL backend is the recommended backend to use for GPU training.

\2. In your training program, you must parse the command-line argument: `--local_rank=LOCAL_PROCESS_RANK`, which will be provided by this module. If your training program uses GPUs, you should ensure that your code only runs on the GPU device of LOCAL_PROCESS_RANK. This can be done by:

Parsing the local_rank argument

```
>>> import argparse
>>> parser = argparse.ArgumentParser()
>>> parser.add_argument("--local_rank", type=int)
>>> args = parser.parse_args()
```

Set your device to local rank using either

```
>>> torch.cuda.set_device(arg.local_rank)  # before your code runs
```

or

```
>>> with torch.cuda.device(arg.local_rank):
>>>    # your code to run
```

\3. In your training program, you are supposed to call the following function at the beginning to start the distributed backend. You need to make sure that the init_method uses `env://`, which is the only supported `init_method` by this module.

```
torch.distributed.init_process_group(backend='YOUR BACKEND',
                                     init_method='env://')
```

\4. In your training program, you can either use regular distributed functions or use [`torch.nn.parallel.DistributedDataParallel()`](https://pytorch.org/docs/stable/nn.html#torch.nn.parallel.DistributedDataParallel) module. If your training program uses GPUs for training and you would like to use [`torch.nn.parallel.DistributedDataParallel()`](https://pytorch.org/docs/stable/nn.html#torch.nn.parallel.DistributedDataParallel) module, here is how to configure it.

```
model = torch.nn.parallel.DistributedDataParallel(model,
                                                  device_ids=[arg.local_rank],
                                                  output_device=arg.local_rank)
```

Please ensure that `device_ids` argument is set to be the only GPU device id that your code will be operating on. This is generally the local rank of the process. In other words, the `device_ids` needs to be `[args.local_rank]`, and `output_device` needs to be `args.local_rank` in order to use this utility

\5. Another way to pass `local_rank` to the subprocesses via environment variable `LOCAL_RANK`. This behavior is enabled when you launch the script with `--use_env=True`. You must adjust the subprocess example above to replace `args.local_rank` with `os.environ['LOCAL_RANK']`; the launcher will not pass `--local_rank`when you specify this flag.

WARNING

`local_rank` is NOT globally unique: it is only unique per process on a machine. Thus, don’t use it to decide if you should, e.g., write to a networked filesystem. See<https://github.com/pytorch/pytorch/issues/12042> for an example of how things can go wrong if you don’t do this correctly.

## Spawn utility

The torch.multiprocessing package also provides a `spawn` function in [`torch.multiprocessing.spawn()`](https://pytorch.org/docs/stable/multiprocessing.html#torch.multiprocessing.spawn). This helper function can be used to spawn multiple processes. It works by passing in the function that you want to run and spawns N processes to run it. This can be used for multiprocess distributed training as well.

For references on how to use it, please refer to [PyTorch example - ImageNet implementation](https://github.com/pytorch/examples/tree/master/imagenet)

Note that this function requires python 3.4 or higher.
