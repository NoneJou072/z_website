---
title: 关于 Goal-Conditioned RL 中子目标选择的问题
description: 关于 Goal-Conditioned RL 中子目标选择的问题
toc: true
authors:
  - Haoran Zhou
tags: ["rl"]
categories:
series:
date: '2023-11-21T13:11:22+08:00'
lastmod: '2023-11-23T13:11:22+08:00'
featuredImage:
draft: false
---

{{< katex >}}

## GCRL with Sub-goal Selection / Generation
> 本文翻译与参考自: [Goal-Conditioned Reinforcement Learning: Problems and Solutions](https://arxiv.org/pdf/2201.08299.pdf)

由于难以实现长期目标，可以把目标分割成许多短期目标来帮助智能体到达长期目标。
在期望目标被替换后收集经验，就是将环境的目标分布从$f$改成$p_g$，目标函数变成

$$
J(\pi)=\mathbb{E}_{\textstyle{s_{t+1}\sim \mathcal{T}(\cdot\mid s_t,a_t),\atop a_{t} \sim \pi(\cdot\mid s_{t},g),g\sim f}}\left[\sum_t \gamma^tr(s_t,a_t,g)\right]
$$

$f$可以是一个规则，能够从过去的经验中选择$g$，或者是根据某种准则学习出的一个函数。

下面介绍几种选择子目标的方法。
### 1. 中间难度 Intermediate difficulty
*准确评估到达目标的难度有助于确定在智能体的能力范围内学习什么，一个合理的目标应该超出智能体的范围（why？）*

* *Automatic goal generation for reinforcement learning agents.* 根据奖励给难度打分，用来把目标标记成正/负采样，然后训练一个GAN模型来生成具有合理难度分数的目标。
* *Learning with amigo: Adversarially motivated intrinsic goals.* 提出了一种 teacher-student 框架。teacher充当目标提供者，尝试提供难度递增的目标。teacher从student在提供的目标上的表现中学习，student从环境中得到的外部奖励和teacher中得到的内部奖励学习。
* *Automatic curriculum learning through value disagreement.* 把Q函数认知的不确定性看成是实现目标难度的衡量，用于构建一个分布来采样目标。当不确定性过高时，目标应该处于策略的知识边界，以一个合理的难度来学习。

### 2. 探索意识 Exploration awareness
由于稀疏奖励限制了策略的学习效率，我们应该提高策略的探索能力，尽可能覆盖没有遇到的目标和状态。
在GCRL，许多研究者们提议**使用子目标采样**来得到一个更好的探索能力。

* *Maximum entropy gain exploration for long horizon multi-goal reinforcement learning.* 提出采样使过去**已实现目标**的熵最大化的目标，以提高目标空间（goal space）的覆盖范围。（这一点和SAC的目的很像）
* *State-covering self-supervised reinforcement learning.* 训练一个自监督的生成式模型来生成行为目标，该目标在过去的经验中对已实现目标进行了近似均匀的倾斜，表现为最大化**期望目标**分布的熵。
* *Unsupervised controlthrough non-parametric discriminative rewards.* 选择从不同的replay buffer中均匀地采样过去已实现的目标作为行为目标，这些储存的目标间的距离要尽可能地远。
* *Visual reinforcement learning with imagined goals.* 选择从经验回放中均匀地采样过去已实现的目标，用这些历史目标拟合生成式模型来生成行为目标。
* *Exploration via hindsight goal generation* 通过 Wasserstein Barycenter 问题，生成了一组事后目标以进行探索。把生成的目标作为隐性课程，可以有效地使用当前值函数来学习。
* *Dynamical distance learning for semi-supervised and unsupervised skill discovery* 学习一个距离函数，并从replay buffer中选择距离初始状态最远的目标来提高探索效率。

### 3. 从经验中搜索 Searching from experience.
历史经验包含导向实现某些目标的路径点，可以使人类或智能体获益。

* *Hindsight planner.* 从一个采样的回合中选择 $k$ 个关键的中间路径点，并训练一个RNN网络，根据给定的起始和终止点来生成序列。再应用这些中间点作为期望目标的序列来与环境交互。
* *Search on the replay buffer: Bridging planning and reinforcement learning.* 在 replay buffer 上建立图，把状态作为节点，从起始状态到目标状态的距离作为边的权重。该工作利用了二值奖励下的状态值可以近似成到目标的距离这一直觉，使用图搜索去寻找路径点序列来实现目标，并不断通过在路径点上学习的策略来采取动作。

### 4. Model-based planning 和 Learn from experts.
model-based RL 与 learning from demonstrations 不在笔者的考虑范围内，故不在此翻译。


## Relabeling in GCRL

与子目标选择类似，relabeling也替换了初始的期望目标以提高学习效率。二者的区别是：
* Relabeling 关注于在训练前替换经验容器中的历史数据；
* Sub-goal Selection 关注于改变采样经验的分布。
为了实现重标记操作，理论上需要一个重标记函数 $h$ 去替换从 replay buffer 采样出的 transition 中的目标，并重新计算奖励
$$
(s_t, a_t, g_t, r_t) \gets (s_t, a_t, h(·), r(s_t, a_t, h(·))) 
$$

### 1. 事后回放 Hindsight relabeling.
该方法可以追溯到最著名的 Hindsight experience replay(HER)，想法源自于人类从失败的经验中来学习。HER 使用replay buffer相同轨迹中已实现目标来替换期望目标，同时减轻稀疏奖励的问题。

* *Curriculum-guided hindsight experience replay.* 提出 CHER，使用课程重标记方法自适应地从失败经验中选择重标记目标。课程的评价标准是与期望目标的相似度和目标的多样性。

* *Goal densitybased hindsight experience prioritization for multi-goal
robot manipulation reinforcement learning.* 为已实现目标学习了一个稠密模型，用于优先标记少见的目标。以实现在少见经验中探索来提高采样效率。

### 2. Relabeling by learning.
* *Guided goal generation for hindsight multigoal reinforcement learning*
从过去的经验选择重标记目标限制了重标记目标的多样性，为了减轻该问题并能够重标记未见过的目标，这篇文章通过隐式地建模策略表现和已实现目标之间的关系来训练一个条件生成式RNN网络，用于根据当前策略的平均返回值生成重标记目标。

### 3. 先见之明 Foresight.
人类不仅可以从失败中学习，还可以基于当前状态对未来作出规划。

* *Mapgo: Modelassisted policy optimization for goal-oriented tasks.*  学习一个动态模型用来生成用于重标记的虚拟轨迹。先见目标重标记可以防止标记局限于历史数据的同质目标，规划出当前策略可以实现的新目标。
