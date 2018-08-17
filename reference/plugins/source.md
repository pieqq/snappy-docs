---
layout: base
title: Common source options
---

Unless the part plugin overrides this behaviour, a part can use these
'source' keys in its definition. They tell Snapcraft where to pull source
code for that part, and how to unpack it if necessary.

  - `source`: `<url-or-path>`

    A URL or path to some source tree to build. It can be local
    ('./src/foo') or remote ('https://foo.org/...'), and can refer to a
    directory tree or a tarball or a revision control repository
    ('git:...').

  - `source-type`: `local`, `git`, `bzr`, `hg`, `svn`, `tar`, `deb`, `rpm`, or
  `zip`

    In some cases the source string is not enough to identify the version
    control system or compression algorithm. The source-type key can tell
    snapcraft exactly how to treat that content.

  - `source-checksum`: `<algorithm>`/`<digest>`

    Snapcraft will use the digest specified to verify the integrity of the
    source. The source-type needs to be a file (tar, zip, deb or rpm) and
    the algorithm either md5, sha1, sha224, sha256, sha384, sha512, sha3_256,
    sha3_384 or sha3_512.

  - `source-depth`: `<integer>`

    By default clones or branches with full history, specifying a depth
    will truncate the history to the specified number of commits.

  - `source-branch`: `<branch-name>`

    Snapcraft will checkout a specific branch from the source tree. This
    only works on multi-branch repositories from git and hg (mercurial).

  - `source-commit`: `<commit>`

    Snapcraft will checkout the specific commit from the source tree revision
    control system.

  - `source-tag`: `<tag>`

    Snapcraft will checkout the specific tag from the source tree revision
    control system.

  - `source-subdir`: `<path>`

    When building, Snapcraft will set the working directory to be this
    subdirectory within the source.

Note that plugins might well define their own semantics for the 'source'
keywords, because they handle specific build systems, and many languages
have their own built-in packaging systems (think CPAN, PyPI, NPM). In those
cases you want to refer to the help for the specific plugin.
