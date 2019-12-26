---
title: 29 Moore-Penrose 伪逆
toc: true
date: 2019-05-07
---
# 可以补充进来的

- 基本没有理解，中间步骤都要补充下。
- Moore-Penrose 求解欠定线性方程


# Moore-Penrose 伪逆


对于非方矩阵而言，其逆矩阵没有定义。

假设在下面的问题中，我们希望通过矩阵 $\boldsymbol{A}$ 的左逆 $\boldsymbol{B}$ 来求解线性方程：

$$
\boldsymbol{A x}=\boldsymbol{y}\tag{2.45}
$$

等式两边左乘左逆 $\boldsymbol{B}$ 后，我们得到

$$
\boldsymbol{x}=\boldsymbol{B} \boldsymbol{y}\tag{2.46}
$$

取决于问题的形式，我们可能无法设计一个唯一的映射将 $\boldsymbol{A}$ 映射到 $\boldsymbol{B}$ 。

如果矩阵 $\boldsymbol{A}$ 的行数大于列数，那么上述方程可能没有解。如果矩阵 $\boldsymbol{A}$ 的行数小于列数，那么上述矩阵可能有多个解。

Moore-Penrose 伪逆(Moore-Penrose pseudoinverse)使我们在这类问题上取得了一定的进展。

矩阵 $\boldsymbol{A}$ 的伪逆定义为：

$$
\boldsymbol{A}^{+}=\lim _{a \searrow 0}\left(\boldsymbol{A}^{\top} \boldsymbol{A}+\alpha \boldsymbol{I}\right)^{-1} \boldsymbol{A}^{\top}\tag{2.47}
$$

<span style="color:red;">为什么是这么奇怪的一个式子？</span>


计算伪逆的实际算法没有基于这个定义，而是使用下面的公式：

$$
\boldsymbol{A}^{+}=\boldsymbol{V} \boldsymbol{D}^{+} \boldsymbol{U}^{\top}\tag{2.48}
$$

<span style="color:red;">好吧，这个式子又是哪里来的？</span>



其中，矩阵 $\boldsymbol{U}$、$\boldsymbol{D}$ 和 $\boldsymbol{V}$ 是矩阵 $\boldsymbol{A}$ 奇异值分解后得到的矩阵。对角矩阵 $\boldsymbol{D}$ 的伪逆 $\boldsymbol{D}^{+}$ 是其非零元素取倒数之后再转置得到的。<span style="color:red;">嗯，跟奇异值分解有关了，不过为什么呢？中间的步骤还是要补充下的。</span>

当矩阵 $\boldsymbol{A}$ 的列数多于行数时，使用伪逆求解线性方程是众多可能解法中的一种。特别地，$\boldsymbol{x}=\boldsymbol{A}^{+} \boldsymbol{y}$ 是方程所有可行解中欧几里得范数 $\|x\|_{2}$ 最小的一个。<span style="color:red;">为什么呢？而且作为最小的一个有什么好处吗？</span>

当矩阵 $\boldsymbol{A}$ 的行数多于列数时，可能没有解。在这种情况下，通过伪逆得到的 $\boldsymbol{x}$ 使得 $\boldsymbol{Ax}$ 和 $\boldsymbol{y}$ 的欧几里得距离 $\|\boldsymbol{A x}-\boldsymbol{y}\|_{2}$ 最小。



# 相关

- 《深度学习》花书
