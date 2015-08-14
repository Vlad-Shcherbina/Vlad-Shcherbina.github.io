---
title: Debug statement
---

Print statement in Python 2 is optimized for hello world and few other
programs people who study the language write.

But because of its convenience, it found two other applications.
First, it is sometimes used to produce output in serious, non-beginner programs.
Second, it is the default choice for
[print debugging](http://stackoverflow.com/questions/189562/what-is-the-proper-name-for-doing-debugging-by-adding-print-statements).

I don't think it's a good API for "real" output. It offers limited and awkward
formatting options that overlap with rich and powerful formatting capabilities
of `str.format`. Should I write

{% highlight python %}
print 'hello,', name
{% endhighlight %}
or
{% highlight python %}
print 'hello, {}'.format(name)
{% endhighlight %}
?

However, Python 3 legitimizes this use of print statement by turning it into
a function, with [the rationale saying](https://www.python.org/dev/peps/pep-3105/#rationale)
"print is the only application-level functionality that has a statement dedicated to it..."
and "...one quite often feels the need to replace print output by something more sophisticated..."

From a theoretical standpoint, I favor nice and orthogonal combination of
`sys.stdout.write()` and `.format()`, but I'm not sure my resolve is strong
enough in face of extra verbosity required:

{% highlight python %}
sys.stdout.write('hello, {}\n'.format(name))
{% endhighlight %}

Anyway, speaking of the debugging/tracing use of `print`, I think pattern

{% highlight python %}
print('x =', repr(x))
{% endhighlight %}

is common and useful enough that it should be implied.
Proper way to achieve that would be, of course, to make debug print a statement!

It recently occurred to me that language support is not necessary.
Intended behavior can be emulated with some stack trace magic:

{% gist 7106825232b68e4b06eb %}
