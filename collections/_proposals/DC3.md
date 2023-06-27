---
number: DC3
name: Python version support
tags: proposal
---

# DC3: Supported Python versions

| Proposal | Supported Python versions                            |
| -------- | ---------------------------------------------------- |
| Created  | 2021-05-06                                           |
| Status   | [Accepted](https://dials.github.io/kb/core/2021-06-03) |

---

DIALS supports a number of recent Python versions. However, supporting too old
versions of Python does limit us both in terms of available language features
and upstream dependency support. To resolve this conflict DIALS will follow the
[NEP-29 deprecation calendar][nep-29], which has been widely adopted by the
Scientific Python community.

According to this schedule, DIALS will support a Python release for
approximately 42 months after its release. With a Python release cadence of
[12 months][pep-602] this means DIALS will support the 3-4 most recent Python
releases at any point in time.

### Deprecation schedule

NEP-29 includes a [support table and drop schedule for upcoming versions of
Python][nep-29-support] and Numpy.

DIALS will deviate from NEP-29 in that deprecations will take effect _following
the next release after deprecation_.

If, for example, on the deprecation day of Python 3.A the current release
version of DIALS is 3.**N**, then Python 3.A support will be dropped from the
main development branch once the release branch for DIALS 3.**N+1** has been
made. DIALS 3.**N+1**.0 will therefore support Python 3.A, and future DIALS
releases such as 3.**N+2**.0 will not.

We will not deprecate Python version support within a DIALS release. So, if
DIALS 3.**N+1**.0 supports Python 3.A, then all patch releases (3.**N+1**.1,
3.**N+1**.2, ...) will retain Python 3.A support.

DIALS releases generally are supported until the next release is published.
This support _may_ be extended in the case of releases that are the final
release supporting a particular version of Python. Extended support can take
the form of point releases including binary builds, point releases without
binary builds, or just accepting source tree changes without release tags. No
support will be provided outside of release branches, or where the [Python
version in question is itself no longer supported][python-endoflife].

[nep-29]: https://numpy.org/neps/nep-0029-deprecation_policy.html
[nep-29-support]: https://numpy.org/neps/nep-0029-deprecation_policy.html#support-table
[pep-602]: https://www.python.org/dev/peps/pep-0602/
[python-endoflife]: https://endoflife.date/python
