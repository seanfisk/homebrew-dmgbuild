<!-- -*- coding: utf-8; -*- -->

dmgbuild Homebrew Tap
=====================

[![Build Status](https://travis-ci.org/seanfisk/homebrew-dmgbuild.svg?branch=master)](https://travis-ci.org/seanfisk/homebrew-dmgbuild)

This is a [Homebrew][] tap for [dmgbuild][].

[Homebrew]: http://brew.sh/
[dmgbuild]: https://bitbucket.org/al45tair/dmgbuild

Rationale
---------

Although it does have somewhat of a library feature, dmgbuild is primarily an application written in Python. I personally believe that dmgbuild has the potential to reach a much wider audience, with one caveat: non-Python developers don't want to install dmgbuild via [pip][]. They don't have a Python environment set up, and don't care to have to do so for one tool. However, [Homebrew][] is the go-to source for installing generally useful tools for developers. Homebrew's [Python formula instructions][] explicitly state that these are the kinds of applications well-suited for packaging with Homebrew.

As dmgbuild is currently only compatible with Python 2.7 (OS X's system Python version), this also neatly side-steps the requirement to use Python 2.7 if using dmgbuild in a Python project. This is already possible with tools like [pipsi][], but again, those are focused on existing Python developers.

[pip]: https://pip.pypa.io/en/stable/
[Python formula instructions]: https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/Python-for-Formula-Authors.md#introduction
[pipsi]: https://github.com/mitsuhiko/pipsi

Techniques
----------

To create the dmgbuild formula, I used [homebrew-pypi-poet][] to [manage dependencies][] and automatically write `resource` stanzas for the formula.

[homebrew-pypi-poet]: https://pypi.python.org/pypi/homebrew-pypi-poet
[manage dependencies]: https://github.com/Homebrew/homebrew/blob/master/share/doc/homebrew/Python-for-Formula-Authors.md#dependencies
