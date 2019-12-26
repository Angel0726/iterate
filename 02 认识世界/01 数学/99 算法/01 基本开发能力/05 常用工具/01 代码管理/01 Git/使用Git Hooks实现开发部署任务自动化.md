---
title: 使用Git Hooks实现开发部署任务自动化
toc: true
date: 2019-01-02
---
# 可以补充进来的

- 感觉这篇讲的非常好

# 使用 Git Hooks 实现开发部署任务

提供：[ZStack云计算](http://www.zstack.org.cn/)

### 前言

版本控制，这是现代软件开发的核心需求之一。有了它，软件项目可以安全的跟踪代码变更并执行回溯、完整性检查、协同开发等多种操作。在各种版本控制软件中，`git`是近年来最流行的软件之一，它的去中心化架构以及源码变更交换的速度被很多开发者青睐。

在`git`的众多优点中，最有用的一点莫过于它的灵活性。通过“hooks”（钩子）系统，开发者和管理员们可以指定 git 在不同事件、不同动作下执行特定的脚本。

本文将介绍 git hooks的基本思路以及用法，示范如何在你的环境中实现自动化的任务。本文所用的操作系统是 Ubuntu 14.04服务器版，理论上任何可以跑 git 的系统都可以用同样的方法来做。

## 前提条件

首先你的服务器上先要安装过`git`。Ubuntu 14.04的用户可以查看这篇教程了解[如何在 Ubuntu 14.04上安装 git](https://www.digitalocean.com/community/tutorials/how-to-install-git-on-ubuntu-14-04)。

其次你应该能够进行基本的 git 操作。如果你觉得对 git 不太熟，可以先看看这个[Git入门教程](https://www.digitalocean.com/community/tutorial_series/introduction-to-git-installation-usage-and-branches)。

上述条件达成后，请继续往下阅读。

## Git Hooks的基本思路

Git hooks的概念相当简单，它是为了一个单一需求而被设计实现的。在一个共享项目（或者说多人协同开发的项目）的开发过程中，团队成员需要确保其编码风格的统一，确保部署方式的统一，等等（git的用户经常会涉及到此类场景），而这些工作会造成大量的重复劳动。

Git hooks是基于事件的（event-based）。当你执行特定的 git 指令时，该软件会从 git 仓库下的`hooks`目录下检查是否有相对应的脚本，如果有则执行之。

有些脚本是在动作执行之前被执行的，这种“先行脚本”可用于实现代码规范的统一、完整性检查、环境搭建等功能。有些脚本则在事件之后被执行，这种“后行脚本”可用于实现代码的部署、权限错误纠正（git在这方面的功能有点欠缺）等功能。

总体来说，git hooks可以实现策略强制执行、确保一致性、环境控制、部署任务处理等多种功能。

Scott Chacon在他的[Pro Git](http://git-scm.com/book)一书中将 hooks 划分为如下类型：

- 客户端的 hook：此类 hook 在提交者（committer）的计算机上被调用执行。此类 hook 又分为如下几类：
  - 代码提交相关的工作流 hook：提交类 hook 作用在代码提交的动作前后，通常用于运行完整性检查、提交信息生成、信息内容验证等功能，也可以用来发送通知。
  - Email相关工作流 hook：Email类 hook 主要用于使用 Email 提交的代码补丁。像是 Linux 内核这样的项目是采用 Email 进行补丁提交的，就可以使用此类 hook。工作方式和提交类 hook 类似，而且项目维护者可以用此类 hook 直接完成打补丁的动作。
  - 其他类：包括代码合并、签出（check out）、rebase、重写（rewrite）、以及软件仓库的清理等工作。
- 服务器端 hook：此类 hook 作用在服务器端，一般用于接收推送，部署在项目的 git 仓库主干（main）所在的服务器上。Chacon将服务器端 hook 分为两类：
  - 接受触发类：在服务器接收到一个推送之前或之后执行动作，前触发常用于检查，后触发常用于部署。
  - 更新：类似于前触发，不过更新类 hook 是以分支（branch）作为作用对象，在每一个分支更新通过之前执行代码。

上述分类有助于我们对 hook 建立一个整体的概念，了解它可以用于哪类事件。当然了，要能够实际的运用它，还需要亲自动手操作、调试。

有些 hook 可以接受参数。也就是说，当 git 调用了 hook 的脚本时，我们可以传递一些数据给这个脚本。可用的 hook 列表如下：

| Hook名称           | 触发指令                         | 描述                                                         | 参数的个数与描述                                             |
| ------------------ | -------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| applypatch-msg     | `git am`                         | 可以编辑 commit 时提交的 message。通常用于验证或纠正补丁提交的信息以符合项目标准。 | (1) 包含预备 commit 信息的文件名                               |
| pre-applypatch     | `git am`                         | 虽然这个 hook 的名称是“打补丁前”，不过实际上的调用时机是打补丁之后、变更 commit 之前。如果以非 0 的状态退出，会导致变更成为 uncommitted 状态。可用于在实际进行 commit 之前检查代码树的状态。 | 无                                                           |
| post-applypatch    | `git am`                         | 本 hook 的调用时机是打补丁后、commit完成提交后。因此，本 hook 无法用于取消进程，而主要用于通知。 | 无                                                           |
| pre-commit         | `git commit`                     | 本 hook 的调用时机是在获取 commit message之前。如果以非 0 的状态退出则会取消本次 commit。主要用于检查 commit 本身（而不是 message） | 无                                                           |
| prepare-commit-msg | `git commit`                     | 本 hook 的调用时机是在接收默认 commit message之后、启动 commit message编辑器之前。非 0 的返回结果会取消本次 commit。本 hook 可用于强制应用指定的 commit message。 | 1. 包含 commit message的文件名。2. commit message的源（message、template、merge、squash或 commit）。3. commit的 SHA-1（在现有 commit 上操作的情况）。 |
| commit-msg         | `git commit`                     | 可用于在 message 提交之后修改 message 的内容或打回 message 不合格的 commit。非 0 的返回结果会取消本次 commit。 | (1) 包含 message 内容的文件名。                                |
| post-commit        | `git commit`                     | 本 hook 在 commit 完成之后调用，因此无法用于打回 commit。主要用于通知。 | 无                                                           |
| pre-rebase         | `git rebase`                     | 在执行 rebase 的时候调用，可用于中断不想要的 rebase。           | 1. 本次 fork 的上游。2. 被 rebase 的分支（如果 rebase 的是当前分支则没有此参数） |
| post-checkout      | `git checkout` 和 `git clone`    | 更新工作树后调用 checkout 时调用，或者执行 git clone后调用。主要用于验证环境、显示变更、配置环境。 | 1. 之前的 HEAD 的 ref。 2. 新 HEAD 的 ref。 3. 一个标签，表示其是一次 branch checkout还是 file checkout。 |
| post-merge         | `git merge` 或 `git pull`        | 合并后调用，无法用于取消合并。可用于进行权限操作等 git 无法执行的动作。 | (1) 一个标签，表示是否是一次标注为 squash 的 merge。            |
| pre-push           | `git push`                       | 在往远程 push 之前调用。本 hook 除了携带参数之外，还同时给 stdin 输入了如下信息：” ”（每项之间有空格）。这些信息可以用来做一些检查，比如说，如果本地（local）sha1为 40 个零，则本次 push 是一个删除操作；如果远程（remote）sha1是 40 个零，则是一个新的分支。非 0 的返回结果会取消本次 push。 | 1. 远程目标的名称。 2. 远程目标的位置。                      |
| pre-receive        | 远程 repo 进行`git-receive-pack`   | 本 hook 在远程 repo 更新刚被 push 的 ref 之前调用。非 0 的返回结果会中断本次进程。本 hook 虽然不携带参数，但是会给 stdin 输入如下信息：” ”。 | 无                                                           |
| update             | 远程 repo 进行`git-receive-pack`   | 本 hook 在远程 repo 每一次 ref 被 push 的时候调用（而不是每一次 push）。可以用于满足“所有的 commit 只能快进”这样的需求。 | 1. 被更新的 ref 名称。2. 老的对象名称。3. 新的对象名称。       |
| post-receive       | 远程 repo 进行`git-receive-pack`   | 本 hook 在远程 repo 上所有 ref 被更新后，push操作的时候调用。本 hook 不携带参数，但可以从 stdin 接收信息，接收格式为” ”。因为 hook 的调用在更新之后进行，因此无法用于终止进程。 | 无                                                           |
| post-update        | 远程 repo 进行`git-receive-pack`   | 本 hook 仅在所有的 ref 被 push 之后执行一次。它与 post-receive很像，但是不接收旧值与新值。主要用于通知。 | 每个被 push 的 repo 都会生成一个参数，参数内容是 ref 的名称        |
| pre-auto-gc        | `git gc –auto`                   | 用于在自动清理 repo 之前做一些检查。                           | 无                                                           |
| post-rewrite       | `git commit –amend`,`git-rebase` | 本 hook 在 git 命令重写（rewrite）已经被 commit 的数据时调用。除了其携带的参数之外，本 hook 还从 stdin 接收信息，信息格式为” ”。 | 触发本 hook 的命令名称（amend或者 rebase）                      |

下面我们通过几个场景来说明 git hook的使用方法。

## 设置软件仓库

首先，在用户目录下创建一个新的空仓库，命名为 `proj`。

```
mkdir ~/proj
cd ~/proj
git init


Initialized empty Git repository in /home/demo/proj/.git/
123456
```

我们现在已经处于这个 git 控制的目录下，目录下还没有任何内容。在添加任何内容之前，我们先进入 `.git` 这个隐藏目录下：

```
cd .git
ls -F


branches/  config  description  HEAD  hooks/  info/  objects/  refs/
12345
```

这里可以看到一些文件和目录。我们感兴趣的是 `hooks` 这个目录：

```
cd hooks
ls -l


total 40
-rwxrwxr-x 1 demo demo  452 Aug  8 16:50 applypatch-msg.sample
-rwxrwxr-x 1 demo demo  896 Aug  8 16:50 commit-msg.sample
-rwxrwxr-x 1 demo demo  189 Aug  8 16:50 post-update.sample
-rwxrwxr-x 1 demo demo  398 Aug  8 16:50 pre-applypatch.sample
-rwxrwxr-x 1 demo demo 1642 Aug  8 16:50 pre-commit.sample
-rwxrwxr-x 1 demo demo 1239 Aug  8 16:50 prepare-commit-msg.sample
-rwxrwxr-x 1 demo demo 1352 Aug  8 16:50 pre-push.sample
-rwxrwxr-x 1 demo demo 4898 Aug  8 16:50 pre-rebase.sample
-rwxrwxr-x 1 demo demo 3611 Aug  8 16:50 update.sample
1234567891011121314
```

这里面已经有了一些东西。首先可以看到的是，目录下的每一个文件都被标记为“可执行”。脚本通过文件名被调用，因此它们必须是可执行的，而且其内容的第一行必须有一个[Shebang魔术数字](https://en.wikipedia.org/wiki/Shebang_%28Unix%29#Magic_number)（#!）引用至正确的脚本解析器。常用的脚本语言有 bash、perl、python等。

其次，我们可以看到现在所有的文件都有一个 `.sample` 后缀名。Git决定是否执行一个 hook 文件完全是通过其文件名来判定的， `.sample` 代表不执行，所以如果要激活某个 hook，则需要将这个后缀名删除。

现在，回到项目的根目录：

```
cd ../..
1
```

### 示范 1：用“提交后触发”类 hook 在本地 Web 服务器上部署代码

第一个示范将用到 `post-commit` hook 来自动给本地 Web 服务器提交代码。我们会让 git 在每次 commit 提交后都做一次部署——这当然不适用于生产环境，但你明白这个意思就行。

首先安装一个 Apache：

```
sudo apt-get update
sudo apt-get install apache2
12
```

我们的脚本需要能够修改 `/var/www/html` 路径（Web服务器根目录）下的内容，因此需要添加写权限。我们可以直接将当前系统用户设置为该目录的 owner：

```
sudo chown -R `whoami`:`id -gn` /var/www/html
1
```

接下来，回到我们的项目目录，创建一个 `index.html` 文件：

```
cd ~/proj
nano index.html
12
```

里面随便写点什么内容：

```
<h1>Here is a title!</h1>

<p>Please deploy me!</p>
123
```

保存退出，然后告诉 git 跟踪这个文件：

```
git add .
1
```

现在，我们就要开始给这个仓库设置 `post-commit` hook了。在 `.git/hooks` 目录下创建这个文件：

```
vim .git/hooks/post-commit
1
```

在编写这个文件之前，我们先来了解一下 git 在运行 hook 的时候是如何设置环境的。

### 有关 Git hooks的环境变量

调用 hook 的时候会涉及一些环境变量。要让我们的脚本完成工作，我们需要把 git 在调用 `post-commit` hook 时变更的环境变量再改回去。

这是编写 git hook时需要特别注意的一点。Git在调用不同 hook 的时候会设置不同的环境变量。也就是说，不同的 hook 会导致 git 从不同的环境拉取信息。

这样一来，你的脚本环境会变得不可控，你可能根本没意识到哪些变量被自动更改了。糟糕的是，这些变更的变量完全没有在 git 的文档中说明。

幸运的是，Mark Longair找到了一种测试方法来[检查每个 hook 被调用时所变更的环境变量](http://longair.net/blog/2011/04/09/missing-git-hooks-documentation/)。这个测试方法只需要你把下面这几行代码粘贴到你的 git hook脚本中即可：

```
#!/bin/bash
echo Running $BASH_SOURCE
set | egrep GIT
echo PWD is $PWD
1234
```

他这篇文章是在 2011 年写的，当时的 git 版本在 1.7.1。我写这篇文章的时间是 2014 年 8 月，用的 git 版本是 1.9.1，操作系统是 Ubuntu 14.04，应该说还是有一些变化。总之，下面是我的测试结果：

在以下测试中，本地项目目录为 `/home/demo/test_hooks`，远程路径为 `/home/demo/origin/test_hooks.git`。

- **Hooks**：`applypatch-msg`、`pre-applypatch`、`post-applypatch`
  - **环境变量**：
  - GIT_AUTHOR_DATE=’Mon, 11 Aug 2014 11:25:16 -0400’
  - GIT_AUTHOR_EMAIL=demo@example.com
  - GIT_AUTHOR_NAME=’Demo User’
  - GIT_INTERNAL_GETTEXT_SH_SCHEME=gnu
  - GIT_REFLOG_ACTION=am
  - **工作目录**: /home/demo/test_hooks
- **Hooks**：`pre-commit`、`prepare-commit-msg`、`commit-msg`、`post-commit`
  - **环境变量**：
  - GIT_AUTHOR_DATE=’@1407774159 -0400’
  - GIT_AUTHOR_EMAIL=demo@example.com
  - GIT_AUTHOR_NAME=’Demo User’
  - GIT_DIR=.git
  - GIT_EDITOR=:
  - GIT_INDEX_FILE=.git/index
  - GIT_PREFIX=
  - **工作目录**: /home/demo/test_hooks
- **Hooks**: `pre-rebase`
  - **环境变量**：
  - GIT_INTERNAL_GETTEXT_SH_SCHEME=gnu
  - GIT_REFLOG_ACTION=rebase
  - **工作目录**: /home/demo/test_hooks
- **Hooks**: `post-checkout`
  - **环境变量**：
  - GIT_DIR=.git
  - GIT_PREFIX=
  - **工作目录**: /home/demo/test_hooks
- **Hooks**: `post-merge`
  - **环境变量**：
  - GITHEAD_4b407c…
  - GIT_DIR=.git
  - GIT_INTERNAL_GETTEXT_SH_SCHEME=gnu
  - GIT_PREFIX=
  - GIT_REFLOG_ACTION=’pull other master’
  - **工作目录**: /home/demo/test_hooks
- **Hooks**: `pre-push`
  - **环境变量**：
  - GIT_PREFIX=
  - **工作目录**: /home/demo/test_hooks
- **Hooks**: `pre-receive`, `update`, `post-receive`, `post-update`
  - **环境变量**：
  - GIT_DIR=.
  - **工作目录**: /home/demo/origin/test_hooks.git
- **Hooks**: `pre-auto-gc`
  - 这个很难测试所以信息缺失
- **Hooks**: `post-rewrite`
  - **环境变量**：
  - GIT_AUTHOR_DATE=’@1407773551 -0400’
  - GIT_AUTHOR_EMAIL=demo@example.com
  - GIT_AUTHOR_NAME=’Demo User’
  - GIT_DIR=.git
  - GIT_PREFIX=
  - **工作目录**: /home/demo/test_hooks

以上就是 git 在调用不同 hook 时所看到的环境。有了这些信息，我们可以回去继续编写我们的脚本了。

### 继续回来写脚本

我们现在知道了 `post-commit` hook 会改变的环境变量。把这个信息记录下来。

Git hooks是标准的脚本，所以要在第一行告诉 git 用什么解释器：

```
#!/bin/bash
1
```

然后，我们要让 git 把最新版本的代码仓库（最新一次提交后）解包到 Web 服务器的根目录下。这需要把工作目录设置为 Apache 的文件根目录，把 git 目录设置为软件仓库的目录。

同时，我们还需要确保这个过程每次都能成功，即使出现了冲突也要强制执行。接下来的脚本是这样写的：

```
#!/bin/bash
git --work-tree=/var/www/html --git-dir=/home/demo/proj/.git checkout -f
12
```

At this point, we are almost done. However, we need to look extra close at the environmental variables that are set each time the `post-commit` hook is called. In particular, the `GIT_INDEX_FILE` is set to`.git/index`.

这样就基本完成了。接下来的工作就是有关环境变量的工作了。`post-commit` hook被调用时所变更的环境变量中，有一个 `GIT_INDEX_FILE` 被变更为 `.git/index`，这个是我们关注的重点。

这个路径是相对于工作路径的，而我们现在的工作路径是 `/var/www/html`，而这下面是没有 `.git/index` 目录的，导致脚本出错。所以，我们需要手动的把这个变量改回正确的路径。这个 unset 指令需要放在 checkout 指令**之前**，像这样：

```
#!/bin/bash
unset GIT_INDEX_FILE
git --work-tree=/var/www/html --git-dir=/home/demo/proj/.git checkout -f
123
```

很多时候，这种问题是很难跟踪到的。如果你在使用 git hook之前没意识到环境变量的问题，往往会到处踩坑。

总之，我们的脚本完成了，现在保存退出。

然后，我们需要给这个脚本文件添加执行权限：

```
chmod +x .git/hooks/post-commit
1
```

现在回到项目所在的目录，来一发 commit 试试～

```
cd ~/proj
git commit -m "here we go..."
12
```

现在到浏览器里看看效果，是不是我们刚才写的 `index.html` 的内容：

```
http://你的服务器 IP
1
```

![](http://images.iterate.site/blog/image/20190102/eCqyFhvGVGQj.png?imageslim){ width=55% }


正如我们所看到的，刚才提交的代码已经自动部署到 Web 服务器的文件根目录下啦。再来更新点内容试试：

```
echo "<p>Here is a change.</p>" >> index.html
git add .
git commit -m "First change"
123
```

刷新浏览器页面，看看变更生效没：

![](http://images.iterate.site/blog/image/20190102/Hqubul1XpwhM.png?imageslim){ width=55% }


你看，这让本地测试变得方便了很多。当然正如我们前面说的，生产环境上是不能这么用的。要上生产环境的代码一定要仔细的测试验证过才行。

## 使用 Git hook往另一台生产服务器上部署

下面我将示范往生产环境服务器上部署代码的正确姿势。我将使用 push-to-deploy模型，在我们往一个裸 git 仓库（bare git repo）推送代码的时候触发线上 web 服务器的代码更新。

我们刚才的那台机器现在就当作开发机，我们每次 commit 之后这里都会自动部署，可随时查看变更效果。

接下来，我会设置另一台服务器做我们的生产服务器。这台服务器上有一个裸仓库用于接收推送，还有一个能够被推送行为触发的 git hook。然后，以普通用户在 sudo 权限下执行如下步骤。

### 设置生产服务器的 post-receive hook

首先，在生产服务器上安装 Web 服务器：

```
sudo apt-get update
sudo apt-get install apache2
12
```

别忘了给 git 设置权限：

```
sudo chown -R `whoami`:`id -gn` /var/www/html
1
```

也别忘了安装 git：

```
sudo apt-get install git
1
```

然后，还是在用户主目录下创建同样名称的项目目录。然后，在这个目录下初始化一个裸仓库。裸仓库是没有工作路径的，它比较适合不经常直接操作的服务器。

```
mkdir ~/proj
cd ~/proj
git init --bare
123
```

因为这是裸仓库，所以它没有工作路径，而一个正常 git 仓库的 `.git` 路径下的所有文件都会直接出现在这个裸仓库的根目录下。

现在，创建我们的 `post-receive` hook，这个 hook 在服务器收到 `git push` 时被触发。用编辑器打开这个文件：

```
nano hooks/post-receive
1
```

第一行还是要定义我们的脚本类型。然后，告诉 git 我们想做什么，还是跟之前的 `post-commit` 做的事情一样，把文件解包到这台 Web 服务器的文件根目录下：

```
#!/bin/bash
git --work-tree=/var/www/html --git-dir=/home/demo/proj checkout -f
12
```

因为是裸仓库，所以 `--git-dir` 需要指定一个绝对路径。其他的都差不多。

然后，我们需要添加一些额外的逻辑，因为我们不希望把标记为 `test-feature` 的分支代码部署到生产服务器。我们的生产服务器仅仅部署 `master` 分支的内容。

在之前的那张表格中可以看到， `post-receive` hook能够从 git 接受三个通过标准输入（standard input）写到脚本中的内容，包括上一版的 commit hash（），最新版的 commit hash()，以及引用名称。我们可以用这些信息检查 ref 是否是 master 分支。

首先我们需要从标准输入读取内容。每一个 ref 被推送时，上述三条信息都会以标准输入的格式被提供给脚本，三条信息之间由空格分隔。我们可以在一个 `while` 循环中读取这些信息，把上面的 git 命令放进这个循环中：

```
#!/bin/bash
while read oldrev newrev ref
do
    git --work-tree=/var/www/html --git-dir=/home/demo/proj checkout -f
done
12345
```

然后我们需要添加一个判定条件。一个来自 master 分支的 push，其 ref 通常会包含一个 `refs/heads/master` 字段。这可以作为我们判定的依据：

```
#!/bin/bash
while read oldrev newrev ref
do
    if [[ $ref =~ .*/master$ ]];
    then
        git --work-tree=/var/www/html --git-dir=/home/demo/proj checkout -f
    fi
done
12345678
```

另一方面，服务器端的 hook 可以让 git 传递一些消息返回给客户端。发送到标准输出的内容都会被转发给客户端，我们可以用这个功能给用户发送通知。

这个通知应该包含一些场景描述以及系统最终执行了什么动作。对于来自非 master 的推送，我们也应该给用户返回信息，告诉他们为什么这次推送是成功的但代码并没有部署到线上：

```
#!/bin/bash
while read oldrev newrev ref
do
    if [[ $ref =~ .*/master$ ]];
    then
        echo "Master ref received.  Deploying master branch to production..."
        git --work-tree=/var/www/html --git-dir=/home/demo/proj checkout -f
    else
        echo "Ref $ref successfully received.  Doing nothing: only the master branch may be deployed on this server."
    fi
done
1234567891011
```

编辑完毕后，保存退出。

最后，别忘了把脚本文件设置为可执行：

```
chmod +x hooks/post-receive
1
```

现在，我们就可以在我们的客户端访问这个远程服务器了。

### 在客户端上配置远程服务器

现在回到我们的客户端，也就是开发机上，进入项目目录：

```
cd ~/proj
1
```

我们要在这个目录下将我们的远程服务器添加进来，就叫做 `production`。你需要知道远程服务器上的用户名、服务器的 IP 或者域名、以及裸仓库相对于用户 home 目录的路径。整个操作指令看起来差不多是这样的：

```
git remote add production demo@server_domain_or_IP:proj
1
```

来 push 一个看看：

```
git push production master
1
```

如果你的 SSH 密钥还没设置，则需要敲入你的密码。服务器返回的内容看起来应该是这样的：

```
Counting objects: 8, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (4/4), 473 bytes | 0 bytes/s, done.
Total 4 (delta 0), reused 0 (delta 0)
remote: Master ref received.  Deploying master branch...
To demo@107.170.14.32:proj
   009183f..f1b9027  master -> master
12345678
```

我们在这里能够看到刚才在`post-receive` hook里面写的信息了。如果我们从浏览器里访问远程服务器的 IP 或者域名，则应该能看到最新版的页面：

![](http://images.iterate.site/blog/image/20190102/naWTy82KaJJs.png?imageslim){ width=55% }


看起来，这个 hook 已经成功的把我们的代码部署到生产环境啦。

现在继续来测试。我们在开发机上创建一个新的分支`test_feature`，签入到这个分支下面：

```
git checkout -b test_feature
1
```

现在，我们所做的变更都会在 `test_feature` 这个测试分支中进行。来改点东西先：

```
echo "<h2>New Feature Here</h2>" >> index.html
git add .
git commit -m "Trying out new feature"
123
```

这样 commit 之后，在浏览器里输入开发机的 IP，你应该能看到这个变更：

![](http://images.iterate.site/blog/image/20190102/oIjxXpnXamCM.png?imageslim){ width=55% }


正如我们所需要的那样，开发机上的 Web 服务器内容更新了。这样进行本地测试再方便不过。

然后，试试把这个 `test_feature` 推送到远程服务器上：

```
git push production test_feature
1
```

从`post-receive` hook返回的结果应该是这样的：

```
Counting objects: 5, done.
Delta compression using up to 2 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 301 bytes | 0 bytes/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Ref refs/heads/test_feature successfully received.  Doing nothing: only the master branch may be deployed on this server
To demo@107.170.14.32:proj
   83e9dc4..5617b50  test_feature -> test_feature
12345678
```

在浏览器里输入生产服务器的 IP 地址，应该是啥变化都没有。这正是我们需要的，因为我们的变更没有提交到 master。

现在，如果我们完成了测试，想把这个变更推送到生产服务器上，我们可以这样做。首先，签入到`master`分支，把刚才的`test_feature`分支合并进来：

```
git checkout master
git merge test_feature
12
```

合并完成后，再推送到生产服务器：

```
git push production master
1
```

现在再到浏览器里输入生产服务器的 IP 看看，变更被成功部署了：

![](http://images.iterate.site/blog/image/20190102/RWCgfRyyxClD.png?imageslim){ width=55% }

这样的工作流，在开发机上实现了实时部署，在生产环境上实现了推送 master 就部署，皆大欢喜。

## 总结

至此，你对于 git hooks的用法应该有了一个大致的了解，对如何使用它来实现你的任务自动化有了概念。它可以用于部署代码，可以用于维护代码质量，拒绝任何不符合要求的变更。

虽然 git hooks很好用，但实际运用往往不容易掌握，遇到问题后的排障过程也很烦人。要编写出高效的 hook，需要长期的练习，把各种配置、参数、标准输入、环境变量都玩清楚。这会花费相当长的时间，但这些投入最终会帮助你和你的团队免除大量的手动操作，带来更高的回报。

本文来源自[DigitalOcean Community](https://www.digitalocean.com/community)。英文原文：[How To Use Git Hooks To Automate Development and Deployment Tasks](https://www.digitalocean.com/community/tutorials/how-to-use-git-hooks-to-automate-development-and-deployment-tasks) by [Justin Ellingwood](https://www.digitalocean.com/community/users/jellingwood)



# 相关

- [使用 Git Hooks实现开发部署任务自动化](https://blog.csdn.net/zstack_org/article/details/53331077)


