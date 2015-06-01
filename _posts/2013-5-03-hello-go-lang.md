---
layout: post

title: "Hello Go lang!"
cover_image: hello-go-lang.jpg
tags:
  - programming
  - golang
  - redis

excerpt: "During the last months, I was looking for a new programming language to learn for *fun and profit*. I picked [Go lang](http://golang.org), then I tried to build something enough complete and/or useful, to see how it feels to program in Go with an everyday task. Here's how it went."

---

## TL;DR
I decided to try Go and I developed a simple web app. There's a [working demo on heroku](http://goshort.herokuapp.com) and source code is available on [github](https://github.com/apeacox/goshort). It was an awesome journey and I'm planning to go further.

## Beyond Hello World
You know, when you learn a new programming language, *Hello World* is not enough, you always need some known problem to solve, but *known problem* doesn't mean a boring non-practical algorithm. I decided to port [avarus](https://github.com/apeacox/avarus), a simple Url shortener built with [Cuba](http://cuba.is) ruby microframework and [Redis](http://redis.io). It is a web application, it uses a database and it took a very low amount of code (~150/200 loc). So it's a good candidate to port in Go.

## First steps
Until few days ago, I've never used Go, I'd just read a lot of blog posts and some wiki page. I've read [Getting Started](http://golang.org/doc/install) (but I've used homebrew) and followed the first pages of [A Tour of Go](http://tour.golang.org/), then I launched the editor to get my hands on. I premise that, during the years, I've used a lot of programming languages, so maybe I'm a bit biased, however Go looked very clean and familiar, especially if you've used C/C++.
My first impressions were quite positive, Go has an awesome set of easy and portable tools: you install Go and you get all you need to work.

## From Ruby to Go
I was wondering how much effort should be needed to write the same program from Ruby to Go. They are different for many reasons, so I was expecting a big gap in the lines of code and a steep learning curve. I was wrong, it only took 2 hours to get a working prototype, then I've dedicated some day trying to refine the code to reach a decent implementation. The final result took ~200/250 lines of code, it's an excellent result, considering that Go is a *low level* language, especially when compared to Ruby.

## Libraries and support
I've found that Go has a lot of *packages* bundled with its standard library (plus the ones provided by the community) and it was quite easy to find answers, most of them on the [Go mail list](https://groups.google.com/forum/?fromgroups#!forum/golang-nuts)) to solve problems related to some weirdness in code organization.
At the beginning, I've used [net/http](http://golang.org/pkg/net/http/) package to write the web server part but, you know, nobody would use a so low level library for this task. So I chose [web.go](https://github.com/hoisie/web), a micro web framework very similar to Sinatra and Cuba minimalistic approach. If you're looking to something more MVC-like, you should look at [beego](https://github.com/astaxie/beego), it has excellent features (eg: it handles JSON).

## One tool to rule them all
I said that the Go toolchain is easy to use, the ```go``` command, does almost everything:

* run a program (compiles it temporarily): ```go run myfile.go```
* build (compile to a binary): ```go build```
* test ```go test```
* compile and install a remote package: ```go get url.to/package_name```

I'm almost sure there are other available commands, I've just listed the bare minimum ones. Moreover, the compiler warns you about syntax errors, unused packages and variables and the resulted binary is ready for use *as is*. Isn't it cool?

## Troubles?
During this amazing jouney, the hardest problems where:

* File organization and namespaces: you can use subdirectories for namespacing, but they become internal *packages* and you need to follow some rules and conventions to keep your app working. The only offical guideline is [How to write Go code](http://golang.org/doc/code.html), but it offers a very basic overview on code organization.

* Sometimes, you get *unclear errors* from the compiler, most of them due to some access to null pointers or errors in language syntax (eg: using control stuctures outside a function block).

* I wasn't able to write decent tests, I tried for a bit, then I've *delayed* the idea for later. Maybe I need some more nowledge (and snippets) to get an idea. All I know, is that you need to put the file names with a ```_test.go``` suffix inside the same directory.

As you can see, they're trivial, perhaps caused from the fact that I'm a Go newbie :P

## Deploy
Before deciding to pick Go, I've quickly investigated about deploying the app. I've found some article about deploying on heroku, so I've started to write my app without reading them. Again, I was wondering about the needed effort to deploy a Go app and, again, I was quite surprised. I've followed [this tutorial](http://mmcgrana.github.io/2012/09/getting-started-with-go-on-heroku.html) and everything worked flawlessy: heroku compiles your code to a binary, then runs it through Procfile. Almost the same would happen on a custom VPS box, once you have a working Go installation.

##  Final thoughts
It was an awesome journey, Go is a very interesting language and I think it might be used for other projects. I'm planning to go further, trying to build some other pet project. As you can see, I didn't mention about Go's main strenghts (eg: concurrency), you can find thousands of articles and discussions about this. Instead, I focused on telling my story on *what happens when you try to learn Go and build a simple web app*. A lot of people is adopting (or going to adopt) NodeJs for several (almost good) reasons, if you're one of these, give Go a try ;-)
