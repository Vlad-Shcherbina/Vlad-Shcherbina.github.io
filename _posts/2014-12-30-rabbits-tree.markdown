---
title: Navigating tree of rabbits
---
As stated in famous Fibonacci rabbit problem, it takes one month for young pair
of rabbits to mature (reach reproductive age). Mature pair breeds, and in one
month produces young pair of offsprings, while remaining reproductive itself.

If we denote young pair as $1$ and mature pair as $0$, events of one month can
be captured in the following rule $T$:

$$
\begin{align}
&1 \to 0, \\
&0 \to 01.
\end{align}
$$

If we start with a single mature pair, here is what happens in the following
months:

$$
\begin{align}
& S_0 = 0, \\
& S_1 = T(S_0) = T(0) = 01, \\
& S_2 = T(S_1) = T(01) = 01\ 0, \\
& S_3 = T(S_2) = T(010) = 01\ 0\ 01, \\
& S_4 = T(S_3) = T(01001) = 01\ 0\ 01\ 01\ 0, \\
& ...
\end{align}
$$

This is a sequence of
[Fibonacci words](http://en.wikipedia.org/wiki/Fibonacci_word).
Wikipedia uses alternative definition
$S_0 = 0$, $S_1 = 1$, `$S_n = S_{n-1} S_{n-2}$`,
but it's easy to show that it's equivalent to iterative application of $T$:

$$
T^n(0) =
T^{n-1}(T(0)) =
T^{n-1}(01) =
T^{n-1}(0) T^{n-1}(1) =
T^{n-1}(0) T^{n-2}(0).
$$

This process can also be understood as a tree:

<figure>
<img src="{{ site.baseurl }}images/rabbit_tree.png"
     style="width: 100%; max-width: 300px">
</figure>

We will distiguish three types of events (or tree edges):

$$
\begin{align}
&\boldsymbol{1} \to \boldsymbol{0} &\mbox{(maturation)}\\
&\boldsymbol{0} \to \boldsymbol{0}1 &\mbox{(staying)} \\
&\boldsymbol{0} \to 0\boldsymbol{1} &\mbox{(birth)}
\end{align}
$$

and history of a particular tree node will be represented as a chain of such
events, applied in reverse chronological order for convenience.

{% highlight haskell %}
data RabbitHistory =
    Born RabbitHistory |
    Stayed RabbitHistory |
    Matured RabbitHistory |
    NoHistory
{% endhighlight %}

For example, $01\boldsymbol{0}$ in $S_2$ is a result of _maturation_ of
$0\boldsymbol{1}$ in $S_1$, which in turn was _born_ by historyless element
$\boldsymbol{0}$ in $S_0$, so it will be represented by an expression

{% highlight haskell %}
Matured (Born NoHistory)
{% endhighlight %}

Operations:

{% highlight haskell %}
up :: RabbitHistory -> RabbitHistory
up (Born h) = h
up (Stayed h) = h
up (Matured h) = h

down :: RabbitHistory -> [RabbitHistory]
down (Born h) = [Matured (Born h)]
down h = [Stayed h, Born h]

left :: RabbitHistory -> RabbitHistory
left (Born h)           = Stayed h         -- (a)
left (Matured (Born h)) = Born (Stayed h)  -- (b)
left (Stayed h)         =
    case left h of
        Matured h1 -> Born (Matured h1)    -- (c)
        Born h1    -> Matured (Born h1)    -- (c')

right :: RabbitHistory -> RabbitHistory
right (Stayed h)        = Born h            -- (a)
right (Born (Stayed h)) = Matured (Born h)  -- (b)
right (Born h)          = Stayed (right h)  -- (c')
right (Matured h)       = Stayed (right h)  -- (c)
{% endhighlight %}

These operations have nice property that `down` is inverse of `up` and `left`
is inverse of `right`.

Moving left and right is somewhat tricky, because it may involve direct siblings,
1st cousins, 2nd cousins, and so on. Principal cases are shown below.

<figure>
<img src="{{ site.baseurl }}images/rabbit_navigation.png" style="width: 100%">
</figure>
