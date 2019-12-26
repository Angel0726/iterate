---
title: Python 环境下的自动化机器学习超参数调优
toc: true
date: 2019-11-17
---
# Python 环境下的自动化机器学习超参数调优


> 机器学习算法的性能高度依赖于超参数的选择，对机器学习超参数进行调优是一项繁琐但却至关重要的任务。本文介绍了一个使用「Hyperopt」库对梯度提升机（GBM）进行贝叶斯超参数调优的完整示例，并着重介绍了其实现过程。



由于机器学习算法的性能高度依赖于超参数的选择，对机器学习超参数进行调优是一项繁琐但至关重要的任务。手动调优占用了机器学习算法流程中一些关键步骤（如特征工程和结果解释）的时间。网格搜索和随机搜索则不会干涉这些步骤，但是需要大量的运行时间，因为它们浪费了时间去评估搜索空间中并不太可能找到最优点的区域。如今越来越多的超参数调优过程都是通过自动化的方法完成的，它们旨在使用带有策略的启发式搜索（informed search）在更短的时间内找到最优超参数，除了初始设置之外，并不需要额外的手动操作。



贝叶斯优化是一种基于模型的用于寻找函数最小值的方法。近段时间以来，贝叶斯优化开始被用于机器学习超参数调优，结果表明，该方法在测试集上的表现更加优异，但需要的迭代次数小于随机搜索。此外，现在一些 Python 库的出现使得对任意的机器学习模型实现贝叶斯超参数调优变得更加简单。



本文将介绍一个使用「Hyperopt」库对梯度提升机（GBM）进行贝叶斯超参数调优的完整示例。在本文作者早先的一篇文章中，他已经对这个方法背后的概念进行了概述，所以本文将着重介绍实现过程。和大多数机器学习的主题类似，读者并不需要理解所有的细节，但是了解基本的原理可以帮助读者更有效地使用这项技术！



本文所有的代码都可以以 Jupyter Notebook 的形式在 GitHub 上获取（https://github.com/WillKoehrsen/hyperparameter-optimization）。



本文目录：



- 贝叶斯优化方法
- 优化问题的四个组成部分
- 目标函数
- 域空间
- 优化算法
- 结果的历史数据
- 优化
- 搜索结果
- 搜索结果的可视化
- 搜索的演化过程
- 继续搜索
- 结语



**贝叶斯优化方法**



简单地说，贝叶斯优化通过基于过去对目标的评估结果建立一个代理函数（概率模型）找到使得目标函数最小的值。代理函数比目标函数更易于优化，因此下一个待评估的输入值是通过对代理函数应用某种标准（通常为预期提升）来选择的。贝叶斯方法不同于随机搜索或网格搜索，后两者都使用了过去的评估结果来选择接下来待评估的值。它们的思想是：通过根据过去表现良好的值选择下一个输入值来限制评价目标函数的高昂开销。



对于超参数优化来说，其目标函数为使用一组超参数的机器学习模型的验证误差。它的目标是找出在验证集上产生最小误差的超参数，并希望将这些结果泛化到测试集上去。对目标函数评估的开销是巨大的，因为它需要训练带有一组特定超参数的机器学习模型。理想情况下，我们希望找到这样一方法，它既能探索搜索空间，又能限制耗时的超参数评估。贝叶斯超参数调优使用一个不断更新的概率模型，通过从过去的结果中进行推理，使搜索过程「专注」于有可能达到最优的超参数。



Python 环境下有一些贝叶斯优化程序库，它们目标函数的代理算法有所区别。在本文中，我们将使用「Hyperopt」库，它使用树形 Parzen 评估器（TPE，https://papers.nips.cc/paper/4443-algorithms-for-hyper-parameter-optimization.pdf）作为搜索算法，其他的 Python 库还包含「Spearmint」（高斯过程代理）和「SMAC」（随即森林回归）。目前在这个领域有大量有趣的研究，所以如果你对某一个库不是很满意，你可以试试其他的选项！针对某个问题的通用结构（本文将使用的结构）可以在各个库间进行转换，其句法差异非常小。



**优化问题的四个组成部分**



贝叶斯优化问题有四个组成部分：



 \1. 目标函数：我们想要最小化的对象，这里指带超参数的机器学习模型的验证误差

 \2. 域空间：待搜索的超参数值

 \3. 优化算法：构造代理模型和选择接下来要评估的超参数值的方法

 \4. 结果的历史数据：存储下来的目标函数评估结果，包含超参数和验证损失



通过以上四个步骤，我们可以对任意实值函数进行优化（找到最小值）。这是一个强大的抽象过程，除了机器学习超参数的调优，它还能帮我们解决其他许多问题。



**数据集**



在本文的示例中，我们将使用 Caravan Insurance 数据集（https://www.kaggle.com/uciml/caravan-insurance-challenge），它的目标是预测客户是否会购买一份保险产品。这是一个监督分类问题，带有 5800 个用于训练的观测值和 4000 个测试点。由于这是一个不平衡的分类问题，本文使用的评价性能的指标是受试者工作特征曲线下的面积（ROC AUC），ROC AUC 的值越高越好，其值为 1 代表模型是完美的。数据集如下所示：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiboeI2T4xmrqTg9jkwqqU7YWibIQNkg2tjQ4Dtzw0Em5cANsoEnJnOhx93ichcgiaUnB789QOic1ebfeA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)

*数据集（CARAVAN）是带标签的*



由于 Hyperopt 要做的是取最小值，我们将从目标函数中返回「1-ROC AUC」，从而提高 ROC AUC。



**梯度提升模型**



在本文中，我们对梯度提升机（GBM）的细节知识不做过多探讨，下面是我们需要理解的基本知识：GBM 是一种基于使用依次训练的弱学习器（多为决策树）构建强学习器的集成增强方法。GBM 中有许多超参数，它们控制着整个集成结构和单棵决策树。我们在这里使用的一种最有效的选择决策树数量的方法（称为评估器）是早停止（early stopping）。LightGBM 提供了一种 Python 环境下的快速简单的 GBM 实现。



- 想了解更多 GBM 的细节，这里有一篇高屋建瓴的文章：https://medium.com/mlreview/gradient-boosting-from-scratch-1e317ae4587d
- 一篇技术论文：https://brage.bibsys.no/xmlui/bitstream/handle/11250/2433761/16128_FULLTEXT.pdf



抛开必要的背景知识不谈，让我们将用于超参数调优的贝叶斯优化问题的四个组成部分一一列举出来。



**目标函数**



我们试图最小化目标函数。其输入为一组值——在本例中为 GBM 的超参数，输出为需要最小化的实值——交叉验证损失。Hyperopt 将目标函数作为黑盒处理，因为这个库只关心输入和输出是什么。为了找到使损失最小的输入值，该算法不需要知道目标函数的内部细节！从一个高度抽象的层次上说（以伪代码的形式），我们的目标函数可以表示为：



```
def objective(hyperparameters):
    """Returns validation score from hyperparameters"""

    model = Classifier(hyperparameters)
    validation_loss = cross_validation(model, training_data)
    return validation_loss
```



在对最终的模型进行评价时，我们需要注意的是，不要使用测试集上的损失，因为我们只能使用一次测试集。相对地，我们对验证集上的超参数进行评估。此外，我们使用 K 折交叉验证而不是将数据划分到一个独立的验证集中，这种验证方法除了保留了有价值的训练数据外，还能让我们在测试集上获得偏差更小的误差估计。



不同模型超参数调优的目标函数的基本结构是相同的：函数接收超参数作为输入，并返回使用这些超参数的交叉验证误差。尽管本文的示例是针对 GBM 的，但该结构同样可以被应用于其他的方法。



下图为 GBM 的完整目标函数，该 GBM 使用带早停止机制的 10 折交叉验证：



```
import lightgbm as lgb
from hyperopt import STATUS_OK

N_FOLDS = 10

# Create the dataset
train_set = lgb.Dataset(train_features, train_labels)

def objective(params, n_folds = N_FOLDS):
    """Objective function for Gradient Boosting Machine Hyperparameter Tuning"""

    # Perform n_fold cross validation with hyperparameters
    # Use early stopping and evalute based on ROC AUC
    cv_results = lgb.cv(params, train_set, nfold = n_folds, num_boost_round = 10000,
                        early_stopping_rounds = 100, metrics = 'auc', seed = 50)

    # Extract the best score
    best_score = max(cv_results['auc-mean'])

    # Loss must be minimized
    loss = 1 - best_score

    # Dictionary with information for evaluation
    return {'loss': loss, 'params': params, 'status': STATUS_OK}
```



核心的代码为「cv_results = lgb.cv(...)」。为了实现带早停止的交叉验证，我们使用了 LightGBM 的函数「cv」，向该函数传入的参数包含超参数、一个训练集、交叉验证中使用的许多折，以及一些其它的参数。我们将评估器的数量（num_boost_round）设置为 10000，但是由于我们使用了「early_stopping_rounds」，当 100 个评估器的验证得分没有提高时训练会被停止，所以实际上使用的评估器不会达到这个数量。早停止是一种有效的选择评估器数量的方法，而不是将其设置为另一个需要调优的超参数！



当交叉验证完成后，我们将得到最高得分（ROC AUC）。之后，由于我们想要得到的是最小值，我们将采用「1-最高得分」。该值将在返回的字典数据结构中作为「loss」关键字返回。



这个目标函数实际上比它所需的结构复杂一些，因为我们将返回一个值的字典。对于 Hyperopt 中的目标函数，我们可以返回一个单一的值（即损失），或者返回一个带有最小值的关键字「loss」和「status」的字典。返回超参数的值使我们能够查看每组超参数得到的损失。



**域空间**



域空间表示我们想要对每个超参数进行评估的值的范围。在每一轮搜索迭代中，贝叶斯优化算法将从域空间中为每个超参数选定一个值。当我们进行随机搜索或网格搜索时，域空间就是一个网格。贝叶斯优化中也是如此，只是这个域空间对每个超参数来说是一个概率分布而不是离散的值。



然而，在贝叶斯优化问题中，确定域空间是最难的部分。如果有机器学习方法的相关经验，我们可以将更大的概率赋予我们认为最佳值可能存在的点，以此来启发对超参数分布的选择。但是，不同的数据集之间的最佳模型设定是不同的，并且具有高维度的问题（大量的超参数），这会使我们很难弄清超参数之间的相互作用。在不确定最佳值的情况下，我们可以使用更大范围的概率分布，通过贝叶斯算法进行推理。



首先，我们应该了解一个 GBM 中所有的超参数：



```
import lgb
# Default gradient boosting machine classifier
model = lgb.LGBMClassifier()
model
LGBMClassifier(boosting_type='gbdt', n_estimators=100,
               class_weight=None, colsample_bytree=1.0,
               learning_rate=0.1, max_depth=-1,
               min_child_samples=20,
               min_child_weight=0.001, min_split_gain=0.0,
               n_jobs=-1, num_leaves=31, objective=None,
               random_state=None, reg_alpha=0.0, reg_lambda=0.0,
               silent=True, subsample=1.0,
               subsample_for_bin=200000, subsample_freq=1)
```



我不确定世界上是否真有人知道所有的这些超参数是如何相互作用的！而其中有一些超参数是不需要调优（如「objective」和「random_state」）。我们将使用早停止方法找到最佳的评估器数量「n_estimators」。尽管如此，我们仍然需要优化 10 个超参数！当我们第一次对一个模型进行调优时，我通常创建一个以缺省值为中心的大范围域空间，然后在接下来的搜索中对其进行优化。



举个例子，我们不妨在 Hyperopt 中定义一个简单的域——一个离散均匀分布，其中离散点的数量为 GBM 中每棵决策树的叶子结点数：



```
from hyperopt import hp
# Discrete uniform distribution
num_leaves = {'num_leaves': hp.quniform('num_leaves', 30, 150, 1)}
```



这里使用的是一个离散均匀分布，因为叶子结点的数量必须是一个整数（离散的）并且域中的每个值出现的概率是均等的（均匀）。



概率分布的另一种选项是对数均匀分布，在对数尺度上其值的分布是均匀的。我们将对学习率使用一个对数均匀分布（域空间从 0.005 到 0.2），因为它的值往往在不同的数量级之间变化：





```
# Learning rate log uniform distribution
learning_rate = {'learning_rate': hp.loguniform('learning_rate',
                                                 np.log(0.005),
                                                 np.log(0.2)}
```



由于这是一个对数均匀分布，所以我们在 exp（low）和 exp（high）之间绘制其值的示意图。下面左侧的示意图显示了离散均匀分布，右侧的示意图则显示了对数均匀分布。它们是核密度估计示意图，所以 y 轴坐标为密度而不是计数。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiboeI2T4xmrqTg9jkwqqU7YibrDYXucUcxlazmPuf2pfF4nHACVxtdic5ywemzg5T8T3h3tFgAicNsDA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



现在让我们定义整个域：



```
# Define the search space
space = {
    'class_weight': hp.choice('class_weight', [None, 'balanced']),
    'boosting_type': hp.choice('boosting_type',
                               [{'boosting_type': 'gbdt',
                                    'subsample': hp.uniform('gdbt_subsample', 0.5, 1)},
                                 {'boosting_type': 'dart',
                                     'subsample': hp.uniform('dart_subsample', 0.5, 1)},
                                 {'boosting_type': 'goss'}]),
    'num_leaves': hp.quniform('num_leaves', 30, 150, 1),
    'learning_rate': hp.loguniform('learning_rate', np.log(0.01), np.log(0.2)),
    'subsample_for_bin': hp.quniform('subsample_for_bin', 20000, 300000, 20000),
    'min_child_samples': hp.quniform('min_child_samples', 20, 500, 5),
    'reg_alpha': hp.uniform('reg_alpha', 0.0, 1.0),
    'reg_lambda': hp.uniform('reg_lambda', 0.0, 1.0),
    'colsample_bytree': hp.uniform('colsample_by_tree', 0.6, 1.0)
}
```



此处我们使用许多不同种类的域分布：



- choice：类别变量
- quniform：离散均匀分布（在整数空间上均匀分布）
- uniform：连续均匀分布（在浮点数空间上均匀分布）
- loguniform：连续对数均匀分布（在浮点数空间中的对数尺度上均匀分布）



当定义提升（boosting）的类型时，有一个要点需要我们注意：



```
# boosting type domain
boosting_type = {'boosting_type': hp.choice('boosting_type',
                                            [{'boosting_type': 'gbdt',
                                                  'subsample': hp.uniform('subsample', 0.5, 1)},
                                             {'boosting_type': 'dart',
                                                  'subsample': hp.uniform('subsample', 0.5, 1)},
                                             {'boosting_type': 'goss',
                                                  'subsample': 1.0}])}
```



在这里，我们使用一个条件域，它意味着一个超参数的值依赖于另一个超参数的值。对于「goss」类型的提升算法，GBM 不能使用下采样技术（选择一个训练观测数据的子样本部分用于每轮迭代）。因此，如果提升的类型为「goss」，则下采样率设置为 1.0（不使用下采样），否则将其设置为 0.5-1.0。这个过程是使用嵌套域实现的。



当我们使用参数完全不同的机器学习模型时，条件嵌套往往是很有用的。条件嵌套让我们能根据「choice」的不同值使用不同的超参数集。



现在已经定义了域空间，我们可以从中提取一个样本来查看典型样本的形式。当我们进行采样时，因为子样本最初是嵌套的，所以我们需要将它分配给顶层的关键字。这个操作是通过 Python 字典的「get」方法实现的，缺省值为 1.0。



```
# Sample from the full space
example = sample(space)

# Dictionary get method with default
subsample = example['boosting_type'].get('subsample', 1.0)

# Assign top-level keys
example['boosting_type'] = example['boosting_type']['boosting_type']
example['subsample'] = subsample

example
```



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiboeI2T4xmrqTg9jkwqqU7YtcNysDNu1hvUDTT4aI6yVrCcicjZrITjtYVlz6kOnbxglTZWmsAzLicw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



重新分配嵌套的关键字是必要的，因为梯度提升机不能处理嵌套的超参数字典。



**优化算法**



尽管从概念上来说，这是贝叶斯优化最难的一部分，但在 Hyperopt 中创建优化算法只需一行代码。使用树形 Parzen 评估器（Tree Parzen Estimation，以下简称 TPE）的代码如下：



```
from hyperopt import tpe
# Algorithm
tpe_algorithm = tpe.suggest
```



这就是优化算法的所有代码！Hyperopt 目前只支持 TPE 和随机搜索，尽管其 GitHub 主页声称将会开发其它方法。在优化过程中，TPE 算法从过去的搜索结果中构建出概率模型，并通过最大化预期提升（EI）来决定下一组目标函数中待评估的超参数。



**结果历史数据**



跟踪这些结果并不是绝对必要的，因为 Hyperopt 会在内部为算法执行此操作。然而，如果我们想要知道这背后发生了什么，我们可以使用「Trials」对象，它将存储基本的训练信息，还可以使用目标函数返回的字典（包含损失「loss」和参数「params」）。创建一个「Trials」对象也仅需一行代码：





```
from hyperopt import Trials
# Trials object to track progress
bayes_trials = Trials()
```



另一个让我们能够监控长期训练进度的做法是，在每轮迭代中向 csv 文件写入一行。这样做将所有的搜索结果存储到了磁盘上，以防意外情况发生使得我们丢失「Trails」对象（根据经验来说）。我们可以使用「csv」库做到这一点。在开始训练之前，我们打开一个新的 csv 文件并且写入文件头（hearder）：



```
import csv

# File to save first results
out_file = 'gbm_trials.csv'
of_connection = open(out_file, 'w')
writer = csv.writer(of_connection)

# Write the headers to the file
writer.writerow(['loss', 'params', 'iteration', 'estimators', 'train_time'])
of_connection.close()
```



然后在目标函数中，我们可以添加几行代码，在每轮迭代中写入 csv 文件（完整的目标函数可以在 notebook 中获取。



```
# Write to the csv file ('a' means append)
  of_connection = open(out_file, 'a')
  writer = csv.writer(of_connection)
  writer.writerow([loss, params, iteration, n_estimators, run_time])
  of_connection.close()
```



写入 csv 文件意味着我们可以在训练时通过打开文件来检查进度（不是在 Excel 文件中，因为 Excel 会在 Python 环境下导致错误。在 bash 中使用「tail out_file.csv」操作来查看文件的最后一行。）



**优化**



当我们完成了上述四个部分的工作，我们可以通过「fmin」进行优化：



```
from hyperopt import fmin

MAX_EVALS = 500

# Optimize
best = fmin(fn = objective, space = space, algo = tpe.suggest,
            max_evals = MAX_EVALS, trials = bayes_trials)
```



在每一轮迭代中，优化算法从基于先前结果构造的代理函数中选择新的超参数值，并在目标函数中对这些值进行计算。接着，对目标函数进行「MAX_EVALS」评估，代理函数会不断根据新的结果进行更新。



**结果**



从「fmin」返回的最佳对象包含在目标函数上产生最小损失的超参数：





![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiboeI2T4xmrqTg9jkwqqU7YR3Flwrto7fsQXSeWweA7ibnaLjqpWUaQXIJM4EIM2O5SM6YJA5Eib3Sw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



拥有这些超参数之后，我们可以使用它们在完整的训练数据上训练模型，然后对测试数据进行评估（记住我们只能在评估最终的模型时使用一次测试集）。对于评估器的数量，我们可以使用在交叉验证中提前停止时返回最低损失的评估器数量。最终结果如下：





![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiboeI2T4xmrqTg9jkwqqU7Yw4BWEEoG27SUQJ8FedYf1FV2DQEVEibiaPu3plKISQG2y9ZicrqZ28uOA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



作为参考，500 轮随机搜索返回一个在测试集上 ROC AUC 得分为 0.7232、在交叉验证中得分为 0.76850 的模型。一个没有经过优化的缺省模型在测试集上的 ROC AUC 得分则为 0.7143.



当我们查看结果时，需要将以下几点重要事项牢记于心：



- 最优的超参数在交叉验证中表现最好，但并不一定在测试数据上表现最好。当我们使用交叉验证时，我们希望这些结果能够泛化至测试数据上。
- 即使使用 10 折交叉验证，超参数调优还是会对训练数据过度拟合。交叉验证取得的最佳得分远远高于在测试数据上的得分。
- 随机搜索可能由于运气好而返回更好的超参数（重新运行 notebook 就可能改变搜索结果）。贝叶斯优化不能保证找到更好的超参数，并且可能陷入目标函数的局部最小值。



贝叶斯优化虽然十分有效，但它并不能解决我们所有的调优问题。随着搜索的进行，该算法将从探索——尝试新的超参数值，转向开发——利用使目标函数损失最低的 超参数值。如果算法找到了目标函数的一个局部最小值，它可能会专注于搜索局部最小值附近的超参数值，而不会尝试域空间中相对于局部最小值较远的其他值。随机搜索则不会受到这个问题的影响，因为它不会专注于搜索任何值！



另一个重点是，超参数优化的效果将随着数据集的变化而有所差异。本文使用的是一个相对较小的数据集（大约 6000 条训练观测数据），因此对超参数进行调优的回报较小（获取更多的数据将更好地利用时间！）考虑到所有这些注意事项，在这种情况下，通过贝叶斯优化我们可以得到：



- 在测试集上更好的性能
- 更少的超参数调优迭代次数



贝叶斯方法可以（尽管不是经常）获得比随机搜索更好的调优结果。在接下来的几节中，我们将查看贝叶斯超参数搜索的演化过程，并且将其与随机搜索进行对比，从而理解贝叶斯优化的工作原理。



**搜索结果可视化**



将结果通过图表绘制出来可以直观地理解在超参数搜索过程中发生了什么。此外，将贝叶斯优化和随机搜索进行对比有助于我们看到这些方法之间的差异。如果你想知道这些图是如何绘制的，以及随机搜索是如何实现的，请查阅项目 notebook。但是在这里我们将直接显示结果。（请注意，实际的结果会随着迭代次数增加而发生变化，所以如果你运行了这个 notebook，得到了不同的图也无需惊讶。本节所有的图像都是经过了 500 轮迭代所得到的）。



首先我们可以绘制随机搜索和贝叶斯优化中采样得到的学习率「learning_rate」的核密度估计图。作为参考，我们还可以显示采样的分布。垂直的虚线表示学习率最优值（根据交叉验证得到）。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiboeI2T4xmrqTg9jkwqqU7Y7khJ0JvJgyN6fuuEWNWqUvB2MYibzNvdfeu9jmMU14NccPgNvZIsicBQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



我们将学习率定义为 0.005 到 0.2 之间的对数正态分布形式，贝叶斯优化的结果与采样分布的结果看起来相类似。这说明，我们定义的分布看上去很适合这个任务，尽管最优值比我们放置最大概率的位置略高。这个结果可以用于通知域空间进一步搜索。



另一个超参数是提升类型，下图为在随机搜索和贝叶斯优化的过程中对每种类型进行评估的直方图。由于随机搜索不关心过去的搜索结果，我们预计每种提升类型的使用次数大致相同。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiboeI2T4xmrqTg9jkwqqU7YTRlOr7Zpo47N8ogrA7gAhZqD9kdtia87MJpFMlkEEw8KYsz1ibTgN5iaA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



根据贝叶斯算法的评估结果直方图，「gbdt」提升比「dart」或「goss」更有可能找到最优值。同样地，这有助于为进一步搜索提供信息，无论是贝叶斯方法或网格搜索。如果我们想做一个更精确的网格搜索，我们可以用这些结果来定义一个更小的网格，集中在最有可能找到最优超参数的值周围。



既然我们已经有了这些结果，我们可以看看所有参考分布、随机搜索、以及贝叶斯优化中数值化的超参数。垂直的虚线表示每次搜索是超参数的最优值：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiboeI2T4xmrqTg9jkwqqU7YlMOvBoayLZ1smmiczpff4bvibEnNkWp5JJcPhYlXnV4MS2qIfYpvLCFA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



在大多数情况下（「subsample_for_bin」除外），贝叶斯优化搜索倾向于专注搜索（设置更大的概率）能够在交叉验证中得到最小损失的超参数值附近的值。这体现了使用贝叶斯方法进行超参数调优的基本思想：将更多的时间用于评估更有可能达到最优的超参数值。



有些结果很有趣，可能会帮助我们以后定义一个用于搜索的域空间。举例来说，「reg_alpha」和「reg_lambda」看起来是互补的：如果其中的一个值很高（接近于 1.0），另一个值则会变得较低。不能保证这一点在不同的问题中都适用，但是通过研究这些结果，我们可以得到一些可能在未来的机器学习问题中能够用到的观点！



**搜索的演化过程**



随着优化的推进，我们期望贝叶斯方法能够专注于更有希望达到最优超参数的值：那些在交叉验证中产生最低误差的值。我们可以绘制超参数域迭代次数的关系图，看看是否存在明显的趋势。



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiboeI2T4xmrqTg9jkwqqU7YL2PwGlMXCJ37rFhP2AjBKianyHDypdpicBpava5Y7gwNFPkia2hdTHib6g/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



黑色的星星代表最优值。「colsample_bytree」和「learning_rate」随着时间的推移而下降，这可以在未来的搜索中为我们提供指导。



![img](data:image/gif;base64,iVBORw0KGgoAAAANSUhEUgAAAAEAAAABCAYAAAAfFcSJAAAADUlEQVQImWNgYGBgAAAABQABh6FO1AAAAABJRU5ErkJggg==)



最后，如果贝叶斯优化有效，我们预计平均的验证分数会随着时间推移越来越高（相反，损失将会越来越小）：



![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gWiboeI2T4xmrqTg9jkwqqU7YfE36bahz9dWiaiaGDSEKa7nLEzvVKTnuVYVV1lIGEJgJCAicAn76IErdg/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



随着时间的推移，贝叶斯超参数优化的验证得分会越来越高，这说明该方法正在尝试「更好」的超参数值（值得注意的是，只是根据验证分数说明这些值是更好的）。随机搜索并不会随着迭代次数增加显示出性能提升。



**继续搜索**



如果我们对模型的性能不太满意，可以使用 Hyperopt 从我们上次结束的地方继续搜索。我们只需要传入相同的「Trials」对象，算法就会继续进行搜索。



随着算法的运行，它会进行更多的「利用」操作——选用在过去表现得很好的值，进行更少的「探索」操作——选用新的值。因此，与其从上次结束的地方开始，还不如从头开始搜索。如果第一次搜索到的最佳超参数确实是「最优值」，我们预计接下来的搜索会收敛到同样的值上。考虑到这个高维度问题，以及超参数之间复杂的相互作用，另一次搜索并不太可能产生一组类似的超参数。



再次进行了 500 轮迭代的训练后，最终模型在测试集上的 ROC AUC 得分为 0.72736。（我们真的不应该在测试集上对第一个模型进行评估，并且仅仅以验证得分作为依据。理想情况下，测试集只应使用一次，在将算法部署在新数据上时测试其性能）。同样的，由于数据集规模较小，这个问题可能会在未来的超参数优化中得到逐渐减小的返回值，并且最终会在验证误差上达到一个趋近于稳定不变的值（数据集上任何模型的性能都有一个固有的限制，因为隐藏的变量没有被测量，并且有噪声数据，这被称为贝叶斯误差）。



**结语**



我们可以使用贝叶斯优化完成机器学习模型的自动超参数调优。不同于随机搜索方法，贝叶斯优化通过启发式方法选择下一个超参数，从而能够花更多时间评估可能达到最优的值。最终的结果可能是，与随机搜索或网格搜索相比，贝叶斯优化对于目标函数评估的次数更少，并且在测试集上泛化的能力更强。



在本文中，我们使用 Hyperopt 一步一步地实现了 Python 环境下的贝叶斯超参数优化。尽管对于训练数据的过拟合问题需要多加小心，但我们提升了梯度提升机在测试集上的性能，超过了对比基线和随机搜索方法。此外，我们还通过查看结果示意图看到了随机搜索和贝叶斯优化的区别，该图说明了贝叶斯方法会将更大的概率赋予在交叉验证中获得更低损失的超参数值。



通过利用最优化问题的四个组成部分，我们可以使用 Hyperopt 解决各种各样的问题。贝叶斯优化的基本组成部分也适用于大量的实现其他算法的 Python 库。从手动调优到随机搜索或网格搜索只是一个小的进步，但如果想要将你的机器学习技术提升到一个新的水平，则需要自动化的超参数调优。贝叶斯优化是一种能够在 Python 环境下使用的方法，并且能返回比随机搜索更好的结果。希望你能满怀信心地开始将这种强大的技术应用到自己的机器学习问题中！![img](https://mmbiz.qpic.cn/mmbiz_png/KmXPKA19gW8Zfpicd40EribGuaFicDBCRH6IOu1Rnc4T3W3J1wE0j6kQ6GorRSgicib0fmNrj3yzlokup2jia9Z0YVeA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



*原文地址：https://towardsdatascience.com/automated-machine-learning-hyperparameter-tuning-in-python-dfda59b72f8a*


# 相关

- [资源 | Python 环境下的自动化机器学习超参数调优](https://mp.weixin.qq.com/s?__biz=MzU1NTUxNTM0Mg==&mid=2247489168&idx=2&sn=9bed6744b4b7556112ad170f0ab616fa&chksm=fbd27a31cca5f32764c656dcc1d3d816a0e34c0e9f5043e8ea3cbd1ffef7a42547fe18f8bdc8&mpshare=1&scene=1&srcid=0808PPy5sfHAftGnu5PDccZX#rd)
