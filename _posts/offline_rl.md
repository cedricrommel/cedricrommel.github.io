---
title: "Offline data-driven optimal control"
layout: post
image: /assets/images/marginal_likelihood_illustration.png
headerImage: True
tag:
    - model-based RL
    - system identification
    - ODE learning
    - model exploitatio
author: cedricrommel
category: project
externalLink: false
description: "Main issues that arise in offline model-based RL that I could investigate during my PhD."
# jemoji: '<img class="emoji" title=":ramen:" alt=":ramen:" src="https://assets.github.com/images/icons/emoji/unicode/1f35c.png" height="20" width="20" align="absmiddle">'
---

## Offline data-driven optimal control:

-   [Data-driven control](#data-driven-control)
-   [Flexibly interpretable system identification](#flexible-and-interpretable-system-identification)
-   [Offline planning with approximate dynamics](#offline-planning-with-approximate-dynamics)

#### Data-driven control

The core idea of reinforcement learning (RL) is to use data to learn how to control an uncertain/unknown system so as to optimize some performance metric. In its model-based flavor, also known as data-driven control, the system dynamics is modeled explicitly, which allows to inject prior knowledge into the problem formulation. When such class of approaches are applicable, they may greatly improve data efficiency compared to model-free RL and sometimes lead to more compact formulations. The original problem then breaks down into two subproblems:
1. *system identification*: using the available data to learn the model dynamics;
2. *optimal control/planning*: using the learned model to look for the best performing controls/policy given the approximated dynamics.

During my PhD thesis, I have investigated how to make both these steps more reliable for industry standards, focusing on the optimization of fuel-efficient aircraft trajectories for real airliners.<!-- A possible application of this family of approaches is the planning of fuel efficient trajectories for civil aircrafts, which I have been particularly interested in during my PhD. -->

#### Flexible and interpretable system identification

System identification can basically be sumarized as *learning differential equations* and can be solved using standard supervised learning approaches. It is often cast using off-the-shelf parametric models, mostly based on first principles (*e.g.* laws of motion), and parameters are inferred using least-squares or maximum likelihood estimation.<!-- For example, consider the following dynamical system describing the evolution of an arcraft during climbing and obtained from the 2nd Newton law:
$$\left\{ \right.$$ --> However, it often happens that parts of the dynamical system have no generic analytical models available (*e.g.* aerodynamic forces, friction, ...), or would require a fine-grained model, which could be too costly to derive (*e.g.* engine performance, finite elements model of a cooling system, ...). A possible solution in these situations could be to use flexible black-box estimators, such as neural networks, to accurately fit these unknown elements as functions of the state and control variables. This is not always an option in industrial applications though for three main reasons:
1. Interpretability is often a very important feature, specially when dealing with critical systems for which the model needs to be trusted downstream ;
2. If the black-box models used are not sufficiently constrained, they might lead to solutions which closely fit the data but don't respect basic physical principles (*e.g.* positivity of forces), which can lead to unexpected and unsafe behaviors in real conditions ;
3. Planning is an expensive task and when it is constrained by a computation-budget, having optimization constraints based on a large black-box model might be too costly.

With these practical constraints in mind, I have investigated ways of learning parametric dynamics models where the model structure is partly learned from the data together with the parameters values. This was achieved by casting the system identification as a $L_{1,2}$-regularized linear regression problem on a dictionary of non-linear features devired from state and control variables. This allowed to obtain expressive yet interpretable and physically plausible models capable of handling highly non-linear dynamics. More details can be found in [Rommel et. al 2017](https://hal.inria.fr/hal-01816400/document).

#### Offline planning with approximate dynamics

Ideally, system identification and planning should be iterated many times in closed-loop, so that the model is constantly refined and adapts to changes in the environment as it interacts with it. However, this is not possible in many real-world applications <!-- (such as the optimization of fuel-efficient aircraft trajectories) --> because it is either too expensive, too risky or just unfeasible. In these cases, system identification is carried mainly once using historical data and planning is done offline.
In this case, a typical problem that may arise is *model exploitation*: the optimization might exploit inaccuracies of the approximate dynamics since it has no information regarding model uncertainty. For example, the optimization might drive the system into state-control regions where little data was available for the system identification, leading to unreliable solutions. To this matter I have investigated new regularizers which penalize solutions going in low-density regions of the state space (see [Rommel et al. 2021](https://hal.inria.fr/hal-01816407/document) and [Rommel et al. 2019](https://hal.inria.fr/hal-01819749/document)).
<!-- For this, I proposed the mean marginal likelihood, a novel proxy for probability densities over spaces of vector-valued univariate functions. Efficient parametric and non-parametric estimators of this quantity were also derived and proven to converge to the true quantity of interest. -->
