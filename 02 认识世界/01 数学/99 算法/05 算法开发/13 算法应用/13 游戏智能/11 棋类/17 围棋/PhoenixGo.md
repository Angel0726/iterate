---
title: PhoenixGo
toc: true
date: 2019-11-17
---
# 业界 | 微信团队开源围棋AI技术PhoenixGo，复现AlphaGo Zero论文


本文介绍了腾讯微信翻译团队开源的人工智能围棋项目 PhoenixGo，该项目是对 DeepMind [AlphaGo Zero](http://mp.weixin.qq.com/s?__biz=MzA3MzI4MjgzMw==&mid=2650732104&idx=1&sn=8d0f5b9a1d5ede22cc1017c1c596afe2&chksm=871b3236b06cbb2051eaa1a4a8cf5b8c307c1655d9680af7e81f452ebe590a0b557bca84ab4c&scene=21#wechat_redirect) 论文《Mastering the game of Go without human knowledge》的实现。



PhoenixGo 是腾讯微信翻译团队开发的人工智能围棋程序。据介绍，该项目由几名工程师在开发机器翻译引擎之余，基于 AlphaGo Zero 论文实现，做了若干提高训练效率的创新，并利用微信服务器的闲时计算资源进行自我对弈，缓解了 Zero 版本对海量资源的苛刻需求。



4 月底，在 2018 世界人工智能围棋大赛上，PhoenixGo 取得冠军。参赛队伍包括绝艺，LeelaZero、TSGo、石子旋风、Golois，HEROZ Kishi、Baduki 等来自中、日、韩、欧美等国家和地区的人工智能围棋高手。



5 月 11 日，PhoenixGo 在 Github 上正式开源，以下是技术细节：



项目地址：https://github.com/Tencent/PhoenixGo



如果你在研究中使用 PhoenixGo，请按以下方式引用库：





```
@misc{PhoenixGo2018,
 author = {Qinsong Zeng and Jianchang Zhang and Zhanpeng Zeng and Yongsheng Li and Ming Chen and Sifan Liu}
 title = {PhoenixGo},
 year = {2018},
 journal = {GitHub repository},
 howpublished = {\url{https://github.com/Tencent/PhoenixGo}}
}
```



**构建和运行**



**在 Linux 上**



1 要求



- 支持 C++11 的 GCC；
- Bazel（0.11.1）；
- （可选）CUDA 和 cuDNN（支持 GPU）；
- （可选）TensorRT（加速 GPU 上的计算，建议使用 3.0.4 版本）。



2 构建



复制库，并进行构建配置：





```
git clone https://github.com/Tencent/PhoenixGo.git
cd PhoenixGo
./configure
```



./configure 将询问 CUDA 和 TensorRT 的安装位置，如果必要指定二者的位置。



然后使用 bazel 进行构建：





```
bazel build //mcts:mcts_main
```



TensorFlow 等依赖项将会自动下载。构建过程可能需要很长时间。



3 运行



下载和提取训练好的网络：





```
wget https://github.com/Tencent/PhoenixGo/releases/download/trained-network-20b-v1/trained-network-20b-v1.tar.gz
tar xvzf trained-network-20b-v1.tar.gz
```



以 gtp 模式运行，使用配置文件（取决于 GPU 的数量和是否使用 TensorRT）：





```
bazel-bin/mcts/mcts_main --config_path=etc/{config} --gtp --logtostderr --v=1
```



该引擎支持 GTP 协议，这意味着它可以和具备 GTP 能力的 GUI 一起使用，如 Sabaki。



--logtostderr 使 mcts_main 向 stderr 写入日志消息，如果你想将消息写入文件，将 --logtostderr 改成 --log_dir={log_dir} 即可。



你可以按照此说明更改配置文件：https://github.com/Tencent/PhoenixGo#configure-guide



4 分布模式



如果不同的机器上有 GPU，PhoenixGo 支持分布式 worker。



构建分布式 worker：





```
bazel build //dist:dist_zero_model_server
```



在分布式 worker 上运行 dist_zero_model_server，每个 worker 对应一个 GPU：





```
CUDA_VISIBLE_DEVICES={gpu} bazel-bin/dist/dist_zero_model_server --server_address="0.0.0.0:{port}" --logtostderr
```



在 config 文件中填充 worker 的 ip:port（etc/mcts_dist.conf 是 32 个 worker 的配置示例），并运行分布式 master：





```
bazel-bin/mcts/mcts_main --config_path=etc/{config} --gtp --logtostderr --v=1
```





**在 macOS 上**



注意：TensorFlow 在 1.2.0 版本之后停止支持 macOS 上的 GPU，因此在 macOS 上的操作只能在 CPU 上运行。



1 要求 & 构建



同 Linux。



2 运行



首先添加 libtensorflow_framework.so 到 LD_LIBRARY_PATH 中：



```
$ export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:{project_root}/bazel-bin/external/org_tensorflow/tensorflow"
```

**在 Windows 上**



正在进行。



**配置指南**



以下是 config 文件中的一些重要选项：



- num_eval_threads：应与 GPU 的数量一致；
- num_search_threads：应比 num_eval_threads * eval_batch_size 大一些；
- timeout_ms_per_step：每步使用的时间；
- max_simulations_per_step：每步要做多少模拟；
- gpu_list：使用哪块 GPU，用逗号隔开；
- model_config -> train_dir：训练好的网络的存储目录；
- model_config -> checkpoint_path：使用哪个检查点，如果没设定，则从 train_dir/checkpoint 中获取；
- model_config -> enable_tensorrt：是否使用 TensorRT；
- model_config -> tensorrt_model_path：如果 enable_tensorrt，使用哪个 TensorRT 模型；
- max_search_tree_size：树节点的最大数量，根据存储容量进行更改；
- max_children_per_node：每个节点的子节点的最大数量，根据存储容量进行更改；
- enable_background_search：在对手下棋的时候思考；
- early_stop：如果结果不再更改，则 genmove 可能在 timeout_ms_per_step 之前返回；
- unstable_overtime：如果结果仍然不稳定，则更多地考虑 timeout_ms_per_step * time_factor；
- behind_overtime：如果赢率低于 act_threshold，则更多地考虑 timeout_ms_per_step * time_factor。



分布模式的选项：



- enable_dist：启动分布模式；
- dist_svr_addrs：分布式 worker 的 ip:port，多条线，每条线中有一个 ip:port；
- dist_config -> timeout_ms：RPC 超时。



async 分布模式的选项：



Async 模式是在有大量分布式 worker 的时候使用的（多余 200），而在 sync 模式中需要过多的 eval 线程和搜索线程。



etc/mcts_async_dist.conf 是 256 个 worker 模式的 config 示例。



- enable_async：开启 async 模式
- enable_dist：开启分布模式
- dist_svr_addrs：每个命令行 ip:port 的多行、用逗号分开的列表
- eval_task_queue_size:根据分布 worker 的数量调整
- num_search_threads：根据分布式 worker 的数量调整



参看 mcts/mcts_config.proto 更详细的了解 config 选项。



**命令行选项**



mcts_main 接受以下命令行选项：



- --config_path：配置文件路径；
- --gtp：作为 GTP 引擎来运行，如果禁用，则只能进行 genmove；
- --init_moves：围棋棋盘上最初的落子；
- --gpu_list：覆写配置文件中的 gpu_list；
- --listen_port：与 --gtp 一起使用，在 TCP 协议端口上运行 gtp 引擎；
- --allow_ip：与 --listen_port 一起使用，是允许连接的客户端 ip 列表；
- --fork_per_request：与 --listen_port 一起使用，表示是否 fork 每个请求。



Glog 选项还支持：



- --logtostderr：向 stderr 写入日志消息；
- --log_dir：向该文件夹中的文件写入日志消息；
- --minloglevel：记录级别：0 - INFO、1 - WARNING、2 - ERROR；
- --v：详细记录，--v=1 即记录调试日志，--v=0 即关闭记录。



mcts_main --help 支持更多命令行选项。![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8Zfpicd40EribGuaFicDBCRH6IOu1Rnc4T3W3J1wE0j6kQ6GorRSgicib0fmNrj3yzlokup2jia9Z0YVeA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)


# 相关

- [业界 | 微信团队开源围棋AI技术PhoenixGo，复现AlphaGo Zero论文](https://mp.weixin.qq.com/s?__biz=MzA3MzI4MjgzMw==&mid=2650742247&idx=2&sn=252287db61450fdf56e25c1086cc655a&chksm=871ad999b06d508f807251599085f20199ca3c664504a77f93cc8cf1d5fb085b50fcb24a2dd3&mpshare=1&scene=1&srcid=0515GcnqyqiRWIkVa5Oy2GnX#rd)
