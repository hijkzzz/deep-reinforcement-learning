# Prioritized Experience Replay

> [Prioritized Experience Replay](https://arxiv.org/pdf/1511.05952.pdf)

经验回放让在线强化学习代理能够记住并重用过去的经验。 在先前的工作中，经验转移是从回放存储器统一采样的。 然而，这种方法只是以与它们最初经历的频率相同的频率回放样本，而不管其重要性如何。 在本文中，我们开发了一个优先考虑体验的框架，以便更频繁地重放重要的转换，从而更有效地学习。 我们在Deep Q-Networks（DQN）中使用优先级经验重放，这是一种在多种Atari游戏中实现人类性能的强化学习算法。 具有优先经验回放的DQN在49场比赛中的41场比赛中实现了最好效果，超过了DQN和均匀回放。

## 方法

### 优先回放

优先回放的核心组成部分是衡量样本重要性的指标。 一个理想化的标准是RL代理可以从当前样本（预期的学习进度）中学习到的东西。 虽然这个指标不能直接获得，但合理的等价物是样本的TD误差 $$δ$$ 的大小。

但是这个算法有一些缺点：首先，为了避免在整个重放存储器上进行费时的扫描，TD错误仅针对重放的样本进行更新。一种后果是，第一次访问时TD误差低的样本可能不会被长时间重放。此外，它对噪声尖峰很敏感\(例如，当奖励是随机的\)，这可能会因自举而加剧。

为了克服这些问题，我们引入了一种随机抽样方法，在纯贪婪优先和均匀随机抽样之间进行插值。我们确保被采样的概率在转变的优先级上是单调的，同时保证非零概率，即使是最低优先级的转变。具体来说，我们定义了采样的概率：

$$P(i)=\frac{p_{i}^{\alpha}}{\sum_{k} p_{k}^{\alpha}}$$

### 退火偏差

随机更新的期望值的估计依赖于与期望分布相同的更新。优先级重播引入了偏差，因为它以一种不受控制的方式更改了这个分布，因此更改了估计将收敛到的解决方案\(即使策略和状态分布是固定的\)。我们可以通过使用重要采样（IS）权重来纠正这种偏差：

$$w_{i}=\left(\frac{1}{N} \cdot \frac{1}{P(i)}\right)^{\beta}$$

$$\beta$$是退火参数，在实践中，我们将$$\beta$$从其初始值$$\beta_0$$到线性退火到1

## 伪代码

![](../../.gitbook/assets/image-75.png)

