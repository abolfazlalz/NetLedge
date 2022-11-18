We know that **Golang language** is a **compiler** language.
So, if we want to test a Web Application written with Gin, we must first compile it and then run it.
This is very hard for development environments.

For this we can use codegangsta/gin. This library/command line let you run Gin Web Application with live reloading.

## Installation
Assuming you have a working Go environment and `GOPATH/bin` is in your `PATH`, `gin` is a breeze to install:

```bash
go get github.com/codegangsta/gin
```

Then verify that `gin` was installed correctly:

```bash
gin help
```

## Basic usage

```bash
gin run main.go
```
