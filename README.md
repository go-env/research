Golang environment tools (GET)
==============================

Standard toolset for maintainig program environmeet coming with Go
is minimalistic and offer enough features for most use cases. But
there are cases in software development where we need more control
on build process with hierarchy of dependence packages and their
versions.

There are a lot of tools to improve package management and environments
maintainability for Go. GET follows both ways offered by other tools
like dependency vendoring and version locking. You choose way appropriate
for you projects. Additionally GET offer own ways to avoid dependency hell
and manage packages and environments fast and clean.

Primary goals of GET project are:

* have reproducible builds
* fast switching between package trees for different projects
* identify conflicst with package versions
* offer different ways for different needs (vendoring vs version locking)

Planned features are:

* package management:
 - install, update, remove packages
 - install and remove dependencies of installed package
 - fast search on indexed base of installed packages
* revision locking:
 - freeze package versions by selected VCS branches, tags and commits
 - change imports in code to consistent versions
 - detect version incompatibilities
      - based on VCS tags and commits
      - based on package exported symbols changes
      - based on package content hash
* vendoring:
 - maintain per project environments based on GOPATH
 - save package dependencies for a project
 - install all package dependencies for a project
* teamwork:
 - import proxy
 - publish own repositories
 - expose only selected versions
 - not need third party webservices for proxy and publishing
* compatibility with default go toolset:
 - work with all VCS supported by default tools VCS: git, hg, bzr, svn
 - support webservices referenced in sources of go tools

Status of development
---------------------

Just build prototypes of CLI. These are:

* gopkg — package manager CLI
* goenv — environments manager CLI
* gosrc — maintain imports CLI

Other similar solutions for Go
------------------------------

See wikipage at https://code.google.com/p/go-wiki/wiki/PackageManagementTools for almost complete reference.
