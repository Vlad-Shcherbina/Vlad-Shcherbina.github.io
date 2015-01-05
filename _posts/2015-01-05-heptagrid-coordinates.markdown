---
title: Coordinate system for heptagonal tiling of hyperbolic plane
---
While playing [HyperRogue](http://www.roguetemple.com/z/hyper/)
(which is awesome, by the way),
I began to wonder how such game could be implemented.
Crucial component would be
[discrete coordinate system]({% post_url 2015-01-04-coordinates %})
for hyperbolic tiling, and it turned out that it's not entirely trivial.

Let's take
[heptagrid `$\{7, 3\}$`](http://en.wikipedia.org/wiki/Heptagonal_tiling).
Consider two fragments of this tiling: obtuse sector $0$ and acute sector $1$.

<figure>
<img src="{{ site.baseurl }}images/obtuse_sector.png"
     style="max-width: 300px; display: inline-block;">
<img src="{{ site.baseurl }}images/acute_sector.png"
     style="max-width: 300px; display: inline-block;">
</figure>

Obtuse sector can be partitioned into apex tile, two copies of obtuse
sector, and one copy of acute sector. Acute sector partitions into apex
tile, one copy of obtuse sector, and one copy of acute sector:

<figure>
<img src="{{ site.baseurl }}images/obtuse_sector_partition.png"
     style="max-width: 300px; display: inline-block;">
<img src="{{ site.baseurl }}images/acute_sector_partition.png"
     style="max-width: 300px; display: inline-block;">
</figure>

This partitioning rule can be written as $T^2$:

$$
\begin{align}
&0 \to 010, \\
&1 \to 01,
\end{align}
$$

and it happens to coincide with
[the rabbit rule $T$]({% post_url 2014-12-30-rabbits-tree %})
applied twice!

This also explains why "generations" of this partition form Fibonacci words:

<figure>
<img src="{{ site.baseurl }}images/obtuse_sector_layers.png"
     style="max-width: 300px; display: inline-block;">
<img src="{{ site.baseurl }}images/acute_sector_layers.png"
     style="max-width: 300px; display: inline-block;">
</figure>

<table style="width: 100%;">
<tr>
<td>
$$
\begin{align}
& S_0 = 0, \\
& S_2 = 010, \\
& S_4 = 01001010, \\
& ...
\end{align}
$$
</td>
<td>
$$
\begin{align}
& S_1 = 01, \\
& S_3 = 01001, \\
& ...
\end{align}
$$
</td>
</tr>
</table>

Anyway, we now have a natural way to refer to heptagonal tiles:

{% highlight haskell %}
data Heptagon = Heptagon RabbitHistory
    deriving Eq
{% endhighlight %}

Since rabbit history can be
[imaginary]({% post_url 2014-12-31-imaginary-history %}),
this data type is capable of representing any tile on a plane,
not only those within a particular sector.

Operations:

{% highlight haskell %}
origin :: Heptagon
origin = Heptagon (ImaginaryHistory 0)

adjacent :: Heptagon -> [Heptagon]
adjacent (Heptagon h) = map Heptagon (
    let
        grandparent = up (up h)
        left_cousin = left h
        right_cousin = right h
        grandchildren = [h2 | h1 <- down h,
                              h2 <- down h1]
    in
        [grandparent] ++
        [left_cousin] ++
        grandchildren ++
        [right_cousin]
    )
{% endhighlight %}

Unfortunately, this implementation of `adjacent` has a defect.
It will be addressed in the follow-up post.
