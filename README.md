gopkg
=====

Standard toolset for maintainig program environmeet coming with Go
is minimalistic and offer enough features for most use cases. But
there are cases in software development where we need more control
on build process with hierarchy of dependence packages and their
versions. The gopkg tool offer own way to resolve it and keep
compatibility with `go get` and other Go tools.

Primary goals of the project are:

* maintain hierarchy of source packages with abilities to install, update, remove and search them
* freeze package versions for keep stable builds
* full control on download packages from selected VCS branches, tags and commits (for git, hg, bzr)
* help to maintain forked repositories with local patches
* establish your own repositories with network access (for example by `go get`) to use them in team work

With next ideas in mind:

1. When you need to freeze packages you set up it in your go-code explicitly and don't use
  external metadata. You see the code and understand that you code uses without any magic tools.

  So preferable way to keep package versions is explicitly set them in `import` clause.
  If you need change version of used package you update it just in the code
  with a help of refactor tools or manually.

2. Usage of frozen package version without code refactoring also may be right way
  in some cases. So we need solution for both cases: define version in `import` clause or
  provide some metadata about package versions for use with unchangable program code.

3. When your rebuild application for different versions of imported packages your environment
  must be already set for each case. Dynamically prepared environment for changed requirements
  slowdown build process for large dependency trees. So we need keep ready for use environments
  for each versions setup. Disk storage is cheap, build time is more valuable thing.

This is not a webservice, this is local tool for maintain your packages database and setup
different build environments.

Inspired by gom, godep, godeps, gopkg.cc, gopkg.in. Project is on early stage of development.

Link to proposed CLI (in Russian yet): [Proposed CLI features](CLI.ru.md).
