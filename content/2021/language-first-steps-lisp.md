+++
title = "Language First Steps: Lisp"
date = 2021-03-09
updated = 2021-05-03
[taxonomies]
category=["tech"]
tags=["tutorial"]
+++

# Installation

## Steel Bank Common Lisp (sbcl)

```bash
$ brew install sbcl
```

## REPL

```bash
$ sbcl
* _
```

## Quicklisp

```bash
$ mkdir ql
$ cd ql
$ curl -O https://beta.quicklisp.org/quicklisp.lisp
$ curl -O https://beta.quicklisp.org/quicklisp.lisp.asc
$ sbcl --load quicklisp.lisp

* (quicklisp-quickstart:install :path ".quicklisp/")
* (ql:add-to-init-file)
* (quit)

$ cd ..
$ rm -rf ql
```

Now you should have a ~/.sbclrc file and ~/.quicklisp/ directory.

```lisp
* (ql:quickload :vecto)
```

## Local Registry

By default Quicklisp will find your projects in `~/.quicklisp/local-projects`.
ASDF, the underlying package manager can also find projects on your system via a custom registry setting.

`~/.config/common-lisp/source-registry.conf`

```lisp
(:source-registry
  (:tree (:home "projects/lisp"))
  :inherit-configuration)
```

# Defining A System Manually

## System Definition

The following definition is about the simplest ASDF system you can make in CL.

```lisp
(asdf:defsystem :mylisp
  :serial t
  :components ((:file "package")
               (:file "hello")))
```

```lisp
(defpackage :hello
  (:use #:cl))
```

```lisp
(in-package :hello)

(defun say-hello () "Hello, Lisp!")
```
> `hello.lisp` - The hello lisp package


# Define A System Automatically

## Quickproject

There is a package called [quickproject](https://www.xach.com/lisp/quickproject/) that creates a basic common lisp structure for you via the REPL.

```bash
$ sbcl

* (ql:quickload :quickproject)
* (quickproject:make-project #p"~/projects/lisp/mylisp/")
```

# Executables

# Doing Stuff

## REPL

To run code within a current REPL

```lisp
(ql:quickload :my-lisp)
(in-package :my-lisp)

MYLISP> (say-hello)
```

## Testing

### Rove

```lisp
(
  ...
  :depends-on ("rove")
)
```
> `mylisp.asd`

```lisp
(defpackage #:mylisp
  (:use #:cl #:rove))
```
> `package.lisp`

```lisp
(deftest it-works
    (testing "2 is 2"
        (ok (= 2 2))))
```
> `mylisp.lisp`

## Buildapp

You can create the `./buildapp` binary with quicklisp.

```lisp
* (ql:quickload :buildapp)
* (buildapp:build-buildapp)
```

You can use `buildapp` to generate an executable for any project with a `main` function.

```bash
$ buildapp --output mylisp \
           --asdf-path . \
           --asdf-tree ~/.quicklisp/dists \
           --load-system mylisp \
           --entry mylisp:main
```