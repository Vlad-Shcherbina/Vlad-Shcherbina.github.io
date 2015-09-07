---
title: Negative indices
excerpt: "<p>xs[-1]: good, bad, ugly?</p>"
---
As a motivating example I'll use Perfect Matching, a very nice problem from
[MMA CTF 2015](http://score.mmactf.link/).

[Their server](https://gist.github.com/Vlad-Shcherbina/05f60d9cdacc69e760ee)
generates random $n$-vertex undirected graph and asks for a perfect matching
(that is, subset of $n/2$ edges whose endpoints constitute all graph vertices).

"Unfortunately", some of the graphs created by the server do not have
perfect matching.

Relevant part of the solution checker:

{% highlight python %}
# Here `graph` is n-element list,
# and `graph[v1]` is sorted list of all vertices adjacent to v1.
edges = []
used_node = []
for i in range(0, n / 2):
    v1, v2 = map(int, raw_input().split())
    edges.append((v1, v2))
    used_node.extend([v1, v2])
if len(list(set(used_node))) != n:
    raise Exception() # Wrong Size
for (v1, v2) in edges: # G includes? (v1,v2)
    pos = bisect.bisect_left(graph[v1], v2)
    if pos == len(graph[v1]) or graph[v1][pos] != v2:
        raise Exception()
{% endhighlight %}

It verifies that number of distinct endpoints is $n$. It does not check that
set of all endpoints is exactly $\\{0, 1, ..., n-1\\}$, but it does not matter,
because it also checks that all supplied edges are actually present
in the graph, which implies that their endpoints are legitimate vertices.
Right?..

Well, if `v1` happens to be a negative number no less than `-n`, element access
`graph[v1]` will not trigger `IndexError: list index out of range` exception.
It will behave equivalently to `graph[v1 + n]` expression.

This defect allows to sell the same graph edge `(v1, v2)` twice, if we send it
first as `(v1 - n, v2)`, and then as `(v2 - n, v1)`. And first part of the
checker will count it as 4 distinct vertices (`v1`, `v2`, `v1 - n`, `v2 - n`).
With this trick, problem reduces to finding merely a $n/4$-edge matching,
which is, let's say, two times easier.

---

That was one problem with negative indices: sometimes what should be
`IndexError` does not actually fail runtime check and silently leads to
incorrect behavior instead.

Another problem is that there is no negative zero.
Expression `xs[:-2]` denotes all elements of the list but the last two,
`xs[:-1]` means all elements but the last one,
so `xs[:-0]` refers simply to all elements, doesn't it?

On the other hand, negative indices are super convenient. I don't want to write
`a[len(a) - 1][len(a[len(a) - 1]) - 1]` instead of `a[-1][-1]`.

Solution is to introduce new data type "right-to-left index",
incompatible with integers. Values of this type should be explictly
constructed from integer values using unary "<" operator
(or something less ugly).

`a[-1][-1]` becomes `a[<1][<1]`, and incorrect form `xs[:-0]` can now be safely
expressed as `xs[:<0]`. With this new semantics, element access operations can
always go through strict and natural boundary checks:
expression `xs[i]` requires `0 <= i < len(xs)`,
expression `xs[<i]` requires `len(xs) >= i > 0`.
Both `xs[-1]` and `xs[<0]` result in runtime errors.
