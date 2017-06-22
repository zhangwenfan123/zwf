---
layout: post
title:  "AI that has Values"
date:   2016-10-14 23:00:00
categories: Translation
tags: Translation
comments: false
---
* content
{:toc}

让人工智能完美运行：我们怎么给人工智能系统赋予价值观？
 <!--more-->

Keeping AI Well Behaved:How Do We Engineer An Artificial System That Has Values?


**In Brief**

**摘要：**

> It is estimated that some 10 million self-driving cars will be on the road by the close of 2020, which raises questions about how an AI will respond in lethal situations.
> 预计到2020年为止，全球将会有超过100万辆人工智能驱动的无人车上路，同时也引出了人工智能在发生致命事故时会怎样反应的问题。


> In this exclusive interview, FLI researcher David Parkes discusses his work to engineer a value-aligned AI system.
> 在这篇专访中，FLI的研究员大卫.帕吉斯讨论了他构建带有价值观的人工智能系统的一些工作。


-----
**Teaching AI to make Decisions**
**教人工智能进行讨论**


>Imagine you’re sitting in a self-driving car that’s about to make a left turn into on-coming traffic. One small AI system in the car will be responsible for making the vehicle turn, one system might speed it up or hit the brakes, other systems will have sensors that detect obstacles, and yet another system may be in communication with other vehicles on the road. Each system has its own goals — starting or stopping, turning or traveling straight, recognizing potential problems, etc. — but they also have to all work together toward one common goal: turning into traffic without causing an accident.
>想象一下你正坐在一辆自我驱动的无人车中，即将左转面对迎面而来的车流。车内的人工智能系统将会负责控制转向的过程，一个系统也许会加速或者是刹车，另一些系统将会通过传感器探测障碍物，还有一些系统也许会和路上的其他车辆进行通信。每一个系统都有其自己的目标 — 启动或者停止，转向或者直接行驶，分辨出潜在的问题等等。— 但是他们都需要被整合到一起朝向同一个目标：在不发生事故的情况下转换为正常的交通。


>Harvard professor and Future of Life researcher, David Parkes, is trying to solve just this type of problem. Parkes told FLI, “The particular question I’m asking is: If we have a system of AIs, how can we construct rewards for individual AIs, such that the combined system is well behaved?”
>哈佛教授，未来生活研究员，大卫.帕吉斯，正在试图解决这一类型的问题。帕吉斯告诉FLI，“实际上我正要解决的问题是：如果我们有一个人工智能系统，我们如何才能为其中每一个独立的系统构建奖励模式，以便于整合的系统可以完美的运行？”


>Essentially, an AI within a system of AIs—like that in the car example above—needs to learn how to meet its own objective, as well as how to compromise so that it’s actions will help satisfy the group objective. On top of that, the system of AIs needs to consider the preferences of society. For example, it needs to determine if the safety of the passenger in the car or a pedestrian in the crosswalk is a higher priority than turning left.
>尤其是，一个人包含很多子系统的工智能系统 — 比如上面无人车的例子中说的 — 需要学会怎样才能完成自己的目标，同时还要学会妥协以便于其子系统的行为能对完成宏观的目标提供帮助。在这些问题之上，包含多个子系统的人工智能需要将社会的价值偏好考虑进来。比如，当其需要决定在转弯的过程中究竟是车内乘客的安全更为重要还是人行道上行人的安全更重要。


>Because environments like a busy street are so complicated, an engineer can’t just program an AI to act in some way to always achieve its objectives. AIs need to learn proper behavior based on a rewards system. “Each AI has a reward for its action and the action of the other AI,” Parkes explained. With the world constantly changing, the rewards have to evolve, and the AIs need to keep up not only with how their own goals change, but also with the evolving objectives of the system as a whole.
>因为像忙碌的街道这样的环境太复杂，工程师无法通过简单的编程告诉人工智能去如何通过某种特定的行为模式去反应，同时又总能完成其目标。人工智能需要通过基于奖励的系统去学习合适的行为。“每一个人工智能的行为都有自己的激励模式，同时其他系统也有。”

-----

**Making an Evolving AI**
**构建一个自我进化的智能系统**


>The idea of a rewards-based learning system is something most people can likely relate to. Who doesn’t remember the excitement of a gold star or a smiley face on a test? And any dog owner has experienced how much more likely their pet is to perform a trick when it realizes it will get a treat. A reward for an AI is similar.
>基于奖励的学习系统的想法其实也和大多数人的经历相关。谁不记得在考试之后得到金色小星星或者一个笑脸时的兴奋呢？而且每一个有宠物狗的主人都有这样的经验：当宠物狗发现某种行为可以获得奖励的时候，它们是多么愿意执行某种特定的行为。对于人工智能的奖励也是类似的道理。


>A technique often used in designing artificial intelligence is reinforcement learning. With reinforcement learning, when the AI takes some action, it receives either positive or negative feedback. And it then tries to optimize its actions to receive more positive rewards. However, the reward can’t just be programmed into the AI. The AI has to interact with its environment to learn which actions will be considered good, bad, or neutral. Again, the idea is similar to a dog learning that tricks can earn it treats or praise, but misbehaving could result in punishment.
>在设计人工智能系统的时候有一种常用的技术 — 强化学习。在强化学习的作用下，当人工智能采取行动的时候，其会获得积极的或者消极的反馈。而且其随后就会通过最优化其行为去获得更为积极的奖励。然而，这奖励不能直接被编程到AI的系统中。这些人工智能必须要和周围的环境进行互动，去学习哪些行为被认为是好的，坏的或者中性的。同样，这个想法和训练宠物狗的过程类似：执行某种行为获得奖励或者赞美，做错了则可能会引发惩罚。


>More than this, Parkes wants to understand how to distribute rewards to subcomponents (the individual AIs) in order to achieve good system-wide behavior. How often should there be positive (or negative) reinforcement, and in reaction to which types of actions?
>不仅如此，帕吉斯想要理解如何通过将奖励分发给子系统（独立的人工智能）以便于能够产生系统层面的合理行为。正向强化或者负向强化的频率要怎么控制，而且应该基于哪种行为去强化呢？

-----

```
"Rather than programming a reward specifically into the AI, Parkes shapes the way rewards flow from the environment to the AI in order to promote desirable behaviors as the AI interacts with the world around it."

“相比于将特定的奖励机制编程到人工智能，帕吉斯重塑了环境对人工智能施加奖励的途径，这样一来就可以在系统和环境互动的过程中提升目标行为的预期。”
```
-----

>For example, if you were to play a video game without any points or lives or levels or other indicators of success or failure, you might run around the world killing or fighting aliens and monsters, and you might eventually beat the game, but you wouldn’t know which specific actions led you to win. Instead, games are designed to provide regular feedback and reinforcement so that you know when you make progress and what steps you need to take next. To train an AI, Parkes has to determine which smaller actions will merit feedback so that the AI can move toward a larger, overarching goal.
>例如，如果你正在玩一款视频游戏，但是没有分数，没有命数限制或者其他的成功或者失败的提示，你也许会满世界跑打外星人，杀怪玩，也许最后游戏被你通关了，但是你不知道具体什么样的行为可以让你获得胜利。相反，游戏通常被设计成频繁的进行反馈和强化，这样你可以知道，你正在获得一定的进展而且你知道下一步你要做什么。为了训练一个人工智能系统，帕吉斯必须决定哪些小的行为会产生反馈以至于人工智能能够朝向更加宏观的目标。


>But this is all for just one AI. How do these techniques apply to two or more AIs?
>但是这只是对于单个人工智能而言。这些技术怎样被应用于两个或者更多的人工智能混合的系统呢？

-----

**Gaming the System**
**用博弈训练整个系统**

>Much of Parkes’ work involves game theory. Game theory helps researchers understand what types of rewards will elicit collaboration among otherwise self-interested players, or in this case, rational AIs. Once an AI figures out how to maximize its own reward, what will entice it to act in accordance with another AI?
>帕吉斯很大一部分工作包含博弈论。博弈论能帮助研究者理解哪些奖励的类型会引出“自私的”局中人的合作行为，在当前的情况中，是理性的人工智能系统。一旦人工智能搞清楚如何去让自身的奖励最大化，什么会影响其系统和其他人工智能系统的协同呢？


>To answer this question, Parkes turns to an economic theory called mechanism design.
>为了解答这些问题，帕吉斯转向了对一种叫机制设计的的经济学理论的研究。


>Mechanism design theory is a Nobel-prize winning theory that allows researchers to determine how a system with multiple parts can achieve an overarching goal. It is a kind of “inverse game theory.” How can rules of interaction – ways to distribute rewards, for instance – be designed so individual AIs will act in favor of system-wide and societal preferences? Among other things, mechanism design theory has been applied to problems in auctions, e-commerce, regulations, environmental policy, and now, artificial intelligence.
>机制设计理论是一个获得过诺奖的理论，这个理论允许研究者去决定一个多部分组成的系统去如何获取宏观的目标。这是一种“反向博弈论”。人工智能系统之间的互动规则 — 在这里是发放奖励的方法或者途径 — 如何被设计用来推动系统层面或者社会层面的偏好？在这些问题中，机制设计理论已经被应用到很多实际的领域中，电子商务，法律法规，环境政策，现在，人工智能。


>The difference between Parkes’ work with AIs and mechanism design theory is that the latter requires some sort of mechanism or manager overseeing the entire system. In the case of an automated car or a drone, the AIs within have to work together to achieve group goals, without a mechanism making final decisions. As the environment changes, the external rewards will change. And as the AIs within the system realize they want to make some sort of change to maximize their rewards, they’ll have to communicate with each other, shifting the goals for the entire autonomous system.
>帕吉斯人工智能系统的工作和机制设计理论的不同在于后者要求某种机制或者管理者来监督整个系统。而在自动驾驶汽车或者无人车的例子中，其中包含的人工智能系统需要一起协作区完成整个团体的目标，并不需要一个机制去做最终的决策。当环境改变的时候，内部的奖励也会改变。并且当这些人工智能系统意识到他们需要做一些改变，以减少其奖励，他们会相互进行通信，将目标和整个自动驾驶系统做整合协调。


>Parkes summarized his work for FLI, saying, “The work that I’m doing as part of the FLI grant program is all about aligning incentives so that when autonomous AIs decide how to act, they act in a way that’s not only good for the AI system, but also good for society more broadly.”
>帕吉斯对FLI总结了其工作，他说，“我正在做的事情，作为FLI整个项目的一部分，其实就是协调激励以便于自动的人工智能系统可以决定如何去行动，而且不仅仅是基于为了自身系统的利益，更要基于广义上社会的利益。”


>Parkes is also involved with the One Hundred Year Study on Artificial Intelligence, and he explained his “research with FLI has informed a broader perspective on thinking about the role that AI can play in an urban context in the near future.” As he considers the future, he asks, “What can we see, for example, from the early trajectory of research and development on autonomous vehicles and robots in the home, about where the hard problems will be in regard to the engineering of value-aligned systems?”
>帕吉斯同时也参与了“人工智能百年研究”项目，他这样解释自己的工作“在FLI的研究为自己提供了一个更加宽广的角度，以便思考在未来的城市生活中人工智能可以扮演怎样的角色。”他同时也对未来有所顾虑，他问道，“我能可以看到什么，比如，早期的研究路径和自动驾驶汽车以及家庭机器人，包括在有价值倾向的人工智能系统工程中，困难的问题又会在什么地方出现呢？”
