---
title: 23 torch.utils.model_zoo
toc: true
date: 2019-06-29
---
# TORCH.UTILS.MODEL_ZOO

Moved to torch.hub.



- `torch.utils.model_zoo.``load_url`(*url*, *model_dir=None*, *map_location=None*, *progress=True*)

  Loads the Torch serialized object at the given URL.If the object is already present in model_dir, it’s deserialized and returned. The filename part of the URL should follow the naming convention `filename-<sha256>.ext` where `<sha256>` is the first eight or more digits of the SHA256 hash of the contents of the file. The hash is used to ensure unique names and to verify the contents of the file.The default value of model_dir is `$TORCH_HOME/checkpoints` where environment variable `$TORCH_HOME` defaults to `$XDG_CACHE_HOME/torch`. `$XDG_CACHE_HOME` follows the X Design Group specification of the Linux filesytem layout, with a default value `~/.cache` if not set.Parameters**url** (*string*) – URL of the object to download**model_dir** (*string**,* *optional*) – directory in which to save the object**map_location** (*optional*) – a function or a dict specifying how to remap storage locations (see torch.load)**progress** ([*bool*](https://docs.python.org/3/library/functions.html#bool)*,* *optional*) – whether or not to display a progress bar to stderrExample`>>> state_dict = torch.hub.load_state_dict_from_url('https://s3.amazonaws.com/pytorch/models/resnet18-5c106cde.pth')`
