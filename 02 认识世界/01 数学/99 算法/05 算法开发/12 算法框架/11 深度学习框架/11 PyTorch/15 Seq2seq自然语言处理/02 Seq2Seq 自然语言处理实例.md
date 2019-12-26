---
title: 02 Seq2Seq 自然语言处理实例
toc: true
date: 2019-06-21
---
# 可以补充进来的

- 感觉这个例子还是挺好的，不过没怎么看明白，要重新看下。
- 对于 NLP 要从头到尾学下。他山之石可以攻玉。

# Seq2seq自然语言处理案例


我们接下来通过神经网络来实现用法语翻译英语。

接下来我们用 PyTorch 来实现 Seq2seq 自然语言处理。

代码如下：


```py
from __future__ import unicode_literals, print_function, division
from io import open
import unicodedata
import string
import re
import random

import torch
import torch.nn as nn
from torch.autograd import Variable
from torch import optim
import torch.nn.functional as F
import matplotlib.pyplot as plt
import matplotlib.ticker as ticker
import numpy as np

use_cuda = torch.cuda.is_available()

SOS_token = 0  # start of string
EOS_token = 1  # end of string


class Lang:
    def __init__(self, name):
        self.name = name
        self.word2index = {}
        self.word2count = {}
        self.index2word = {0: "SOS", 1: "EOS"}
        self.n_words = 2  # Count SOS and EOS

    # 对句子进行拆分，并将对应的 word 补充进来
    def addSentence(self, sentence):
        for word in sentence.split(' '):
            self.addWord(word)

    # 根据 word 记录 word2index index2word word2count
    def addWord(self, word):
        if word not in self.word2index:
            self.word2index[word] = self.n_words
            self.word2count[word] = 1
            self.index2word[self.n_words] = word
            self.n_words += 1
        else:
            self.word2count[word] += 1


# TODO 这个真的可以从 unicode 转化为 Ascii 吗？
def unicodeToAscii(s):
    return ''.join(c for c in unicodedata.normalize('NFD', s) if unicodedata.category(c) != 'Mn')


# Lowercase, trim, and remove non-letter characters

# TODO re.sub 还是要补充下的。
def normalizeString(s):
    s = unicodeToAscii(s.lower().strip())
    s = re.sub(r"([.!?])", r" \1", s)
    s = re.sub(r"[^a-zA-Z.!?]+", r" ", s)
    return s


def readLangs(lang1, lang2, reverse=False):
    print("Reading lines...")

    # Read the file and split into lines
    # TODO 帅气 没想到可以这样写。
    lines = open('data/%s-%s.txt' % (lang1, lang2), encoding='utf-8'). \
        read().strip().split('\n')

    # Split every line into pairs and normalize # 嗯。
    pairs = [[normalizeString(s) for s in l.split('\t')] for l in lines]

    # Reverse pairs, make Lang instances
    if reverse:
        pairs = [list(reversed(p)) for p in pairs] # 之前没用过 reversed 来翻转一个 list
        input_lang = Lang(lang2)
        output_lang = Lang(lang1)
    else:
        input_lang = Lang(lang1)
        output_lang = Lang(lang2)

    return input_lang, output_lang, pairs # 嗯，返回这个感觉可以拆分下吧，感觉从返回值比较难看出想法


MAX_LENGTH = 10

eng_prefixes = (
    "i am ", "i m ",
    "he is", "he s ",
    "she is", "she s",
    "you are", "you re ",
    "we are", "we re ",
    "they are", "they re "
)

# TODO 为啥只将满足 eng_prefixes 的保存下来呢？
# TODO 这样不是默认了 eng 的就是在 pair[1] 里面的？那之前的 reversed 情况不需要过滤吗？
def filterPair(p):
    return len(p[0].split(' ')) < MAX_LENGTH and \
           len(p[1].split(' ')) < MAX_LENGTH and \
           p[1].startswith(eng_prefixes)


def filterPairs(pairs):
    return [pair for pair in pairs if filterPair(pair)]

# 嗯，挺好的，对文本数据进行加载、过滤。
def prepareData(lang1, lang2, reverse=False):
    input_lang, output_lang, pairs = readLangs(lang1, lang2, reverse)
    print("Read %s sentence pairs" % len(pairs))
    pairs = filterPairs(pairs)
    print("Trimmed to %s sentence pairs" % len(pairs))
    print("Counting words...")
    for pair in pairs:
        input_lang.addSentence(pair[0])
        output_lang.addSentence(pair[1])
    print("Counted words:")
    print(input_lang.name, input_lang.n_words)
    print(output_lang.name, output_lang.n_words)
    return input_lang, output_lang, pairs


input_lang, output_lang, pairs = prepareData('eng', 'fra', True)
print(random.choice(pairs)) # 嗯随机选个看下，这个在真正项目中也是有必要的，防止预处理出了问题。


class EncoderRNN(nn.Module):
    def __init__(self, input_size, hidden_size, n_layers=1):
        super(EncoderRNN, self).__init__()
        self.n_layers = n_layers
        self.hidden_size = hidden_size

        self.embedding = nn.Embedding(input_size, hidden_size) # nn.Embedding 是什么意思？
        self.gru = nn.GRU(hidden_size, hidden_size)

    def forward(self, input, hidden):
        embedded = self.embedding(input).view(1, 1, -1) # 没明白
        output = embedded
        for i in range(self.n_layers):
            output, hidden = self.gru(output, hidden) # 没明白。
        return output, hidden

    def initHidden(self):
        result = Variable(torch.zeros(1, 1, self.hidden_size))
        if use_cuda:
            return result.cuda()
        else:
            return result


class DecoderRNN(nn.Module):
    def __init__(self, hidden_size, output_size, n_layers=1):
        super(DecoderRNN, self).__init__()
        self.n_layers = n_layers
        self.hidden_size = hidden_size

        self.embedding = nn.Embedding(output_size, hidden_size)
        self.gru = nn.GRU(hidden_size, hidden_size)
        self.out = nn.Linear(hidden_size, output_size)
        self.softmax = nn.LogSoftmax(dim=1)

    def forward(self, input, hidden):
        output = self.embedding(input).view(1, 1, -1)
        for i in range(self.n_layers):
            output = F.relu(output)
            output, hidden = self.gru(output, hidden)
        output = self.softmax(self.out(output[0]))
        return output, hidden

    def initHidden(self):
        result = Variable(torch.zeros(1, 1, self.hidden_size))
        if use_cuda:
            return result.cuda()
        else:
            return result


class AttnDecoderRNN(nn.Module):
    def __init__(self, hidden_size, output_size, n_layers=1, dropout_p=0.1, max_length=MAX_LENGTH):
        super(AttnDecoderRNN, self).__init__()
        self.hidden_size = hidden_size
        self.output_size = output_size
        self.n_layers = n_layers
        self.dropout_p = dropout_p
        self.max_length = max_length

        self.embedding = nn.Embedding(self.output_size, self.hidden_size)
        self.attn = nn.Linear(self.hidden_size * 2, self.max_length)
        self.attn_combine = nn.Linear(self.hidden_size * 2, self.hidden_size)
        self.dropout = nn.Dropout(self.dropout_p)
        self.gru = nn.GRU(self.hidden_size, self.hidden_size)
        self.out = nn.Linear(self.hidden_size, self.output_size)

    def forward(self, input, hidden, encoder_outputs):
        embedded = self.embedding(input).view(1, 1, -1)
        embedded = self.dropout(embedded)

        attn_weights = F.softmax(
            self.attn(torch.cat((embedded[0], hidden[0]), 1))) # 这个 torch.cat 是什么？
        attn_applied = torch.bmm(attn_weights.unsqueeze(0), # torch.bmm 是什么？unsqueeze 是什么？
                                 encoder_outputs.unsqueeze(0))

        output = torch.cat((embedded[0], attn_applied[0]), 1)
        output = self.attn_combine(output).unsqueeze(0)

        for i in range(self.n_layers):
            output = F.relu(output)
            output, hidden = self.gru(output, hidden)

        output = F.log_softmax(self.out(output[0]))
        return output, hidden, attn_weights

    def initHidden(self):
        result = Variable(torch.zeros(1, 1, self.hidden_size))
        if use_cuda:
            return result.cuda()
        else:
            return result


def indexesFromSentence(lang, sentence):
    return [lang.word2index[word] for word in sentence.split(' ')]


def variableFromSentence(lang, sentence):
    indexes = indexesFromSentence(lang, sentence)
    indexes.append(EOS_token)
    result = Variable(torch.LongTensor(indexes).view(-1, 1))
    if use_cuda:
        return result.cuda()
    else:
        return result


def variablesFromPair(pair):
    input_variable = variableFromSentence(input_lang, pair[0])
    target_variable = variableFromSentence(output_lang, pair[1])
    return (input_variable, target_variable)


teacher_forcing_ratio = 0.5


def train(input_variable, target_variable, encoder, decoder, encoder_optimizer, decoder_optimizer, criterion,
          max_length=MAX_LENGTH):
    encoder_hidden = encoder.initHidden()

    encoder_optimizer.zero_grad()
    decoder_optimizer.zero_grad()

    input_length = input_variable.size()[0]
    target_length = target_variable.size()[0]

    encoder_outputs = Variable(torch.zeros(max_length, encoder.hidden_size))
    encoder_outputs = encoder_outputs.cuda() if use_cuda else encoder_outputs

    loss = 0

    for ei in range(input_length):
        encoder_output, encoder_hidden = encoder(
            input_variable[ei], encoder_hidden)
        encoder_outputs[ei] = encoder_output[0][0]

    decoder_input = Variable(torch.LongTensor([[SOS_token]]))
    decoder_input = decoder_input.cuda() if use_cuda else decoder_input

    decoder_hidden = encoder_hidden

    use_teacher_forcing = True if random.random() < teacher_forcing_ratio else False

    if use_teacher_forcing:
        # Teacher forcing: Feed the target as the next input
        for di in range(target_length):
            decoder_output, decoder_hidden, decoder_attention = decoder(
                decoder_input, decoder_hidden, encoder_outputs)
            loss += criterion(decoder_output, target_variable[di])
            decoder_input = target_variable[di]  # Teacher forcing

    else:
        # Without teacher forcing: use its own predictions as the next input
        for di in range(target_length):
            decoder_output, decoder_hidden, decoder_attention = decoder(
                decoder_input, decoder_hidden, encoder_outputs)
            topv, topi = decoder_output.data.topk(1)
            ni = topi[0][0]

            decoder_input = Variable(torch.LongTensor([[ni]]))
            decoder_input = decoder_input.cuda() if use_cuda else decoder_input

            loss += criterion(decoder_output, target_variable[di])
            if ni == EOS_token:
                break

    loss.backward()

    encoder_optimizer.step()
    decoder_optimizer.step()

    return loss.data[0] / target_length


import time
import math


def asMinutes(s):
    m = math.floor(s / 60)
    s -= m * 60
    return '%dm %ds' % (m, s)


def timeSince(since, percent):
    now = time.time()
    s = now - since
    es = s / (percent)
    rs = es - s
    return '%s (- %s)' % (asMinutes(s), asMinutes(rs))


def trainIters(encoder, decoder, n_iters, print_every=1000, plot_every=100, learning_rate=0.01):
    start = time.time()
    plot_losses = []
    print_loss_total = 0  # Reset every print_every
    plot_loss_total = 0  # Reset every plot_every

    encoder_optimizer = optim.SGD(encoder.parameters(), lr=learning_rate)
    decoder_optimizer = optim.SGD(decoder.parameters(), lr=learning_rate)
    training_pairs = [variablesFromPair(random.choice(pairs))
                      for i in range(n_iters)]
    criterion = nn.NLLLoss()

    for iter in range(1, n_iters + 1):
        training_pair = training_pairs[iter - 1]
        input_variable = training_pair[0]
        target_variable = training_pair[1]

        loss = train(input_variable, target_variable, encoder,
                     decoder, encoder_optimizer, decoder_optimizer, criterion)
        print_loss_total += loss
        plot_loss_total += loss

        if iter % print_every == 0:
            print_loss_avg = print_loss_total / print_every
            print_loss_total = 0
            print('%s (%d %d%%) %.4f' % (timeSince(start, iter / n_iters),
                                         iter, iter / n_iters * 100, print_loss_avg))

        if iter % plot_every == 0:
            plot_loss_avg = plot_loss_total / plot_every
            plot_losses.append(plot_loss_avg)
            plot_loss_total = 0

    showPlot(plot_losses)





def showPlot(points):
    plt.figure()
    fig, ax = plt.subplots()
    # this locator puts ticks at regular intervals
    loc = ticker.MultipleLocator(base=0.2)
    ax.yaxis.set_major_locator(loc)
    plt.plot(points)


def evaluate(encoder, decoder, sentence, max_length=MAX_LENGTH):
    input_variable = variableFromSentence(input_lang, sentence)
    input_length = input_variable.size()[0]
    encoder_hidden = encoder.initHidden()

    encoder_outputs = Variable(torch.zeros(max_length, encoder.hidden_size))
    encoder_outputs = encoder_outputs.cuda() if use_cuda else encoder_outputs

    for ei in range(input_length):
        encoder_output, encoder_hidden = encoder(input_variable[ei],
                                                 encoder_hidden)
        encoder_outputs[ei] = encoder_outputs[ei] + encoder_output[0][0]

    decoder_input = Variable(torch.LongTensor([[SOS_token]]))  # SOS
    decoder_input = decoder_input.cuda() if use_cuda else decoder_input

    decoder_hidden = encoder_hidden

    decoded_words = []
    decoder_attentions = torch.zeros(max_length, max_length)

    for di in range(max_length):
        decoder_output, decoder_hidden, decoder_attention = decoder(
            decoder_input, decoder_hidden, encoder_outputs)
        decoder_attentions[di] = decoder_attention.data
        topv, topi = decoder_output.data.topk(1)
        ni = topi[0][0]
        if ni == EOS_token:
            decoded_words.append('<EOS>')
            break
        else:
            decoded_words.append(output_lang.index2word[int(ni.cpu().numpy())])

        decoder_input = Variable(torch.LongTensor([[ni]]))
        decoder_input = decoder_input.cuda() if use_cuda else decoder_input

    return decoded_words, decoder_attentions[:di + 1]


def evaluateRandomly(encoder, decoder, n=10):
    for i in range(n):
        pair = random.choice(pairs)
        print('>', pair[0])
        print('=', pair[1])
        output_words, attentions = evaluate(encoder, decoder, pair[0])
        output_sentence = ' '.join(output_words)
        print('<', output_sentence)
        print('')


hidden_size = 256
encoder1 = EncoderRNN(input_lang.n_words, hidden_size)
attn_decoder1 = AttnDecoderRNN(hidden_size, output_lang.n_words,
                               1, dropout_p=0.1)

if use_cuda:
    encoder1 = encoder1.cuda()
    attn_decoder1 = attn_decoder1.cuda()

trainIters(encoder1, attn_decoder1, 75000, print_every=5000)

######################################################################

evaluateRandomly(encoder1, attn_decoder1)

output_words, attentions = evaluate(
    encoder1, attn_decoder1, "je suis trop froid .")
plt.matshow(attentions.numpy())


def showAttention(input_sentence, output_words, attentions):
    # Set up figure with colorbar
    fig = plt.figure()
    ax = fig.add_subplot(111)
    cax = ax.matshow(attentions.numpy(), cmap='bone')
    fig.colorbar(cax)

    # Set up axes
    ax.set_xticklabels([''] + input_sentence.split(' ') +
                       ['<EOS>'], rotation=90)
    ax.set_yticklabels([''] + output_words)

    # Show label at every tick
    ax.xaxis.set_major_locator(ticker.MultipleLocator(1))
    ax.yaxis.set_major_locator(ticker.MultipleLocator(1))

    plt.show()


def evaluateAndShowAttention(input_sentence):
    output_words, attentions = evaluate(
        encoder1, attn_decoder1, input_sentence)
    print('input =', input_sentence)
    print('output =', ' '.join(output_words))
    showAttention(input_sentence, output_words, attentions)


evaluateAndShowAttention("elle a cinq ans de moins que moi .")
evaluateAndShowAttention("elle est trop petit .")
evaluateAndShowAttention("je ne crains pas de mourir .")
evaluateAndShowAttention("c est un jeune directeur plein de talent .")
```

输出：（其中 “>” 符号后面的句子表示待翻译的法文，“=” 符号后面的句子是真是的英文翻译，“<” 符号后面的句子是机器翻译后的结果）

```
Reading lines...
Read 135842 sentence pairs
Trimmed to 10853 sentence pairs
Counting words...
Counted words:
fra 4489
eng 2925
['elles l adorent .', 'they re very fond of him .']
1m 1s (- 14m 14s) (5000 6%) 2.9178
2m 1s (- 13m 9s) (10000 13%) 2.3213
3m 1s (- 12m 7s) (15000 20%) 2.0001
4m 2s (- 11m 7s) (20000 26%) 1.7451
5m 3s (- 10m 6s) (25000 33%) 1.5652
6m 4s (- 9m 6s) (30000 40%) 1.3846
7m 5s (- 8m 6s) (35000 46%) 1.2464
8m 6s (- 7m 5s) (40000 53%) 1.1280
9m 8s (- 6m 5s) (45000 60%) 1.0181
10m 9s (- 5m 4s) (50000 66%) 0.9166
11m 11s (- 4m 4s) (55000 73%) 0.8402
12m 13s (- 3m 3s) (60000 80%) 0.7588
13m 15s (- 2m 2s) (65000 86%) 0.7200
14m 16s (- 1m 1s) (70000 93%) 0.6341
15m 18s (- 0m 0s) (75000 100%) 0.5828

> nous allons avoir un bebe .
= we are going to have a baby .
< we are going to have a baby . <EOS>

> j y suis habitue .
= i m accustomed to this .
< i m used to it . <EOS>

> je ne te crains plus .
= i m not scared of you anymore .
< i m not scared of you anymore . <EOS>

> vous etes riches .
= you are rich .
< you re rich . <EOS>

> vous n y etes pas bon .
= you re not good at this .
< you re not good at this . <EOS>

> je ne reste pas .
= i m not staying .
< i m not joking . <EOS>

> elle n est pas chanteuse .
= she s no singer .
< she s no singer . <EOS>

> je suis motive .
= i m motivated .
< i m motivated . <EOS>

> tu es agressif .
= you re aggressive .
< you re aggressive . <EOS>

> nous sommes en route .
= we re on our way .
< we re on a way . <EOS>

input = elle a cinq ans de moins que moi .
output = she is five years younger than me . <EOS>
input = elle est trop petit .
output = she s too short . <EOS>
input = je ne crains pas de mourir .
output = i m not scared of dying . <EOS>
input = c est un jeune directeur plein de talent .
output = he s a young young player . <EOS>
```

输出图像如下：

误差效果图：

<center>

![](http://images.iterate.site/blog/image/20190621/fIcXGrXbOcma.png?imageslim){ width=55% }

</center>

注意力的权重可视化：

<center>

![](http://images.iterate.site/blog/image/20190621/vCXizL0YDukA.png?imageslim){ width=55% }

</center>

<center>

![](http://images.iterate.site/blog/image/20190621/gKsel8SPXzDM.png?imageslim){ width=55% }

</center>


<center>

![](http://images.iterate.site/blog/image/20190621/RGhwV1kVTEob.png?imageslim){ width=55% }

</center>

<center>

![](http://images.iterate.site/blog/image/20190621/Hfmw5CT42tac.png?imageslim){ width=55% }

</center>



<center>

![](http://images.iterate.site/blog/image/20190621/UAxgDBaibsn4.png?imageslim){ width=55% }

</center>



关于上面这个代码想知道的：

1. <span style="color:red;">`from __future__ import unicode_literals, print_function, division` 还有什么吗？要总结下 `__future__` 。</span>
2. <span style="color:red;">`unicodedata` 需要了解下。</span>
3. <span style="color:red;">`re.sub`  还是要补充下的</span>
4. <span style="color:red;">`nn.Embedding` 不清楚。</span>
5. <span style="color:red;">`self.embedding(input).view(1, 1, -1)` 没明白。</span>
6. <span style="color:red;">`output, hidden = self.gru(output, hidden)` 没明白。</span>
7. <span style="color:red;">上面提到的几个 RNN 的结构还是有点没明白。而且训练的过程也没有怎么看明白。</span>




Attention 机制把源句子中对生成句子重要的关键词的权重进行提高，可以更准确地应答。Attention 机制应用在聊天机器人，机器翻译等领域，大大提高了翻译效果。

<span style="color:red;">Attention 机制还是要更多的了解下。现在对于这个还没有怎么理解。</span>

采用注意力集中机制能够让网络在解码的时候着重输出某些重要的部分。如图表示编码输入到解码输出：<span style="color:red;">没明白。</span>

<center>

![](http://images.iterate.site/blog/image/20190621/iFuScM9bn6pI.png?imageslim){ width=55% }

</center>


我们可以通过注意力权重可视化来观察每个单词的权重，来确定编码输出的重点部分，有利于我们理解网络的注意力在哪部分。

从图中可以看到每句话中的每个单词占的比重不一样，所以编码重点输出的英文句子也有所差别。





# 相关

- 《深度学习框架 Pytorch 快速开发与实战》
- 需要的数据如下：链接：https://pan.baidu.com/s/1_ZpE-XqPkY6SziJUhRsoDA  提取码：68qc
