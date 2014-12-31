---
title: Rabbits make up history on demand
---
Operations defined in the
[previous post]({% post_url 2014-12-30-rabbits-tree %}) are not total.
For example, we can't go `up` from the root of the tree.
That's because root element has no history.
Let's pretend that this whole tree is just a subtree of a tree with the same
uniform structure that spans infinitely in all directions (including `up`).

In other words, when we need to go `up` from an element that has no `up`, we
will imagine fictitious chain of events that brought this element into
existence.

{% highlight haskell %}
data RabbitHistory =
    Born RabbitHistory |
    Stayed RabbitHistory |
    Matured RabbitHistory |
    ImaginaryHistory Int
{% endhighlight %}

Root element is denoted

{% highlight haskell %}
ImaginaryHistory 0
{% endhighlight %}

but when necessary, we will also treat it as

{% highlight haskell %}
Matured (Born (Stayed (ImaginaryHistory 1))),
{% endhighlight %}

where integer value is used to keep track of how much history we imagined.

{% highlight haskell %}
imagineHistory :: RabbitHistory -> RabbitHistory
imagineHistory (ImaginaryHistory k) =
    Matured (Born (Stayed (ImaginaryHistory (k + 1))))
{% endhighlight %}

<figure>
<img src="{{ site.baseurl }}images/imaginary_tree.png"
     style="width: 100%; max-width: 300px">
</figure>

Amended implementation of `up` adds additional "catch all" clause in case
regular patterns do not match (there is no regular up):

{% highlight haskell %}
up :: RabbitHistory -> RabbitHistory
up (Born h) = h
up (Stayed h) = h
up (Matured h) = h
up h = up (imagineHistory h)  -- no real history
{% endhighlight %}

Let's consider what happens if we go up and then down from the root:
`down (up (ImaginaryHistory 0))` first reduces to
`down (up (Matured (Born (Stayed (ImaginaryHistory 1)))))`, then to
`down (Born (Stayed (ImaginaryHistory 1)))`, and finally to
`[Matured (Born (Stayed (ImaginaryHistory 1)))]`, which actually should be just
`[ImaginaryHistory 0]`.

This is unfortunate, because we loose the uniqueness property
of our element representation. To get it back, we will replace direct calls
to `Matured` constructor with calls to `matured` helper that automatically
collapses imaginary part whenever possible:

{% highlight haskell %}
matured :: RabbitHistory -> RabbitHistory
matured (Born (Stayed (ImaginaryHistory k))) | k > 0 =
    ImaginaryHistory (k - 1)
matured h = Matured h
{% endhighlight %}

Implementation of `down` will now look like this (one character edit):

{% highlight haskell %}
down :: RabbitHistory -> [RabbitHistory]
down (Born h) = [matured (Born h)]
down h = [Stayed h, Born h]
{% endhighlight %}

To update `left` and `right`, we will do both (add "catch all" part and replace
`Matured` with `matured`):
{% highlight haskell %}
left :: RabbitHistory -> RabbitHistory
left (Born h)           = Stayed h
left (Matured (Born h)) = Born (Stayed h)
left (Stayed h)         = rightChild (left h)
    where rightChild h = last (down h)
left h = left (imagineHistory h)  -- no real history

right :: RabbitHistory -> RabbitHistory
right (Stayed h)        = Born h
right (Born (Stayed h)) = matured (Born h)
right (Born h)          = Stayed (right h)
right (Matured h)       = Stayed (right h)
right h = right (imagineHistory h)  -- no real history
{% endhighlight %}

Now `up`, `down`, `left`, and `right` are defined on all elements reachable
from the root.
