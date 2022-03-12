---
layout: single
title: "A measure-theoretic basis for probability"
date: 2022-01-16 07:55:43 +0000
categories: math probability
excerpt: "The axiom of choice, Vitali sets and events with no probability"
header:
  # Enter post address in https://cards-dev.twitter.com/validator if title/image is out-of-date when sharing on twitter
  # https://www.linkedin.com/post-inspector/ for linkedin
  image: "/assets/images/2022-01-17-measure-theoretic-probability/math.jpg"
  overlay_image: "/assets/images/2022-01-17-measure-theoretic-probability/math.jpg"
  overlay_filter: rgba(0, 0, 0, 0.5)
  caption: "[Max Pixel / Public Domain](https://www.maxpixel.net/Blackboard-Classroom-Symbols-Lesson-Pie-Chart-Math-1500720)"
---

What's wrong with the notion of probability we were taught in school? Why do we need to base it on _measure theory_ instead? In this post, I want to show you how our current notion is limited, and how, if we try to extend it in a naive way, we run up against an issue: the existence of events for which we cannot assign any probability. This will be the motivation for us to refine our understanding, using the notion of a $\sigma$-algebra.

## Our current notion of probability

### Discrete

When there are only finitely many things that can happen, we can just list out the probability of each. For example, if the experiment is throwing a die, we can create a table as below:

| Outcome         | 1             | 2             | 3             | 4             | 5             | 6             |
| --------------- | ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| **Probability** | $\frac{1}{6}$ | $\frac{1}{6}$ | $\frac{1}{6}$ | $\frac{1}{6}$ | $\frac{1}{6}$ | $\frac{1}{6}$ |

We then say that the probability of rolling an even number is
$$ P(\{2,4,6\}) = \frac{1}{6} + \frac{1}{6} + \frac{1}{6} = \frac{1}{2} $$

### Continuous

But what if the space of outcomes is infinitely large? For example, say $X$ is the height of a new-born baby. Measurements of such natural phenomena often follow the _bell curve_, i.e., most babies will have height close to the average, and very few will be much shorter or much taller than the average.

![baby_height_gaussian.png](/assets/images/2022-01-17-measure-theoretic-probability/baby_height_gaussian.png)

We then say that the probability that a baby has a height between $a$ and $b$ is
$P(a < x < b) = \int\limits_{a}^{b}f(x)dx$.

Here, $f$ is called the _density function_.

## The limitation of this approach

This looks very reasonable and in fact works really well for most practical problems. But such an approach of defining a _density_ only works if there is a way to "order" the space of outcomes. But consider the set of all functions from the set of real numbers $R$ to the range $[0, 1]$.
$f:R \rightarrow [0, 1]$

We want to to find the probability that, if we choose a function at random, the function will map negative numbers to the range $[0, 0.5]$ and positive numbers to the range $[0.5, 1]$. Unfortunately, there's no good way to define a density function like we did above.

## A naive alternative

We might start to wonder, can we do the same thing we did in the discrete case? This time, instead of creating a huge table mapping every outcome to a probability measure (since the number of outcomes is infinite, the probability of obtaining any one outcome is exactly 0), we will define a function $P$ (for probability) which takes any set of outcomes and gives us a number between 0 and 1 which represents its probability.

$P: 2^\Omega \rightarrow [0, 1]$, where $\Omega$ is the set of all possible outcomes and $2^\Omega$ is the set of all events.

In order for this function to conform to our understanding of probability, we want it to have these properties as well.

1. $P(\Omega) = 1$, $P(\phi) = 0$. The probability of $\Omega$ (obtaining any outcome) should be 1, and the probability of $\phi$ (not obtaining any outcome) should be 0.
2. $P(A^c) = 1-P(A)$. The probability of an event not occuring should be 1 - the probability of the event occuring.
3. If we have disjoint sets $$ \{A*i\}*{i=1}^{\infty} $$, $$ \bigcup\limits*{i=1}^{\infty}A_i = \sum\limits*{i=1}^{\infty}P(A_i) $$. The probability that any one of multiple events happen should be the sum of their individual probabilities.

But it turns out we cannot find such a function for even the most fundamental distribution, the uniform distribution.

![uniform.png](/assets/images/2022-01-17-measure-theoretic-probability/uniform.png)

To be precise, we want a function $P$ which satisfies the requirements 1 to 3 above and where
$P([a, b]) = b-a$ for $0 \leq a \leq b \leq 1$

But such a function does not exist. Let us see why.

## Constructing the Vitali set

First, let us assume that such a function $P$ does exist. We will construct a set $V$, called the Vitali set, named after the Italian mathematician [Giuseppe Vitali](https://en.wikipedia.org/wiki/Giuseppe_Vitali) and show that we cannot define its probability. That is, we cannot define the probability that, when a number is randomly picked from the range $[0,1]$, it will be in $V$.

We will put each number in $[\frac{1}{4},\frac{3}{4}]$ in one of an infinite set of boxes in the following way:
Given two numbers $x$ and $y$, If $x-y$ is rational, both $x$ and $y$ go into the same box. For example,

- All the rational numbers will end up in the same box since for any two rational numbers $p$ and $q$, $p-q$ is rational.
- $\sqrt 2 - 1$, $(\sqrt 2 - 1) + \frac{1}{4}$, $(\sqrt 2 - 1) + \frac{1}{3}$, ... will end up in a different but the same box.
- $(\pi - 3) +\frac{1}{4}$, $(\pi - 3) + \frac{1}{3}$, $(\pi - 3) + \frac{1}{2}$, ... will end up in a different but the same box.

![vitali_set_corrected.png](/assets/images/2022-01-17-measure-theoretic-probability/vitali_set_corrected.png)

Now, we will construct the Vitali set $V$ by picking any one element from each of these boxes. Notice that here, we are invoking the [axiom of choice](https://en.wikipedia.org/wiki/Axiom_of_choice), which says roughly that given an infinite set of boxes such as this, you can construct a set by picking one element from each.

Since we assumed that the function $P$ exists, we must be able to assign a probability $P(V)$ that, when a number is picked at random from $[0,1]$, it will be in the Vitali set. We will now do something strange.

Let's construct a new set $S$ by "translating" $V$ by every rational number $p$ between $-\frac{1}{4}$ and $\frac{1}{4}$. That is, for every rational number p in $[-\frac{1}{4},\frac{1}{4}]$, we will put $V+p$ in $S$, where $V+p$ is the set we get by adding $p$ to every element of $V$.

$$ S = \bigcup\limits\_{p} (V+p) $$
where p is a rational numer in 
$$ [-\frac{1}{4},\frac{1}{4}] $$
and 
$$ V+p = \{v+p \ |\ v \in V\} $$

## Showing that $P(V)$ does not exist

### "$V+p$"s are disjoint

Notice that for any rational numbers $p$ and $q$ between $-\frac{1}{4}$ and $\frac{1}{4}$, $V+p$ and $V+q$ are disjoint when $p \neq q$. If they were not disjoint, they would have some element $v = v_1+p = v_2+q$ in common.
$\implies (v_1 - v_2) = (q - p)$.

Since $q-p$ is rational, $v_1$ and $v_2$ are from the same box. But this cannot be possible because we chose only one element from each box.

In such cases, we want our probabilities to add up. From the requirement 3 above,
$P(S) = \sum\limits_{p}P(V+p)$.

Also, since we're assuming a uniform distribution, "translating" the set by some rational number should not change its probability. Therefore, each $V+p$ has the same probability $P(V)$.

$\implies P(S) = \sum\limits_{p}P(V)$

### $P(V)$ is bounded above by 1

Since $V$ was in the range $[\frac{1}{4}, \frac{3}{4}]$ and we're adding rational numbers p in the range $[-\frac{1}{4}, \frac{1}{4}]$, $V+p$ should be in the range $[0,1]$. Therefore, the probability of picking an element of $V$ can be at most $P([0, 1])$, which is 1.

$\implies P(S) \leq 1$

### $P(V)$ is bounded below by $\frac{1}{2}$

Every real number $r$ between $\frac{1}{4}$ and $\frac{3}{4}$ is in $S$. Say $r$ was put in the box $B$. There should have been some $b$ that was chosen from $B$ and put in $V$. Since $r$ and $b$ are both in $B$, $r - b = p$ for some rational number $p$. Therefore, $r = b+p$ must have been in $V+p$ and so in $S$.
Therefore, $P(S)$ is at least $P([\frac{1}{4}, \frac{3}{4}])$ which is $\frac{1}{2}$

$\implies P(S) \geq \frac{1}{2}$

Putting both bounds together,
$\frac{1}{2} \leq P(S) \leq 1 \implies \frac{1}{2} \leq \sum\limits_{p}P(V) \leq 1$

### The contradiction

Do you see the problem? We want the sum of infinite "$P(V)$"s to be a finite number between $\frac{1}{2}$ and $1$. This is impossible. If $P(V)$ was 0, the sum would have been 0. If $P(V)$ was even a very small positive value, the sum would have been $\infty$. Therefore, we should never have been able to assign a probability $P(V)$ of picking an element of the Vitali set.

## A more humble alternative

Instead of assigning a probability to every possible event, let's assign probabilities to only a subset of events $B$ (also called the $\sigma$-algebra) that we need to and can.

1. If $A \in B$, we will let $A^c \in B$. This is because, if we can assign a probability to A, we can assign a probability of $1-P(A)$ to $A^c$.
2. If disjoint events $$ \{A*i\}*{i=1}^{\infty} $$ are in B, we will let $$ \bigcup\limits*{i=1}^{\infty}A_i $$ also be in $$ B $$. If we can assign a probability $$ P(A_i) $$ to each $$ A_i $$, we can assign a probability of $$ \sum\limits*{i=1}^{\infty} P(A*i) $$ to $$ \bigcup\limits*{i=1}^{\infty}A_i $$.

This will give us these nice properties for free:

3. From 1 and 2, $P(\Omega) = P(A \cup A^c) = P(A) + (1-P(A)) = 1$. So we might as well put $\Omega$ in $B$ and say that $P(\Omega) = 1$.
4. From 1 and 3, $P(\phi) = P(\Omega^c) = 1-P(\Omega) = 1-1 = 0$. So we might as well put $\phi$ in $B$ and say that $P(\phi) = 0$

That's it for now. Stay tuned for more beautiful ideas in math!
