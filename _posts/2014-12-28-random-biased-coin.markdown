---
title: Random biased coin
---
Suppose we pick biased coin at random, so that
tails probability $\theta$ is itself uniformly distributed on $[0, 1]$.
What is the distribution of number of tails if we toss this coin $n$ times?

For specific values of $\theta$ distribution has a peak around
$n\theta$.

<!--
import numpy
b = numpy.array([1, 4, 6, 4, 1])
i = numpy.arange(5)
for theta in 0.1, 0.5, 0.75:
    print theta, (200 * b * theta ** i * (1 - theta) ** (4 - i) + 0.5).astype(int)
-->

<figure>
<figcaption>$n=4$, $\theta=0.1$:</figcaption>
<table>
<tr>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 131px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 58px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 10px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 1px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 0px"></div>
  </td>
</tr>
<tr>
  <td style="text-align: center">0</td>
  <td style="text-align: center">1</td>
  <td style="text-align: center">2</td>
  <td style="text-align: center">3</td>
  <td style="text-align: center">4</td>
</tr>
</table>
</figure>

<figure>
<figcaption>$n=4$, $\theta=0.5$:</figcaption>
<table>
<tr>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 13px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 50px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 75px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 50px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 13px"></div>
  </td>
</tr>
<tr>
  <td style="text-align: center">0</td>
  <td style="text-align: center">1</td>
  <td style="text-align: center">2</td>
  <td style="text-align: center">3</td>
  <td style="text-align: center">4</td>
</tr>
</table>
</figure>

<figure>
<figcaption>$n=4$, $\theta=0.75$:</figcaption>
<table>
<tr>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 1px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 9px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 42px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 84px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 63px"></div>
  </td>
</tr>
<tr>
  <td style="text-align: center">0</td>
  <td style="text-align: center">1</td>
  <td style="text-align: center">2</td>
  <td style="text-align: center">3</td>
  <td style="text-align: center">4</td>
</tr>
</table>
</figure>

What is surprising, is that if we "add up" these distributions for all values
of $\theta$, we'll get equal probabilities for all possible numbers of tails:
<figure>
<table>
<tr>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 40px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 40px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 40px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 40px"></div>
  </td>
  <td style="vertical-align: bottom;">
    <div style="background-color: #777; width: 50px; height: 40px"></div>
  </td>
</tr>
<tr>
  <td style="text-align: center">0</td>
  <td style="text-align: center">1</td>
  <td style="text-align: center">2</td>
  <td style="text-align: center">3</td>
  <td style="text-align: center">4</td>
</tr>
</table>
</figure>

One can verify it be checking that for all $k$

$$
\int_0^1 {n\choose k} \theta^{k}(1-\theta)^{n-k} \,d\theta = \frac{1}{n+1},
$$

but that's boring.

Instead, consider alternative formulation of this experiment.
First we pick $\theta$ uniformly. Then, instead of flipping coins, we choose
values of $y_1, ..., y_n$ independently and uniformly from $[0, 1]$.
We say that coin $i$ produces tails iff $y_i < \theta$, which happens with
probability exactly $\theta$. Then number of tails is the number of $y_i$ that
fall to the left of $\theta$, or, equivalently, zero-based index of symbol
$\theta$ in the sequence $\theta, y_1, ..., y_n$ sorted by values of respective
variables. For example,
if these values can be arranged as

$$
y_2 < y_3 < y_1 < \theta < y_4,
$$

we observe three tails.

Since all these random variables are independent and have the same continuous
distribution, sorted sequence $\theta, y_1, ..., y_n$ is random permutation with
all possible orders being equiprobable, thus $\theta$ is equally likely
to appear in any of $n + 1$ positions.
$$\tag*{$\blacksquare$}$$
