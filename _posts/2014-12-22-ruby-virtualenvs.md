---
layout: post
title: "Ruby Virtualenvs"
date: 'Mon Dec 22 12:52:40 CST 2014'
comments: false
categories: [ruby, python]
description: >
  After some research, I found a much better solution to the problem of
  sandboxing Ruby gems.
share: false
permalink: /:title/
redirect_from:
- /2014/12/22/ruby-virtualenvs/
---

A while back I found a command that removes all Ruby gems installed on a system
when you're using rbenv. It worked great, so I decided to build on top of it.
After a bit of research, I found a much better solution to the root of my
problems: sandboxing Ruby gems.

<!-- more -->

# Ugh, Ruby...

If you're anything like me, you can never do anything right on the first try
using Ruby. At one point, I found myself needing a script to just nuke
everything and start over... That's when I found Ian Vaughan's [script][iv] that
magically removes all gems. I was delighted to see that it worked perfectly on
the first try, and went about the rest of my business.

# Modifications

There were two ways though in which this script's functionality differed from
what I wanted it to do: it always removed __all__ gems, and it left behind a
`.ruby_version` file after it was used, clobbering any file that might have been
there before.

In my updated script, you can specify a list of ruby versions as arguments, and
it will only gems from those versions instead of all of them.  Also, it saves
and restores the value of the old `.ruby_version` file once it's done.

The new script is available [as a fork of the original Gist][gist] and also as
a part of of [my personal bin folder][bin].

# The Underlying Problem: Virtualenv's in Ruby

After a bit of reflection, I realized I should be trying to solve the underlying
problem: different projects had different dependencies, and gems from one
project were bleeding into gems from another. If you're a Python developer, you
don't have this issue: [virtualenvwrapper][venv], `pip`, and `requirements.txt`
files make this a non-issue.

After looking into if there existed a similar Ruby solution, I came up with
[this blog post][venv-ruby] outlining how you can do the exact same thing using
virtualenvs but with Ruby gems! Once again, it needed a little bit of
modification so that everything works again as you'd expect when you
`deactivate`. Add these lines to your virtualenv's `postactivate` script:

<figure>

```{.python .numberLines}
export OLD_GEMHOME="$GEM_HOME"
export GEM_HOME="$VIRTUAL_ENV/gems"

export OLD_GEM_PATH="$GEM_PATH"
export GEM_PATH=""

export OLD_PATH="$PATH"
export PATH="$GEM_HOME/bin:$PATH"
```

<figcaption>$VIRTUAL_ENV/bin/postactivate</figcaption>
</figure>

And then add this complementary section to your `predeactivate` script:

<figure>

```{.python .numberLines}
export GEM_HOME="$OLD_GEM_HOME"
unset OLD_GEM_HOME

export GEM_PATH="$OLD_GEM_PATH"
unset OLD_GEM_PATH

export PATH="$OLD_PATH"
unset OLD_PATH
```

<figcaption>$VIRTUAL_ENV/bin/predeactivate</figcaption>
</figure>

Now, whenever you install gems, they'll install to the folder
`$VIRTUAL_ENV/gems/` instead of the system's location, so no gems bleed into
another project!

# One Step Further

Bringing up this web page, copying those snippets, and pasting them in the two
necessary files every time is a bit tedious. To automate this process, we can
tap into virtualenvwrapper's configurability using hooks. Instead of dropping
those snippets into `$VIRTUAL_ENV/bin/{post,prede}activate,`, place them in
`$VIRTUALENVWRAPPER_HOOK_DIR/{post,prede}activate`.

Now every time you `workon` a virtualenv, the appropriate configuration will
be set up. Note that this means every normal Python project you use will have
this Ruby configuration added (not just the Ruby projects), but that shouldn't
matter because they interoperate nicely. If it's really an issue, you can stick
with the per-virtualenv solution above.

Note: a side effect of this nice sandboxing is that you can normally run
commands without prefixing them with `bundle exec ...`, which is actually really
handy.


[iv]: https://gist.github.com/IanVaughan/2902499
[gist]: https://gist.github.com/jez/cc2ba08062c6183a489c
[bin]: https://github.com/jez/bin/blob/master/uninstall_gems
[venv]: http://virtualenvwrapper.readthedocs.org/en/latest/
[venv-ruby]: http://honza.ca/2011/06/install-ruby-gems-into-virtualenv
