# Object Oriented Programming in Python

This lesson provides a brief introduction to the principles of object-oriented
programming in Python. While most of the principles taught are applicable to
all object-oriented languages, some are taught in a way that is more idiomatic
in Python than in other programming languages, and it isn't possible to show
the additional advantages of using object-oriented programming in statically-
typed compiled languages.

## Contributing

We welcome all contributions to improve the lesson! Maintainers will do their best to help you if you have any
questions, concerns, or experience any difficulties along the way.

To build the material, do:
 * git clone git@github.com:pletzer/python-oop-novice.git
 * cd python-oop-novice
 * make serve

"make serve" will serve the pages at http://127.0.0.1:4000.

### Ruby/Jekyll setup

This lesson site is built with Jekyll via `bundle`/`gem` (Ruby), not Python — `make serve`
does not need a Python virtualenv or `requirements.txt`. On Ubuntu, see
[this guide](https://www.geeksforgeeks.org/how-to-install-ruby-bundler-on-linux/) for installing
Ruby and Bundler.

**On macOS**, the system Ruby is too old, and Homebrew's current `ruby` (Ruby 3.2+) is *also* too
new: the `github-pages` gem pins `liquid 4.0.3`, which calls `String#tainted?` — a method Ruby
removed in 3.2. The last Ruby that still has it (and satisfies `github-pages`'s `ffi >= 3.0`
requirement) is the **3.1.x** series:

~~~ bash
brew install ruby@3.1
export PATH="/opt/homebrew/opt/ruby@3.1/bin:$PATH"   # put this in ~/.zshrc to persist
export LANG=en_US.UTF-8                              # avoids a Sass "Invalid US-ASCII character" build error
export LC_ALL=en_US.UTF-8
cd python-oop-novice
make serve
~~~

(`ruby@3.1` is a deprecated Homebrew formula, so `brew install ruby@3.1` may stop working once
Homebrew removes it — at that point the fix is either patching `String#tainted?`/`#taint` as
no-ops in a small Ruby file Jekyll auto-loads, or dropping the `github-pages` gem for a plain
`jekyll` + explicit plugin list in the `Gemfile`.)

The material can be accessed [here](https://USERNAME.github.io/python-oop-novice/index.html) where USERNAME is your Github username.


We'd like to ask you to familiarise yourself with our [Contribution Guide](CONTRIBUTING.md) and have a look at
the [more detailed guidelines][lesson-example] on proper formatting, ways to render the lesson locally, and even
how to write new episodes.

Please see the current list of [issues][FIXME] for ideas for contributing to this
repository. For making your contribution, we use the GitHub flow, which is
nicely explained in the chapter [Contributing to a Project](http://git-scm.com/book/en/v2/GitHub-Contributing-to-a-Project) in Pro Git
by Scott Chacon.
Look for the tag ![good_first_issue](https://img.shields.io/badge/-good%20first%20issue-gold.svg). This indicates that the maintainers will welcome a pull request fixing this issue.  


## Maintainer(s)

Current maintainers of this lesson are 

* Ed Bennett
* Mark Dawson
* Alexander Pletzer


## Authors

A list of contributors to the lesson can be found in [AUTHORS](AUTHORS)

## Citation

To cite this lesson, please consult with [CITATION](CITATION)

[lesson-example]: https://carpentries.github.io/lesson-example
