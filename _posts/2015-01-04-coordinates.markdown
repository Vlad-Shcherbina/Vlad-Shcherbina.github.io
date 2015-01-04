---
title: What does it mean to introduce coordinate system on a regular tiling?
---
I suppose there could be many valid answers to this question, but I'll just go
with one that is especially convenient for my puproses.

Let's take [hexagonal tiling `$\{6, 3\}$`](http://en.wikipedia.org/wiki/Hexagonal_tiling) for example. Coordinate system assigns some unique
representation to every tile in a grid:

{% highlight haskell %}
data Hexagon = ???
    deriving Eq
{% endhighlight %}

Given hexagon, we might want to get list of all its direct neighbors, in
counter-clockwise order (does not matter where this list starts, we will think
of it as a cycle anyway):

{% highlight haskell %}
adjacent :: Hexagon -> [Hexagon]
{% endhighlight %}

And finally, we need some way to begin exploration of the grid.
Constant `origin` holds representation of some tile in the grid (does not
matter which one):

{% highlight haskell %}
origin :: Hexagon
{% endhighlight %}

So how this abstract data type should be implemented? Here is one possible
approach:

<figure>
<img src="{{ site.baseurl }}images/hex_grid.png"
     style="width: 100%; max-width: 400px">
</figure>

{% highlight haskell %}
data Hexagon = Hexagon Int Int
    deriving Eq

origin :: Hexagon
origin = Hexagon 0 0

adjacent :: Hexagon -> [Hexagon]
adjacent (Hexagon x y) = [
    Hexagon (x + 1) y,
    Hexagon (x + 1) (y + 1),
    Hexagon x (y + 1),
    Hexagon (x - 1) y,
    Hexagon (x - 1) (y - 1),
    Hexagon x (y - 1)]
{% endhighlight %}

Note that although this set of operations completely captures
the structure of the grid,
it might not be the most efficient choice for certain applications.
For example, finding shortest path between two tiles
can be done purely in terms of equality checks and `adjacent`
(without looking directly at `x` and `y`),
but that would be terribly slow.
