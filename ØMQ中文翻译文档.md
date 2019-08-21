
ØMQ - The Guide



[Table of Contents](javascript:;)



## By Pieter Hintjens, CEO of iMatix

Please use the [issue tracker](https://github.com/imatix/zguide/issues) for all comments and errata. This version covers the latest stable release of ZeroMQ (3.2). If you are using older versions of ZeroMQ then some of the examples and explanations won't be accurate.

The Guide is originally [in C](http://zguide.zeromq.org/page:all), but also in [PHP](http://zguide.zeromq.org/php:all), [Java](http://zguide.zeromq.org/java:all), [Python](http://zguide.zeromq.org/py:all), [Lua](http://zguide.zeromq.org/lua:all), and [Haxe](http://zguide.zeromq.org/hx:all). We've also translated most of the examples into C++, C#, CL, Delphi, Erlang, F#, Felix, Haskell, Objective-C, Ruby, Ada, Basic, Clojure, Go, Haxe, Node.js, ooc, Perl, and Scala.

## ZeroMQ 简介

ZeroMQ(也称为ØMQ, 0mq或zmq)看起来像一个嵌入式的网络库（an embeddable networking library）,但就像一个并发性框架。它为您提供了scoket，可以跨进程内、进程间、TCP和多播等各种传输传输原子消息。您可以N-to-N的连接scokets 诸如 fan-out, pub-sub, task distribution,和request-reply等模式。它的速度足以成为集群产品的组织（fabric）。它的异步I/O模型为您提供了可伸缩的多核应用程序，构建为异步消息处理任务。它有大量的语言api，可以在大多数操作系统上运行。ZeroMQ来自[iMatix](http://www.imatix.com/)，是LGPLv3级开源。



## 它如何开始

We took a normal TCP socket, injected it with a mix of radioactive isotopes stolen from a secret Soviet atomic research project, bombarded it with 1950-era cosmic rays, and put it into the hands of a drug-addled comic book author with a badly-disguised fetish for bulging muscles clad in spandex. Yes, ZeroMQ sockets are the world-saving superheroes of the networking world.

**Figure 1 - A terrible accident…**

![fig1.png](https://github.com/imatix/zguide/raw/master/images/fig1.png)



## Zero的含义
ZeroMQ的Ø权衡。一方面，这个奇怪的名字降低了ZeroMQ在谷歌和Twitter上的知名度。另一方面它惹恼了我们丹麦人写一些诸如“ØMG røtfl”,并且“Ø看作（looking）零不是好笑的!”和“地中海Rødgrød fløde !”，这显然是一种侮辱，意思是“愿你的邻居是格伦德尔的直系后裔!”似乎是公平交易。

最初ZeroMQ中的0表示“零代理”，并且(接近于)“零延迟”(尽可能)。从那时起，它开始包含不同的目标:零管理、零成本、零浪费。更普遍地说，“零”指的是渗透在项目中的极简主义文化。我们通过消除复杂性而不是公开新功能来增加功能。

## Audience

本书是为专业程序员编写的，他们想要学习如何制作将主导未来计算的大规模分布式软件。我们假设您可以阅读C代码，因为这里的大多数示例都是用C编写的，即使ZeroMQ在许多语言中都被使用。我们假设您关心规模，因为ZeroMQ首先解决了这个问题。我们假设您需要以尽可能少的成本获得尽可能好的结果，否则您将不会欣赏ZeroMQ所做的权衡。除了基本的背景知识，我们还将介绍使用ZeroMQ所需的网络和分布式计算的所有概念。



## 致谢

Thanks to Andy Oram for making [the O'Reilly book](http://shop.oreilly.com/product/0636920026136.do) happen, and editing this text.

Thanks to Bill Desmarais, Brian Dorsey, Daniel Lin, Eric Desgranges, Gonzalo Diethelm, Guido Goldstein, Hunter Ford, Kamil Shakirov, Martin Sustrik, Mike Castleman, Naveen Chawla, Nicola Peduzzi, Oliver Smith, Olivier Chamoux, Peter Alexander, Pierre Rouleau, Randy Dryburgh, John Unwin, Alex Thomas, Mihail Minkov, Jeremy Avnet, Michael Compton, Kamil Kisiel, Mark Kharitonov, Guillaume Aubert, Ian Barber, Mike Sheridan, Faruk Akgul, Oleg Sidorov, Lev Givon, Allister MacLeod, Alexander D'Archangel, Andreas Hoelzlwimmer, Han Holl, Robert G. Jakabosky, Felipe Cruz, Marcus McCurdy, Mikhail Kulemin, Dr. Gergő Érdi, Pavel Zhukov, Alexander Else, Giovanni Ruggiero, Rick "Technoweenie", Daniel Lundin, Dave Hoover, Simon Jefford, Benjamin Peterson, Justin Case, Devon Weller, Richard Smith, Alexander Morland, Wadim Grasza, Michael Jakl, Uwe Dauernheim, Sebastian Nowicki, Simone Deponti, Aaron Raddon, Dan Colish, Markus Schirp, Benoit Larroque, Jonathan Palardy, Isaiah Peng, Arkadiusz Orzechowski, Umut Aydin, Matthew Horsfall, Jeremy W. Sherman, Eric Pugh, Tyler Sellon, John E. Vincent, Pavel Mitin, Min RK, Igor Wiedler, Olof Åkesson, Patrick Lucas, Heow Goodman, Senthil Palanisami, John Gallagher, Tomas Roos, Stephen McQuay, Erik Allik, Arnaud Cogoluègnes, Rob Gagnon, Dan Williams, Edward Smith, James Tucker, Kristian Kristensen, Vadim Shalts, Martin Trojer, Tom van Leeuwen, Hiten Pandya, Harm Aarts, Marc Harter, Iskren Ivov Chernev, Jay Han, Sonia Hamilton, Nathan Stocks, Naveen Palli, and Zed Shaw for their contributions to this work.


# Chapter 1 - Basics
## Fixing the World
如何解释ZeroMQ?我们中的一些人从它所做的所有奇妙的事情开始说起。它的sockets在steroids上。它就像带有路由的邮箱。它很快! 其他人试图分享他们的顿悟时刻，即当一切都变得显而易见时，ap-pow-kaboom satori paradigm-shift moment。事情变得简单了。复杂性消失。它能开阔思维。*其他人试图通过比较来解释。它更小、更简单，但看起来仍然很眼熟。就我个人而言，我想要记住我们为什么要制作ZeroMQ，因为这很有可能就是你们读者今天仍然在做的事情。

编程是一门伪装成艺术的科学，因为我们大多数人都不懂软件的物理原理，而且很少有人教过编程。
软件的物理不是算法、数据结构、语言和抽象。这些只是我们制造、使用、丢弃的工具。软件真正的物理特性是人的物理特性——具体地说，是我们在复杂性方面的局限性，以及我们合作解决大问题的愿望。这是编程的科学:制作人们能够理解和使用的积木，然后人们将一起工作来解决最大的问题。

我们生活在一个互联的世界，现代软件必须在这个世界中导航。因此，未来最大的解决方案的构建模块是相互连接和大规模并行的。仅仅让代码变得“强大而安静”是不够的。代码必须与代码对话。代码必须健谈、善于交际、关系良好。代码必须像人脑一样运行，数以万亿计的单个神经元相互发送信息，这是一个大规模的并行网络，没有中央控制，没有单点故障，但能够解决极其困难的问题。代码的未来看起来像人脑，这并非偶然，因为每个网络的端点，在某种程度上，都是人脑。

如果您使用线程、协议或网络做过任何工作，您就会发现这几乎是不可能的。这是一个梦。当您开始处理实际的情况时，即使跨几个scoket连接几个程序也是非常麻烦的。数万亿吗?其代价将是难以想象的。连接计算机是如此困难，以至于软件和服务要做这是一项数十亿美元的业务。

所以我们生活在一个线路比我们使用它的能力超前数年的世界里。上世纪80年代，我们经历了一场软件危机。当时，弗雷德•布鲁克斯(Fred Brooks)等顶尖软件工程师相信，没有什么“灵丹妙药”能“保证生产率、可靠性或简单性哪怕提高一个数量级”。

布鲁克斯错过了免费和开源软件，正是这些软件解决了这场危机，使我们能够有效地共享知识。今天，我们面临着另一场软件危机，但我们很少谈论它。只有最大、最富有的公司才有能力创建连接的应用程序。有云，但它是私有的。我们的数据和知识正在从个人电脑上消失，变成我们无法访问、无法与之竞争的云。谁拥有我们的社交网络?这就像是反过来的大型机- pc革命。

我们可以把政治哲学留给另一本书。关键是,互联网提供了大量的潜在连接代码,现实情况是,对于大多数人来说,这是难以企及的,所以巨大而有趣的问题(在健康、教育、经济、交通、等等)仍然没有解决,因为没有办法连接代码,因此没有办法去连接可以一起工作的大脑来解决这些问题。

已经有很多尝试来解决连接代码的挑战。有数以千计的IETF规范，每个规范都解决了这个难题的一部分。对于应用程序开发人员来说，HTTP可能是一个简单到足以工作的解决方案，但是它鼓励开发人员和架构师从大服务器和thin，stupid的客户机的角度考虑问题，从而使问题变得更糟。

因此，今天人们仍然使用原始UDP和TCP、专有协议、HTTP和Websockets连接应用程序。它仍然痛苦、缓慢、难以扩展，而且本质上是集中的。分布式P2P架构主要是为了玩，而不是工作。有多少应用程序使用Skype或Bittorrent来交换数据?

这让我们回到编程科学。要改变世界，我们需要做两件事。第一，解决“如何在任何地方将任何代码连接到任何代码”的一般问题。第二，用最简单的模块来概括，让人们能够理解和使用。

这听起来简单得可笑。也许确实如此。这就是重点。



## 开始的前提

我们假设您至少使用了ZeroMQ的3.2版。我们假设您正在使用Linux机器或类似的东西。我们假设您可以或多或少地阅读C代码，因为这是示例的默认语言。我们假设，当我们编写像PUSH或SUBSCRIBE这样的常量时，您可以想象它们实际上被称为' ZMQ_PUSH '或' ZMQ_SUBSCRIBE '(如果编程语言需要的话)。



## 获取例子

The examples live in a public [GitHub repository](https://github.com/imatix/zguide). The simplest way to get all the examples is to clone this repository:

```
git clone --depth=1 https://github.com/imatix/zguide.git
```

接下来，浏览examples子目录。你会通过语言找到例子。如果您使用的语言中缺少示例，建议您提交翻译。正是由于许多人的努力，这篇文章才变得如此有用。所有示例都是根据MIT/X11授权的。



## 有求必应

让我们从一些代码开始。当然，我们从Hello World的例子开始。我们将创建一个客户机和一个服务器。客户端向服务器发送“Hello”，服务器以“World”作为响应。这是C语言的服务器，它在端口5555上打开一个ZeroMQ scoket，读取请求，然后用“World”对每个请求进行响应:

[hwserver: Hello World server in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:hwserver) | [C#](http://zguide.zeromq.org/cs:hwserver) | [Clojure](http://zguide.zeromq.org/clj:hwserver) | [CL](http://zguide.zeromq.org/lisp:hwserver) | [Delphi](http://zguide.zeromq.org/dpr:hwserver) | [Erlang](http://zguide.zeromq.org/es:hwserver) | [F#](http://zguide.zeromq.org/fsx:hwserver) | [Felix](http://zguide.zeromq.org/flx:hwserver) | [Go](http://zguide.zeromq.org/go:hwserver) | [Haskell](http://zguide.zeromq.org/hs:hwserver) | [Haxe](http://zguide.zeromq.org/hx:hwserver) | [Java](http://zguide.zeromq.org/java:hwserver) | [Lua](http://zguide.zeromq.org/lua:hwserver) | [Node.js](http://zguide.zeromq.org/js:hwserver) | [Objective-C](http://zguide.zeromq.org/m:hwserver)| [Perl](http://zguide.zeromq.org/pl:hwserver) | [PHP](http://zguide.zeromq.org/php:hwserver) | [Python](http://zguide.zeromq.org/py:hwserver) | [Q](http://zguide.zeromq.org/q:hwserver) | [Racket](http://zguide.zeromq.org/rkt:hwserver) | [Ruby](http://zguide.zeromq.org/rb:hwserver) | [Scala](http://zguide.zeromq.org/scala:hwserver) | [Tcl](http://zguide.zeromq.org/tcl:hwserver) | [Ada | Basic | ooc](http://zguide.zeromq.org/main:translate)

### 图2 - Request-Reply

![fig2.png](https://github.com/imatix/zguide/raw/master/images/fig2.png)

REQ-REP套接字对是同步的。客户机在循环中发出zmq_send()然后zmq_recv()，在循环中(或者只需要执行一次)。执行任何其他序列(例如，在一行中发送两条消息)都会导致send或recv调用返回的代码为-1。类似地，服务按这个顺序发出zmq_recv()和zmq_send()，只要它需要。

ZeroMQ使用C作为参考语言，这是我们在示例中使用的主要语言。如果您正在在线阅读本文，下面的示例链接将带您到其他编程语言的翻译。让我们在c++中比较相同的服务器:
```
//Hello World server in C++
//Binds REP socket to tcp://\*:5555
//Expects "Hello" from client, replies with "World"
//
#include <zmq.hpp>
#include <string>
#include <iostream>
#ifndef _WIN32
#include <unistd.h>
#else
#include <windows.h>

#define sleep(n)    Sleep(n)
#endif

int main () {
`    `*//  Prepare our context and socket*
`    `zmq::context_t context (1);
`    `zmq::socket_t socket (context, ZMQ_REP);
`    `socket.bind ("tcp://*:5555");

`    `**while** (true) {
`        `zmq::message_t request;

`        `*//  Wait for next request from client*
`        `socket.recv (&request);
`        `std::cout << "Received Hello" << std::endl;

`        `*//  Do some 'work'*
`        `sleep(1);

`        `*//  Send reply back to client*
`        `zmq::message_t reply (5);
`        `memcpy (reply.data (), "World", 5);
`        `socket.send (reply);
`    }`
`    `**return** 0;
}
```
*hwserver.cpp: Hello World server*

You can see that the ZeroMQ API is similar in C and C++. In a language like PHP or Java, we can hide even more and the code becomes even easier to read:
```
<?php
*/**`  `Hello World server\*`  `Binds REP socket to tcp://\*:5555\*`  `Expects "Hello" from client, replies with "World"\* @author Ian Barber <ian(dot)barber(at)gmail(dot)com>\*/*

$context = **new** ZMQContext(1);

*//  Socket to talk to clients*
$responder = **new** ZMQSocket($context, ZMQ::SOCKET_REP);
$responder->bind("tcp://*:5555");

**while** (**true**) {
`    `*//  Wait for next request from client*
`    `$request = $responder->recv();
`    `printf ("Received request: [%s]**\n**", $request);

`    `*//  Do some 'work'*
`    `sleep (1);

`    `*//  Send reply back to client*
`    `$responder->send("World");
}
```
*hwserver.php: Hello World server*
```
package guide;

//
//  Hello World server in Java
//  Binds REP socket to tcp://*:5555
//  Expects "Hello" from client, replies with "World"
//

import org.zeromq.SocketType;
import org.zeromq.ZMQ;
import org.zeromq.ZContext;

public class hwserver
{
    public static void main(String[] args) throws Exception
    {
        try (ZContext context = new ZContext()) {
            // Socket to talk to clients
            ZMQ.Socket socket = context.createSocket(SocketType.REP);
            socket.bind("tcp://*:5555");

            while (!Thread.currentThread().isInterrupted()) {
                byte[] reply = socket.recv(0);
                System.out.println(
                    "Received " + ": [" + new String(reply, ZMQ.CHARSET) + "]"
                );

                String response = "world";
                socket.send(response.getBytes(ZMQ.CHARSET), 0);

                Thread.sleep(1000); //  Do some 'work'
            }
        }
    }
}

```
*hwserver.java: Hello World server*

The server in other languages:

[hwserver: Hello World server in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:hwserver) | [C#](http://zguide.zeromq.org/cs:hwserver) | [Clojure](http://zguide.zeromq.org/clj:hwserver) | [CL](http://zguide.zeromq.org/lisp:hwserver) | [Delphi](http://zguide.zeromq.org/dpr:hwserver) | [Erlang](http://zguide.zeromq.org/es:hwserver) | [F#](http://zguide.zeromq.org/fsx:hwserver) | [Felix](http://zguide.zeromq.org/flx:hwserver) | [Go](http://zguide.zeromq.org/go:hwserver) | [Haskell](http://zguide.zeromq.org/hs:hwserver) | [Haxe](http://zguide.zeromq.org/hx:hwserver) | [Java](http://zguide.zeromq.org/java:hwserver) | [Lua](http://zguide.zeromq.org/lua:hwserver) | [Node.js](http://zguide.zeromq.org/js:hwserver) | [Objective-C](http://zguide.zeromq.org/m:hwserver)| [Perl](http://zguide.zeromq.org/pl:hwserver) | [PHP](http://zguide.zeromq.org/php:hwserver) | [Python](http://zguide.zeromq.org/py:hwserver) | [Q](http://zguide.zeromq.org/q:hwserver) | [Racket](http://zguide.zeromq.org/rkt:hwserver) | [Ruby](http://zguide.zeromq.org/rb:hwserver) | [Scala](http://zguide.zeromq.org/scala:hwserver) | [Tcl](http://zguide.zeromq.org/tcl:hwserver) | [Ada | Basic | ooc](http://zguide.zeromq.org/main:translate)

Here's the client code:

[hwclient: Hello World client in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:hwclient) | [C#](http://zguide.zeromq.org/cs:hwclient) | [Clojure](http://zguide.zeromq.org/clj:hwclient) | [CL](http://zguide.zeromq.org/lisp:hwclient) | [Delphi](http://zguide.zeromq.org/dpr:hwclient) | [Erlang](http://zguide.zeromq.org/es:hwclient) | [F#](http://zguide.zeromq.org/fsx:hwclient) | [Felix](http://zguide.zeromq.org/flx:hwclient) | [Go](http://zguide.zeromq.org/go:hwclient) | [Haskell](http://zguide.zeromq.org/hs:hwclient) | [Haxe](http://zguide.zeromq.org/hx:hwclient) | [Java](http://zguide.zeromq.org/java:hwclient) | [Lua](http://zguide.zeromq.org/lua:hwclient) | [Node.js](http://zguide.zeromq.org/js:hwclient) | [Objective-C](http://zguide.zeromq.org/m:hwclient)| [Perl](http://zguide.zeromq.org/pl:hwclient) | [PHP](http://zguide.zeromq.org/php:hwclient) | [Python](http://zguide.zeromq.org/py:hwclient) | [Q](http://zguide.zeromq.org/q:hwclient) | [Racket](http://zguide.zeromq.org/rkt:hwclient) | [Ruby](http://zguide.zeromq.org/rb:hwclient) | [Scala](http://zguide.zeromq.org/scala:hwclient) | [Tcl](http://zguide.zeromq.org/tcl:hwclient) | [Ada | Basic | ooc](http://zguide.zeromq.org/main:translate)

这看起来太简单了，不太现实，但是正如我们已经知道的，ZeroMQ套接字具有超能力。您可以同时将数千个客户机扔到这个服务器上，它将继续愉快而快速地工作。有趣的是，先启动客户机，然后再启动服务器，看看它是如何工作的，然后再考虑一下这意味着什么。
让我们简要地解释一下这两个程序实际上在做什么。它们创建要使用的ZeroMQ context 和socket。不要担心这些词的意思。你会知道的。服务器将其REP (reply) socket 绑定到端口5555。服务器在一个循环中等待一个请求，每次都用一个响应来响应。客户机发送请求并从服务器读取响应。

如果您关闭服务器(Ctrl-C)并重新启动它，客户机将无法正常恢复。从进程崩溃中恢复并不那么容易。
创建一个可靠的request-reply流非常复杂，直到可靠的Request-Reply模式才会涉及它。

幕后发生了很多事情，但对我们程序员来说，重要的是代码有多短、多好，以及即使在重负载下也不会崩溃的频率。这是request-reply模式，可能是使用ZeroMQ的最简单方法。它映射到RPC和经典的 client/server模型。



## 需要对Strings小小的注意

除了以字节为单位的大小外，ZeroMQ对您发送的数据一无所知。这意味着您要负责安全地格式化它，以便应用程序能够读取它。为对象和复杂数据类型执行此操作是专门库(如协议缓冲区)的工作。但即使是字符串，你也要小心。

在C语言和其他一些语言中，字符串以空字节结束。我们可以发送一个字符串，如“HELLO”与额外的空字节:
```
zmq_send (requester, "Hello", 6, 0);
```
但是，如果您从另一种语言发送一个字符串，它可能不会包含那个空字节。例如，当我们用Python发送相同的字符串时，我们这样做:
```
socket.send ("Hello")
```
然后连接到线路上的是长度(对于较短的字符串是一个字节)和作为单个字符的字符串内容。

### 图 3 - ZeroMQ的 string

![fig3.png](https://github.com/imatix/zguide/raw/master/images/fig3.png)

如果您从C程序中读取这段代码，您将得到一个看起来像字符串的东西，并且可能意外地表现得像字符串(如果幸运的话，这5个字节后面跟着一个无辜的潜伏的null)，但是它不是一个正确的字符串。当您的客户机和服务器不同意字符串格式时，您将得到奇怪的结果。

当您在C语言中从ZeroMQ接收字符串数据时，您不能简单地相信它已经安全终止。每次读取字符串时，都应该为额外的字节分配一个带空间的新缓冲区，复制字符串，并使用null正确地终止它。

因此，让我们建立一个规则，即**ZeroMQ字符串是指定长度的，并且在传输时不带null**。在最简单的情况下(在我们的示例中我们将这样做)，ZeroMQ字符串整洁地映射到ZeroMQ消息框架，它看起来像上面的图—长度和一些字节。

在C语言中，我们需要做的是接收一个ZeroMQ字符串并将其作为一个有效的C字符串发送给应用程序:
```
*//`  `Receive ZeroMQ string from socket and convert into C string//`  `Chops string at 255 chars, if it's longer*
**static** char *
s_recv (void *socket) {
`    `char buffer [256];
`    `int size = zmq_recv (socket, buffer, 255, 0);
`    `**if** (size == -1)
`        `**return** NULL;
`    `**if** (size > 255)
`        `size = 255;
`    `buffer [size] = \0;
`    `*/\* use strndup(buffer, sizeof(buffer)-1) in \*nix **/
`    `**return** strdup (buffer);
}
```
这是一个方便的helper函数，本着使我们可以有效重用的精神，让我们编写一个类似的s_send函数，它以正确的ZeroMQ格式发送字符串，并将其打包到一个可以重用的头文件中。

结果是zhelpers.h,它是一个相当长的源代码，而且只对C开发人员有乐趣，所以请在闲暇时阅读它。



## 版本报告
ZeroMQ有几个版本，通常，如果遇到问题，它会在以后的版本中得到修复。所以这是一个很有用的技巧，可以准确地知道您实际链接的是哪个版本的ZeroMQ。

这里有一个小程序可以做到这一点:

[version: ZeroMQ version reporting in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:version) | [C#](http://zguide.zeromq.org/cs:version) | [Clojure](http://zguide.zeromq.org/clj:version) | [CL](http://zguide.zeromq.org/lisp:version) | [Delphi](http://zguide.zeromq.org/dpr:version) | [Erlang](http://zguide.zeromq.org/es:version) | [F#](http://zguide.zeromq.org/fsx:version) | [Felix](http://zguide.zeromq.org/flx:version) | [Go](http://zguide.zeromq.org/go:version) | [Haskell](http://zguide.zeromq.org/hs:version) | [Java](http://zguide.zeromq.org/java:version) | [Lua](http://zguide.zeromq.org/lua:version) | [Node.js](http://zguide.zeromq.org/js:version) | [Objective-C](http://zguide.zeromq.org/m:version) | [Perl](http://zguide.zeromq.org/pl:version)| [PHP](http://zguide.zeromq.org/php:version) | [Python](http://zguide.zeromq.org/py:version) | [Q](http://zguide.zeromq.org/q:version) | [Ruby](http://zguide.zeromq.org/rb:version) | [Scala](http://zguide.zeromq.org/scala:version) | [Tcl](http://zguide.zeromq.org/tcl:version) | [Ada | Basic | Haxe | ooc | Racket](http://zguide.zeromq.org/main:translate)

## 传达信息
第二个经典模式是单向数据分发，其中服务器将更新推送到一组客户机。让我们看一个示例，它推出由邮政编码、温度和相对湿度组成的天气更新。我们将生成随机值，就像真实的气象站所做的那样。
这是服务器。我们将为这个应用程序使用端口5556:

[wuserver: Weather update server in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:wuserver) | [C#](http://zguide.zeromq.org/cs:wuserver) | [Clojure](http://zguide.zeromq.org/clj:wuserver) | [CL](http://zguide.zeromq.org/lisp:wuserver) | [Delphi](http://zguide.zeromq.org/dpr:wuserver) | [Erlang](http://zguide.zeromq.org/es:wuserver) | [F#](http://zguide.zeromq.org/fsx:wuserver) | [Felix](http://zguide.zeromq.org/flx:wuserver) | [Go](http://zguide.zeromq.org/go:wuserver) | [Haskell](http://zguide.zeromq.org/hs:wuserver) | [Haxe](http://zguide.zeromq.org/hx:wuserver) | [Java](http://zguide.zeromq.org/java:wuserver) | [Lua](http://zguide.zeromq.org/lua:wuserver) | [Node.js](http://zguide.zeromq.org/js:wuserver) | [Objective-C](http://zguide.zeromq.org/m:wuserver)| [Perl](http://zguide.zeromq.org/pl:wuserver) | [PHP](http://zguide.zeromq.org/php:wuserver) | [Python](http://zguide.zeromq.org/py:wuserver) | [Racket](http://zguide.zeromq.org/rkt:wuserver) | [Ruby](http://zguide.zeromq.org/rb:wuserver) | [Scala](http://zguide.zeromq.org/scala:wuserver) | [Tcl](http://zguide.zeromq.org/tcl:wuserver) | [Ada | Basic | ooc | Q](http://zguide.zeromq.org/main:translate)

这个更新流没有起点也没有终点，就像一个永无止境的广播。

下面是客户端应用程序，它监听更新流并获取与指定zip code有关的任何内容，默认情况下，纽约是开始任何冒险的好地方:

[wuclient: Weather update client in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:wuclient) | [C#](http://zguide.zeromq.org/cs:wuclient) | [Clojure](http://zguide.zeromq.org/clj:wuclient) | [CL](http://zguide.zeromq.org/lisp:wuclient) | [Delphi](http://zguide.zeromq.org/dpr:wuclient) | [Erlang](http://zguide.zeromq.org/es:wuclient) | [F#](http://zguide.zeromq.org/fsx:wuclient) | [Felix](http://zguide.zeromq.org/flx:wuclient) | [Go](http://zguide.zeromq.org/go:wuclient) | [Haskell](http://zguide.zeromq.org/hs:wuclient) | [Haxe](http://zguide.zeromq.org/hx:wuclient) | [Java](http://zguide.zeromq.org/java:wuclient) | [Lua](http://zguide.zeromq.org/lua:wuclient) | [Node.js](http://zguide.zeromq.org/js:wuclient) | [Objective-C](http://zguide.zeromq.org/m:wuclient)| [Perl](http://zguide.zeromq.org/pl:wuclient) | [PHP](http://zguide.zeromq.org/php:wuclient) | [Python](http://zguide.zeromq.org/py:wuclient) | [Racket](http://zguide.zeromq.org/rkt:wuclient) | [Ruby](http://zguide.zeromq.org/rb:wuclient) | [Scala](http://zguide.zeromq.org/scala:wuclient) | [Tcl](http://zguide.zeromq.org/tcl:wuclient) | [Ada | Basic | ooc | Q](http://zguide.zeromq.org/main:translate)
### 图 4 - Publish-Subscribe

![fig4.png](https://github.com/imatix/zguide/raw/master/images/fig4.png)

注意，当您使用 SUB socket 时，必须使用zmq_setsockopt()和SUBSCRIBE设置订阅，如下面的代码所示。如果不设置任何订阅，就不会收到任何消息。这是初学者常犯的错误。订阅者可以设置许多订阅，这些订阅被添加到一起。也就是说，如果更新匹配任何订阅，订阅方将接收更新。订阅者还可以取消特定的订阅。订阅通常是，但不一定是可打印的字符串。请参阅zmq_setsockopt()了解其工作原理。

PUB-SUB socket 对（双方的意思）是异步的。客户机在循环中执行zmq_recv()(或者它只需要一次)。试图向 SUB socket发送消息将导致错误（单向的只能收不能发）。类似地，服务在需要的时候执行zmq_send()，但是不能在PUB scoket上执行zmq_recv()（单向的只能发不能收）。

理论上，对于ZeroMQ sockets，哪一端连接和哪一端绑定并不重要。然而，在实践中有一些未记录的差异，我将在稍后讨论。现在，绑定PUB并连接SUB，除非您的网络设计不允许这样做。

关于 PUB-SUB sockets，还有一件更重要的事情需要了解:您不知道订阅者何时开始接收消息。即使启动订阅服务器，等一下，然后启动发布服务器，订阅服务器也始终会错过发布服务器发送的第一个消息。这是因为当订阅服务器连接到发布服务器时(这需要一点时间，但不是零)，发布服务器可能已经在发送消息了。

这种“慢速加入者”症状经常出现在很多人身上，我们将对此进行详细解释。
记住ZeroMQ执行异步I/O，即，在后台。假设有两个节点按如下顺序执行此操作:

- 订阅者连接到端点并接收和计数消息。
- 发布者绑定到端点并立即发送1,000条消息。

那么订阅者很可能不会收到任何东西。您会闪烁（困扰？），检查是否设置了正确的过滤器，然后重试一次，订阅者仍然不会收到任何内容。

建立TCP连接涉及到握手和握手，握手需要几毫秒，这取决于您的网络和对等点之间的跳数。在这段时间里，ZeroMQ可以发送许多消息。为了便于讨论，假设建立一个连接需要5毫秒，并且相同的链接每秒可以处理1M条消息。在订阅者连接到发布者的5毫秒期间，发布者只需要1毫秒就可以发送那些1K消息。

在Sockets and Patterns中，我们将解释如何同步发布者和订阅者，以便在订阅者真正连接并准备好之前不会开始发布数据。有一个简单而愚蠢的方法可以延迟发布，那就是sleep。但是，不要在实际应用程序中这样做，因为它非常脆弱、不优雅且速度很慢。使用sleep向您自己证明发生了什么，然后等待Sockets and Patterns来查看如何正确地执行此操作。

同步的另一种选择是简单地假设发布的数据流是无限的，没有开始和结束。还有一种假设是订阅者不关心在启动之前发生了什么。这是我们如何构建天气客户端示例的。

因此，客户端订阅其选择的zip code，并为该zip code收集100个更新。如果zip code是随机分布的，这意味着大约有一千万次来自服务器的更新。您可以启动客户机，然后启动服务器，客户机将继续工作。您可以随时停止和重启服务器，客户机将继续工作。当客户机收集了它的100个更新后，它计算平均值，打印并退出。

关于发布-订阅(发布-订阅) publish-subscribe (pub-sub) 模式的几点:

- 订阅服务器可以连接到多个发布服务器，每次使用一个连接调用。然后，数据将到达并交错(“公平排队”)，这样就不会有一个发布者淹没其他发布者。

- 如果发布者没有连接的订阅者，那么它将删除所有消息。

- 如果您正在使用TCP，而订阅服务器很慢，则消息将在发布服务器上排队。稍后，我们将研究如何使用“高水位标记(high-water mark)”来保护publishers 不受此影响。

从ZeroMQ v3.x，当使用连接的协议(tcp://或ipc://)时，过滤发生在发布端。
使用epgm://协议，过滤发生在订阅方。在ZeroMQ v2.x，所有过滤都发生在订阅端。

我的笔记本电脑是2011年的英特尔i5，接收和过滤1000万条信息的时间是这样的:

```
$ time wuclient
Collecting updates from weather server...
Average temperature for zipcode '10001 ' was 28F

real    0m4.470s
user    0m0.000s
sys     0m0.008s
```



## Divide and Conquer(分而治之)
**图 5 - Parallel Pipeline**

![fig5.png](https://github.com/imatix/zguide/raw/master/images/fig5.png)

作为最后一个例子(您肯定已经厌倦了有趣的代码，并希望重新研究比较抽象规范的语言学讨论)，让我们来做一些超级计算。然后咖啡。我们的超级计算应用程序是一个相当典型的并行处理模型。我们有:

- 可同时完成多项任务的ventilator 
- 一组处理任务的workers 
- 从工作进程收集结果的sink 

在现实中，workers 在超级快的机器上运行，可能使用gpu(图形处理单元)来做艰难的计算。这是ventilator 。它会生成100个任务，每个任务都有一条消息告诉worker睡眠几毫秒:

[taskvent: Parallel task ventilator in C](javascript:;)

[C++](http://zguide.zeromq.org/cpp:taskvent) | [C#](http://zguide.zeromq.org/cs:taskvent) | [Clojure](http://zguide.zeromq.org/clj:taskvent) | [CL](http://zguide.zeromq.org/lisp:taskvent) | [Delphi](http://zguide.zeromq.org/dpr:taskvent) | [Erlang](http://zguide.zeromq.org/es:taskvent) | [F#](http://zguide.zeromq.org/fsx:taskvent) | [Felix](http://zguide.zeromq.org/flx:taskvent) | [Go](http://zguide.zeromq.org/go:taskvent) | [Haskell](http://zguide.zeromq.org/hs:taskvent) | [Haxe](http://zguide.zeromq.org/hx:taskvent) | [Java](http://zguide.zeromq.org/java:taskvent) | [Lua](http://zguide.zeromq.org/lua:taskvent) | [Node.js](http://zguide.zeromq.org/js:taskvent) | [Objective-C](http://zguide.zeromq.org/m:taskvent)| [Perl](http://zguide.zeromq.org/pl:taskvent) | [PHP](http://zguide.zeromq.org/php:taskvent) | [Python](http://zguide.zeromq.org/py:taskvent) | [Ruby](http://zguide.zeromq.org/rb:taskvent) | [Scala](http://zguide.zeromq.org/scala:taskvent) | [Tcl](http://zguide.zeromq.org/tcl:taskvent) | [Ada | Basic | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)

这是worker应用程序。它接收到一条消息，休眠几秒钟，然后发出信号，表示它已经完成:
[taskwork: Parallel task worker in C](javascript:;)

[C++](http://zguide.zeromq.org/cpp:taskwork) | [C#](http://zguide.zeromq.org/cs:taskwork) | [Clojure](http://zguide.zeromq.org/clj:taskwork) | [CL](http://zguide.zeromq.org/lisp:taskwork) | [Delphi](http://zguide.zeromq.org/dpr:taskwork) | [Erlang](http://zguide.zeromq.org/es:taskwork) | [F#](http://zguide.zeromq.org/fsx:taskwork) | [Felix](http://zguide.zeromq.org/flx:taskwork) | [Go](http://zguide.zeromq.org/go:taskwork) | [Haskell](http://zguide.zeromq.org/hs:taskwork) | [Haxe](http://zguide.zeromq.org/hx:taskwork) | [Java](http://zguide.zeromq.org/java:taskwork) | [Lua](http://zguide.zeromq.org/lua:taskwork) | [Node.js](http://zguide.zeromq.org/js:taskwork) | [Objective-C](http://zguide.zeromq.org/m:taskwork)| [Perl](http://zguide.zeromq.org/pl:taskwork) | [PHP](http://zguide.zeromq.org/php:taskwork) | [Python](http://zguide.zeromq.org/py:taskwork) | [Ruby](http://zguide.zeromq.org/rb:taskwork) | [Scala](http://zguide.zeromq.org/scala:taskwork) | [Tcl](http://zguide.zeromq.org/tcl:taskwork) | [Ada | Basic | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)

下面是sink应用程序。它收集了100个任务，然后计算出整个处理过程花费了多长时间，这样我们就可以确认，如果有多个任务，那么这些工人确实是并行运行的:
[tasksink: Parallel task sink in C](javascript:;)

[C++](http://zguide.zeromq.org/cpp:tasksink) | [C#](http://zguide.zeromq.org/cs:tasksink) | [Clojure](http://zguide.zeromq.org/clj:tasksink) | [CL](http://zguide.zeromq.org/lisp:tasksink) | [Delphi](http://zguide.zeromq.org/dpr:tasksink) | [Erlang](http://zguide.zeromq.org/es:tasksink) | [F#](http://zguide.zeromq.org/fsx:tasksink) | [Felix](http://zguide.zeromq.org/flx:tasksink) | [Go](http://zguide.zeromq.org/go:tasksink) | [Haskell](http://zguide.zeromq.org/hs:tasksink) | [Haxe](http://zguide.zeromq.org/hx:tasksink) | [Java](http://zguide.zeromq.org/java:tasksink) | [Lua](http://zguide.zeromq.org/lua:tasksink) | [Node.js](http://zguide.zeromq.org/js:tasksink) | [Objective-C](http://zguide.zeromq.org/m:tasksink)| [Perl](http://zguide.zeromq.org/pl:tasksink) | [PHP](http://zguide.zeromq.org/php:tasksink) | [Python](http://zguide.zeromq.org/py:tasksink) | [Ruby](http://zguide.zeromq.org/rb:tasksink) | [Scala](http://zguide.zeromq.org/scala:tasksink) | [Tcl](http://zguide.zeromq.org/tcl:tasksink) | [Ada | Basic | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)

- 1 worker: total elapsed time: 5034 msecs.
- 2 workers: total elapsed time: 2421 msecs.
- 4 workers: total elapsed time: 1018 msecs.

让我们更详细地看看这段代码的一些方面:

- worker将上游连接到ventilator ，下游连接到sink。这意味着可以任意添加worker。如果worker绑定到他们的端点，您将需要(a)更多的端点和(b)每次添加worker时修改ventilator 和/或sink。我们说ventilator 和sink是我们建筑的“稳定”部分，worker是建筑的“动态”部分。

- 我们必须在所有worker正在启动和运行后开始批处理(We have to synchronize the start of the batch with all workers being up and running.)。这是ZeroMQ中一个相当常见的问题，没有简单的解决方案。' zmq_connect '方法需要一定的时间。因此，当一组worker连接到ventilator 时，第一个成功连接的worker将在短时间内获得大量信息，而其他worker也在连接。如果不以某种方式同步批处理的开始，系统就根本不会并行运行。试着把ventilator 里的等待时间去掉，看看会发生什么。

- ventilator 的PUSH socket 将任务分配给worker(假设他们在批处理开始输出之前都连接好了)。这就是所谓的“负载平衡”，我们将再次详细讨论它。

- sink的PULL均匀地收集worker的结果。这叫做“公平排队”。
**图 6 - Fair Queuing**

![fig6.png](https://github.com/imatix/zguide/raw/master/images/fig6.png)

管道模式(pipeline pattern)还表现出“慢连接者”综合征，导致指责PUSH sockets不能正确地平衡负载。如果您正在使用 PUSH 和 PULL，而您的一个worker获得的消息比其他worker多得多，这是因为这个PULL socket连接得比其他worker更快，并且在其他worker设法连接之前捕获了大量消息。如果您想要适当的负载平衡，您可能需要查看Advanced Request-Reply Patterns中的负载平衡模式。



## ZeroMQ编程
看过一些例子之后，您一定很想开始在一些应用程序中使用ZeroMQ。在你开始之前，深呼吸，放松，并思考一些基本的建议，这会帮你减轻很多压力和困惑。

- 循序渐进的学习ZeroMQ。它只是一个简单的API，但它隐藏了大量的可能性。慢慢地把握每一种可能性。

- 写好代码。丑陋的代码隐藏了问题，让别人很难帮助你。您可能已经习惯了无意义的变量名，但是阅读您代码的人不会习惯。使用真实的单词，而不是“我太粗心了，不能告诉您这个变量的真正用途”。使用一致的缩进和干净的布局。写好代码，你的世界就会更舒适。

- 一边做一边测试。当您的程序无法工作时，您应该知道应该归咎于哪五行。当你使用极具魅力的ZeroMQ的时候，这一点尤其正确，因为在你开始尝试的几次之后，它都不会起作用。

- - 当您发现有些东西不像预期的那样工作时，将您的代码分成几部分，测试每一部分，看看哪一部分不工作。ZeroMQ允许你编写模块化代码;利用这一点。

- 根据需要进行抽象(类、方法等)。如果你复制/粘贴了很多代码，你也会复制/粘贴错误。



## 正确理解 Context 

ZeroMQ应用程序总是从创建Context开始，然后使用Context创建sockets。在C语言中，它是zmq_ctx_new()调用。您应该在流程中创建并使用一个Context。从技术上讲，Context是一个进程中所有sockets的容器，它充当inproc sockets的传输，inproc sockets是在一个进程中连接线程的最快方式。如果在运行时一个流程有两个Context，那么它们就像独立的ZeroMQ实例。如果这是你明确想要的，好的，否则记住:

**Call zmq_ctx_new() once at the start of a process, and zmq_ctx_destroy() once at the end.**

在流程开始时调用zmq_ctx_new()一次，在流程结束时调用zmq_ctx_destroy()一次。
如果使用fork()系统调用，那么在fork之后和子进程代码的开头执行zmq_ctx_new()。通常，您希望在子进程中执行有趣的(ZeroMQ)操作，而在父进程中执行乏味的流程管理。



## 退出前清理

一流的程序员与一流的杀手有相同的座右铭:当你完成工作时，总是要清理干净。当您在Python之类的语言中使用ZeroMQ时，会自动释放一些内容。但是在使用C语言时，必须小心地释放对象，否则会导致内存泄漏、应用程序不稳定，通常还会产生坏的因果报应。

内存泄漏是一回事，但是ZeroMQ对如何退出应用程序非常挑剔。原因是技术性的和痛苦的，但是结果是，如果您打开任何sockets ，zmq_ctx_destroy()函数将永远挂起。即使关闭所有sockets ，默认情况下，如果有挂起连接或发送，zmq_ctx_destroy()将永远等待，除非在关闭这些sockets 之前将这些sockets 的逗留时间设置为零。

我们需要担心的ZeroMQ对象是 messages, sockets, 和 contexts。幸运的是，它非常简单，至少在简单的程序中:

- 可以时使用zmq_send()和zmq_recv()，因为它避免了使用zmq_msg_t对象。

- 如果您确实使用zmq_msg_recv()，那么总是在使用完接收到的消息后立即释放它，方法是调用zmq_msg_close()。

- 如果您打开和关闭了许多sockets，这可能是您需要重新设计应用程序的标志。在某些情况下，在销毁上下文之前不会释放sockets句柄。

- 退出程序后，关闭socket，然后调用zmq_ctx_destroy()。这会销毁context。

这至少是C开发的情况。在具有自动对象销毁的语言中，离开作用域时将销毁套接字和上下文。
如果使用异常，则必须在类似“final”块的地方进行清理，这与任何资源都是一样的。

如果你在做多线程的工作，它会变得比这更复杂。我们将在下一章中讨论多线程，但是由于有些人会不顾警告，在安全地行走前先尝试运行，下面是在多线程ZeroMQ应用程序中实现干净退出的快速而又脏的指南。

首先，不要尝试从多个线程使用同一个socket。请不要解释为什么你认为这将是非常有趣的，只是请不要这样做。接下来，您需要关闭具有正在进行的请求的每个socket。正确的方法是设置一个较低的逗留值(1秒)，然后关闭socket。如果您的语言绑定在销毁context时没有自动为您完成此任务，我建议发送一个补丁。

最后，销毁context。这将导致任何阻塞接收或轮询或发送附加线程(即,共享context)返回一个错误。捕获该错误，然后设置逗留，关闭该线程中的socket，然后退出。不要两次破坏相同的Context。主线程中的zmq_ctx_destroy将阻塞，直到它所知道的所有socket都安全关闭为止。

瞧!这是非常复杂和痛苦的，任何称职的语言绑定作者都会自动地这样做，使socket关闭舞蹈变得不必要。



## 为什么我们需要ZeroMQ
既然您已经看到了ZeroMQ的作用，让我们回到“为什么”。

现在的许多应用程序都是由跨越某种网络(LAN或Internet)的组件组成的。因此，许多应用程序开发人员最终都会进行某种消息传递。一些开发人员使用消息队列产品，但大多数时候他们自己使用TCP或UDP来完成。这些协议并不难使用，但是从a向B发送几个字节与以任何一种可靠的方式进行消息传递之间有很大的区别。

让我们看看在开始使用原始TCP连接各个部分时所面临的典型问题。任何可重用的消息层都需要解决所有或大部分问题:

- 我们如何处理I/O?我们的应用程序是阻塞还是在后台处理I/O ?这是一个关键的设计决策。阻塞I/O会创建伸缩性不好的体系结构。但是后台I/O很难正确地执行。
- 我们如何处理动态组件，即，暂时消失的碎片?我们是否将组件正式划分为“客户端”和“服务器”，并要求服务器不能消失?如果我们想把服务器连接到服务器呢?我们是否每隔几秒钟就尝试重新连接?
- 我们如何在网络上表示消息?我们如何设置数据的框架，使其易于读写，不受缓冲区溢出的影响，对小消息有效，但对于那些戴着派对帽子跳舞的猫的大型视频来说，这已经足够了吗?
- 我们如何处理无法立即交付的消息?特别是，如果我们正在等待一个组件重新联机?我们是丢弃消息，将它们放入数据库，还是放入内存队列?
- 我们在哪里存储消息队列?如果从队列读取的组件非常慢，导致我们的队列增加，会发生什么?那么我们的策略是什么呢?
- 我们如何处理丢失的消息?我们是等待新数据、请求重发，还是构建某种确保消息不会丢失的可靠性层?如果这个层本身崩溃了呢?
- 如果我们需要使用不同的网络传输怎么办?比如说，多播而不是TCP单播?还是IPv6 ?我们是否需要重写应用程序，还是在某个层中抽象传输?
- 我们如何路由消息?我们可以向多个对等点发送相同的消息吗?我们可以将回复发送回原始请求者吗?
- 我们如何为另一种语言编写API ?我们是重新实现一个线级协议，还是重新打包一个库?如果是前者，如何保证栈的高效稳定?如果是后者，我们如何保证互操作性?
- 我们如何表示数据，以便在不同的体系结构之间读取数据?我们是否对数据类型强制执行特定的编码?这是消息传递系统的工作，而不是更高一层的工作。
- 我们如何处理网络错误?我们是等待并重试，默不作声地忽略它们，还是中止?

以一个典型的开源项目为例，比如Hadoop Zookeeper，在src/ C /src/ Zookeeper . C中读取C API代码。当我在2013年1月读到这段代码时，它是4200行神秘代码，其中有一个未文档化的客户机/服务器网络通信协议。我认为这是有效的，因为它使用轮询而不是选择。但实际上，Zookeeper应该使用通用消息层和显式文档化的有线级协议。对于团队来说，一遍又一遍地构建这个特定的轮子是非常浪费的。

但是如何创建可重用的消息层呢?为什么在如此多的项目需要这种技术的时候，人们仍然在用一种很困难的方式来完成它，在他们的代码中驱动TCP套接字，并一次又一次地解决长列表中的问题?

事实证明，构建可重用的消息传递系统是非常困难的，这就是为什么很少有自由/开源软件项目尝试过，以及为什么商业消息传递产品是复杂的、昂贵的、不灵活的和脆弱的。2006年，iMatix设计了AMQP，它开始为自由/开源软件开发人员提供消息系统的第一个可重用配方。AMQP比其他许多设计都要好，但仍然相对复杂、昂贵和脆弱。学习使用它需要几周的时间，而创建当事情变得棘手时不会崩溃的稳定的体系结构需要几个月的时间。

**图 7 - Messaging as it Starts**

![fig7.png](https://github.com/imatix/zguide/raw/master/images/fig7.png)

大多数消息传递项目，如AMQP，都试图通过发明一个新的概念“broker”来解决这一长串问题，该概念负责寻址、路由和排队，从而以可重用的方式解决这些问题。这将导致客户机/服务器协议或一些未文档化协议之上的一组api，这些协议允许应用程序与此broker通信。在减少大型网络的复杂性方面，Brokers 是一件很好的事情。但是在Zookeeper这样的产品中添加基于代理的消息会让情况变得更糟，而不是更好。这将意味着添加一个额外的大框和一个新的单点故障。broker 迅速成为一个瓶颈和一个需要管理的新风险。如果软件支持它，我们可以添加第二个、第三个和第四个broker ，并制定一些故障转移方案。人们这样做。它创造了更多的活动部件，更多的复杂性，以及更多需要打破的东西。

以broker 为中心需要自己的operations team。你确实需要日日夜夜地观察这些brokers，当他们开始行为不端时，你要用棍子打他们。你需要盒子，你需要备份盒子，你需要人们来管理这些盒子。它只值得为大型应用程序做很多移动的部分，由几个团队的人在几年的时间内构建。

**图 8 - Messaging as it Becomes**

![fig8.png](https://github.com/imatix/zguide/raw/master/images/fig8.png)

因此，中小型应用程序开发人员陷入了困境。它们要么避免网络编程，要么开发不可伸缩的单片应用程序。或者他们跳入网络编程，使脆弱、复杂的应用程序难以维护。或者他们押注于一个消息传递产品，最终开发出可伸缩的应用程序，这些应用程序依赖于昂贵且容易崩溃的技术。一直没有真正好的选择，这也许就是为什么messaging 在很大程度上停留在上个世纪，并激起强烈的情感:对用户来说是负面的，对那些销售支持和许可的人来说是欢欣鼓舞的。

我们需要的是能够完成消息传递功能的东西，但它的实现方式非常简单和廉价，可以在任何应用程序中运行，成本几乎为零。它应该是一个链接的库，没有任何其他依赖关系。没有额外的移动部件，所以没有额外的风险。它应该运行在任何操作系统上，并且可以使用任何编程语言。

这就是ZeroMQ:一个高效的、可嵌入的库，它解决了应用程序需要在不花费太多成本的情况下在网络上保持良好弹性的大部分问题。

 特别地:

- 它在后台线程中异步处理I/O。这些线程使用无锁数据结构与应用程序线程通信，因此并发ZeroMQ应用程序不需要锁、信号量或其他等待状态。

- 组件可以动态进出，ZeroMQ将自动重新连接。这意味着您可以以任何顺序启动组件。您可以创建“面向服务的体系结构”(service-oriented architecture, soa)，其中服务可以随时加入和离开网络。

- 它在需要时自动对消息进行排队。它很聪明地做到了这一点，在对消息进行排队之前，尽可能地将消息推送到接收端。

- 它有办法处理过满的队列(称为“高水位”)。当队列已满时，ZeroMQ会根据您正在执行的消息类型(所谓的“模式”)自动阻塞发送者或丢弃消息。

- 它允许您的应用程序通过任意传输相互通信:TCP、多播、进程内、进程间。您不需要更改代码来使用不同的传输。

- 它使用依赖于消息传递模式的不同策略安全地处理慢速/阻塞的readers 。

- 它允许您使用各种模式路由消息，比如请求-应答和发布-订阅。这些模式是取决于你如何创建拓扑结构的，即网络的结构。

- 它允许您创建代理来通过一个调用对消息进行排队、转发或捕获。代理可以降低网络的互连复杂性。

- 它通过在网络上使用一个简单的框架，完全按照发送的方式传递整个消息。如果您写了一条10k的消息，您将收到一条10k的消息。

- 它不将任何格式强加于消息。它们是从0到gb大小的水滴。当您想要表示数据时，您可以在顶部选择一些其他产品，例如msgpack、谷歌的协议缓冲区等。

- 它通过在有意义的情况下自动重试来智能地处理网络错误。

- 它可以减少你的碳足迹。用更少的CPU做更多的事情意味着您的机器使用更少的能量，并且您可以让旧的机器使用更长时间。 Al Gore会喜欢ZeroMQ的。
- 实际上ZeroMQ做的远不止这些。
它对如何开发支持网络的应用程序具有颠覆性的影响。从表面上看，它是一个受套接字启发的API，您可以在其上执行' zmq_recv() '和' zmq_send() '。但是消息处理很快成为中心循环，您的应用程序很快就分解为一组消息处理任务。它优雅自然。它是可伸缩的:每个任务都映射到一个节点，节点之间通过任意传输进行通信。一个进程中的两个节点(节点是一个线程)、一个框中的两个节点(节点是一个进程)或一个网络上的两个节点(节点是一个框)—都是一样的，没有应用程序代码更改。


（可伸缩性的scoket）
## Socket Scalability（可伸缩性的scoket）
让我们看看ZeroMQ的可伸缩性。下面是一个shell脚本，它先启动天气服务器，然后并行地启动一堆客户机:

```
wuserver &
wuclient 12345 &
wuclient 23456 &
wuclient 34567 &
wuclient 45678 &
wuclient 56789 &
```

As the clients run, we take a look at the active processes using the `top` command', and we see something like (on a 4-core box):

```
PID  USER  PR  NI  VIRT  RES  SHR S %CPU %MEM   TIME+  COMMAND
7136  ph   20   0 1040m 959m 1156 R  157 12.0 16:25.47 wuserver
7966  ph   20   0 98608 1804 1372 S   33  0.0  0:03.94 wuclient
7963  ph   20   0 33116 1748 1372 S   14  0.0  0:00.76 wuclient
7965  ph   20   0 33116 1784 1372 S    6  0.0  0:00.47 wuclient
7964  ph   20   0 33116 1788 1372 S    5  0.0  0:00.25 wuclient
7967  ph   20   0 33072 1740 1372 S    5  0.0  0:00.35 wuclient
```

让我们想一下这里发生了什么。气象服务器只有一个套接字，但是这里我们让它并行地向五个客户机发送数据。我们可以有成千上万的并发客户端。服务器应用程序不会看到它们，也不会直接与它们对话。所以ZeroMQ套接字就像一台小服务器，默默地接受客户机请求，并以网络最快的速度将数据发送给它们。它是一个多线程服务器，可以从CPU中挤出更多的能量。



## 从ZeroMQ v2.2升级到ZeroMQ v3.2

## Compatible Changes(兼容变更)

这些更改不会直接影响现有的应用程序代码:

- Pub-sub filtering现在运行在在publisher而不是在subscriber，这在许多pub-sub 用例中显著提高了性能 You can mix v3.2 and v2.1/v2.2 publishers and subscribers safely.

- ZeroMQ v3.2 has many new API methods (`zmq_disconnect()`, `zmq_unbind()`, `zmq_monitor()`, `zmq_ctx_set()`, etc.)



## 不兼容的变更
这些是影响应用程序和语言绑定的主要领域:（These are the main areas of impact on applications and language bindings）:

- Changed send/recv methods: `zmq_send()` and `zmq_recv()` have a different, simpler interface, and the old functionality is now provided by `zmq_msg_send()` and `zmq_msg_recv()`. Symptom: compile errors. Solution: fix up your code.

- 这两种方法成功时返回正值，错误时返回-1。在v2。他们成功时总是零回报。症状:工作正常时明显的错误。解决方案:严格测试返回代码= -1，而不是非零.

- `zmq_poll()` 现在等待毫秒，而不是微秒。症状:应用程序停止响应(实际上响应慢了1000倍)。解决方案：:在所有zmq_poll调用中，使用下面定义的ZMQ_POLL_MSEC宏。

- “ZMQ_NOBLOCK”现在称为“ZMQ_DONTWAIT”。症状:在“ZMQ NOBLOCK”宏上编译失败。

- “ZMQ_HWM”socket 选项现在分为“ZMQ_SNDHWM”和“ZMQ_RCVHWM”。症状:在' ZMQ_HWM '宏上编译失败。

- 大多数但不是所有的' zmq_getsockopt() '选项现在都是整数值。症状:运行时错误返回' zmq_setsockopt '和' zmq_getsockopt '。

- ' ZMQ_SWAP '选项已被删除。症状:在' ZMQ_SWAP '上编译失败。解决方案:重新设计使用此功能的任何代码。



## Suggested Shim Macros
对于希望在两个v2上运行的应用程序。x和v3.2，例如语言绑定，我们的建议是尽可能地模拟v3.2。这里有一些C宏定义，可以帮助您的C/ c++代码跨两个版本工作(取自[CZMQ](http://czmq.zeromq.org/)):
```
\#ifndef ZMQ_DONTWAIT
\#`   `define ZMQ_DONTWAIT`     `ZMQ_NOBLOCK
\#endif
\#if ZMQ_VERSION_MAJOR == 2
\#`   `define zmq_msg_send(msg,sock,opt) zmq_send (sock, msg, opt)
\#`   `define zmq_msg_recv(msg,sock,opt) zmq_recv (sock, msg, opt)
\#`   `define zmq_ctx_destroy(context) zmq_term(context)
\#`   `define ZMQ_POLL_MSEC`    `1000`        `*//  zmq_poll is usec*
\#`   `define ZMQ_SNDHWM ZMQ_HWM
\#`   `define ZMQ_RCVHWM ZMQ_HWM
\#elif ZMQ_VERSION_MAJOR == 3
\#`   `define ZMQ_POLL_MSEC`    `1`           `*//  zmq_poll is msec*
\#endif
```


## Warning: 不稳定的范例!
传统的网络编程建立在一个socket 与一个connection、一个peer通信的一般假设之上。有多播协议，但这些都是外来的。当我们假设“"one socket = one connection"”时，我们以某种方式扩展架构。我们创建逻辑线程，其中每个线程使用一个socket和一个peer。我们在这些线程中放置intelligence 和状态。
在ZeroMQ领域，sockets是快速后台通信引擎的入口，这些引擎可以自动地为您管理一整套连接。您无法查看、处理、打开、关闭或将状态附加到这些连接。无论您使用阻塞发送、接收或轮询，您只能与socket通信，而不是它为您管理的连接。连接是私有的，不可见的，这是ZeroMQ可伸缩性的关键。
这是因为，与socket通信的代码可以处理任意数量的连接，而无需更改周围的任何网络协议。ZeroMQ中的消息传递模式比应用程序代码中的消息传递模式扩展得更便宜。

所以一般的假设不再适用。当您阅读代码示例时，您的大脑将尝试将它们映射到您所知道的内容。您将读取“socket”并认为“啊，这表示到另一个节点的连接”。这是错误的。当你读到“thread”时，你的大脑又会想，“啊，一个thread代表了与另一个节点的连接”，你的大脑又会出错。
如果你是第一次读这本指南的话,要意识到这一点，直到你在一两天内编写ZeroMQ代码(可能是三到四天),你可能会感到困惑,特别是ZeroMQ使事情多么简单,你可以试着把这个普遍的假设强加给ZeroMQ,它不会工作。然后你将经历你的启蒙和信任的时刻，当一切都变得清晰的时候，你将经历“zap-pow-kaboom satori”的时刻。



# Chapter 2 - Sockets and Patterns

在第1章—基础知识中，我们将ZeroMQ作为驱动器，并提供了一些主要ZeroMQ模式的基本示例:请求-应答、发布-订阅和管道。在本章中，我们将亲自动手，开始学习如何在实际程序中使用这些工具。

我们将讨论:

- 如何创建和使用ZeroMQ Sockets。
- 如何在Sockets上发送和接收消息。
如何围绕ZeroMQ的异步I/O模型构建应用程序。
如何在一个线程中处理多个Sockets。
如何正确处理致命和非致命错误。
如何处理像Ctrl-C这样的中断信号。
如何干净地关闭ZeroMQ应用程序。
如何检查ZeroMQ应用程序的内存泄漏。
如何发送和接收多部分消息。
如何跨网络转发消息。
如何构建一个简单的消息队列代理（broker）。
如何使用ZeroMQ编写多线程应用程序。
如何使用ZeroMQ在线程之间发送信号。
如何使用ZeroMQ来协调节点网络。
如何为发布-订阅创建和使用消息信封。
使用HWM (high-water mark)来防止内存溢出。



## The Socket API
说句老实话，ZeroMQ对你耍了个花招，对此我们不道歉。这是为了你好，我们比你更伤心。ZeroMQ提供了一个熟悉的基于Socket的API，要隐藏一堆消息处理引擎需要付出很大的努力。然而，结果将慢慢地修正您关于如何设计和编写分布式软件的世界观。

Socket实际上是网络编程的标准API， as well as being useful for stopping your eyes from falling onto your cheeks（怎么翻译？ 大跌眼镜?）。ZeroMQ对开发人员特别有吸引力的一点是，它使用Socket和messages ，而不是其他任意一组概念。感谢Martin Sustrik的成功。它将“面向消息的中间件”变成了“额外辛辣(Extra Spicy ，升级版)的Sockets!”这让我们对披萨产生了一种奇怪的渴望，并渴望了解更多。
就像最喜欢的菜一样，ZeroMQsockets 很容易消化。sockets 的生命由四个部分组成，就像BSDsockets 一样：

- 创造和摧毁sockets ，它们一起形成一个插座生命的业力循环(see `zmq_socket()`, `zmq_close()`).

- 通过设置套接字上的选项并在必要时检查它们来配置套接字(see `zmq_setsockopt()`, `zmq_getsockopt()`).

- Plugging sockets into the network topology by creating ZeroMQ connections to and from them (see `zmq_bind()`, `zmq_connect()`).

- 通过创建与它们之间的ZeroMQ连接，将sockets插入网络拓扑(see `zmq_msg_send()`, `zmq_msg_recv()`).

注意，套接字总是空指针，消息(我们很快就会讲到)是结构。所以在C语言中，按原样传递sockets ，但是在所有处理消息的函数中传递消息的地址，比如zmq_msg_send()和zmq_msg_recv()。作为一个助记符，请认识到“在ZeroMQ中，您所有的sockets 都属于我们”，但是消息实际上是您在代码中拥有的东西。
创建、销毁和配置Sockets的工作原理与您对任何对象的期望一样。但是请记住ZeroMQ是一个异步的、有弹性的结构。这对我们如何将Sockets插入网络拓扑以及之后如何使用Sockets有一定的影响。



## 将Sockets插入拓扑中

要在两个节点之间创建连接，可以在一个节点中使用zmq_bind()，在另一个节点中使用zmq_connect()。一般来说，执行zmq_bind()的节点是一个“服务器”，位于一个已知的网络地址上，执行zmq_connect()的节点是一个“客户机”，具有未知或任意的网络地址。因此，我们说“将socket 绑定到端点”和“将socket 连接到端点”，端点就是那个已知的网络地址。

ZeroMQ连接与传统TCP连接有些不同。主要的显著差异是:

- They go across an arbitrary transport (`inproc`, `ipc`, `tcp`, `pgm`, or `epgm`). See `zmq_inproc()`, `zmq_ipc()`, `zmq_tcp()`, `zmq_pgm()`, and `zmq_epgm()`.

- 一个将socket可能有许多传出和传入连接。.

- 没有' zmq_accept '()方法。当socket绑定到端点时，它将自动开始接受连接

- 网络连接本身发生在后台，如果网络连接中断，ZeroMQ将自动重新连接(例如，如果peer 消失，然后返回)。

- 您的应用程序代码不能直接使用这些连接;它们被封装在socket下面。

许多架构遵循某种客户机/服务器模型，其中服务器是最静态的组件，而客户机是最动态的组件，即，他们来了又走的最多。有时存在寻址问题:服务器对客户机可见，但是反过来不一定是这样的。因此，很明显，哪个节点应该执行zmq_bind()(服务器)，而哪个节点应该执行zmq_connect()(客户机)。它还取决于您使用的socket的类型，对于不常见的网络体系结构有一些例外。稍后我们将研究socket类型。

现在，假设在启动服务器之前先启动客户机。在传统的网络中，我们会看到一个大大的红色失败标志。但是ZeroMQ让我们任意地开始和停止。只要客户机节点执行zmq_connect()，连接就存在，该节点就可以开始向socket写入消息。在某个阶段(希望是在消息排队太多而开始被丢弃或客户机阻塞之前)，服务器会启动，执行zmq_bind()，然后ZeroMQ开始传递消息。
一个服务器节点可以绑定到许多端点(即协议和地址的组合)，并且它可以使用一个socket来实现这一点。这意味着它将接受跨不同传输的连接：
```
zmq_bind (socket, "tcp://*:5555");
zmq_bind (socket, "tcp://*:9999");
zmq_bind (socket, "inproc://somename");
```
对于大多数传输，不能像UDP那样两次绑定到同一个端点。然而，ipc传输允许一个进程绑定到第一个进程已经使用的端点。这意味着允许进程在崩溃后恢复。
虽然ZeroMQ试图对哪边绑定和哪边连接保持中立，但还是有区别的。稍后我们将更详细地看到这些。其结果是，您通常应该将“服务器”视为拓扑的静态部分，它绑定到或多或少固定的端点，而将“客户机”视为动态部分，它们来来去去并连接到这些端点。然后，围绕这个模型设计应用程序。它“正常工作”的可能性要大得多。

Sockets 有多个类型。Socket类型定义Sockets的语义、Socket向内和向外路由消息的策略、队列等。您可以将某些类型的Socket连接在一起，例如，publisher Socket和subscriber Socket。Socket在“messaging patterns”中协同工作。稍后我们将更详细地讨论这个问题。
正是能够以这些不同的方式连接Sockets，使ZeroMQ具备了作为消息队列系统的基本功能。在此之上还有一些层，比如代理，我们稍后将讨论它。但从本质上讲，使用ZeroMQ，您可以像孩子的积木玩具一样将各个部分拼接在一起，从而定义您的网络体系结构。



## 发送和接收消息
要发送和接收消息，可以使用zmq_msg_send()和zmq_msg_recv()方法。这些名称都是传统的，但是ZeroMQ的I/O模型与传统的TCP模型有很大的不同，您需要时间来理解它。
### 图 9 - TCP sockets are 1 to 1

![fig9.png](https://github.com/imatix/zguide/raw/master/images/fig9.png)

让我们来看看TCP sockets和ZeroMQ sockets在处理数据方面的主要区别:

- ZeroMQ套接字像UDP一样携带消息，而不像TCP那样携带字节流。ZeroMQ消息是长度指定的二进制数据。我们很快就会讲到信息;它们的设计是针对性能进行优化的，因此有点棘手。
- ZeroMQ套接字在后台线程中执行I/O。这意味着消息到达本地输入队列并从本地输出队列发送，无论您的应用程序在忙什么。
- 根据socket类型，ZeroMQ sockets具有内置的1对n路由行为。

zmq_send()方法实际上并不将消息发送到socket connection(s)。它对消息进行排队，以便I/O线程可以异步发送消息。它不会阻塞，除非在某些异常情况下。因此，当zmq_send()返回到应用程序时，不一定要发送消息。



## 单播传输（Unicast Transports）

ZeroMQ提供了一组单播传输(inproc、ipc和tcp)和多播传输(epgm、pgm)。多播是一种先进的技术，我们稍后会讲到。不要开始使用它，除非你知道你的扇出比将使1到n单播不可能（Don't even start using it unless you know that your fan-out ratios will make 1-to-N unicast impossible.）。

对于大多数常见的情况，使用tcp，这是一个断开连接式（disconnected ）的tcp传输。它是弹性的，便携式的，和足够快的大多数情况下。我们将此称为断开连接式（disconnected ），因为ZeroMQ的tcp传输不需要在连接到端点之前存在端点。客户机和服务器可以随时连接和绑定，可以来回切换，并且对应用程序保持透明。

进程间ipc传输也是断开连接式（disconnected ）的，就像tcp一样。它有一个限制:它还不能在Windows上运行。按照惯例，我们使用带有“.ipc”扩展名，以避免与其他文件名的潜在冲突。在UNIX系统上，如果使用ipc端点，则需要使用适当的权限创建这些端点，否则在不同用户id下运行的进程之间可能无法共享这些端点。您还必须确保所有进程都可以访问这些文件，例如，在相同的工作目录中运行。

线程间传输(inproc)是一种连接（connected ）的信号传输。它比tcp或ipc快得多。与tcp和ipc相比，这种传输有一个特定的限制:**服务器必须在任何客户机发出连接之前发出绑定**。这是ZeroMQ的未来版本可能会解决的问题，但目前这定义了如何使用inproc套接字。我们创建并绑定一个socket，并启动子线程，子线程创建并连接其他socket。



## ZeroMQ 不是中立的载体（Neutral Carrier）

ZeroMQ新手常问的一个问题(我自己也问过这个问题)是:“如何用ZeroMQ编写XYZ服务器?”
例如，“如何用ZeroMQ编写HTTP服务器?”这意味着，如果我们使用普通sockets 来承载HTTP请求和响应，我们应该能够使用ZeroMQsockets 来做同样的事情，只是更快更好。

答案曾经是“事情不是这样的”。ZeroMQ并不是一个中立的载体:它在使用的传输协议上强加了一个框架。这种帧与现有协议不兼容，现有协议倾向于使用自己的帧。例如，比较TCP/IP上的HTTP请求和ZeroMQ请求。

**图 10 - HTTP on the Wire**

![fig10.png](https://github.com/imatix/zguide/raw/master/images/fig10.png)

HTTP请求使用CR-LF作为最简单的帧分隔符，而ZeroMQ使用指定长度的帧。因此，您可以使用ZeroMQ编写类似http的协议，例如使用request-reply socket模式。但它不是HTTP。

**图 11 - ZeroMQ on the Wire**

![fig11.png](https://github.com/imatix/zguide/raw/master/images/fig11.png)

但是，从v3.3开始，ZeroMQ就有一个名为ZMQ_ROUTER_RAW的套接字选项，允许您在不使用ZeroMQ帧的情况下读写数据。您可以使用它来读写正确的HTTP请求和响应。Hardeep Singh对此做出了贡献，这样他就可以从ZeroMQ应用程序连接到Telnet服务器。在编写本文时，这还处于试验阶段，但它显示了ZeroMQ如何不断发展以解决新问题。也许下一个补丁就是你的了。



## I/O Threads

我们说过ZeroMQ在后台线程中执行I/O。一个I/O线程(适用于所有类型socket)对于除最极端的应用程序之外的所有应用程序都是足够的。当您创建一个新的context时，它从一个I/O线程开始。一般的经验法则是，每秒允许1千兆字节（gigabyte ，1GB?）的数据进出一个I/O线程。要增加I/O线程的数量，请在创建任何socket之前使用zmq_ctx_set()调用:
```
int io_threads = 4;
void *context = zmq_ctx_new ();
zmq_ctx_set (context, ZMQ_IO_THREADS, io_threads);
assert (zmq_ctx_get (context, ZMQ_IO_THREADS) == io_threads);
```
我们已经看到一个socket可以同时处理几十个、甚至数千个连接。这对如何编写应用程序具有根本性的影响。传统的网络应用程序每个远程连接有一个进程或一个线程，该进程或线程处理一个scoket。ZeroMQ允许您将整个结构折叠成一个进程，然后根据需要将其拆分以实现可伸缩性

如果您只将ZeroMQ用于线程间通信(即，一个没有外部scoket  I/O的多线程应用程序)您可以将I/O线程设置为零。这不是一个重要的优化，更多的是一个好奇心。



## 消息传递模式（Messaging Patterns）

在ZeroMQ socket API的牛皮纸包装下，隐藏着消息传递模式的世界。如果您有企业消息传递方面的背景知识，或者熟悉UDP，那么您对这些可能会有些熟悉。但对ZeroMQ的大多数新来者来说，它们是一个惊喜。我们非常习惯TCP范例，其中socket 一对一地映射到另一个节点。

让我们简要回顾一下ZeroMQ为您做了什么。它将数据块(消息)快速有效地交付给节点。您可以将节点映射到线程、进程或节点。ZeroMQ为您的应用程序提供了一个可以使用的socket API，而不管实际的传输是什么(比如进程内、进程间、TCP或多播)。它会在同行来来去去时自动重新连接到他们。它根据需要在发送方和接收方对消息进行排队。它限制这些队列，以防止进程耗尽内存。它处理socket 错误。它在后台线程中执行所有I/O操作。它使用无锁技术在节点之间进行通信，因此从不存在锁、等待、信号量或死锁。

但是，它会根据称为模式的精确配方路由和排队消息。正是这些模式提供了ZeroMQ的智能。它们浓缩了我们来之不易的经验，即最好的数据和工作分发方式。ZeroMQ的模式是硬编码的，但是未来的版本可能允许用户定义模式。

ZeroMQ模式由具有匹配类型的 sockets 对实现。换句话说，要理解ZeroMQ模式，您需要了解sockets 类型及其协同工作的方式。大多数情况下，这只需要学习;在这个层面上，没有什么是显而易见的。
内置的核心ZeroMQ模式是:

- **Request-reply**： 它将一组客户机连接到一组服务。这是一个远程过程调用和任务分发模式。

- **Pub-sub**：它将一组发布者连接到一组订阅者。这是一个数据发布模式。

- **Pipeline**：它以扇出/扇入模式连接节点，该模式可以有多个步骤和循环。
这是一个并行的任务分发和收集模式

- **Exclusive pair**：只连接两个sockets 。这是一个用于连接进程中的两个线程的模式，不要与“普通”sockets 对混淆。
我们在第1章-基础知识中讨论了前三种模式，我们将在本章后面看到the exclusive pair 模式。zmq_socket()手册页对模式非常清楚——值得反复阅读几遍，直到开始理解为止。这些socket组合对连接绑定对是有效的(任何一方都可以绑定):
- PUB and SUB
- REQ and REP
- REQ and ROUTER (注意，REQ插入了一个额外的空帧)
- DEALER and REP (注意，REP插入了一个额外的空帧)
- DEALER and ROUTER
- DEALER and DEALER
- ROUTER and ROUTER
- PUSH and PULL
- PAIR and PAIR

您还将看到对XPUB和XSUB sockets的引用，我们稍后将对此进行讨论(它们类似于PUB和SUB的原始版本)。任何其他组合都将产生未文档化和不可靠的结果，如果您尝试ZeroMQ的未来版本，可能会返回错误。当然，您可以并且将通过代码桥接其他socket类型，即，从一个socket类型读取并写入到另一个socket类型。



## High-Level Messaging Patterns
这四个核心模式被煮成ZeroMQ。它们是ZeroMQ API的一部分，在核心c++库中实现，并保证可以在所有优秀的零售商店中使用。
除此之外，我们还添加了高级消息传递模式。我们在ZeroMQ的基础上构建这些高级模式，并在应用程序中使用的任何语言中实现它们。它们不是核心库的一部分，不附带ZeroMQ包，并且作为ZeroMQ社区的一部分存在于它们自己的空间中。例如，我们在可靠的请求-应答模式中探索的Majordomo模式位于ZeroMQ组织中的GitHub Majordomo项目中。
在本书中，我们的目标之一是为您提供一组这样的高级模式，包括小型模式(如何明智地处理消息)和大型模式(如何构建可靠的发布子体系结构)。



## 处理消息（Working with Messages）

libzmq核心库实际上有两个api来发送和接收消息。我们已经看到和使用的zmq_send()和zmq_recv()方法都是简单的一行程序。我们将经常使用这些方法，但是zmq_recv()不擅长处理任意消息大小:它将消息截断为您提供的任何缓冲区大小。所以有第二个API与zmq_msg_t结构一起工作，它有一个更丰富但更困难的API:

- Initialise a message: `zmq_msg_init()`, `zmq_msg_init_size()`, `zmq_msg_init_data()`.
- Sending and receiving a message: `zmq_msg_send()`, `zmq_msg_recv()`.
- Release a message: `zmq_msg_close()`.
- 访问 message content: `zmq_msg_data()`, `zmq_msg_size()`, `zmq_msg_more()`.
- 处理消息属性 message properties: `zmq_msg_get()`, `zmq_msg_set()`.
- Message manipulation: `zmq_msg_copy()`, `zmq_msg_move()`.

在网络上，ZeroMQ消息是从零开始的任何大小的块，大小都适合存储在内存中。您可以使用协议缓冲区、msgpack、JSON或应用程序需要使用的任何其他东西来进行自己的序列化。选择一个可移植的数据表示形式是明智的，但是您可以自己做出关于权衡的决定。

在内存中，ZeroMQ消息是zmq_msg_t结构(或类，取决于您的语言)。下面是在C语言中使用ZeroMQ消息的基本规则:

- 创建并传递zmq_msg_t对象，而不是数据块。
- 要读取消息，可以使用zmq_msg_init()创建一个空消息，然后将其传递给zmq_msg_recv()。
- 要从新数据中编写一条消息，可以使用zmq_msg_init_size()创建一条消息，同时分配某个大小的数据块。然后使用memcpy填充数据，并将消息传递给zmq_msg_send()。
- 要释放(而不是销毁)消息，可以调用zmq_msg_close()。这将删除引用，最终ZeroMQ将销毁消息。
- 要访问消息内容，可以使用zmq_msg_data()。要知道消息包含多少数据，可以使用zmq_msg_size()。
- 不要使用zmq_msg_move()、zmq_msg_copy()或zmq_msg_init_data()，除非您阅读了手册页并确切地知道为什么需要这些。
- 您传递一个消息后zmq_msg_send(),ØMQ将clear 这个消息。i.e.，将大小设置为零。您不能两次发送相同的消息，并且不能在发送消息后访问消息数据。
- 如果您使用zmq_send()和zmq_recv()，而不是消息结构，这些规则将不适用。

如果您希望多次发送相同的消息，并且消息大小相当，那么创建第二个消息，使用zmq_msg_init()初始化它，然后使用zmq_msg_copy()创建第一个消息的副本。这不是复制数据，而是复制引用。然后可以发送消息两次(如果创建了更多副本，则可以发送两次或多次)，并且只有在发送或关闭最后一个副本时才最终销毁消息。
ZeroMQ还支持多部分消息，它允许您以单个在线消息的形式发送或接收帧列表。这在实际应用程序中得到了广泛的应用，我们将在本章后面和高级请求-应答模式中对此进行研究。帧(在ZeroMQ参考手册页面中也称为“消息部件”)是ZeroMQ消息的基本有线格式。帧是指定长度的数据块。长度可以是0以上。如果您已经做过TCP编程，您就会明白为什么帧是“我现在应该读取多少关于这个网络socket的数据”这个问题的有用答案。

有一个称为ZMTP的线级协议，它定义了ZeroMQ如何在TCP连接上读写帧。如果您对它的工作原理感兴趣，那么这个规范非常简短。
最初，ZeroMQ消息是一个帧，就像UDP一样。稍后，我们使用多部分消息对此进行了扩展，这些消息非常简单，就是一系列帧，其中“more”位设置为1，然后是一个位设置为0的帧。然后ZeroMQ API允许您编写带有“more”标志的消息，当您读取消息时，它允许您检查是否有“more”。
因此，在底层ZeroMQ API和参考手册中，消息和帧之间存在一些模糊。所以这里有一个有用的词汇:

- 消息可以是一个或多个部分。
- 这些部分也被称为“框架”。
- 每个部分都是zmq_msg_t对象。
- 您在底层API中分别发送和接收每个部分。
- 高级api提供包装器来发送整个多部分消息。

还有一些关于信息值得了解的事情:

- 您可以发送零长度的消息，例如，从一个线程发送信号到另一个线程。
- ZeroMQ保证要不提供消息的所有部分(一个或多个)，要不一个也不提供。
- ZeroMQ不会立即发送消息(单个或多个部分)，而是在稍后某个不确定的时间。因此，多部分消息必须适合于内存。
- 消息(单个或多个部分)必须装入内存。如果您想发送任意大小的文件，您应该将它们分成几部分，并将每一部分作为单独的单部分消息发送。使用多部分数据不会减少内存消耗。
- 必须在接收到消息后调用zmq_msg_close()，使用的语言在范围关闭时不会自动销毁对象。发送消息后不会调用此方法。

重复一下，不要使用zmq_msg_init_data()。这是一种零拷贝的方法，肯定会给您带来麻烦。在您开始担心减少微秒之前，还有许多更重要的事情需要了解ZeroMQ。
使用这个丰富的API可能会很累。这些方法是针对性能而不是简单性进行优化的。如果你开始使用这些，你几乎肯定会弄错，直到你仔细阅读手册页。因此，一个好的语言绑定的主要工作之一就是将这个API封装在更容易使用的类中。



## 处理多个Sockets(Handling Multiple Sockets)
在到目前为止的所有例子中，大多数例子的主循环是:

- 1.等待套接字上的消息。
- 2.过程信息。
- 3.重复。

如果我们想同时读取多个端点呢?最简单的方法是将一个socket连接到所有端点，并让ZeroMQ为我们执行扇入。如果远程端点使用相同的模式，这是合法的，但是将PULL socket连接到PUB端点将是错误的。
要同时读取多个sockets，可以使用zmq_poll()。更好的方法可能是将zmq_poll()封装在一个框架中，该框架将其转换为一个不错的事件驱动的反应器，但是它的工作量比我们在这里要介绍的多得多。
让我们从一个脏的hack开始，部分原因是为了好玩，但主要是因为它让我向您展示如何进行非阻塞socket 读取。下面是一个使用非阻塞读取从两个sockets读取的简单示例。这个相当混乱的程序既是天气更新的订阅者，又是并行任务的工作人员:

[msreader: Multiple socket reader in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:msreader) | [C#](http://zguide.zeromq.org/cs:msreader) | [Clojure](http://zguide.zeromq.org/clj:msreader) | [CL](http://zguide.zeromq.org/lisp:msreader) | [Delphi](http://zguide.zeromq.org/dpr:msreader) | [Erlang](http://zguide.zeromq.org/es:msreader) | [F#](http://zguide.zeromq.org/fsx:msreader) | [Felix](http://zguide.zeromq.org/flx:msreader) | [Go](http://zguide.zeromq.org/go:msreader) | [Java](http://zguide.zeromq.org/java:msreader) | [Lua](http://zguide.zeromq.org/lua:msreader) | [Objective-C](http://zguide.zeromq.org/m:msreader) | [Perl](http://zguide.zeromq.org/pl:msreader) | [PHP](http://zguide.zeromq.org/php:msreader) | [Python](http://zguide.zeromq.org/py:msreader) | [Ruby](http://zguide.zeromq.org/rb:msreader) | [Scala](http://zguide.zeromq.org/scala:msreader) | [Tcl](http://zguide.zeromq.org/tcl:msreader) | [Ada | Basic | Haskell | Haxe | Node.js | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)

这种方法的代价是对第一个消息(循环末尾的休眠，当没有等待消息要处理时)增加一些延迟。在亚毫秒级延迟非常重要的应用程序中，这将是一个问题。此外，您还需要检查nanosleep()或其他函数的文档，以确保它不繁忙循环。
您可以通过先读取一个套接字，然后读取第二个套接字来公平地对待套接字，而不是像我们在本例中所做的那样对它们进行优先级排序。
现在让我们看看同样毫无意义的小应用程序，使用zmq_poll():
[mspoller: Multiple socket poller in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:mspoller) | [C#](http://zguide.zeromq.org/cs:mspoller) | [Clojure](http://zguide.zeromq.org/clj:mspoller) | [CL](http://zguide.zeromq.org/lisp:mspoller) | [Delphi](http://zguide.zeromq.org/dpr:mspoller) | [Erlang](http://zguide.zeromq.org/es:mspoller) | [F#](http://zguide.zeromq.org/fsx:mspoller) | [Felix](http://zguide.zeromq.org/flx:mspoller) | [Go](http://zguide.zeromq.org/go:mspoller) | [Haskell](http://zguide.zeromq.org/hs:mspoller) | [Java](http://zguide.zeromq.org/java:mspoller) | [Lua](http://zguide.zeromq.org/lua:mspoller) | [Node.js](http://zguide.zeromq.org/js:mspoller) | [Objective-C](http://zguide.zeromq.org/m:mspoller) | [Perl](http://zguide.zeromq.org/pl:mspoller)| [PHP](http://zguide.zeromq.org/php:mspoller) | [Python](http://zguide.zeromq.org/py:mspoller) | [Ruby](http://zguide.zeromq.org/rb:mspoller) | [Scala](http://zguide.zeromq.org/scala:mspoller) | [Tcl](http://zguide.zeromq.org/tcl:mspoller) | [Ada | Basic | Haxe | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)
The items structure has these four members:
```
typedef struct {
    void *socket;       //  ZeroMQ socket to poll on
    int fd;             //  OR, native file handle to poll on
    short events;       //  Events to poll on
    short revents;      //  Events returned after poll
} zmq_pollitem_t;
```
## 多部分消息(Multipart Messages)
ZeroMQ让我们用几个帧组成一个消息，给我们一个“多部分消息”。现实的应用程序大量使用多部分消息，既用于包装带有地址信息的消息，也用于简单的序列化。稍后我们将查看回复信封。

我们现在要学习的只是如何盲目而安全地读写任何应用程序(例如代理)中的多部分消息，这些应用程序需要在不检查消息的情况下转发消息。

当你处理多部分消息时，每个部分都是zmq_msg项。例如，如果要发送包含五个部分的消息，必须构造、发送和销毁五个zmq_msg项。您可以预先执行此操作(并将zmq_msg项存储在数组或其他结构中)，或者在发送它们时逐个执行。

这是我们如何发送帧在一个多部分的消息(我们接收每帧到一个消息对象):
```
zmq_msg_send (&message, socket, ZMQ_SNDMORE);
…
zmq_msg_send (&message, socket, ZMQ_SNDMORE);
…
zmq_msg_send (&message, socket, 0);
```
下面是我们如何接收和处理一个消息中的所有部分，无论是单个部分还是多个部分:
```
while (1) {
    zmq_msg_t message;
    zmq_msg_init (&message);
    zmq_msg_recv (&message, socket, 0);
    //  Process the message frame
    …
    zmq_msg_close (&message);
    if (!zmq_msg_more (&message))
        break;      //  Last message frame
}
```

关于多部分消息需要知道的一些事情:

- 当您发送一个多部分消息时，第一部分(以及所有后续部分)只有在您发送最后一部分时才实际通过网络发送。
- 如果您正在使用zmq_poll()，当您接收到消息的第一部分时，其他部分也都已经到达。
- 您将接收到消息的所有部分，或者完全不接收。
- 消息的每个部分都是一个单独的zmq_msg项。
- 无论是否选中more属性，都将接收消息的所有部分。
- 发送时，ZeroMQ将消息帧在内存中排队，直到最后一个消息帧被接收，然后将它们全部发送出去。
- 除了关闭套接字外，无法取消部分发送的消息。



## 中介和代理 Intermediaries and Proxies
ZeroMQ的目标是分散智能，但这并不意味着你的网络是中间的空白空间。它充满了消息感知的基础设施，通常，我们使用ZeroMQ构建该基础设施。ZeroMQ管道可以从很小的管道到成熟的面向服务的brokers。消息传递行业将此称为中介，即中间的内容处理任何一方。在ZeroMQ中，我们根据context调用这些代理、队列、转发器、设备或brokers。

这种模式在现实世界中极为常见，这也是为什么我们的社会和经济中充斥着中介机构，它们除了降低大型网络的复杂性和规模成本外，没有其他实际功能。真实的中介通常称为批发商、分销商、经理等等。



## 动态发现问题 The Dynamic Discovery Problem

在设计大型分布式架构时，您将遇到的问题之一是发现。也就是说，各个部分是如何相互了解的?这是特别困难的，如果部分来了又走了，所以我们称之为“动态发现问题”。

动态发现有几种解决方案。最简单的方法是通过硬编码(或配置)网络体系结构来完全避免这种情况，以便手工完成发现。也就是说，当您添加一个新片段时，您将重新配置网络以了解它。

**图 12 - 小规模的发布-订阅网络（Small-Scale Pub-Sub Network）**

![fig12.png](https://github.com/imatix/zguide/raw/master/images/fig12.png)

在实践中，这将导致越来越脆弱和笨拙的体系结构。假设有一个发布者和100个订阅者。通过在每个订阅服务器中配置一个发布服务器端点，可以将每个订阅服务器连接到发布服务器。这很简单。用户是动态的;发布者是静态的。现在假设您添加了更多的发布者。突然间，它不再那么容易了。如果您继续将每个订阅者连接到每个发布者，那么避免动态发现的成本就会越来越高。

**Figure 13 -使用代理的发布-订阅网络 Pub-Sub Network with a Proxy**

![fig13.png](https://github.com/imatix/zguide/raw/master/images/fig13.png)

对此有很多答案，但最简单的答案是添加中介;也就是说，网络中所有其他节点都连接到的一个静态点。在传统的消息传递中，这是消息代理的工作。ZeroMQ没有提供这样的消息代理，但是它让我们可以很容易地构建中介。

您可能想知道，如果所有网络最终都变得足够大，需要中介体，那么为什么不为所有应用程序设置一个message broker呢?对于初学者来说，这是一个公平的妥协。只要始终使用星型拓扑结构，忘记性能，事情就会正常工作。然而，消息代理是贪婪的;作为中央中介人，它们变得太复杂、太有状态，最终成为一个问题。

最好将中介看作简单的无状态消息交换机。一个很好的类比是HTTP代理;它在那里，但没有任何特殊的作用。在我们的示例中，添加一个 pub-sub代理解决了动态发现问题。我们在网络的“中间”设置代理。代理打开一个XSUB套接字、一个XPUB套接字，并将每个套接字绑定到已知的IP地址和端口。然后，所有其他进程都连接到代理，而不是彼此连接。添加更多订阅者或发布者变得很简单。

**Figure 14 - Extended Pub-Sub**

![fig14.png](https://github.com/imatix/zguide/raw/master/images/fig14.png)

我们需要XPUB和XSUB套接字，因为ZeroMQ从订阅者到发布者执行订阅转发。XSUB和XPUB与SUB和PUB完全一样，只是它们将订阅公开为特殊消息。代理必须通过从XPUB套接字读取这些订阅消息并将其写入XSUB套接字，从而将这些订阅消息从订阅方转发到发布方。这是XSUB和XPUB的主要用例。



## 共享队列Shared Queue (DEALER and ROUTER sockets)

在Hello World客户机/服务器应用程序中，我们有一个客户机与一个服务通信。然而，在实际情况中，我们通常需要允许多个服务和多个客户机。这让我们可以扩展服务的功能(许多线程、进程或节点，而不是一个)。唯一的限制是服务必须是无状态的，所有状态都在请求中，或者在一些共享存储(如数据库)中。

**Figure 15 -请求分发 Request Distribution**

![fig15.png](https://github.com/imatix/zguide/raw/master/images/fig15.png)

有两种方法可以将多个客户机连接到多个服务器。蛮力方法是将每个客户端套接字连接到多个服务端点。一个客户端套接字可以连接到多个服务套接字，然后REQ套接字将在这些服务之间分发请求。假设您将一个客户端套接字连接到三个服务端点;客户机请求R1、R2、R3、R4。R1和R4是服务A的，R2是服务B的，R3是服务C的。

这种设计可以让您更便宜地添加更多的客户端。您还可以添加更多的服务。每个客户端将其请求分发给服务。但是每个客户机都必须知道服务拓扑。如果您有100个客户机，然后决定再添加3个服务，那么您需要重新配置并重新启动100个客户机，以便客户机了解这3个新服务。

这显然不是我们想在凌晨3点做的事情，因为我们的超级计算集群已经耗尽了资源，我们迫切需要添加几百个新的服务节点。太多的静态部分就像液体混凝土:知识是分散的，你拥有的静态部分越多，改变拓扑结构的努力就越大。我们想要的是位于客户机和服务之间的东西，它集中了拓扑的所有知识。理想情况下，我们应该能够在任何时候添加和删除服务或客户机，而不需要触及拓扑的任何其他部分。

因此，我们将编写一个小消息队列代理来提供这种灵活性。代理绑定到两个端点，一个用于客户机的前端，一个用于服务的后端。然后，它使用zmq_poll()监视这两个sockets 的活动，当它有一些活动时，它在它的两个sockets 之间传递消息。它实际上并不明确地管理任何队列—zeromq在每个sockets 上自动管理队列。

当您使用REQ与REP对话时，您将得到一个严格同步的请求-应答对话框。客户端发送一个请求。服务读取请求并发送响应。然后客户端读取应答。如果客户机或服务尝试执行其他操作(例如，在不等待响应的情况下连续发送两个请求)，它们将得到一个错误。

但是我们的代理必须是非阻塞的。显然，我们可以使用zmq_poll()来等待两个socket上的活动，但是不能使用REP和REQ。

**Figure 16 - Extended Request-Reply**

![fig16.png](https://github.com/imatix/zguide/raw/master/images/fig16.png)

幸运的是，有两个名为DEALER和ROUTER的socket允许您执行非阻塞的请求-响应。在高级请求-应答模式中，您将看到商人和路由器套接字如何让您构建各种异步请求-应答流。现在，我们只需要看看DEALER 和ROUTER 如何让我们扩展REQ-REP跨一个中介，也就是我们的小broker。
在这个简单的扩展请求-应答模式中，REQ与ROUTER 对话，而DEALER 与REP对话。在DEALER 与ROUTER 之间，我们必须有代码(就像我们的broker一样)将消息从一个socket 中提取出来，并将它们推送到另一个socket 中。
request-reply broker绑定到两个端点，一个用于clients 连接(前端socket)，另一个用于workers 连接(后端)。要测试此broker，您需要更改workers ，以便他们连接到后端socket。这是一个client ，我的意思是:

[rrclient: Request-reply client in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:rrclient) | [C#](http://zguide.zeromq.org/cs:rrclient) | [Clojure](http://zguide.zeromq.org/clj:rrclient) | [CL](http://zguide.zeromq.org/lisp:rrclient) | [Delphi](http://zguide.zeromq.org/dpr:rrclient) | [Erlang](http://zguide.zeromq.org/es:rrclient) | [F#](http://zguide.zeromq.org/fsx:rrclient) | [Go](http://zguide.zeromq.org/go:rrclient) | [Haskell](http://zguide.zeromq.org/hs:rrclient) | [Haxe](http://zguide.zeromq.org/hx:rrclient) | [Java](http://zguide.zeromq.org/java:rrclient) | [Lua](http://zguide.zeromq.org/lua:rrclient) | [Node.js](http://zguide.zeromq.org/js:rrclient) | [Perl](http://zguide.zeromq.org/pl:rrclient) | [PHP](http://zguide.zeromq.org/php:rrclient) | [Python](http://zguide.zeromq.org/py:rrclient) | [Racket](http://zguide.zeromq.org/rkt:rrclient) | [Ruby](http://zguide.zeromq.org/rb:rrclient) | [Scala](http://zguide.zeromq.org/scala:rrclient) | [Tcl](http://zguide.zeromq.org/tcl:rrclient) | [Ada | Basic | Felix | Objective-C | ooc | Q](http://zguide.zeromq.org/main:translate)

Here is the worker:
[rrworker: Request-reply worker in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:rrworker) | [C#](http://zguide.zeromq.org/cs:rrworker) | [Clojure](http://zguide.zeromq.org/clj:rrworker) | [CL](http://zguide.zeromq.org/lisp:rrworker) | [Delphi](http://zguide.zeromq.org/dpr:rrworker) | [Erlang](http://zguide.zeromq.org/es:rrworker) | [F#](http://zguide.zeromq.org/fsx:rrworker) | [Go](http://zguide.zeromq.org/go:rrworker) | [Haskell](http://zguide.zeromq.org/hs:rrworker) | [Haxe](http://zguide.zeromq.org/hx:rrworker) | [Java](http://zguide.zeromq.org/java:rrworker) | [Lua](http://zguide.zeromq.org/lua:rrworker) | [Node.js](http://zguide.zeromq.org/js:rrworker) | [Perl](http://zguide.zeromq.org/pl:rrworker) | [PHP](http://zguide.zeromq.org/php:rrworker) | [Python](http://zguide.zeromq.org/py:rrworker) | [Racket](http://zguide.zeromq.org/rkt:rrworker) | [Ruby](http://zguide.zeromq.org/rb:rrworker) | [Scala](http://zguide.zeromq.org/scala:rrworker) | [Tcl](http://zguide.zeromq.org/tcl:rrworker) | [Ada | Basic | Felix | Objective-C | ooc | Q](http://zguide.zeromq.org/main:translate)

这是代理，它可以正确地处理多部分消息:
[rrbroker: Request-reply broker in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:rrbroker) | [C#](http://zguide.zeromq.org/cs:rrbroker) | [Clojure](http://zguide.zeromq.org/clj:rrbroker) | [CL](http://zguide.zeromq.org/lisp:rrbroker) | [Delphi](http://zguide.zeromq.org/dpr:rrbroker) | [Erlang](http://zguide.zeromq.org/es:rrbroker) | [F#](http://zguide.zeromq.org/fsx:rrbroker) | [Go](http://zguide.zeromq.org/go:rrbroker) | [Haskell](http://zguide.zeromq.org/hs:rrbroker) | [Haxe](http://zguide.zeromq.org/hx:rrbroker) | [Java](http://zguide.zeromq.org/java:rrbroker) | [Lua](http://zguide.zeromq.org/lua:rrbroker) | [Node.js](http://zguide.zeromq.org/js:rrbroker) | [Perl](http://zguide.zeromq.org/pl:rrbroker) | [PHP](http://zguide.zeromq.org/php:rrbroker) | [Python](http://zguide.zeromq.org/py:rrbroker) | [Ruby](http://zguide.zeromq.org/rb:rrbroker) | [Scala](http://zguide.zeromq.org/scala:rrbroker) | [Tcl](http://zguide.zeromq.org/tcl:rrbroker) | [Ada | Basic | Felix | Objective-C | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)

### 图 17 - Request-Reply Broker
![fig17.png](https://github.com/imatix/zguide/raw/master/images/fig17.png)

使用请求-应答代理可以使客户机/服务器体系结构更容易伸缩，因为客户机看不到worker，而worker也看不到客户机。唯一的静态节点是中间的代理。



## ZeroMQ的内置代理函数 ZeroMQ's Built-In Proxy Function

原来，上一节的rrbroker中的核心循环非常有用，并且可以重用。它让我们可以毫不费力地构建pub-sub转发器和共享队列以及其他小型中介。ZeroMQ将其封装在一个方法中，`zmq_proxy()`:
```
zmq_proxy (frontend, backend, capture);
```

zmq_proxy (frontend, backend, capture);

必须正确地连接、绑定和配置这两个(或者三个sockets，如果我们想捕获数据的话)。当我们调用zmq_proxy方法时，就像启动rrbroker的主循环一样。让我们重写 request-reply broker来调用zmq_proxy，并将其重新标记为一个听起来很昂贵的“消息队列”(人们已经为执行更少的代码向house收费):

[msgqueue: Message queue broker in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:msgqueue) | [C#](http://zguide.zeromq.org/cs:msgqueue) | [Clojure](http://zguide.zeromq.org/clj:msgqueue) | [CL](http://zguide.zeromq.org/lisp:msgqueue) | [Delphi](http://zguide.zeromq.org/dpr:msgqueue) | [Erlang](http://zguide.zeromq.org/es:msgqueue) | [F#](http://zguide.zeromq.org/fsx:msgqueue) | [Go](http://zguide.zeromq.org/go:msgqueue) | [Haskell](http://zguide.zeromq.org/hs:msgqueue) | [Haxe](http://zguide.zeromq.org/hx:msgqueue) | [Java](http://zguide.zeromq.org/java:msgqueue) | [Lua](http://zguide.zeromq.org/lua:msgqueue) | [Node.js](http://zguide.zeromq.org/js:msgqueue) | [Perl](http://zguide.zeromq.org/pl:msgqueue) | [PHP](http://zguide.zeromq.org/php:msgqueue) | [Python](http://zguide.zeromq.org/py:msgqueue) | [Q](http://zguide.zeromq.org/q:msgqueue) | [Ruby](http://zguide.zeromq.org/rb:msgqueue) | [Tcl](http://zguide.zeromq.org/tcl:msgqueue) | [Ada | Basic | Felix | Objective-C | ooc | Racket | Scala](http://zguide.zeromq.org/main:translate)

如果您和大多数ZeroMQ用户一样，在这个阶段，您的思想开始思考，“如果我将随机的套接字类型插入代理，我能做什么坏事?”简单的回答是:试一试，看看发生了什么。实际上，您通常会坚持使用 ROUTER/DEALER、XSUB/XPUB或PULL/PUSH。


## 传输桥接 Transport Bridging

ZeroMQ用户经常会问，“我如何将我的ZeroMQ网络与技术X连接起来?”其中X是其他网络或消息传递技术。

**图 18 - Pub-Sub Forwarder Proxy**

![fig18.png](https://github.com/imatix/zguide/raw/master/images/fig18.png)

答案很简单，就是建一座桥。桥接是一个小应用程序，它在一个socket上讲一个协议，并在另一个套接字上转换成 to/from第二个协议。协议解释器，如果你喜欢的话。ZeroMQ中常见的桥接问题是桥接两个传输或网络。
例如，我们将编写一个小代理，它位于发布者和一组订阅者之间，连接两个网络。前端socket (SUB)面向气象服务器所在的内部网络，后端(PUB)面向外部网络上的订阅者。它订阅前端socket 上的天气服务，并在后端socket 上重新发布数据。

[wuproxy: Weather update proxy in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:wuproxy) | [C#](http://zguide.zeromq.org/cs:wuproxy) | [Clojure](http://zguide.zeromq.org/clj:wuproxy) | [CL](http://zguide.zeromq.org/lisp:wuproxy) | [Delphi](http://zguide.zeromq.org/dpr:wuproxy) | [Erlang](http://zguide.zeromq.org/es:wuproxy) | [F#](http://zguide.zeromq.org/fsx:wuproxy) | [Go](http://zguide.zeromq.org/go:wuproxy) | [Haskell](http://zguide.zeromq.org/hs:wuproxy) | [Haxe](http://zguide.zeromq.org/hx:wuproxy) | [Java](http://zguide.zeromq.org/java:wuproxy) | [Lua](http://zguide.zeromq.org/lua:wuproxy) | [Node.js](http://zguide.zeromq.org/js:wuproxy) | [Perl](http://zguide.zeromq.org/pl:wuproxy) | [PHP](http://zguide.zeromq.org/php:wuproxy) | [Python](http://zguide.zeromq.org/py:wuproxy) | [Ruby](http://zguide.zeromq.org/rb:wuproxy) | [Scala](http://zguide.zeromq.org/scala:wuproxy) | [Tcl](http://zguide.zeromq.org/tcl:wuproxy) | [Ada | Basic | Felix | Objective-C | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)

它看起来与前面的代理示例非常相似，但关键部分是前端和后端sockets 位于两个不同的网络上。例如，我们可以使用这个模型将组播网络(pgm传输)连接到tcp publisher。

## 处理错误和黑屏？ Handling Errors and ETERM

ZeroMQ的错误处理哲学是快速故障和恢复能力的结合。我们认为，流程应该尽可能容易受到内部错误的攻击，并且尽可能健壮地抵御外部攻击和错误。打个比方，如果一个活细胞检测到一个内部错误，它就会自我毁灭，但它也会尽一切可能抵抗来自外部的攻击。

断言充斥着ZeroMQ代码，对于健壮的代码是绝对重要的;它们只需要在细胞壁的右边。应该有这样一堵墙。如果不清楚故障是内部的还是外部的，那就是需要修复的设计缺陷。在C/ c++中，断言一旦出现错误就立即停止应用程序。在其他语言中，可能会出现异常或暂停。

当ZeroMQ检测到外部故障时，它会向调用代码返回一个错误。在一些罕见的情况下，如果没有明显的策略来从错误中恢复，它会无声地删除消息。

到目前为止，我们看到的大多数C示例中都没有错误处理。真正的代码应该对每个ZeroMQ调用执行错误处理。如果您使用的是C之外的语言绑定，那么绑定可能会为您处理错误。在C语言中，你需要自己做这个。有一些简单的规则，从POSIX约定开始:

- 如果创建对象的方法失败，则返回NULL。
- 处理数据的方法可能返回已处理的字节数，或在出现错误或故障时返回-1。
- 其他方法在成功时返回0，在错误或失败时返回-1。
- 错误代码在errno或zmq_errno()中提供。
- zmq_strerror()提供了用于日志记录的描述性错误文本。

For example:
```
void *context = zmq_ctx_new ();
assert (context);
void *socket = zmq_socket (context, ZMQ_REP);
assert (socket);
int rc = zmq_bind (socket, "tcp://*:5555");
if (rc == -1) {
    printf ("E: bind failed: %s\n", strerror (errno));
    return -1;
}
```
有两个主要的例外情况，你应该作为非致命的处理:
- 当您的代码接收到带有ZMQ_DONTWAIT选项的消息并且没有等待的数据时，ZeroMQ将返回-1并再次将errno设置为EAGAIN。
- 当一个线程调用zmq_ctx_destroy()，而其他线程仍在执行阻塞工作时，zmq_ctx_destroy()调用关闭上下文，所有阻塞调用都以-1退出，errno设置为ETERM。

在C/ c++中，断言可以在经过优化的代码中完全删除，所以不要错误地将整个ZeroMQ调用封装在assert()中。它看起来整洁;然后优化器删除所有您想要执行的断言和调用，您的应用程序就会以令人印象深刻的方式崩溃。

**图 19 - 带终止信号的并行管道Parallel Pipeline with Kill Signaling**

![fig19.png](https://github.com/imatix/zguide/raw/master/images/fig19.png)

让我们看看如何干净利落地关闭进程。我们将使用上一节中的并行管道示例。如果我们在后台启动了大量的worker，那么现在我们想在批处理完成时杀死它们。让我们通过发送一个kill消息给工人来实现这一点。最好的地方是sink，因为它知道批处理什么时候完成。
我们怎样把水槽和工人连接起来?推/拉插座是单向的。我们可以切换到另一种套接字类型，或者混合多个套接字流。让我们试试后者:使用发布-订阅模型向工人发送kill消息:

- sink 在新端点上创建一个PUB socket 。
- Workers 将他们的输入socket 连接到这个端点。
- 当sink 检测到批处理的结束时，它向其PUB socket 发送一个kill。
- 当Workers 检测到此终止消息时，它将退出。

sink不需要太多的新代码:
```
void *controller = zmq_socket (context, ZMQ_PUB);
zmq_bind (controller, "tcp://*:5559");
…
//  Send kill signal to workers
s_send (controller, "KILL");
```
这是worker 进程，它使用我们前面看到的zmq_poll()技术管理两个sockets (一个获取任务的PULL socket和一个获取控制命令的SUB socket):

[taskwork2: Parallel task worker with kill signaling in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:taskwork2) | [C#](http://zguide.zeromq.org/cs:taskwork2) | [Clojure](http://zguide.zeromq.org/clj:taskwork2) | [CL](http://zguide.zeromq.org/lisp:taskwork2) | [Delphi](http://zguide.zeromq.org/dpr:taskwork2) | [Erlang](http://zguide.zeromq.org/es:taskwork2) | [F#](http://zguide.zeromq.org/fsx:taskwork2) | [Go](http://zguide.zeromq.org/go:taskwork2) | [Haskell](http://zguide.zeromq.org/hs:taskwork2) | [Haxe](http://zguide.zeromq.org/hx:taskwork2) | [Java](http://zguide.zeromq.org/java:taskwork2) | [Lua](http://zguide.zeromq.org/lua:taskwork2) | [Node.js](http://zguide.zeromq.org/js:taskwork2) | [Objective-C](http://zguide.zeromq.org/m:taskwork2) | [Perl](http://zguide.zeromq.org/pl:taskwork2)| [PHP](http://zguide.zeromq.org/php:taskwork2) | [Python](http://zguide.zeromq.org/py:taskwork2) | [Ruby](http://zguide.zeromq.org/rb:taskwork2) | [Scala](http://zguide.zeromq.org/scala:taskwork2) | [Tcl](http://zguide.zeromq.org/tcl:taskwork2) | [Ada | Basic | Felix | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)
下面是修改后的sink应用程序。当它收集完结果后，它会向所有workers发送一条“杀死”消息:
[tasksink2: Parallel task sink with kill signaling in C](javascript:;)

[C++](http://zguide.zeromq.org/cpp:tasksink2) | [C#](http://zguide.zeromq.org/cs:tasksink2) | [Clojure](http://zguide.zeromq.org/clj:tasksink2) | [CL](http://zguide.zeromq.org/lisp:tasksink2) | [Delphi](http://zguide.zeromq.org/dpr:tasksink2) | [Erlang](http://zguide.zeromq.org/es:tasksink2) | [F#](http://zguide.zeromq.org/fsx:tasksink2) | [Go](http://zguide.zeromq.org/go:tasksink2) | [Haskell](http://zguide.zeromq.org/hs:tasksink2) | [Haxe](http://zguide.zeromq.org/hx:tasksink2) | [Java](http://zguide.zeromq.org/java:tasksink2) | [Lua](http://zguide.zeromq.org/lua:tasksink2) | [Node.js](http://zguide.zeromq.org/js:tasksink2) | [Objective-C](http://zguide.zeromq.org/m:tasksink2) | [Perl](http://zguide.zeromq.org/pl:tasksink2)| [PHP](http://zguide.zeromq.org/php:tasksink2) | [Python](http://zguide.zeromq.org/py:tasksink2) | [Ruby](http://zguide.zeromq.org/rb:tasksink2) | [Scala](http://zguide.zeromq.org/scala:tasksink2) | [Tcl](http://zguide.zeromq.org/tcl:tasksink2) | [Ada | Basic | Felix | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)

## 处理中断信号 Handling Interrupt Signals

当使用Ctrl-C或其他信号(如SIGTERM)中断时，实际应用程序需要干净地关闭。
默认情况下，这些操作只会杀死进程，这意味着不会刷新消息，不会干净地关闭文件，等等。

下面是我们如何处理不同语言的信号:

[interrupt: Handling Ctrl-C cleanly in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:interrupt) | [C#](http://zguide.zeromq.org/cs:interrupt) | [Delphi](http://zguide.zeromq.org/dpr:interrupt) | [Erlang](http://zguide.zeromq.org/es:interrupt) | [Go](http://zguide.zeromq.org/go:interrupt) | [Haskell](http://zguide.zeromq.org/hs:interrupt) | [Haxe](http://zguide.zeromq.org/hx:interrupt) | [Java](http://zguide.zeromq.org/java:interrupt) | [Lua](http://zguide.zeromq.org/lua:interrupt) | [Node.js](http://zguide.zeromq.org/js:interrupt) | [Perl](http://zguide.zeromq.org/pl:interrupt) | [PHP](http://zguide.zeromq.org/php:interrupt) | [Python](http://zguide.zeromq.org/py:interrupt) | [Ruby](http://zguide.zeromq.org/rb:interrupt) | [Scala](http://zguide.zeromq.org/scala:interrupt) | [Ada | Basic | Clojure | CL | F# | Felix | Objective-C | ooc | Q | Racket | Tcl](http://zguide.zeromq.org/main:translate)

该程序提供s_catch_signals()，它捕获Ctrl-C (SIGINT)和SIGTERM。当其中一个信号到达时，s_catch_signals()处理程序设置全局变量s_interrupted。由于您的信号处理程序，您的应用程序不会自动死亡。相反，你有机会收拾干净，优雅地离开。现在您必须显式地检查中断并正确地处理它。通过在主代码的开头调用s_catch_signals()(从interrupt.c复制)来实现这一点。这将设置信号处理。中断将影响ZeroMQ调用如下:
- 如果您的代码在阻塞调用(发送消息、接收消息或轮询)中阻塞，那么当信号到达时，调用将返回EINTR。
- 如果s_recv()之类的包装器被中断，则返回NULL。
因此，检查EINTR返回代码:NULL返回 and/or  s_interrupted。
下面是一个典型的代码片段:

```
s_catch_signals ();
client = zmq_socket (...);
while (!s_interrupted) {
    char *message = s_recv (client);
    if (!message)
        break;          //  Ctrl-C used
}
zmq_close (client);
```

如果您调用s_catch_signals()而不测试中断，那么您的应用程序将对Ctrl-C和SIGTERM免疫，这可能有用，但通常不是。


## 检测内存泄漏 Detecting Memory Leaks

任何长时间运行的应用程序都必须正确地管理内存，否则最终会耗尽所有可用内存并崩溃。如果您使用的语言可以自动处理这一问题，那么恭喜您。如果您使用C或c++或任何其他负责内存管理的语言编写程序，这里有一个关于使用valgrind的简短教程，其中包括报告程序中出现的任何泄漏。

- To install valgrind, e.g., on Ubuntu or Debian, issue this command:

```
sudo apt-get install valgrind
```

- By default, ZeroMQ will cause valgrind to complain a lot. To remove these warnings, create a file called `vg.supp` that contains this:

```
{
   <socketcall_sendto>
   Memcheck:Param
   socketcall.sendto(msg)
   fun:send
   ...
}
{
   <socketcall_sendto>
   Memcheck:Param
   socketcall.send(msg)
   fun:send
   ...
}
```

- Fix your applications to exit cleanly after Ctrl-C. For any application that exits by itself, that's not needed, but for long-running applications, this is essential, otherwise valgrind will complain about all currently allocated memory.

- Build your application with `-DDEBUG` if it's not your default setting. That ensures valgrind can tell you exactly where memory is being leaked.

- Finally, run valgrind thus:

```
valgrind --tool=memcheck --leak-check=full --suppressions=vg.supp someprog
```

And after fixing any errors it reported, you should get the pleasant message:

```
==30536== ERROR SUMMARY: 0 errors from 0 contexts...
```



## 多线程与ZeroMQMultithreading with ZeroMQ

ZeroMQ可能是有史以来编写多线程(MT)应用程序的最好方法。而ZeroMQ sockets 需要一些调整，如果你习惯了传统sockets ，ZeroMQ多线程将采取你所知道的写MT应用程序的一切，把它扔到一个堆在花园里，浇上汽油，并点燃它。这是一本难得的值得一读的书，但是大多数关于并发编程的书都值得一读。

为了使MT程序完全完美(我的意思是字面意思)，我们不需要互斥锁、锁或任何其他形式的线程间通信，除了通过ZeroMQ sockets 发送的消息。

所谓“完美的MT程序”，我的意思是代码易于编写和理解，在任何编程语言和任何操作系统中都可以使用相同的设计方法，并且可以跨任意数量的cpu伸缩，没有等待状态，没有收益递减点。

如果您花了多年的时间学习一些技巧，使MT代码能够正常工作，更不用说快速地使用锁、信号量和关键部分，那么当您意识到这一切都是徒劳时，您会感到厌恶。如果说我们从30多年的并发编程中学到了什么，那就是:不要共享状态。就像两个醉汉想要分享一杯啤酒。他们是不是好朋友并不重要。他们迟早会打起来的。你加的酒越多，他们就越会为了啤酒而打架。大多数MT应用程序看起来都像醉酒的酒吧斗殴。

在编写经典的共享状态MT代码时，如果不能将这些奇怪的问题直接转化为压力和风险，那就太可笑了，因为在压力下似乎可以工作的代码会突然失效。一家在bug代码方面拥有世界一流经验的大型公司发布了它的“多线程代码中的11个可能问题”列表，其中包括被遗忘的同步、不正确的粒度、读写撕裂、无锁重排序、锁保护、两步舞和优先级反转。

我们数了7道题，不是11道。但这不是重点。问题是，您真的希望运行电网或股票市场的代码在繁忙的周四下午3点开始得到两步锁定护送吗?谁在乎这些术语的实际含义呢?这并不是让我们转向编程的原因，而是用更复杂的黑客攻击来对抗更复杂的副作用。

尽管一些被广泛使用的模型是整个行业的基础，但它们从根本上是被破坏的，共享状态并发就是其中之一。想要无限扩展的代码就像互联网一样，发送消息，除了对坏掉的编程模型的普遍蔑视之外，什么也不分享。
你应该遵循一些规则来编写快乐的多线程代码与ZeroMQ:

- 在线程中单独隔离数据，永远不要在多个线程中共享数据。唯一的例外是ZeroMQ上下文，它是线程安全的。
- 远离经典的并发机制，如互斥、临界区、信号量等。这些是ZeroMQ应用程序中的反模式。
- 在进程开始时创建一个ZeroMQ上下文，并将其传递给希望通过inproc套接字连接的所有线程。
- 使用附加线程在应用程序中创建结构，并使用inproc上的PAIR sockets将这些线程连接到它们的父线程。模式是:绑定父socket，然后创建连接其socket的子线程。
- 使用分离的线程模拟独立的任务，并使用它们自己的contexts。通过tcp连接这些。稍后，您可以将它们转移到独立进程，而不需要显著更改代码。
- 线程之间的所有交互都以ZeroMQ消息的形式发生，您可以或多或少地正式定义它。
- 不要在线程之间共享ZeroMQ socket。ZeroMQ socket不是线程安全的。从技术上讲，可以将socket从一个线程迁移到另一个线程，但这需要技巧。在线程之间共享socket的惟一合理的地方是语言绑定，它需要像socket上的垃圾收集那样做。

例如，如果需要在应用程序中启动多个代理，则希望在它们各自的线程中运行每个代理。在一个线程中创建代理前端和后端socket，然后将socket传递给另一个线程中的代理，这很容易出错。这可能在一开始看起来有效，但在实际使用中会随机失败。记住:除非在创建socket的线程中，否则不要使用或关闭socket。

如果遵循这些规则，就可以很容易地构建优雅的多线程应用程序，然后根据需要将线程拆分为单独的进程。应用程序逻辑可以位于线程、进程或节点中:无论您的规模需要什么。
ZeroMQ使用本机OS线程，而不是虚拟的“绿色”线程。其优点是您不需要学习任何新的线程API，而且ZeroMQ线程可以干净地映射到您的操作系统。您可以使用诸如Intel的ThreadChecker之类的标准工具来查看您的应用程序在做什么。缺点是本地线程api并不总是可移植的，而且如果您有大量的线程(数千个)，一些操作系统将会受到压力。

让我们看看这在实践中是如何工作的。我们将把原来的Hello World服务器变成更强大的服务器。原始服务器在一个线程中运行。如果每个请求的工作很低,很好:一个ØMQ线程CPU核心可以全速运行,没有等待,做了很多的工作。但是，实际的服务器必须对每个请求执行重要的工作。当10,000个客户机同时攻击服务器时，单个内核可能还不够。因此，一个实际的服务器将启动多个工作线程。然后，它以最快的速度接受请求，并将这些请求分发给它的工作线程。工作线程在工作中不断地工作，并最终将它们的响应发送回去。

当然，您可以使用代理代理和外部工作进程来完成所有这些操作，但是启动一个占用16个内核的进程通常比启动16个进程(每个进程占用一个内核)更容易。此外，将worker作为线程运行将减少网络跳、延迟和网络流量。Hello World服务的MT版本基本上将代理和worker分解为一个进程:

[mtserver: Multithreaded service in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:mtserver) | [C#](http://zguide.zeromq.org/cs:mtserver) | [Clojure](http://zguide.zeromq.org/clj:mtserver) | [CL](http://zguide.zeromq.org/lisp:mtserver) | [Delphi](http://zguide.zeromq.org/dpr:mtserver) | [Erlang](http://zguide.zeromq.org/es:mtserver) | [F#](http://zguide.zeromq.org/fsx:mtserver) | [Go](http://zguide.zeromq.org/go:mtserver) | [Haskell](http://zguide.zeromq.org/hs:mtserver) | [Haxe](http://zguide.zeromq.org/hx:mtserver) | [Java](http://zguide.zeromq.org/java:mtserver) | [Lua](http://zguide.zeromq.org/lua:mtserver) | [Perl](http://zguide.zeromq.org/pl:mtserver) | [PHP](http://zguide.zeromq.org/php:mtserver) | [Python](http://zguide.zeromq.org/py:mtserver) | [Q](http://zguide.zeromq.org/q:mtserver) | [Ruby](http://zguide.zeromq.org/rb:mtserver) | [Scala](http://zguide.zeromq.org/scala:mtserver) | [Ada | Basic | Felix | Node.js | Objective-C | ooc | Racket | Tcl](http://zguide.zeromq.org/main:translate)

![fig20.png](https://github.com/imatix/zguide/raw/master/images/fig20.png)

到目前为止，所有代码都应该是你能看懂的。它是如何工作的:

- 服务器启动一组工作线程。每个工作线程创建一个REP socket，然后在这个socket上处理请求。工作线程就像单线程服务器。唯一的区别是传输(inproc而不是tcp)和绑定连接方向。
- 服务器创建一个 ROUTER socket来与clients 通信，并将其绑定到外部接口(通过tcp)。
- 服务器创建一个 DEALER socket来与工人对话，并将其绑定到其内部接口(通过inproc)。
- 服务器启动连接两个sockets的代理。代理从所有客户端公平地提取传入请求，并将这些请求分发给工作人员。它还将回复路由回它们的原点。

注意，在大多数编程语言中，创建线程是不可移植的。POSIX库是pthreads，但是在Windows上必须使用不同的API。在我们的示例中，pthread_create调用启动一个运行我们定义的worker_routine函数的新线程。我们将在 Advanced Request-Reply Patterns中看到如何将其封装到可移植API中。

这里的“工作”只是一秒钟的停顿。我们可以在worker中做任何事情，包括与其他节点通信。
这就是MT服务器在ØMQsockets 和节点方面的样子请注意 request-reply 链是如何表示为REQ-ROUTER-queue-DEALER-REP。



## 线程之间的通信Signaling Between Threads (PAIR Sockets)

当您开始使用ZeroMQ创建多线程应用程序时，您将遇到如何协调线程的问题。尽管您可能想要插入“sleep”语句，或者使用多线程技术(如信号量或互斥锁)，但是您应该使用的惟一机制是ZeroMQ消息。记住酒鬼和啤酒瓶的故事。

让我们创建三个线程，当它们准备好时互相发出信号。在这个例子中，我们在inproc传输上使用 PAIR sockets:

[mtrelay: Multithreaded relay in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:mtrelay) | [C#](http://zguide.zeromq.org/cs:mtrelay) | [Clojure](http://zguide.zeromq.org/clj:mtrelay) | [CL](http://zguide.zeromq.org/lisp:mtrelay) | [Delphi](http://zguide.zeromq.org/dpr:mtrelay) | [Erlang](http://zguide.zeromq.org/es:mtrelay) | [F#](http://zguide.zeromq.org/fsx:mtrelay) | [Go](http://zguide.zeromq.org/go:mtrelay) | [Haskell](http://zguide.zeromq.org/hs:mtrelay) | [Haxe](http://zguide.zeromq.org/hx:mtrelay) | [Java](http://zguide.zeromq.org/java:mtrelay) | [Lua](http://zguide.zeromq.org/lua:mtrelay) | [Perl](http://zguide.zeromq.org/pl:mtrelay) | [PHP](http://zguide.zeromq.org/php:mtrelay) | [Python](http://zguide.zeromq.org/py:mtrelay) | [Q](http://zguide.zeromq.org/q:mtrelay) | [Ruby](http://zguide.zeromq.org/rb:mtrelay) | [Scala](http://zguide.zeromq.org/scala:mtrelay) | [Ada | Basic | Felix | Node.js | Objective-C | ooc | Racket | Tcl](http://zguide.zeromq.org/main:translate)
Figure 21 - 接力赛The Relay Race
![fig21.png](https://github.com/imatix/zguide/raw/master/images/fig21.png)

这是一个经典的模式多线程与ZeroMQ:
- 1.两个线程使用共享context,通过inproc进行通信。
- 2.父线程创建一个socket，将其绑定到inproc://端点，然后启动子线程，将context传递给它。
子线程创建第二个socket，将其连接到inproc://端点，然后向父线程发出准备就绪的信号。

注意，使用此模式的多线程代码不能扩展到进程。如果您使用inproc和 socket pairs，那么您正在构建一个紧密绑定的应用程序，即，其中线程在结构上相互依赖。当低延迟非常重要时，执行此操作。另一种设计模式是松散绑定的应用程序，其中线程有自己的context ，并通过ipc或tcp进行通信。您可以轻松地将松散绑定的线程拆分为单独的进程。
这是我们第一次展示使用 PAIR sockets的示例。为什么使用PAIR?其他socket 组合似乎也有效果，但它们都有副作用，可能会干扰信号:

- 您可以使用PUSH作为发送方，PULL作为接收方。这看起来很简单，也很有效，但是请记住PUSH将向所有可用的接收者分发消息。如果你不小心启动了两个接收器(例如，你已经启动了一个接收器，然后你又启动了另一个接收器)，你将“丢失”一半的信号。PAIR 具有拒绝多个连接的优势;这个PAIR 是独一无二的。
- 您可以使用DEALER作为发送方，使用 ROUTER 作为接收方。ROUTER ，然而，将你的消息包装在一个“信封”，这意味着你的零大小的信号变成一个多部分的消息。如果您不关心数据并将任何内容视为有效信号，如果您从socket中读取的次数不超过一次，那么这就无关紧要了。然而，如果你决定发送真实的数据，你会突然发现ROUTER提供给你“错误”的消息。DEALER 还分发outgoing 的消息，像PUSH一样带来相同的风险。
- 您可以将PUB用于发送方，将SUB用于接收方。这将正确地发送您的邮件，就像您发送邮件一样，PUB不会像PUSH或DEALER那样分发但是，您需要使用空订阅来配置订阅者，这很烦人。
由于这些原因，PAIR是线程对之间协调的最佳选择。

## 节点的协调Node Coordination

当您想要协调网络上的一组节点时，PAIR sockets将不再有效。这是少数几个线程和节点策略不同的领域之一。基本上，节点来来去去，而线程通常是静态的。如果远程节点离开并返回， PAIR sockets不会自动重新连接。

**Figure 22 - 发布-订阅同步Pub-Sub Synchronization**

![fig22.png](https://github.com/imatix/zguide/raw/master/images/fig22.png)

线程和节点之间的第二个显著差异是，通常有固定数量的线程，但节点的数量更可变。让我们以前面的一个场景(天气服务器和客户机)为例，使用节点协调确保订阅者在启动时不会丢失数据。

以下是应用程序的工作原理:

- 发布者预先知道它希望有多少订阅者。这是一个神奇的数字。
- 发布者启动并等待所有订阅者连接。这是节点协调部分。每个订阅者订阅，然后通过另一个socket告诉发布者它已经准备好了。
- 当发布者连接了所有订阅者后，它开始发布数据。
在本例中，我们将使用REQ-REP套接字流来同步订阅者和发布者。以下是出版商:

[syncpub: Synchronized publisher in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:syncpub) | [C#](http://zguide.zeromq.org/cs:syncpub) | [Clojure](http://zguide.zeromq.org/clj:syncpub) | [CL](http://zguide.zeromq.org/lisp:syncpub) | [Delphi](http://zguide.zeromq.org/dpr:syncpub) | [Erlang](http://zguide.zeromq.org/es:syncpub) | [F#](http://zguide.zeromq.org/fsx:syncpub) | [Go](http://zguide.zeromq.org/go:syncpub) | [Haskell](http://zguide.zeromq.org/hs:syncpub) | [Haxe](http://zguide.zeromq.org/hx:syncpub) | [Java](http://zguide.zeromq.org/java:syncpub) | [Lua](http://zguide.zeromq.org/lua:syncpub) | [Node.js](http://zguide.zeromq.org/js:syncpub) | [Perl](http://zguide.zeromq.org/pl:syncpub) | [PHP](http://zguide.zeromq.org/php:syncpub) | [Python](http://zguide.zeromq.org/py:syncpub) | [Racket](http://zguide.zeromq.org/rkt:syncpub) | [Ruby](http://zguide.zeromq.org/rb:syncpub) | [Scala](http://zguide.zeromq.org/scala:syncpub) | [Tcl](http://zguide.zeromq.org/tcl:syncpub) | [Ada | Basic | Felix | Objective-C | ooc | Q](http://zguide.zeromq.org/main:translate)

And here is the subscriber:
[syncsub: Synchronized subscriber in C](javascript:;)

[C++](http://zguide.zeromq.org/cpp:syncsub) | [C#](http://zguide.zeromq.org/cs:syncsub) | [Clojure](http://zguide.zeromq.org/clj:syncsub) | [CL](http://zguide.zeromq.org/lisp:syncsub) | [Delphi](http://zguide.zeromq.org/dpr:syncsub) | [Erlang](http://zguide.zeromq.org/es:syncsub) | [F#](http://zguide.zeromq.org/fsx:syncsub) | [Go](http://zguide.zeromq.org/go:syncsub) | [Haskell](http://zguide.zeromq.org/hs:syncsub) | [Haxe](http://zguide.zeromq.org/hx:syncsub) | [Java](http://zguide.zeromq.org/java:syncsub) | [Lua](http://zguide.zeromq.org/lua:syncsub) | [Node.js](http://zguide.zeromq.org/js:syncsub) | [Perl](http://zguide.zeromq.org/pl:syncsub) | [PHP](http://zguide.zeromq.org/php:syncsub) | [Python](http://zguide.zeromq.org/py:syncsub) | [Racket](http://zguide.zeromq.org/rkt:syncsub) | [Ruby](http://zguide.zeromq.org/rb:syncsub) | [Scala](http://zguide.zeromq.org/scala:syncsub) | [Tcl](http://zguide.zeromq.org/tcl:syncsub) | [Ada | Basic | Felix | Objective-C | ooc | Q](http://zguide.zeromq.org/main:translate)

这个Bash shell脚本将启动10个订阅者，然后是发布者:
```
echo "Starting subscribers..."
for ((a=0; a<10; a++)); do
    syncsub &
done
echo "Starting publisher..."
syncpub
```

这就得到了令人满意的结果:

```
Starting subscribers...
Starting publisher...
Received 1000000 updates
Received 1000000 updates
...
Received 1000000 updates
Received 1000000 updates
```

我们不能假设SUB connect将在REQ/REP对话框完成时完成。如果使用除inproc之外的任何传输，则不能保证出站连接将以任何顺序完成。因此，该示例在订阅和发送REQ/REP同步之间强制休眠一秒钟。

一个更健壮的模型可以是:

- Publisher打开PUB socket 并开始发送“Hello”消息(而不是数据)。
- 订阅者连接SUB socket ，当他们收到一条Hello消息时，他们通过 REQ/REP socket pair告诉发布者。
- 当发布者获得所有必要的确认后，它就开始发送实际数据。

## 零拷贝Zero-Copy

ZeroMQ的消息API允许您直接从应用程序缓冲区发送和接收消息，而不需要复制数据。
我们称之为零拷贝，它可以在某些应用程序中提高性能。

您应该考虑在以高频率发送大内存块(数千字节)的特定情况下使用zero-copy。对于短消息或较低的消息率，使用零拷贝将使您的代码更混乱、更复杂，并且没有可度量的好处。像所有优化一样，当您知道它有帮助时使用它，并在前后进行度量。

要执行zero-copy，可以使用zmq_msg_init_data()创建一条消息，该消息引用已经用malloc()或其他分配器分配的数据块，然后将其传递给zmq_msg_send()。创建消息时，还传递一个函数，ZeroMQ在发送完消息后将调用该函数释放数据块。这是最简单的例子，假设buffer是一个在堆上分配了1000字节的块:
```
void my_free (void *data, void *hint) {
    free (data);
}
//  Send message from buffer, which we allocate and ZeroMQ will free for us
zmq_msg_t message;
zmq_msg_init_data (&message, buffer, 1000, my_free, NULL);
zmq_msg_send (&message, socket, 0);
```
注意，发送消息后不调用zmq_msg_close()—libzmq在实际发送消息后将自动调用zmq_msg_close()。

没有办法在接收时执行零复制:ZeroMQ提供了一个缓冲区，您可以存储任意长的缓冲区，但是它不会直接将数据写入应用程序缓冲区。

在编写时，ZeroMQ的多部分消息与zero-copy很好地结合在一起。在传统的消息传递中，需要将不同的缓冲区组合到一个可以发送的缓冲区中。这意味着复制数据。使用ZeroMQ，您可以将来自不同来源的多个缓冲区作为单独的消息帧发送。将每个字段作为长度分隔的帧发送。对于应用程序，它看起来像一系列发送和接收调用。但是在内部，多个部分被写到网络中，并通过单个系统调用进行读取，因此非常高效。


## 发布-订阅消息信封Pub-Sub Message Envelopes

在 pub-sub 模式中，我们可以将密钥拆分为一个单独的消息框架，称为信封。如果你想使用pub-sub信封，那就自己做吧。它是可选的，在之前的 pub-sub例子中我们没有这样做。
对于简单的情况，使用 pub-sub信封要多做一些工作，但是对于实际情况，尤其是键和数据是自然分离的情况，使用它会更简洁。

**Figure 23 - Pub-Sub Envelope with Separate Key**

![fig23.png](https://github.com/imatix/zguide/raw/master/images/fig23.png)

订阅执行前缀匹配。也就是说，它们查找“所有以XYZ开头的消息”。一个明显的问题是:如何将键与数据分隔开来，以便前缀匹配不会意外匹配数据。最好的答案是使用信封，因为匹配不会跨越框架边界。下面是一个极简示例，展示了 pub-sub信封在代码中的外观。此发布者发送两种类型的消息，A和B。

The envelope holds the message type:

[psenvpub: Pub-Sub envelope publisher in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:psenvpub) | [C#](http://zguide.zeromq.org/cs:psenvpub) | [Clojure](http://zguide.zeromq.org/clj:psenvpub) | [CL](http://zguide.zeromq.org/lisp:psenvpub) | [Delphi](http://zguide.zeromq.org/dpr:psenvpub) | [Erlang](http://zguide.zeromq.org/es:psenvpub) | [F#](http://zguide.zeromq.org/fsx:psenvpub) | [Go](http://zguide.zeromq.org/go:psenvpub) | [Haskell](http://zguide.zeromq.org/hs:psenvpub) | [Haxe](http://zguide.zeromq.org/hx:psenvpub) | [Java](http://zguide.zeromq.org/java:psenvpub) | [Lua](http://zguide.zeromq.org/lua:psenvpub) | [Node.js](http://zguide.zeromq.org/js:psenvpub) | [Perl](http://zguide.zeromq.org/pl:psenvpub) | [PHP](http://zguide.zeromq.org/php:psenvpub) | [Python](http://zguide.zeromq.org/py:psenvpub) | [Ruby](http://zguide.zeromq.org/rb:psenvpub) | [Scala](http://zguide.zeromq.org/scala:psenvpub) | [Tcl](http://zguide.zeromq.org/tcl:psenvpub) | [Ada | Basic | Felix | Objective-C | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)
The subscriber wants only messages of type B:
[psenvsub: Pub-Sub envelope subscriber in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:psenvsub) | [C#](http://zguide.zeromq.org/cs:psenvsub) | [Clojure](http://zguide.zeromq.org/clj:psenvsub) | [CL](http://zguide.zeromq.org/lisp:psenvsub) | [Delphi](http://zguide.zeromq.org/dpr:psenvsub) | [Erlang](http://zguide.zeromq.org/es:psenvsub) | [F#](http://zguide.zeromq.org/fsx:psenvsub) | [Go](http://zguide.zeromq.org/go:psenvsub) | [Haskell](http://zguide.zeromq.org/hs:psenvsub) | [Haxe](http://zguide.zeromq.org/hx:psenvsub) | [Java](http://zguide.zeromq.org/java:psenvsub) | [Lua](http://zguide.zeromq.org/lua:psenvsub) | [Node.js](http://zguide.zeromq.org/js:psenvsub) | [Perl](http://zguide.zeromq.org/pl:psenvsub) | [PHP](http://zguide.zeromq.org/php:psenvsub) | [Python](http://zguide.zeromq.org/py:psenvsub) | [Ruby](http://zguide.zeromq.org/rb:psenvsub) | [Scala](http://zguide.zeromq.org/scala:psenvsub) | [Tcl](http://zguide.zeromq.org/tcl:psenvsub) | [Ada | Basic | Felix | Objective-C | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)

When you run the two programs, the subscriber should show you this:
```
[B] We would like to see this
[B] We would like to see this
[B] We would like to see this
...
```

此示例显示订阅筛选器拒绝或接受整个多部分消息(键和数据)。您永远不会得到多部分消息的一部分。如果您订阅了多个发布者，并且希望知道它们的地址，以便能够通过另一个socket (这是一个典型的用例)向它们发送数据，那么创建一个由三部分组成的消息。

**Figure 24 - Pub-Sub Envelope with Sender Address**

![fig24.png](https://github.com/imatix/zguide/raw/master/images/fig24.png)



## High-Water Marks

当您可以快速地从一个进程发送消息到另一个进程时，您很快就会发现内存是一种宝贵的资源，并且可以被轻松地填满。流程中某些地方的几秒钟延迟可能会变成积压，导致服务器崩溃，除非您了解问题并采取预防措施。

问题是这样的:假设您有一个进程A以很高的频率向正在处理它们的进程B发送消息。突然，B变得非常繁忙(垃圾收集、CPU过载等等)，短时间内无法处理消息。对于一些繁重的垃圾收集，可能需要几秒钟的时间，或者如果有更严重的问题，可能需要更长的时间。进程A仍然试图疯狂发送的消息会发生什么情况?有些将位于B的网络缓冲区中。有些将位于以太网线路本身。有些将位于A的网络缓冲区中。其余的会在A的内存中积累，就像A后面的应用程序发送它们一样快。如果不采取一些预防措施，A很容易耗尽内存并崩溃。

这是消息代理的一个一致的经典问题。更糟糕的是，从表面上看，这是B的错，而B通常是a无法控制的用户编写的应用程序。

答案是什么?一是把问题往上游推。A从其他地方获取信息。所以告诉这个过程，“停止!”等等。这叫做流量控制。这听起来很有道理，但是如果你在Twitter上发消息呢?你会告诉全世界的人在B行动起来的时候停止发推吗?

流程控制在某些情况下有效，但在其他情况下无效。运输层不能告诉应用层“停止”，就像地铁系统不能告诉大型企业“请让您的员工再工作半个小时”一样。我太忙了”。消息传递的解决方案是设置缓冲区大小的限制，然后当达到这些限制时，采取一些明智的行动。在某些情况下(不是地铁系统)，答案是扔掉信息。在另一些国家，最好的策略是等待。

ZeroMQ使用HWM(高水位)的概念来定义其内部管道的容量。每个socket 外或socket 内的连接都有自己的管道和用于发送和/或接收的HWM，这取决于socket 类型。一些socket (PUB, PUSH)只有发送缓冲区。有些(SUB、PULL、REQ、REP)只有接收缓冲区。一些(DEALER, ROUTER, PAIR)有发送和接收缓冲区。

In ZeroMQ v2.x, HWM默认为无穷大。这很容易做到，但对于高容量publishers来说，通常也是致命的。In ZeroMQ v3.x,默认设置为1000，这样更合理。如果你还在用ZeroMQ v2.x，你应该总是在你的socket 上设置一个HWM，设置成1000或另一个考虑您的信息大小和预期的用户性能的数字来匹配ZeroMQ v3.x。

当socket 到达其HWM时，它将根据socket 类型阻塞或删除数据。如果PUB和ROUTER socket 到达它们的HWM，它们将丢弃数据，而其他 socket 类型将阻塞。在inproc传输中，发送方和接收方共享相同的缓冲区，因此实际的HWM是双方设置的HWM的和。最后，HWMs并不精确;由于libzmq实现其队列的方式，默认情况下最多可以获得1,000条消息，但实际缓冲区大小可能要小得多(只有一半)。



## 缺少消息问题的解决者（解决方式）Missing Message Problem Solver
在使用ZeroMQ构建应用程序时，您将不止一次地遇到这个问题:丢失预期接收到的消息。
我们已经整理了一个图表，介绍了造成这种情况的最常见原因。

**Figure 25 - Missing Message Problem Solver**

![fig25.png](https://github.com/imatix/zguide/raw/master/images/fig25.png)

下面是图表的摘要:

- 在 SUB sockets上，使用zmq_setsockopt()和ZMQ_SUBSCRIBE设置订阅，否则将不会收到消息。因为您通过前缀订阅消息，如果您订阅“”(空订阅)，您将获得所有内容。
- 如果您启动 SUB sockets(即在PUB socket开始发送数据之后，您将丢失连接之前发布的任何内容。如果这是一个问题，请设置您的体系结构，以便首先启动 SUB sockets，然后PUB socket开始发布。
- 即使同步了 SUB 和 PUB socket，仍然可能丢失消息。这是因为在实际创建连接之前不会创建内部队列。**如果您可以切换绑定/连接方向，以便 SUB socket 绑定，而PUB socket连接，您可能会发现它的工作方式与您所期望的一样。**
- 如果您使用REP和REQsockets，并且没有坚持同步发送/recv/send/recv命令，ZeroMQ将报告错误，您可能会忽略这些错误。然后，看起来就像是你在丢失信息。如果您使用REQ或REP，请坚持send/recv顺序，并且始终在实际代码中检查ZeroMQ调用中的错误。
- 如果使用 PUSH sockets，您会发现第一个连接的PULL socket 将获取不公平的消息共享。只有在成功连接所有PULL套接字时才会发生准确的消息轮换，这可能需要几毫秒的时间。作为PUSH / PULL的替代方案，对于较低的数据速率，请考虑使用ROUTER / DEALER和负载平衡模式。
- 如果您正在跨线程共享sockets ，请不要这样做。这将导致随机的怪异，并崩溃。
- 如果使用inproc，请确保两个socket位于相同的context中。否则，连接端实际上会失败。同样，先绑定，然后连接。inproc不像tcp那样是一个断开连接的传输。
- 如果您正在使用ROUTER sockets，通过发送不正确的身份帧(或忘记发送身份帧)，很容易意外丢失消息。通常，在 ROUTER sockets 上设置ZMQ_ROUTER_MANDATORY选项是一个好主意，但是也要在每次发送调用时检查返回代码。
- 最后，如果您真的不知道哪里出了问题，那么就创建一个最小的测试用例来重现问题，并向ZeroMQ社区寻求帮助。



# Chapter 3 -高级 Advanced Request-Reply Patterns

在第2章-Sockets and Patterns 中，我们通过开发一系列小型应用程序来学习使用ZeroMQ的基础知识，每次都要探索ZeroMQ的新方面。在本章中，我们将继续使用这种方法，探索构建在ZeroMQ核心请求-应答模式之上的高级模式。

我们将讨论:
- 请求-应答机制如何工作
- 如何组合REQ、REP、DEALER和 ROUTER sockets
- ROUTER sockets如何工作，详细的讨论
- 负载平衡模式
- 构建一个简单的负载平衡消息代理
- 为ZeroMQ设计一个高级API
- 构建异步请求-应答服务器
- 一个详细的代理间路由示例



## The Request-Reply Mechanisms（机制）

我们已经简要介绍了多部分消息。现在让我们看一个主要的用例，即回复消息信封。信封是一种用地址安全包装数据的方法，而不需要接触数据本身。通过将回复地址分离到信封中，我们可以编写通用的中介，如api和代理，无论消息有效负载或结构是什么，它们都可以创建、读取和删除地址。

在请求-应答模式中，信封包含应答的返回地址。这就是没有状态的ZeroMQ网络如何创建往返的请求-应答对话框。

当您使用REQ和REP sockets 时，您甚至看不到信封;这些sockets 自动处理它们。但是对于大多数有趣的请求-应答模式，您需要了解信封，特别是ROUTER sockets。我们会一步一步来。



## The Simple Reply Envelope

请求-应答交换由请求消息和最终的应答消息组成。在简单的请求-应答模式中，每个请求都有一个应答。在更高级的模式中，请求和响应可以异步流动。然而，回复信封总是以相同的方式工作。

ZeroMQ应答信封正式由零个或多个应答地址组成，后跟一个空帧(信封分隔符)，后跟消息体(零个或多个帧)。信封是由多个sockets 在一个链中一起工作创建的。我们来分解一下。

我们将从通过REQsocket发送“Hello”开始。REQ套接字创建了最简单的回复信封，它没有地址，只有一个空的分隔符框架和包含“Hello”字符串的消息框架。这是一个两帧的消息。

**Figure 26 - Request with Minimal Envelope**

![fig26.png](https://github.com/imatix/zguide/raw/master/images/fig26.png)

REP socket执行匹配工作:它剥离信封，直到并包括分隔符框架，保存整个信封，并将“Hello”字符串传递给应用程序。因此，我们最初的Hello World示例在内部使用了请求-回复信封，但是应用程序从未看到过它们。

如果您监视在hwclient和hwserver之间流动的网络数据，您将看到:每个请求和每个响应实际上是两个帧，一个空帧，然后是主体。这对于一个简单的REQ-REP对话框似乎没有多大意义。不过，当我们探讨ROUTER和DEALER 如何处理信封时，您将会看到原因。



## 加长回信信封The Extended Reply Envelope

现在，让我们使用中间的 ROUTER-DEALER代理扩展 REQ-REP对 ，看看这会如何影响回复信封。这是我们在Chapter 2 - Sockets and Patterns中已经看到的扩展请求-应答模式。实际上，我们可以插入任意数量的代理步骤。机制是一样的。

**Figure 27 - Extended Request-Reply Pattern**

![fig27.png](https://github.com/imatix/zguide/raw/master/images/fig27.png)

代理在伪代码中这样做:

```
prepare context, frontend and backend sockets
while true:
    poll on both sockets
    if frontend had input:
        read all frames from frontend
        send to backend
    if backend had input:
        read all frames from backend
        send to frontend
```

与其他socket不同，ROUTER socket跟踪它所拥有的每个连接，并将这些信息告诉调用者。
它告诉调用者的方法是将连接标识粘贴到接收到的每个消息前面。
标识(有时称为地址)只是一个二进制字符串，除了“这是连接的惟一句柄”之外没有任何意义。
然后，当您通过ROUTER socket发送消息时，您首先发送一个标识帧。

zmq_socket()手册页这样描述它:

> 当接收到消息时，ZMQ_ROUTER socket 在将消息传递给应用程序之前，应该在消息部分前加上一个包含消息的原始对等点标识的消息部分。接收到的消息在所有连接的对等点之间公平排队。发送消息时，ZMQ_ROUTER socket 应删除消息的第一部分，并使用它来确定消息应路由到的对等方的身份。

作为历史记录，ZeroMQ v2.2和更早的版本使用uuid作为标识。ZeroMQ v3.0和以后的版本在默认情况下生成一个5字节的标识(0 +一个随机32位整数)。这对网络性能有一定的影响，但仅当您使用多个代理跃点时，这种情况很少见。主要的更改是通过删除对UUID库的依赖来简化libzmq的构建。

身份是一个很难理解的概念，但如果你想成为ZeroMQ专家，它是必不可少的。 ROUTER socket 为它工作的每个连接创建一个随机标识。如果有三个 REQ sockets连接到ROUTER socket，它将为每个REQ sockets创建一个随机标识。
如果我们继续我们的工作示例，假设REQsocket有一个3字节的标识ABC。在内部，这意味着ROUTER socket保留一个哈希表，它可以在这个哈希表中搜索ABC并为REQsocket找到TCP连接。当我们从ROUTER socket接收消息时，我们得到三个帧。

**Figure 28 - Request with One Address**

![fig28.png](https://github.com/imatix/zguide/raw/master/images/fig28.png)

代理循环的核心是“从一个socket读取，向另一个socket写入”，因此我们将这三帧发送到ROUTER socket上。如果您现在嗅探网络流量，您将看到这三个帧从DEALER socket飞向REP socket。REP socket和前面一样，去掉整个信封，包括新的回复地址，并再次向调用者传递“Hello”。顺便提一下，REP socket一次只能处理一个请求-应答交换，这就是为什么如果您尝试读取多个请求或发送多个响应而不坚持严格的recv-send循环，它会给出一个错误。
您现在应该能够可视化返回路径。当hwserver将“World”发送回来时，REP socket将其与它保存的信封打包，并通过网络向DEALER socket发送一个三帧回复消息。

**Figure 29 - Reply with one Address**

![fig29.png](https://github.com/imatix/zguide/raw/master/images/fig29.png)

现在DEALER 读取这三帧，并通过 ROUTER socket发送所有这三帧。 ROUTER接受消息的第一帧，即ABC标识，并为此查找连接。如果它发现了，它就会把接下来的两帧泵到网络上。

**Figure 30 - Reply with Minimal Envelope**

![fig30.png](https://github.com/imatix/zguide/raw/master/images/fig30.png)

REQ socket 接收此消息，并检查第一帧是否为空分隔符，它就是空分隔符。REQ socket 丢弃了框架并将“World”传递给调用应用程序，该应用程序将它打印出来，这让第一次看到ZeroMQ的年轻一代感到惊讶。



## What's This Good For?

说实话，用于严格请求-应答或扩展请求-应答的用例在某种程度上是有限的。首先，没有简单的方法可以从常见的故障中恢复，比如由于应用程序代码错误导致服务器崩溃。我们将在可靠的请求-应答模式中看到更多这方面的内容。然而，一旦你掌握了这四个sockets 处理信封的方式，以及它们之间的通信方式，你就可以做一些非常有用的事情。我们了解了ROUTER 如何使用应答信封来决定将应答路由回哪个客户机REQ socket 。现在让我们用另一种方式来表达:
- 每次ROUTER 给你一个消息，它会告诉你来自哪个对等点，作为一个身份。
- 您可以将其与散列表一起使用(以标识为键)，以便在新对等点到达时跟踪它们。
- 如果将标识前缀作为消息的第一帧，ROUTER 将异步地将消息路由到连接到它的任何对等点。
ROUTER sockets并不关心整个信封。他们对空分隔符一无所知。他们所关心的只是一个身份框架，这个框架让他们知道要向哪个连接发送消息。



## （概述）Recap of Request-Reply Sockets

让我们来总结一下:
- REQ socket向网络发送消息数据前面的空分隔符帧。REQ socket是同步的。REQ socket总是发送一个请求，然后等待一个响应。REQ socket每次只与一个对等点通信。如果您将一个REQ socket连接到多个对等点，则请求将被分发到每个对等点，并期望每个对等点一次发送一个响应。
-  REP socket读取并保存所有标识帧，直到并包括空分隔符，然后将以下一帧或多帧传递给调用方。 REP socket是同步的，每次只与一个对等点通信。如果您将一个 REP socket连接到多个对等点，则以公平的方式从对等点读取请求，并且始终将响应发送到发出最后一个请求的同一对等点。
-  DEALER socket不理会回复信封，并像处理任何多部分消息一样处理此消息。DEALER socket是异步的，就像PUSH and PULL 的组合。它们在所有连接之间分发发送的消息，并且公平队列接收来自所有连接的消息。
-  ROUTER socket 不理会回复信封，就像DEALER一样。它为其连接创建标识，并将这些标识作为任何接收到的消息中的第一帧传递给调用者。相反，当调用者发送消息时，它使用第一个消息帧作为标识来查找要发送到的连接。 ROUTERS是异步的。



## Request-Reply Combinations(组合)

我们有四个 request-reply sockets，每个sockets具有特定的行为。我们已经看到它们如何以简单和扩展的请求-应答模式连接。但是这些sockets是您可以用来解决许多问题的构建块。
这些是合法的组合:

- REQ to REP
- DEALER to REP
- REQ to ROUTER
- DEALER to ROUTER
- DEALER to DEALER
- ROUTER to ROUTER

And these combinations are invalid (and I'll explain why):

- REQ to REQ
- REQ to DEALER
- REP to REP
- REP to ROUTER

下面是一些记忆语义的技巧。DEALER 类似于异步REQ socket，而ROUTER 类似于异步REP socket。在我们使用REQ socket的地方，我们可以使用一个DEALER ;我们只需要自己读和写信封。在使用REP socket的地方，我们可以使用ROUTER ;我们只需要自己管理身份。将REQ和DEALER  socket视为“客户端”，而REP和ROUTER socket视为“服务器”。大多数情况下，您需要绑定REP和ROUTER socket，并将REQ和DEALER socket连接到它们。它并不总是这么简单，但它是一个干净而令人难忘的起点。



## The REQ to REP Combination
我们已经介绍了一个与REP服务器对话的REQ客户机，但是让我们看一个方面:REQ客户机必须启动消息流。代表服务器不能与未首先向其发送请求的REQ客户机通信。从技术上讲，这甚至是不可能的，如果您尝试了，API还会返回一个EFSM错误。



## The DEALER to REP Combination

现在，让我们用DEALER替换REQ客户端。这为我们提供了一个异步客户机，它可以与多个 REP服务器通信。如果我们使用DEALER重写“Hello World”客户端，我们就可以发送任意数量的“Hello”请求，而不需要等待回复。

当我们使用一个DEALER 与一个REP socket通信时，我们必须准确地模拟REQ socket将发送的信封，否则REP socket将把消息作为无效丢弃。所以，为了传递信息，我们:
- 发送一个设置了更多标志的空消息帧;然后
- 发送消息体。

当我们收到信息时，我们:
- 接收第一个帧，如果它不是空的，则丢弃整个消息;
- 接收下一帧并将其传递给应用程序。



## The REQ to ROUTER Combination

就像我们可以用DEALER替换REQ一样，我们也可以用ROUTER替换REP。这为我们提供了一个异步服务器，它可以同时与多个REQ客户机通信。如果我们使用ROUTER重写“Hello World”服务器，我们将能够并行处理任意数量的“Hello”请求。我们在 Chapter 2 - Sockets and Patterns        mtserver示例中看到了这一点。我们可以用两种不同的方式使用ROUTER:
- 作为在前端和后端sockets之间切换消息的代理。
- 作为读取消息并对其进行操作的应用程序。

在第一种情况下，ROUTER 只是读取所有帧，包括人工身份帧，然后盲目地传递它们。在第二种情况下，ROUTER 必须知道它正在发送的回复信封的格式。由于另一个对等点是REQ socket，ROUTER 获得标识帧、空帧和数据帧。



The DEALER to ROUTER Combination

现在，我们可以切换REQ和REP与DEALER 和ROUTER ，以获得最强大的socket 组合，这是DEALER 与ROUTER 交谈。它为我们提供了与异步服务器通信的异步客户机，在异步服务器上，双方都完全控制消息格式。

因为DEALER 和ROUTER 都可以处理任意的消息格式，如果您希望安全地使用这些格式，您必须成为一个协议设计人员。至少您必须决定是否要模拟REQ/REP回复信封。这取决于你是否真的需要发送回复。



## The DEALER to DEALER Combination

您可以用ROUTER交换一个REP ，但也可以用一个DEALER交换一个REP ，前提是DEALER只与一个同行通信。

当您将REP 替换为DEALER时，您的worker 可以突然完全异步，发送任意数量的回复。这样做的代价是你必须自己管理回复信封，并把它们处理好，否则什么都不管用。稍后我们将看到一个工作示例。就目前而言，DEALER 对DEALER 模式是一种比较棘手的模式，值得庆幸的是，我们很少需要这种模式。



| [The ROUTER to ROUTER Combination](http://zguide.zeromq.org/page:all#The-ROUTER-to-ROUTER-Combination) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-64) [next](http://zguide.zeromq.org/page:all#header-66) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

对于N-to-N连接，这听起来很完美，但是这是最难使用的组合。在使用ZeroMQ之前，您应该避免使用它。我们将在自由模式和 Reliable Request-Reply 模式中看到一个例子，以及为分布式计算框架中的点对点工作设计的DEALER to ROUTER 的另一种替代方案（and an alternative DEALER to ROUTER design for peer-to-peer work in A Framework for Distributed Computing.）。



## Invalid Combinations

大多数情况下，试图将客户端连接到客户端或服务器连接到服务器是一个坏主意，不会奏效。不过，我不会给出笼统的模糊警告，而是会详细解释:
- REQ对REQ:双方都希望从互相发送消息开始，并且只有在您对事情进行计时以便两个对等方同时交换消息的情况下，这才能工作。一想到它就会伤到我的大脑。
- REQ to DEALER:理论上可以这样做，但是如果添加第二个REQ，就会中断，因为DEALER无法向原始对等点发送回复。因此REQ socket 会混淆，并且/或返回针对其他客户机的消息。
- REP to REP：:双方都会等待对方发出第一个信息。
- REP to ROUTER:理论上，如果 ROUTER socket知道REP socket 已经连接并且知道该连接的身份，ROUTER socket 可以启动对话框并发送正确格式的请求。这是混乱的，并没有增加超过经销商路由器（It's messy and adds nothing over DEALER to ROUTER）。

在这个有效与无效的细分中，常见的线程是ZeroMQ socket 连接总是偏向于绑定到端点的一个对等点，以及连接到端点的另一个对等点。此外，哪边绑定哪边连接并不是任意的，而是遵循自然模式。我们期望“在那里”的那一面是绑定的:它将是一个服务器、一个代理、一个发布者和一个收集器。“来了又走”的一方将clients 和workers联系起来。记住这一点将帮助您设计更好的ZeroMQ架构。


## 探索Exploring ROUTER Sockets

让我们再仔细看看ROUTER sockets。我们已经看到了它们如何通过将单个消息路由到特定的连接来工作。我将更详细地解释如何识别这些连接，以及ROUTER sockets在不能发送消息时做什么。



### Identities and Addresses

ZeroMQ中的标识概念特别指ROUTER sockets ，以及它们如何标识与其他socket的连接。
更广泛地说，身份在回复信封中用作地址。在大多数情况下，标识是任意的，并且是ROUTER sockets 的本地标识:它是哈希表中的一个查找键。独立地，对等点可以有物理地址(网络端点，如“tcp://192.168.55.117:5670”)或逻辑地址(UUID或电子邮件地址或其他惟一密钥)。

使用ROUTER sockets 与特定对等点通信的应用程序，如果已经构建了必要的散列表，则可以将逻辑地址转换为标识。因为ROUTER sockets 只在一个连接(到一个特定的对等点)发送消息时声明该连接的身份，所以您只能真正地回复一个消息，而不能自动地与一个对等点通信。

这是真的，即使你翻转规则，使ROUTER连接到对等点，而不是等待对等点连接到ROUTER。但是，您可以强制ROUTER sockets 使用逻辑地址来代替它的标识。zmq_setsockopt引用页面调用此设置socket标识。
其工作原理如下:

- 对等应用程序在绑定或连接之前设置其对等socket (DEALER 或REQ)的ZMQ_IDENTITY选项。
- 通常，对等点然后连接到已经绑定的 ROUTER socket。但是 ROUTER也可以连接到对等点。
- 在连接时，对等socket 告诉ROUTER socket，“请为这个连接使用这个标识”。
- 如果对等socket 没有这样说，ROUTER就为连接生成它通常的任意随机标识。
- ROUTER socket现在将此逻辑地址提供给应用程序，作为来自该对等点的任何消息的前缀标识帧。
- ROUTER 还期望逻辑地址作为任何传出消息的前缀标识帧。

下面是连接到ROUTER socket的两个对等点的简单例子，其中一个附加了一个逻辑地址“PEER2”:

[identity: Identity check in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:identity) | [C#](http://zguide.zeromq.org/cs:identity) | [Clojure](http://zguide.zeromq.org/clj:identity) | [CL](http://zguide.zeromq.org/lisp:identity) | [Delphi](http://zguide.zeromq.org/dpr:identity) | [Erlang](http://zguide.zeromq.org/es:identity) | [F#](http://zguide.zeromq.org/fsx:identity) | [Go](http://zguide.zeromq.org/go:identity) | [Haskell](http://zguide.zeromq.org/hs:identity) | [Haxe](http://zguide.zeromq.org/hx:identity) | [Java](http://zguide.zeromq.org/java:identity) | [Lua](http://zguide.zeromq.org/lua:identity) | [Node.js](http://zguide.zeromq.org/js:identity) | [Perl](http://zguide.zeromq.org/pl:identity) | [PHP](http://zguide.zeromq.org/php:identity) | [Python](http://zguide.zeromq.org/py:identity) | [Q](http://zguide.zeromq.org/q:identity) | [Ruby](http://zguide.zeromq.org/rb:identity) | [Scala](http://zguide.zeromq.org/scala:identity) | [Tcl](http://zguide.zeromq.org/tcl:identity) | [Ada | Basic | Felix | Objective-C | ooc | Racket](http://zguide.zeromq.org/main:translate)

Here is what the program prints:
```
----------------------------------------
[005] 006B8B4567
[000]
[039] ROUTER uses a generated 5 byte identity
----------------------------------------
[005] PEER2
[000]
[038] ROUTER uses REQ's socket identity
```



### 的错误处理 ROUTER Error Handling

ROUTER sockets确实有一种处理无法发送到任何地方的消息的方法:它们无声地丢弃这些消息。
这种态度在工作代码中是有意义的，但它使调试变得困难。“发送标识为第一帧”的方法非常棘手，以至于我们在学习时经常会出错，而ROUTER在我们出错时的死寂也不是很有建设性。

因为ZeroMQ v3.2中有一个socket选项可以设置为捕捉这个错误:**ZMQ_ROUTER_MANDATORY。**将其设置在ROUTER socket 上，然后当您在发送调用上提供不可路由的标识时，socket将发出EHOSTUNREACH错误的信号。



## 负载平衡模式The Load Balancing Pattern

现在让我们看一些代码。我们将看到如何将ROUTER socket连接到 REQ socket，然后连接到DEALER socket。这两个示例遵循相同的逻辑，即负载平衡模式。这种模式是我们第一次公开使用 ROUTER socket进行有意路由，而不是简单地充当应答通道。

负载平衡模式非常常见，我们将在本书中多次看到它。它解决了简单的循环式路由(如 PUSH和DEALER提供)的主要问题，即如果任务没有大致占用相同的时间，那么循环式路由就会变得低效。

这是邮局的比喻。如果每个柜台都有一个队列，有些人购买邮票(快速、简单的交易)，有些人开立新帐户(非常慢的交易)，那么您将发现邮票购买者被不公平地困在队列中。就像在邮局一样，如果您的消息传递体系结构是不公平的，人们会感到恼火。

邮局的解决方案是创建一个队列，这样即使一个或两个柜台工作缓慢，其他柜台将继续以先到先得的方式为客户服务。

PUSH和DEALER使用这种简单方法的一个原因是纯粹的性能。如果你到达美国任何一个主要机场，你会发现移民处排着长队。边境巡逻官员将提前派人在每个柜台排队，而不是使用单一队列。让人们提前走50码可以为每位乘客节省一到两分钟。由于每次护照检查的时间大致相同，所以或多或少是公平的。这是PUSH和DEALER的策略:提前发送工作负载，这样就有更少的旅行距离。

这是ZeroMQ反复出现的主题:世界上的问题是多种多样的，用正确的方法解决不同的问题可以让你受益。机场不是邮局，而且没有一个尺寸适合任何人，真的很好。
让我们回到一个worker (DEALER 或者 REQ)连接到一个broker (ROUTER)的场景。broker必须知道worker什么时候准备好了，并保存一个workers列表，以便每次可以使用最近最少使用的工人。

事实上，解决方案非常简单:工作人员在开始和完成每个任务后都会发送一条“ready”消息。broker逐个读取这些消息。每次读取一条消息时，它都是从最后一个使用的worker中读取的。因为我们使用的是ROUTER socket，我们得到一个标识，然后我们可以用它把一个任务发送回worker。
这是request-reply的一个曲解，因为任务与应答一起发送，任务的任何响应都作为一个新请求发送。下面的代码示例应该更清楚。



### ROUTER Broker and REQ Workers
下面是一个负载平衡模式的例子，使用ROUTER broker与一组REQ workers对话:

[rtreq: ROUTER-to-REQ in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:rtreq) | [C#](http://zguide.zeromq.org/cs:rtreq) | [Clojure](http://zguide.zeromq.org/clj:rtreq) | [CL](http://zguide.zeromq.org/lisp:rtreq) | [Delphi](http://zguide.zeromq.org/dpr:rtreq) | [Erlang](http://zguide.zeromq.org/es:rtreq) | [F#](http://zguide.zeromq.org/fsx:rtreq) | [Go](http://zguide.zeromq.org/go:rtreq) | [Haskell](http://zguide.zeromq.org/hs:rtreq) | [Haxe](http://zguide.zeromq.org/hx:rtreq) | [Java](http://zguide.zeromq.org/java:rtreq) | [Lua](http://zguide.zeromq.org/lua:rtreq) | [Node.js](http://zguide.zeromq.org/js:rtreq) | [Perl](http://zguide.zeromq.org/pl:rtreq) | [PHP](http://zguide.zeromq.org/php:rtreq) | [Python](http://zguide.zeromq.org/py:rtreq) | [Ruby](http://zguide.zeromq.org/rb:rtreq) | [Scala](http://zguide.zeromq.org/scala:rtreq) | [Tcl](http://zguide.zeromq.org/tcl:rtreq) | [Ada | Basic | Felix | Objective-C | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)

该示例运行5秒，然后每个工作人员打印他们处理了多少任务。如果路由成功了，我们希望工作得到公平分配:

```
Completed: 20 tasks
Completed: 18 tasks
Completed: 21 tasks
Completed: 23 tasks
Completed: 19 tasks
Completed: 21 tasks
Completed: 17 tasks
Completed: 17 tasks
Completed: 25 tasks
Completed: 19 tasks
```

要与本例中的工作人员对话，我们必须创建一个对 REQ-friendly的信封，它由一个标识符和一个空信封分隔符框架组成。

**Figure 31 - Routing Envelope for REQ**

![fig31.png](https://github.com/imatix/zguide/raw/master/images/fig31.png)



### ROUTER Broker and DEALER Workers

任何地方你可以使用REQ，你就可以使用DEALER。有两个具体的区别:

- REQ socket总是在任何数据帧之前发送一个空的分隔符帧;DEALER没有。
- REQ socket在收到回复之前只发送一条消息;DEALER是完全异步的。

同步和异步行为对我们的示例没有影响，因为我们正在执行严格的请求-应答。当我们处理从失败中恢复时，它更相关，我们将在 Reliable Request-Reply Patterns中讨论这个问题。现在让我们看看完全相同的例子，但与REQ socket替换为一个DEALER socket:

[rtdealer: ROUTER-to-DEALER in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:rtdealer) | [C#](http://zguide.zeromq.org/cs:rtdealer) | [Clojure](http://zguide.zeromq.org/clj:rtdealer) | [CL](http://zguide.zeromq.org/lisp:rtdealer) | [Delphi](http://zguide.zeromq.org/dpr:rtdealer) | [Erlang](http://zguide.zeromq.org/es:rtdealer) | [F#](http://zguide.zeromq.org/fsx:rtdealer) | [Go](http://zguide.zeromq.org/go:rtdealer) | [Haskell](http://zguide.zeromq.org/hs:rtdealer) | [Haxe](http://zguide.zeromq.org/hx:rtdealer) | [Java](http://zguide.zeromq.org/java:rtdealer) | [Lua](http://zguide.zeromq.org/lua:rtdealer) | [Node.js](http://zguide.zeromq.org/js:rtdealer) | [Perl](http://zguide.zeromq.org/pl:rtdealer) | [PHP](http://zguide.zeromq.org/php:rtdealer) | [Python](http://zguide.zeromq.org/py:rtdealer) | [Ruby](http://zguide.zeromq.org/rb:rtdealer) | [Scala](http://zguide.zeromq.org/scala:rtdealer) | [Tcl](http://zguide.zeromq.org/tcl:rtdealer) | [Ada | Basic | Felix | Objective-C | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)

代码几乎是相同的，除了worker使用一个DEALER socket，并读写数据帧之前的空帧。
当我想要保持与REQ worker的兼容性时，我使用这种方法。

但是，请记住空分隔符帧的原因:它允许多跳扩展请求在 REP socket中终止， REP socket使用该分隔符分隔应答信封，以便将数据帧传递给应用程序。

如果我们从来不需要将消息传递给REP socket，我们可以简单地在两边删除空分隔符框架，这使事情变得更简单。这通常是我为纯DEALER to ROUTER协议使用的设计。



## 负载平衡消息代理 A Load Balancing Message Broker

前面的示例只完成了一半。它可以用虚拟的请求和响应来管理一组工人，但是它没有办法与clients交谈。如果我们添加第二个接收clients请求的frontend ROUTER socket，并将我们的示例转换为一个可以将消息从前端切换到后端的代理，我们将得到一个有用的、可重用的小型负载平衡消息代理。

**Figure 32 - Load Balancing Broker**

![fig32.png](https://github.com/imatix/zguide/raw/master/images/fig32.png)

该代理执行以下操作:
- 接受来自一组clients的连接。
- 接受来自一组workers的连接。
- 接受来自clients的请求，并将这些请求保存在一个队列中。
- 使用负载平衡模式将这些请求发送给workers 。
- 收到workers的回复。
- 将这些响应发送回原始请求client。

理代码相当长，但值得理解:

[lbbroker: Load balancing broker in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:lbbroker) | [C#](http://zguide.zeromq.org/cs:lbbroker) | [Clojure](http://zguide.zeromq.org/clj:lbbroker) | [CL](http://zguide.zeromq.org/lisp:lbbroker) | [Delphi](http://zguide.zeromq.org/dpr:lbbroker) | [Erlang](http://zguide.zeromq.org/es:lbbroker) | [F#](http://zguide.zeromq.org/fsx:lbbroker) | [Go](http://zguide.zeromq.org/go:lbbroker) | [Haskell](http://zguide.zeromq.org/hs:lbbroker) | [Haxe](http://zguide.zeromq.org/hx:lbbroker) | [Java](http://zguide.zeromq.org/java:lbbroker) | [Lua](http://zguide.zeromq.org/lua:lbbroker) | [Node.js](http://zguide.zeromq.org/js:lbbroker) | [Perl](http://zguide.zeromq.org/pl:lbbroker) | [PHP](http://zguide.zeromq.org/php:lbbroker) | [Python](http://zguide.zeromq.org/py:lbbroker) | [Ruby](http://zguide.zeromq.org/rb:lbbroker) | [Scala](http://zguide.zeromq.org/scala:lbbroker) | [Tcl](http://zguide.zeromq.org/tcl:lbbroker) | [Ada | Basic | Felix | Objective-C | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)

这个程序最困难的部分是(a)每个socket 读取和写入的信封，以及(b)负载平衡算法。我们将依次从消息信封格式开始。

让我们遍历一个完整的请求-响应链，从client 到worker，然后返回。在这段代码中，我们设置了client 和worker sockets 的标识，以便更容易地跟踪消息帧。实际上，我们允许 ROUTER sockets为连接创建身份。假设客户机的标识是“client”，而worker的标识是“worker”。客户机应用程序发送一个包含“Hello”的帧。

**Figure 33 - Message that Client Sends**

![fig33.png](https://github.com/imatix/zguide/raw/master/images/fig33.png)

Because the REQ socket adds its empty delimiter frame and the ROUTER socket adds its connection identity, the proxy reads off the frontend ROUTER socket the client address, empty delimiter frame, and the data part.

**Figure 34 - Message Coming in on Frontend**

![fig34.png](https://github.com/imatix/zguide/raw/master/images/fig34.png)

The broker sends this to the worker, prefixed by the address of the chosen worker, plus an additional empty part to keep the REQ at the other end happy.

**Figure 35 - Message Sent to Backend**

![fig35.png](https://github.com/imatix/zguide/raw/master/images/fig35.png)

This complex envelope stack gets chewed up first by the backend ROUTER socket, which removes the first frame. Then the REQ socket in the worker removes the empty part, and provides the rest to the worker application.

**Figure 36 - Message Delivered to Worker**

![fig36.png](https://github.com/imatix/zguide/raw/master/images/fig36.png)

The worker has to save the envelope (which is all the parts up to and including the empty message frame) and then it can do what's needed with the data part. Note that a REP socket would do this automatically, but we're using the REQ-ROUTER pattern so that we can get proper load balancing.

On the return path, the messages are the same as when they come in, i.e., the backend socket gives the broker a message in five parts, and the broker sends the frontend socket a message in three parts, and the client gets a message in one part.

Now let's look at the load balancing algorithm. It requires that both clients and workers use REQ sockets, and that workers correctly store and replay the envelope on messages they get. The algorithm is:

- Create a pollset that always polls the backend, and polls the frontend only if there are one or more workers available.

- Poll for activity with infinite timeout.

- If there is activity on the backend, we either have a "ready" message or a reply for a client. In either case, we store the worker address (the first part) on our worker queue, and if the rest is a client reply, we send it back to that client via the frontend.

- If there is activity on the frontend, we take the client request, pop the next worker (which is the last used), and send the request to the backend. This means sending the worker address, empty part, and then the three parts of the client request.

You should now see that you can reuse and extend the load balancing algorithm with variations based on the information the worker provides in its initial "ready" message. For example, workers might start up and do a performance self test, then tell the broker how fast they are. The broker can then choose the fastest available worker rather than the oldest.



| [A High-Level API for ZeroMQ](http://zguide.zeromq.org/page:all#A-High-Level-API-for-ZeroMQ) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-73) [next](http://zguide.zeromq.org/page:all#header-75) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

We're going to push request-reply onto the stack and open a different area, which is the ZeroMQ API itself. There's a reason for this detour: as we write more complex examples, the low-level ZeroMQ API starts to look increasingly clumsy. Look at the core of the worker thread from our load balancing broker:

**while** (true) {
`    `*//  Get one address frame and empty delimiter*
`    `char *address = s_recv (worker);
`    `char *empty = s_recv (worker);
`    `assert (*empty == 0);
`    `free (empty);

`    `*//  Get request, send reply*
`    `char *request = s_recv (worker);
`    `printf ("Worker: %s**\n**", request);
`    `free (request);

`    `s_sendmore (worker, address);
`    `s_sendmore (worker, "");
`    `s_send`     `(worker, "OK");
`    `free (address);
}

That code isn't even reusable because it can only handle one reply address in the envelope, and it already does some wrapping around the ZeroMQ API. If we used the `libzmq` simple message API this is what we'd have to write:

**while** (true) {
`    `*//  Get one address frame and empty delimiter*
`    `char address [255];
`    `int address_size = zmq_recv (worker, address, 255, 0);
`    `**if** (address_size == -1)
`        `**break**;

`    `char empty [1];
`    `int empty_size = zmq_recv (worker, empty, 1, 0);
`    `assert (empty_size <= 0);
`    `**if** (empty_size == -1)
`        `**break**;

`    `*//  Get request, send reply*
`    `char request [256];
`    `int request_size = zmq_recv (worker, request, 255, 0);
`    `**if** (request_size == -1)
`        `**return** NULL;
`    `request [request_size] = 0;
`    `printf ("Worker: %s**\n**", request);
`    `
`    `zmq_send (worker, address, address_size, ZMQ_SNDMORE);
`    `zmq_send (worker, empty, 0, ZMQ_SNDMORE);
`    `zmq_send (worker, "OK", 2, 0);
}

And when code is too long to write quickly, it's also too long to understand. Up until now, I've stuck to the native API because, as ZeroMQ users, we need to know that intimately. But when it gets in our way, we have to treat it as a problem to solve.

We can't of course just change the ZeroMQ API, which is a documented public contract on which thousands of people agree and depend. Instead, we construct a higher-level API on top based on our experience so far, and most specifically, our experience from writing more complex request-reply patterns.

What we want is an API that lets us receive and send an entire message in one shot, including the reply envelope with any number of reply addresses. One that lets us do what we want with the absolute least lines of code.

Making a good message API is fairly difficult. We have a problem of terminology: ZeroMQ uses "message" to describe both multipart messages, and individual message frames. We have a problem of expectations: sometimes it's natural to see message content as printable string data, sometimes as binary blobs. And we have technical challenges, especially if we want to avoid copying data around too much.

The challenge of making a good API affects all languages, though my specific use case is C. Whatever language you use, think about how you could contribute to your language binding to make it as good (or better) than the C binding I'm going to describe.



| [Features of a Higher-Level API](http://zguide.zeromq.org/page:all#Features-of-a-Higher-Level-API) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-74) [next](http://zguide.zeromq.org/page:all#header-76) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

My solution is to use three fairly natural and obvious concepts: *string* (already the basis for our `s_send` and `s_recv`) helpers, *frame* (a message frame), and *message* (a list of one or more frames). Here is the worker code, rewritten onto an API using these concepts:

**while** (true) {
`    `zmsg_t *msg = zmsg_recv (worker);
`    `zframe_reset (zmsg_last (msg), "OK", 2);
`    `zmsg_send (&msg, worker);
}

Cutting the amount of code we need to read and write complex messages is great: the results are easy to read and understand. Let's continue this process for other aspects of working with ZeroMQ. Here's a wish list of things I'd like in a higher-level API, based on my experience with ZeroMQ so far:

- *Automatic handling of sockets.* I find it cumbersome to have to close sockets manually, and to have to explicitly define the linger timeout in some (but not all) cases. It'd be great to have a way to close sockets automatically when I close the context.

- *Portable thread management.* Every nontrivial ZeroMQ application uses threads, but POSIX threads aren't portable. So a decent high-level API should hide this under a portable layer.

- *Piping from parent to child threads.* It's a recurrent problem: how to signal between parent and child threads. Our API should provide a ZeroMQ message pipe (using PAIR sockets and `inproc` automatically.

- *Portable clocks.* Even getting the time to a millisecond resolution, or sleeping for some milliseconds, is not portable. Realistic ZeroMQ applications need portable clocks, so our API should provide them.

- *A reactor to replace zmq_poll().* The poll loop is simple, but clumsy. Writing a lot of these, we end up doing the same work over and over: calculating timers, and calling code when sockets are ready. A simple reactor with socket readers and timers would save a lot of repeated work.

- *Proper handling of Ctrl-C.* We already saw how to catch an interrupt. It would be useful if this happened in all applications.



| [The CZMQ High-Level API](http://zguide.zeromq.org/page:all#The-CZMQ-High-Level-API) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-75) [next](http://zguide.zeromq.org/page:all#header-77) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Turning this wish list into reality for the C language gives us [CZMQ](http://zero.mq/c), a ZeroMQ language binding for C. This high-level binding, in fact, developed out of earlier versions of the examples. It combines nicer semantics for working with ZeroMQ with some portability layers, and (importantly for C, but less for other languages) containers like hashes and lists. CZMQ also uses an elegant object model that leads to frankly lovely code.

Here is the load balancing broker rewritten to use a higher-level API (CZMQ for the C case):

[lbbroker2: Load balancing broker using high-level API in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:lbbroker2) | [Delphi](http://zguide.zeromq.org/dpr:lbbroker2) | [Haxe](http://zguide.zeromq.org/hx:lbbroker2) | [Java](http://zguide.zeromq.org/java:lbbroker2) | [Lua](http://zguide.zeromq.org/lua:lbbroker2) | [PHP](http://zguide.zeromq.org/php:lbbroker2) | [Python](http://zguide.zeromq.org/py:lbbroker2) | [Scala](http://zguide.zeromq.org/scala:lbbroker2) | [Ada | Basic | C# | Clojure | CL | Erlang | F# | Felix | Go | Haskell | Node.js | Objective-C | ooc | Perl | Q | Racket | Ruby | Tcl](http://zguide.zeromq.org/main:translate)

*//  Shows how to handle Ctrl-C*

\#include <stdlib.h>
\#include <stdio.h>
\#include <signal.h>
\#include <unistd.h>
\#include <fcntl.h>

\#include <zmq.h>

*//`  `Signal handling////`  `Create a self-pipe and call s_catch_signals(pipe's writefd) in your application//`  `at startup, and then exit your main loop if your pipe contains any data.//`  `Works especially well with zmq_poll.*

\#define S_NOTIFY_MSG " "
\#define S_ERROR_MSG "Error while writing to self-pipe.\n"
**static** int s_fd;
**static** void s_signal_handler (int signal_value)
{
`    `int rc = write (s_fd, S_NOTIFY_MSG, **sizeof**(S_NOTIFY_MSG));
`    `**if** (rc != **sizeof**(S_NOTIFY_MSG)) {
`        `write (STDOUT_FILENO, S_ERROR_MSG, **sizeof**(S_ERROR_MSG)-1);
`        `exit(1);
`    }`
}

**static** void s_catch_signals (int fd)
{
`    `s_fd = fd;

`    `**struct** sigaction action;
`    `action.sa_handler = s_signal_handler;
`    `*//  Doesn't matter if SA_RESTART set because self-pipe will wake up zmq_poll*
`    `*//  But setting to 0 will allow zmq_read to be interrupted.*
`    `action.sa_flags = 0;
`    `sigemptyset (&action.sa_mask);
`    `sigaction (SIGINT, &action, NULL);
`    `sigaction (SIGTERM, &action, NULL);
}

int main (void)
{
`    `int rc;

`    `void *context = zmq_ctx_new ();
`    `void *socket = zmq_socket (context, ZMQ_REP);
`    `zmq_bind (socket, "tcp://*:5555");

```
    `int pipefds[2];
`    `rc = pipe(pipefds);
`    `**if** (rc != 0) {
`        `perror("Creating self-pipe");
`        `exit(1);
`    }`
`    `**for** (int i = 0; i < 2; i++) {
`        `int flags = fcntl(pipefds[0], F_GETFL, 0);
`        `**if** (flags < 0) {
`            `perror ("fcntl(F_GETFL)");
`            `exit(1);
`        }`
`        `rc = fcntl (pipefds[0], F_SETFL, flags | O_NONBLOCK);
`        `**if** (rc != 0) {
`            `perror ("fcntl(F_SETFL)");
`            `exit(1);
`        }`
`    }
```

`    `s_catch_signals (pipefds[1]);

`    `zmq_pollitem_t items [] = {
`        `{ 0, pipefds[0], ZMQ_POLLIN, 0 },
`        `{ socket, 0, ZMQ_POLLIN, 0 }
`    }`;

```
    `**while** (1) {
`        `rc = zmq_poll (items, 2, -1);
`        `**if** (rc == 0) {
`            `**continue**;
`        }` **else** **if** (rc < 0) {
`            `**if** (errno == EINTR) { **continue**; }
`            `perror("zmq_poll");
`            `exit(1);
`        }
        `*// Signal pipe FD*
`        `**if** (items [0].revents & ZMQ_POLLIN) {
`            `char buffer [1];
`            `read (pipefds[0], buffer, 1);`  `*// clear notifying byte*
`            `printf ("W: interrupt received, killing server…**\n**");
`            `**break**;
`        }
```

`        `*// Read socket*
`        `**if** (items [1].revents & ZMQ_POLLIN) {
`            `char buffer [255];
`            `*// Use non-blocking so we can continue to check self-pipe via zmq_poll*
`            `rc = zmq_recv (socket, buffer, 255, ZMQ_NOBLOCK);
`            `**if** (rc < 0) {
`                `**if** (errno == EAGAIN) { **continue**; }
`                `**if** (errno == EINTR) { **continue**; }
`                `perror("recv");
`                `exit(1);
`            }`
`            `printf ("W: recv**\n**");

```
            `*// Now send message back.*
`            `*// …*
`        }`
`    }
```

`    `printf ("W: cleaning up**\n**");
`    `zmq_close (socket);
`    `zmq_ctx_destroy (context);
`    `**return** 0;
}

Or, if you're calling `zmq_poll()`, test on the return code:

**if** (zmq_poll (items, 2, 1000 * 1000) == -1)
`    `**break**;`              `*//  Interrupted*

The previous example still uses `zmq_poll()`. So how about reactors? The CZMQ `zloop`reactor is simple but functional. It lets you:

- Set a reader on any socket, i.e., code that is called whenever the socket has input.
- Cancel a reader on a socket.
- Set a timer that goes off once or multiple times at specific intervals.
- Cancel a timer.

`zloop` of course uses `zmq_poll()` internally. It rebuilds its poll set each time you add or remove readers, and it calculates the poll timeout to match the next timer. Then, it calls the reader and timer handlers for each socket and timer that need attention.

When we use a reactor pattern, our code turns inside out. The main logic looks like this:

zloop_t *reactor = zloop_new ();
zloop_reader (reactor, self->backend, s_handle_backend, self);
zloop_start (reactor);
zloop_destroy (&reactor);

The actual handling of messages sits inside dedicated functions or methods. You may not like the style—it's a matter of taste. What it does help with is mixing timers and socket activity. In the rest of this text, we'll use `zmq_poll()` in simpler cases, and `zloop` in more complex examples.

Here is the load balancing broker rewritten once again, this time to use `zloop`:

[lbbroker3: Load balancing broker using zloop in C](javascript:;)


[Haxe](http://zguide.zeromq.org/hx:lbbroker3) | [Java](http://zguide.zeromq.org/java:lbbroker3) | [Python](http://zguide.zeromq.org/py:lbbroker3) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

If you're using child threads, they won't receive the interrupt. To tell them to shutdown, you can either:

- Destroy the context, if they are sharing the same context, in which case any blocking calls they are waiting on will end with ETERM.
- Send them shutdown messages, if they are using their own contexts. For this you'll need some socket plumbing.



| [The Asynchronous Client/Server Pattern](http://zguide.zeromq.org/page:all#The-Asynchronous-Client-Server-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-76) [next](http://zguide.zeromq.org/page:all#header-78) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

In the ROUTER to DEALER example, we saw a 1-to-N use case where one server talks asynchronously to multiple workers. We can turn this upside down to get a very useful N-to-1 architecture where various clients talk to a single server, and do this asynchronously.

**Figure 37 - Asynchronous Client/Server**

![fig37.png](https://github.com/imatix/zguide/raw/master/images/fig37.png)

Here's how it works:

- Clients connect to the server and send requests.
- For each request, the server sends 0 or more replies.
- Clients can send multiple requests without waiting for a reply.
- Servers can send multiple replies without waiting for new requests.

Here's code that shows how this works:

[asyncsrv: Asynchronous client/server in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:asyncsrv) | [C#](http://zguide.zeromq.org/cs:asyncsrv) | [Clojure](http://zguide.zeromq.org/clj:asyncsrv) | [Delphi](http://zguide.zeromq.org/dpr:asyncsrv) | [Erlang](http://zguide.zeromq.org/es:asyncsrv) | [F#](http://zguide.zeromq.org/fsx:asyncsrv) | [Go](http://zguide.zeromq.org/go:asyncsrv) | [Haskell](http://zguide.zeromq.org/hs:asyncsrv) | [Haxe](http://zguide.zeromq.org/hx:asyncsrv) | [Java](http://zguide.zeromq.org/java:asyncsrv) | [Lua](http://zguide.zeromq.org/lua:asyncsrv) | [Node.js](http://zguide.zeromq.org/js:asyncsrv) | [PHP](http://zguide.zeromq.org/php:asyncsrv) | [Python](http://zguide.zeromq.org/py:asyncsrv) | [Ruby](http://zguide.zeromq.org/rb:asyncsrv) | [Scala](http://zguide.zeromq.org/scala:asyncsrv) | [Tcl](http://zguide.zeromq.org/tcl:asyncsrv) | [Ada | Basic | CL | Felix | Objective-C | ooc | Perl | Q | Racket](http://zguide.zeromq.org/main:translate)

Some comments on this code:

- The clients send a request once per second, and get zero or more replies back. To make this work using `zmq_poll()`, we can't simply poll with a 1-second timeout, or we'd end up sending a new request only one second *after we received the last reply*. So we poll at a high frequency (100 times at 1/100th of a second per poll), which is approximately accurate.

- The server uses a pool of worker threads, each processing one request synchronously. It connects these to its frontend socket using an internal queue. It connects the frontend and backend sockets using a `zmq_proxy()` call.

**Figure 38 - Detail of Asynchronous Server**

![fig38.png](https://github.com/imatix/zguide/raw/master/images/fig38.png)

Note that we're doing DEALER to ROUTER dialog between client and server, but internally between the server main thread and workers, we're doing DEALER to DEALER. If the workers were strictly synchronous, we'd use REP. However, because we want to send multiple replies, we need an async socket. We do *not* want to route replies, they always go to the single server thread that sent us the request.

Let's think about the routing envelope. The client sends a message consisting of a single frame. The server thread receives a two-frame message (original message prefixed by client identity). We send these two frames on to the worker, which treats it as a normal reply envelope, returns that to us as a two frame message. We then use the first frame as an identity to route the second frame back to the client as a reply.

It looks something like this:

```
     client          server       frontend       worker
   [ DEALER ]<---->[ ROUTER <----> DEALER <----> DEALER ]
             1 part         2 parts       2 parts
```

Now for the sockets: we could use the load balancing ROUTER to DEALER pattern to talk to workers, but it's extra work. In this case, a DEALER to DEALER pattern is probably fine: the trade-off is lower latency for each request, but higher risk of unbalanced work distribution. Simplicity wins in this case.

When you build servers that maintain stateful conversations with clients, you will run into a classic problem. If the server keeps some state per client, and clients keep coming and going, eventually it will run out of resources. Even if the same clients keep connecting, if you're using default identities, each connection will look like a new one.

We cheat in the above example by keeping state only for a very short time (the time it takes a worker to process a request) and then throwing away the state. But that's not practical for many cases. To properly manage client state in a stateful asynchronous server, you have to:

- Do heartbeating from client to server. In our example, we send a request once per second, which can reliably be used as a heartbeat.

- Store state using the client identity (whether generated or explicit) as key.

- Detect a stopped heartbeat. If there's no request from a client within, say, two seconds, the server can detect this and destroy any state it's holding for that client.



| [Worked Example: Inter-Broker Routing](http://zguide.zeromq.org/page:all#Worked-Example-Inter-Broker-Routing) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-77) [next](http://zguide.zeromq.org/page:all#header-79) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Let's take everything we've seen so far, and scale things up to a real application. We'll build this step-by-step over several iterations. Our best client calls us urgently and asks for a design of a large cloud computing facility. He has this vision of a cloud that spans many data centers, each a cluster of clients and workers, and that works together as a whole. Because we're smart enough to know that practice always beats theory, we propose to make a working simulation using ZeroMQ. Our client, eager to lock down the budget before his own boss changes his mind, and having read great things about ZeroMQ on Twitter, agrees.



| [Establishing the Details](http://zguide.zeromq.org/page:all#Establishing-the-Details) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-78) [next](http://zguide.zeromq.org/page:all#header-80) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Several espressos later, we want to jump into writing code, but a little voice tells us to get more details before making a sensational solution to entirely the wrong problem. "What kind of work is the cloud doing?", we ask.

The client explains:

- Workers run on various kinds of hardware, but they are all able to handle any task. There are several hundred workers per cluster, and as many as a dozen clusters in total.

- Clients create tasks for workers. Each task is an independent unit of work and all the client wants is to find an available worker, and send it the task, as soon as possible. There will be a lot of clients and they'll come and go arbitrarily.

- The real difficulty is to be able to add and remove clusters at any time. A cluster can leave or join the cloud instantly, bringing all its workers and clients with it.

- If there are no workers in their own cluster, clients' tasks will go off to other available workers in the cloud.

- Clients send out one task at a time, waiting for a reply. If they don't get an answer within X seconds, they'll just send out the task again. This isn't our concern; the client API does it already.

- Workers process one task at a time; they are very simple beasts. If they crash, they get restarted by whatever script started them.

So we double-check to make sure that we understood this correctly:

- "There will be some kind of super-duper network interconnect between clusters, right?", we ask. The client says, "Yes, of course, we're not idiots."

- "What kind of volumes are we talking about?", we ask. The client replies, "Up to a thousand clients per cluster, each doing at most ten requests per second. Requests are small, and replies are also small, no more than 1K bytes each."

So we do a little calculation and see that this will work nicely over plain TCP. 2,500 clients x 10/second x 1,000 bytes x 2 directions = 50MB/sec or 400Mb/sec, not a problem for a 1Gb network.

It's a straightforward problem that requires no exotic hardware or protocols, just some clever routing algorithms and careful design. We start by designing one cluster (one data center) and then we figure out how to connect clusters together.



| [Architecture of a Single Cluster](http://zguide.zeromq.org/page:all#Architecture-of-a-Single-Cluster) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-79) [next](http://zguide.zeromq.org/page:all#header-81) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Workers and clients are synchronous. We want to use the load balancing pattern to route tasks to workers. Workers are all identical; our facility has no notion of different services. Workers are anonymous; clients never address them directly. We make no attempt here to provide guaranteed delivery, retry, and so on.

For reasons we already examined, clients and workers won't speak to each other directly. It makes it impossible to add or remove nodes dynamically. So our basic model consists of the request-reply message broker we saw earlier.

**Figure 39 - Cluster Architecture**

![fig39.png](https://github.com/imatix/zguide/raw/master/images/fig39.png)



| [Scaling to Multiple Clusters](http://zguide.zeromq.org/page:all#Scaling-to-Multiple-Clusters) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-80) [next](http://zguide.zeromq.org/page:all#header-82) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Now we scale this out to more than one cluster. Each cluster has a set of clients and workers, and a broker that joins these together.

**Figure 40 - Multiple Clusters**

![fig40.png](https://github.com/imatix/zguide/raw/master/images/fig40.png)

The question is: how do we get the clients of each cluster talking to the workers of the other cluster? There are a few possibilities, each with pros and cons:

- Clients could connect directly to both brokers. The advantage is that we don't need to modify brokers or workers. But clients get more complex and become aware of the overall topology. If we want to add a third or forth cluster, for example, all the clients are affected. In effect we have to move routing and failover logic into the clients and that's not nice.

- Workers might connect directly to both brokers. But REQ workers can't do that, they can only reply to one broker. We might use REPs but REPs don't give us customizable broker-to-worker routing like load balancing does, only the built-in load balancing. That's a fail; if we want to distribute work to idle workers, we precisely need load balancing. One solution would be to use ROUTER sockets for the worker nodes. Let's label this "Idea #1".

- Brokers could connect to each other. This looks neatest because it creates the fewest additional connections. We can't add clusters on the fly, but that is probably out of scope. Now clients and workers remain ignorant of the real network topology, and brokers tell each other when they have spare capacity. Let's label this "Idea #2".

Let's explore Idea #1. In this model, we have workers connecting to both brokers and accepting jobs from either one.

**Figure 41 - Idea 1: Cross-connected Workers**

![fig41.png](https://github.com/imatix/zguide/raw/master/images/fig41.png)

It looks feasible. However, it doesn't provide what we wanted, which was that clients get local workers if possible and remote workers only if it's better than waiting. Also workers will signal "ready" to both brokers and can get two jobs at once, while other workers remain idle. It seems this design fails because again we're putting routing logic at the edges.

So, idea #2 then. We interconnect the brokers and don't touch the clients or workers, which are REQs like we're used to.

**Figure 42 - Idea 2: Brokers Talking to Each Other**

![fig42.png](https://github.com/imatix/zguide/raw/master/images/fig42.png)

This design is appealing because the problem is solved in one place, invisible to the rest of the world. Basically, brokers open secret channels to each other and whisper, like camel traders, "Hey, I've got some spare capacity. If you have too many clients, give me a shout and we'll deal".

In effect it is just a more sophisticated routing algorithm: brokers become subcontractors for each other. There are other things to like about this design, even before we play with real code:

- It treats the common case (clients and workers on the same cluster) as default and does extra work for the exceptional case (shuffling jobs between clusters).

- It lets us use different message flows for the different types of work. That means we can handle them differently, e.g., using different types of network connection.

- It feels like it would scale smoothly. Interconnecting three or more brokers doesn't get overly complex. If we find this to be a problem, it's easy to solve by adding a super-broker.

We'll now make a worked example. We'll pack an entire cluster into one process. That is obviously not realistic, but it makes it simple to simulate, and the simulation can accurately scale to real processes. This is the beauty of ZeroMQ—you can design at the micro-level and scale that up to the macro-level. Threads become processes, and then become boxes and the patterns and logic remain the same. Each of our "cluster" processes contains client threads, worker threads, and a broker thread.

We know the basic model well by now:

- The REQ client (REQ) threads create workloads and pass them to the broker (ROUTER).
- The REQ worker (REQ) threads process workloads and return the results to the broker (ROUTER).
- The broker queues and distributes workloads using the load balancing pattern.



| [Federation Versus Peering](http://zguide.zeromq.org/page:all#Federation-Versus-Peering) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-81) [next](http://zguide.zeromq.org/page:all#header-83) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

There are several possible ways to interconnect brokers. What we want is to be able to tell other brokers, "we have capacity", and then receive multiple tasks. We also need to be able to tell other brokers, "stop, we're full". It doesn't need to be perfect; sometimes we may accept jobs we can't process immediately, then we'll do them as soon as possible.

The simplest interconnect is *federation*, in which brokers simulate clients and workers for each other. We would do this by connecting our frontend to the other broker's backend socket. Note that it is legal to both bind a socket to an endpoint and connect it to other endpoints.

**Figure 43 - Cross-connected Brokers in Federation Model**

![fig43.png](https://github.com/imatix/zguide/raw/master/images/fig43.png)

This would give us simple logic in both brokers and a reasonably good mechanism: when there are no workers, tell the other broker "ready", and accept one job from it. The problem is also that it is too simple for this problem. A federated broker would be able to handle only one task at a time. If the broker emulates a lock-step client and worker, it is by definition also going to be lock-step, and if it has lots of available workers they won't be used. Our brokers need to be connected in a fully asynchronous fashion.

The federation model is perfect for other kinds of routing, especially service-oriented architectures (SOAs), which route by service name and proximity rather than load balancing or round robin. So don't dismiss it as useless, it's just not right for all use cases.

Instead of federation, let's look at a *peering* approach in which brokers are explicitly aware of each other and talk over privileged channels. Let's break this down, assuming we want to interconnect N brokers. Each broker has (N - 1) peers, and all brokers are using exactly the same code and logic. There are two distinct flows of information between brokers:

- Each broker needs to tell its peers how many workers it has available at any time. This can be fairly simple information—just a quantity that is updated regularly. The obvious (and correct) socket pattern for this is pub-sub. So every broker opens a PUB socket and publishes state information on that, and every broker also opens a SUB socket and connects that to the PUB socket of every other broker to get state information from its peers.

- Each broker needs a way to delegate tasks to a peer and get replies back, asynchronously. We'll do this using ROUTER sockets; no other combination works. Each broker has two such sockets: one for tasks it receives and one for tasks it delegates. If we didn't use two sockets, it would be more work to know whether we were reading a request or a reply each time. That would mean adding more information to the message envelope.

And there is also the flow of information between a broker and its local clients and workers.



| [The Naming Ceremony](http://zguide.zeromq.org/page:all#The-Naming-Ceremony) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-82) [next](http://zguide.zeromq.org/page:all#header-84) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Three flows x two sockets for each flow = six sockets that we have to manage in the broker. Choosing good names is vital to keeping a multisocket juggling act reasonably coherent in our minds. Sockets *do* something and what they do should form the basis for their names. It's about being able to read the code several weeks later on a cold Monday morning before coffee, and not feel any pain.

Let's do a shamanistic naming ceremony for the sockets. The three flows are:

- A *local* request-reply flow between the broker and its clients and workers.
- A *cloud* request-reply flow between the broker and its peer brokers.
- A *state* flow between the broker and its peer brokers.

Finding meaningful names that are all the same length means our code will align nicely. It's not a big thing, but attention to details helps. For each flow the broker has two sockets that we can orthogonally call the *frontend* and *backend*. We've used these names quite often. A frontend receives information or tasks. A backend sends those out to other peers. The conceptual flow is from front to back (with replies going in the opposite direction from back to front).

So in all the code we write for this tutorial, we will use these socket names:

- *localfe* and *localbe* for the local flow.
- *cloudfe* and *cloudbe* for the cloud flow.
- *statefe* and *statebe* for the state flow.

For our transport and because we're simulating the whole thing on one box, we'll use `ipc` for everything. This has the advantage of working like `tcp` in terms of connectivity (i.e., it's a disconnected transport, unlike `inproc`), yet we don't need IP addresses or DNS names, which would be a pain here. Instead, we will use `ipc` endpoints called *something*-`local`, *something*-`cloud`, and *something*-`state`, where *something* is the name of our simulated cluster.

You might be thinking that this is a lot of work for some names. Why not call them s1, s2, s3, s4, etc.? The answer is that if your brain is not a perfect machine, you need a lot of help when reading code, and we'll see that these names do help. It's easier to remember "three flows, two directions" than "six different sockets".

**Figure 44 - Broker Socket Arrangement**

![fig44.png](https://github.com/imatix/zguide/raw/master/images/fig44.png)

Note that we connect the cloudbe in each broker to the cloudfe in every other broker, and likewise we connect the statebe in each broker to the statefe in every other broker.



| [Prototyping the State Flow](http://zguide.zeromq.org/page:all#Prototyping-the-State-Flow) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-83) [next](http://zguide.zeromq.org/page:all#header-85) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Because each socket flow has its own little traps for the unwary, we will test them in real code one-by-one, rather than try to throw the whole lot into code in one go. When we're happy with each flow, we can put them together into a full program. We'll start with the state flow.

**Figure 45 - The State Flow**

![fig45.png](https://github.com/imatix/zguide/raw/master/images/fig45.png)

Here is how this works in code:

[peering1: Prototype state flow in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:peering1) | [Clojure](http://zguide.zeromq.org/clj:peering1) | [Delphi](http://zguide.zeromq.org/dpr:peering1) | [F#](http://zguide.zeromq.org/fsx:peering1) | [Go](http://zguide.zeromq.org/go:peering1) | [Haskell](http://zguide.zeromq.org/hs:peering1) | [Haxe](http://zguide.zeromq.org/hx:peering1) | [Java](http://zguide.zeromq.org/java:peering1) | [Lua](http://zguide.zeromq.org/lua:peering1) | [Node.js](http://zguide.zeromq.org/js:peering1) | [PHP](http://zguide.zeromq.org/php:peering1) | [Python](http://zguide.zeromq.org/py:peering1) | [Racket](http://zguide.zeromq.org/rkt:peering1) | [Ruby](http://zguide.zeromq.org/rb:peering1) | [Scala](http://zguide.zeromq.org/scala:peering1) | [Tcl](http://zguide.zeromq.org/tcl:peering1) | [Ada | Basic | C++ | CL | Erlang | Felix | Objective-C | ooc | Perl | Q](http://zguide.zeromq.org/main:translate)

- Each broker has an identity that we use to construct `ipc` endpoint names. A real broker would need to work with TCP and a more sophisticated configuration scheme. We'll look at such schemes later in this book, but for now, using generated `ipc` names lets us ignore the problem of where to get TCP/IP addresses or names.

- We use a `zmq_poll()` loop as the core of the program. This processes incoming messages and sends out state messages. We send a state message *only* if we did not get any incoming messages *and* we waited for a second. If we send out a state message each time we get one in, we'll get message storms.

- We use a two-part pub-sub message consisting of sender address and data. Note that we will need to know the address of the publisher in order to send it tasks, and the only way is to send this explicitly as a part of the message.

- We don't set identities on subscribers because if we did then we'd get outdated state information when connecting to running brokers.

- We don't set a HWM on the publisher, but if we were using ZeroMQ v2.x that would be a wise idea.

We can build this little program and run it three times to simulate three clusters. Let's call them DC1, DC2, and DC3 (the names are arbitrary). We run these three commands, each in a separate window:

```
peering1 DC1 DC2 DC3  #  Start DC1 and connect to DC2 and DC3
peering1 DC2 DC1 DC3  #  Start DC2 and connect to DC1 and DC3
peering1 DC3 DC1 DC2  #  Start DC3 and connect to DC1 and DC2
```

You'll see each cluster report the state of its peers, and after a few seconds they will all happily be printing random numbers once per second. Try this and satisfy yourself that the three brokers all match up and synchronize to per-second state updates.

In real life, we'd not send out state messages at regular intervals, but rather whenever we had a state change, i.e., whenever a worker becomes available or unavailable. That may seem like a lot of traffic, but state messages are small and we've established that the inter-cluster connections are super fast.

If we wanted to send state messages at precise intervals, we'd create a child thread and open the `statebe` socket in that thread. We'd then send irregular state updates to that child thread from our main thread and allow the child thread to conflate them into regular outgoing messages. This is more work than we need here.



| [Prototyping the Local and Cloud Flows](http://zguide.zeromq.org/page:all#Prototyping-the-Local-and-Cloud-Flows) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-84) [next](http://zguide.zeromq.org/page:all#header-86) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Let's now prototype the flow of tasks via the local and cloud sockets. This code pulls requests from clients and then distributes them to local workers and cloud peers on a random basis.

**Figure 46 - The Flow of Tasks**

![fig46.png](https://github.com/imatix/zguide/raw/master/images/fig46.png)

Before we jump into the code, which is getting a little complex, let's sketch the core routing logic and break it down into a simple yet robust design.

We need two queues, one for requests from local clients and one for requests from cloud clients. One option would be to pull messages off the local and cloud frontends, and pump these onto their respective queues. But this is kind of pointless because ZeroMQ sockets *are*queues already. So let's use the ZeroMQ socket buffers as queues.

This was the technique we used in the load balancing broker, and it worked nicely. We only read from the two frontends when there is somewhere to send the requests. We can always read from the backends, as they give us replies to route back. As long as the backends aren't talking to us, there's no point in even looking at the frontends.

So our main loop becomes:

- Poll the backends for activity. When we get a message, it may be "ready" from a worker or it may be a reply. If it's a reply, route back via the local or cloud frontend.

- If a worker replied, it became available, so we queue it and count it.

- While there are workers available, take a request, if any, from either frontend and route to a local worker, or randomly, to a cloud peer.

Randomly sending tasks to a peer broker rather than a worker simulates work distribution across the cluster. It's dumb, but that is fine for this stage.

We use broker identities to route messages between brokers. Each broker has a name that we provide on the command line in this simple prototype. As long as these names don't overlap with the ZeroMQ-generated UUIDs used for client nodes, we can figure out whether to route a reply back to a client or to a broker.

Here is how this works in code. The interesting part starts around the comment "Interesting part".

[peering2: Prototype local and cloud flow in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:peering2) | [Delphi](http://zguide.zeromq.org/dpr:peering2) | [F#](http://zguide.zeromq.org/fsx:peering2) | [Go](http://zguide.zeromq.org/go:peering2) | [Haskell](http://zguide.zeromq.org/hs:peering2) | [Haxe](http://zguide.zeromq.org/hx:peering2) | [Java](http://zguide.zeromq.org/java:peering2) | [Lua](http://zguide.zeromq.org/lua:peering2) | [PHP](http://zguide.zeromq.org/php:peering2) | [Python](http://zguide.zeromq.org/py:peering2) | [Ruby](http://zguide.zeromq.org/rb:peering2) | [Scala](http://zguide.zeromq.org/scala:peering2) | [Tcl](http://zguide.zeromq.org/tcl:peering2) | [Ada | Basic | C++ | Clojure | CL | Erlang | Felix | Node.js | Objective-C | ooc | Perl | Q | Racket](http://zguide.zeromq.org/main:translate)

```
peering2 me you
peering2 you me
```

Some comments on this code:

- In the C code at least, using the zmsg class makes life much easier, and our code much shorter. It's obviously an abstraction that works. If you build ZeroMQ applications in C, you should use CZMQ.

- Because we're not getting any state information from peers, we naively assume they are running. The code prompts you to confirm when you've started all the brokers. In the real case, we'd not send anything to brokers who had not told us they exist.

You can satisfy yourself that the code works by watching it run forever. If there were any misrouted messages, clients would end up blocking, and the brokers would stop printing trace information. You can prove that by killing either of the brokers. The other broker tries to send requests to the cloud, and one-by-one its clients block, waiting for an answer.



| [Putting it All Together](http://zguide.zeromq.org/page:all#Putting-it-All-Together) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-85) [next](http://zguide.zeromq.org/page:all#header-87) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Let's put this together into a single package. As before, we'll run an entire cluster as one process. We're going to take the two previous examples and merge them into one properly working design that lets you simulate any number of clusters.

This code is the size of both previous prototypes together, at 270 LoC. That's pretty good for a simulation of a cluster that includes clients and workers and cloud workload distribution. Here is the code:

[peering3: Full cluster simulation in C](javascript:;)


[Delphi](http://zguide.zeromq.org/dpr:peering3) | [F#](http://zguide.zeromq.org/fsx:peering3) | [Go](http://zguide.zeromq.org/go:peering3) | [Haskell](http://zguide.zeromq.org/hs:peering3) | [Haxe](http://zguide.zeromq.org/hx:peering3) | [Java](http://zguide.zeromq.org/java:peering3) | [Lua](http://zguide.zeromq.org/lua:peering3) | [PHP](http://zguide.zeromq.org/php:peering3) | [Python](http://zguide.zeromq.org/py:peering3) | [Ruby](http://zguide.zeromq.org/rb:peering3) | [Tcl](http://zguide.zeromq.org/tcl:peering3) | [Ada | Basic | C++ | C# | Clojure | CL | Erlang | Felix | Node.js | Objective-C | ooc | Perl | Q | Racket | Scala](http://zguide.zeromq.org/main:translate)

- The client threads detect and report a failed request. They do this by polling for a response and if none arrives after a while (10 seconds), printing an error message.

- Client threads don't print directly, but instead send a message to a monitor socket (PUSH) that the main loop collects (PULL) and prints off. This is the first case we've seen of using ZeroMQ sockets for monitoring and logging; this is a big use case that we'll come back to later.

- Clients simulate varying loads to get the cluster 100% at random moments, so that tasks are shifted over to the cloud. The number of clients and workers, and delays in the client and worker threads control this. Feel free to play with them to see if you can make a more realistic simulation.

- The main loop uses two pollsets. It could in fact use three: information, backends, and frontends. As in the earlier prototype, there is no point in taking a frontend message if there is no backend capacity.

These are some of the problems that arose during development of this program:

- Clients would freeze, due to requests or replies getting lost somewhere. Recall that the ROUTER socket drops messages it can't route. The first tactic here was to modify the client thread to detect and report such problems. Secondly, I put `zmsg_dump()` calls after every receive and before every send in the main loop, until the origin of the problems was clear.

- The main loop was mistakenly reading from more than one ready socket. This caused the first message to be lost. I fixed that by reading only from the first ready socket.

- The `zmsg` class was not properly encoding UUIDs as C strings. This caused UUIDs that contain 0 bytes to be corrupted. I fixed that by modifying `zmsg` to encode UUIDs as printable hex strings.

This simulation does not detect disappearance of a cloud peer. If you start several peers and stop one, and it was broadcasting capacity to the others, they will continue to send it work even if it's gone. You can try this, and you will get clients that complain of lost requests. The solution is twofold: first, only keep the capacity information for a short time so that if a peer does disappear, its capacity is quickly set to zero. Second, add reliability to the request-reply chain. We'll look at reliability in the next chapter.



| [Chapter 4 - Reliable Request-Reply Patterns](http://zguide.zeromq.org/page:all#Chapter-Reliable-Request-Reply-Patterns) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-86) [next](http://zguide.zeromq.org/page:all#header-88) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

[Chapter 3 - Advanced Request-Reply Patterns](http://zguide.zeromq.org/page:all#advanced-request-reply) covered advanced uses of ZeroMQ's request-reply pattern with working examples. This chapter looks at the general question of reliability and builds a set of reliable messaging patterns on top of ZeroMQ's core request-reply pattern.

In this chapter, we focus heavily on user-space request-reply *patterns*, reusable models that help you design your own ZeroMQ architectures:

- The *Lazy Pirate* pattern: reliable request-reply from the client side
- The *Simple Pirate* pattern: reliable request-reply using load balancing
- The *Paranoid Pirate* pattern: reliable request-reply with heartbeating
- The *Majordomo* pattern: service-oriented reliable queuing
- The *Titanic* pattern: disk-based/disconnected reliable queuing
- The *Binary Star* pattern: primary-backup server failover
- The *Freelance* pattern: brokerless reliable request-reply



| [What is "Reliability"?](http://zguide.zeromq.org/page:all#What-is-Reliability) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-87) [next](http://zguide.zeromq.org/page:all#header-89) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Most people who speak of "reliability" don't really know what they mean. We can only define reliability in terms of failure. That is, if we can handle a certain set of well-defined and understood failures, then we are reliable with respect to those failures. No more, no less. So let's look at the possible causes of failure in a distributed ZeroMQ application, in roughly descending order of probability:

- Application code is the worst offender. It can crash and exit, freeze and stop responding to input, run too slowly for its input, exhaust all memory, and so on.

- System code—such as brokers we write using ZeroMQ—can die for the same reasons as application code. System code *should* be more reliable than application code, but it can still crash and burn, and especially run out of memory if it tries to queue messages for slow clients.

- Message queues can overflow, typically in system code that has learned to deal brutally with slow clients. When a queue overflows, it starts to discard messages. So we get "lost" messages.

- Networks can fail (e.g., WiFi gets switched off or goes out of range). ZeroMQ will automatically reconnect in such cases, but in the meantime, messages may get lost.

- Hardware can fail and take with it all the processes running on that box.

- Networks can fail in exotic ways, e.g., some ports on a switch may die and those parts of the network become inaccessible.

- Entire data centers can be struck by lightning, earthquakes, fire, or more mundane power or cooling failures.

To make a software system fully reliable against *all* of these possible failures is an enormously difficult and expensive job and goes beyond the scope of this book.

Because the first five cases in the above list cover 99.9% of real world requirements outside large companies (according to a highly scientific study I just ran, which also told me that 78% of statistics are made up on the spot, and moreover never to trust a statistic that we didn't falsify ourselves), that's what we'll examine. If you're a large company with money to spend on the last two cases, contact my company immediately! There's a large hole behind my beach house waiting to be converted into an executive swimming pool.



| [Designing Reliability](http://zguide.zeromq.org/page:all#Designing-Reliability) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-88) [next](http://zguide.zeromq.org/page:all#header-90) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

So to make things brutally simple, reliability is "keeping things working properly when code freezes or crashes", a situation we'll shorten to "dies". However, the things we want to keep working properly are more complex than just messages. We need to take each core ZeroMQ messaging pattern and see how to make it work (if we can) even when code dies.

Let's take them one-by-one:

- Request-reply: if the server dies (while processing a request), the client can figure that out because it won't get an answer back. Then it can give up in a huff, wait and try again later, find another server, and so on. As for the client dying, we can brush that off as "someone else's problem" for now.

- Pub-sub: if the client dies (having gotten some data), the server doesn't know about it. Pub-sub doesn't send any information back from client to server. But the client can contact the server out-of-band, e.g., via request-reply, and ask, "please resend everything I missed". As for the server dying, that's out of scope for here. Subscribers can also self-verify that they're not running too slowly, and take action (e.g., warn the operator and die) if they are.

- Pipeline: if a worker dies (while working), the ventilator doesn't know about it. Pipelines, like the grinding gears of time, only work in one direction. But the downstream collector can detect that one task didn't get done, and send a message back to the ventilator saying, "hey, resend task 324!" If the ventilator or collector dies, whatever upstream client originally sent the work batch can get tired of waiting and resend the whole lot. It's not elegant, but system code should really not die often enough to matter.

In this chapter we'll focus just on request-reply, which is the low-hanging fruit of reliable messaging.

The basic request-reply pattern (a REQ client socket doing a blocking send/receive to a REP server socket) scores low on handling the most common types of failure. If the server crashes while processing the request, the client just hangs forever. If the network loses the request or the reply, the client hangs forever.

Request-reply is still much better than TCP, thanks to ZeroMQ's ability to reconnect peers silently, to load balance messages, and so on. But it's still not good enough for real work. The only case where you can really trust the basic request-reply pattern is between two threads in the same process where there's no network or separate server process to die.

However, with a little extra work, this humble pattern becomes a good basis for real work across a distributed network, and we get a set of reliable request-reply (RRR) patterns that I like to call the *Pirate* patterns (you'll eventually get the joke, I hope).

There are, in my experience, roughly three ways to connect clients to servers. Each needs a specific approach to reliability:

- Multiple clients talking directly to a single server. Use case: a single well-known server to which clients need to talk. Types of failure we aim to handle: server crashes and restarts, and network disconnects.

- Multiple clients talking to a broker proxy that distributes work to multiple workers. Use case: service-oriented transaction processing. Types of failure we aim to handle: worker crashes and restarts, worker busy looping, worker overload, queue crashes and restarts, and network disconnects.

- Multiple clients talking to multiple servers with no intermediary proxies. Use case: distributed services such as name resolution. Types of failure we aim to handle: service crashes and restarts, service busy looping, service overload, and network disconnects.

Each of these approaches has its trade-offs and often you'll mix them. We'll look at all three in detail.



| [Client-Side Reliability (Lazy Pirate Pattern)](http://zguide.zeromq.org/page:all#Client-Side-Reliability-Lazy-Pirate-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-89) [next](http://zguide.zeromq.org/page:all#header-91) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

We can get very simple reliable request-reply with some changes to the client. We call this the Lazy Pirate pattern. Rather than doing a blocking receive, we:

- Poll the REQ socket and receive from it only when it's sure a reply has arrived.
- Resend a request, if no reply has arrived within a timeout period.
- Abandon the transaction if there is still no reply after several requests.

If you try to use a REQ socket in anything other than a strict send/receive fashion, you'll get an error (technically, the REQ socket implements a small finite-state machine to enforce the send/receive ping-pong, and so the error code is called "EFSM"). This is slightly annoying when we want to use REQ in a pirate pattern, because we may send several requests before getting a reply.

The pretty good brute force solution is to close and reopen the REQ socket after an error:

[lpclient: Lazy Pirate client in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:lpclient) | [C#](http://zguide.zeromq.org/cs:lpclient) | [Clojure](http://zguide.zeromq.org/clj:lpclient) | [Delphi](http://zguide.zeromq.org/dpr:lpclient) | [Go](http://zguide.zeromq.org/go:lpclient) | [Haskell](http://zguide.zeromq.org/hs:lpclient) | [Haxe](http://zguide.zeromq.org/hx:lpclient) | [Java](http://zguide.zeromq.org/java:lpclient) | [Lua](http://zguide.zeromq.org/lua:lpclient) | [Perl](http://zguide.zeromq.org/pl:lpclient) | [PHP](http://zguide.zeromq.org/php:lpclient) | [Python](http://zguide.zeromq.org/py:lpclient) | [Ruby](http://zguide.zeromq.org/rb:lpclient) | [Tcl](http://zguide.zeromq.org/tcl:lpclient) | [Ada | Basic | CL | Erlang | F# | Felix | Node.js | Objective-C | ooc | Q | Racket | Scala](http://zguide.zeromq.org/main:translate)

[lpserver: Lazy Pirate server in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:lpserver) | [C#](http://zguide.zeromq.org/cs:lpserver) | [Clojure](http://zguide.zeromq.org/clj:lpserver) | [Delphi](http://zguide.zeromq.org/dpr:lpserver) | [Go](http://zguide.zeromq.org/go:lpserver) | [Haskell](http://zguide.zeromq.org/hs:lpserver) | [Haxe](http://zguide.zeromq.org/hx:lpserver) | [Java](http://zguide.zeromq.org/java:lpserver) | [Lua](http://zguide.zeromq.org/lua:lpserver) | [Perl](http://zguide.zeromq.org/pl:lpserver) | [PHP](http://zguide.zeromq.org/php:lpserver) | [Python](http://zguide.zeromq.org/py:lpserver) | [Ruby](http://zguide.zeromq.org/rb:lpserver) | [Scala](http://zguide.zeromq.org/scala:lpserver) | [Tcl](http://zguide.zeromq.org/tcl:lpserver) | [Ada | Basic | CL | Erlang | F# | Felix | Node.js | Objective-C | ooc | Q | Racket](http://zguide.zeromq.org/main:translate)

![fig47.png](https://github.com/imatix/zguide/raw/master/images/fig47.png)

To run this test case, start the client and the server in two console windows. The server will randomly misbehave after a few messages. You can check the client's response. Here is typical output from the server:

```
I: normal request (1)
I: normal request (2)
I: normal request (3)
I: simulating CPU overload
I: normal request (4)
I: simulating a crash
```

And here is the client's response:

```
I: connecting to server...
I: server replied OK (1)
I: server replied OK (2)
I: server replied OK (3)
W: no response from server, retrying...
I: connecting to server...
W: no response from server, retrying...
I: connecting to server...
E: server seems to be offline, abandoning
```

The client sequences each message and checks that replies come back exactly in order: that no requests or replies are lost, and no replies come back more than once, or out of order. Run the test a few times until you're convinced that this mechanism actually works. You don't need sequence numbers in a production application; they just help us trust our design.

The client uses a REQ socket, and does the brute force close/reopen because REQ sockets impose that strict send/receive cycle. You might be tempted to use a DEALER instead, but it would not be a good decision. First, it would mean emulating the secret sauce that REQ does with envelopes (if you've forgotten what that is, it's a good sign you don't want to have to do it). Second, it would mean potentially getting back replies that you didn't expect.

Handling failures only at the client works when we have a set of clients talking to a single server. It can handle a server crash, but only if recovery means restarting that same server. If there's a permanent error, such as a dead power supply on the server hardware, this approach won't work. Because the application code in servers is usually the biggest source of failures in any architecture, depending on a single server is not a great idea.

So, pros and cons:

- Pro: simple to understand and implement.
- Pro: works easily with existing client and server application code.
- Pro: ZeroMQ automatically retries the actual reconnection until it works.
- Con: doesn't failover to backup or alternate servers.



| [Basic Reliable Queuing (Simple Pirate Pattern)](http://zguide.zeromq.org/page:all#Basic-Reliable-Queuing-Simple-Pirate-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-90) [next](http://zguide.zeromq.org/page:all#header-92) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Our second approach extends the Lazy Pirate pattern with a queue proxy that lets us talk, transparently, to multiple servers, which we can more accurately call "workers". We'll develop this in stages, starting with a minimal working model, the Simple Pirate pattern.

In all these Pirate patterns, workers are stateless. If the application requires some shared state, such as a shared database, we don't know about it as we design our messaging framework. Having a queue proxy means workers can come and go without clients knowing anything about it. If one worker dies, another takes over. This is a nice, simple topology with only one real weakness, namely the central queue itself, which can become a problem to manage, and a single point of failure.

**Figure 48 - The Simple Pirate Pattern**

![fig48.png](https://github.com/imatix/zguide/raw/master/images/fig48.png)

The basis for the queue proxy is the load balancing broker from [Chapter 3 - Advanced Request-Reply Patterns](http://zguide.zeromq.org/page:all#advanced-request-reply). What is the very *minimum* we need to do to handle dead or blocked workers? Turns out, it's surprisingly little. We already have a retry mechanism in the client. So using the load balancing pattern will work pretty well. This fits with ZeroMQ's philosophy that we can extend a peer-to-peer pattern like request-reply by plugging naive proxies in the middle.

We don't need a special client; we're still using the Lazy Pirate client. Here is the queue, which is identical to the main task of the load balancing broker:

[spqueue: Simple Pirate queue in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:spqueue) | [C#](http://zguide.zeromq.org/cs:spqueue) | [Clojure](http://zguide.zeromq.org/clj:spqueue) | [Delphi](http://zguide.zeromq.org/dpr:spqueue) | [Go](http://zguide.zeromq.org/go:spqueue) | [Haskell](http://zguide.zeromq.org/hs:spqueue) | [Haxe](http://zguide.zeromq.org/hx:spqueue) | [Java](http://zguide.zeromq.org/java:spqueue) | [Lua](http://zguide.zeromq.org/lua:spqueue) | [PHP](http://zguide.zeromq.org/php:spqueue) | [Python](http://zguide.zeromq.org/py:spqueue) | [Tcl](http://zguide.zeromq.org/tcl:spqueue) | [Ada | Basic | CL | Erlang | F# | Felix | Node.js | Objective-C | ooc | Perl | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

[spworker: Simple Pirate worker in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:spworker) | [C#](http://zguide.zeromq.org/cs:spworker) | [Clojure](http://zguide.zeromq.org/clj:spworker) | [Delphi](http://zguide.zeromq.org/dpr:spworker) | [Go](http://zguide.zeromq.org/go:spworker) | [Haskell](http://zguide.zeromq.org/hs:spworker) | [Haxe](http://zguide.zeromq.org/hx:spworker) | [Java](http://zguide.zeromq.org/java:spworker) | [Lua](http://zguide.zeromq.org/lua:spworker) | [PHP](http://zguide.zeromq.org/php:spworker) | [Python](http://zguide.zeromq.org/py:spworker) | [Tcl](http://zguide.zeromq.org/tcl:spworker) | [Ada | Basic | CL | Erlang | F# | Felix | Node.js | Objective-C | ooc | Perl | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)



| [Robust Reliable Queuing (Paranoid Pirate Pattern)](http://zguide.zeromq.org/page:all#Robust-Reliable-Queuing-Paranoid-Pirate-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-91) [next](http://zguide.zeromq.org/page:all#header-93) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

**Figure 49 - The Paranoid Pirate Pattern**

![fig49.png](https://github.com/imatix/zguide/raw/master/images/fig49.png)

The Simple Pirate Queue pattern works pretty well, especially because it's just a combination of two existing patterns. Still, it does have some weaknesses:

- It's not robust in the face of a queue crash and restart. The client will recover, but the workers won't. While ZeroMQ will reconnect workers' sockets automatically, as far as the newly started queue is concerned, the workers haven't signaled ready, so don't exist. To fix this, we have to do heartbeating from queue to worker so that the worker can detect when the queue has gone away.

- The queue does not detect worker failure, so if a worker dies while idle, the queue can't remove it from its worker queue until the queue sends it a request. The client waits and retries for nothing. It's not a critical problem, but it's not nice. To make this work properly, we do heartbeating from worker to queue, so that the queue can detect a lost worker at any stage.

We'll fix these in a properly pedantic Paranoid Pirate Pattern.

We previously used a REQ socket for the worker. For the Paranoid Pirate worker, we'll switch to a DEALER socket. This has the advantage of letting us send and receive messages at any time, rather than the lock-step send/receive that REQ imposes. The downside of DEALER is that we have to do our own envelope management (re-read [Chapter 3 - Advanced Request-Reply Patterns](http://zguide.zeromq.org/page:all#advanced-request-reply) for background on this concept).

We're still using the Lazy Pirate client. Here is the Paranoid Pirate queue proxy:

[ppqueue: Paranoid Pirate queue in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:ppqueue) | [C#](http://zguide.zeromq.org/cs:ppqueue) | [Go](http://zguide.zeromq.org/go:ppqueue) | [Haskell](http://zguide.zeromq.org/hs:ppqueue) | [Haxe](http://zguide.zeromq.org/hx:ppqueue) | [Java](http://zguide.zeromq.org/java:ppqueue) | [Lua](http://zguide.zeromq.org/lua:ppqueue) | [PHP](http://zguide.zeromq.org/php:ppqueue) | [Python](http://zguide.zeromq.org/py:ppqueue) | [Tcl](http://zguide.zeromq.org/tcl:ppqueue) | [Ada | Basic | Clojure | CL | Delphi | Erlang | F# | Felix | Node.js | Objective-C | ooc | Perl | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

Here is the Paranoid Pirate worker:

[ppworker: Paranoid Pirate worker in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:ppworker) | [C#](http://zguide.zeromq.org/cs:ppworker) | [Go](http://zguide.zeromq.org/go:ppworker) | [Haskell](http://zguide.zeromq.org/hs:ppworker) | [Haxe](http://zguide.zeromq.org/hx:ppworker) | [Java](http://zguide.zeromq.org/java:ppworker) | [Lua](http://zguide.zeromq.org/lua:ppworker) | [PHP](http://zguide.zeromq.org/php:ppworker) | [Python](http://zguide.zeromq.org/py:ppworker) | [Tcl](http://zguide.zeromq.org/tcl:ppworker) | [Ada | Basic | Clojure | CL | Delphi | Erlang | F# | Felix | Node.js | Objective-C | ooc | Perl | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

- The code includes simulation of failures, as before. This makes it (a) very hard to debug, and (b) dangerous to reuse. When you want to debug this, disable the failure simulation.

- The worker uses a reconnect strategy similar to the one we designed for the Lazy Pirate client, with two major differences: (a) it does an exponential back-off, and (b) it retries indefinitely (whereas the client retries a few times before reporting a failure).

Try the client, queue, and workers, such as by using a script like this:

```
ppqueue &
for i in 1 2 3 4; do
    ppworker &
    sleep 1
done
lpclient &
```

You should see the workers die one-by-one as they simulate a crash, and the client eventually give up. You can stop and restart the queue and both client and workers will reconnect and carry on. And no matter what you do to queues and workers, the client will never get an out-of-order reply: the whole chain either works, or the client abandons.



| [Heartbeating](http://zguide.zeromq.org/page:all#Heartbeating) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-92) [next](http://zguide.zeromq.org/page:all#header-94) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Heartbeating solves the problem of knowing whether a peer is alive or dead. This is not an issue specific to ZeroMQ. TCP has a long timeout (30 minutes or so), that means that it can be impossible to know whether a peer has died, been disconnected, or gone on a weekend to Prague with a case of vodka, a redhead, and a large expense account.

It's is not easy to get heartbeating right. When writing the Paranoid Pirate examples, it took about five hours to get the heartbeating working properly. The rest of the request-reply chain took perhaps ten minutes. It is especially easy to create "false failures", i.e., when peers decide that they are disconnected because the heartbeats aren't sent properly.

We'll look at the three main answers people use for heartbeating with ZeroMQ.



| [Shrugging It Off](http://zguide.zeromq.org/page:all#Shrugging-It-Off) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-93) [next](http://zguide.zeromq.org/page:all#header-95) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The most common approach is to do no heartbeating at all and hope for the best. Many if not most ZeroMQ applications do this. ZeroMQ encourages this by hiding peers in many cases. What problems does this approach cause?

- When we use a ROUTER socket in an application that tracks peers, as peers disconnect and reconnect, the application will leak memory (resources that the application holds for each peer) and get slower and slower.

- When we use SUB- or DEALER-based data recipients, we can't tell the difference between good silence (there's no data) and bad silence (the other end died). When a recipient knows the other side died, it can for example switch over to a backup route.

- If we use a TCP connection that stays silent for a long while, it will, in some networks, just die. Sending something (technically, a "keep-alive" more than a heartbeat), will keep the network alive.



| [One-Way Heartbeats](http://zguide.zeromq.org/page:all#One-Way-Heartbeats) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-94) [next](http://zguide.zeromq.org/page:all#header-96) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

A second option is to send a heartbeat message from each node to its peers every second or so. When one node hears nothing from another within some timeout (several seconds, typically), it will treat that peer as dead. Sounds good, right? Sadly, no. This works in some cases but has nasty edge cases in others.

For pub-sub, this does work, and it's the only model you can use. SUB sockets cannot talk back to PUB sockets, but PUB sockets can happily send "I'm alive" messages to their subscribers.

As an optimization, you can send heartbeats only when there is no real data to send. Furthermore, you can send heartbeats progressively slower and slower, if network activity is an issue (e.g., on mobile networks where activity drains the battery). As long as the recipient can detect a failure (sharp stop in activity), that's fine.

Here are the typical problems with this design:

- It can be inaccurate when we send large amounts of data, as heartbeats will be delayed behind that data. If heartbeats are delayed, you can get false timeouts and disconnections due to network congestion. Thus, always treat *any* incoming data as a heartbeat, whether or not the sender optimizes out heartbeats.

- While the pub-sub pattern will drop messages for disappeared recipients, PUSH and DEALER sockets will queue them. So if you send heartbeats to a dead peer and it comes back, it will get all the heartbeats you sent, which can be thousands. Whoa, whoa!

- This design assumes that heartbeat timeouts are the same across the whole network. But that won't be accurate. Some peers will want very aggressive heartbeating in order to detect faults rapidly. And some will want very relaxed heartbeating, in order to let sleeping networks lie and save power.



| [Ping-Pong Heartbeats](http://zguide.zeromq.org/page:all#Ping-Pong-Heartbeats) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-95) [next](http://zguide.zeromq.org/page:all#header-97) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The third option is to use a ping-pong dialog. One peer sends a ping command to the other, which replies with a pong command. Neither command has any payload. Pings and pongs are not correlated. Because the roles of "client" and "server" are arbitrary in some networks, we usually specify that either peer can in fact send a ping and expect a pong in response. However, because the timeouts depend on network topologies known best to dynamic clients, it is usually the client that pings the server.

This works for all ROUTER-based brokers. The same optimizations we used in the second model make this work even better: treat any incoming data as a pong, and only send a ping when not otherwise sending data.



| [Heartbeating for Paranoid Pirate](http://zguide.zeromq.org/page:all#Heartbeating-for-Paranoid-Pirate) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-96) [next](http://zguide.zeromq.org/page:all#header-98) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

For Paranoid Pirate, we chose the second approach. It might not have been the simplest option: if designing this today, I'd probably try a ping-pong approach instead. However the principles are similar. The heartbeat messages flow asynchronously in both directions, and either peer can decide the other is "dead" and stop talking to it.

In the worker, this is how we handle heartbeats from the queue:

- We calculate a *liveness*, which is how many heartbeats we can still miss before deciding the queue is dead. It starts at three and we decrement it each time we miss a heartbeat.
- We wait, in the `zmq_poll` loop, for one second each time, which is our heartbeat interval.
- If there's any message from the queue during that time, we reset our liveness to three.
- If there's no message during that time, we count down our liveness.
- If the liveness reaches zero, we consider the queue dead.
- If the queue is dead, we destroy our socket, create a new one, and reconnect.
- To avoid opening and closing too many sockets, we wait for a certain interval before reconnecting, and we double the interval each time until it reaches 32 seconds.

And this is how we handle heartbeats *to* the queue:

- We calculate when to send the next heartbeat; this is a single variable because we're talking to one peer, the queue.
- In the `zmq_poll` loop, whenever we pass this time, we send a heartbeat to the queue.

Here's the essential heartbeating code for the worker:

\#define HEARTBEAT_LIVENESS`  `3`       `*//  3-5 is reasonable*
\#define HEARTBEAT_INTERVAL`  `1000`    `*//  msecs*
\#define INTERVAL_INIT`       `1000`    `*//  Initial reconnect*
\#define INTERVAL_MAX`       `32000`    `*//  After exponential backoff*

…
*//  If liveness hits zero, queue is considered disconnected*
size_t liveness = HEARTBEAT_LIVENESS;
size_t interval = INTERVAL_INIT;

*//  Send out heartbeats at regular intervals*
uint64_t heartbeat_at = zclock_time () + HEARTBEAT_INTERVAL;

**while** (true) {
`    `zmq_pollitem_t items [] = { { worker,`  `0, ZMQ_POLLIN, 0 } };
`    `int rc = zmq_poll (items, 1, HEARTBEAT_INTERVAL * ZMQ_POLL_MSEC);

`    `**if** (items [0].revents & ZMQ_POLLIN) {
`        `*//  Receive any message from queue*
`        `liveness = HEARTBEAT_LIVENESS;
`        `interval = INTERVAL_INIT;
`    }`
`    `**else**
`    `**if** (--liveness == 0) {
`        `zclock_sleep (interval);
`        `**if** (interval < INTERVAL_MAX)
`            `interval *= 2;
`        `zsocket_destroy (ctx, worker);
`        `…
`        `liveness = HEARTBEAT_LIVENESS;
`    }`
`    `*//  Send heartbeat to queue if it's time*
`    `**if** (zclock_time () > heartbeat_at) {
`        `heartbeat_at = zclock_time () + HEARTBEAT_INTERVAL;
`        `*//  Send heartbeat message to queue*
`    }`
}

The queue does the same, but manages an expiration time for each worker.

Here are some tips for your own heartbeating implementation:

- Use `zmq_poll` or a reactor as the core of your application's main task.

- Start by building the heartbeating between peers, test it by simulating failures, and *then* build the rest of the message flow. Adding heartbeating afterwards is much trickier.

- Use simple tracing, i.e., print to console, to get this working. To help you trace the flow of messages between peers, use a dump method such as zmsg offers, and number your messages incrementally so you can see if there are gaps.

- In a real application, heartbeating must be configurable and usually negotiated with the peer. Some peers will want aggressive heartbeating, as low as 10 msecs. Other peers will be far away and want heartbeating as high as 30 seconds.

- If you have different heartbeat intervals for different peers, your poll timeout should be the lowest (shortest time) of these. Do not use an infinite timeout.

- Do heartbeating on the same socket you use for messages, so your heartbeats also act as a *keep-alive* to stop the network connection from going stale (some firewalls can be unkind to silent connections).



| [Contracts and Protocols](http://zguide.zeromq.org/page:all#Contracts-and-Protocols) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-97) [next](http://zguide.zeromq.org/page:all#header-99) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

If you're paying attention, you'll realize that Paranoid Pirate is not interoperable with Simple Pirate, because of the heartbeats. But how do we define "interoperable"? To guarantee interoperability, we need a kind of contract, an agreement that lets different teams in different times and places write code that is guaranteed to work together. We call this a "protocol".

It's fun to experiment without specifications, but that's not a sensible basis for real applications. What happens if we want to write a worker in another language? Do we have to read code to see how things work? What if we want to change the protocol for some reason? Even a simple protocol will, if it's successful, evolve and become more complex.

Lack of contracts is a sure sign of a disposable application. So let's write a contract for this protocol. How do we do that?

There's a wiki at [rfc.zeromq.org](http://rfc.zeromq.org/) that we made especially as a home for public ZeroMQ contracts.
To create a new specification, register on the wiki if needed, and follow the instructions. It's fairly straightforward, though writing technical texts is not everyone's cup of tea.

It took me about fifteen minutes to draft the new [Pirate Pattern Protocol](http://rfc.zeromq.org/spec:6). It's not a big specification, but it does capture enough to act as the basis for arguments ("your queue isn't PPP compatible; please fix it!").

Turning PPP into a real protocol would take more work:

- There should be a protocol version number in the READY command so that it's possible to distinguish between different versions of PPP.

- Right now, READY and HEARTBEAT are not entirely distinct from requests and replies. To make them distinct, we would need a message structure that includes a "message type" part.



| [Service-Oriented Reliable Queuing (Majordomo Pattern)](http://zguide.zeromq.org/page:all#Service-Oriented-Reliable-Queuing-Majordomo-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-98) [next](http://zguide.zeromq.org/page:all#header-100) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

**Figure 50 - The Majordomo Pattern**

![fig50.png](https://github.com/imatix/zguide/raw/master/images/fig50.png)

The nice thing about progress is how fast it happens when lawyers and committees aren't involved. The [one-page MDP specification](http://rfc.zeromq.org/spec:7) turns PPP into something more solid. This is how we should design complex architectures: start by writing down the contracts, and only *then*write software to implement them.

The Majordomo Protocol (MDP) extends and improves on PPP in one interesting way: it adds a "service name" to requests that the client sends, and asks workers to register for specific services. Adding service names turns our Paranoid Pirate queue into a service-oriented broker. The nice thing about MDP is that it came out of working code, a simpler ancestor protocol (PPP), and a precise set of improvements that each solved a clear problem. This made it easy to draft.

To implement Majordomo, we need to write a framework for clients and workers. It's really not sane to ask every application developer to read the spec and make it work, when they could be using a simpler API that does the work for them.

So while our first contract (MDP itself) defines how the pieces of our distributed architecture talk to each other, our second contract defines how user applications talk to the technical framework we're going to design.

Majordomo has two halves, a client side and a worker side. Because we'll write both client and worker applications, we will need two APIs. Here is a sketch for the client API, using a simple object-oriented approach:

//`  `Majordomo Protocol client example
//`  `Uses the mdcli API to hide all MDP aspects

//`  `Lets us build this source without creating a library
\#include "mdcliapi.c"

int main (int argc, char *argv [])
{
`    `int verbose = (argc > 1 && streq (argv [1], "-v"));
`    `mdcli_t *session = mdcli_new ("tcp://localhost:5555", verbose);

`    `int count;
`    `**for** (count = 0; count < 100000; count++) {
`        `zmsg_t *request = zmsg_new ();
`        `zmsg_pushstr (request, "Hello world");
`        `zmsg_t *reply = mdcli_send (session, "echo", &request);
`        `**if** (reply)
`            `zmsg_destroy (&reply);
`        `**else**
`            `**break**;`              `*//  Interrupt or failure*
`    }`
`    `printf ("%d requests/replies processed**\n**", count);
`    `mdcli_destroy (&session);
`    `**return** 0;
}

That's it. We open a session to the broker, send a request message, get a reply message back, and eventually close the connection. Here's a sketch for the worker API:

//`  `Majordomo Protocol worker example
//`  `Uses the mdwrk API to hide all MDP aspects

//`  `Lets us build this source without creating a library
\#include "mdwrkapi.c"

int main (int argc, char *argv [])
{
`    `int verbose = (argc > 1 && streq (argv [1], "-v"));
`    `mdwrk_t *session = mdwrk_new (
`        `"tcp://localhost:5555", "echo", verbose);

`    `zmsg_t *reply = NULL;
`    `**while** (true) {
`        `zmsg_t *request = mdwrk_recv (session, &reply);
`        `**if** (request == NULL)
`            `**break**;`              `*//  Worker was interrupted*
`        `reply = request;`        `*//  Echo is complex… :-)*
`    }`
`    `mdwrk_destroy (&session);
`    `**return** 0;
}

It's more or less symmetrical, but the worker dialog is a little different. The first time a worker does a recv(), it passes a null reply. Thereafter, it passes the current reply, and gets a new request.

The client and worker APIs were fairly simple to construct because they're heavily based on the Paranoid Pirate code we already developed. Here is the client API:

[mdcliapi: Majordomo client API in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:mdcliapi) | [Go](http://zguide.zeromq.org/go:mdcliapi) | [Haxe](http://zguide.zeromq.org/hx:mdcliapi) | [Java](http://zguide.zeromq.org/java:mdcliapi) | [Lua](http://zguide.zeromq.org/lua:mdcliapi) | [PHP](http://zguide.zeromq.org/php:mdcliapi) | [Python](http://zguide.zeromq.org/py:mdcliapi) | [Tcl](http://zguide.zeromq.org/tcl:mdcliapi) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Haskell | Node.js | Objective-C | ooc | Perl | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

[mdclient: Majordomo client application in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:mdclient) | [C#](http://zguide.zeromq.org/cs:mdclient) | [Go](http://zguide.zeromq.org/go:mdclient) | [Haskell](http://zguide.zeromq.org/hs:mdclient) | [Haxe](http://zguide.zeromq.org/hx:mdclient) | [Java](http://zguide.zeromq.org/java:mdclient) | [Lua](http://zguide.zeromq.org/lua:mdclient) | [PHP](http://zguide.zeromq.org/php:mdclient) | [Python](http://zguide.zeromq.org/py:mdclient) | [Tcl](http://zguide.zeromq.org/tcl:mdclient) | [Ada | Basic | Clojure | CL | Delphi | Erlang | F# | Felix | Node.js | Objective-C | ooc | Perl | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

[mdwrkapi: Majordomo worker API in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:mdwrkapi) | [Go](http://zguide.zeromq.org/go:mdwrkapi) | [Haxe](http://zguide.zeromq.org/hx:mdwrkapi) | [Java](http://zguide.zeromq.org/java:mdwrkapi) | [Lua](http://zguide.zeromq.org/lua:mdwrkapi) | [PHP](http://zguide.zeromq.org/php:mdwrkapi) | [Python](http://zguide.zeromq.org/py:mdwrkapi) | [Ruby](http://zguide.zeromq.org/rb:mdwrkapi) | [Tcl](http://zguide.zeromq.org/tcl:mdwrkapi) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Haskell | Node.js | Objective-C | ooc | Perl | Q | Racket | Scala](http://zguide.zeromq.org/main:translate)

[mdworker: Majordomo worker application in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:mdworker) | [C#](http://zguide.zeromq.org/cs:mdworker) | [Go](http://zguide.zeromq.org/go:mdworker) | [Haskell](http://zguide.zeromq.org/hs:mdworker) | [Haxe](http://zguide.zeromq.org/hx:mdworker) | [Java](http://zguide.zeromq.org/java:mdworker) | [Lua](http://zguide.zeromq.org/lua:mdworker) | [PHP](http://zguide.zeromq.org/php:mdworker) | [Python](http://zguide.zeromq.org/py:mdworker) | [Ruby](http://zguide.zeromq.org/rb:mdworker) | [Tcl](http://zguide.zeromq.org/tcl:mdworker) | [Ada | Basic | Clojure | CL | Delphi | Erlang | F# | Felix | Node.js | Objective-C | ooc | Perl | Q | Racket | Scala](http://zguide.zeromq.org/main:translate)

- The APIs are single-threaded. This means, for example, that the worker won't send heartbeats in the background. Happily, this is exactly what we want: if the worker application gets stuck, heartbeats will stop and the broker will stop sending requests to the worker.

- The worker API doesn't do an exponential back-off; it's not worth the extra complexity.

- The APIs don't do any error reporting. If something isn't as expected, they raise an assertion (or exception depending on the language). This is ideal for a reference implementation, so any protocol errors show immediately. For real applications, the API should be robust against invalid messages.

You might wonder why the worker API is manually closing its socket and opening a new one, when ZeroMQ will automatically reconnect a socket if the peer disappears and comes back. Look back at the Simple Pirate and Paranoid Pirate workers to understand. Although ZeroMQ will automatically reconnect workers if the broker dies and comes back up, this isn't sufficient to re-register the workers with the broker. I know of at least two solutions. The simplest, which we use here, is for the worker to monitor the connection using heartbeats, and if it decides the broker is dead, to close its socket and start afresh with a new socket. The alternative is for the broker to challenge unknown workers when it gets a heartbeat from the worker and ask them to re-register. That would require protocol support.

Now let's design the Majordomo broker. Its core structure is a set of queues, one per service. We will create these queues as workers appear (we could delete them as workers disappear, but forget that for now because it gets complex). Additionally, we keep a queue of workers per service.

And here is the broker:

[mdbroker: Majordomo broker in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:mdbroker) | [C#](http://zguide.zeromq.org/cs:mdbroker) | [Go](http://zguide.zeromq.org/go:mdbroker) | [Haskell](http://zguide.zeromq.org/hs:mdbroker) | [Haxe](http://zguide.zeromq.org/hx:mdbroker) | [Java](http://zguide.zeromq.org/java:mdbroker) | [Lua](http://zguide.zeromq.org/lua:mdbroker) | [PHP](http://zguide.zeromq.org/php:mdbroker) | [Python](http://zguide.zeromq.org/py:mdbroker) | [Ruby](http://zguide.zeromq.org/rb:mdbroker) | [Tcl](http://zguide.zeromq.org/tcl:mdbroker) | [Ada | Basic | Clojure | CL | Delphi | Erlang | F# | Felix | Node.js | Objective-C | ooc | Perl | Q | Racket | Scala](http://zguide.zeromq.org/main:translate)

Here are some things to note about the broker code:

- The Majordomo Protocol lets us handle both clients and workers on a single socket. This is nicer for those deploying and managing the broker: it just sits on one ZeroMQ endpoint rather than the two that most proxies need.

- The broker implements all of MDP/0.1 properly (as far as I know), including disconnection if the broker sends invalid commands, heartbeating, and the rest.

- It can be extended to run multiple threads, each managing one socket and one set of clients and workers. This could be interesting for segmenting large architectures. The C code is already organized around a broker class to make this trivial.

- A primary/failover or live/live broker reliability model is easy, as the broker essentially has no state except service presence. It's up to clients and workers to choose another broker if their first choice isn't up and running.

- The examples use five-second heartbeats, mainly to reduce the amount of output when you enable tracing. Realistic values would be lower for most LAN applications. However, any retry has to be slow enough to allow for a service to restart, say 10 seconds at least.

We later improved and extended the protocol and the Majordomo implementation, which now sits in its own Github project. If you want a properly usable Majordomo stack, use the GitHub project.



| [Asynchronous Majordomo Pattern](http://zguide.zeromq.org/page:all#Asynchronous-Majordomo-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-99) [next](http://zguide.zeromq.org/page:all#header-101) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The Majordomo implementation in the previous section is simple and stupid. The client is just the original Simple Pirate, wrapped up in a sexy API. When I fire up a client, broker, and worker on a test box, it can process 100,000 requests in about 14 seconds. That is partially due to the code, which cheerfully copies message frames around as if CPU cycles were free. But the real problem is that we're doing network round-trips. ZeroMQ disables [Nagle's algorithm](http://en.wikipedia.org/wiki/Nagles_algorithm), but round-tripping is still slow.

Theory is great in theory, but in practice, practice is better. Let's measure the actual cost of round-tripping with a simple test program. This sends a bunch of messages, first waiting for a reply to each message, and second as a batch, reading all the replies back as a batch. Both approaches do the same work, but they give very different results. We mock up a client, broker, and worker:

[tripping: Round-trip demonstrator in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:tripping) | [C#](http://zguide.zeromq.org/cs:tripping) | [Go](http://zguide.zeromq.org/go:tripping) | [Haskell](http://zguide.zeromq.org/hs:tripping) | [Haxe](http://zguide.zeromq.org/hx:tripping) | [Java](http://zguide.zeromq.org/java:tripping) | [Lua](http://zguide.zeromq.org/lua:tripping) | [PHP](http://zguide.zeromq.org/php:tripping) | [Python](http://zguide.zeromq.org/py:tripping) | [Tcl](http://zguide.zeromq.org/tcl:tripping) | [Ada | Basic | Clojure | CL | Delphi | Erlang | F# | Felix | Node.js | Objective-C | ooc | Perl | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

```
Setting up test...
Synchronous round-trip test...
 9057 calls/second
Asynchronous round-trip test...
 173010 calls/second
```

Note that the client thread does a small pause before starting. This is to get around one of the "features" of the router socket: if you send a message with the address of a peer that's not yet connected, the message gets discarded. In this example we don't use the load balancing mechanism, so without the sleep, if the worker thread is too slow to connect, it will lose messages, making a mess of our test.

As we see, round-tripping in the simplest case is 20 times slower than the asynchronous, "shove it down the pipe as fast as it'll go" approach. Let's see if we can apply this to Majordomo to make it faster.

First, we modify the client API to send and receive in two separate methods:

mdcli_t *mdcli_new`     `(char *broker);
void`     `mdcli_destroy (mdcli_t **self_p);
int`      `mdcli_send`    `(mdcli_t *self, char *service, zmsg_t **request_p);
zmsg_t`  `*mdcli_recv`    `(mdcli_t *self);

It's literally a few minutes' work to refactor the synchronous client API to become asynchronous:

[mdcliapi2: Majordomo asynchronous client API in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:mdcliapi2) | [Haxe](http://zguide.zeromq.org/hx:mdcliapi2) | [Java](http://zguide.zeromq.org/java:mdcliapi2) | [Lua](http://zguide.zeromq.org/lua:mdcliapi2) | [PHP](http://zguide.zeromq.org/php:mdcliapi2) | [Python](http://zguide.zeromq.org/py:mdcliapi2) | [Ruby](http://zguide.zeromq.org/rb:mdcliapi2) | [Tcl](http://zguide.zeromq.org/tcl:mdcliapi2) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Node.js | Objective-C | ooc | Perl | Q | Racket | Scala](http://zguide.zeromq.org/main:translate)

- We use a DEALER socket instead of REQ, so we emulate REQ with an empty delimiter frame before each request and each response.
- We don't retry requests; if the application needs to retry, it can do this itself.
- We break the synchronous `send` method into separate `send` and `recv` methods.
- The `send` method is asynchronous and returns immediately after sending. The caller can thus send a number of messages before getting a response.
- The `recv` method waits for (with a timeout) one response and returns that to the caller.

And here's the corresponding client test program, which sends 100,000 messages and then receives 100,000 back:

[mdclient2: Majordomo client application in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:mdclient2) | [C#](http://zguide.zeromq.org/cs:mdclient2) | [Haxe](http://zguide.zeromq.org/hx:mdclient2) | [Java](http://zguide.zeromq.org/java:mdclient2) | [Lua](http://zguide.zeromq.org/lua:mdclient2) | [PHP](http://zguide.zeromq.org/php:mdclient2) | [Python](http://zguide.zeromq.org/py:mdclient2) | [Ruby](http://zguide.zeromq.org/rb:mdclient2) | [Tcl](http://zguide.zeromq.org/tcl:mdclient2) | [Ada | Basic | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Node.js | Objective-C | ooc | Perl | Q | Racket | Scala](http://zguide.zeromq.org/main:translate)

```
$ time mdclient
100000 requests/replies processed

real    0m14.088s
user    0m1.310s
sys     0m2.670s
```

And here's the asynchronous client, with a single worker:

```
$ time mdclient2
100000 replies received

real    0m8.730s
user    0m0.920s
sys     0m1.550s
```

Twice as fast. Not bad, but let's fire up 10 workers and see how it handles the traffic

```
$ time mdclient2
100000 replies received

real    0m3.863s
user    0m0.730s
sys     0m0.470s
```

It isn't fully asynchronous because workers get their messages on a strict last-used basis. But it will scale better with more workers. On my PC, after eight or so workers, it doesn't get any faster. Four cores only stretches so far. But we got a 4x improvement in throughput with just a few minutes' work. The broker is still unoptimized. It spends most of its time copying message frames around, instead of doing zero-copy, which it could. But we're getting 25K reliable request/reply calls a second, with pretty low effort.

However, the asynchronous Majordomo pattern isn't all roses. It has a fundamental weakness, namely that it cannot survive a broker crash without more work. If you look at the `mdcliapi2` code you'll see it does not attempt to reconnect after a failure. A proper reconnect would require the following:

- A number on every request and a matching number on every reply, which would ideally require a change to the protocol to enforce.
- Tracking and holding onto all outstanding requests in the client API, i.e., those for which no reply has yet been received.
- In case of failover, for the client API to *resend* all outstanding requests to the broker.

It's not a deal breaker, but it does show that performance often means complexity. Is this worth doing for Majordomo? It depends on your use case. For a name lookup service you call once per session, no. For a web frontend serving thousands of clients, probably yes.



| [Service Discovery](http://zguide.zeromq.org/page:all#Service-Discovery) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-100) [next](http://zguide.zeromq.org/page:all#header-102) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

So, we have a nice service-oriented broker, but we have no way of knowing whether a particular service is available or not. We know whether a request failed, but we don't know why. It is useful to be able to ask the broker, "is the echo service running?" The most obvious way would be to modify our MDP/Client protocol to add commands to ask this. But MDP/Client has the great charm of being simple. Adding service discovery to it would make it as complex as the MDP/Worker protocol.

Another option is to do what email does, and ask that undeliverable requests be returned. This can work well in an asynchronous world, but it also adds complexity. We need ways to distinguish returned requests from replies and to handle these properly.

Let's try to use what we've already built, building on top of MDP instead of modifying it. Service discovery is, itself, a service. It might indeed be one of several management services, such as "disable service X", "provide statistics", and so on. What we want is a general, extensible solution that doesn't affect the protocol or existing applications.

So here's a small RFC that layers this on top of MDP: [the Majordomo Management Interface (MMI)](http://rfc.zeromq.org/spec:8). We already implemented it in the broker, though unless you read the whole thing you probably missed that. I'll explain how it works in the broker:

- When a client requests a service that starts with `mmi.`, instead of routing this to a worker, we handle it internally.

- We handle just one service in this broker, which is `mmi.service`, the service discovery service.

- The payload for the request is the name of an external service (a real one, provided by a worker).

- The broker returns "200" (OK) or "404" (Not found), depending on whether there are workers registered for that service or not.

Here's how we use the service discovery in an application:

[mmiecho: Service discovery over Majordomo in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:mmiecho) | [Go](http://zguide.zeromq.org/go:mmiecho) | [Haxe](http://zguide.zeromq.org/hx:mmiecho) | [Java](http://zguide.zeromq.org/java:mmiecho) | [Lua](http://zguide.zeromq.org/lua:mmiecho) | [PHP](http://zguide.zeromq.org/php:mmiecho) | [Python](http://zguide.zeromq.org/py:mmiecho) | [Ruby](http://zguide.zeromq.org/rb:mmiecho) | [Tcl](http://zguide.zeromq.org/tcl:mmiecho) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Haskell | Node.js | Objective-C | ooc | Perl | Q | Racket | Scala](http://zguide.zeromq.org/main:translate)



| [Idempotent Services](http://zguide.zeromq.org/page:all#Idempotent-Services) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-101) [next](http://zguide.zeromq.org/page:all#header-103) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Idempotency is not something you take a pill for. What it means is that it's safe to repeat an operation. Checking the clock is idempotent. Lending ones credit card to ones children is not. While many client-to-server use cases are idempotent, some are not. Examples of idempotent use cases include:

- Stateless task distribution, i.e., a pipeline where the servers are stateless workers that compute a reply based purely on the state provided by a request. In such a case, it's safe (though inefficient) to execute the same request many times.

- A name service that translates logical addresses into endpoints to bind or connect to. In such a case, it's safe to make the same lookup request many times.

And here are examples of a non-idempotent use cases:

- A logging service. One does not want the same log information recorded more than once.

- Any service that has impact on downstream nodes, e.g., sends on information to other nodes. If that service gets the same request more than once, downstream nodes will get duplicate information.

- Any service that modifies shared data in some non-idempotent way; e.g., a service that debits a bank account is not idempotent without extra work.

When our server applications are not idempotent, we have to think more carefully about when exactly they might crash. If an application dies when it's idle, or while it's processing a request, that's usually fine. We can use database transactions to make sure a debit and a credit are always done together, if at all. If the server dies while sending its reply, that's a problem, because as far as it's concerned, it has done its work.

If the network dies just as the reply is making its way back to the client, the same problem arises. The client will think the server died and will resend the request, and the server will do the same work twice, which is not what we want.

To handle non-idempotent operations, use the fairly standard solution of detecting and rejecting duplicate requests. This means:

- The client must stamp every request with a unique client identifier and a unique message number.

- The server, before sending back a reply, stores it using the combination of client ID and message number as a key.

- The server, when getting a request from a given client, first checks whether it has a reply for that client ID and message number. If so, it does not process the request, but just resends the reply.



| [Disconnected Reliability (Titanic Pattern)](http://zguide.zeromq.org/page:all#Disconnected-Reliability-Titanic-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-102) [next](http://zguide.zeromq.org/page:all#header-104) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Once you realize that Majordomo is a "reliable" message broker, you might be tempted to add some spinning rust (that is, ferrous-based hard disk platters). After all, this works for all the enterprise messaging systems. It's such a tempting idea that it's a little sad to have to be negative toward it. But brutal cynicism is one of my specialties. So, some reasons you don't want rust-based brokers sitting in the center of your architecture are:

- As you've seen, the Lazy Pirate client performs surprisingly well. It works across a whole range of architectures, from direct client-to-server to distributed queue proxies. It does tend to assume that workers are stateless and idempotent. But we can work around that limitation without resorting to rust.

- Rust brings a whole set of problems, from slow performance to additional pieces that you have to manage, repair, and handle 6 a.m. panics from, as they inevitably break at the start of daily operations. The beauty of the Pirate patterns in general is their simplicity. They won't crash. And if you're still worried about the hardware, you can move to a peer-to-peer pattern that has no broker at all. I'll explain later in this chapter.

Having said this, however, there is one sane use case for rust-based reliability, which is an asynchronous disconnected network. It solves a major problem with Pirate, namely that a client has to wait for an answer in real time. If clients and workers are only sporadically connected (think of email as an analogy), we can't use a stateless network between clients and workers. We have to put state in the middle.

So, here's the Titanic pattern, in which we write messages to disk to ensure they never get lost, no matter how sporadically clients and workers are connected. As we did for service discovery, we're going to layer Titanic on top of MDP rather than extend it. It's wonderfully lazy because it means we can implement our fire-and-forget reliability in a specialized worker, rather than in the broker. This is excellent for several reasons:

- It is *much* easier because we divide and conquer: the broker handles message routing and the worker handles reliability.
- It lets us mix brokers written in one language with workers written in another.
- It lets us evolve the fire-and-forget technology independently.

The only downside is that there's an extra network hop between broker and hard disk. The benefits are easily worth it.

There are many ways to make a persistent request-reply architecture. We'll aim for one that is simple and painless. The simplest design I could come up with, after playing with this for a few hours, is a "proxy service". That is, Titanic doesn't affect workers at all. If a client wants a reply immediately, it talks directly to a service and hopes the service is available. If a client is happy to wait a while, it talks to Titanic instead and asks, "hey, buddy, would you take care of this for me while I go buy my groceries?"

**Figure 51 - The Titanic Pattern**

![fig51.png](https://github.com/imatix/zguide/raw/master/images/fig51.png)

Titanic is thus both a worker and a client. The dialog between client and Titanic goes along these lines:

- Client: Please accept this request for me. Titanic: OK, done.
- Client: Do you have a reply for me? Titanic: Yes, here it is. Or, no, not yet.
- Client: OK, you can wipe that request now, I'm happy. Titanic: OK, done.

Whereas the dialog between Titanic and broker and worker goes like this:

- Titanic: Hey, Broker, is there an coffee service? Broker: Uhm, Yeah, seems like.
- Titanic: Hey, coffee service, please handle this for me.
- Coffee: Sure, here you are.
- Titanic: Sweeeeet!

You can work through this and the possible failure scenarios. If a worker crashes while processing a request, Titanic retries indefinitely. If a reply gets lost somewhere, Titanic will retry. If the request gets processed but the client doesn't get the reply, it will ask again. If Titanic crashes while processing a request or a reply, the client will try again. As long as requests are fully committed to safe storage, work can't get lost.

The handshaking is pedantic, but can be pipelined, i.e., clients can use the asynchronous Majordomo pattern to do a lot of work and then get the responses later.

We need some way for a client to request *its* replies. We'll have many clients asking for the same services, and clients disappear and reappear with different identities. Here is a simple, reasonably secure solution:

- Every request generates a universally unique ID (UUID), which Titanic returns to the client after it has queued the request.
- When a client asks for a reply, it must specify the UUID for the original request.

In a realistic case, the client would want to store its request UUIDs safely, e.g., in a local database.

Before we jump off and write yet another formal specification (fun, fun!), let's consider how the client talks to Titanic. One way is to use a single service and send it three different request types. Another way, which seems simpler, is to use three services:

- `titanic.request`: store a request message, and return a UUID for the request.
- `titanic.reply`: fetch a reply, if available, for a given request UUID.
- `titanic.close`: confirm that a reply has been stored and processed.

We'll just make a multithreaded worker, which as we've seen from our multithreading experience with ZeroMQ, is trivial. However, let's first sketch what Titanic would look like in terms of ZeroMQ messages and frames. This gives us the [Titanic Service Protocol (TSP)](http://rfc.zeromq.org/spec:9).

Using TSP is clearly more work for client applications than accessing a service directly via MDP. Here's the shortest robust "echo" client example:

[ticlient: Titanic client example in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:ticlient) | [Haxe](http://zguide.zeromq.org/hx:ticlient) | [Java](http://zguide.zeromq.org/java:ticlient) | [PHP](http://zguide.zeromq.org/php:ticlient) | [Python](http://zguide.zeromq.org/py:ticlient) | [Ruby](http://zguide.zeromq.org/rb:ticlient) | [Tcl](http://zguide.zeromq.org/tcl:ticlient) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Lua | Node.js | Objective-C | ooc | Perl | Q | Racket | Scala](http://zguide.zeromq.org/main:translate)

For example, this client blocks on each request whereas in a real application, we'd want to be doing useful work while tasks are executed. This requires some nontrivial plumbing to build a background thread and talk to that cleanly. It's the kind of thing you want to wrap in a nice simple API that the average developer cannot misuse. It's the same approach that we used for Majordomo.

Here's the Titanic implementation. This server handles the three services using three threads, as proposed. It does full persistence to disk using the most brutal approach possible: one file per message. It's so simple, it's scary. The only complex part is that it keeps a separate queue of all requests, to avoid reading the directory over and over:

[titanic: Titanic broker example in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:titanic) | [Haxe](http://zguide.zeromq.org/hx:titanic) | [Java](http://zguide.zeromq.org/java:titanic) | [PHP](http://zguide.zeromq.org/php:titanic) | [Python](http://zguide.zeromq.org/py:titanic) | [Ruby](http://zguide.zeromq.org/rb:titanic) | [Tcl](http://zguide.zeromq.org/tcl:titanic) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Lua | Node.js | Objective-C | ooc | Perl | Q | Racket | Scala](http://zguide.zeromq.org/main:translate)

Some notes about this code:

- Note that some loops start by sending, others by receiving messages. This is because Titanic acts both as a client and a worker in different roles.
- The Titanic broker uses the MMI service discovery protocol to send requests only to services that appear to be running. Since the MMI implementation in our little Majordomo broker is quite poor, this won't work all the time.
- We use an inproc connection to send new request data from the `titanic.request`service through to the main dispatcher. This saves the dispatcher from having to scan the disk directory, load all request files, and sort them by date/time.

The important thing about this example is not performance (which, although I haven't tested it, is surely terrible), but how well it implements the reliability contract. To try it, start the mdbroker and titanic programs. Then start the ticlient, and then start the mdworker echo service. You can run all four of these using the `-v` option to do verbose activity tracing. You can stop and restart any piece *except the client* and nothing will get lost.

If you want to use Titanic in real cases, you'll rapidly be asking "how do we make this faster?"

Here's what I'd do, starting with the example implementation:

- Use a single disk file for all data, rather than multiple files. Operating systems are usually better at handling a few large files than many smaller ones.
- Organize that disk file as a circular buffer so that new requests can be written contiguously (with very occasional wraparound). One thread, writing full speed to a disk file, can work rapidly.
- Keep the index in memory and rebuild the index at startup time, from the disk buffer. This saves the extra disk head flutter needed to keep the index fully safe on disk. You would want an fsync after every message, or every N milliseconds if you were prepared to lose the last M messages in case of a system failure.
- Use a solid-state drive rather than spinning iron oxide platters.
- Pre-allocate the entire file, or allocate it in large chunks, which allows the circular buffer to grow and shrink as needed. This avoids fragmentation and ensures that most reads and writes are contiguous.

And so on. What I'd not recommend is storing messages in a database, not even a "fast" key/value store, unless you really like a specific database and don't have performance worries. You will pay a steep price for the abstraction, ten to a thousand times over a raw disk file.

If you want to make Titanic *even more reliable*, duplicate the requests to a second server, which you'd place in a second location just far away enough to survive a nuclear attack on your primary location, yet not so far that you get too much latency.

If you want to make Titanic *much faster and less reliable*, store requests and replies purely in memory. This will give you the functionality of a disconnected network, but requests won't survive a crash of the Titanic server itself.



| [High-Availability Pair (Binary Star Pattern)](http://zguide.zeromq.org/page:all#High-Availability-Pair-Binary-Star-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-103) [next](http://zguide.zeromq.org/page:all#header-105) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

**Figure 52 - High-Availability Pair, Normal Operation**

![fig52.png](https://github.com/imatix/zguide/raw/master/images/fig52.png)

The Binary Star pattern puts two servers in a primary-backup high-availability pair. At any given time, one of these (the active) accepts connections from client applications. The other (the passive) does nothing, but the two servers monitor each other. If the active disappears from the network, after a certain time the passive takes over as active.

We developed the Binary Star pattern at iMatix for our [OpenAMQ server](http://www.openamq.org/). We designed it:

- To provide a straightforward high-availability solution.
- To be simple enough to actually understand and use.
- To fail over reliably when needed, and only when needed.

Assuming we have a Binary Star pair running, here are the different scenarios that will result in a failover:

- The hardware running the primary server has a fatal problem (power supply explodes, machine catches fire, or someone simply unplugs it by mistake), and disappears. Applications see this, and reconnect to the backup server.
- The network segment on which the primary server sits crashes—perhaps a router gets hit by a power spike—and applications start to reconnect to the backup server.
- The primary server crashes or is killed by the operator and does not restart automatically.

**Figure 53 - High-availability Pair During Failover**

![fig53.png](https://github.com/imatix/zguide/raw/master/images/fig53.png)

Recovery from failover works as follows:

- The operators restart the primary server and fix whatever problems were causing it to disappear from the network.
- The operators stop the backup server at a moment when it will cause minimal disruption to applications.
- When applications have reconnected to the primary server, the operators restart the backup server.

Recovery (to using the primary server as active) is a manual operation. Painful experience teaches us that automatic recovery is undesirable. There are several reasons:

- Failover creates an interruption of service to applications, possibly lasting 10-30 seconds. If there is a real emergency, this is much better than total outage. But if recovery creates a further 10-30 second outage, it is better that this happens off-peak, when users have gone off the network.

- When there is an emergency, the absolute first priority is certainty for those trying to fix things. Automatic recovery creates uncertainty for system administrators, who can no longer be sure which server is in charge without double-checking.

- Automatic recovery can create situations where networks fail over and then recover, placing operators in the difficult position of analyzing what happened. There was an interruption of service, but the cause isn't clear.

Having said this, the Binary Star pattern will fail back to the primary server if this is running (again) and the backup server fails. In fact, this is how we provoke recovery.

The shutdown process for a Binary Star pair is to either:

1. Stop the passive server and then stop the active server at any later time, or
2. Stop both servers in any order but within a few seconds of each other.

Stopping the active and then the passive server with any delay longer than the failover timeout will cause applications to disconnect, then reconnect, and then disconnect again, which may disturb users.



| [Detailed Requirements](http://zguide.zeromq.org/page:all#Detailed-Requirements) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-104) [next](http://zguide.zeromq.org/page:all#header-106) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Binary Star is as simple as it can be, while still working accurately. In fact, the current design is the third complete redesign. Each of the previous designs we found to be too complex, trying to do too much, and we stripped out functionality until we came to a design that was understandable, easy to use, and reliable enough to be worth using.

These are our requirements for a high-availability architecture:

- The failover is meant to provide insurance against catastrophic system failures, such as hardware breakdown, fire, accident, and so on. There are simpler ways to recover from ordinary server crashes and we already covered these.

- Failover time should be under 60 seconds and preferably under 10 seconds.

- Failover has to happen automatically, whereas recovery must happen manually. We want applications to switch over to the backup server automatically, but we do not want them to switch back to the primary server except when the operators have fixed whatever problem there was and decided that it is a good time to interrupt applications again.

- The semantics for client applications should be simple and easy for developers to understand. Ideally, they should be hidden in the client API.

- There should be clear instructions for network architects on how to avoid designs that could lead to *split brain syndrome*, in which both servers in a Binary Star pair think they are the active server.

- There should be no dependencies on the order in which the two servers are started.

- It must be possible to make planned stops and restarts of either server without stopping client applications (though they may be forced to reconnect).

- Operators must be able to monitor both servers at all times.

- It must be possible to connect the two servers using a high-speed dedicated network connection. That is, failover synchronization must be able to use a specific IP route.

We make the following assumptions:

- A single backup server provides enough insurance; we don't need multiple levels of backup.

- The primary and backup servers are equally capable of carrying the application load. We do not attempt to balance load across the servers.

- There is sufficient budget to cover a fully redundant backup server that does nothing almost all the time.

We don't attempt to cover the following:

- The use of an active backup server or load balancing. In a Binary Star pair, the backup server is inactive and does no useful work until the primary server goes offline.

- The handling of persistent messages or transactions in any way. We assume the existence of a network of unreliable (and probably untrusted) servers or Binary Star pairs.

- Any automatic exploration of the network. The Binary Star pair is manually and explicitly defined in the network and is known to applications (at least in their configuration data).

- Replication of state or messages between servers. All server-side state must be recreated by applications when they fail over.

Here is the key terminology that we use in Binary Star:

- *Primary*: the server that is normally or initially active.

- *Backup*: the server that is normally passive. It will become active if and when the primary server disappears from the network, and when client applications ask the backup server to connect.

- *Active*: the server that accepts client connections. There is at most one active server.

- *Passive*: the server that takes over if the active disappears. Note that when a Binary Star pair is running normally, the primary server is active, and the backup is passive. When a failover has happened, the roles are switched.

To configure a Binary Star pair, you need to:

1. Tell the primary server where the backup server is located.
2. Tell the backup server where the primary server is located.
3. Optionally, tune the failover response times, which must be the same for both servers.

The main tuning concern is how frequently you want the servers to check their peering status, and how quickly you want to activate failover. In our example, the failover timeout value defaults to 2,000 msec. If you reduce this, the backup server will take over as active more rapidly but may take over in cases where the primary server could recover. For example, you may have wrapped the primary server in a shell script that restarts it if it crashes. In that case, the timeout should be higher than the time needed to restart the primary server.

For client applications to work properly with a Binary Star pair, they must:

1. Know both server addresses.
2. Try to connect to the primary server, and if that fails, to the backup server.
3. Detect a failed connection, typically using heartbeating.
4. Try to reconnect to the primary, and then backup (in that order), with a delay between retries that is at least as high as the server failover timeout.
5. Recreate all of the state they require on a server.
6. Retransmit messages lost during a failover, if messages need to be reliable.

It's not trivial work, and we'd usually wrap this in an API that hides it from real end-user applications.

These are the main limitations of the Binary Star pattern:

- A server process cannot be part of more than one Binary Star pair.
- A primary server can have a single backup server, and no more.
- The passive server does no useful work, and is thus wasted.
- The backup server must be capable of handling full application loads.
- Failover configuration cannot be modified at runtime.
- Client applications must do some work to benefit from failover.



| [Preventing Split-Brain Syndrome](http://zguide.zeromq.org/page:all#Preventing-Split-Brain-Syndrome) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-105) [next](http://zguide.zeromq.org/page:all#header-107) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*Split-brain syndrome* occurs when different parts of a cluster think they are active at the same time. It causes applications to stop seeing each other. Binary Star has an algorithm for detecting and eliminating split brain, which is based on a three-way decision mechanism (a server will not decide to become active until it gets application connection requests and it cannot see its peer server).

However, it is still possible to (mis)design a network to fool this algorithm. A typical scenario would be a Binary Star pair, that is distributed between two buildings, where each building also had a set of applications and where there was a single network link between both buildings. Breaking this link would create two sets of client applications, each with half of the Binary Star pair, and each failover server would become active.

To prevent split-brain situations, we must connect a Binary Star pair using a dedicated network link, which can be as simple as plugging them both into the same switch or, better, using a crossover cable directly between two machines.

We must not split a Binary Star architecture into two islands, each with a set of applications. While this may be a common type of network architecture, you should use federation, not high-availability failover, in such cases.

A suitably paranoid network configuration would use two private cluster interconnects, rather than a single one. Further, the network cards used for the cluster would be different from those used for message traffic, and possibly even on different paths on the server hardware. The goal is to separate possible failures in the network from possible failures in the cluster. Network ports can have a relatively high failure rate.



| [Binary Star Implementation](http://zguide.zeromq.org/page:all#Binary-Star-Implementation) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-106) [next](http://zguide.zeromq.org/page:all#header-108) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Without further ado, here is a proof-of-concept implementation of the Binary Star server. The primary and backup servers run the same code, you choose their roles when you run the code:

[bstarsrv: Binary Star server in C](javascript:;)


[Haxe](http://zguide.zeromq.org/hx:bstarsrv) | [Java](http://zguide.zeromq.org/java:bstarsrv) | [Python](http://zguide.zeromq.org/py:bstarsrv) | [Ruby](http://zguide.zeromq.org/rb:bstarsrv) | [Tcl](http://zguide.zeromq.org/tcl:bstarsrv) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Scala](http://zguide.zeromq.org/main:translate)

[bstarcli: Binary Star client in C](javascript:;)


[Haxe](http://zguide.zeromq.org/hx:bstarcli) | [Java](http://zguide.zeromq.org/java:bstarcli) | [Python](http://zguide.zeromq.org/py:bstarcli) | [Ruby](http://zguide.zeromq.org/rb:bstarcli) | [Tcl](http://zguide.zeromq.org/tcl:bstarcli) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Scala](http://zguide.zeromq.org/main:translate)

```
bstarsrv -p     # Start primary
bstarsrv -b     # Start backup
bstarcli
```

You can then provoke failover by killing the primary server, and recovery by restarting the primary and killing the backup. Note how it's the client vote that triggers failover, and recovery.

Binary star is driven by a finite state machine. Events are the peer state, so "Peer Active" means the other server has told us it's active. "Client Request" means we've received a client request. "Client Vote" means we've received a client request AND our peer is inactive for two heartbeats.

Note that the servers use PUB-SUB sockets for state exchange. No other socket combination will work here. PUSH and DEALER block if there is no peer ready to receive a message. PAIR does not reconnect if the peer disappears and comes back. ROUTER needs the address of the peer before it can send it a message.

**Figure 54 - Binary Star Finite State Machine**

![fig54.png](https://github.com/imatix/zguide/raw/master/images/fig54.png)



| [Binary Star Reactor](http://zguide.zeromq.org/page:all#Binary-Star-Reactor) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-107) [next](http://zguide.zeromq.org/page:all#header-109) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Binary Star is useful and generic enough to package up as a reusable reactor class. The reactor then runs and calls our code whenever it has a message to process. This is much nicer than copying/pasting the Binary Star code into each server where we want that capability.

In C, we wrap the CZMQ `zloop` class that we saw before. `zloop` lets you register handlers to react on socket and timer events. In the Binary Star reactor, we provide handlers for voters and for state changes (active to passive, and vice versa). Here is the `bstar` API:

//`  `bstar class - Binary Star reactor

\#include "bstar.h"

*//  States we can be in at any point in time*
**typedef** **enum** {
`    `STATE_PRIMARY = 1,`          `*//  Primary, waiting for peer to connect*
`    `STATE_BACKUP = 2,`           `*//  Backup, waiting for peer to connect*
`    `STATE_ACTIVE = 3,`           `*//  Active - accepting connections*
`    `STATE_PASSIVE = 4`           `*//  Passive - not accepting connections*
} state_t;

*//  Events, which start with the states our peer can be in*
**typedef** **enum** {
`    `PEER_PRIMARY = 1,`           `*//  HA peer is pending primary*
`    `PEER_BACKUP = 2,`            `*//  HA peer is pending backup*
`    `PEER_ACTIVE = 3,`            `*//  HA peer is active*
`    `PEER_PASSIVE = 4,`           `*//  HA peer is passive*
`    `CLIENT_REQUEST = 5`          `*//  Client makes request*
} event_t;

*//  Structure of our class*

**struct** _bstar_t {
`    `zctx_t *ctx;`                `*//  Our private context*
`    `zloop_t *loop;`              `*//  Reactor loop*
`    `void *statepub;`             `*//  State publisher*
`    `void *statesub;`             `*//  State subscriber*
`    `state_t state;`              `*//  Current state*
`    `event_t event;`              `*//  Current event*
`    `int64_t peer_expiry;`        `*//  When peer is considered 'dead'*
`    `zloop_fn *voter_fn;`         `*//  Voting socket handler*
`    `void *voter_arg;`            `*//  Arguments for voting handler*
`    `zloop_fn *active_fn;`        `*//  Call when become active*
`    `void *active_arg;`           `*//  Arguments for handler*
`    `zloop_fn *passive_fn;`         `*//  Call when become passive*
`    `void *passive_arg;`            `*//  Arguments for handler*
};

//`  `The finite-state machine is the same as in the proof-of-concept server.
//`  `To understand this reactor in detail, first read the CZMQ zloop class.

//`  `We send state information every this often
//`  `If peer doesn't respond in two heartbeats, it is 'dead'
\#define BSTAR_HEARTBEAT`     `1000`        `*//  In msecs*

*//`  `Binary Star finite state machine (applies event to state)//`  `Returns -1 if there was an exception, 0 if event was valid.*

**static** int
s_execute_fsm (bstar_t *self)
{
`    `int rc = 0;
`    `*//  Primary server is waiting for peer to connect*
`    `*//  Accepts CLIENT_REQUEST events in this state*
`    `**if** (self->state == STATE_PRIMARY) {
`        `**if** (self->event == PEER_BACKUP) {
`            `zclock_log ("I: connected to backup (passive), ready as active");
`            `self->state = STATE_ACTIVE;
`            `**if** (self->active_fn)
`                `(self->active_fn) (self->loop, NULL, self->active_arg);
`        }`
`        `**else**
`        `**if** (self->event == PEER_ACTIVE) {
`            `zclock_log ("I: connected to backup (active), ready as passive");
`            `self->state = STATE_PASSIVE;
`            `**if** (self->passive_fn)
`                `(self->passive_fn) (self->loop, NULL, self->passive_arg);
`        }`
`        `**else**
`        `**if** (self->event == CLIENT_REQUEST) {
`            `*// Allow client requests to turn us into the active if we've*
`            `*// waited sufficiently long to believe the backup is not*
`            `*// currently acting as active (i.e., after a failover)*
`            `assert (self->peer_expiry > 0);
`            `**if** (zclock_time () >= self->peer_expiry) {
`                `zclock_log ("I: request from client, ready as active");
`                `self->state = STATE_ACTIVE;
`                `**if** (self->active_fn)
`                    `(self->active_fn) (self->loop, NULL, self->active_arg);
`            }` **else**
`                `*// Don't respond to clients yet - it's possible we're*
`                `*// performing a failback and the backup is currently active*
`                `rc = -1;
`        }`
`    }`
`    `**else**
`    `*//  Backup server is waiting for peer to connect*
`    `*//  Rejects CLIENT_REQUEST events in this state*
`    `**if** (self->state == STATE_BACKUP) {
`        `**if** (self->event == PEER_ACTIVE) {
`            `zclock_log ("I: connected to primary (active), ready as passive");
`            `self->state = STATE_PASSIVE;
`            `**if** (self->passive_fn)
`                `(self->passive_fn) (self->loop, NULL, self->passive_arg);
`        }`
`        `**else**
`        `**if** (self->event == CLIENT_REQUEST)
`            `rc = -1;
`    }`
`    `**else**
`    `*//  Server is active*
`    `*//  Accepts CLIENT_REQUEST events in this state*
`    `*//  The only way out of ACTIVE is death*
`    `**if** (self->state == STATE_ACTIVE) {
`        `**if** (self->event == PEER_ACTIVE) {
`            `*//  Two actives would mean split-brain*
`            `zclock_log ("E: fatal error - dual actives, aborting");
`            `rc = -1;
`        }`
`    }`
`    `**else**
`    `*//  Server is passive*
`    `*//  CLIENT_REQUEST events can trigger failover if peer looks dead*
`    `**if** (self->state == STATE_PASSIVE) {
`        `**if** (self->event == PEER_PRIMARY) {
`            `*//  Peer is restarting - become active, peer will go passive*
`            `zclock_log ("I: primary (passive) is restarting, ready as active");
`            `self->state = STATE_ACTIVE;
`        }`
`        `**else**
`        `**if** (self->event == PEER_BACKUP) {
`            `*//  Peer is restarting - become active, peer will go passive*
`            `zclock_log ("I: backup (passive) is restarting, ready as active");
`            `self->state = STATE_ACTIVE;
`        }`
`        `**else**
`        `**if** (self->event == PEER_PASSIVE) {
`            `*//  Two passives would mean cluster would be non-responsive*
`            `zclock_log ("E: fatal error - dual passives, aborting");
`            `rc = -1;
`        }`
`        `**else**
`        `**if** (self->event == CLIENT_REQUEST) {
`            `*//  Peer becomes active if timeout has passed*
`            `*//  It's the client request that triggers the failover*
`            `assert (self->peer_expiry > 0);
`            `**if** (zclock_time () >= self->peer_expiry) {
`                `*//  If peer is dead, switch to the active state*
`                `zclock_log ("I: failover successful, ready as active");
`                `self->state = STATE_ACTIVE;
`            }`
`            `**else**
`                `*//  If peer is alive, reject connections*
`                `rc = -1;
`        }`
`        `*//  Call state change handler if necessary*
`        `**if** (self->state == STATE_ACTIVE && self->active_fn)
`            `(self->active_fn) (self->loop, NULL, self->active_arg);
`    }`
`    `**return** rc;
}

**static** void
s_update_peer_expiry (bstar_t *self)
{
`    `self->peer_expiry = zclock_time () + 2 * BSTAR_HEARTBEAT;
}

*//  Reactor event handlers…*

*//  Publish our state to peer*
int s_send_state (zloop_t *loop, int timer_id, void *arg)
{
`    `bstar_t *self = (bstar_t *) arg;
`    `zstr_sendf (self->statepub, "%d", self->state);
`    `**return** 0;
}

*//  Receive state from peer, execute finite state machine*
int s_recv_state (zloop_t *loop, zmq_pollitem_t *poller, void *arg)
{
`    `bstar_t *self = (bstar_t *) arg;
`    `char *state = zstr_recv (poller->socket);
`    `**if** (state) {
`        `self->event = atoi (state);
`        `s_update_peer_expiry (self);
`        `free (state);
`    }`
`    `**return** s_execute_fsm (self);
}

*//  Application wants to speak to us, see if it's possible*
int s_voter_ready (zloop_t *loop, zmq_pollitem_t *poller, void *arg)
{
`    `bstar_t *self = (bstar_t *) arg;
`    `*//  If server can accept input now, call appl handler*
`    `self->event = CLIENT_REQUEST;
`    `**if** (s_execute_fsm (self) == 0)
`        `(self->voter_fn) (self->loop, poller, self->voter_arg);
`    `**else** {
`        `*//  Destroy waiting message, no-one to read it*
`        `zmsg_t *msg = zmsg_recv (poller->socket);
`        `zmsg_destroy (&msg);
`    }`
`    `**return** 0;
}

*//`  `This is the constructor for our `bstar` class. We have to tell it//`  `whether we're primary or backup server, as well as our local and//`  `remote endpoints to bind and connect to:*

bstar_t *
bstar_new (int primary, char *local, char *remote)
{
`    `bstar_t
`        `*self;

`    `self = (bstar_t *) zmalloc (**sizeof** (bstar_t));

`    `*//  Initialize the Binary Star*
`    `self->ctx = zctx_new ();
`    `self->loop = zloop_new ();
`    `self->state = primary? STATE_PRIMARY: STATE_BACKUP;

`    `*//  Create publisher for state going to peer*
`    `self->statepub = zsocket_new (self->ctx, ZMQ_PUB);
`    `zsocket_bind (self->statepub, local);

`    `*//  Create subscriber for state coming from peer*
`    `self->statesub = zsocket_new (self->ctx, ZMQ_SUB);
`    `zsocket_set_subscribe (self->statesub, "");
`    `zsocket_connect (self->statesub, remote);

`    `*//  Set-up basic reactor events*
`    `zloop_timer (self->loop, BSTAR_HEARTBEAT, 0, s_send_state, self);
`    `zmq_pollitem_t poller = { self->statesub, 0, ZMQ_POLLIN };
`    `zloop_poller (self->loop, &poller, s_recv_state, self);
`    `**return** self;
}

*//  The destructor shuts down the bstar reactor:*

void
bstar_destroy (bstar_t **self_p)
{
`    `assert (self_p);
`    `**if** (*self_p) {
`        `bstar_t *self = *self_p;
`        `zloop_destroy (&self->loop);
`        `zctx_destroy (&self->ctx);
`        `free (self);
`        `*self_p = NULL;
`    }`
}

*//`  `This method returns the underlying zloop reactor, so we can add//`  `additional timers and readers:*

zloop_t *
bstar_zloop (bstar_t *self)
{
`    `**return** self->loop;
}

*//`  `This method registers a client voter socket. Messages received//`  `on this socket provide the CLIENT_REQUEST events for the Binary Star//`  `FSM and are passed to the provided application handler. We require//`  `exactly one voter per `bstar` instance:*

int
bstar_voter (bstar_t *self, char *endpoint, int type, zloop_fn handler,
`             `void *arg)
{
`    `*//  Hold actual handler+arg so we can call this later*
`    `void *socket = zsocket_new (self->ctx, type);
`    `zsocket_bind (socket, endpoint);
`    `assert (!self->voter_fn);
`    `self->voter_fn = handler;
`    `self->voter_arg = arg;
`    `zmq_pollitem_t poller = { socket, 0, ZMQ_POLLIN };
`    `**return** zloop_poller (self->loop, &poller, s_voter_ready, self);
}

*//  Register handlers to be called each time there's a state change:*

void
bstar_new_active (bstar_t *self, zloop_fn handler, void *arg)
{
`    `assert (!self->active_fn);
`    `self->active_fn = handler;
`    `self->active_arg = arg;
}

void
bstar_new_passive (bstar_t *self, zloop_fn handler, void *arg)
{
`    `assert (!self->passive_fn);
`    `self->passive_fn = handler;
`    `self->passive_arg = arg;
}

*//  Enable/disable verbose tracing, for debugging:*

void bstar_set_verbose (bstar_t *self, bool verbose)
{
`    `zloop_set_verbose (self->loop, verbose);
}

*//`  `Finally, start the configured reactor. It will end if any handler//`  `returns -1 to the reactor, or if the process receives SIGINT or SIGTERM:*

int
bstar_start (bstar_t *self)
{
`    `assert (self->voter_fn);
`    `s_update_peer_expiry (self);
`    `**return** zloop_start (self->loop);
}

And here is the class implementation:

[bstar: Binary Star core class in C](javascript:;)


[Haxe](http://zguide.zeromq.org/hx:bstar) | [Java](http://zguide.zeromq.org/java:bstar) | [Python](http://zguide.zeromq.org/py:bstar) | [Tcl](http://zguide.zeromq.org/tcl:bstar) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

[bstarsrv2: Binary Star server, using core class in C](javascript:;)


[Haxe](http://zguide.zeromq.org/hx:bstarsrv2) | [Java](http://zguide.zeromq.org/java:bstarsrv2) | [Python](http://zguide.zeromq.org/py:bstarsrv2) | [Tcl](http://zguide.zeromq.org/tcl:bstarsrv2) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

| [Brokerless Reliability (Freelance Pattern)](http://zguide.zeromq.org/page:all#Brokerless-Reliability-Freelance-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-108) [next](http://zguide.zeromq.org/page:all#header-110) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

It might seem ironic to focus so much on broker-based reliability, when we often explain ZeroMQ as "brokerless messaging". However, in messaging, as in real life, the middleman is both a burden and a benefit. In practice, most messaging architectures benefit from a mix of distributed and brokered messaging. You get the best results when you can decide freely what trade-offs you want to make. This is why I can drive twenty minutes to a wholesaler to buy five cases of wine for a party, but I can also walk ten minutes to a corner store to buy one bottle for a dinner. Our highly context-sensitive relative valuations of time, energy, and cost are essential to the real world economy. And they are essential to an optimal message-based architecture.

This is why ZeroMQ does not *impose* a broker-centric architecture, though it does give you the tools to build brokers, aka *proxies*, and we've built a dozen or so different ones so far, just for practice.

So we'll end this chapter by deconstructing the broker-based reliability we've built so far, and turning it back into a distributed peer-to-peer architecture I call the Freelance pattern. Our use case will be a name resolution service. This is a common problem with ZeroMQ architectures: how do we know the endpoint to connect to? Hard-coding TCP/IP addresses in code is insanely fragile. Using configuration files creates an administration nightmare. Imagine if you had to hand-configure your web browser, on every PC or mobile phone you used, to realize that "google.com" was "74.125.230.82".

A ZeroMQ name service (and we'll make a simple implementation) must do the following:

- Resolve a logical name into at least a bind endpoint, and a connect endpoint. A realistic name service would provide multiple bind endpoints, and possibly multiple connect endpoints as well.

- Allow us to manage multiple parallel environments, e.g., "test" versus "production", without modifying code.

- Be reliable, because if it is unavailable, applications won't be able to connect to the network.

Putting a name service behind a service-oriented Majordomo broker is clever from some points of view. However, it's simpler and much less surprising to just expose the name service as a server to which clients can connect directly. If we do this right, the name service becomes the *only* global network endpoint we need to hard-code in our code or configuration files.

**Figure 55 - The Freelance Pattern**

![fig55.png](https://github.com/imatix/zguide/raw/master/images/fig55.png)

The types of failure we aim to handle are server crashes and restarts, server busy looping, server overload, and network issues. To get reliability, we'll create a pool of name servers so if one crashes or goes away, clients can connect to another, and so on. In practice, two would be enough. But for the example, we'll assume the pool can be any size.

In this architecture, a large set of clients connect to a small set of servers directly. The servers bind to their respective addresses. It's fundamentally different from a broker-based approach like Majordomo, where workers connect to the broker. Clients have a couple of options:

- Use REQ sockets and the Lazy Pirate pattern. Easy, but would need some additional intelligence so clients don't stupidly try to reconnect to dead servers over and over.

- Use DEALER sockets and blast out requests (which will be load balanced to all connected servers) until they get a reply. Effective, but not elegant.

- Use ROUTER sockets so clients can address specific servers. But how does the client know the identity of the server sockets? Either the server has to ping the client first (complex), or the server has to use a hard-coded, fixed identity known to the client (nasty).

We'll develop each of these in the following subsections.



| [Model One: Simple Retry and Failover](http://zguide.zeromq.org/page:all#Model-One-Simple-Retry-and-Failover) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-109) [next](http://zguide.zeromq.org/page:all#header-111) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

So our menu appears to offer: simple, brutal, complex, or nasty. Let's start with simple and then work out the kinks. We take Lazy Pirate and rewrite it to work with multiple server endpoints.

Start one or several servers first, specifying a bind endpoint as the argument:

[flserver1: Freelance server, Model One in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:flserver1) | [Java](http://zguide.zeromq.org/java:flserver1) | [Lua](http://zguide.zeromq.org/lua:flserver1) | [PHP](http://zguide.zeromq.org/php:flserver1) | [Python](http://zguide.zeromq.org/py:flserver1) | [Tcl](http://zguide.zeromq.org/tcl:flserver1) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Node.js | Objective-C | ooc | Perl | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

[flclient1: Freelance client, Model One in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:flclient1) | [Java](http://zguide.zeromq.org/java:flclient1) | [PHP](http://zguide.zeromq.org/php:flclient1) | [Python](http://zguide.zeromq.org/py:flclient1) | [Tcl](http://zguide.zeromq.org/tcl:flclient1) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

```
flserver1 tcp://*:5555 &
flserver1 tcp://*:5556 &
flclient1 tcp://localhost:5555 tcp://localhost:5556
```

Although the basic approach is Lazy Pirate, the client aims to just get one successful reply. It has two techniques, depending on whether you are running a single server or multiple servers:

- With a single server, the client will retry several times, exactly as for Lazy Pirate.
- With multiple servers, the client will try each server at most once until it's received a reply or has tried all servers.

This solves the main weakness of Lazy Pirate, namely that it could not fail over to backup or alternate servers.

However, this design won't work well in a real application. If we're connecting many sockets and our primary name server is down, we're going to experience this painful timeout each time.



| [Model Two: Brutal Shotgun Massacre](http://zguide.zeromq.org/page:all#Model-Two-Brutal-Shotgun-Massacre) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-110) [next](http://zguide.zeromq.org/page:all#header-112) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Let's switch our client to using a DEALER socket. Our goal here is to make sure we get a reply back within the shortest possible time, no matter whether a particular server is up or down. Our client takes this approach:

- We set things up, connecting to all servers.
- When we have a request, we blast it out as many times as we have servers.
- We wait for the first reply, and take that.
- We ignore any other replies.

What will happen in practice is that when all servers are running, ZeroMQ will distribute the requests so that each server gets one request and sends one reply. When any server is offline and disconnected, ZeroMQ will distribute the requests to the remaining servers. So a server may in some cases get the same request more than once.

What's more annoying for the client is that we'll get multiple replies back, but there's no guarantee we'll get a precise number of replies. Requests and replies can get lost (e.g., if the server crashes while processing a request).

So we have to number requests and ignore any replies that don't match the request number. Our Model One server will work because it's an echo server, but coincidence is not a great basis for understanding. So we'll make a Model Two server that chews up the message and returns a correctly numbered reply with the content "OK". We'll use messages consisting of two parts: a sequence number and a body.

Start one or more servers, specifying a bind endpoint each time:

[flserver2: Freelance server, Model Two in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:flserver2) | [Java](http://zguide.zeromq.org/java:flserver2) | [Lua](http://zguide.zeromq.org/lua:flserver2) | [PHP](http://zguide.zeromq.org/php:flserver2) | [Python](http://zguide.zeromq.org/py:flserver2) | [Tcl](http://zguide.zeromq.org/tcl:flserver2) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Node.js | Objective-C | ooc | Perl | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

[flclient2: Freelance client, Model Two in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:flclient2) | [Java](http://zguide.zeromq.org/java:flclient2) | [PHP](http://zguide.zeromq.org/php:flclient2) | [Python](http://zguide.zeromq.org/py:flclient2) | [Tcl](http://zguide.zeromq.org/tcl:flclient2) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

- The client is structured as a nice little class-based API that hides the dirty work of creating ZeroMQ contexts and sockets and talking to the server. That is, if a shotgun blast to the midriff can be called "talking".

- The client will abandon the chase if it can't find *any* responsive server within a few seconds.

- The client has to create a valid REP envelope, i.e., add an empty message frame to the front of the message.

The client performs 10,000 name resolution requests (fake ones, as our server does essentially nothing) and measures the average cost. On my test box, talking to one server, this requires about 60 microseconds. Talking to three servers, it takes about 80 microseconds.

The pros and cons of our shotgun approach are:

- Pro: it is simple, easy to make and easy to understand.
- Pro: it does the job of failover, and works rapidly, so long as there is at least one server running.
- Con: it creates redundant network traffic.
- Con: we can't prioritize our servers, i.e., Primary, then Secondary.
- Con: the server can do at most one request at a time, period.



| [Model Three: Complex and Nasty](http://zguide.zeromq.org/page:all#Model-Three-Complex-and-Nasty) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-111) [next](http://zguide.zeromq.org/page:all#header-113) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The shotgun approach seems too good to be true. Let's be scientific and work through all the alternatives. We're going to explore the complex/nasty option, even if it's only to finally realize that we preferred brutal. Ah, the story of my life.

We can solve the main problems of the client by switching to a ROUTER socket. That lets us send requests to specific servers, avoid servers we know are dead, and in general be as smart as we want to be. We can also solve the main problem of the server (single-threadedness) by switching to a ROUTER socket.

But doing ROUTER to ROUTER between two anonymous sockets (which haven't set an identity) is not possible. Both sides generate an identity (for the other peer) only when they receive a first message, and thus neither can talk to the other until it has first received a message. The only way out of this conundrum is to cheat, and use hard-coded identities in one direction. The proper way to cheat, in a client/server case, is to let the client "know" the identity of the server. Doing it the other way around would be insane, on top of complex and nasty, because any number of clients should be able to arise independently. Insane, complex, and nasty are great attributes for a genocidal dictator, but terrible ones for software.

Rather than invent yet another concept to manage, we'll use the connection endpoint as identity. This is a unique string on which both sides can agree without more prior knowledge than they already have for the shotgun model. It's a sneaky and effective way to connect two ROUTER sockets.

Remember how ZeroMQ identities work. The server ROUTER socket sets an identity before it binds its socket. When a client connects, they do a little handshake to exchange identities, before either side sends a real message. The client ROUTER socket, having not set an identity, sends a null identity to the server. The server generates a random UUID to designate the client for its own use. The server sends its identity (which we've agreed is going to be an endpoint string) to the client.

This means that our client can route a message to the server (i.e., send on its ROUTER socket, specifying the server endpoint as identity) as soon as the connection is established. That's not *immediately* after doing a `zmq_connect()`, but some random time thereafter. Herein lies one problem: we don't know when the server will actually be available and complete its connection handshake. If the server is online, it could be after a few milliseconds. If the server is down and the sysadmin is out to lunch, it could be an hour from now.

There's a small paradox here. We need to know when servers become connected and available for work. In the Freelance pattern, unlike the broker-based patterns we saw earlier in this chapter, servers are silent until spoken to. Thus we can't talk to a server until it's told us it's online, which it can't do until we've asked it.

My solution is to mix in a little of the shotgun approach from model 2, meaning we'll fire (harmless) shots at anything we can, and if anything moves, we know it's alive. We're not going to fire real requests, but rather a kind of ping-pong heartbeat.

This brings us to the realm of protocols again, so here's a [short spec that defines how a Freelance client and server exchange ping-pong commands and request-reply commands](http://rfc.zeromq.org/spec:10).

It is short and sweet to implement as a server. Here's our echo server, Model Three, now speaking FLP:

[flserver3: Freelance server, Model Three in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:flserver3) | [Java](http://zguide.zeromq.org/java:flserver3) | [Lua](http://zguide.zeromq.org/lua:flserver3) | [Python](http://zguide.zeromq.org/py:flserver3) | [Tcl](http://zguide.zeromq.org/tcl:flserver3) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

[flclient3: Freelance client, Model Three in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:flclient3) | [Java](http://zguide.zeromq.org/java:flclient3) | [Python](http://zguide.zeromq.org/py:flclient3) | [Tcl](http://zguide.zeromq.org/tcl:flclient3) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

[flcliapi: Freelance client API in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:flcliapi) | [Java](http://zguide.zeromq.org/java:flcliapi) | [Python](http://zguide.zeromq.org/py:flcliapi) | [Tcl](http://zguide.zeromq.org/tcl:flcliapi) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

- **Multithreaded API**: the client API consists of two parts, a synchronous `flcliapi` class that runs in the application thread, and an asynchronous *agent* class that runs as a background thread. Remember how ZeroMQ makes it easy to create multithreaded apps. The flcliapi and agent classes talk to each other with messages over an `inproc`socket. All ZeroMQ aspects (such as creating and destroying a context) are hidden in the API. The agent in effect acts like a mini-broker, talking to servers in the background, so that when we make a request, it can make a best effort to reach a server it believes is available.

- **Tickless poll timer**: in previous poll loops we always used a fixed tick interval, e.g., 1 second, which is simple enough but not excellent on power-sensitive clients (such as notebooks or mobile phones), where waking the CPU costs power. For fun, and to help save the planet, the agent uses a *tickless timer*, which calculates the poll delay based on the next timeout we're expecting. A proper implementation would keep an ordered list of timeouts. We just check all timeouts and calculate the poll delay until the next one.



| [Conclusion](http://zguide.zeromq.org/page:all#Conclusion) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-112) [next](http://zguide.zeromq.org/page:all#header-114) |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
|                                                            |                                                              |

In this chapter, we've seen a variety of reliable request-reply mechanisms, each with certain costs and benefits. The example code is largely ready for real use, though it is not optimized. Of all the different patterns, the two that stand out for production use are the Majordomo pattern, for broker-based reliability, and the Freelance pattern, for brokerless reliability.



| [Chapter 5 - Advanced Pub-Sub Patterns](http://zguide.zeromq.org/page:all#Chapter-Advanced-Pub-Sub-Patterns) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-113) [next](http://zguide.zeromq.org/page:all#header-115) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

In [Chapter 3 - Advanced Request-Reply Patterns](http://zguide.zeromq.org/page:all#advanced-request-reply) and [Chapter 4 - Reliable Request-Reply Patterns](http://zguide.zeromq.org/page:all#reliable-request-reply) we looked at advanced use of ZeroMQ's request-reply pattern. If you managed to digest all that, congratulations. In this chapter we'll focus on publish-subscribe and extend ZeroMQ's core pub-sub pattern with higher-level patterns for performance, reliability, state distribution, and monitoring.

We'll cover:

- When to use publish-subscribe
- How to handle too-slow subscribers (the *Suicidal Snail* pattern)
- How to design high-speed subscribers (the *Black Box* pattern)
- How to monitor a pub-sub network (the *Espresso* pattern)
- How to build a shared key-value store (the *Clone* pattern)
- How to use reactors to simplify complex servers
- How to use the Binary Star pattern to add failover to a server



| [Pros and Cons of Pub-Sub](http://zguide.zeromq.org/page:all#Pros-and-Cons-of-Pub-Sub) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-114) [next](http://zguide.zeromq.org/page:all#header-116) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

ZeroMQ's low-level patterns have their different characters. Pub-sub addresses an old messaging problem, which is *multicast* or *group messaging*. It has that unique mix of meticulous simplicity and brutal indifference that characterizes ZeroMQ. It's worth understanding the trade-offs that pub-sub makes, how these benefit us, and how we can work around them if needed.

First, PUB sends each message to "all of many", whereas PUSH and DEALER rotate messages to "one of many". You cannot simply replace PUSH with PUB or vice versa and hope that things will work. This bears repeating because people seem to quite often suggest doing this.

More profoundly, pub-sub is aimed at scalability. This means large volumes of data, sent rapidly to many recipients. If you need millions of messages per second sent to thousands of points, you'll appreciate pub-sub a lot more than if you need a few messages a second sent to a handful of recipients.

To get scalability, pub-sub uses the same trick as push-pull, which is to get rid of back-chatter. This means that recipients don't talk back to senders. There are some exceptions, e.g., SUB sockets will send subscriptions to PUB sockets, but it's anonymous and infrequent.

Killing back-chatter is essential to real scalability. With pub-sub, it's how the pattern can map cleanly to the PGM multicast protocol, which is handled by the network switch. In other words, subscribers don't connect to the publisher at all, they connect to a multicast *group*on the switch, to which the publisher sends its messages.

When we remove back-chatter, our overall message flow becomes *much* simpler, which lets us make simpler APIs, simpler protocols, and in general reach many more people. But we also remove any possibility to coordinate senders and receivers. What this means is:

- Publishers can't tell when subscribers are successfully connected, both on initial connections, and on reconnections after network failures.

- Subscribers can't tell publishers anything that would allow publishers to control the rate of messages they send. Publishers only have one setting, which is *full-speed*, and subscribers must either keep up or lose messages.

- Publishers can't tell when subscribers have disappeared due to processes crashing, networks breaking, and so on.

The downside is that we actually need all of these if we want to do reliable multicast. The ZeroMQ pub-sub pattern will lose messages arbitrarily when a subscriber is connecting, when a network failure occurs, or just if the subscriber or network can't keep up with the publisher.

The upside is that there are many use cases where *almost* reliable multicast is just fine. When we need this back-chatter, we can either switch to using ROUTER-DEALER (which I tend to do for most normal volume cases), or we can add a separate channel for synchronization (we'll see an example of this later in this chapter).

Pub-sub is like a radio broadcast; you miss everything before you join, and then how much information you get depends on the quality of your reception. Surprisingly, this model is useful and widespread because it maps perfectly to real world distribution of information. Think of Facebook and Twitter, the BBC World Service, and the sports results.

As we did for request-reply, let's define *reliability* in terms of what can go wrong. Here are the classic failure cases for pub-sub:

- Subscribers join late, so they miss messages the server already sent.
- Subscribers can fetch messages too slowly, so queues build up and then overflow.
- Subscribers can drop off and lose messages while they are away.
- Subscribers can crash and restart, and lose whatever data they already received.
- Networks can become overloaded and drop data (specifically, for PGM).
- Networks can become too slow, so publisher-side queues overflow and publishers crash.

A lot more can go wrong but these are the typical failures we see in a realistic system. Since v3.x, ZeroMQ forces default limits on its internal buffers (the so-called high-water mark or HWM), so publisher crashes are rarer unless you deliberately set the HWM to infinite.

All of these failure cases have answers, though not always simple ones. Reliability requires complexity that most of us don't need, most of the time, which is why ZeroMQ doesn't attempt to provide it out of the box (even if there was one global design for reliability, which there isn't).



| [Pub-Sub Tracing (Espresso Pattern)](http://zguide.zeromq.org/page:all#Pub-Sub-Tracing-Espresso-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-115) [next](http://zguide.zeromq.org/page:all#header-117) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Let's start this chapter by looking at a way to trace pub-sub networks. In [Chapter 2 - Sockets and Patterns](http://zguide.zeromq.org/page:all#sockets-and-patterns) we saw a simple proxy that used these to do transport bridging. The `zmq_proxy()` method has three arguments: a *frontend* and *backend* socket that it bridges together, and a *capture* socket to which it will send all messages.

The code is deceptively simple:

[espresso: Espresso Pattern in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:espresso) | [Java](http://zguide.zeromq.org/java:espresso) | [Python](http://zguide.zeromq.org/py:espresso) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

The subscriber thread subscribes to "A" and "B", receives five messages, and then destroys its socket. When you run the example, the listener prints two subscription messages, five data messages, two unsubscribe messages, and then silence:

```
[002] 0141
[002] 0142
[007] B-91164
[007] B-12979
[007] A-52599
[007] A-06417
[007] A-45770
[002] 0041
[002] 0042
```

This shows neatly how the publisher socket stops sending data when there are no subscribers for it. The publisher thread is still sending messages. The socket just drops them silently.



| [Last Value Caching](http://zguide.zeromq.org/page:all#Last-Value-Caching) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-116) [next](http://zguide.zeromq.org/page:all#header-118) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

If you've used commercial pub-sub systems, you may be used to some features that are missing in the fast and cheerful ZeroMQ pub-sub model. One of these is *last value caching*(LVC). This solves the problem of how a new subscriber catches up when it joins the network. The theory is that publishers get notified when a new subscriber joins and subscribes to some specific topics. The publisher can then rebroadcast the last message for those topics.

I've already explained why publishers don't get notified when there are new subscribers, because in large pub-sub systems, the volumes of data make it pretty much impossible. To make really large-scale pub-sub networks, you need a protocol like PGM that exploits an upscale Ethernet switch's ability to multicast data to thousands of subscribers. Trying to do a TCP unicast from the publisher to each of thousands of subscribers just doesn't scale. You get weird spikes, unfair distribution (some subscribers getting the message before others), network congestion, and general unhappiness.

PGM is a one-way protocol: the publisher sends a message to a multicast address at the switch, which then rebroadcasts that to all interested subscribers. The publisher never sees when subscribers join or leave: this all happens in the switch, which we don't really want to start reprogramming.

However, in a lower-volume network with a few dozen subscribers and a limited number of topics, we can use TCP and then the XSUB and XPUB sockets *do* talk to each other as we just saw in the Espresso pattern.

Can we make an LVC using ZeroMQ? The answer is yes, if we make a proxy that sits between the publisher and subscribers; an analog for the PGM switch, but one we can program ourselves.

I'll start by making a publisher and subscriber that highlight the worst case scenario. This publisher is pathological. It starts by immediately sending messages to each of a thousand topics, and then it sends one update a second to a random topic. A subscriber connects, and subscribes to a topic. Without LVC, a subscriber would have to wait an average of 500 seconds to get any data. To add some drama, let's pretend there's an escaped convict called Gregor threatening to rip the head off Roger the toy bunny if we can't fix that 8.3 minutes' delay.

Here's the publisher code. Note that it has the command line option to connect to some address, but otherwise binds to an endpoint. We'll use this later to connect to our last value cache:

[pathopub: Pathologic Publisher in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:pathopub) | [C#](http://zguide.zeromq.org/cs:pathopub) | [Java](http://zguide.zeromq.org/java:pathopub) | [Python](http://zguide.zeromq.org/py:pathopub) | [Ruby](http://zguide.zeromq.org/rb:pathopub) | [Ada | Basic | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Scala | Tcl](http://zguide.zeromq.org/main:translate)

[pathosub: Pathologic Subscriber in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:pathosub) | [C#](http://zguide.zeromq.org/cs:pathosub) | [Java](http://zguide.zeromq.org/java:pathosub) | [Python](http://zguide.zeromq.org/py:pathosub) | [Ruby](http://zguide.zeromq.org/rb:pathosub) | [Ada | Basic | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Scala | Tcl](http://zguide.zeromq.org/main:translate)

```
./pathosub &
./pathopub
```

It's when you run a second subscriber that you understand Roger's predicament. You have to leave it an awful long time before it reports getting any data. So, here's our last value cache. As I promised, it's a proxy that binds to two sockets and then handles messages on both:

[lvcache: Last Value Caching Proxy in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:lvcache) | [C#](http://zguide.zeromq.org/cs:lvcache) | [Java](http://zguide.zeromq.org/java:lvcache) | [Node.js](http://zguide.zeromq.org/js:lvcache) | [Python](http://zguide.zeromq.org/py:lvcache) | [Ruby](http://zguide.zeromq.org/rb:lvcache) | [Ada | Basic | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Objective-C | ooc | Perl | PHP | Q | Racket | Scala | Tcl](http://zguide.zeromq.org/main:translate)

```
./lvcache &
./pathopub tcp://localhost:5557
```

And now run as many instances of the subscriber as you want to try, each time connecting to the proxy on port 5558:

```
./pathosub tcp://localhost:5558
```

Each subscriber happily reports "Save Roger", and Gregor the Escaped Convict slinks back to his seat for dinner and a nice cup of hot milk, which is all he really wanted in the first place.

One note: by default, the XPUB socket does not report duplicate subscriptions, which is what you want when you're naively connecting an XPUB to an XSUB. Our example sneakily gets around this by using random topics so the chance of it not working is one in a million. In a real LVC proxy, you'll want to use the `ZMQ_XPUB_VERBOSE` option that we implement in [The ZeroMQ Community](http://zguide.zeromq.org/page:all#the-community) as an exercise.



| [Slow Subscriber Detection (Suicidal Snail Pattern)](http://zguide.zeromq.org/page:all#Slow-Subscriber-Detection-Suicidal-Snail-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-117) [next](http://zguide.zeromq.org/page:all#header-119) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

A common problem you will hit when using the pub-sub pattern in real life is the slow subscriber. In an ideal world, we stream data at full speed from publishers to subscribers. In reality, subscriber applications are often written in interpreted languages, or just do a lot of work, or are just badly written, to the extent that they can't keep up with publishers.

How do we handle a slow subscriber? The ideal fix is to make the subscriber faster, but that might take work and time. Some of the classic strategies for handling a slow subscriber are:

- **Queue messages on the publisher**. This is what Gmail does when I don't read my email for a couple of hours. But in high-volume messaging, pushing queues upstream has the thrilling but unprofitable result of making publishers run out of memory and crash—especially if there are lots of subscribers and it's not possible to flush to disk for performance reasons.

- **Queue messages on the subscriber**. This is much better, and it's what ZeroMQ does by default if the network can keep up with things. If anyone's going to run out of memory and crash, it'll be the subscriber rather than the publisher, which is fair. This is perfect for "peaky" streams where a subscriber can't keep up for a while, but can catch up when the stream slows down. However, it's no answer to a subscriber that's simply too slow in general.

- **Stop queuing new messages after a while**. This is what Gmail does when my mailbox overflows its precious gigabytes of space. New messages just get rejected or dropped. This is a great strategy from the perspective of the publisher, and it's what ZeroMQ does when the publisher sets a HWM. However, it still doesn't help us fix the slow subscriber. Now we just get gaps in our message stream.

- **Punish slow subscribers with disconnect**. This is what Hotmail (remember that?) did when I didn't log in for two weeks, which is why I was on my fifteenth Hotmail account when it hit me that there was perhaps a better way. It's a nice brutal strategy that forces subscribers to sit up and pay attention and would be ideal, but ZeroMQ doesn't do this, and there's no way to layer it on top because subscribers are invisible to publisher applications.

None of these classic strategies fit, so we need to get creative. Rather than disconnect the publisher, let's convince the subscriber to kill itself. This is the Suicidal Snail pattern. When a subscriber detects that it's running too slowly (where "too slowly" is presumably a configured option that really means "so slowly that if you ever get here, shout really loudly because I need to know, so I can fix this!"), it croaks and dies.

How can a subscriber detect this? One way would be to sequence messages (number them in order) and use a HWM at the publisher. Now, if the subscriber detects a gap (i.e., the numbering isn't consecutive), it knows something is wrong. We then tune the HWM to the "croak and die if you hit this" level.

There are two problems with this solution. One, if we have many publishers, how do we sequence messages? The solution is to give each publisher a unique ID and add that to the sequencing. Second, if subscribers use `ZMQ_SUBSCRIBE` filters, they will get gaps by definition. Our precious sequencing will be for nothing.

Some use cases won't use filters, and sequencing will work for them. But a more general solution is that the publisher timestamps each message. When a subscriber gets a message, it checks the time, and if the difference is more than, say, one second, it does the "croak and die" thing, possibly firing off a squawk to some operator console first.

The Suicide Snail pattern works especially when subscribers have their own clients and service-level agreements and need to guarantee certain maximum latencies. Aborting a subscriber may not seem like a constructive way to guarantee a maximum latency, but it's the assertion model. Abort today, and the problem will be fixed. Allow late data to flow downstream, and the problem may cause wider damage and take longer to appear on the radar.

Here is a minimal example of a Suicidal Snail:

[suisnail: Suicidal Snail in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:suisnail) | [C#](http://zguide.zeromq.org/cs:suisnail) | [Java](http://zguide.zeromq.org/java:suisnail) | [Lua](http://zguide.zeromq.org/lua:suisnail) | [PHP](http://zguide.zeromq.org/php:suisnail) | [Python](http://zguide.zeromq.org/py:suisnail) | [Tcl](http://zguide.zeromq.org/tcl:suisnail) | [Ada | Basic | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Node.js | Objective-C | ooc | Perl | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

- The message here consists simply of the current system clock as a number of milliseconds. In a realistic application, you'd have at least a message header with the timestamp and a message body with data.

- The example has subscriber and publisher in a single process as two threads. In reality, they would be separate processes. Using threads is just convenient for the demonstration.



| [High-Speed Subscribers (Black Box Pattern)](http://zguide.zeromq.org/page:all#High-Speed-Subscribers-Black-Box-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-118) [next](http://zguide.zeromq.org/page:all#header-120) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Now lets look at one way to make our subscribers faster. A common use case for pub-sub is distributing large data streams like market data coming from stock exchanges. A typical setup would have a publisher connected to a stock exchange, taking price quotes, and sending them out to a number of subscribers. If there are a handful of subscribers, we could use TCP. If we have a larger number of subscribers, we'd probably use reliable multicast, i.e., PGM.

**Figure 56 - The Simple Black Box Pattern**

![fig56.png](https://github.com/imatix/zguide/raw/master/images/fig56.png)

Let's imagine our feed has an average of 100,000 100-byte messages a second. That's a typical rate, after filtering market data we don't need to send on to subscribers. Now we decide to record a day's data (maybe 250 GB in 8 hours), and then replay it to a simulation network, i.e., a small group of subscribers. While 100K messages a second is easy for a ZeroMQ application, we want to replay it *much faster*.

So we set up our architecture with a bunch of boxes—one for the publisher and one for each subscriber. These are well-specified boxes—eight cores, twelve for the publisher.

And as we pump data into our subscribers, we notice two things:

1. When we do even the slightest amount of work with a message, it slows down our subscriber to the point where it can't catch up with the publisher again.

1. We're hitting a ceiling, at both publisher and subscriber, to around 6M messages a second, even after careful optimization and TCP tuning.

The first thing we have to do is break our subscriber into a multithreaded design so that we can do work with messages in one set of threads, while reading messages in another. Typically, we don't want to process every message the same way. Rather, the subscriber will filter some messages, perhaps by prefix key. When a message matches some criteria, the subscriber will call a worker to deal with it. In ZeroMQ terms, this means sending the message to a worker thread.

So the subscriber looks something like a queue device. We could use various sockets to connect the subscriber and workers. If we assume one-way traffic and workers that are all identical, we can use PUSH and PULL and delegate all the routing work to ZeroMQ. This is the simplest and fastest approach.

The subscriber talks to the publisher over TCP or PGM. The subscriber talks to its workers, which are all in the same process, over `inproc://`.

**Figure 57 - Mad Black Box Pattern**

![fig57.png](https://github.com/imatix/zguide/raw/master/images/fig57.png)

Now to break that ceiling. The subscriber thread hits 100% of CPU and because it is one thread, it cannot use more than one core. A single thread will always hit a ceiling, be it at 2M, 6M, or more messages per second. We want to split the work across multiple threads that can run in parallel.

The approach used by many high-performance products, which works here, is *sharding*. Using sharding, we split the work into parallel and independent streams, such as half of the topic keys in one stream, and half in another. We could use many streams, but performance won't scale unless we have free cores. So let's see how to shard into two streams.

With two streams, working at full speed, we would configure ZeroMQ as follows:

- Two I/O threads, rather than one.
- Two network interfaces (NIC), one per subscriber.
- Each I/O thread bound to a specific NIC.
- Two subscriber threads, bound to specific cores.
- Two SUB sockets, one per subscriber thread.
- The remaining cores assigned to worker threads.
- Worker threads connected to both subscriber PUSH sockets.

Ideally, we want to match the number of fully-loaded threads in our architecture with the number of cores. When threads start to fight for cores and CPU cycles, the cost of adding more threads outweighs the benefits. There would be no benefit, for example, in creating more I/O threads.



| [Reliable Pub-Sub (Clone Pattern)](http://zguide.zeromq.org/page:all#Reliable-Pub-Sub-Clone-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-119) [next](http://zguide.zeromq.org/page:all#header-121) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

As a larger worked example, we'll take the problem of making a reliable pub-sub architecture. We'll develop this in stages. The goal is to allow a set of applications to share some common state. Here are our technical challenges:

- We have a large set of client applications, say thousands or tens of thousands.
- They will join and leave the network arbitrarily.
- These applications must share a single eventually-consistent *state*.
- Any application can update the state at any point in time.

Let's say that updates are reasonably low-volume. We don't have real time goals. The whole state can fit into memory. Some plausible use cases are:

- A configuration that is shared by a group of cloud servers.
- Some game state shared by a group of players.
- Exchange rate data that is updated in real time and available to applications.



| [Centralized Versus Decentralized](http://zguide.zeromq.org/page:all#Centralized-Versus-Decentralized) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-120) [next](http://zguide.zeromq.org/page:all#header-122) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

A first decision we have to make is whether we work with a central server or not. It makes a big difference in the resulting design. The trade-offs are these:

- Conceptually, a central server is simpler to understand because networks are not naturally symmetrical. With a central server, we avoid all questions of discovery, bind versus connect, and so on.

- Generally, a fully-distributed architecture is technically more challenging but ends up with simpler protocols. That is, each node must act as server and client in the right way, which is delicate. When done right, the results are simpler than using a central server. We saw this in the Freelance pattern in [Chapter 4 - Reliable Request-Reply Patterns](http://zguide.zeromq.org/page:all#reliable-request-reply).

- A central server will become a bottleneck in high-volume use cases. If handling scale in the order of millions of messages a second is required, we should aim for decentralization right away.

- Ironically, a centralized architecture will scale to more nodes more easily than a decentralized one. That is, it's easier to connect 10,000 nodes to one server than to each other.

So, for the Clone pattern we'll work with a *server* that publishes state updates and a set of *clients* that represent applications.



| [Representing State as Key-Value Pairs](http://zguide.zeromq.org/page:all#Representing-State-as-Key-Value-Pairs) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-121) [next](http://zguide.zeromq.org/page:all#header-123) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

We'll develop Clone in stages, solving one problem at a time. First, let's look at how to update a shared state across a set of clients. We need to decide how to represent our state, as well as the updates. The simplest plausible format is a key-value store, where one key-value pair represents an atomic unit of change in the shared state.

We have a simple pub-sub example in [Chapter 1 - Basics](http://zguide.zeromq.org/page:all#basics), the weather server and client. Let's change the server to send key-value pairs, and the client to store these in a hash table. This lets us send updates from one server to a set of clients using the classic pub-sub model.

An update is either a new key-value pair, a modified value for an existing key, or a deleted key. We can assume for now that the whole store fits in memory and that applications access it by key, such as by using a hash table or dictionary. For larger stores and some kind of persistence we'd probably store the state in a database, but that's not relevant here.

This is the server:

[clonesrv1: Clone server, Model One in C](javascript:;)


[Java](http://zguide.zeromq.org/java:clonesrv1) | [Python](http://zguide.zeromq.org/py:clonesrv1) | [Tcl](http://zguide.zeromq.org/tcl:clonesrv1) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

[clonecli1: Clone client, Model One in C](javascript:;)


[Java](http://zguide.zeromq.org/java:clonecli1) | [Python](http://zguide.zeromq.org/py:clonecli1) | [Tcl](http://zguide.zeromq.org/tcl:clonecli1) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

![fig58.png](https://github.com/imatix/zguide/raw/master/images/fig58.png)

Here are some things to note about this first model:

- All the hard work is done in a `kvmsg` class. This class works with key-value message objects, which are multipart ZeroMQ messages structured as three frames: a key (a ZeroMQ string), a sequence number (64-bit value, in network byte order), and a binary body (holds everything else).

- The server generates messages with a randomized 4-digit key, which lets us simulate a large but not enormous hash table (10K entries).

- We don't implement deletions in this version: all messages are inserts or updates.

- The server does a 200 millisecond pause after binding its socket. This is to prevent *slow joiner syndrome*, where the subscriber loses messages as it connects to the server's socket. We'll remove that in later versions of the Clone code.

- We'll use the terms *publisher* and *subscriber* in the code to refer to sockets. This will help later when we have multiple sockets doing different things.

Here is the `kvmsg` class, in the simplest form that works for now:

[kvsimple: Key-value message class in C](javascript:;)


[Java](http://zguide.zeromq.org/java:kvsimple) | [Python](http://zguide.zeromq.org/py:kvsimple) | [Tcl](http://zguide.zeromq.org/tcl:kvsimple) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

Both the server and client maintain hash tables, but this first model only works properly if we start all clients before the server and the clients never crash. That's very artificial.



| [Getting an Out-of-Band Snapshot](http://zguide.zeromq.org/page:all#Getting-an-Out-of-Band-Snapshot) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-122) [next](http://zguide.zeromq.org/page:all#header-124) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

So now we have our second problem: how to deal with late-joining clients or clients that crash and then restart.

In order to allow a late (or recovering) client to catch up with a server, it has to get a snapshot of the server's state. Just as we've reduced "message" to mean "a sequenced key-value pair", we can reduce "state" to mean "a hash table". To get the server state, a client opens a DEALER socket and asks for it explicitly.

To make this work, we have to solve a problem of timing. Getting a state snapshot will take a certain time, possibly fairly long if the snapshot is large. We need to correctly apply updates to the snapshot. But the server won't know when to start sending us updates. One way would be to start subscribing, get a first update, and then ask for "state for update N". This would require the server storing one snapshot for each update, which isn't practical.

**Figure 59 - State Replication**

![fig59.png](https://github.com/imatix/zguide/raw/master/images/fig59.png)

So we will do the synchronization in the client, as follows:

- The client first subscribes to updates and then makes a state request. This guarantees that the state is going to be newer than the oldest update it has.

- The client waits for the server to reply with state, and meanwhile queues all updates. It does this simply by not reading them: ZeroMQ keeps them queued on the socket queue.

- When the client receives its state update, it begins once again to read updates. However, it discards any updates that are older than the state update. So if the state update includes updates up to 200, the client will discard updates up to 201.

- The client then applies updates to its own state snapshot.

It's a simple model that exploits ZeroMQ's own internal queues. Here's the server:

[clonesrv2: Clone server, Model Two in C](javascript:;)


[Java](http://zguide.zeromq.org/java:clonesrv2) | [Python](http://zguide.zeromq.org/py:clonesrv2) | [Tcl](http://zguide.zeromq.org/tcl:clonesrv2) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

[clonecli2: Clone client, Model Two in C](javascript:;)


[Java](http://zguide.zeromq.org/java:clonecli2) | [Python](http://zguide.zeromq.org/py:clonecli2) | [Tcl](http://zguide.zeromq.org/tcl:clonecli2) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

- The server uses two tasks. One thread produces the updates (randomly) and sends these to the main PUB socket, while the other thread handles state requests on the ROUTER socket. The two communicate across PAIR sockets over an `inproc://`connection.

- The client is really simple. In C, it consists of about fifty lines of code. A lot of the heavy lifting is done in the `kvmsg` class. Even so, the basic Clone pattern is easier to implement than it seemed at first.

- We don't use anything fancy for serializing the state. The hash table holds a set of `kvmsg` objects, and the server sends these, as a batch of messages, to the client requesting state. If multiple clients request state at once, each will get a different snapshot.

- We assume that the client has exactly one server to talk to. The server must be running; we do not try to solve the question of what happens if the server crashes.

Right now, these two programs don't do anything real, but they correctly synchronize state. It's a neat example of how to mix different patterns: PAIR-PAIR, PUB-SUB, and ROUTER-DEALER.



| [Republishing Updates from Clients](http://zguide.zeromq.org/page:all#Republishing-Updates-from-Clients) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-123) [next](http://zguide.zeromq.org/page:all#header-125) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

In our second model, changes to the key-value store came from the server itself. This is a centralized model that is useful, for example if we have a central configuration file we want to distribute, with local caching on each node. A more interesting model takes updates from clients, not the server. The server thus becomes a stateless broker. This gives us some benefits:

- We're less worried about the reliability of the server. If it crashes, we can start a new instance and feed it new values.

- We can use the key-value store to share knowledge between active peers.

To send updates from clients back to the server, we could use a variety of socket patterns. The simplest plausible solution is a PUSH-PULL combination.

Why don't we allow clients to publish updates directly to each other? While this would reduce latency, it would remove the guarantee of consistency. You can't get consistent shared state if you allow the order of updates to change depending on who receives them. Say we have two clients, changing different keys. This will work fine. But if the two clients try to change the same key at roughly the same time, they'll end up with different notions of its value.

There are a few strategies for obtaining consistency when changes happen in multiple places at once. We'll use the approach of centralizing all change. No matter the precise timing of the changes that clients make, they are all pushed through the server, which enforces a single sequence according to the order in which it gets updates.

**Figure 60 - Republishing Updates**

![fig60.png](https://github.com/imatix/zguide/raw/master/images/fig60.png)

By mediating all changes, the server can also add a unique sequence number to all updates. With unique sequencing, clients can detect the nastier failures, including network congestion and queue overflow. If a client discovers that its incoming message stream has a hole, it can take action. It seems sensible that the client contact the server and ask for the missing messages, but in practice that isn't useful. If there are holes, they're caused by network stress, and adding more stress to the network will make things worse. All the client can do is warn its users that it is "unable to continue", stop, and not restart until someone has manually checked the cause of the problem.

We'll now generate state updates in the client. Here's the server:

[clonesrv3: Clone server, Model Three in C](javascript:;)


[Java](http://zguide.zeromq.org/java:clonesrv3) | [Python](http://zguide.zeromq.org/py:clonesrv3) | [Tcl](http://zguide.zeromq.org/tcl:clonesrv3) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

[clonecli3: Clone client, Model Three in C](javascript:;)


[Java](http://zguide.zeromq.org/java:clonecli3) | [Python](http://zguide.zeromq.org/py:clonecli3) | [Tcl](http://zguide.zeromq.org/tcl:clonecli3) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

- The server has collapsed to a single task. It manages a PULL socket for incoming updates, a ROUTER socket for state requests, and a PUB socket for outgoing updates.

- The client uses a simple tickless timer to send a random update to the server once a second. In a real implementation, we would drive updates from application code.



| [Working with Subtrees](http://zguide.zeromq.org/page:all#Working-with-Subtrees) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-124) [next](http://zguide.zeromq.org/page:all#header-126) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

As we grow the number of clients, the size of our shared store will also grow. It stops being reasonable to send everything to every client. This is the classic story with pub-sub: when you have a very small number of clients, you can send every message to all clients. As you grow the architecture, this becomes inefficient. Clients specialize in different areas.

So even when working with a shared store, some clients will want to work only with a part of that store, which we call a *subtree*. The client has to request the subtree when it makes a state request, and it must specify the same subtree when it subscribes to updates.

There are a couple of common syntaxes for trees. One is the *path hierarchy*, and another is the *topic tree*. These look like this:

- Path hierarchy: `/some/list/of/paths`
- Topic tree: `some.list.of.topics`

We'll use the path hierarchy, and extend our client and server so that a client can work with a single subtree. Once you see how to work with a single subtree you'll be able to extend this yourself to handle multiple subtrees, if your use case demands it.

Here's the server implementing subtrees, a small variation on Model Three:

[clonesrv4: Clone server, Model Four in C](javascript:;)


[Java](http://zguide.zeromq.org/java:clonesrv4) | [Python](http://zguide.zeromq.org/py:clonesrv4) | [Tcl](http://zguide.zeromq.org/tcl:clonesrv4) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

[clonecli4: Clone client, Model Four in C](javascript:;)


[Java](http://zguide.zeromq.org/java:clonecli4) | [Python](http://zguide.zeromq.org/py:clonecli4) | [Tcl](http://zguide.zeromq.org/tcl:clonecli4) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

| [Ephemeral Values](http://zguide.zeromq.org/page:all#Ephemeral-Values) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-125) [next](http://zguide.zeromq.org/page:all#header-127) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

An ephemeral value is one that expires automatically unless regularly refreshed. If you think of Clone being used for a registration service, then ephemeral values would let you do dynamic values. A node joins the network, publishes its address, and refreshes this regularly. If the node dies, its address eventually gets removed.

The usual abstraction for ephemeral values is to attach them to a *session*, and delete them when the session ends. In Clone, sessions would be defined by clients, and would end if the client died. A simpler alternative is to attach a *time to live* (TTL) to ephemeral values, which the server uses to expire values that haven't been refreshed in time.

A good design principle that I use whenever possible is to *not invent concepts that are not absolutely essential*. If we have very large numbers of ephemeral values, sessions will offer better performance. If we use a handful of ephemeral values, it's fine to set a TTL on each one. If we use masses of ephemeral values, it's more efficient to attach them to sessions and expire them in bulk. This isn't a problem we face at this stage, and may never face, so sessions go out the window.

Now we will implement ephemeral values. First, we need a way to encode the TTL in the key-value message. We could add a frame. The problem with using ZeroMQ frames for properties is that each time we want to add a new property, we have to change the message structure. It breaks compatibility. So let's add a properties frame to the message, and write the code to let us get and put property values.

Next, we need a way to say, "delete this value". Up until now, servers and clients have always blindly inserted or updated new values into their hash table. We'll say that if the value is empty, that means "delete this key".

Here's a more complete version of the `kvmsg` class, which implements the properties frame (and adds a UUID frame, which we'll need later on). It also handles empty values by deleting the key from the hash, if necessary:

[kvmsg: Key-value message class: full in C](javascript:;)


[Java](http://zguide.zeromq.org/java:kvmsg) | [Python](http://zguide.zeromq.org/py:kvmsg) | [Tcl](http://zguide.zeromq.org/tcl:kvmsg) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

kvmsg_set_prop (kvmsg, "ttl", "%d", randof (30));



| [Using a Reactor](http://zguide.zeromq.org/page:all#Using-a-Reactor) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-126) [next](http://zguide.zeromq.org/page:all#header-128) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Until now, we have used a poll loop in the server. In this next model of the server, we switch to using a reactor. In C, we use CZMQ's `zloop` class. Using a reactor makes the code more verbose, but easier to understand and build out because each piece of the server is handled by a separate reactor handler.

We use a single thread and pass a server object around to the reactor handlers. We could have organized the server as multiple threads, each handling one socket or timer, but that works better when threads don't have to share data. In this case all work is centered around the server's hashmap, so one thread is simpler.

There are three reactor handlers:

- One to handle snapshot requests coming on the ROUTER socket;
- One to handle incoming updates from clients, coming on the PULL socket;
- One to expire ephemeral values that have passed their TTL.

[clonesrv5: Clone server, Model Five in C](javascript:;)


[Java](http://zguide.zeromq.org/java:clonesrv5) | [Python](http://zguide.zeromq.org/py:clonesrv5) | [Tcl](http://zguide.zeromq.org/tcl:clonesrv5) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala](http://zguide.zeromq.org/main:translate)

| [Adding the Binary Star Pattern for Reliability](http://zguide.zeromq.org/page:all#Adding-the-Binary-Star-Pattern-for-Reliability) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-127) [next](http://zguide.zeromq.org/page:all#header-129) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The Clone models we've explored up to now have been relatively simple. Now we're going to get into unpleasantly complex territory, which has me getting up for another espresso. You should appreciate that making "reliable" messaging is complex enough that you always need to ask, "Do we actually need this?" before jumping into it. If you can get away with unreliable or with "good enough" reliability, you can make a huge win in terms of cost and complexity. Sure, you may lose some data now and then. It is often a good trade-off. Having said, that, and… sips… because the espresso is really good, let's jump in.

As you play with the last model, you'll stop and restart the server. It might look like it recovers, but of course it's applying updates to an empty state instead of the proper current state. Any new client joining the network will only get the latest updates instead of the full historical record.

What we want is a way for the server to recover from being killed, or crashing. We also need to provide backup in case the server is out of commission for any length of time. When someone asks for "reliability", ask them to list the failures they want to handle. In our case, these are:

- The server process crashes and is automatically or manually restarted. The process loses its state and has to get it back from somewhere.

- The server machine dies and is offline for a significant time. Clients have to switch to an alternate server somewhere.

- The server process or machine gets disconnected from the network, e.g., a switch dies or a datacenter gets knocked out. It may come back at some point, but in the meantime clients need an alternate server.

Our first step is to add a second server. We can use the Binary Star pattern from [Chapter 4 - Reliable Request-Reply Patterns](http://zguide.zeromq.org/page:all#reliable-request-reply) to organize these into primary and backup. Binary Star is a reactor, so it's useful that we already refactored the last server model into a reactor style.

We need to ensure that updates are not lost if the primary server crashes. The simplest technique is to send them to both servers. The backup server can then act as a client, and keep its state synchronized by receiving updates as all clients do. It'll also get new updates from clients. It can't yet store these in its hash table, but it can hold onto them for a while.

So, Model Six introduces the following changes over Model Five:

- We use a pub-sub flow instead of a push-pull flow for client updates sent to the servers. This takes care of fanning out the updates to both servers. Otherwise we'd have to use two DEALER sockets.

- We add heartbeats to server updates (to clients), so that a client can detect when the primary server has died. It can then switch over to the backup server.

- We connect the two servers using the Binary Star `bstar` reactor class. Binary Star relies on the clients to vote by making an explicit request to the server they consider active. We'll use snapshot requests as the voting mechanism.

- We make all update messages uniquely identifiable by adding a UUID field. The client generates this, and the server propagates it back on republished updates.

- The passive server keeps a "pending list" of updates that it has received from clients, but not yet from the active server; or updates it's received from the active server, but not yet from the clients. The list is ordered from oldest to newest, so that it is easy to remove updates off the head.

**Figure 61 - Clone Client Finite State Machine**

![fig61.png](https://github.com/imatix/zguide/raw/master/images/fig61.png)

It's useful to design the client logic as a finite state machine. The client cycles through three states:

- The client opens and connects its sockets, and then requests a snapshot from the first server. To avoid request storms, it will ask any given server only twice. One request might get lost, which would be bad luck. Two getting lost would be carelessness.

- The client waits for a reply (snapshot data) from the current server, and if it gets it, it stores it. If there is no reply within some timeout, it fails over to the next server.

- When the client has gotten its snapshot, it waits for and processes updates. Again, if it doesn't hear anything from the server within some timeout, it fails over to the next server.

The client loops forever. It's quite likely during startup or failover that some clients may be trying to talk to the primary server while others are trying to talk to the backup server. The Binary Star state machine handles this, hopefully accurately. It's hard to prove software correct; instead we hammer it until we can't prove it wrong.

Failover happens as follows:

- The client detects that primary server is no longer sending heartbeats, and concludes that it has died. The client connects to the backup server and requests a new state snapshot.

- The backup server starts to receive snapshot requests from clients, and detects that primary server has gone, so it takes over as primary.

- The backup server applies its pending list to its own hash table, and then starts to process state snapshot requests.

When the primary server comes back online, it will:

- Start up as passive server, and connect to the backup server as a Clone client.

- Start to receive updates from clients, via its SUB socket.

We make a few assumptions:

- At least one server will keep running. If both servers crash, we lose all server state and there's no way to recover it.

- Multiple clients do not update the same hash keys at the same time. Client updates will arrive at the two servers in a different order. Therefore, the backup server may apply updates from its pending list in a different order than the primary server would or did. Updates from one client will always arrive in the same order on both servers, so that is safe.

Thus the architecture for our high-availability server pair using the Binary Star pattern has two servers and a set of clients that talk to both servers.

**Figure 62 - High-availability Clone Server Pair**

![fig62.png](https://github.com/imatix/zguide/raw/master/images/fig62.png)

Here is the sixth and last model of the Clone server:

[clonesrv6: Clone server, Model Six in C](javascript:;)


[Java](http://zguide.zeromq.org/java:clonesrv6) | [Python](http://zguide.zeromq.org/py:clonesrv6) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

The reactor-based design removes a lot of the grunt work from the code, and what remains is simpler and easier to understand. We reuse the bstar reactor from [Chapter 4 - Reliable Request-Reply Patterns](http://zguide.zeromq.org/page:all#reliable-request-reply). The whole server runs as one thread, so there's no inter-thread weirdness going on—just a structure pointer (`self`) passed around to all handlers, which can do their thing happily. One nice side effect of using reactors is that the code, being less tightly integrated into a poll loop, is much easier to reuse. Large chunks of Model Six are taken from Model Five.

I built it piece by piece, and got each piece working *properly* before going onto the next one. Because there are four or five main socket flows, that meant quite a lot of debugging and testing. I debugged just by dumping messages to the console. Don't use classic debuggers to step through ZeroMQ applications; you need to see the message flows to make any sense of what is going on.

For testing, I always try to use Valgrind, which catches memory leaks and invalid memory accesses. In C, this is a major concern, as you can't delegate to a garbage collector. Using proper and consistent abstractions like kvmsg and CZMQ helps enormously.



| [The Clustered Hashmap Protocol](http://zguide.zeromq.org/page:all#The-Clustered-Hashmap-Protocol) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-128) [next](http://zguide.zeromq.org/page:all#header-130) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

While the server is pretty much a mashup of the previous model plus the Binary Star pattern, the client is quite a lot more complex. But before we get to that, let's look at the final protocol. I've written this up as a specification on the ZeroMQ RFC website as the [Clustered Hashmap Protocol](http://rfc.zeromq.org/spec:12).

Roughly, there are two ways to design a complex protocol such as this one. One way is to separate each flow into its own set of sockets. This is the approach we used here. The advantage is that each flow is simple and clean. The disadvantage is that managing multiple socket flows at once can be quite complex. Using a reactor makes it simpler, but still, it makes a lot of moving pieces that have to fit together correctly.

The second way to make such a protocol is to use a single socket pair for everything. In this case, I'd have used ROUTER for the server and DEALER for the clients, and then done everything over that connection. It makes for a more complex protocol but at least the complexity is all in one place. In [Advanced Architecture using ZeroMQ](http://zguide.zeromq.org/page:all#advanced-architecture) we'll look at an example of a protocol done over a ROUTER-DEALER combination.

Let's take a look at the CHP specification. Note that "SHOULD", "MUST" and "MAY" are key words we use in protocol specifications to indicate requirement levels.

**Goals**

CHP is meant to provide a basis for reliable pub-sub across a cluster of clients connected over a ZeroMQ network. It defines a "hashmap" abstraction consisting of key-value pairs. Any client can modify any key-value pair at any time, and changes are propagated to all clients. A client can join the network at any time.

**Architecture**

CHP connects a set of client applications and a set of servers. Clients connect to the server. Clients do not see each other. Clients can come and go arbitrarily.

**Ports and Connections**

The server MUST open three ports as follows:

- A SNAPSHOT port (ZeroMQ ROUTER socket) at port number P.
- A PUBLISHER port (ZeroMQ PUB socket) at port number P + 1.
- A COLLECTOR port (ZeroMQ SUB socket) at port number P + 2.

The client SHOULD open at least two connections:

- A SNAPSHOT connection (ZeroMQ DEALER socket) to port number P.
- A SUBSCRIBER connection (ZeroMQ SUB socket) to port number P + 1.

The client MAY open a third connection, if it wants to update the hashmap:

- A PUBLISHER connection (ZeroMQ PUB socket) to port number P + 2.

This extra frame is not shown in the commands explained below.

**State Synchronization**

The client MUST start by sending a ICANHAZ command to its snapshot connection. This command consists of two frames as follows:

```
ICANHAZ command
-----------------------------------
Frame 0: "ICANHAZ?"
Frame 1: subtree specification
```

Both frames are ZeroMQ strings. The subtree specification MAY be empty. If not empty, it consists of a slash followed by one or more path segments, ending in a slash.

The server MUST respond to a ICANHAZ command by sending zero or more KVSYNC commands to its snapshot port, followed with a KTHXBAI command. The server MUST prefix each command with the identity of the client, as provided by ZeroMQ with the ICANHAZ command. The KVSYNC command specifies a single key-value pair as follows:

```
KVSYNC command
-----------------------------------
Frame 0: key, as ZeroMQ string
Frame 1: sequence number, 8 bytes in network order
Frame 2: <empty>
Frame 3: <empty>
Frame 4: value, as blob
```

The sequence number has no significance and may be zero.

The KTHXBAI command takes this form:

```
KTHXBAI command
-----------------------------------
Frame 0: "KTHXBAI"
Frame 1: sequence number, 8 bytes in network order
Frame 2: <empty>
Frame 3: <empty>
Frame 4: subtree specification
```

The sequence number MUST be the highest sequence number of the KVSYNC commands previously sent.

When the client has received a KTHXBAI command, it SHOULD start to receive messages from its subscriber connection and apply them.

**Server-to-Client Updates**

When the server has an update for its hashmap it MUST broadcast this on its publisher socket as a KVPUB command. The KVPUB command has this form:

```
KVPUB command
-----------------------------------
Frame 0: key, as ZeroMQ string
Frame 1: sequence number, 8 bytes in network order
Frame 2: UUID, 16 bytes
Frame 3: properties, as ZeroMQ string
Frame 4: value, as blob
```

The sequence number MUST be strictly incremental. The client MUST discard any KVPUB commands whose sequence numbers are not strictly greater than the last KTHXBAI or KVPUB command received.

The UUID is optional and frame 2 MAY be empty (size zero). The properties field is formatted as zero or more instances of "name=value" followed by a newline character. If the key-value pair has no properties, the properties field is empty.

If the value is empty, the client SHOULD delete its key-value entry with the specified key.

In the absence of other updates the server SHOULD send a HUGZ command at regular intervals, e.g., once per second. The HUGZ command has this format:

```
HUGZ command
-----------------------------------
Frame 0: "HUGZ"
Frame 1: 00000000
Frame 2: <empty>
Frame 3: <empty>
Frame 4: <empty>
```

The client MAY treat the absence of HUGZ as an indicator that the server has crashed (see Reliability below).

**Client-to-Server Updates**

When the client has an update for its hashmap, it MAY send this to the server via its publisher connection as a KVSET command. The KVSET command has this form:

```
KVSET command
-----------------------------------
Frame 0: key, as ZeroMQ string
Frame 1: sequence number, 8 bytes in network order
Frame 2: UUID, 16 bytes
Frame 3: properties, as ZeroMQ string
Frame 4: value, as blob
```

The sequence number has no significance and may be zero. The UUID SHOULD be a universally unique identifier, if a reliable server architecture is used.

If the value is empty, the server MUST delete its key-value entry with the specified key.

The server SHOULD accept the following properties:

- `ttl`: specifies a time-to-live in seconds. If the KVSET command has a `ttl` property, the server SHOULD delete the key-value pair and broadcast a KVPUB with an empty value in order to delete this from all clients when the TTL has expired.

**Reliability**

CHP may be used in a dual-server configuration where a backup server takes over if the primary server fails. CHP does not specify the mechanisms used for this failover but the Binary Star pattern may be helpful.

To assist server reliability, the client MAY:

- Set a UUID in every KVSET command.
- Detect the lack of HUGZ over a time period and use this as an indicator that the current server has failed.
- Connect to a backup server and re-request a state synchronization.

**Scalability and Performance**

CHP is designed to be scalable to large numbers (thousands) of clients, limited only by system resources on the broker. Because all updates pass through a single server, the overall throughput will be limited to some millions of updates per second at peak, and probably less.

**Security**

CHP does not implement any authentication, access control, or encryption mechanisms and should not be used in any deployment where these are required.



| [Building a Multithreaded Stack and API](http://zguide.zeromq.org/page:all#Building-a-Multithreaded-Stack-and-API) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-129) [next](http://zguide.zeromq.org/page:all#header-131) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The client stack we've used so far isn't smart enough to handle this protocol properly. As soon as we start doing heartbeats, we need a client stack that can run in a background thread. In the Freelance pattern at the end of [Chapter 4 - Reliable Request-Reply Patterns](http://zguide.zeromq.org/page:all#reliable-request-reply)we used a multithreaded API but didn't explain it in detail. It turns out that multithreaded APIs are quite useful when you start to make more complex ZeroMQ protocols like CHP.

**Figure 63 - Multithreaded API**

![fig63.png](https://github.com/imatix/zguide/raw/master/images/fig63.png)

If you make a nontrivial protocol and you expect applications to implement it properly, most developers will get it wrong most of the time. You're going to be left with a lot of unhappy people complaining that your protocol is too complex, too fragile, and too hard to use. Whereas if you give them a simple API to call, you have some chance of them buying in.

Our multithreaded API consists of a frontend object and a background agent, connected by two PAIR sockets. Connecting two PAIR sockets like this is so useful that your high-level binding should probably do what CZMQ does, which is package a "create new thread with a pipe that I can use to send messages to it" method.

The multithreaded APIs that we see in this book all take the same form:

- The constructor for the object (`clone_new`) creates a context and starts a background thread connected with a pipe. It holds onto one end of the pipe so it can send commands to the background thread.

- The background thread starts an *agent* that is essentially a `zmq_poll` loop reading from the pipe socket and any other sockets (here, the DEALER and SUB sockets).

- The main application thread and the background thread now communicate only via ZeroMQ messages. By convention, the frontend sends string commands so that each method on the class turns into a message sent to the backend agent, like this:

void
clone_connect (clone_t *self, char *address, char *service)
{
`    `assert (self);
`    `zmsg_t *msg = zmsg_new ();
`    `zmsg_addstr (msg, "CONNECT");
`    `zmsg_addstr (msg, address);
`    `zmsg_addstr (msg, service);
`    `zmsg_send (&msg, self->pipe);
}

- If the method needs a return code, it can wait for a reply message from the agent.

- If the agent needs to send asynchronous events back to the frontend, we add a `recv`method to the class, which waits for messages on the frontend pipe.

- We may want to expose the frontend pipe socket handle to allow the class to be integrated into further poll loops. Otherwise any `recv` method would block the application.

The clone class has the same structure as the `flcliapi` class from [Chapter 4 - Reliable Request-Reply Patterns](http://zguide.zeromq.org/page:all#reliable-request-reply) and adds the logic from the last model of the Clone client. Without ZeroMQ, this kind of multithreaded API design would be weeks of really hard work. With ZeroMQ, it was a day or two of work.

The actual API methods for the clone class are quite simple:

*//  Create a new clone class instance*
clone_t *
`    `clone_new (void);

*//  Destroy a clone class instance*
void
`    `clone_destroy (clone_t **self_p);

*//  Define the subtree, if any, for this clone class*
void
`    `clone_subtree (clone_t *self, char *subtree);

*//  Connect the clone class to one server*
void
`    `clone_connect (clone_t *self, char *address, char *service);

*//  Set a value in the shared hashmap*
void
`    `clone_set (clone_t *self, char *key, char *value, int ttl);

*//  Get a value from the shared hashmap*
char *
`    `clone_get (clone_t *self, char *key);

So here is Model Six of the clone client, which has now become just a thin shell using the clone class:

[clonecli6: Clone client, Model Six in C](javascript:;)


[Java](http://zguide.zeromq.org/java:clonecli6) | [Python](http://zguide.zeromq.org/py:clonecli6) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

- The server state router (ROUTER) is at port P.
- The server updates publisher (PUB) is at port P + 1.
- The server updates subscriber (SUB) is at port P + 2.

So we can fold the three connections into one logical operation (which we implement as three separate ZeroMQ connect calls).

Let's end with the source code for the clone stack. This is a complex piece of code, but easier to understand when you break it into the frontend object class and the backend agent. The frontend sends string commands ("SUBTREE", "CONNECT", "SET", "GET") to the agent, which handles these commands as well as talking to the server(s). Here is the agent's logic:

1. Start up by getting a snapshot from the first server
2. When we get a snapshot switch to reading from the subscriber socket.
3. If we don't get a snapshot then fail over to the second server.
4. Poll on the pipe and the subscriber socket.
5. If we got input on the pipe, handle the control message from the frontend object.
6. If we got input on the subscriber, store or apply the update.
7. If we didn't get anything from the server within a certain time, fail over.
8. Repeat until the process is interrupted by Ctrl-C.

And here is the actual clone class implementation:

[clone: Clone class in C](javascript:;)


[Java](http://zguide.zeromq.org/java:clone) | [Python](http://zguide.zeromq.org/py:clone) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

| [Chapter 6 - The ZeroMQ Community](http://zguide.zeromq.org/page:all#Chapter-The-ZeroMQ-Community) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-130) [next](http://zguide.zeromq.org/page:all#header-132) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

People sometimes ask me what's so special about ZeroMQ. My standard answer is that ZeroMQ is arguably the best answer we have to the vexing question of "How do we make the distributed software that the 21st century demands?" But more than that, ZeroMQ is special because of its community. This is ultimately what separates the wolves from the sheep.

There are three main open source patterns. The first is the large firm dumping code to break the market for others. This is the Apache Foundation model. The second is tiny teams or small firms building their dream. This is the most common open source model, which can be very successful commercially. The last is aggressive and diverse communities that swarm over a problem landscape. This is the Linux model, and the one to which we aspire with ZeroMQ.

It's hard to overemphasize the power and persistence of a working open source community. There really does not seem to be a better way of making software for the long term. Not only does the community choose the best problems to solve, it solves them minimally, carefully, and it then looks after these answers for years, decades, until they're no longer relevant, and then it quietly puts them away.

To really benefit from ZeroMQ, you need to understand the community. At some point down the road you'll want to submit a patch, an issue, or an add-on. You might want to ask someone for help. You will probably want to bet a part of your business on ZeroMQ, and when I tell you that the community is much, much more important than the company that backs the product, even though I'm CEO of that company, this should be significant.

In this chapter I'm going to look at our community from several angles and conclude by explaining in detail our contract for collaboration, which [we call "C4"](http://rfc.zeromq.org/spec:22). You should find the discussion useful for your own work. We've also adapted the ZeroMQ C4 process for closed source projects with good success.

We'll cover:

- The rough structure of ZeroMQ as a set of projects
- What "software architecture" is really about
- Why we use the LGPL and not the BSD license
- How we designed and grew the ZeroMQ community
- The business that backs ZeroMQ
- Who owns the ZeroMQ source code
- How to make and submit a patch to ZeroMQ
- Who controls what patches actually go into ZeroMQ
- How we guarantee compatibility with old code
- Why we don't use public git branches
- Who decides on the ZeroMQ road map
- A worked example of a change to `libzmq`



| [Architecture of the ZeroMQ Community](http://zguide.zeromq.org/page:all#Architecture-of-the-ZeroMQ-Community) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-131) [next](http://zguide.zeromq.org/page:all#header-133) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

You know that ZeroMQ is an LGPL-licensed project. In fact it's a collection of projects, built around the core library, `libzmq`. I'll visualize these projects as an expanding galaxy:

- At the core, `libzmq` is the ZeroMQ core library. It's written in C++, with a low-level C API. The code is nasty, mainly because it's highly optimized but also because it's written in C++, a language that lends itself to subtle and deep nastiness. Martin Sustrik wrote the bulk of this code. Today it has dozens of people who maintain different parts of it.

- Around `libzmq`, there are about 50 *bindings*. These are individual projects that create higher-level APIs for ZeroMQ, or at least map the low-level API into other languages. The bindings vary in quality from experimental to utterly awesome. Probably the most impressive binding is [PyZMQ](https://github.com/zeromq/pyzmq), which was one of the first community projects on top of ZeroMQ. If you are a binding author, you should really study PyZMQ and aspire to making your code and community as great.

- A lot of languages have multiple bindings (Erlang, Ruby, C#, at least) written by different people over time, or taking varying approaches. We don't regulate these in any way. There are no "official" bindings. You vote by using one or the other, contributing to it, or ignoring it.

- There are a series of reimplementations of `libzmq`, starting with JeroMQ, a full Java translation of the library, which is now the basis for NetMQ, a C# stack. These native stacks offer similar or identical APIs, and speak the same protocol (ZMTP) as `libzmq`.

- On top of the bindings are a lot of projects that use ZeroMQ or build on it. See the "Labs" page on the wiki for a long list of projects and proto-projects that use ZeroMQ in some way. There are frameworks, web servers like Mongrel2, brokers like Majordomo, and enterprise open source tools like Storm.

`Libzmq`, most of the bindings, and some of the outer projects sit in the [ZeroMQ community "organization"](https://github.com/organizations/zeromq) on GitHub. This organization is "run" by a group consisting of the most senior binding authors. There's very little to run as it's almost all self-managing and there's zero conflict these days.

iMatix, my firm, plays a specific role in the community. We own the trademarks and enforce them discretely in order to make sure that if you download a package calling itself "ZeroMQ", you can trust what you are getting. People have on rare occasion tried to hijack the name, maybe believing that "free software" means there is no property at stake and no one willing to defend it. One thing you'll understand from this chapter is how seriously we take the process behind our software (and I mean "us" as a community, not a company). iMatix backs the community by enforcing that process on anything calling itself "ZeroMQ" or "ZeroMQ". We also put money and time into the software and packaging for reasons I'll explain later.

It is not a charity exercise. ZeroMQ is a for-profit project, and a very profitable one. The profits are widely distributed among all those who invest in it. It's really that simple: take the time to become an expert in ZeroMQ, or build something useful on top of ZeroMQ, and you'll find your value as an individual, or team, or company increasing. iMatix enjoys the same benefits as everyone else in the community. It's win-win to everyone except our competitors, who find themselves facing a threat they can't beat and can't really escape. ZeroMQ dominates the future world of massively distributed software.

My firm doesn't just have the community's back—we also built the community. This was deliberate work; in the original ZeroMQ white paper from 2007, there were two projects. One was technical, how to make a better messaging system. The second was how to build a community that could take the software to dominant success. Software dies, but community survives.



| [How to Make Really Large Architectures](http://zguide.zeromq.org/page:all#How-to-Make-Really-Large-Architectures) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-132) [next](http://zguide.zeromq.org/page:all#header-134) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

There are, it has been said (at least by people reading this sentence out loud), two ways to make really large-scale software. Option One is to throw massive amounts of money and problems at empires of smart people, and hope that what emerges is not yet another career killer. If you're very lucky and are building on lots of experience, have kept your teams solid, and are not aiming for technical brilliance, and are furthermore incredibly lucky, it works.

But gambling with hundreds of millions of others' money isn't for everyone. For the rest of us who want to build large-scale software, there's Option Two, which is open source, and more specifically, *free software*. If you're asking how the choice of software license is relevant to the scale of the software you build, that's the right question.

The brilliant and visionary Eben Moglen once said, roughly, that a free software license is the contract on which a community builds. When I heard this, about ten years ago, the idea came to me—*Can we deliberately grow free software communities*?

Ten years later, the answer is "yes", and there is almost a science to it. I say "almost" because we don't yet have enough evidence of people doing this deliberately with a documented, reproducible process. It is what I'm trying to do with [Social Architecture](http://cultureandempire.com/cande.html#/4/6). ZeroMQ came after Wikidot, after the [Digital Standards Organization](http://www.digistan.org/) (Digistan) and after the [Foundation for a Free Information Infrastructure](http://www.ffii.org/) (aka the FFII, an NGO that fights against software patents). This all came after a lot of less successful community projects like Xitami and Libero. My main takeaway from a long career of projects of every conceivable format is: if you want to build truly large-scale and long-lasting software, aim to build a free software community.



| [Psychology of Software Architecture](http://zguide.zeromq.org/page:all#Psychology-of-Software-Architecture) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-133) [next](http://zguide.zeromq.org/page:all#header-135) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Dirkjan Ochtman pointed me to [Wikipedia's definition of Software Architecture](http://en.wikipedia.org/wiki/Software_architecture) as "the set of structures needed to reason about the system, which comprise software elements, relations among them, and properties of both". For me this vapid and circular jargon is a good example of how miserably little we understand what actually makes a successful large scale software architecture.

Architecture is the art and science of making large artificial structures for human use. If there is one thing I've learned and applied successfully in 30 years of making larger and larger software systems, it is this: *software is about people*. Large structures in themselves are meaningless. It's how they function for *human use* that matters. And in software, human use starts with the programmers who make the software itself.

The core problems in software architecture are driven by human psychology, not technology. There are many ways our psychology affects our work. I could point to the way teams seem to get stupider as they get larger or when they have to work across larger distances. Does that mean the smaller the team, the more effective? How then does a large global community like ZeroMQ manage to work successfully?

The ZeroMQ community wasn't accidental. It was a deliberate design, my contribution to the early days when the code came out of a cellar in Bratislava. The design was based on my pet science of "Social Architecture", which [Wikipedia defines](http://en.wikipedia.org/wiki/Social_architecture) as "the conscious design of an environment that encourages a desired range of social behaviors leading towards some goal or set of goals." I define this as more specifically as "the process, and the product, of planning, designing, and growing an online community."

One of the tenets of Social Architecture is that *how we organize* is more significant than *who we are*. The same group, organized differently, can produce wholly different results. We are like peers in a ZeroMQ network, and our communication patterns have a dramatic impact on our performance. Ordinary people, well connected, can far outperform a team of experts using poor patterns. If you're the architect of a larger ZeroMQ application, you're going to have to help others find the right patterns for working together. Do this right, and your project can succeed. Do it wrong, and your project will fail.

The two most important psychological elements are that we're really bad at understanding complexity and that we are so good at working together to divide and conquer large problems. We're highly social apes, and kind of smart, but only in the right kind of crowd.

So here is my short list of the Psychological Elements of Software Architecture:

- **Stupidity**: our mental bandwidth is limited, so we're all stupid at some point. The architecture has to be simple to understand. This is the number one rule: simplicity beats functionality, every single time. If you can't understand an architecture on a cold gray Monday morning before coffee, it is too complex.

- **Selfishness**: we act only out of self-interest, so the architecture must create space and opportunity for selfish acts that benefit the whole. Selfishness is often indirect and subtle. For example, I'll spend hours helping someone else understand something because that could be worth days to me later.

- **Laziness**: we make lots of assumptions, many of which are wrong. We are happiest when we can spend the least effort to get a result or to test an assumption quickly, so the architecture has to make this possible. Specifically, that means it must be simple.

- **Jealousy**: we're jealous of others, which means we'll overcome our stupidity and laziness to prove others wrong and beat them in competition. The architecture thus has to create space for public competition based on fair rules that anyone can understand.

- **Fear**: we're unwilling to take risks, especially if it makes us look stupid. Fear of failure is a major reason people conform and follow the group in mass stupidity. The architecture should make silent experimentation easy and cheap, giving people opportunity for success without punishing failure.

- **Reciprocity**: we'll pay extra in terms of hard work, even money, to punish cheats and enforce fair rules. The architecture should be heavily rule-based, telling people how to work together, but not what to work on.

- **Conformity**: we're happiest to conform, out of fear and laziness, which means if the patterns are good, clearly explained and documented, and fairly enforced, we'll naturally choose the right path every time.

- **Pride**: we're intensely aware of our social status, and we'll work hard to avoid looking stupid or incompetent in public. The architecture has to make sure every piece we make has our name on it, so we'll have sleepless nights stressing about what others will say about our work.

- **Greed**: we're ultimately economic animals (see selfishness), so the architecture has to give us economic incentive to invest in making it happen. Maybe it's polishing our reputation as experts, maybe it's literally making money from some skill or component. It doesn't matter what it is, but there must be economic incentive. Think of architecture as a market place, not an engineering design.

These strategies work on a large scale but also on a small scale, within an organization or team.



| [The Importance of Contracts](http://zguide.zeromq.org/page:all#The-Importance-of-Contracts) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-134) [next](http://zguide.zeromq.org/page:all#header-136) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Let me discuss a contentious but important area, which is what license to choose. I'll say "BSD" to cover MIT, X11, BSD, Apache, and similar licenses, and "GPL" to cover GPLv3, LGPLv3, and AGPLv3. The significant difference is the obligation to share back any forked versions, which prevents any entity from capturing the software, and thus keeps it "free".

A software license isn't technically a contract since you don't sign anything. But broadly, calling it a contract is useful since it takes the obligations of each party, and makes them legally enforceable in court, under copyright law.

You might ask, why do we need contracts at all to make open source? Surely it's all about decency, goodwill, people working together for selfless motives. Surely the principle of "less is more" applies here of all places? Don't more rules mean less freedom? Do we really need lawyers to tell us how to work together? It seems cynical and even counter-productive to force a restrictive set of rules on the happy communes of free and open source software.

But the truth about human nature is not that pretty. We're not really angels, nor devils, just self-interested winners descended from a billion-year unbroken line of winners. In business, marriage, and collective works, sooner or later, we either stop caring, or we fight and we argue.

Put this another way: a collective work has two extreme outcomes. Either it's a failure, irrelevant, and worthless, in which case every sane person walks away, without a fight. Or, it's a success, relevant, and valuable, in which case we start jockeying for power, control, and often, money.

What a well-written contract does is to protect those valuable relationships from conflict. A marriage where the terms of divorce are clearly agreed up-front is much less likely to end in divorce. A business deal where both parties agree how to resolve various classic conflicts—such as one party stealing the others' clients or staff—is much less likely to end in conflict.

Similarly, a software project that has a well-written contract that defines the terms of breakup clearly is much less likely to end in breakup. The alternative seems to be to immerse the project into a larger organization that can assert pressure on teams to work together (or lose the backing and branding of the organization). This is for example how the Apache Foundation works. In my experience organization building has its own costs, and ends up favoring wealthier participants (who can afford those sometimes huge costs).

In an open source or free software project, breakup usually takes the form of a fork, where the community splits into two or more groups, each with different visions of the future. During the honeymoon period of a project, which can last years, there's no question of a breakup. It is as a project begins to be worth money, or as the main authors start to burn out, that the goodwill and generosity tends to dry up.

So when discussing software licenses, for the code you write or the code you use, a little cynicism helps. Ask yourself, not "which license will attract more contributors?" because the answer to that lies in the mission statement and contribution process. Ask yourself, "if this project had a big fight, and split three ways, which license would save us?" Or, "if the whole team was bought by a hostile firm that wanted to turn this code into a proprietary product, which license would save us?"

Long-term survival means enduring the bad times, as well as enjoying the good ones.

When BSD projects fork, they cannot easily merge again. Indeed, one-way forking of BSD projects is quite systematic: every time BSD code ends up in a commercial project, this is what's happened. When GPL projects fork, however, re-merging is trivial.

The GPL's story is relevant here. Though communities of programmers sharing their code openly were already significant by the 1980's, they tended to use minimal licenses that worked as long as no real money got involved. There was an important language stack called Emacs, originally built in Lisp by Richard Stallman. Another programmer, James Gosling (who later gave us Java), rewrote Emacs in C with the help of many contributors, on the assumption that it would be open. Stallman got that code and used it as the basis for his own C version. Gosling then sold the code to a firm which turned around and blocked anyone distributing a competing product. Stallman found this sale of the common work hugely unethical, and began developing a reusable license that would protect communities from this.

What eventually emerged was the GNU General Public License, which used traditional copyright to force remixability. It was a neat hack that spread to other domains, for instance the Creative Commons for photography and music. In 2007, we saw version 3 of the license, which was a response to belated attacks from Microsoft and others on the concept. It has become a long and complex document but corporate copyright lawyers have become familiar with it and in my experience, few companies mind using GPL software and libraries, so long as the boundaries are clearly defined.

Thus, a good contract—and I consider the modern GPL to be the best for software—lets programmers work together without upfront agreements, organizations, or assumptions of decency and goodwill. It makes it cheaper to collaborate, and turns conflict into healthy competition. GPL doesn't just define what happens with a fork, it actively encourages forks as a tool for experimentation and learning. Whereas a fork can kill a project with a "more liberal" license, GPL projects thrive on forks since successful experiments can, by contract, be remixed back into the mainstream.

Yes, there are many thriving BSD projects and many dead GPL ones. It's always wrong to generalize. A project will thrive or die for many reasons. However, in a competitive sport, one needs every advantage.

The other important part of the BSD vs. GPL story is what I call "leakage", which is the effect of pouring water into a pot with a small but real hole in the bottom.



| [Eat Me](http://zguide.zeromq.org/page:all#Eat-Me) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-135) [next](http://zguide.zeromq.org/page:all#header-137) |
| -------------------------------------------------- | ------------------------------------------------------------ |
|                                                    |                                                              |

Here is a story. It happened to the eldest brother-in-law of the cousin of a friend of mine's colleague at work. His name was, and still is, Patrick.

Patrick was a computer scientist with a PhD in advanced network topologies. He spent two years and his savings building a new product, and choose the BSD license because he believed that would get him more adoption. He worked in his attic, at great personal cost, and proudly published his work. People applauded, for it was truly fantastic, and his mailing lists were soon abuzz with activity and patches and happy chatter. Many companies told him how they were saving millions using his work. Some of them even paid him for consultancy and training. He was invited to speak at conferences and started collecting badges with his name on them. He started a small business, hired a friend to work with him, and dreamed of making it big.

Then one day, someone pointed him to a new project, GPL licensed, which had forked his work and was improving on it. He was irritated and upset, and asked how people—fellow open sourcers, no less!—would so shamelessly steal his code. There were long arguments on the list about whether it was even legal to relicense their BSD code as GPL code. Turned out, it was. He tried to ignore the new project, but then he soon realized that new patches coming from that project *couldn't even be merged back* into his work!

Worse, the GPL project got popular and some of his core contributors made first small, and then larger patches to it. Again, he couldn't use those changes, and he felt abandoned. Patrick went into a depression, his girlfriend left him for an international currency dealer called, weirdly, Patrice, and he stopped all work on the project. He felt betrayed, and utterly miserable. He fired his friend, who took it rather badly and told everyone that Patrick was a closet banjo player. Finally, Patrick took a job as a project manager for a cloud company, and by the age of forty, he had stopped programming even for fun.

Poor Patrick. I almost felt sorry for him. Then I asked him, "Why didn't you choose the GPL?" "Because it's a restrictive viral license", he replied. I told him, "You may have a PhD, and you may be the eldest brother-in-law of the cousin of a friend of my colleague, but you are an idiot and Monique was smart to leave you. You published your work inviting people to please steal your code as long as they kept this 'please steal my code' statement in the resulting work", and when people did exactly that, you got upset. Worse, you were a hypocrite because when they did it in secret, you were happy, but when they did it openly, you felt betrayed."

Seeing your hard work captured by a smarter team and then used against you is enormously painful, so why even make that possible? Every proprietary project that uses BSD code is capturing it. A public GPL fork is perhaps more humiliating, but it's fully self-inflicted.

BSD is like food. It literally (and I mean that metaphorically) whispers "eat me" in the little voice one imagines a cube of cheese might use when it's sitting next to an empty bottle of the best beer in the world, which is of course Orval, brewed by an ancient and almost extinct order of silent Belgian monks called *Les Gars Labas Qui Fabrique l'Orval*. The BSD license, like its near clone MIT/X11, was designed specifically by a university (Berkeley) with no profit motive to leak work and effort. It is a way to push subsidized technology at below its cost price, a dumping of under-priced code in the hope that it will break the market for others. BSD is an *excellent* strategic tool, but only if you're a large well-funded institution that can afford to use Option One. The Apache license is BSD in a suit.

For us small businesses who aim our investments like precious bullets, leaking work and effort is unacceptable. Breaking the market is great, but we cannot afford to subsidize our competitors. The BSD networking stack ended up putting Windows on the Internet. We cannot afford battles with those we should naturally be allies with. We cannot afford to make fundamental business errors because in the end, that means we have to fire people.

It comes down to behavioral economics and game theory. *The license we choose modifies the economics of those who use our work*. In the software industry, there are friends, foes, and food. BSD makes most people see us as lunch. Closed source makes most people see us as enemies (do you *like* paying people for software?) GPL, however, makes most people, with the exception of the Patricks of the world, our allies. Any fork of ZeroMQ is license compatible with ZeroMQ, to the point where we *encourage* forks as a valuable tool for experimentation. Yes, it can be weird to see someone try to run off with the ball but here's the secret, *I can get it back any time I want.*



| [The Process](http://zguide.zeromq.org/page:all#The-Process) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-136) [next](http://zguide.zeromq.org/page:all#header-138) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

If you've accepted my thesis up to now, great! Now, I'll explain the rough process by which we actually build an open source community. This was how we built or grew or gently steered the ZeroMQ community into existence.

Your goal as leader of a community is to motivate people to get out there and explore; to ensure they can do so safely and without disturbing others; to reward them when they make successful discoveries; and to ensure they share their knowledge with everyone else (and not because we ask them, not because they feel generous, but because it's The Law).

It is an iterative process. You make a small product, at your own cost, but in public view. You then build a small community around that product. If you have a small but real hit, the community then helps design and build the next version, and grows larger. And then that community builds the next version, and so on. It's evident that you remain part of the community, maybe even a majority contributor, but the more control you try to assert over the material results, the less people will want to participate. Plan your own retirement well before someone decides you are their next problem.



| [Crazy, Beautiful, and Easy](http://zguide.zeromq.org/page:all#Crazy-Beautiful-and-Easy) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-137) [next](http://zguide.zeromq.org/page:all#header-139) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

You need a goal that's crazy and simple enough to get people out of bed in the morning. Your community has to attract the very best people and that demands something special. With ZeroMQ, we said we were going to make "the Fastest. Messaging. Ever.", which qualifies as a good motivator. If we'd said, we're going to make "a smart transport layer that'll connect your moving pieces cheaply and flexibly across your enterprise", we'd have failed.

Then your work must be beautiful, immediately useful, and attractive. Your contributors are users who want to explore just a little beyond where they are now. Make it simple, elegant, and brutally clean. The experience when people run or use your work should be an emotional one. They should *feel* something, and if you accurately solved even just one big problem that until then they didn't quite realize they faced, you'll have a small part of their soul.

It must be easy to understand, use, and join. Too many projects have barriers to access: put yourself in the other person's mind and see all the reasons they come to your site, thinking "Um, interesting project, but…" and then leave. You want them to stay and try it, just once. Use GitHub and put the issue tracker right there.

If you do these things well, your community will be smart but more importantly, it will be intellectually and geographically diverse. This is really important. A group of like-minded experts cannot explore the problem landscape well. They tend to make big mistakes. Diversity beats education any time.



| [Stranger, Meet Stranger](http://zguide.zeromq.org/page:all#Stranger-Meet-Stranger) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-138) [next](http://zguide.zeromq.org/page:all#header-140) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

How much up-front agreement do two people need to work together on something? In most organizations, a lot. But you can bring this cost down to near-zero, and then people can collaborate without having ever met, done a phone conference, meeting, or business trip to discuss Roles and Responsibilities over way too many bottles of cheap Korean rice wine.

You need well-written rules that are designed by cynical people like me to force strangers into mutually beneficial collaboration instead of conflict. The GPL is a good start. GitHub and its fork/merge strategy is a good follow-up. And then you want something like our [C4 rulebook](http://rfc.zeromq.org/spec:22) to control how work actually happens.

C4 (which I now use for every new open source project) has detailed and tested answers to a lot of common mistakes people make, such as the sin of working offline in a corner with others "because it's faster". Transparency is essential to get trust, which is essential to get scale. By forcing every single change through a single transparent process, you build real trust in the results.

Another cardinal sin that many open source developers make is to place themselves above others. "I founded this project thus my intellect is superior to that of others". It's not just immodest and rude, and usually inaccurate, it's also poor business. The rules must apply equally to everyone, without distinction. You are part of the community. Your job, as founder of a project, is not to impose your vision of the product over others, but to make sure the rules are good, honest, and *enforced*.



| [Infinite Property](http://zguide.zeromq.org/page:all#Infinite-Property) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-139) [next](http://zguide.zeromq.org/page:all#header-141) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

One of the saddest myths of the knowledge business is that ideas are a sensible form of property. It's medieval nonsense that should have been junked along with slavery, but sadly it's still making too many powerful people too much money.

Ideas are cheap. What does work sensibly as property is the hard work we do in building a market. "You eat what you kill" is the right model for encouraging people to work hard. Whether it's moral authority over a project, money from consulting, or the sale of a trademark to some large, rich firm: if you make it, you own it. But what you really own is "footfall", participants in your project, which ultimately defines your power.

To do this requires infinite free space. Thankfully, GitHub solved this problem for us, for which I will die a grateful person (there are many reasons to be grateful in life, which I won't list here because we only have a hundred or so pages left, but this is one of them).

You cannot scale a single project with many owners like you can scale a collection of many small projects, each with fewer owners. When we embrace forks, a person can become an "owner" with a single click. Now they just have to convince others to join by demonstrating their unique value.

So in ZeroMQ, we aimed to make it easy to write bindings on top of the core library, and we stopped trying to make those bindings ourselves. This created space for others to make those, become their owners, and get that credit.



| [Care and Feeding](http://zguide.zeromq.org/page:all#Care-and-Feeding) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-140) [next](http://zguide.zeromq.org/page:all#header-142) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

I wish a community could be 100% self-steering, and perhaps one day this will work, but today it's not the case. We're very close with ZeroMQ, but from my experience a community needs four types of care and feeding:

- First, simply because most people are too nice, we need some kind of symbolic leadership or owners who provide ultimate authority in case of conflict. Usually it's the founders of the community. I've seen it work with self-elected groups of "elders", but old men like to talk a lot. I've seen communities split over the question "who is in charge?", and setting up legal entities with boards and such seems to make arguments over control worse, not better. Maybe because there seems to be more to fight over. One of the real benefits of free software is that it's always remixable, so instead of fighting over a pie, one simply forks the pie.

- Second, communities need living rules, and thus they need a lawyer able to formulate and write these down. Rules are critical; when done right, they remove friction. When done wrong, or neglected, we see real friction and argument that can drive away the nice majority, leaving the argumentative core in charge of the burning house. One thing I've tried to do with the ZeroMQ and previous communities is create reusable rules, which perhaps means we don't need lawyers as much.

- Thirdly, communities need some kind of financial backing. This is the jagged rock that breaks most ships. If you starve a community, it becomes more creative but the core contributors burn out. If you pour too much money into it, you attract the professionals, who never say "no", and the community loses its diversity and creativity. If you create a fund for people to share, they will fight (bitterly) over it. With ZeroMQ, we (iMatix) spend our time and money on marketing and packaging (like this book), and the basic care, like bug fixes, releases, and websites.

- Lastly, sales and commercial mediation are important. There is a natural market between expert contributors and customers, but both are somewhat incompetent at talking to each other. Customers assume that support is free or very cheap because the software is free. Contributors are shy at asking a fair rate for their work. It makes for a difficult market. A growing part of my work and my firm's profits is simply connecting ZeroMQ users who want help with experts from the community able to provide it, and ensuring both sides are happy with the results.

I've seen communities of brilliant people with noble goals dying because the founders got some or all of these four things wrong. The core problem is that you can't expect consistently great leadership from any one company, person, or group. What works today often won't work tomorrow, yet structures become more solid, not more flexible, over time.

The best answer I can find is a mix of two things. One, the GPL and its guarantee of remixability. No matter how bad the authority, no matter how much they try to privatize and capture the community's work, if it's GPL licensed, that work can walk away and find a better authority. Before you say, "all open source offers this," think it through. I can kill a BSD-licensed project by hiring the core contributors and not releasing any new patches. But even with a billion of dollars, I *cannot* kill a GPL-licensed project. Two, the philosophical anarchist model of authority, which is that we choose it, it does not own us.



| [The ZeroMQ Process: C4](http://zguide.zeromq.org/page:all#The-ZeroMQ-Process-C) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-141) [next](http://zguide.zeromq.org/page:all#header-143) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

When we say ZeroMQ we sometimes mean `libzmq`, the core library. In early 2012, we synthesized the `libzmq` process into a formal protocol for collaboration that we called the [Collective Code Construction Contract](http://rfc.zeromq.org/spec:22), or C4. You can see this as a layer above the GPL. These are our rules, and I'll explain the reasoning behind each one.

C4 is an evolution of the GitHub [Fork + Pull Model](https://help.github.com/articles/about-pull-requests/). You may get the feeling I'm a fan of git and GitHub. This would be accurate: these two tools have made such a positive impact on our work over the last years, especially when it comes to building community.



| [Language](http://zguide.zeromq.org/page:all#Language) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-142) [next](http://zguide.zeromq.org/page:all#header-144) |
| ------------------------------------------------------ | ------------------------------------------------------------ |
|                                                        |                                                              |

> The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in RFC 2119.

By starting with the RFC 2119 language, the C4 text makes very clear its intention to act as a protocol rather than a randomly written set of recommendations. A protocol is a contract between parties that defines the rights and obligations of each party. These can be peers in a network or they can be strangers working in the same project.

I think C4 is the first time anyone has attempted to codify a community's rulebook as a formal and reusable protocol spec. Previously, our rules were spread out over several wiki pages, and were quite specific to `libzmq` in many ways. But experience teaches us that the more formal, accurate, and reusable the rules, the easier it is for strangers to collaborate up-front. And less friction means a more scalable community. At the time of C4, we also had some disagreement in the `libzmq` project over precisely what process we were using. Not everyone felt bound by the same rules. Let's just say some people felt they had a special status, which created friction with the rest of the community. So codification made things clear.

It's easy to use C4: just host your project on GitHub, get one other person to join, and open the floor to pull requests. In your README, put a link to C4 and that's it. We've done this in quite a few projects and it does seem to work. I've been pleasantly surprised a few times just applying these rules to my own work, like CZMQ. None of us are so amazing that we can work without others.



| [Goals](http://zguide.zeromq.org/page:all#Goals) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-143) [next](http://zguide.zeromq.org/page:all#header-145) |
| ------------------------------------------------ | ------------------------------------------------------------ |
|                                                  |                                                              |

> C4 is meant to provide a reusable optimal collaboration model for open source software projects.

The short term reason for writing C4 was to end arguments over the `libzmq` contribution process. The dissenters went off elsewhere. [The ZeroMQ community blossomed](https://github.com/zeromq/libzmq/graphs/contributors) smoothly and easily, as I'd predicted. Most people were surprised, but gratified. There's been no real criticisms of C4 except its branching policy, which I'll come to later as it deserves its own discussion.

There's a reason I'm reviewing history here: as founder of a community, you are asking people to invest in your property, trademark, and branding. In return, and this is what we do with ZeroMQ, you can use that branding to set a bar for quality. When you download a product labeled "ZeroMQ", you know that it's been produced to certain standards. It's a basic rule of quality: write down your process; otherwise you cannot improve it. Our processes aren't perfect, nor can they ever be. But any flaw in them can be fixed, and tested.

Making C4 reusable is therefore really important. To learn more about the best possible process, we need to get results from the widest range of projects.

> It has these specific goals:
> To maximize the scale of the community around a project, by reducing the friction for new Contributors and creating a scaled participation model with strong positive feedbacks;

The number one goal is size and health of the community—not technical quality, not profits, not performance, not market share. The goal is simply the number of people who contribute to the project. The science here is simple: the larger the community, the more accurate the results.

> To relieve dependencies on key individuals by separating different skill sets so that there is a larger pool of competence in any required domain;

Perhaps the worst problem we faced in `libzmq` was dependence on people who could understand the code, manage GitHub branches, and make clean releases—all at the same time. It's like looking for athletes who can run marathons and sprint, swim, and also lift weights. We humans are really good at specialization. Asking us to be really good at two contradictory things reduces the number of candidates sharply, which is a Bad Thing for any project. We had this problem severely in `libzmq` in 2009 or so, and fixed it by splitting the role of maintainer into two: one person makes patches and another makes releases.

> To allow the project to develop faster and more accurately, by increasing the diversity of the decision making process;

This is theory—not fully proven, but not falsified. The diversity of the community and the number of people who can weigh in on discussions, without fear of being criticized or dismissed, the faster and more accurately the software develops. Speed is quite subjective here. Going very fast in the wrong direction is not just useless, it's actively damaging (and we suffered a lot of that in `libzmq` before we switched to C4).

> To support the natural life cycle of project versions from experimental through to stable, by allowing safe experimentation, rapid failure, and isolation of stable code;

To be honest, this goal seems to be fading into irrelevance. It's quite an interesting effect of the process: *the git master is almost always perfectly stable*. This has to do with the size of changes and their *latency*, i.e., the time between someone writing the code and someone actually using it fully. However, people still expect "stable" releases, so we'll keep this goal there for a while.

> To reduce the internal complexity of project repositories, thus making it easier for Contributors to participate and reducing the scope for error;

Curious observation: people who thrive in complex situations like to create complexity because it keeps their value high. It's the Cobra Effect (Google it). Git made branches easy and left us with the all too common syndrome of "git is easy once you understand that a git branch is just a folded five-dimensional lepton space that has a detached history with no intervening cache". Developers should not be made to feel stupid by their tools. I've seen too many top-class developers confused by repository structures to accept conventional wisdom on git branches. We'll come back to dispose of git branches shortly, dear reader.

> To enforce collective ownership of the project, which increases economic incentive to Contributors and reduces the risk of hijack by hostile entities.

Ultimately, we're economic creatures, and the sense that "we own this, and our work can never be used against us" makes it much easier for people to invest in an open source project like ZeroMQ. And it can't be just a feeling, it has to be real. There are a number of aspects to making collective ownership work, we'll see these one-by-one as we go through C4.



| [Preliminaries](http://zguide.zeromq.org/page:all#Preliminaries) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-144) [next](http://zguide.zeromq.org/page:all#header-146) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

> The project SHALL use the git distributed revision control system.

Git has its faults. Its command-line API is horribly inconsistent, and it has a complex, messy internal model that it shoves in your face at the slightest provocation. But despite doing its best to make its users feel stupid, git does its job really, really well. More pragmatically, I've found that if you stay away from certain areas (branches!), people learn git rapidly and don't make many mistakes. That works for me.

> The project SHALL be hosted on github.com or equivalent, herein called the "Platform".

I'm sure one day some large firm will buy GitHub and break it, and another platform will rise in its place. Until then, Github serves up a near-perfect set of minimal, fast, simple tools. I've thrown hundreds of people at it, and they all stick like flies stuck in a dish of honey.

> The project SHALL use the Platform issue tracker.

We made the mistake in `libzmq` of switching to Jira because we hadn't learned yet how to properly use the GitHub issue tracker. Jira is a great example of how to turn something useful into a complex mess because the business depends on selling more "features". But even without criticizing Jira, keeping the issue tracker on the same platform means one less UI to learn, one less login, and smooth integration between issues and patches.

> The project SHOULD have clearly documented guidelines for code style.

This is a protocol plug-in: insert code style guidelines here. If you don't document the code style you use, you have no basis except prejudice to reject patches.

> A "Contributor" is a person who wishes to provide a patch, being a set of commits that solve some clearly identified problem.
> A "Maintainer" is a person who merge patches to the project. Maintainers are not developers; their job is to enforce process.

Now we move on to definitions of the parties, and the splitting of roles that saved us from the sin of structural dependency on rare individuals. This worked well in `libzmq`, but as you will see it depends on the rest of the process. C4 isn't a buffet; you will need the whole process (or something very like it), or it won't hold together.

> Contributors SHALL NOT have commit access to the repository unless they are also Maintainers.
> Maintainers SHALL have commit access to the repository.

What we wanted to avoid was people pushing their changes directly to master. This was the biggest source of trouble in `libzmq` historically: large masses of raw code that took months or years to fully stabilize. We eventually followed other ZeroMQ projects like PyZMQ in using pull requests. We went further, and stipulated that *all* changes had to follow the same path. No exceptions for "special people".

> Everyone, without distinction or discrimination, SHALL have an equal right to become a Contributor under the terms of this contract.

We had to state this explicitly. It used to be that the `libzmq` maintainers would reject patches simply because they didn't like them. Now, that may sound reasonable to the author of a library (though `libzmq` was not written by any one person), but let's remember our goal of creating a work that is owned by as many people as possible. Saying "I don't like your patch so I'm going to reject it" is equivalent to saying, "I claim to own this and I think I'm better than you, and I don't trust you". Those are toxic messages to give to others who are thinking of becoming your co-investors.

I think this fight between individual expertise and collective intelligence plays out in other areas. It defined Wikipedia, and still does, a decade after that work surpassed anything built by small groups of experts. For me, we make software by slowly synthesizing the most accurate knowledge, much as we make Wikipedia articles.



| [Licensing and Ownership](http://zguide.zeromq.org/page:all#Licensing-and-Ownership) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-145) [next](http://zguide.zeromq.org/page:all#header-147) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

> The project SHALL use the GPLv3 or a variant thereof (LGPL, AGPL).

I've already explained how full remixability creates better scale and why the GPL and its variants seems the optimal contract for remixable software. If you're a large business aiming to dump code on the market, you won't want C4, but then you won't really care about community either.

> All contributions to the project source code ("patches") SHALL use the same license as the project.

This removes the need for any specific license or contribution agreement for patches. You fork the GPL code, you publish your remixed version on GitHub, and you or anyone else can then submit that as a patch to the original code. BSD doesn't allow this. Any work that contains BSD code may also contain unlicensed proprietary code so you need explicit action from the author of the code before you can remix it.

> All patches are owned by their authors. There SHALL NOT be any copyright assignment process.

Here we come to the key reason people trust their investments in ZeroMQ: it's logistically impossible to buy the copyrights to create a closed source competitor to ZeroMQ. iMatix can't do this either. And the more people that send patches, the harder it becomes. ZeroMQ isn't just free and open today—this specific rule means it will remain so forever. Note that it's not the case in all GPL projects, many of which still ask for copyright transfer back to the maintainers.

> The project SHALL be owned collectively by all its Contributors.

This is perhaps redundant, but worth saying: if everyone owns their patches, then the resulting whole is also owned by every contributor. There's no legal concept of owning lines of code: the "work" is at least a source file.

> Each Contributor SHALL be responsible for identifying themselves in the project Contributor list.

In other words, the maintainers are not karma accountants. Anyone who wants credit has to claim it themselves.



| [Patch Requirements](http://zguide.zeromq.org/page:all#Patch-Requirements) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-146) [next](http://zguide.zeromq.org/page:all#header-148) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

In this section, we define the obligations of the contributor: specifically, what constitutes a "valid" patch, so that maintainers have rules they can use to accept or reject patches.

> Maintainers and Contributors MUST have a Platform account and SHOULD use their real names or a well-known alias.

In the worst case scenario, where someone has submitted toxic code (patented, or owned by someone else), we need to be able to trace who and when, so we can remove the code. Asking for real names or a well-known alias is a theoretical strategy for reducing the risk of bogus patches. We don't know if this actually works because we haven't had the problem yet.

> A patch SHOULD be a minimal and accurate answer to exactly one identified and agreed problem.

This implements the Simplicity Oriented Design process that I'll come to later in this chapter. One clear problem, one minimal solution, apply, test, repeat.

> A patch MUST adhere to the code style guidelines of the project if these are defined.

This is just sanity. I've spent time cleaning up other peoples' patches because they insisted on putting the `else` beside the `if` instead of just below as Nature intended. Consistent code is healthier.

> A patch MUST adhere to the "Evolution of Public Contracts" guidelines defined below.

Ah, the pain, the pain. I'm not speaking of the time at age eight when I stepped on a plank with a 4-inch nail protruding from it. That was relatively OK. I'm speaking of 2010-2011 when we had multiple parallel releases of ZeroMQ, each with different *incompatible* APIs or wire protocols. It was an exercise in bad rules, pointlessly enforced, that still hurts us today. The rule was, "If you change the API or protocol, you SHALL create a new major version". Give me the nail through the foot; that hurt less.

One of the big changes we made with C4 was simply to ban, outright, this kind of sanctioned sabotage. Amazingly, it's not even hard. We just don't allow the breaking of existing public contracts, period, unless everyone agrees, in which case no period. As Linus Torvalds famously put it on 23 December 2012, "WE DO NOT BREAK USERSPACE!"

> A patch SHALL NOT include nontrivial code from other projects unless the Contributor is the original author of that code.

This rule has two effects. The first is that it forces people to make minimal solutions because they cannot simply import swathes of existing code. In the cases where I've seen this happen to projects, it's always bad unless the imported code is very cleanly separated. The second is that it avoids license arguments. You write the patch, you are allowed to publish it as LGPL, and we can merge it back in. But you find a 200-line code fragment on the web, and try to paste that, we'll refuse.

> A patch MUST compile cleanly and pass project self-tests on at least the principle target platform.

For cross-platform projects, it is fair to ask that the patch works on the development box used by the contributor.

> A patch commit message SHOULD consist of a single short (less than 50 character) line summarizing the change, optionally followed by a blank line and then a more thorough description.

This is a good format for commit messages that fits into email (the first line becomes the subject, and the rest becomes the email body).

> A "Correct Patch" is one that satisfies the above requirements.

Just in case it wasn't clear, we're back to legalese and definitions.



| [Development Process](http://zguide.zeromq.org/page:all#Development-Process) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-147) [next](http://zguide.zeromq.org/page:all#header-149) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

In this section, we aim to describe the actual development process, step-by-step.

> Change on the project SHALL be governed by the pattern of accurately identifying problems and applying minimal, accurate solutions to these problems.

This is a unapologetic ramming through of thirty years' software design experience. It's a profoundly simple approach to design: make minimal, accurate solutions to real problems, nothing more or less. In ZeroMQ, we don't have feature requests. Treating new features the same as bugs confuses some newcomers. But this process works, and not just in open source. Enunciating the problem we're trying to solve, with every single change, is key to deciding whether the change is worth making or not.

> To initiate changes, a user SHALL log an issue on the project Platform issue tracker.

This is meant to stop us from going offline and working in a ghetto, either by ourselves or with others. Although we tend to accept pull requests that have clear argumentation, this rule lets us say "stop" to confused or too-large patches.

> The user SHOULD write the issue by describing the problem they face or observe.

"Problem: we need feature X. Solution: make it" is not a good issue. "Problem: user cannot do common tasks A or B except by using a complex workaround. Solution: make feature X" is a decent explanation. Because everyone I've ever worked with has needed to learn this, it seems worth restating: document the real problem first, solution second.

> The user SHOULD seek consensus on the accuracy of their observation, and the value of solving the problem.

And because many apparent problems are illusionary, by stating the problem explicitly we give others a chance to correct our logic. "You're only using A and B a lot because function C is unreliable. Solution: make function C work properly."

> Users SHALL NOT log feature requests, ideas, suggestions, or any solutions to problems that are not explicitly documented and provable.

There are several reasons for not logging ideas, suggestions, or feature requests. In our experience, these just accumulate in the issue tracker until someone deletes them. But more profoundly, when we treat all change as problem solutions, we can prioritize trivially. Either the problem is real and someone wants to solve it now, or it's not on the table. Thus, wish lists are off the table.

> Thus, the release history of the project SHALL be a list of meaningful issues logged and solved.

I'd love the GitHub issue tracker to simply list all the issues we solved in each release. Today we still have to write that by hand. If one puts the issue number in each commit, and if one uses the GitHub issue tracker, which we sadly don't yet do for ZeroMQ, this release history is easier to produce mechanically.

> To work on an issue, a Contributor SHALL fork the project repository and then work on their forked repository.

Here we explain the GitHub fork + pull request model so that newcomers only have to learn one process (C4) in order to contribute.

> To submit a patch, a Contributor SHALL create a Platform pull request back to the project.

GitHub has made this so simple that we don't need to learn git commands to do it, for which I'm deeply grateful. Sometimes, I'll tell people who I don't particularly like that command-line git is awesome and all they need to do is learn git's internal model in detail before trying to use it on real work. When I see them several months later they look… changed.

> A Contributor SHALL NOT commit changes directly to the project.

Anyone who submits a patch is a contributor, and all contributors follow the same rules. No special privileges to the original authors, because otherwise we're not building a community, only boosting our egos.

> To discuss a patch, people MAY comment on the Platform pull request, on the commit, or elsewhere.

Randomly distributed discussions may be confusing if you're walking up for the first time, but GitHub solves this for all current participants by sending emails to those who need to follow what's going on. We had the same experience and the same solution in Wikidot, and it works. There's no evidence that discussing in different places has any negative effect.

> To accept or reject a patch, a Maintainer SHALL use the Platform interface.

Working via the GitHub web user interface means pull requests are logged as issues, with workflow and discussion. I'm sure there are more complex ways to work. Complexity is easy; it's simplicity that's incredibly hard.

> Maintainers SHALL NOT accept their own patches.

There was a rule we defined in the FFII years ago to stop people burning out: no less than two people on any project. One-person projects tend to end in tears, or at least bitter silence. We have quite a lot of data on burnout, why it happens, and how to prevent it (even cure it). I'll explore this later in the chapter, because if you work with or on open source you need to be aware of the risks. The "no merging your own patch" rule has two goals. First, if you want your project to be C4-certified, you have to get at least one other person to help. If no one wants to help you, perhaps you need to rethink your project. Second, having a control for every patch makes it much more satisfying, keeps us more focused, and stops us breaking the rules because we're in a hurry, or just feeling lazy.

> Maintainers SHALL NOT make value judgments on correct patches.

We already said this but it's worth repeating: the role of Maintainer is not to judge a patch's substance, only its technical quality. The substantive worth of a patch only emerges over time: people use it, and like it, or they do not. And if no one is using a patch, eventually it'll annoy someone else who will remove it, and no one will complain.

> Maintainers SHALL merge correct patches rapidly.

There is a criteria I call *change latency*, which is the round-trip time from identifying a problem to testing a solution. The faster the better. If maintainers cannot respond to pull requests as rapidly as people expect, they're not doing their job (or they need more hands).

> The Contributor MAY tag an issue as "Ready" after making a pull request for the issue.

By default, GitHub offers the usual variety of issues, but with C4 we don't use them. Instead, we need just two labels, "Urgent" and "Ready". A contributor who wants another user to test an issue can then label it as "Ready".

> The user who created an issue SHOULD close the issue after checking the patch is successful.

When one person opens an issue, and another works on it, it's best to allow the original person to close the issue. That acts as a double-check that the issue was properly resolved.

> Maintainers SHOULD ask for improvements to incorrect patches and SHOULD reject incorrect patches if the Contributor does not respond constructively.

Initially, I felt it was worth merging all patches, no matter how poor. There's an element of trolling involved. Accepting even obviously bogus patches could, I felt, pull in more contributors. But people were uncomfortable with this so we defined the "correct patch" rules, and the Maintainer's role in checking for quality. On the negative side, I think we didn't take some interesting risks, which could have paid off with more participants. On the positive side, this has led to `libzmq` master (and in all projects that use C4) being practically production quality, practically all the time.

> Any Contributor who has value judgments on a correct patch SHOULD express these via their own patches.

In essence, the goal here is to allow users to try patches rather than to spend time arguing pros and cons. As easy as it is to make a patch, it's as easy to revert it with another patch. You might think this would lead to "patch wars", but that hasn't happened. We've had a handful of cases in `libzmq` where patches by one contributor were killed by another person who felt the experimentation wasn't going in the right direction. It is easier than seeking up-front consensus.

> Maintainers MAY commit changes to non-source documentation directly to the project.

This exit allows maintainers who are making release notes to push those without having to create an issue which would then affect the release notes, leading to stress on the space time fabric and possibly involuntary rerouting backwards in the fourth dimension to before the invention of cold beer. Shudder. It is simpler to agree that release notes aren't technically software.



| [Creating Stable Releases](http://zguide.zeromq.org/page:all#Creating-Stable-Releases) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-148) [next](http://zguide.zeromq.org/page:all#header-150) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

We want some guarantee of stability for a production system. In the past, this meant taking unstable code and then over months hammering out the bugs and faults until it was safe to trust. iMatix's job, for years, has been to do this to `libzmq`, turning raw code into packages by allowing only bug fixes and no new code into a "stabilization branch". It's surprisingly not as thankless as it sounds.

Now, since we went full speed with C4, we've found that git master of `libzmq` is mostly perfect, most of the time. This frees our time to do more interesting things, such as building new open source layers on top of `libzmq`. However, people still want that guarantee: many users will simply not install except from an "official" release. So a stable release today means two things. First, a snapshot of the master taken at a time when there were no new changes for a while, and no dramatic open bugs. Second, a way to fine tune that snapshot to fix the critical issues remaining in it.

This is the process we explain in this section.

> The project SHALL have one branch ("master") that always holds the latest in-progress version and SHOULD always build.

This is redundant because every patch always builds but it's worth restating. If the master doesn't build (and pass its tests), someone needs waking up.

> The project SHALL NOT use topic branches for any reason. Personal forks MAY use topic branches.

I'll come to branches soon. In short (or "tl;dr", as they say on the webs), branches make the repository too complex and fragile, and require up-front agreement, all of which are expensive and avoidable.

> To make a stable release someone SHALL fork the repository by copying it and thus become maintainer of this repository.
> Forking a project for stabilization MAY be done unilaterally and without agreement of project maintainers.

It's free software. No one has a monopoly on it. If you think the maintainers aren't producing stable releases right, fork the repository and do it yourself. Forking isn't a failure, it's an essential tool for competition. You can't do this with branches, which means a branch-based release policy gives the project maintainers a monopoly. And that's bad because they'll become lazier and more arrogant than if real competition is chasing their heels.

> A stabilization project SHOULD be maintained by the same process as the main project.

Stabilization projects have maintainers and contributors like any project. In practice we usually cherry pick patches from the main project to the stabilization project, but that's just a convenience.

> A patch to a repository declared "stable" SHALL be accompanied by a reproducible test case.

Beware of a one-size-fits-all process. New code does not need the same paranoia as code that people are trusting for production use. In the normal development process, we did not mention test cases. There's a reason for this. While I love testable patches, many changes aren't easily or at all testable. However, to stabilize a code base you want to fix only serious bugs, and you want to be 100% sure every change is accurate. This means before and after tests for every change.



| [Evolution of Public Contracts](http://zguide.zeromq.org/page:all#Evolution-of-Public-Contracts) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-149) [next](http://zguide.zeromq.org/page:all#header-151) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

By "public contracts", I mean APIs and protocols. Up until the end of 2011, `libzmq`'s naturally happy state was marred by broken promises and broken contracts. We stopped making promises (aka "road maps") for `libzmq` completely, and our dominant theory of change is now that it emerges carefully and accurately over time. At a 2012 Chicago meetup, Garrett Smith and Chuck Remes called this the "drunken stumble to greatness", which is how I think of it now.

We stopped breaking public contracts simply by banning the practice. Before then it had been "OK" (as in we did it and everyone complained bitterly, and we ignored them) to break the API or protocol so long as we changed the major version number. Sounds fine, until you get ZeroMQ v2.0, v3.0, and v4.0 all in development at the same time, and not speaking to each other.

> All Public Contracts (APIs or protocols) SHOULD be documented.

You'd think this was a given for professional software engineers but no, it's not. So, it's a rule. You want C4 certification for your project, you make sure your public contracts are documented. No "It's specified in the code" excuses. Code is not a contract. (Yes, I intend at some point to create a C4 certification process to act as a quality indicator for open source projects.)

> All Public Contracts SHALL use Semantic Versioning.

This rule is mainly here because people asked for it. I've no real love for it, as Semantic Versioning is what led to the so-called "Why does ZeroMQ not speak to itself?!" debacle. I've never seen the problem that this solved. Something about runtime validation of library versions, or some-such.

> All Public Contracts SHOULD have space for extensibility and experimentation.

Now, the real thing is that public contracts *do change*. It's not about not changing them. It's about changing them safely. This means educating (especially protocol) designers to create that space up-front.

> A patch that modifies a stable Public Contract SHOULD not break existing applications unless there is overriding consensus on the value of doing this.

Sometimes the patch is fixing a bad API that no one is using. It's a freedom we need, but it should be based on consensus, not one person's dogma. However, making random changes "just because" is not good. In ZeroMQ v3.x, did we benefit from renaming `ZMQ_NOBLOCK` to `ZMQ_DONTWAIT`? Sure, it's closer to the POSIX socket `recv()` call, but is that worth breaking thousands of applications? No one ever reported it as an issue. To misquote Stallman: "your freedom to create an ideal world stops one inch from my application."

> A patch that introduces new features to a Public Contract SHOULD do so using new names.

We had the experience in ZeroMQ once or twice of new features using old names (or worse, using names that were *still in use* elsewhere). ZeroMQ v3.0 had a newly introduced "ROUTER" socket that was totally different from the existing ROUTER socket in 2.x. Dear lord, you should be face-palming, why? The reason: apparently, even smart people sometimes need regulation to stop them doing silly things.

> Old names SHOULD be deprecated in a systematic fashion by marking new names as "experimental" until they are stable, then marking the old names as "deprecated".

This life cycle notation has the great benefit of actually telling users what is going on with a consistent direction. "Experimental" means "we have introduced this and intend to make it stable if it works". It does not mean, "we have introduced this and will remove it at any time if we feel like it". One assumes that code that survives more than one patch cycle is meant to be there. "Deprecated" means "we have replaced this and intend to remove it".

> When sufficient time has passed, old deprecated names SHOULD be marked "legacy" and eventually removed.

In theory this gives applications time to move onto stable new contracts without risk. You can upgrade first, make sure things work, and then, over time, fix things up to remove dependencies on deprecated and legacy APIs and protocols.

> Old names SHALL NOT be reused by new features.

Ah, yes, the joy when ZeroMQ v3.x renamed the top-used API functions (`zmq_send()` and `zmq_recv()`) and then recycled the old names for new methods that were utterly incompatible (and which I suspect few people actually use). You should be slapping yourself in confusion again, but really, this is what happened and I was as guilty as anyone. After all, we did change the version number! The only benefit of that experience was to get this rule.

> When old names are removed, their implementations MUST provoke an exception (assertion) if used by applications.

I've not tested this rule to be certain it makes sense. Perhaps what it means is "if you can't provoke a compile error because the API is dynamic, provoke an assertion".



| [Project Administration](http://zguide.zeromq.org/page:all#Project-Administration) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-150) [next](http://zguide.zeromq.org/page:all#header-152) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

> The project founders SHALL act as Administrators to manage the set of project Maintainers.

Someone needs to administer the project, and it makes sense that the original founders start this ball rolling.

> The Administrators SHALL ensure their own succession over time by promoting the most effective Maintainers.

At the same time, as founder of a project you really want to get out of the way before you become over-attached to it. Promoting the most active and consistent maintainers is good for everyone.

> A new Contributor who makes a correct patch SHALL be invited to become a Maintainer.

I met Felix Geisendörfer in Lyons in 2012 at the [Mix-IT conference](http://www.mix-it.fr/) where I presented Social Architecture and one thing that came out of this was Felix's now famous [Pull Request Hack](http://felixge.de/2013/03/11/the-pull-request-hack.html). It fits elegantly into C4 and solves the problem of maintainers dropping out over time.

> Administrators MAY remove Maintainers who are inactive for an extended period of time, or who repeatedly fail to apply this process accurately.

This was Ian Barber's suggestion: we need a way to crop inactive maintainers. Originally maintainers were self-elected but that makes it hard to drop troublemakers (who are rare, but not unknown).

C4 is not perfect. Few things are. The process for changing it (Digistan's COSS) is a little outdated now: it relies on a single-editor workflow with the ability to fork, but not merge. This seems to work but it could be better to use C4 for protocols like C4.



| [A Real-Life Example](http://zguide.zeromq.org/page:all#A-Real-Life-Example) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-151) [next](http://zguide.zeromq.org/page:all#header-153) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

In [this email thread](https://lists.zeromq.org/pipermail/zeromq-dev/2012-October/018470.html), Dan Goes asks how to make a publisher that knows when a new client subscribes, and sends out previous matching messages. It's a standard pub-sub technique called "last value caching". Now over a 1-way transport like pgm (where subscribers literally send no packets back to publishers), this can't be done. But over TCP, it can, if we use an XPUB socket and if that socket didn't cleverly filter out duplicate subscriptions to reduce upstream traffic.

Though I'm not an expert contributor to `libzmq`, this seems like a fun problem to solve. How hard could it be? I start by forking the `libzmq` repository to my own GitHub account and then clone it to my laptop, where I build it:

```
git clone git@github.com:hintjens/libzmq.git
cd libzmq
./autogen.sh
./configure
make
```

Because the `libzmq` code is neat and well-organized, it was quite easy to find the main files to change (`xpub.cpp` and `xpub.hpp`). Each socket type has its own source file and class. They inherit from `socket_base.cpp`, which has this hook for socket-specific options:

```
//  First, check whether specific socket type overloads the option.
int rc = xsetsockopt (option_, optval_, optvallen_);
if (rc == 0 || errno != EINVAL)
    return rc;

//  If the socket type doesn't support the option, pass it to
//  the generic option parser.
return options.setsockopt (option_, optval_, optvallen_);
```

Then I check where the XPUB socket filters out duplicate subscriptions, in its `xread_activated` method:

```
bool unique;
if (*data == 0)
    unique = subscriptions.rm (data + 1, size - 1, pipe_);
else
    unique = subscriptions.add (data + 1, size - 1, pipe_);

//  If the subscription is not a duplicate store it so that it can be
//  passed to used on next recv call.
if (unique && options.type != ZMQ_PUB)
    pending.push_back (blob_t (data, size));
```

At this stage, I'm not too concerned with the details of how `subscriptions.rm` and `subscriptions.add` work. The code seems obvious except that "subscription" also includes unsubscription, which confused me for a few seconds. If there's anything else weird in the rm and add methods, that's a separate issue to fix later. Time to make an issue for this change. I head over to the `zeromq.jira.com` site, log in, and create a new entry.

Jira kindly offers me the traditional choice between "bug" and "new feature" and I spend thirty seconds wondering where this counterproductive historical distinction came from. Presumably, the "we'll fix bugs for free, but you pay for new features" commercial proposal, which stems from the "you tell us what you want and we'll make it for $X" model of software development, and which generally leads to "we spent three times $X and we got what?!" email Fists of Fury.

Putting such thoughts aside, I create [an issue #443](https://zeromq.jira.com/browse/LIBZMQ-443) and described the problem and plausible solution:

> Problem: XPUB socket filters out duplicate subscriptions (deliberate design). However this makes it impossible to do subscription-based intelligence. See http://lists.zeromq.org/pipermail/zeromq-dev/2012-October/018838.html for a use case.
> Solution: make this behavior configurable with a socket option.

It's naming time. The API sits in `include/zmq.h`, so this is where I added the option name. When you invent a concept in an API or anywhere, *please* take a moment to choose a name that is explicit and short and obvious. Don't fall back on generic names that need additional context to understand. You have one chance to tell the reader what your concept is and does. A name like `ZMQ_SUBSCRIPTION_FORWARDING_FLAG` is terrible. It technically kind of aims in the right direction, but is miserably long and obscure. I chose `ZMQ_XPUB_VERBOSE`: short and explicit and clearly an on/off switch with "off" being the default setting.

So, it's time to add a private property to the `xpub` class definition in `xpub.hpp`:

```
// If true, send all subscription messages upstream, not just
// unique ones
bool verbose;
```

And then lift some code from `router.cpp` to implement the `xsetsockopt` method. Finally, change the `xread_activated` method to use this new option, and while at it, make that test on socket type more explicit too:

```
//  If the subscription is not a duplicate store it so that it can be
//  passed to used on next recv call.
if (options.type == ZMQ_XPUB && (unique || verbose))
    pending.push_back (blob_t (data, size));
```

The thing builds nicely the first time. This makes me a little suspicious, but being lazy and jet-lagged I don't immediately make a test case to actually try out the change. The process doesn't demand that, even if usually I'd do it just to catch that inevitable 10% of mistakes we all make. I do however document this new option on the `doc/zmq_setsockopt.txt` man page. In the worst case, I added a patch that wasn't really useful. But I certainly didn't break anything.

I don't implement a matching `zmq_getsockopt` because "minimal" means what it says. There's no obvious use case for getting the value of an option that you presumably just set, in code. Symmetry isn't a valid reason to double the size of a patch. I did have to document the new option because the process says, "All Public Contracts SHOULD be documented."

Committing the code, I push the patch to my forked repository (the "origin"):

```
git commit -a -m "Fixed issue #443"
git push origin master
```

Switching to the GitHub web interface, I go to my `libzmq` fork, and press the big "Pull Request" button at the top. GitHub asks me for a title, so I enter "Added ZMQ_XPUB_VERBOSE option". I'm not sure why it asks this as I made a neat commit message but hey, let's go with the flow here.

This makes a nice little pull request with two commits; the one I'd made a month ago on the release notes to prepare for the v3.2.1 release (a month passes so quickly when you spend most of it in airports), and my fix for issue #443 (37 new lines of code). GitHub lets you continue to make commits after you've kicked off a pull request. They get queued up and merged in one go. That is easy, but the maintainer may refuse the whole bundle based on one patch that doesn't look valid.

Because Dan is waiting (at least in my highly optimistic imagination) for this fix, I go back to the zeromq-dev list and tell him I've made the patch, with a link to the commit. The faster I get feedback, the better. It's 1 a.m. in South Korea as I make this patch, so early evening in Europe, and morning in the States. You learn to count timezones when you work with people across the world. Ian is in a conference, Mikko is getting on a plane, and Chuck is probably in the office, but three hours later, Ian merges the pull request.

After Ian merges the pull request, I resynchronize my fork with the upstream `libzmq`repository. First, I add a *remote* that tells git where this repository sits (I do this just once in the directory where I'm working):

```
git remote add upstream git://github.com/zeromq/libzmq.git
```

And then I pull changes back from the upstream master and check the git log to double-check:

```
git pull --rebase upstream master
git log
```

And that is pretty much it, in terms of how much git one needs to learn and use to contribute patches to `libzmq`. Six git commands and some clicking on web pages. Most importantly to me as a naturally lazy, stupid, and easily confused developer, I don't have to learn git's internal models, and never have to do anything involving those infernal engines of structural complexity we call "git branches". Next up, the attempted assassination of git branches. Let's live dangerously!



| [Git Branches Considered Harmful](http://zguide.zeromq.org/page:all#Git-Branches-Considered-Harmful) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-152) [next](http://zguide.zeromq.org/page:all#header-154) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

One of git's most popular features is its branches. Almost all projects that use git use branches, and the selection of the "best" branching strategy is like a rite of passage for an open source project. Vincent Driessen's [git-flow](http://nvie.com/posts/a-successful-git-branching-model/) may be the best known. It has *base*branches (master, develop), *feature* branches, *release* branches, *hotfix* branches, and *support* branches. Many teams have adopted git-flow, which even has git extensions to support it. I'm a great believer in popular wisdom, but sometimes you have to recognize mass delusion for what it is.

Here is a section of C4 that might have shocked you when you first read it:

> The project SHALL NOT use topic branches for any reason. Personal forks MAY use topic branches.

To be clear, it's *public branches in shared repositories* that I'm talking about. Using branches for private work, e.g., to work on different issues, appears to work well enough, though it's more complexity than I personally enjoy. To channel Stallman again: "your freedom to create complexity ends one inch from our shared workspace."

Like the rest of C4, the rules on branches are not accidental. They came from our experience making ZeroMQ, starting when Martin Sustrik and I rethought how to make stable releases. We both love and appreciate simplicity (some people seem to have a remarkable tolerance for complexity). We chatted for a while… I asked him, "I'm going to start making a stable release. Would it be OK for me to make a branch in the git you're working in?" Martin didn't like the idea. "OK, if I fork the repository, I can move patches from your repo to that one". That felt much better to both of us.

The response from many in the ZeroMQ community was shock and horror. People felt we were being lazy and making contributors work harder to find the "right" repository. Still, this seemed simple, and indeed it worked smoothly. The best part was that we each worked as we wanted to. Whereas before, the ZeroMQ repository had felt horribly complex (and it wasn't even anything like git-flow), this felt simple. And it worked. The only downside was that we lost a single unified history. Now, perhaps historians will feel robbed, but I honestly can't see that the historical minutiae of who changed what, when, including every branch and experiment, are worth any significant pain or friction.

People have gotten used to the "multiple repositories" approach in ZeroMQ and we've started using that in other projects quite successfully. My own opinion is that history will judge git branches and patterns like git-flow as a complex solution to imaginary problems inherited from the days of Subversion and monolithic repositories.

More profoundly, and perhaps this is why the majority seems to be "wrong": I think the branches versus forks argument is really a deeper design versus evolve argument about how to make software optimally. I'll address that deeper argument in the next section. For now, I'll try to be scientific about my irrational hatred of branches, by looking at a number of criteria, and comparing branches and forks in each one.



| [Simplicity Versus Complexity](http://zguide.zeromq.org/page:all#Simplicity-Versus-Complexity) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-153) [next](http://zguide.zeromq.org/page:all#header-155) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*The simpler, the better.*

There is no inherent reason why branches are more complex than forks. However, git-flow uses *five types* of branch, whereas C4 uses two types of fork (development, and stable) and one branch (master). Circumstantial evidence is thus that branches lead to more complexity than forks. For new users, it is definitely, and we've measured this in practice, easier to learn to work with many repositories and no branches except master.



| [Change Latency](http://zguide.zeromq.org/page:all#Change-Latency) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-154) [next](http://zguide.zeromq.org/page:all#header-156) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*The smaller and more rapid the delivery, the better.*

Development branches seem to correlate strongly with large, slow, risky deliveries. "Sorry, I have to merge this branch before we can test the new version" signals a breakdown in process. It's certainly not how C4 works, which is by focusing tightly on individual problems and their minimal solutions. Allowing branches in development raises change latency. Forks have a different outcome: it's up to the forker to ensure that his changes merge cleanly, and to keep them simple so they won't be rejected.



| [Learning Curve](http://zguide.zeromq.org/page:all#Learning-Curve) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-155) [next](http://zguide.zeromq.org/page:all#header-157) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*The smoother the learning curve, the better.*

Evidence definitely shows that learning to use git branches is complex. For some people, this is OK. For most developers, every cycle spent learning git is a cycle lost on more productive things. I've been told several times, by different people that I do not like branches because I "never properly learned git". That is fair, but it is a criticism of the tool, not the human.



| [Cost of Failure](http://zguide.zeromq.org/page:all#Cost-of-Failure) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-156) [next](http://zguide.zeromq.org/page:all#header-158) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*The lower the cost of failure, the better.*

Branches demand more perfection from developers because mistakes potentially affect others. This raises the cost of failure. Forks make failure extremely cheap because literally nothing that happens in a fork can affect others not using that fork.



| [Up-front Coordination](http://zguide.zeromq.org/page:all#Up-front-Coordination) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-157) [next](http://zguide.zeromq.org/page:all#header-159) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*The less need for up-front coordination, the better.*

You can do a hostile fork. You cannot do a hostile branch. Branches depend on up-front coordination, which is expensive and fragile. One person can veto the desires of a whole group. For example in the ZeroMQ community we were unable to agree on a git branching model for a year. We solved that by using forking instead. The problem went away.



| [Scalability](http://zguide.zeromq.org/page:all#Scalability) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-158) [next](http://zguide.zeromq.org/page:all#header-160) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*The more you can scale a project, the better.*

The strong assumption in all branch strategies is that the repository *is* the project. But there is a limit to how many people you can get to agree to work together in one repository. As I explained, the cost of up-front coordination can become fatal. A more realistic project scales by allowing anyone to start their own repositories, and ensuring these can work together. A project like ZeroMQ has dozens of repositories. Forking looks more scalable than branching.



| [Surprise and Expectations](http://zguide.zeromq.org/page:all#Surprise-and-Expectations) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-159) [next](http://zguide.zeromq.org/page:all#header-161) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*The less surprising, the better.*

People expect branches and find forks to be uncommon and thus confusing. This is the one aspect where branches win. If you use branches, a single patch will have the same commit hash tag, whereas across forks the patch will have different hash tags. That makes it harder to track patches as they cross forks, true. But seriously, *having to track hexadecimal hash tags is not a feature*. It's a bug. Sometimes better ways of working are surprising at first.



| [Economics of Participation](http://zguide.zeromq.org/page:all#Economics-of-Participation) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-160) [next](http://zguide.zeromq.org/page:all#header-162) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*The more tangible the rewards, the better.*

People like to own their work and get credit for it. This is much easier with forks than with branches. Forks create more competition in a healthy way, while branches suppress competition and force people to collaborate and share credit. This sounds positive but in my experience it demotivates people. A branch isn't a product you can "own", whereas a fork can be.



| [Robustness in Conflict](http://zguide.zeromq.org/page:all#Robustness-in-Conflict) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-161) [next](http://zguide.zeromq.org/page:all#header-163) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*The more a model can survive conflict, the better.*

Like it or not, people fight over ego, status, beliefs, and theories of the world. Challenge is a necessary part of science. If your organizational model depends on agreement, you won't survive the first real fight. Branches do not survive real arguments and fights, whereas forks can be hostile, and still benefit all parties. And this is indeed how free software works.



| [Guarantees of Isolation](http://zguide.zeromq.org/page:all#Guarantees-of-Isolation) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-162) [next](http://zguide.zeromq.org/page:all#header-164) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*The stronger the isolation between production code and experiment, the better.*

People make mistakes. I've seen experimental code pushed to mainline production by error. I've seen people make bad panic changes under stress. But the real fault is in allowing two entirely separate generations of product to exist in the same protected space. If you can push to random-branch-x, you can push to master. Branches do not guarantee isolation of production critical code. Forks do.



| [Visibility](http://zguide.zeromq.org/page:all#Visibility) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-163) [next](http://zguide.zeromq.org/page:all#header-165) |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
|                                                            |                                                              |

*The more visible our work, the better.*

Forks have watchers, issues, a README, and a wiki. Branches have none of these. People try forks, build them, break them, patch them. Branches sit there until someone remembers to work on them. Forks have downloads and tarballs. Branches do not. When we look for self-organization, the more visible and declarative the problems, the faster and more accurately we can work.



| [Conclusions](http://zguide.zeromq.org/page:all#Conclusions) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-164) [next](http://zguide.zeromq.org/page:all#header-166) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

In this section, I've listed a series of arguments, most of which came from fellow team members. Here's how it seems to break down: git veterans insist that branches are the way to work, whereas newcomers tend to feel intimidated when asked to navigate git branches. Git is not an easy tool to master. What we've discovered, accidentally, is that when you stop using branches *at all*, git becomes trivial to use. It literally comes down to six commands (`clone`, `remote`, `commit`, `log`, `push`, and `pull`). Furthermore, a branch-free process actually works, we've used it for a couple of years now, and no visible downside except surprise to the veterans and growth of "single" projects over multiple repositories.

If you can't use forks, perhaps because your firm doesn't trust GitHub's private repositories, then you can perhaps use topic branches, one per issue. You'll still suffer the costs of getting up-front consensus, low competitiveness, and risk of human error.



| [Designing for Innovation](http://zguide.zeromq.org/page:all#Designing-for-Innovation) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-165) [next](http://zguide.zeromq.org/page:all#header-167) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Let's look at innovation, which Wikipedia defines as, "the development of new values through solutions that meet new requirements, inarticulate needs, or old customer and market needs in value adding new ways." This really just means solving problems more cheaply. It sounds straight-forward, but the history of collapsed tech giants proves that it's not. I'll try to explain how teams so often get it wrong, and suggest a way for doing innovation right.



| [The Tale of Two Bridges](http://zguide.zeromq.org/page:all#The-Tale-of-Two-Bridges) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-166) [next](http://zguide.zeromq.org/page:all#header-168) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Two old engineers were talking of their lives and boasting of their greatest projects. One of the engineers explained how he had designed one of the greatest bridges ever made.

"We built it across a river gorge," he told his friend. "It was wide and deep. We spent two years studying the land, and choosing designs and materials. We hired the best engineers and designed the bridge, which took another five years. We contracted the largest engineering firms to build the structures, the towers, the tollbooths, and the roads that would connect the bridge to the main highways. Dozens died during the construction. Under the road level we had trains, and a special path for cyclists. That bridge represented years of my life."

The second man reflected for a while, then spoke. "One evening me and a friend got drunk on vodka, and we threw a rope across a gorge," he said. "Just a rope, tied to two trees. There were two villages, one at each side. At first, people pulled packages across that rope with a pulley and string. Then someone threw a second rope, and built a foot walk. It was dangerous, but the kids loved it. A group of men then rebuilt that, made it solid, and women started to cross, everyday, with their produce. A market grew up on one side of the bridge, and slowly that became a large town, because there was a lot of space for houses. The rope bridge got replaced with a wooden bridge, to allow horses and carts to cross. Then the town built a real stone bridge, with metal beams. Later, they replaced the stone part with steel, and today there's a suspension bridge standing in that same spot."

The first engineer was silent. "Funny thing," he said, "my bridge was demolished about ten years after we built it. Turns out it was built in the wrong place and no one wanted to use it. Some guys had thrown a rope across the gorge, a few miles further downstream, and that's where everyone went."



| [How ZeroMQ Lost Its Road Map](http://zguide.zeromq.org/page:all#How-ZeroMQ-Lost-Its-Road-Map) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-167) [next](http://zguide.zeromq.org/page:all#header-169) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Presenting ZeroMQ at the Mix-IT conference in Lyon in early 2012, I was asked several times for the "road map". My answer was: there is no road map any longer. We had road maps, and we deleted them. Instead of a few experts trying to lay out the next steps, we were allowing this to happen organically. The audience didn't really like my answer. So un-French.

However, the history of ZeroMQ makes it quite clear why road maps were problematic. In the beginning, we had a small team making the library, with few contributors, and no documented road map. As ZeroMQ grew more popular and we switched to more contributors, users asked for road maps. So we collected our plans together and tried to organize them into releases. Here, we wrote, is what will come in the next release.

As we rolled out releases, we hit the problem that it's very easy to promise stuff, and rather harder to make it as planned. For one thing, much of the work was voluntary, and it's not clear how you force volunteers to commit to a road map. But also, priorities can shift dramatically over time. So we were making promises we could not keep, and the real deliveries didn't match the road maps.

The second problem was that by defining the road map, we in effect claimed territory, making it harder for others to participate. People do prefer to contribute to changes they believe were their idea. Writing down a list of things to do turns contribution into a chore rather than an opportunity.

Finally, we saw changes in ZeroMQ that were quite traumatic, and the road maps didn't help with this, despite a lot of discussion and effort to "do it right". Examples of this were incompatible changes in APIs and protocols. It was quite clear that we needed a different approach for defining the change process.

Software engineers don't like the notion that powerful, effective solutions can come into existence without an intelligent designer actively thinking things through. And yet no one in that room in Lyon would have questioned evolution. A strange irony, and one I wanted to explore further as it underpins the direction the ZeroMQ community has taken since the start of 2012.

In the dominant theory of innovation, brilliant individuals reflect on large problem sets and then carefully and precisely create a solution. Sometimes they will have "eureka" moments where they "get" brilliantly simple answers to whole large problem sets. The inventor, and the process of invention are rare, precious, and can command a monopoly. History is full of such heroic individuals. We owe them our modern world.

Looking more closely, however, and you will see that the facts don't match. History doesn't show lone inventors. It shows lucky people who steal or claim ownership of ideas that are being worked on by many. It shows brilliant people striking lucky once, and then spending decades on fruitless and pointless quests. The best known large-scale inventors like Thomas Edison were in fact just very good at systematic broad research done by large teams. It's like claiming that Steve Jobs invented every device made by Apple. It is a nice myth, good for marketing, but utterly useless as practical science.

Recent history, much better documented and less easy to manipulate, shows this well. The Internet is surely one of the most innovative and fast-moving areas of technology, and one of the best documented. It has no inventor. Instead, it has a massive economy of people who have carefully and progressively solved a long series of immediate problems, documented their answers, and made those available to all. The innovative nature of the Internet comes not from a small, select band of Einsteins. It comes from RFCs anyone can use and improve, made by hundreds and thousands of smart, but not uniquely smart, individuals. It comes from open source software anyone can use and improve. It comes from sharing, scale of community, and the continuous accretion of good solutions and disposal of bad ones.

Here thus is an alternative theory of innovation:

1. There is an infinite problem/solution terrain.
2. This terrain changes over time according to external conditions.
3. We can only accurately perceive problems to which we are close.
4. We can rank the cost/benefit economics of problems using a market for solutions.
5. There is an optimal solution to any solvable problem.
6. We can approach this optimal solution heuristically, and mechanically.
7. Our intelligence can make this process faster, but does not replace it.

There are a few corollaries to this:

- *Individual creativity matters less than process.* Smarter people may work faster, but they may also work in the wrong direction. It's the collective vision of reality that keeps us honest and relevant.

- *We don't need road maps if we have a good process.* Functionality will emerge and evolve over time as solutions compete for market share.

- *We don't invent solutions so much as discover them.* All sympathies to the creative soul. It's just an information processing machine that likes to polish its own ego and collect karma.

- *Intelligence is a social effect, though it feels personal.* A person cut off from others eventually stops thinking. We can neither collect problems nor measure solutions without other people.

- *The size and diversity of the community is a key factor.* Larger, more diverse communities collect more relevant problems, and solve them more accurately, and do this faster, than a small expert group.

So, when we trust the solitary experts, they make classic mistakes. They focus on ideas, not problems. They focus on the wrong problems. They make misjudgments about the value of solving problems. They don't use their own work.

Can we turn the above theory into a reusable process? In late 2011, I started documenting C4 and similar contracts, and using them both in ZeroMQ and in closed source projects. The underlying process is something I call "Simplicity Oriented Design", or SOD. This is a reproducible way of developing simple and elegant products. It organizes people into flexible supply chains that are able to navigate a problem landscape rapidly and cheaply. They do this by building, testing, and keeping or discarding minimal plausible solutions, called "patches". Living products consist of long series of patches, applied one atop the other.

SOD is relevant first because it's how we evolve ZeroMQ. It's also the basis for the design process we will use in [Advanced Architecture using ZeroMQ](http://zguide.zeromq.org/page:all#advanced-architecture) to develop larger-scale ZeroMQ applications. Of course, you can use any software architecture methodology with ZeroMQ.

To best understand how we ended up with SOD, let's look at the alternatives.



| [Trash-Oriented Design](http://zguide.zeromq.org/page:all#Trash-Oriented-Design) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-168) [next](http://zguide.zeromq.org/page:all#header-170) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The most popular design process in large businesses seems to be *Trash-Oriented Design*, or TOD. TOD feeds off the belief that all we need to make money are great ideas. It's tenacious nonsense, but a powerful crutch for people who lack imagination. The theory goes that ideas are rare, so the trick is to capture them. It's like non-musicians being awed by a guitar player, not realizing that great talent is so cheap it literally plays on the streets for coins.

The main output of TODs is expensive "ideation": concepts, design documents, and products that go straight into the trash can. It works as follows:

- The Creative People come up with long lists of "we could do X and Y". I've seen endlessly detailed lists of everything amazing a product could do. We've all been guilty of this. Once the creative work of idea generation has happened, it's just a matter of execution, of course.

- So the managers and their consultants pass their brilliant ideas to designers who create acres of preciously refined design documents. The designers take the tens of ideas the managers came up with, and turn them into hundreds of world-changing designs.

- These designs get given to engineers who scratch their heads and wonder who the heck came up with such nonsense. They start to argue back, but the designs come from up high, and really, it's not up to engineers to argue with creative people and expensive consultants.

- So the engineers creep back to their cubicles, humiliated and threatened into building the gigantic but oh-so-elegant junk heap. It is bone-breaking work because the designs take no account of practical costs. Minor whims might take weeks of work to build. As the project gets delayed, the managers bully the engineers into giving up their evenings and weekends.

- Eventually, something resembling a working product makes it out of the door. It's creaky and fragile, complex and ugly. The designers curse the engineers for their incompetence and pay more consultants to put lipstick onto the pig, and slowly the product starts to look a little nicer.

- By this time, the managers have started to try to sell the product and they find, shockingly, that no one wants it. Undaunted, they courageously build million-dollar web sites and ad campaigns to explain to the public why they absolutely need this product. They do deals with other businesses to force the product on the lazy, stupid, and ungrateful market.

- After twelve months of intense marketing, the product still isn't making profits. Worse, it suffers dramatic failures and gets branded in the press as a disaster. The company quietly shelves it, fires the consultants, buys a competing product from a small startup and rebrands that as its own Version 2. Hundreds of millions of dollars end up in the trash.

- Meanwhile, another visionary manager somewhere in the organization drinks a little too much tequila with some marketing people and has a Brilliant Idea.

Trash-Oriented Design would be a caricature if it wasn't so common. Something like 19 out of 20 market-ready products built by large firms are failures (yes, 87% of statistics are made up on the spot). The remaining 1 in 20 probably only succeeds because the competitors are so bad and the marketing is so aggressive.

The main lessons of TOD are quite straightforward but hard to swallow. They are:

- Ideas are cheap. No exceptions. There are no brilliant ideas. Anyone who tries to start a discussion with "oooh, we can do this too!" should be beaten down with all the passion one reserves for traveling evangelists. It is like sitting in a cafe at the foot of a mountain, drinking a hot chocolate and telling others, "Hey, I have a great idea, we can climb that mountain! And build a chalet on top! With two saunas! And a garden! Hey, and we can make it solar powered! Dude, that's awesome! What color should we paint it? Green! No, blue! OK, go and make it, I'll stay here and make spreadsheets and graphics!"

- The starting point for a good design process is to collect real problems that confront real people. The second step is to evaluate these problems with the basic question, "How much is it worth to solve this problem?" Having done that, we can collect that set of problems that are worth solving.

- Good solutions to real problems will succeed as products. Their success will depend on how good and cheap the solution is, and how important the problem is (and sadly, how big the marketing budgets are). But their success will also depend on how much they demand in effort to use—in other words, how simple they are.

Now, after slaying the dragon of utter irrelevance, we attack the demon of complexity.



| [Complexity-Oriented Design](http://zguide.zeromq.org/page:all#Complexity-Oriented-Design) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-169) [next](http://zguide.zeromq.org/page:all#header-171) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Really good engineering teams and small firms can usually build decent products. But the vast majority of products still end up being too complex and less successful than they might be. This is because specialist teams, even the best, often stubbornly apply a process I call *Complexity-Oriented Design*, or COD, which works as follows:

- Management correctly identifies some interesting and difficult problem with economic value. In doing so, they already leapfrog over any TOD team.

- The team with enthusiasm starts to build prototypes and core layers. These work as designed and thus encouraged, the team go off into intense design and architecture discussions, coming up with elegant schemas that look beautiful and solid.

- Management comes back and challenges the team with yet more difficult problems. We tend to equate cost with value, so the harder and more expensive to solve, the more the solution should be worth, in their minds.

- The team, being engineers and thus loving to build stuff, build stuff. They build and build and build and end up with massive, perfectly-designed complexity.

- The products go to market, and the market scratches its head and asks, "Seriously, is this the best you can do?" People do use the products, especially if they aren't spending their own money in climbing the learning curve.

- Management gets positive feedback from its larger customers, who share the same idea that high cost (in training and use) means high value, and so continues to push the process.

- Meanwhile somewhere across the world, a small team is solving the same problem using a better process, and a year later smashes the market to little pieces.

COD is characterized by a team obsessively solving the wrong problems in a form of collective delusion. COD products tend to be large, ambitious, complex, and unpopular. Much open source software is the output of COD processes. It is insanely hard for engineers to *stop* extending a design to cover more potential problems. They argue, "What if someone wants to do X?" but never ask themselves, "What is the real value of solving X?"

A good example of COD in practice is Bluetooth, a complex, over-designed set of protocols that users hate. It continues to exist only because in a massively-patented industry there are no real alternatives. Bluetooth is perfectly secure, which is close to pointless for a proximity protocol. At the same time, it lacks a standard API for developers, meaning it's really costly to use Bluetooth in applications.

On the #zeromq IRC channel, Wintre once wrote of how enraged he was many years ago when he "found that XMMS 2 had a working plugin system, but could not actually play music."

COD is a form of large-scale "rabbit-holing", in which designers and engineers cannot distance themselves from the technical details of their work. They add more and more features, utterly misreading the economics of their work.

The main lessons of COD are also simple, but hard for experts to swallow. They are:

- Making stuff that you don't immediately have a need for is pointless. Doesn't matter how talented or brilliant you are, if you just sit down and make stuff people are not actually asking for, you are most likely wasting your time.

- Problems are not equal. Some are simple, and some are complex. Ironically, solving the simpler problems often has more value to more people than solving the really hard ones. So if you allow engineers to just work on random things, they'll mostly focus on the most interesting but least worthwhile things.

- Engineers and designers love to make stuff and decoration, and this inevitably leads to complexity. It is crucial to have a "stop mechanism", a way to set short, hard deadlines that force people to make smaller, simpler answers to just the most crucial problems.



| [Simplicity Oriented Design](http://zguide.zeromq.org/page:all#Simplicity-Oriented-Design) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-170) [next](http://zguide.zeromq.org/page:all#header-172) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Finally, we come to the rare but precious *Simplicity Oriented Design*, or SOD. This process starts with a realization: we do not know what we have to make until after we start making it. Coming up with ideas or large-scale designs isn't just wasteful, it's a direct hindrance to designing the truly accurate solutions. The really juicy problems are hidden like far valleys, and any activity except active scouting creates a fog that hides those distant valleys. You need to keep mobile, pack light, and move fast.

SOD works as follows:

- We collect a set of interesting problems (by looking at how people use technology or other products) and we line these up from simple to complex, looking for and identifying patterns of use.

- We take the simplest, most dramatic problem and we solve this with a minimal plausible solution, or "patch". Each patch solves exactly a genuine and agreed-upon problem in a brutally minimal fashion.

- We apply one measure of quality to patches, namely "Can this be done any simpler while still solving the stated problem?" We can measure complexity in terms of concepts and models that the user has to learn or guess in order to use the patch. The fewer, the better. A perfect patch solves a problem with zero learning required by the user.

- Our product development consists of a patch that solves the problem "we need a proof of concept" and then evolves in an unbroken line to a mature series of products, through hundreds or thousands of patches piled on top of each other.

- We do not do *anything* that is not a patch. We enforce this rule with formal processes that demand that every activity or task is tied to a genuine and agreed-upon problem, explicitly enunciated and documented.

- We build our projects into a supply chain where each project can provide problems to its "suppliers" and receive patches in return. The supply chain creates the "stop mechanism" because when people are impatiently waiting for an answer, we necessarily cut our work short.

- Individuals are free to work on any projects, and provide patches at any place they feel it's worthwhile. No individuals "own" any project, except to enforce the formal processes. A single project can have many variations, each a collection of different, competing patches.

- Projects export formal and documented interfaces so that upstream (client) projects are unaware of change happening in supplier projects. Thus multiple supplier projects can compete for client projects, in effect creating a free and competitive market.

- We tie our supply chain to real users and external clients and we drive the whole process by rapid cycles so that a problem received from outside users can be analyzed, evaluated, and solved with a patch in a few hours.

- At every moment from the very first patch, our product is shippable. This is essential, because a large proportion of patches will be wrong (10-30%) and only by giving the product to users can we know which patches have become problems that need solving.

SOD is a *hill-climbing algorithm*, a reliable way of finding optimal solutions to the most significant problems in an unknown landscape. You don't need to be a genius to use SOD successfully, you just need to be able to see the difference between the fog of activity and the progress towards new real problems.

People have pointed out that hill-climbing algorithms have known limitations. One gets stuck on local peaks, mainly. But this is nonetheless how life itself works: collecting tiny incremental improvements over long periods of time. There is no intelligent designer. We reduce the risk of local peaks by spreading out widely across the landscape, but it is somewhat moot. The limitations aren't optional, they are physical laws. The theory says, *this is how innovation really works, so better embrace it and work with it than try to work on the basis of magical thinking*.

And in fact once you see all innovation as more or less successful hill-climbing, you realize why some teams and companies and products get stuck in a never-never land of diminishing prospects. They simply don't have the diversity and collective intelligence to find better hills to climb. When Nokia killed their open source projects, they cut their own throat.

A really good designer with a good team can use SOD to build world-class products, rapidly and accurately. To get the most out of SOD the designer has to use the product continuously, from day one, and develop his or her ability to smell out problems such as inconsistency, surprising behavior, and other forms of friction. We naturally overlook many annoyances, but a good designer picks these up and thinks about how to patch them. Design is about removing friction in the use of a product.

In an open source setting, we do this work in public. There's no "let's open the code" moment. Projects that do this are in my view missing the point of open source, which is to engage your users in your exploration, and to build community around the seed of the architecture.



| [Burnout](http://zguide.zeromq.org/page:all#Burnout) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-171) [next](http://zguide.zeromq.org/page:all#header-173) |
| ---------------------------------------------------- | ------------------------------------------------------------ |
|                                                      |                                                              |

The ZeroMQ community has been and still is heavily dependent on pro bono individual efforts. I'd like to think that everyone was compensated in some way for their contributions, and I believe that with ZeroMQ, contributing means gaining expertise in an extraordinarily valuable technology, which leads to improved professional options.

However, not all projects will be so lucky and if you work with or in open source, you should understand the risk of burnout that volunteers face. This applies to all pro bono communities. In this section, I'll explain what causes burnout, how to recognize it, how to prevent it, and (if it happens) how to try to treat it. Disclaimer: I'm not a psychiatrist and this article is based on my own experiences of working in pro bono contexts for the last 20 years, including free software projects, and NGOs such as the [FFII](http://www.ffii.org/).

In a pro bono context, we're expected to work without direct or obvious economic incentive. That is, we sacrifice family life, professional advancement, free time, and health in order to accomplish some goal we have decided to accomplish. In any project, we need some kind of reward to make it worth continuing each day. In most pro bono projects the rewards are very indirect, superficially not economical at all. Mostly, we do things because people say, "Hey, great!" Karma is a powerful motivator.

However, we are economic beings, and sooner or later, if a project costs us a great deal and does not bring economic rewards of some kind (money, fame, a new job), we start to suffer. At a certain stage, it seems our subconscious simply gets disgusted and says, "Enough is enough!" and refuses to go any further. If we try to force ourselves, we can literally get sick.

This is what I call "burnout", though the term is also used for other kinds of exhaustion. Too much investment on a project with too little economic reward, for too long. We are great at manipulating ourselves and others, and this is often part of the process that leads to burnout. We tell ourselves that it's for a good cause and that the other guy is doing OK, so we should be able to as well.

When I got burned out on open source projects like Xitami, I remember clearly how I felt. I simply stopped working on it, refused to answer any more emails, and told people to forget about it. You can tell when someone's burned out. They go offline, and everyone starts saying, "He's acting strange… depressed, or tired…"

Diagnosis is simple. Has someone worked a lot on a project that was not paying back in any way? Did she make exceptional sacrifices? Did he lose or abandon his job or studies to do the project? If you're answering "yes", it's burnout.

There are three simple techniques I've developed over the years to reduce the risk of burnout in the teams I work with:

- *No one is irreplaceable.* Working solo on a critical or popular project—the concentration of responsibility on one person who cannot set their own limits—is probably the main factor. It's a management truism: if someone in your organization is irreplaceable, get rid of him or her.

- *We need day jobs to pay the bills.* This can be hard, but seems necessary. Getting money from somewhere else makes it much easier to sustain a sacrificial project.

- *Teach people about burnout.* This should be a basic course in colleges and universities, as pro bono work becomes a more common way for young people to experiment professionally.

When someone is working alone on a critical project, you *know* they are going blow their fuses sooner or later. It's actually fairly predictable: something like 18-36 months depending on the individual and how much economic stress they face in their private lives. I've not seen anyone burn-out after half a year, nor last five years in a unrewarding project.

There is a simple cure for burnout that works in at least some cases: get paid decently for your work. However, this pretty much destroys the freedom of movement (across that infinite problem landscape) that the volunteer enjoys.



| [Patterns for Success](http://zguide.zeromq.org/page:all#Patterns-for-Success) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-172) [next](http://zguide.zeromq.org/page:all#header-174) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

I'll end this code-free chapter with a series of patterns for success in software engineering. They aim to capture the essence of what divides glorious success from tragic failure. They were described as "religious maniacal dogma" by a manager, and "anything else would be effing insane" by a colleague, in a single day. For me, they are science. But treat the Lazy Perfectionist and others as tools to use, sharpen, and throw away if something better comes along.



| [The Lazy Perfectionist](http://zguide.zeromq.org/page:all#The-Lazy-Perfectionist) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-173) [next](http://zguide.zeromq.org/page:all#header-175) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*Never design anything that's not a precise minimal answer to a problem we can identify and have to solve.*

The Lazy Perfectionist spends his idle time observing others and identifying problems that are worth solving. He looks for agreement on those problems, always asking, "What is the *real* problem". Then he moves, precisely and minimally, to build, or get others to build, a usable answer to one problem. He uses, or gets others to use those solutions. And he repeats this until there are no problems left to solve, or time or money runs out.



| [The Benevolent Tyrant](http://zguide.zeromq.org/page:all#The-Benevolent-Tyrant) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-174) [next](http://zguide.zeromq.org/page:all#header-176) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*The control of a large force is the same principle as the control of a few men: it is merely a question of dividing up their numbers.* — Sun Tzu

The Benevolent Tyrant divides large problems into smaller ones and throws them at groups to focus on. She brokers contracts between these groups, in the form of APIs and the "unprotocols" we'll read about in the next chapter. The Benevolent Tyrant constructs a supply chain that starts with problems, and results in usable solutions. She is ruthless about how the supply chain works, but does not tell people what to work on, nor how to do their work.



| [The Earth and Sky](http://zguide.zeromq.org/page:all#The-Earth-and-Sky) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-175) [next](http://zguide.zeromq.org/page:all#header-177) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*The ideal team consists of two sides: one writing code, and one providing feedback.*

The Earth and Sky work together as a whole, in close proximity, but they communicate formally through issue tracking. Sky seeks out problems from others and from their own use of the product and feeds these to Earth. Earth rapidly answers with testable solutions. Earth and Sky can work through dozens of issues in a day. Sky talks to other users, and Earth talks to other developers. Earth and Sky may be two people, or two small groups.



| [The Open Door](http://zguide.zeromq.org/page:all#The-Open-Door) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-176) [next](http://zguide.zeromq.org/page:all#header-178) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*The accuracy of knowledge comes from diversity.*

The Open Door accepts contributions from almost anyone. She does not argue quality or direction, instead allowing others to argue that and get more engaged. She calculates that even a troll will bring more diverse opinion to the group. She lets the group form its opinion about what goes into stable code, and she enforces this opinion with help of a Benevolent Tyrant.



| [The Laughing Clown](http://zguide.zeromq.org/page:all#The-Laughing-Clown) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-177) [next](http://zguide.zeromq.org/page:all#header-179) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*Perfection precludes participation.*

The Laughing Clown, often acting as the Happy Failure, makes no claim to high competence. Instead his antics and bumbling attempts provoke others into rescuing him from his own tragedy. Somehow however, he always identifies the right problems to solve. People are so busy proving him wrong they don't realize they're doing valuable work.



| [The Mindful General](http://zguide.zeromq.org/page:all#The-Mindful-General) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-178) [next](http://zguide.zeromq.org/page:all#header-180) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*Make no plans. Set goals, develop strategies and tactics.*

The Mindful General operates in unknown territory, solving problems that are hidden until they are nearby. Thus she makes no plans, but seeks opportunities, then exploits them rapidly and accurately. She develops tactics and strategies in the field, and teaches these to her soldiers so they can move independently, and together.



| [The Social Engineer](http://zguide.zeromq.org/page:all#The-Social-Engineer) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-179) [next](http://zguide.zeromq.org/page:all#header-181) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*If you know the enemy and know yourself, you need not fear the result of a hundred battles.* — Sun Tzu

The Social Engineer reads the hearts and minds of those he works with and for. He asks, of everyone, "What makes this person angry, insecure, argumentative, calm, happy?" He studies their moods and dispositions. With this knowledge he can encourage those who are useful, and discourage those who are not. The Social Engineer never acts on his own emotions.



| [The Constant Gardener](http://zguide.zeromq.org/page:all#The-Constant-Gardener) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-180) [next](http://zguide.zeromq.org/page:all#header-182) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*He will win whose army is animated by the same spirit throughout all its ranks.* — Sun Tzu

The Constant Gardener grows a process from a small seed, step-by-step as more people come into the project. She makes every change for a precise reason, with agreement from everyone. She never imposes a process from above but lets others come to consensus, and then he enforces that consensus. In this way, everyone owns the process together and by owning it, they are attached to it.



| [The Rolling Stone](http://zguide.zeromq.org/page:all#The-Rolling-Stone) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-181) [next](http://zguide.zeromq.org/page:all#header-183) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*After crossing a river, you should get far away from it.* — Sun Tzu

The Rolling Stone accepts his own mortality and transience. He has no attachment to his past work. He accepts that all that we make is destined for the trash can, it is just a matter of time. With precise, minimal investments, he can move rapidly away from the past and stay focused on the present and near future. Above all, he has no ego and no pride to be hurt by the actions of others.



| [The Pirate Gang](http://zguide.zeromq.org/page:all#The-Pirate-Gang) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-182) [next](http://zguide.zeromq.org/page:all#header-184) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*Code, like all knowledge, works best as collective—not private—property.*

The Pirate Gang organizes freely around problems. It accepts authority insofar as authority provides goals and resources. The Pirate Gang owns and shares all it makes: every work is fully remixable by others in the Pirate Gang. The gang moves rapidly as new problems emerge, and is quick to abandon old solutions if those stop being relevant. No persons or groups can monopolize any part of the supply chain.



| [The Flash Mob](http://zguide.zeromq.org/page:all#The-Flash-Mob) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-183) [next](http://zguide.zeromq.org/page:all#header-185) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*Water shapes its course according to the nature of the ground over which it flows.* — Sun Tzu

The Flash Mob comes together in space and time as needed, then disperses as soon as they can. Physical closeness is essential for high-bandwidth communications. But over time it creates technical ghettos, where Earth gets separated from Sky. The Flash Mob tends to collect a lot of frequent flier miles.



| [The Canary Watcher](http://zguide.zeromq.org/page:all#The-Canary-Watcher) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-184) [next](http://zguide.zeromq.org/page:all#header-186) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*Pain is not, generally, a Good Sign.*

The Canary Watcher measures the quality of an organization by their own pain level, and the observed pain levels of those with whom he works. He brings new participants into existing organizations so they can express the raw pain of the innocent. He may use alcohol to get others to verbalize their pain points. He asks others, and himself, "Are you happy in this process, and if not, why not?" When an organization causes pain in himself or others, he treats that as a problem to be fixed. People should feel joy in their work.



| [The Hangman](http://zguide.zeromq.org/page:all#The-Hangman) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-185) [next](http://zguide.zeromq.org/page:all#header-187) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*Never interrupt others when they are making mistakes.*

The Hangman knows that we learn only by making mistakes, and she gives others copious rope with which to learn. She only pulls the rope gently, when it's time. A little tug to remind the other of their precarious position. Allowing others to learn by failure gives the good reason to stay, and the bad excuse to leave. The Hangman is endlessly patient, because there is no shortcut to the learning process.



| [The Historian](http://zguide.zeromq.org/page:all#The-Historian) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-186) [next](http://zguide.zeromq.org/page:all#header-188) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*Keeping the public record may be tedious, but it's the only way to prevent collusion.*

The Historian forces discussion into the public view, to prevent collusion to own areas of work. The Pirate Gang depends on full and equal communications that do not depend on momentary presence. No one really reads the archives, but the simply possibility stops most abuses. The Historian encourages the right tool for the job: email for transient discussions, IRC for chatter, wikis for knowledge, issue tracking for recording opportunities.



| [The Provocateur](http://zguide.zeromq.org/page:all#The-Provocateur) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-187) [next](http://zguide.zeromq.org/page:all#header-189) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

*When a man knows he is to be hanged in a fortnight, it concentrates his mind wonderfully.*— Samuel Johnson

The Provocateur creates deadlines, enemies, and the occasional impossibility. Teams work best when they don't have time for the crap. Deadlines bring people together and focus the collective mind. An external enemy can move a passive team into action. The Provocateur never takes the deadline too seriously. The product is *always* ready to ship. But she gently reminds the team of the stakes: fail, and we all look for other jobs.



| [The Mystic](http://zguide.zeromq.org/page:all#The-Mystic) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-188) [next](http://zguide.zeromq.org/page:all#header-190) |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
|                                                            |                                                              |

*When people argue or complain, just write them a Sun Tzu quotation* — Mikko Koppanen

The Mystic never argues directly. He knows that to argue with an emotional person only creates more emotion. Instead he side-steps the discussion. It's hard to be angry at a Chinese general, especially when he has been dead for 2,400 years. The Mystic plays Hangman when people insist on the right to get it wrong.



| [Chapter 7 - Advanced Architecture using ZeroMQ](http://zguide.zeromq.org/page:all#Chapter-Advanced-Architecture-using-ZeroMQ) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-189) [next](http://zguide.zeromq.org/page:all#header-191) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

One of the effects of using ZeroMQ at large scale is that because we can build distributed architectures so much faster than before, the limitations of our software engineering processes become more visible. Mistakes in slow motion are often harder to see (or rather, easier to rationalize away).

My experience when teaching ZeroMQ to groups of engineers is that it's rarely sufficient to just explain how ZeroMQ works and then just expect them to start building successful products. Like any technology that removes friction, ZeroMQ opens the door to big blunders. If ZeroMQ is the ACME rocket-propelled shoe of distributed software development, a lot of us are like Wile E. Coyote, slamming full speed into the proverbial desert cliff.

We saw in [Chapter 6 - The ZeroMQ Community](http://zguide.zeromq.org/page:all#the-community) that ZeroMQ itself uses a formal process for changes. One reason we built this process, over some years, was to stop the repeated cliff-slamming that happened in the library itself.

Partly, it's about slowing down and partially, it's about ensuring that when you move fast, you go—and this is essential Dear Reader—in the *right direction*. It's my standard interview riddle: what's the rarest property of any software system, the absolute hardest thing to get right, the lack of which causes the slow or fast death of the vast majority of projects? The answer is not code quality, funding, performance, or even (though it's a close answer), popularity. The answer is *accuracy*.

Accuracy is half the challenge, and applies to any engineering work. The other half is distributed computing itself, which sets up a whole range of problems that we need to solve if we are going to create architectures. We need to encode and decode data; we need to define protocols to connect clients and servers; we need to secure these protocols against attackers; and we need to make stacks that are robust. Asynchronous messaging is hard to get right.

This chapter will tackle these challenges, starting with a basic reappraisal of how to design and build software and ending with a fully formed example of a distributed application for large-scale file distribution.

We'll cover the following juicy topics:

- How to go from idea to working prototype safely (the MOPED pattern)
- Different ways to serialize your data as ZeroMQ messages
- How to code-generate binary serialization codecs
- How to build custom code generators using the GSL tool
- How to write and license a protocol specification
- How to build fast restartable file transfer over ZeroMQ
- How to use credit-based flow control for nonblocking transfers
- How to build protocol servers and clients as state machines
- How to make a secure protocol over ZeroMQ
- A large-scale file publishing system (FileMQ)



| [Message-Oriented Pattern for Elastic Design](http://zguide.zeromq.org/page:all#Message-Oriented-Pattern-for-Elastic-Design) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-190) [next](http://zguide.zeromq.org/page:all#header-192) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

I'll introduce Message-Oriented Pattern for Elastic Design (MOPED), a software engineering pattern for ZeroMQ architectures. It was either "MOPED" or "BIKE", the Backronym-Induced Kinetic Effect. That's short for "BICICLE", the Backronym-Inflated See if I Care Less Effect. In life, one learns to go with the least embarrassing choice.

If you've read this book carefully, you'll have seen MOPED in action already. The development of Majordomo in [Chapter 4 - Reliable Request-Reply Patterns](http://zguide.zeromq.org/page:all#reliable-request-reply) is a near-perfect case. But cute names are worth a thousand words.

The goal of MOPED is to define a process by which we can take a rough use case for a new distributed application, and go from "Hello World" to fully-working prototype in any language in under a week.

Using MOPED, you grow, more than build, a working ZeroMQ architecture from the ground-up with minimal risk of failure. By focusing on the contracts rather than the implementations, you avoid the risk of premature optimization. By driving the design process through ultra-short test-based cycles, you can be more certain that what you have works before you add more.

We can turn this into five real steps:

- Step 1: internalize the ZeroMQ semantics.
- Step 2: draw a rough architecture.
- Step 3: decide on the contracts.
- Step 4: make a minimal end-to-end solution.
- Step 5: solve one problem and repeat.



| [Step 1: Internalize the Semantics](http://zguide.zeromq.org/page:all#Step-Internalize-the-Semantics) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-191) [next](http://zguide.zeromq.org/page:all#header-193) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

You must learn and digest ZeroMQ's "language", that is, the socket patterns and how they work. The only way to learn a language is to use it. There's no way to avoid this investment, no tapes you can play while you sleep, no chips you can plug in to magically become smarter. Read this book from the start, work through the code examples in whatever language you prefer, understand what's going on, and (most importantly) write some examples yourself and then throw them away.

At a certain point, you'll feel a clicking noise in your brain. Maybe you'll have a weird chili-induced dream where little ZeroMQ tasks run around trying to eat you alive. Maybe you'll just think "aaahh, so *that's* what it means!" If we did our work right, it should take two to three days. However long it takes, until you start thinking in terms of ZeroMQ sockets and patterns, you're not ready for step 2.



| [Step 2: Draw a Rough Architecture](http://zguide.zeromq.org/page:all#Step-Draw-a-Rough-Architecture) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-192) [next](http://zguide.zeromq.org/page:all#header-194) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

From my experience, it's essential to be able to draw the core of your architecture. It helps others understand what you are thinking, and it also helps you think through your ideas. There is really no better way to design a good architecture than to explain your ideas to your colleagues, using a whiteboard.

You don't need to get it right, and you don't need to make it complete. What you do need to do is break your architecture into pieces that make sense. The nice thing about software architecture (as compared to constructing bridges) is that you really can replace entire layers cheaply if you've isolated them.

Start by choosing the core problem that you are going to solve. Ignore anything that's not essential to that problem: you will add it in later. The problem should be an end-to-end problem: the rope across the gorge.

For example, a client asked us to make a supercomputing cluster with ZeroMQ. Clients create bundles of work, which are sent to a broker that distributes them to workers (running on fast graphics processors), collects the results back, and returns them to the client.

The rope across the gorge is one client talking to a broker talking to one worker. We draw three boxes: client, broker, worker. We draw arrows from box to box showing the request flowing one way and the response flowing back. It's just like the many diagrams we saw in earlier chapters.

Be minimalistic. Your goal is not to define a *real* architecture, but to throw a rope across the gorge to bootstrap your process. We make the architecture successfully more complete and realistic over time: e.g., adding multiple workers, adding client and worker APIs, handling failures, and so on.



| [Step 3: Decide on the Contracts](http://zguide.zeromq.org/page:all#Step-Decide-on-the-Contracts) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-193) [next](http://zguide.zeromq.org/page:all#header-195) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

A good software architecture depends on contracts, and the more explicit they are, the better things scale. You don't care *how* things happen; you only care about the results. If I send an email, I don't care how it arrives at its destination, as long as the contract is respected. The email contract is: it arrives within a few minutes, no-one modifies it, and it doesn't get lost.

And to build a large system that works well, you must focus on the contracts before the implementations. It may sound obvious but all too often, people forget or ignore this, or are just too shy to impose themselves. I wish I could say ZeroMQ had done this properly, but for years our public contracts were second-rate afterthoughts instead of primary in-your-face pieces of work.

So what is a contract in a distributed system? There are, in my experience, two types of contract:

- The APIs to client applications. Remember the Psychological Elements. The APIs need to be as absolutely *simple*, *consistent*, and *familiar* as possible. Yes, you can generate API documentation from code, but you must first design it, and designing an API is often hard.

- The protocols that connect the pieces. It sounds like rocket science, but it's really just a simple trick, and one that ZeroMQ makes particularly easy. In fact they're so simple to write, and need so little bureaucracy that I call them *unprotocols*.

You write minimal contracts that are mostly just place markers. Most messages and most API methods will be missing or empty. You also want to write down any known technical requirements in terms of throughput, latency, reliability, and so on. These are the criteria on which you will accept or reject any particular piece of work.



| [Step 4: Write a Minimal End-to-End Solution](http://zguide.zeromq.org/page:all#Step-Write-a-Minimal-End-to-End-Solution) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-194) [next](http://zguide.zeromq.org/page:all#header-196) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The goal is to test out the overall architecture as rapidly as possible. Make skeleton applications that call the APIs, and skeleton stacks that implement both sides of every protocol. You want to get a working end-to-end "Hello World" as soon as you can. You want to be able to test code as you write it, so that you can weed out the broken assumptions and inevitable errors you make. Do not go off and spend six months writing a test suite! Instead, make a minimal bare-bones application that uses our still-hypothetical API.

If you design an API wearing the hat of the person who implements it, you'll start to think of performance, features, options, and so on. You'll make it more complex, more irregular, and more surprising than it should be. But, and here's the trick (it's a cheap one, was big in Japan): if you design an API while wearing the hat of the person who has to actually write apps that use it, you use all that laziness and fear to your advantage.

Write down the protocols on a wiki or shared document in such a way that you can explain every command clearly without too much detail. Strip off any real functionality, because it will only create inertia that makes it harder to move stuff around. You can always add weight. Don't spend effort defining formal message structures: pass the minimum around in the simplest possible fashion using ZeroMQ's multipart framing.

Our goal is to get the simplest test case working, without any avoidable functionality. Everything you can chop off the list of things to do, you chop. Ignore the groans from colleagues and bosses. I'll repeat this once again: you can *always* add functionality, that's relatively easy. But aim to keep the overall weight to a minimum.



| [Step 5: Solve One Problem and Repeat](http://zguide.zeromq.org/page:all#Step-Solve-One-Problem-and-Repeat) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-195) [next](http://zguide.zeromq.org/page:all#header-197) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

You're now in the happy cycle of issue-driven development where you can start to solve tangible problems instead of adding features. Write issues that each state a clear problem, and propose a solution. As you design the API, keep in mind your standards for names, consistency, and behavior. Writing these down in prose often helps keep them sane.

From here, every single change you make to the architecture and code can be proven by running the test case, watching it not work, making the change, and then watching it work.

Now you go through the whole cycle (extending the test case, fixing the API, updating the protocol, and extending the code, as needed), taking problems one at a time and testing the solutions individually. It should take about 10-30 minutes for each cycle, with the occasional spike due to random confusion.



| [Unprotocols](http://zguide.zeromq.org/page:all#Unprotocols) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-196) [next](http://zguide.zeromq.org/page:all#header-198) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |



| [Protocols Without The Goats](http://zguide.zeromq.org/page:all#Protocols-Without-The-Goats) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-197) [next](http://zguide.zeromq.org/page:all#header-199) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

When this man thinks of protocols, this man thinks of massive documents written by committees, over years. This man thinks of the IETF, W3C, ISO, Oasis, regulatory capture, FRAND patent license disputes, and soon after, this man thinks of retirement to a nice little farm in northern Bolivia up in the mountains where the only other needlessly stubborn beings are the goats chewing up the coffee plants.

Now, I've nothing personal against committees. The useless folk need a place to sit out their lives with minimal risk of reproducing; after all, that only seems fair. But most committee protocols tend towards complexity (the ones that work), or trash (the ones we don't talk about). There's a few reasons for this. One is the amount of money at stake. More money means more people who want their particular prejudices and assumptions expressed in prose. But two is the lack of good abstractions on which to build. People have tried to build reusable protocol abstractions, like BEEP. Most did not stick, and those that did, like SOAP and XMPP, are on the complex side of things.

It used to be, decades ago, when the Internet was a young modest thing, that protocols were short and sweet. They weren't even "standards", but "requests for comments", which is as modest as you can get. It's been one of my goals since we started iMatix in 1995 to find a way for ordinary people like me to write small, accurate protocols without the overhead of the committees.

Now, ZeroMQ does appear to provide a living, successful protocol abstraction layer with its "we'll carry multipart messages over random transports" way of working. Because ZeroMQ deals silently with framing, connections, and routing, it's surprisingly easy to write full protocol specs on top of ZeroMQ, and in [Chapter 4 - Reliable Request-Reply Patterns](http://zguide.zeromq.org/page:all#reliable-request-reply) and [Chapter 5 - Advanced Pub-Sub Patterns](http://zguide.zeromq.org/page:all#advanced-pub-sub) I showed how to do this.

Somewhere around mid-2007, I kicked off the Digital Standards Organization to define new simpler ways of producing little standards, protocols, and specifications. In my defense, it was a quiet summer. At the time, I wrote that a new specification should take ["minutes to explain, hours to design, days to write, weeks to prove, months to become mature, and years to replace."](http://www.digistan.org/spec:1)

In 2010, we started calling such little specifications *unprotocols*, which some people might mistake for a dastardly plan for world domination by a shadowy international organization, but which really just means "protocols without the goats".



| [Contracts Are Hard](http://zguide.zeromq.org/page:all#Contracts-Are-Hard) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-198) [next](http://zguide.zeromq.org/page:all#header-200) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Writing contracts is perhaps the most difficult part of large-scale architecture. With unprotocols, we remove as much of the unnecessary friction as possible. What remains is still a hard set of problems to solve. A good contract (be it an API, a protocol, or a rental agreement) has to be simple, unambiguous, technically sound, and easy to enforce.

Like any technical skill, it's something you have to learn and practice. There are a series of specifications on the
[ZeroMQ RFC site](http://rfc.zeromq.org/), which are worth reading and using them as a basis for your own specifications when you find yourself in need.

I'll try to summarize my experience as a protocol writer:

- Start simple, and develop your specifications step-by-step. Don't solve problems you don't have in front of you.

- Use very clear and consistent language. A protocol may often break down into commands and fields; use clear short names for these entities.

- Try to avoid inventing concepts. Reuse anything you can from existing specifications. Use terminology that is obvious and clear to your audience.

- Make *nothing* for which you cannot demonstrate an immediate need. Your specification solves problems; it does not provide features. Make the simplest plausible solution for each problem that you identify.

- Implement your protocol *as you build it*, so that you are aware of the technical consequences of each choice. Use a language that makes it hard (like C) and not one that makes it easy (like Python).

- Test your specification *as you build it* on other people. Your best feedback on a specification is when someone else tries to implement it without the assumptions and knowledge that you have in your head.

- Cross-test rapidly and consistently, throwing others' clients against your servers and vice versa.

- Be prepared to throw it out and start again as often as needed. Plan for this, by layering your architecture so that e.g., you can keep an API but change the underlying protocols.

- Only use constructs that are independent of programming language and operating system.

- Solve a large problem in layers, making each layer an independent specification. Beware of creating monolithic protocols. Think about how reusable each layer is. Think about how different teams could build competing specifications at each layer.

And above all, *write it down*. Code is not a specification. The point about a written specification is that no matter how weak it is, it can be systematically improved. By writing down a specification, you will also spot inconsistencies and gray areas that are impossible to see in code.

If this sounds hard, don't worry too much. One of the less obvious benefits of using ZeroMQ is that it cuts the effort necessary to write a protocol spec by perhaps 90% or more because it already handles framing, routing, queuing, and so on. This means that you can experiment rapidly, make mistakes cheaply, and thus learn rapidly.



| [How to Write Unprotocols](http://zguide.zeromq.org/page:all#How-to-Write-Unprotocols) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-199) [next](http://zguide.zeromq.org/page:all#header-201) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

When you start to write an unprotocol specification document, stick to a consistent structure so that your readers know what to expect. Here is the structure I use:

- Cover section: with a 1-line summary, URL to the spec, formal name, version, who to blame.
- License for the text: absolutely needed for public specifications.
- The change process: i.e., how can I as a reader fix problems in the specification?
- Use of language: MUST, MAY, SHOULD, and so on, with a reference to RFC 2119.
- Maturity indicator: is this an experimental, draft, stable, legacy, or retired?
- Goals of the protocol: what problems is it trying to solve?
- Formal grammar: prevents arguments due to different interpretations of the text.
- Technical explanation: semantics of each message, error handling, and so on.
- Security discussion: explicitly, how secure the protocol is.
- References: to other documents, protocols, and so on.

Writing clear, expressive text is hard. Do avoid trying to describe implementations of the protocol. Remember that you're writing a contract. You describe in clear language the obligations and expectations of each party, the level of obligation, and the penalties for breaking the rules. You do not try to define *how* each party honors its part of the deal.

Here are some key points about unprotocols:

- As long as your process is open, then you don't need a committee: just make clean minimal designs and make sure anyone is free to improve them.

- If use an existing license, then you don't have legal worries afterwards. I use GPLv3 for my public specifications and advise you to do the same. For in-house work, standard copyright is perfect.

- Formality is valuable. That is, learn to write a formal grammar such as ABNF (Augmented Backus-Naur Form) and use this to fully document your messages.

- Use a market-driven life cycle process like [Digistan's COSS](http://www.digistan.org/spec:1) so that people place the right weight on your specs as they mature (or don't).



| [Why use the GPLv3 for Public Specifications?](http://zguide.zeromq.org/page:all#Why-use-the-GPLv-for-Public-Specifications) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-200) [next](http://zguide.zeromq.org/page:all#header-202) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The license you choose is particularly crucial for public specifications. Traditionally, protocols are published under custom licenses, where the authors own the text and derived works are forbidden. This sounds great (after all, who wants to see a protocol forked?), but it's in fact highly risky. A protocol committee is vulnerable to capture, and if the protocol is important and valuable, the incentive for capture grows.

Once captured, like some wild animals, an important protocol will often die. The real problem is that there's no way to *free* a captive protocol published under a conventional license. The word "free" isn't just an adjective to describe speech or air, it's also a verb, and the right to fork a work *against the wishes of the owner* is essential to avoiding capture.

Let me explain this in shorter words. Imagine that iMatix writes a protocol today that's really amazing and popular. We publish the spec and many people implement it. Those implementations are fast and awesome, and free as in beer. They start to threaten an existing business. Their expensive commercial product is slower and can't compete. So one day they come to our iMatix office in Maetang-Dong, South Korea, and offer to buy our firm. Because we're spending vast amounts on sushi and beer, we accept gratefully. With evil laughter, the new owners of the protocol stop improving the public version, close the specification, and add patented extensions. Their new products support this new protocol version, but the open source versions are legally blocked from doing so. The company takes over the whole market, and competition ends.

When you contribute to an open source project, you really want to know your hard work won't be used against you by a closed source competitor. This is why the GPL beats the "more permissive" BSD/MIT/X11 licenses for most contributors. These licenses give permission to cheat. This applies just as much to protocols as to source code.

When you implement a GPLv3 specification, your applications are of course yours, and licensed any way you like. But you can be certain of two things. One, that specification will *never* be embraced and extended into proprietary forms. Any derived forms of the specification must also be GPLv3. Two, no one who ever implements or uses the protocol will ever launch a patent attack on anything it covers, nor can they add their patented technology to it without granting the world a free license.



| [Using ABNF](http://zguide.zeromq.org/page:all#Using-ABNF) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-201) [next](http://zguide.zeromq.org/page:all#header-203) |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
|                                                            |                                                              |

My advice when writing protocol specs is to learn and use a formal grammar. It's just less hassle than allowing others to interpret what you mean, and then recover from the inevitable false assumptions. The target of your grammar is other people, engineers, not compilers.

My favorite grammar is ABNF, as defined by [RFC 2234](http://www.ietf.org/rfc/rfc2234.txt), because it is probably the simplest and most widely used formal language for defining bidirectional communications protocols. Most IETF (Internet Engineering Task Force) specifications use ABNF, which is good company to be in.

I'll give a 30-second crash course in writing ABNF. It may remind you of regular expressions. You write the grammar as rules. Each rule takes the form "name = elements". An element can be another rule (which you define below as another rule) or a pre-defined *terminal* like `CRLF`, `OCTET`, or a number. [The RFC](http://www.ietf.org/rfc/rfc2234.txt) lists all the terminals. To define alternative elements, separate with a slash. To define repetition, use an asterisk. To group elements, use parentheses. Read the RFC because it's not intuitive.

I'm not sure if this extension is proper, but I then prefix elements with "C:" and "S:" to indicate whether they come from the client or server.

Here's a piece of ABNF for an unprotocol called NOM that we'll come back to later in this chapter:

```
nom-protocol    = open-peering *use-peering

open-peering    = C:OHAI ( S:OHAI-OK / S:WTF )

use-peering     = C:ICANHAZ
                / S:CHEEZBURGER
                / C:HUGZ S:HUGZ-OK
                / S:HUGZ C:HUGZ-OK
```

I've actually used these keywords (`OHAI`, `WTF`) in commercial projects. They make developers giggly and happy. They confuse management. They're good in first drafts that you want to throw away later.



| [The Cheap or Nasty Pattern](http://zguide.zeromq.org/page:all#The-Cheap-or-Nasty-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-202) [next](http://zguide.zeromq.org/page:all#header-204) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

There is a general lesson I've learned over a couple of decades of writing protocols small and large. I call this the *Cheap or Nasty* pattern: you can often split your work into two aspects or layers and solve these separately—one using a "cheap" approach, the other using a "nasty" approach.

The key insight to making Cheap or Nasty work is to realize that many protocols mix a low-volume chatty part for control, and a high-volume asynchronous part for data. For instance, HTTP has a chatty dialog to authenticate and get pages, and an asynchronous dialog to stream data. FTP actually splits this over two ports; one port for control and one port for data.

Protocol designers who don't separate control from data tend to make horrid protocols, because the trade-offs in the two cases are almost totally opposed. What is perfect for control is bad for data, and what's ideal for data just doesn't work for control. It's especially true when we want high performance at the same time as extensibility and good error checking.

Let's break this down using a classic client/server use case. The client connects to the server and authenticates. It then asks for some resource. The server chats back, then starts to send data back to the client. Eventually, the client disconnects or the server finishes, and the conversation is over.

Now, before starting to design these messages, stop and think, and let's compare the control dialog and the data flow:

- The control dialog lasts a short time and involves very few messages. The data flow could last for hours or days, and involve billions of messages.

- The control dialog is where all the "normal" errors happen, e.g., not authenticated, not found, payment required, censored, and so on. In contrast, any errors that happen during the data flow are exceptional (disk full, server crashed).

- The control dialog is where things will change over time as we add more options, parameters, and so on. The data flow should barely change over time because the semantics of a resource are fairly constant over time.

- The control dialog is essentially a synchronous request/reply dialog. The data flow is essentially a one-way asynchronous flow.

These differences are critical. When we talk about performance, it applies *only* to data flows. It's pathological to design a one-time control dialog to be fast. Thus when we talk about the cost of serialization, this only applies to the data flow. The cost of encoding/decoding the control flow could be huge, and for many cases it would not change a thing. So we encode control using Cheap, and we encode data flows using Nasty.

Cheap is essentially synchronous, verbose, descriptive, and flexible. A Cheap message is full of rich information that can change for each application. Your goal as designer is to make this information easy to encode and parse, trivial to extend for experimentation or growth, and highly robust against change both forwards and backwards. The Cheap part of a protocol looks like this:

- It uses a simple self-describing structured encoding for data, be it XML, JSON, HTTP-style headers, or some other. Any encoding is fine as long as there are standard simple parsers for it in your target languages.

- It uses a straight request-reply model where each request has a success/failure reply. This makes it trivial to write correct clients and servers for a Cheap dialog.

- It doesn't try, even marginally, to be fast. Performance doesn't matter when you do something only once or a few times per session.

A Cheap parser is something you take off the shelf and throw data at. It shouldn't crash, shouldn't leak memory, should be highly tolerant, and should be relatively simple to work with. That's it.

Nasty however is essentially asynchronous, terse, silent, and inflexible. A Nasty message carries minimal information that practically never changes. Your goal as designer is to make this information ultrafast to parse, and possibly even impossible to extend and experiment with. The ideal Nasty pattern looks like this:

- It uses a hand-optimized binary layout for data, where every bit is precisely crafted.

- It uses a pure asynchronous model where one or both peers send data without acknowledgments (or if they do, they use sneaky asynchronous techniques like credit-based flow control).

- It doesn't try, even marginally, to be friendly. Performance is all that matters when you are doing something several million times per second.

A Nasty parser is something you write by hand, which writes or reads bits, bytes, words, and integers individually and precisely. It rejects anything it doesn't like, does no memory allocations at all, and never crashes.

Cheap or Nasty isn't a universal pattern; not all protocols have this dichotomy. Also, how you use Cheap or Nasty will depend on the situation. In some cases, it can be two parts of a single protocol. In other cases, it can be two protocols, one layered on top of the other.



| [Error Handling](http://zguide.zeromq.org/page:all#Error-Handling) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-203) [next](http://zguide.zeromq.org/page:all#header-205) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Using Cheap or Nasty makes error handling rather simpler. You have two kinds of commands and two ways to signal errors:

- Synchronous control commands: errors are normal: every request has a response that is either OK or an error response.
- Asynchronous data commands: errors are exceptional: bad commands are either discarded silently, or cause the whole connection to be closed.

It's usually good to distinguish a few kinds of errors, but as always keep it minimal and add only what you need.



| [Serializing Your Data](http://zguide.zeromq.org/page:all#Serializing-Your-Data) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-204) [next](http://zguide.zeromq.org/page:all#header-206) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

When we start to design a protocol, one of the first questions we face is how we encode data on the wire. There is no universal answer. There are a half-dozen different ways to serialize data, each with pros and cons. We'll explore some of these.



| [Abstraction Level](http://zguide.zeromq.org/page:all#Abstraction-Level) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-205) [next](http://zguide.zeromq.org/page:all#header-207) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Before looking at how to put data onto the wire, it's worth asking what data we actually want to exchange between applications. If we don't use any abstraction, we literally serialize and deserialize our internal state. That is, the objects and structures we use to implement our functionality.

Putting internal state onto the wire is however a really bad idea. It's like exposing internal state in an API. When you do this, you are hard-coding your implementation decisions into your protocols. You are also going to produce protocols that are significantly more complex than they need to be.

It's perhaps the main reason so many older protocols and APIs are so complex: their designers did not think about how to abstract them into simpler concepts. There is of course no guarantee than an abstraction will be *simpler*; that's where the hard work comes in.

A good protocol or API abstraction encapsulates natural patterns of use, and gives them name and properties that are predictable and regular. It chooses sensible defaults so that the main use cases can be specified minimally. It aims to be simple for the simple cases, and expressive for the rarer complex cases. It does not make any statements or assumptions about the internal implementation unless that is absolutely needed for interoperability.



| [ZeroMQ Framing](http://zguide.zeromq.org/page:all#ZeroMQ-Framing) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-206) [next](http://zguide.zeromq.org/page:all#header-208) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The simplest and most widely used serialization format for ZeroMQ applications is ZeroMQ's own multipart framing. For example, here is how the [Majordomo Protocol](http://rfc.zeromq.org/spec:7) defines a request:

```
Frame 0: Empty frame
Frame 1: "MDPW01" (six bytes, representing MDP/Worker v0.1)
Frame 2: 0x02 (one byte, representing REQUEST)
Frame 3: Client address (envelope stack)
Frame 4: Empty (zero bytes, envelope delimiter)
Frames 5+: Request body (opaque binary)
```

To read and write this in code is easy, but this is a classic example of a control flow (the whole of MDP is really, as it's a chatty request-reply protocol). When we came to improve MDP for the second version, we had to change this framing. Excellent, we broke all existing implementations!

Backwards compatibility is hard, but using ZeroMQ framing for control flows *does not help*. Here's how I should have designed this protocol if I'd followed my own advice (and I'll fix this in the next version). It's split into a Cheap part and a Nasty part, and uses the ZeroMQ framing to separate these:

```
Frame 0: "MDP/2.0" for protocol name and version
Frame 1: command header
Frame 2: command body
```

Where we'd expect to parse the command header in the various intermediaries (client API, broker, and worker API), and pass the command body untouched from application to application.



| [Serialization Languages](http://zguide.zeromq.org/page:all#Serialization-Languages) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-207) [next](http://zguide.zeromq.org/page:all#header-209) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Serialization languages have their fashions. XML used to be big as in popular, then it got big as in over-engineered, and then it fell into the hands of "Enterprise Information Architects" and it's not been seen alive since. Today's XML is the epitome of "somewhere in that mess is a small, elegant language trying to escape".

Still XML was way, way better than its predecessors, which included such monsters as the Standard Generalized Markup Language (SGML), which in turn was a cool breeze compared to mind-torturing beasts like EDIFACT. So the history of serialization languages seems to be of gradually emerging sanity, hidden by waves of revolting EIAs doing their best to hold onto their jobs.

JSON popped out of the JavaScript world as a quick-and-dirty "I'd rather resign than use XML here" way to throw data onto the wire and get it back again. JSON is just minimal XML expressed, sneakily, as JavaScript source code.

Here's a simple example of using JSON in a Cheap protocol:

```
"protocol": {
    "name": "MTL",
    "version": 1
},
"virtual-host": "test-env"
```

The same data in XML would be (XML forces us to invent a single top-level entity):

```
<command>
    <protocol name = "MTL" version = "1" />
    <virtual-host>test-env</virtual-host>
</command>
```

And here it is using plain-old HTTP-style headers:

```
Protocol: MTL/1.0
Virtual-host: test-env
```

These are all pretty equivalent as long as you don't go overboard with validating parsers, schemas, and other "trust us, this is all for your own good" nonsense. A Cheap serialization language gives you space for experimentation for free ("ignore any elements/attributes/headers that you don't recognize"), and it's simple to write generic parsers that, for example, thunk a command into a hash table, or vice versa.

However, it's not all roses. While modern scripting languages support JSON and XML easily enough, older languages do not. If you use XML or JSON, you create nontrivial dependencies. It's also somewhat of a pain to work with tree-structured data in a language like C.

So you can drive your choice according to the languages for which you're aiming. If your universe is a scripting language, then go for JSON. If you are aiming to build protocols for wider system use, keep things simple for C developers and stick to HTTP-style headers.



| [Serialization Libraries](http://zguide.zeromq.org/page:all#Serialization-Libraries) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-208) [next](http://zguide.zeromq.org/page:all#header-210) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The `msgpack.org` site says:

> It's like JSON, but fast and small. MessagePack is an efficient binary serialization format. It lets you exchange data among multiple languages like JSON, but it's faster and smaller. For example, small integers (like flags or error code) are encoded into a single byte, and typical short strings only require an extra byte in addition to the strings themselves.

I'm going to make the perhaps unpopular claim that "fast and small" are features that solve non-problems. The only real problem that serialization libraries solve is, as far as I can tell, the need to document the message contracts and actually serialize data to and from the wire.

Let's start by debunking "fast and small". It's based on a two-part argument. First, that making your messages smaller and reducing CPU cost for encoding and decoding will make a significant difference to your application's performance. Second, that this equally valid across-the-board to all messages.

But most real applications tend to fall into one of two categories. Either the speed of serialization and size of encoding is marginal compared to other costs, such as database access or application code performance. Or, network performance really is critical, and then all significant costs occur in a few specific message types.

Thus, aiming for "fast and small" across the board is a false optimization. You neither get the easy flexibility of Cheap for your infrequent control flows, nor do you get the brutal efficiency of Nasty for your high-volume data flows. Worse, the assumption that all messages are equal in some way can corrupt your protocol design. Cheap or Nasty isn't only about serialization strategies, it's also about synchronous versus asynchronous, error handling and the cost of change.

My experience is that most performance problems in message-based applications can be solved by (a) improving the application itself and (b) hand-optimizing the high-volume data flows. And to hand-optimize your most critical data flows, you need to cheat; to learn exploit facts about your data, something general purpose serializers cannot do.

Now let's address documentation and the need to write our contracts explicitly and formally, rather than only in code. This is a valid problem to solve, indeed one of the main ones if we're to build a long-lasting, large-scale message-based architecture.

Here is how we describe a typical message using the MessagePack interface definition language (IDL):

```
message Person {
  1: string surname
  2: string firstname
  3: optional string email
}
```

Now, the same message using the Google protocol buffers IDL:

```
message Person {
  required string surname = 1;
  required string firstname = 2;
  optional string email = 3;
}
```

It works, but in most practical cases wins you little over a serialization language backed by decent specifications written by hand or produced mechanically (we'll come to this). The price you'll pay is an extra dependency and quite probably, worse overall performance than if you used Cheap or Nasty.



| [Handwritten Binary Serialization](http://zguide.zeromq.org/page:all#Handwritten-Binary-Serialization) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-209) [next](http://zguide.zeromq.org/page:all#header-211) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

As you'll gather from this book, my preferred language for systems programming is C (upgraded to C99, with a constructor/destructor API model and generic containers). There are two reasons I like this modernized C language. First, I'm too weak-minded to learn a big language like C++. Life just seems filled with more interesting things to understand. Second, I find that this specific level of manual control lets me produce better results, faster.

The point here isn't C versus C++, but the value of manual control for high-end professional users. It's no accident that the best cars, cameras, and espresso machines in the world have manual controls. That level of on-the-spot fine tuning often makes the difference between world class success, and being second best.

When you are really, truly concerned about the speed of serialization and/or the size of the result (often these contradict each other), you need handwritten binary serialization. In other words, let's hear it for Mr. Nasty!

Your basic process for writing an efficient Nasty encoder/decoder (codec) is:

- Build representative data sets and test applications that can stress test your codec.
- Write a first dumb version of the codec.
- Test, measure, improve, and repeat until you run out of time and/or money.

Here are some of the techniques we use to make our codecs better:

- *Use a profiler.* There's simply no way to know what your code is doing until you've profiled it for function counts and for CPU cost per function. When you find your hot spots, fix them.

- *Eliminate memory allocations.* The heap is very fast on a modern Linux kernel, but it's still the bottleneck in most naive codecs. On older kernels, the heap can be tragically slow. Use local variables (the stack) instead of the heap where you can.

- *Test on different platforms and with different compilers and compiler options.* Apart from the heap, there are many other differences. You need to learn the main ones, and allow for them.

- *Use state to compress better.* If you are concerned about codec performance, you are almost definitely sending the same kinds of data many times. There will be redundancy between instances of data. You can detect these and use that to compress (e.g., a short value that means "same as last time").

- *Know your data.* The best compression techniques (in terms of CPU cost for compactness) require knowing about the data. For example, the techniques used to compress a word list, a video, and a stream of stock market data are all different.

- *Be ready to break the rules.* Do you really need to encode integers in big-endian network byte order? x86 and ARM account for almost all modern CPUs, yet use little-endian (ARM is actually bi-endian but Android, like Windows and iOS, is little-endian).



| [Code Generation](http://zguide.zeromq.org/page:all#Code-Generation) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-210) [next](http://zguide.zeromq.org/page:all#header-212) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Reading the previous two sections, you might have wondered, "could I write my own IDL generator that was better than a general purpose one?" If this thought wandered into your mind, it probably left pretty soon after, chased by dark calculations about how much work that actually involved.

What if I told you of a way to build custom IDL generators cheaply and quickly? You can have a way to get perfectly documented contracts, code that is as evil and domain-specific as you need it to be, and all you need to do is sign away your soul (*who ever really used that, am I right?*) just here…

At iMatix, until a few years ago, we used code generation to build ever larger and more ambitious systems until we decided the technology (GSL) was too dangerous for common use, and we sealed the archive and locked it with heavy chains in a deep dungeon. We actually posted it on GitHub. If you want to try the examples that are coming up, grab [the repository](https://github.com/imatix/gsl) and build yourself a `gsl` command. Typing "make" in the src subdirectory should do it (and if you're that guy who loves Windows, I'm sure you'll send a patch with project files).

This section isn't really about GSL at all, but about a useful and little-known trick that's useful for ambitious architects who want to scale themselves, as well as their work. Once you learn the trick, you can whip up your own code generators in a short time. The code generators most software engineers know about come with a single hard-coded model. For instance, Ragel "compiles executable finite state machines from regular languages", i.e., Ragel's model is a regular language. This certainly works for a good set of problems, but it's far from universal. How do you describe an API in Ragel? Or a project makefile? Or even a finite-state machine like the one we used to design the Binary Star pattern in [Chapter 4 - Reliable Request-Reply Patterns](http://zguide.zeromq.org/page:all#reliable-request-reply)?

All these would benefit from code generation, but there's no universal model. So the trick is to design your own models as you need them, and then make code generators as cheap compilers for that model. You need some experience in how to make good models, and you need a technology that makes it cheap to build custom code generators. A scripting language, like Perl and Python, is a good option. However, we actually built GSL specifically for this, and that's what I prefer.

Let's take a simple example that ties into what we already know. We'll see more extensive examples later, because I really do believe that code generation is crucial knowledge for large-scale work. In [Chapter 4 - Reliable Request-Reply Patterns](http://zguide.zeromq.org/page:all#reliable-request-reply), we developed the [Majordomo Protocol (MDP)](http://rfc.zeromq.org/spec:7), and wrote clients, brokers, and workers for that. Now could we generate those pieces mechanically, by building our own interface description language and code generators?

When we write a GSL model, we can use *any* semantics we like, in other words we can invent domain-specific languages on the spot. I'll invent a couple—see if you can guess what they represent:

```
slideshow
    name = Cookery level 3
    page
        title = French Cuisine
        item = Overview
        item = The historical cuisine
        item = The nouvelle cuisine
        item = Why the French live longer
    page
        title = Overview
        item = Soups and salads
        item = Le plat principal
        item = Béchamel and other sauces
        item = Pastries, cakes, and quiches
        item = Soufflé: cheese to strawberry
```

How about this one:

```
table
    name = person
    column
        name = firstname
        type = string
    column
        name = lastname
        type = string
    column
        name = rating
        type = integer
```

We could compile the first into a presentation. The second, we could compile into SQL to create and work with a database table. So for this exercise, our domain language, our *model*, consists of "classes" that contain "messages" that contain "fields" of various types. It's deliberately familiar. Here is the MDP client protocol:

```
<class name = "mdp_client">
    MDP/Client
    <header>
        <field name = "empty" type = "string" value = ""
            >Empty frame</field>
        <field name = "protocol" type = "string" value = "MDPC01"
            >Protocol identifier</field>
    </header>
    <message name = "request">
        Client request to broker
        <field name = "service" type = "string">Service name</field>
        <field name = "body" type = "frame">Request body</field>
    </message>
    <message name = "reply">
        Response back to client
        <field name = "service" type = "string">Service name</field>
        <field name = "body" type = "frame">Response body</field>
    </message>
</class>
```

And here is the MDP worker protocol:

```
<class name = "mdp_worker">
    MDP/Worker
    <header>
        <field name = "empty" type = "string" value = ""
            >Empty frame</field>
        <field name = "protocol" type = "string" value = "MDPW01"
            >Protocol identifier</field>
        <field name = "id" type = "octet">Message identifier</field>
    </header>
    <message name = "ready" id = "1">
        Worker tells broker it is ready
        <field name = "service" type = "string">Service name</field>
    </message>
    <message name = "request" id = "2">
        Client request to broker
        <field name = "client" type = "frame">Client address</field>
        <field name = "body" type = "frame">Request body</field>
    </message>
    <message name = "reply" id = "3">
        Worker returns reply to broker
        <field name = "client" type = "frame">Client address</field>
        <field name = "body" type = "frame">Request body</field>
    </message>
    <message name = "hearbeat" id = "4">
        Either peer tells the other it's still alive
    </message>
    <message name = "disconnect" id = "5">
        Either peer tells other the party is over
    </message>
</class>
```

GSL uses XML as its modeling language. XML has a poor reputation, having been dragged through too many enterprise sewers to smell sweet, but it has some strong positives, as long as you keep it simple. Any way to write a self-describing hierarchy of items and attributes would work.

Now here is a short IDL generator written in GSL that turns our protocol models into documentation:

```
.#  Trivial IDL generator (specs.gsl)
.#
.output "$(class.name).md"
## The $(string.trim (class.?''):left) Protocol
.for message
.   frames = count (class->header.field) + count (field)

A $(message.NAME) command consists of a multipart message of $(frames)
frames:

.   for class->header.field
.       if name = "id"
* Frame $(item ()): 0x$(message.id:%02x) (1 byte, $(message.NAME))
.       else
* Frame $(item ()): "$(value:)" ($(string.length ("$(value)")) \
bytes, $(field.:))
.       endif
.   endfor
.   index = count (class->header.field) + 1
.   for field
* Frame $(index): $(field.?'') \
.       if type = "string"
(printable string)
.       elsif type = "frame"
(opaque binary)
.           index += 1
.       else
.           echo "E: unknown field type: $(type)"
.       endif
.       index += 1
.   endfor
.endfor
```

The XML models and this script are in the subdirectory examples/models. To do the code generation, I give this command:

```
gsl -script:specs mdp_client.xml mdp_worker.xml
```

Here is the Markdown text we get for the worker protocol:

```
## The MDP/Worker Protocol

A READY command consists of a multipart message of 4 frames:

* Frame 1: "" (0 bytes, Empty frame)
* Frame 2: "MDPW01" (6 bytes, Protocol identifier)
* Frame 3: 0x01 (1 byte, READY)
* Frame 4: Service name (printable string)

A REQUEST command consists of a multipart message of 5 frames:

* Frame 1: "" (0 bytes, Empty frame)
* Frame 2: "MDPW01" (6 bytes, Protocol identifier)
* Frame 3: 0x02 (1 byte, REQUEST)
* Frame 4: Client address (opaque binary)
* Frame 6: Request body (opaque binary)

A REPLY command consists of a multipart message of 5 frames:

* Frame 1: "" (0 bytes, Empty frame)
* Frame 2: "MDPW01" (6 bytes, Protocol identifier)
* Frame 3: 0x03 (1 byte, REPLY)
* Frame 4: Client address (opaque binary)
* Frame 6: Request body (opaque binary)

A HEARBEAT command consists of a multipart message of 3 frames:

* Frame 1: "" (0 bytes, Empty frame)
* Frame 2: "MDPW01" (6 bytes, Protocol identifier)
* Frame 3: 0x04 (1 byte, HEARBEAT)

A DISCONNECT command consists of a multipart message of 3 frames:

* Frame 1: "" (0 bytes, Empty frame)
* Frame 2: "MDPW01" (6 bytes, Protocol identifier)
* Frame 3: 0x05 (1 byte, DISCONNECT)
```

This, as you can see, is close to what I wrote by hand in the original spec. Now, if you have cloned the `zguide` repository and you are looking at the code in `examples/models`, you can generate the MDP client and worker codecs. We pass the same two models to a different code generator:

```
gsl -script:codec_c mdp_client.xml mdp_worker.xml
```

Which gives us `mdp_client` and `mdp_worker` classes. Actually MDP is so simple that it's barely worth the effort of writing the code generator. The profit comes when we want to change the protocol (which we did for the standalone Majordomo project). You modify the protocol, run the command, and out pops more perfect code.

The `codec_c.gsl` code generator is not short, but the resulting codecs are much better than the handwritten code I originally put together for Majordomo. For instance, the handwritten code had no error checking and would die if you passed it bogus messages.

I'm now going to explain the pros and cons of GSL-powered model-oriented code generation. Power does not come for free and one of the greatest traps in our business is the ability to invent concepts out of thin air. GSL makes this particularly easy, so it can be an equally dangerous tool.

*Do not invent concepts*. The job of a designer is to remove problems, not add features.

Firstly, I will lay out the advantages of model-oriented code generation:

- You can create near-perfect abstractions that map to your real world. So, our protocol model maps 100% to the "real world" of Majordomo. This would be impossible without the freedom to tune and change the model in any way.
- You can develop these perfect models quickly and cheaply.
- You can generate *any* text output. From a single model, you can create documentation, code in any language, test tools—literally any output you can think of.
- You can generate (and I mean this literally) *perfect* output because it's cheap to improve your code generators to any level you want.
- You get a single source that combines specifications and semantics.
- You can leverage a small team to a massive size. At iMatix, we produced the million-line OpenAMQ messaging product out of perhaps 85K lines of input models, including the code generation scripts themselves.

Now let's look at the disadvantages:

- You add tool dependencies to your project.
- You may get carried away and create models for the pure joy of creating them.
- You may alienate newcomers, who will see "strange stuff", from your work.
- You may give people a strong excuse not to invest in your project.

Cynically, model-oriented abuse works great in environments where you want to produce huge amounts of perfect code that you can maintain with little effort and which *no one can ever take away from you.* Personally, I like to cross my rivers and move on. But if long-term job security is your thing, this is almost perfect.

So if you do use GSL and want to create open communities around your work, here is my advice:

- Use it only where you would otherwise be writing tiresome code by hand.
- Design natural models that are what people would expect to see.
- Write the code by hand first so you know what to generate.
- Do not overuse. Keep it simple! *Do not get too meta!!*
- Introduce gradually into a project.
- Put the generated code into your repositories.

We're already using GSL in some projects around ZeroMQ. For example, the high-level C binding, CZMQ, uses GSL to generate the socket options class (`zsockopt`). A 300-line code generator turns 78 lines of XML model into 1,500 lines of perfect, but really boring code. That's a good win.



| [Transferring Files](http://zguide.zeromq.org/page:all#Transferring-Files) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-211) [next](http://zguide.zeromq.org/page:all#header-213) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Let's take a break from the lecturing and get back to our first love and the reason for doing all of this: code.

"How do I send a file?" is a common question on the ZeroMQ mailing lists. This should not be surprising, because file transfer is perhaps the oldest and most obvious type of messaging. Sending files around networks has lots of use cases apart from annoying the copyright cartels. ZeroMQ is very good out of the box at sending events and tasks, but less good at sending files.

I've promised, for a year or two, to write a proper explanation. Here's a gratuitous piece of information to brighten your morning: the word "proper" comes from the archaic French *propre*, which means "clean". The dark age English common folk, not being familiar with hot water and soap, changed the word to mean "foreign" or "upper-class", as in "that's proper food!", but later the word came to mean just "real", as in "that's a proper mess you've gotten us into!"

So, file transfer. There are several reasons you can't just pick up a random file, blindfold it, and shove it whole into a message. The most obvious reason being that despite decades of determined growth in RAM sizes (and who among us old-timers doesn't fondly remember saving up for that 1024-byte memory extension card?!), disk sizes obstinately remain much larger. Even if we could send a file with one instruction (say, using a system call like sendfile), we'd hit the reality that networks are not infinitely fast nor perfectly reliable. After trying to upload a large file several times on a slow flaky network (WiFi, anyone?), you'll realize that a proper file transfer protocol needs a way to recover from failures. That is, it needs a way to send only the part of a file that wasn't yet received.

Finally, after all this, if you build a proper file server, you'll notice that simply sending massive amounts of data to lots of clients creates that situation we like to call, in the technical parlance, "server went belly-up due to all available heap memory being eaten by a poorly designed application". A proper file transfer protocol needs to pay attention to memory use.

We'll solve these problems properly, one-by-one, which should hopefully get us to a good and proper file transfer protocol running over ZeroMQ. First, let's generate a 1GB test file with random data (real power-of-two-giga-like-Von-Neumman-intended, not the fake silicon ones the memory industry likes to sell):

```
dd if=/dev/urandom of=testdata bs=1M count=1024
```

This is large enough to be troublesome when we have lots of clients asking for the same file at once, and on many machines, 1GB is going to be too large to allocate in memory anyhow. As a base reference, let's measure how long it takes to copy this file from disk back to disk. This will tell us how much our file transfer protocol adds on top (including network costs):

```
$ time cp testdata testdata2

real    0m7.143s
user    0m0.012s
sys     0m1.188s
```

The 4-figure precision is misleading; expect variations of 25% either way. This is just an "order of magnitude" measurement.

Here's our first cut at the code, where the client asks for the test data and the server just sends it, without stopping for breath, as a series of messages, where each message holds one *chunk*:

[fileio1: File transfer test, model 1 in C](javascript:;)


[Java](http://zguide.zeromq.org/java:fileio1) | [Python](http://zguide.zeromq.org/py:fileio1) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

Try this: set the HWM to 1,000 (in ZeroMQ v3.x this is the default) and then reduce the chunk size to 100K so we send 10K chunks in one go. Run the test, and you'll see it never finishes. As the `zmq_socket()` man page says with cheerful brutality, for the ROUTER socket: "ZMQ_HWM option action: Drop".

We have to control the amount of data the server sends up-front. There's no point in it sending more than the network can handle. Let's try sending one chunk at a time. In this version of the protocol, the client will explicitly say, "Give me chunk N", and the server will fetch that specific chunk from disk and send it.

Here's the improved second model, where the client asks for one chunk at a time, and the server only sends one chunk for each request it gets from the client:

[fileio2: File transfer test, model 2 in C](javascript:;)


[Java](http://zguide.zeromq.org/java:fileio2) | [Python](http://zguide.zeromq.org/py:fileio2) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

```
$ time ./fileio1
4296 chunks received, 1073741824 bytes

real    0m0.669s
user    0m0.056s
sys     0m1.048s

$ time ./fileio2
4295 chunks received, 1073741824 bytes

real    0m2.389s
user    0m0.312s
sys     0m2.136s
```

There are two valuable lessons here. First, while request-reply is easy, it's also too slow for high-volume data flows. Paying that 300 microseconds once would be fine. Paying it for every single chunk isn't acceptable, particularly on real networks with latencies of perhaps 1,000 times higher.

The second point is something I've said before but will repeat: it's incredibly easy to experiment, measure, and improve a protocol over ZeroMQ. And when the cost of something comes way down, you can afford a lot more of it. Do learn to develop and prove your protocols in isolation: I've seen teams waste time trying to improve poorly designed protocols that are too deeply embedded in applications to be easily testable or fixable.

Our model two file transfer protocol isn't so bad, apart from performance:

- It completely eliminates any risk of memory exhaustion. To prove that, we set the high-water mark to 1 in both sender and receiver.
- It lets the client choose the chunk size, which is useful because if there's any tuning of the chunk size to be done, for network conditions, for file types, or to reduce memory consumption further, it's the client that should be doing this.
- It gives us fully restartable file transfers.
- It allows the client to cancel the file transfer at any point in time.

If we just didn't have to do a request for each chunk, it'd be a usable protocol. What we need is a way for the server to send multiple chunks without waiting for the client to request or acknowledge each one. What are our choices?

- The server could send 10 chunks at once, then wait for a single acknowledgment. That's exactly like multiplying the chunk size by 10, so it's pointless. And yes, it's just as pointless for all values of 10.

- The server could send chunks without any chatter from the client but with a slight delay between each send, so that it would send chunks only as fast as the network could handle them. This would require the server to know what's happening at the network layer, which sounds like hard work. It also breaks layering horribly. And what happens if the network is really fast, but the client itself is slow? Where are chunks queued then?

- The server could try to spy on the sending queue, i.e., see how full it is, and send only when the queue isn't full. Well, ZeroMQ doesn't allow that because it doesn't work, for the same reason as throttling doesn't work. The server and network may be more than fast enough, but the client may be a slow little device.

- We could modify `libzmq` to take some other action on reaching HWM. Perhaps it could block? That would mean that a single slow client would block the whole server, so no thank you. Maybe it could return an error to the caller? Then the server could do something smart like… well, there isn't really anything it could do that's any better than dropping the message.

Apart from being complex and variously unpleasant, none of these options would even work. What we need is a way for the client to tell the server, asynchronously and in the background, that it's ready for more. We need some kind of asynchronous flow control. If we do this right, data should flow without interruption from the server to the client, but only as long as the client is reading it. Let's review our three protocols. This was the first one:

```
C: fetch
S: chunk 1
S: chunk 2
S: chunk 3
....
```

And the second introduced a request for each chunk:

```
C: fetch chunk 1
S: send chunk 1
C: fetch chunk 2
S: send chunk 2
C: fetch chunk 3
S: send chunk 3
C: fetch chunk 4
....
```

Now—waves hands mysteriously—here's a changed protocol that fixes the performance problem:

```
C: fetch chunk 1
C: fetch chunk 2
C: fetch chunk 3
S: send chunk 1
C: fetch chunk 4
S: send chunk 2
S: send chunk 3
....
```

It looks suspiciously similar. In fact, it's identical except that we send multiple requests without waiting for a reply for each one. This is a technique called "pipelining" and it works because our DEALER and ROUTER sockets are fully asynchronous.

Here's the third model of our file transfer test-bench, with pipelining. The client sends a number of requests ahead (the "credit") and then each time it processes an incoming chunk, it sends one more credit. The server will never send more chunks than the client has asked for:

[fileio3: File transfer test, model 3 in C](javascript:;)


[Java](http://zguide.zeromq.org/java:fileio3) | [Python](http://zguide.zeromq.org/py:fileio3) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

In the third model, I chose a pipeline size of 10 messages (each message is a chunk). This will cost a maximum of 2.5MB memory per client. So with 1GB of memory we can handle at least 400 clients. We can try to calculate the ideal pipeline size. It takes about 0.7 seconds to send the 1GB file, which is about 160 microseconds for a chunk. A round trip is 300 microseconds, so the pipeline needs to be at least 3-5 chunks to keep the server busy. In practice, I still got performance spikes with a pipeline of 5 chunks, probably because the credit messages sometimes get delayed by outgoing data. So at 10 chunks, it works consistently.

```
$ time ./fileio3
4291 chunks received, 1072741824 bytes

real    0m0.777s
user    0m0.096s
sys     0m1.120s
```

Do measure rigorously. Your calculations may be good, but the real world tends to have its own opinions.

What we've made is clearly not yet a real file transfer protocol, but it proves the pattern and I think it is the simplest plausible design. For a real working protocol, we might want to add some or all of:

- Authentication and access controls, even without encryption: the point isn't to protect sensitive data, but to catch errors like sending test data to production servers.

- A Cheap-style request including file path, optional compression, and other stuff we've learned is useful from HTTP (such as If-Modified-Since).

- A Cheap-style response, at least for the first chunk, that provides meta data such as file size (so the client can pre-allocate, and avoid unpleasant disk-full situations).

- The ability to fetch a set of files in one go, otherwise the protocol becomes inefficient for large sets of small files.

- Confirmation from the client when it's fully received a file, to recover from chunks that might be lost if the client disconnects unexpectedly.

So far, our semantic has been "fetch"; that is, the recipient knows (somehow) that they need a specific file, so they ask for it. The knowledge of which files exist and where they are is then passed out-of-band (e.g., in HTTP, by links in the HTML page).

How about a "push" semantic? There are two plausible use cases for this. First, if we adopt a centralized architecture with files on a main "server" (not something I'm advocating, but people do sometimes like this), then it's very useful to allow clients to upload files to the server. Second, it lets us do a kind of pub-sub for files, where the client asks for all new files of some type; as the server gets these, it forwards them to the client.

A fetch semantic is synchronous, while a push semantic is asynchronous. Asynchronous is less chatty, so faster. Also, you can do cute things like "subscribe to this path" thus creating a pub-sub file transfer architecture. That is so obviously awesome that I shouldn't need to explain what problem it solves.

Still, here is the problem with the fetch semantic: that out-of-band route to tell clients what files exist. No matter how you do this, it ends up being complex. Either clients have to poll, or you need a separate pub-sub channel to keep clients up-to-date, or you need user interaction.

Thinking this through a little more, though, we can see that fetch is just a special case of pub-sub. So we can get the best of both worlds. Here is the general design:

- Fetch this path
- Here is credit (repeat)

To make this work (and we will, my dear readers), we need to be a little more explicit about how we send credit to the server. The cute trick of treating a pipelined "fetch chunk" request as credit won't fly because the client doesn't know any longer what files actually exist, how large they are, anything. If the client says, "I'm good for 250,000 bytes of data", this should work equally for 1 file of 250K bytes, or 100 files of 2,500 bytes.

And this gives us "credit-based flow control", which effectively removes the need for high-water marks, and any risk of memory overflow.



| [State Machines](http://zguide.zeromq.org/page:all#State-Machines) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-212) [next](http://zguide.zeromq.org/page:all#header-214) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Software engineers tend to think of (finite) state machines as a kind of intermediary interpreter. That is, you take a regular language and compile that into a state machine, then execute the state machine. The state machine itself is rarely visible to the developer: it's an internal representation—optimized, compressed, and bizarre.

However, it turns out that state machines are also valuable as a first-class modeling languages for protocol handlers, e.g., ZeroMQ clients and servers. ZeroMQ makes it rather easy to design protocols, but we've never defined a good pattern for writing those clients and servers properly.

A protocol has at least two levels:

- How we represent individual messages on the wire.
- How messages flow between peers, and the significance of each message.

We've seen in this chapter how to produce codecs that handle serialization. That's a good start. But if we leave the second job to developers, that gives them a lot of room to interpret. As we make more ambitious protocols (file transfer + heartbeating + credit + authentication), it becomes less and less sane to try to implement clients and servers by hand.

Yes, people do this almost systematically. But the costs are high, and they're avoidable. I'll explain how to model protocols using state machines, and how to generate neat and solid code from those models.

My experience with using state machines as a software construction tool dates to 1985 and my first real job making tools for application developers. In 1991, I turned that knowledge into a free software tool called Libero, which spat out executable state machines from a simple text model.

The thing about Libero's model was that it was readable. That is, you described your program logic as named states, each accepting a set of events, each doing some real work. The resulting state machine hooked into your application code, driving it like a boss.

Libero was charmingly good at its job, fluent in many languages, and modestly popular given the enigmatic nature of state machines. We used Libero in anger in dozens of large distributed applications, one of which was finally switched off in 2011 after 20 years of operation. State-machine driven code construction worked so well that it's somewhat impressive that this approach never hit the mainstream of software engineering.

So in this section I'm going to explain Libero's model, and demonstrate how to use it to generate ZeroMQ clients and servers. We'll use GSL again, but like I said, the principles are general and you can put together code generators using any scripting language.

As a worked example, let's see how to carry-on a stateful dialog with a peer on a ROUTER socket. We'll develop the server using a state machine (and the client by hand). We have a simple protocol that I'll call "NOM". I'm using the oh-so-very-serious [keywords for unprotocols](http://unprotocols.org/blog:2) proposal:

```
nom-protocol    = open-peering *use-peering

open-peering    = C:OHAI ( S:OHAI-OK / S:WTF )

use-peering     = C:ICANHAZ
                / S:CHEEZBURGER
                / C:HUGZ S:HUGZ-OK
                / S:HUGZ C:HUGZ-OK
```

I've not found a quick way to explain the true nature of state machine programming. In my experience, it invariably takes a few days of practice. After three or four days' exposure to the idea, there is a near-audible "click!" as something in the brain connects all the pieces together. We'll make it concrete by looking at the state machine for our NOM server.

A useful thing about state machines is that you can read them state by state. Each state has a unique descriptive name and one or more *events*, which we list in any order. For each event, we perform zero or more *actions* and we then move to a *next state* (or stay in the same state).

In a ZeroMQ protocol server, we have a state machine instance *per client*. That sounds complex but it isn't, as we'll see. We describe our first state, `Start`, as having one valid event: `OHAI`. We check the user's credentials and then arrive in the Authenticated state.

**Figure 64 - The Start State**

![fig64.png](https://github.com/imatix/zguide/raw/master/images/fig64.png)

The `Check Credentials` action produces either an `ok` or an `error` event. It's in the Authenticated state that we handle these two possible events by sending an appropriate reply back to the client. If authentication failed, we return to the `Start` state where the client can try again.

**Figure 65 - The Authenticated State**

![fig65.png](https://github.com/imatix/zguide/raw/master/images/fig65.png)

When authentication has succeeded, we arrive in the Ready state. Here we have three possible events: an ICANHAZ or HUGZ message from the client, or a heartbeat timer event.

**Figure 66 - The Ready State**

![fig66.png](https://github.com/imatix/zguide/raw/master/images/fig66.png)

There are a few more things about this state machine model that are worth knowing:

- Events in upper case (like "HUGZ") are *external events* that come from the client as messages.
- Events in lower case (like "heartbeat") are *internal events*, produced by code in the server.
- The "Send SOMETHING" actions are shorthand for sending a specific reply back to the client.
- Events that aren't defined in a particular state are silently ignored.

Now, the original source for these pretty pictures is an XML model:

```
<class name = "nom_server" script = "server_c">

<state name = "start">
    <event name = "OHAI" next = "authenticated">
        <action name = "check credentials" />
    </event>
</state>

<state name = "authenticated">
    <event name = "ok" next = "ready">
        <action name = "send" message ="OHAI-OK" />
    </event>
    <event name = "error" next = "start">
        <action name = "send" message = "WTF" />
    </event>
</state>

<state name = "ready">
    <event name = "ICANHAZ">
        <action name = "send" message = "CHEEZBURGER" />
    </event>
    <event name = "HUGZ">
        <action name = "send" message = "HUGZ-OK" />
    </event>
    <event name = "heartbeat">
        <action name = "send" message = "HUGZ" />
    </event>
</state>
</class>
```

The code generator is in `examples/models/server_c.gsl`. It is a fairly complete tool that I'll use and expand for more serious work later. It generates:

- A server class in C (`nom_server.c`, `nom_server.h`) that implements the whole protocol flow.
- A selftest method that runs the selftest steps listed in the XML file.
- Documentation in the form of graphics (the pretty pictures).

Here's a simple main program that starts the generated NOM server:

\#include "czmq.h"
\#include "nom_server.h"

int main (int argc, char *argv [])
{
`    `printf ("Starting NOM protocol server on port 5670…**\n**");
`    `nom_server_t *server = nom_server_new ();
`    `nom_server_bind (server, "tcp://*:5670");
`    `nom_server_wait (server);
`    `nom_server_destroy (&server);
`    `**return** 0;
}

The generated nom_server class is a fairly classic model. It accepts client messages on a ROUTER socket, so the first frame on every request is the client's connection identity. The server manages a set of clients, each with state. As messages arrive, it feeds these as *events* to the state machine. Here's the core of the state machine, as a mix of GSL commands and the C code we intend to generate:

client_execute (client_t *self, int event)
{
`    `self->next_event = event;
`    `**while** (self->next_event) {
`        `self->event = self->next_event;
`        `self->next_event = 0;
`        `**switch** (self->state) {
.**for** class.state
`            `**case** $(name:c)_state:
.`   `**for** event
.`       `**if** index () > 1
`                `**else**
.`       `endif
`                `**if** (self->event == $(name:c)_event) {
.`       `**for** action
.`           `**if** name = "send"
`                    `zmsg_addstr (self->reply, "$(message:)");
.`           `**else**
`                `$(name:c)_action (self);
.`           `endif
.`       `endfor
.`       `**if** defined (event.next)
`                    `self->state = $(next:c)_state;
.`       `endif
`                }`
.`   `endfor
`                `**break**;
.endfor
`        }`
`        `**if** (zmsg_size (self->reply) > 1) {
`            `zmsg_send (&self->reply, self->router);
`            `self->reply = zmsg_new ();
`            `zmsg_add (self->reply, zframe_dup (self->address));
`        }`
`    }`
}

Each client is held as an object with various properties, including the variables we need to represent a state machine instance:

event_t next_event;`         `*//  Next event*
state_t state;`              `*//  Current state*
event_t event;`              `*//  Current event*

You will see by now that we are generating technically-perfect code that has the precise design and shape we want. The only clue that the `nom_server` class isn't handwritten is that the code is *too good*. People who complain that code generators produce poor code are accustomed to poor code generators. It is trivial to extend our model as we need it. For example, here's how we generate the selftest code.

First, we add a "selftest" item to the state machine and write our tests. We're not using any XML grammar or validation so it really is just a matter of opening the editor and adding half-a-dozen lines of text:

```
<selftest>
    <step send = "OHAI" body = "Sleepy" recv = "WTF" />
    <step send = "OHAI" body = "Joe" recv = "OHAI-OK" />
    <step send = "ICANHAZ" recv = "CHEEZBURGER" />
    <step send = "HUGZ" recv = "HUGZ-OK" />
    <step recv = "HUGZ" />
</selftest>
```

Designing on the fly, I decided that "send" and "recv" were a nice way to express "send this request, then expect this reply". Here's the GSL code that turns this model into real code:

```
.for class->selftest.step
.   if defined (send)
    msg = zmsg_new ();
    zmsg_addstr (msg, "$(send:)");
.       if defined (body)
    zmsg_addstr (msg, "$(body:)");
.       endif
    zmsg_send (&msg, dealer);

.   endif
.   if defined (recv)
    msg = zmsg_recv (dealer);
    assert (msg);
    command = zmsg_popstr (msg);
    assert (streq (command, "$(recv:)"));
    free (command);
    zmsg_destroy (&msg);

.   endif
.endfor
```

Finally, one of the more tricky but absolutely essential parts of any state machine generator is *how do I plug this into my own code?* As a minimal example for this exercise I wanted to implement the "check credentials" action by accepting all OHAIs from my friend Joe (Hi Joe!) and reject everyone else's OHAIs. After some thought, I decided to grab code directly from the state machine model, i.e., embed action bodies in the XML file. So in `nom_server.xml`, you'll see this:

```
<action name = "check credentials">
    char *body = zmsg_popstr (self->request);
    if (body && streq (body, "Joe"))
        self->next_event = ok_event;
    else
        self->next_event = error_event;
    free (body);
</action>
```

And the code generator grabs that C code and inserts it into the generated `nom_server.c`file:

```
.for class.action
static void
$(name:c)_action (client_t *self) {
$(string.trim (.):)
}
.endfor
```

And now we have something quite elegant: a single source file that describes my server state machine and also contains the native implementations for my actions. A nice mix of high-level and low-level that is about 90% smaller than the C code.

Beware, as your head spins with notions of all the amazing things you could produce with such leverage. While this approach gives you real power, it also moves you away from your peers, and if you go too far, you'll find yourself working alone.

By the way, this simple little state machine design exposes just three variables to our custom code:

- `self->next_event`
- `self->request`
- `self->reply`

In the Libero state machine model, there are a few more concepts that we've not used here, but which we will need when we write larger state machines:

- Exceptions, which lets us write terser state machines. When an action raises an exception, further processing on the event stops. The state machine can then define how to handle exception events.
- The `Defaults` state, where we can define default handling for events (especially useful for exception events).



| [Authentication Using SASL](http://zguide.zeromq.org/page:all#Authentication-Using-SASL) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-213) [next](http://zguide.zeromq.org/page:all#header-215) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

When we designed AMQP in 2007, we chose the [Simple Authentication and Security Layer](http://en.wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)(SASL) for the authentication layer, one of the ideas we took from the [BEEP protocol framework](http://www.rfc-editor.org/rfc/rfc3080.txt). SASL looks complex at first, but it's actually simple and fits neatly into a ZeroMQ-based protocol. What I especially like about SASL is that it's scalable. You can start with anonymous access or plain text authentication and no security, and grow to more secure mechanisms over time without changing your protocol.

I'm not going to give a deep explanation now because we'll see SASL in action somewhat later. But I'll explain the principle so you're already somewhat prepared.

In the NOM protocol, the client started with an OHAI command, which the server either accepted ("Hi Joe!") or rejected. This is simple but not scalable because server and client have to agree up-front on the type of authentication they're going to do.

What SASL introduced, which is genius, is a fully abstracted and negotiable security layer that's still easy to implement at the protocol level. It works as follows:

- The client connects.
- The server challenges the client, passing a list of security "mechanisms" that it knows about.
- The client chooses a security mechanism that it knows about, and answers the server's challenge with a blob of opaque data that (and here's the neat trick) some generic security library calculates and gives to the client.
- The server takes the security mechanism the client chose, and that blob of data, and passes it to its own security library.
- The library either accepts the client's answer, or the server challenges again.

There are a number of free SASL libraries. When we come to real code, we'll implement just two mechanisms, ANONYMOUS and PLAIN, which don't need any special libraries.

To support SASL, we have to add an optional challenge/response step to our "open-peering" flow. Here is what the resulting protocol grammar looks like (I'm modifying NOM to do this):

```
secure-nom      = open-peering *use-peering

open-peering    = C:OHAI *( S:ORLY C:YARLY ) ( S:OHAI-OK / S:WTF )

ORLY            = 1*mechanism challenge
mechanism       = string
challenge       = *OCTET

YARLY           = mechanism response
response        = *OCTET
```

Where ORLY and YARLY contain a string (a list of mechanisms in ORLY, one mechanism in YARLY) and a blob of opaque data. Depending on the mechanism, the initial challenge from the server may be empty. We don't care: we just pass this to the security library to deal with.

The SASL [RFC](http://tools.ietf.org/html/rfc4422) goes into detail about other features (that we don't need), the kinds of ways SASL could be attacked, and so on.



| [Large-Scale File Publishing: FileMQ](http://zguide.zeromq.org/page:all#Large-Scale-File-Publishing-FileMQ) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-214) [next](http://zguide.zeromq.org/page:all#header-216) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Let's put all these techniques together into a file distribution system that I'll call FileMQ. This is going to be a real product, living on [GitHub](https://github.com/zeromq/filemq). What we'll make here is a first version of FileMQ, as a training tool. If the concept works, the real thing may eventually get its own book.



| [Why make FileMQ?](http://zguide.zeromq.org/page:all#Why-make-FileMQ) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-215) [next](http://zguide.zeromq.org/page:all#header-217) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Why make a file distribution system? I already explained how to send large files over ZeroMQ, and it's really quite simple. But if you want to make messaging accessible to a million times more people than can use ZeroMQ, you need another kind of API. An API that my five-year old son can understand. An API that is universal, requires no programming, and works with just about every single application.

Yes, I'm talking about the file system. It's the DropBox pattern: chuck your files somewhere and they get magically copied somewhere else when the network connects again.

However, what I'm aiming for is a fully decentralized architecture that looks more like git, that doesn't need any cloud services (though we could put FileMQ in the cloud), and that does multicast, i.e., can send files to many places at once.

FileMQ must be secure(able), easily hooked into random scripting languages, and as fast as possible across our domestic and office networks.

I want to use it to back up photos from my mobile phone to my laptop over WiFi. To share presentation slides in real time across 50 laptops in a conference. To share documents with colleagues in a meeting. To send earthquake data from sensors to central clusters. To back up video from my phone as I take it, during protests or riots. To synchronize configuration files across a cloud of Linux servers.

A visionary idea, isn't it? Well, ideas are cheap. The hard part is making this, and making it simple.



| [Initial Design Cut: the API](http://zguide.zeromq.org/page:all#Initial-Design-Cut-the-API) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-216) [next](http://zguide.zeromq.org/page:all#header-218) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Here's the way I see the first design. FileMQ has to be distributed, which means that every node can be a server and a client at the same time. But I don't want the protocol to be symmetrical, because that seems forced. We have a natural flow of files from point A to point B, where A is the "server" and B is the "client". If files flow back the other way, then we have two flows. FileMQ is not yet directory synchronization protocol, but we'll bring it quite close.

Thus, I'm going to build FileMQ as two pieces: a client and a server. Then, I'll put these together in a main application (the `filemq` tool) that can act both as client and server. The two pieces will look quite similar to the `nom_server`, with the same kind of API:

fmq_server_t *server = fmq_server_new ();
fmq_server_bind (server, "tcp://*:5670");
fmq_server_publish (server, "/home/ph/filemq/share", "/public");
fmq_server_publish (server, "/home/ph/photos/stream", "/photostream");

fmq_client_t *client = fmq_client_new ();
fmq_client_connect (client, "tcp://pieter.filemq.org:5670");
fmq_client_subscribe (server, "/public/", "/home/ph/filemq/share");
fmq_client_subscribe (server, "/photostream/", "/home/ph/photos/stream");

**while** (!zctx_interrupted)
`    `sleep (1);

fmq_server_destroy (&server);
fmq_client_destroy (&client);

If we wrap this C API in other languages, we can easily script FileMQ, embed it applications, port it to smartphones, and so on.



| [Initial Design Cut: the Protocol](http://zguide.zeromq.org/page:all#Initial-Design-Cut-the-Protocol) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-217) [next](http://zguide.zeromq.org/page:all#header-219) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The full name for the protocol is the "File Message Queuing Protocol", or FILEMQ in uppercase to distinguish it from the software. To start with, we write down the protocol as an ABNF grammar. Our grammar starts with the flow of commands between the client and server. You should recognize these as a combination of the various techniques we've seen already:

```
filemq-protocol = open-peering *use-peering [ close-peering ]

open-peering    = C:OHAI *( S:ORLY C:YARLY ) ( S:OHAI-OK / error )

use-peering     = C:ICANHAZ ( S:ICANHAZ-OK / error )
                / C:NOM
                / S:CHEEZBURGER
                / C:HUGZ S:HUGZ-OK
                / S:HUGZ C:HUGZ-OK

close-peering   = C:KTHXBAI / S:KTHXBAI

error           = S:SRSLY / S:RTFM
```

Here are the commands to and from the server:

```
;   The client opens peering to the server
OHAI            = signature %x01 protocol version
signature       = %xAA %xA3
protocol        = string        ; Must be "FILEMQ"
string          = size *VCHAR
size            = OCTET
version         = %x01

;   The server challenges the client using the SASL model
ORLY            = signature %x02 mechanisms challenge
mechanisms      = size 1*mechanism
mechanism       = string
challenge       = *OCTET        ; ZeroMQ frame

;   The client responds with SASL authentication information
YARLY           = %signature x03 mechanism response
response        = *OCTET        ; ZeroMQ frame

;   The server grants the client access
OHAI-OK         = signature %x04

;   The client subscribes to a virtual path
ICANHAZ         = signature %x05 path options cache
path            = string        ; Full path or path prefix
options         = dictionary
dictionary      = size *key-value
key-value       = string        ; Formatted as name=value
cache           = dictionary    ; File SHA-1 signatures

;   The server confirms the subscription
ICANHAZ-OK      = signature %x06

;   The client sends credit to the server
NOM             = signature %x07 credit
credit          = 8OCTET        ; 64-bit integer, network order
sequence        = 8OCTET        ; 64-bit integer, network order

;   The server sends a chunk of file data
CHEEZBURGER     = signature %x08 sequence operation filename
                  offset headers chunk
sequence        = 8OCTET        ; 64-bit integer, network order
operation       = OCTET
filename        = string
offset          = 8OCTET        ; 64-bit integer, network order
headers         = dictionary
chunk           = FRAME

;   Client or server sends a heartbeat
HUGZ            = signature %x09

;   Client or server responds to a heartbeat
HUGZ-OK         = signature %x0A

;   Client closes the peering
KTHXBAI         = signature %x0B
```

And here are the different ways the server can tell the client things went wrong:

```
;   Server error reply - refused due to access rights
S:SRSLY         = signature %x80 reason

;   Server error reply - client sent an invalid command
S:RTFM          = signature %x81 reason
```

FILEMQ lives on the [ZeroMQ unprotocols website](http://rfc.zeromq.org/spec:19) and has a registered TCP port with IANA (the Internet Assigned Numbers Authority), which is port 5670.



| [Building and Trying FileMQ](http://zguide.zeromq.org/page:all#Building-and-Trying-FileMQ) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-218) [next](http://zguide.zeromq.org/page:all#header-220) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The FileMQ stack is [on GitHub](https://github.com/zeromq/filemq). It works like a classic C/C++ project:

```
git clone git://github.com/zeromq/filemq.git
cd filemq
./autogen.sh
./configure
make check
```

You want to be using the latest CZMQ master for this. Now try running the `track` command, which is a simple tool that uses FileMQ to track changes in one directory in another:

```
cd src
./track ./fmqroot/send ./fmqroot/recv
```

And open two file navigator windows, one into `src/fmqroot/send` and one into `src/fmqroot/recv`. Drop files into the send folder and you'll see them arrive in the recv folder. The server checks once per second for new files. Delete files in the send folder, and they're deleted in the recv folder similarly.

I use track for things like updating my MP3 player mounted as a USB drive. As I add or remove files in my laptop's Music folder, the same changes happen on the MP3 player. FILEMQ isn't a full replication protocol yet, but we'll fix that later.



| [Internal Architecture](http://zguide.zeromq.org/page:all#Internal-Architecture) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-219) [next](http://zguide.zeromq.org/page:all#header-221) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

To build FileMQ I used a lot of code generation, possibly too much for a tutorial. However the code generators are all reusable in other stacks and will be important for our final project in [A Framework for Distributed Computing](http://zguide.zeromq.org/page:all#moving-pieces). They are an evolution of the set we saw earlier:

- `codec_c.gsl`: generates a message codec for a given protocol.
- `server_c.gsl`: generates a server class for a protocol and state machine.
- `client_c.gsl`: generates a client class for a protocol and state machine.

The best way to learn to use GSL code generation is to translate these into a language of your choice and make your own demo protocols and stacks. You'll find it fairly easy. FileMQ itself doesn't try to support multiple languages. It could, but it'd make things needlessly complex.

The FileMQ architecture actually slices into two layers. There's a generic set of classes to handle chunks, directories, files, patches, SASL security, and configuration files. Then, there's the generated stack: messages, client, and server. If I was creating a new project I'd fork the whole FileMQ project, and go and modify the three models:

- `fmq_msg.xml`: defines the message formats
- `fmq_client.xml`: defines the client state machine, API, and implementation.
- `fmq_server.xml`: does the same for the server.

You'd want to rename things to avoid confusion. Why didn't I make the reusable classes into a separate library? The answer is two-fold. First, no one actually needs this (yet). Second, it'd make things more complex for you as you build and play with FileMQ. It's never worth adding complexity to solve a theoretical problem.

Although I wrote FileMQ in C, it's easy to map to other languages. It is quite amazing how nice C becomes when you add CZMQ's generic zlist and zhash containers and class style. Let me go through the classes quickly:

- `fmq_sasl`: encodes and decodes a SASL challenge. I only implemented the PLAIN mechanism, which is enough to prove the concept.

- `fmq_chunk`: works with variable sized blobs. Not as efficient as ZeroMQ's messages but they do less weirdness and so are easier to understand. The chunk class has methods to read and write chunks from disk.

- `fmq_file`: works with files, which may or may not exist on disk. Gives you information about a file (like size), lets you read and write to files, remove files, check if a file exists, and check if a file is "stable" (more on that later).

- `fmq_dir`: works with directories, reading them from disk and comparing two directories to see what changed. When there are changes, returns a list of "patches".

- `fmq_patch`: works with one patch, which really just says "create this file" or "delete this file" (referring to a fmq_file item each time).

- `fmq_config`: works with configuration data. I'll come back to client and server configuration later.

Every class has a test method, and the main development cycle is "edit, test". These are mostly simple self tests, but they make the difference between code I can trust and code I know will still break. It's a safe bet that any code that isn't covered by a test case will have undiscovered errors. I'm not a fan of external test harnesses. But internal test code that you write as you write your functionality… that's like the handle on a knife.

You should, really, be able to read the source code and rapidly understand what these classes are doing. If you can't read the code happily, tell me. If you want to port the FileMQ implementation into other languages, start by forking the whole repository and later we'll see if it's possible to do this in one overall repo.



| [Public API](http://zguide.zeromq.org/page:all#Public-API) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-220) [next](http://zguide.zeromq.org/page:all#header-222) |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
|                                                            |                                                              |

The public API consists of two classes (as we sketched earlier):

- `fmq_client`: provides the client API, with methods to connect to a server, configure the client, and subscribe to paths.

- `fmq_server`: provides the server API, with methods to bind to a port, configure the server, and publish a path.

These classes provide an *multithreaded API*, a model we've used a few times now. When you create an API instance (i.e., `fmq_server_new()` or `fmq_client_new()`), this method kicks off a background thread that does the real work, i.e., runs the server or the client. The other API methods then talk to this thread over ZeroMQ sockets (a *pipe* consisting of two PAIR sockets over inproc://).

If I was a keen young developer eager to use FileMQ in another language, I'd probably spend a happy weekend writing a binding for this public API, then stick it in a subdirectory of the filemq project called, say, `bindings/`, and make a pull request.

The actual API methods come from the state machine description, like this (for the server):

```
<method name = "publish">
<argument name = "location" type = "string" />
<argument name = "alias" type = "string" />
mount_t *mount = mount_new (location, alias);
zlist_append (self->mounts, mount);
</method>
```

Which gets turned into this code:

void
fmq_server_publish (fmq_server_t *self, char *location, char *alias)
{
`    `assert (self);
`    `assert (location);
`    `assert (alias);
`    `zstr_sendm (self->pipe, "PUBLISH");
`    `zstr_sendfm (self->pipe, "%s", location);
`    `zstr_sendf (self->pipe, "%s", alias);
}



| [Design Notes](http://zguide.zeromq.org/page:all#Design-Notes) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-221) [next](http://zguide.zeromq.org/page:all#header-223) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The hardest part of making FileMQ wasn't implementing the protocol, but maintaining accurate state internally. An FTP or HTTP server is essentially stateless. But a publish/subscribe server *has* to maintain subscriptions, at least.

So I'll go through some of the design aspects:

- The client detects if the server has died by the lack of heartbeats (`HUGZ`) coming from the server. It then restarts its dialog by sending an `OHAI`. There's no timeout on the `OHAI` because the ZeroMQ DEALER socket will queue an outgoing message indefinitely.

- If a client stops replying with (`HUGZ-OK`) to the heartbeats that the server sends, the server concludes that the client has died and deletes all state for the client including its subscriptions.

- The client API holds subscriptions in memory and replays them when it has connected successfully. This means the caller can subscribe at any time (and doesn't care when connections and authentication actually happen).

- The server and client use virtual paths, much like an HTTP or FTP server. You publish one or more *mount points*, each corresponding to a directory on the server. Each of these maps to some virtual path, for instance "/" if you have only one mount point. Clients then subscribe to virtual paths, and files arrive in an inbox directory. We don't send physical file names across the network.

- There are some timing issues: if the server is creating its mount points while clients are connected and subscribing, the subscriptions won't attach to the right mount points. So, we bind the server port as last thing.

- Clients can reconnect at any point; if the client sends `OHAI`, that signals the end of any previous conversation and the start of a new one. I might one day make subscriptions durable on the server, so they survive a disconnection. The client stack, after reconnecting, replays any subscriptions the caller application already made.



| [Configuration](http://zguide.zeromq.org/page:all#Configuration) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-222) [next](http://zguide.zeromq.org/page:all#header-224) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

I've built several large server products, like the Xitami web server that was popular in the late 90's, and the [OpenAMQ messaging server](http://www.openamq.org/). Getting configuration easy and obvious was a large part of making these servers fun to use.

We typically aim to solve a number of problems:

- Ship default configuration files with the product.
- Allow users to add custom configuration files that are never overwritten.
- Allow users to configure from the command-line.

And then layer these one on the other, so command-line settings override custom settings, which override default settings. It can be a lot of work to do this right. For FileMQ, I've taken a somewhat simpler approach: all configuration is done from the API.

This is how we start and configure the server, for example:

server = fmq_server_new ();
fmq_server_configure (server, "server_test.cfg");
fmq_server_publish (server, "./fmqroot/send", "/");
fmq_server_publish (server, "./fmqroot/logs", "/logs");
fmq_server_bind (server, "tcp://*:5670");

We do use a specific format for the config files, which is [ZPL](http://rfc.zeromq.org/spec:4), a minimalist syntax that we started using for ZeroMQ "devices" a few years ago, but which works well for any server:

```
#   Configure server for plain access
#
server
    monitor = 1             #   Check mount points
    heartbeat = 1           #   Heartbeat to clients

publish
    location = ./fmqroot/logs
    virtual = /logs

security
    echo = I: use guest/guest to login to server
    #   These are SASL mechanisms we accept
    anonymous = 0
    plain = 1
        account
            login = guest
            password = guest
            group = guest
        account
            login = super
            password = secret
            group = admin
```

One cute thing (which seems useful) the generated server code does is to parse this config file (when you use the `fmq_server_configure()` method) and execute any section that matches an API method. Thus the `publish` section works as a `fmq_server_publish()`method.



| [File Stability](http://zguide.zeromq.org/page:all#File-Stability) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-223) [next](http://zguide.zeromq.org/page:all#header-225) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

It is quite common to poll a directory for changes and then do something "interesting" with new files. But as one process is writing to a file, other processes have no idea when the file has been fully written. One solution is to add a second "indicator" file that we create after creating the first file. This is intrusive, however.

There is a neater way, which is to detect when a file is "stable", i.e., no one is writing to it any longer. FileMQ does this by checking the modification time of the file. If it's more than a second old, then the file is considered stable, at least stable enough to be shipped off to clients. If a process comes along after five minutes and appends to the file, it'll be shipped off again.

For this to work, and this is a requirement for any application hoping to use FileMQ successfully, do not buffer more than a second's worth of data in memory before writing. If you use very large block sizes, the file may look stable when it's not.



| [Delivery Notifications](http://zguide.zeromq.org/page:all#Delivery-Notifications) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-224) [next](http://zguide.zeromq.org/page:all#header-226) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

One of the nice things about the multithreaded API model we're using is that it's essentially message based. This makes it ideal for returning events back to the caller. A more conventional API approach would be to use callbacks. But callbacks that cross thread boundaries are somewhat delicate. Here's how the client sends a message back when it has received a complete file:

zstr_sendm (self->pipe, "DELIVER");
zstr_sendm (self->pipe, filename);
zstr_sendf (self->pipe, "%s/%s", inbox, filename);

We can now add a _recv() method to the API that waits for events back from the client. It makes a clean style for the caller: create the client object, configure it, and then receive and process any events it returns.



| [Symbolic Links](http://zguide.zeromq.org/page:all#Symbolic-Links) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-225) [next](http://zguide.zeromq.org/page:all#header-227) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

While using a staging area is a nice, simple API, it also creates costs for senders. If I already have a 2GB video file on a camera, and want to send it via FileMQ, the current implementation asks that I copy it to a staging area before it will be sent to subscribers.

One option is to mount the whole content directory (e.g., `/home/me/Movies`), but this is fragile because it means the application can't decide to send individual files. It's everything or nothing.

A simple answer is to implement portable symbolic links. As Wikipedia explains: "A symbolic link contains a text string that is automatically interpreted and followed by the operating system as a path to another file or directory. This other file or directory is called the *target*. The symbolic link is a second file that exists independently of its target. If a symbolic link is deleted, its target remains unaffected."

This doesn't affect the protocol in any way; it's an optimization in the server implementation. Let's make a simple portable implementation:

- A symbolic link consists of a file with the extension `.ln`.
- The filename without `.ln` is the published file name.
- The link file contains one line, which is the real path to the file.

Because we've collected all operations on files in a single class (`fmq_file`), it's a clean change. When we create a new file object, we check if it's a symbolic link and then all read-only actions (get file size, read file) operate on the target file, not the link.



| [Recovery and Late Joiners](http://zguide.zeromq.org/page:all#Recovery-and-Late-Joiners) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-226) [next](http://zguide.zeromq.org/page:all#header-228) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

As it stands now, FileMQ has one major remaining problem: it provides no way for clients to recover from failures. The scenario is that a client, connected to a server, starts to receive files and then disconnects for some reason. The network may be too slow, or breaks. The client may be on a laptop which is shut down, then resumed. The WiFi may be disconnected. As we move to a more mobile world (see [A Framework for Distributed Computing](http://zguide.zeromq.org/page:all#moving-pieces)) this use case becomes more and more frequent. In some ways it's becoming a dominant use case.

In the classic ZeroMQ pub-sub pattern, there are two strong underlying assumptions, both of which are usually wrong in FileMQ's real world. First, that data expires very rapidly so that there's no interest in asking from old data. Second, that networks are stable and rarely break (so it's better to invest more in improving the infrastructure and less in addressing recovery).

Take any FileMQ use case and you'll see that if the client disconnects and reconnects, then it should get anything it missed. A further improvement would be to recover from partial failures, like HTTP and FTP do. But one thing at a time.

One answer to recovery is "durable subscriptions", and the first drafts of the FILEMQ protocol aimed to support this, with client identifiers that the server could hold onto and store. So if a client reappears after a failure, the server would know what files it had not received.

Stateful servers are, however, nasty to make and difficult to scale. How do we, for example, do failover to a secondary server? Where does it get its subscriptions from? It's far nicer if each client connection works independently and carries all necessary state with it.

Another nail in the coffin of durable subscriptions is that it requires up-front coordination. Up-front coordination is always a red flag, whether it's in a team of people working together, or a bunch of processes talking to each other. What about late joiners? In the real world, clients do not neatly line up and then all say "Ready!" at the same time. In the real world, they come and go arbitrarily, and it's valuable if we can treat a brand new client in the same way as a client that went away and came back.

To address this I will add two concepts to the protocol: a *resynchronization* option and a `cache` field (a dictionary). If the client wants recovery, it sets the resynchronization option, and tells the server what files it already has via the `cache` field. We need both, because there's no way in the protocol to distinguish between an empty field and a null field. The FILEMQ RFC describes these fields as follows:

> The `options` field provides additional information to the server. The server SHOULD implement these options: `RESYNC=1` - if the client sets this, the server SHALL send the full contents of the virtual path to the client, except files the client already has, as identified by their SHA-1 digest in the `cache` field.

And:

> When the client specifies the `RESYNC` option, the `cache` dictionary field tells the server which files the client already has. Each entry in the `cache` dictionary is a "filename=digest" key/value pair where the digest SHALL be a SHA-1 digest in printable hexadecimal format. If the filename starts with "/" then it SHOULD start with the path, otherwise the server MUST ignore it. If the filename does not start with "/" then the server SHALL treat it as relative to the path.

Clients that know they are in the classic pub-sub use case just don't provide any cache data, and clients that want recovery provide their cache data. It requires no state in the server, no up-front coordination, and works equally well for brand new clients (which may have received files via some out-of-band means), and clients that received some files and were then disconnected for a while.

I decided to use SHA-1 digests for several reasons. First, it's fast enough: 150msec to digest a 25MB core dump on my laptop. Second, it's reliable: the chance of getting the same hash for different versions of one file is close enough to zero. Third, it's the widest supported digest algorithm. A cyclic-redundancy check (e.g., CRC-32) is faster but not reliable. More recent SHA versions (SHA-256, SHA-512) are more secure but take 50% more CPU cycles, and are overkill for our needs.

Here is what a typical ICANHAZ message looks like when we use both caching and resyncing (this is output from the `dump` method of the generated codec class):

```
ICANHAZ:
    path='/photos'
    options={
        RESYNC=1
    }
    cache={
        DSCF0001.jpg=1FABCD4259140ACA99E991E7ADD2034AC57D341D
        DSCF0006.jpg=01267C7641C5A22F2F4B0174FFB0C94DC59866F6
        DSCF0005.jpg=698E88C05B5C280E75C055444227FEA6FB60E564
        DSCF0004.jpg=F0149101DD6FEC13238E6FD9CA2F2AC62829CBD0
        DSCF0003.jpg=4A49F25E2030B60134F109ABD0AD9642C8577441
        DSCF0002.jpg=F84E4D69D854D4BF94B5873132F9892C8B5FA94E
    }
```

Although we don't do this in FileMQ, the server can use the cache information to help the client catch up with deletions that it missed. To do this, it would have to log deletions, and then compare this log with the client cache when a client subscribes.



| [Test Use Case: The Track Tool](http://zguide.zeromq.org/page:all#Test-Use-Case-The-Track-Tool) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-227) [next](http://zguide.zeromq.org/page:all#header-229) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

To properly test something like FileMQ we need a test case that plays with live data. One of my sysadmin tasks is to manage the MP3 tracks on my music player, which is, by the way, a Sansa Clip reflashed with Rock Box, which I highly recommend. As I download tracks into my Music folder, I want to copy these to my player, and as I find tracks that annoy me, I delete them in the Music folder and want those gone from my player too.

This is kind of overkill for a powerful file distribution protocol. I could write this using a bash or Perl script, but to be honest the hardest work in FileMQ was the directory comparison code and I want to benefit from that. So I put together a simple tool called `track`, which calls the FileMQ API. From the command line this runs with two arguments; the sending and the receiving directories:

```
./track /home/ph/Music /media/3230-6364/MUSIC
```

The code is a neat example of how to use the FileMQ API to do local file distribution. Here is the full program, minus the license text (it's MIT/X11 licensed):

\#include "czmq.h"
\#include "../include/fmq.h"

int main (int argc, char *argv [])
{
`    `fmq_server_t *server = fmq_server_new ();
`    `fmq_server_configure (server, "anonymous.cfg");
`    `fmq_server_publish (server, argv [1], "/");
`    `fmq_server_set_anonymous (server, true);
`    `fmq_server_bind (server, "tcp://*:5670");

`    `fmq_client_t *client = fmq_client_new ();
`    `fmq_client_connect (client, "tcp://localhost:5670");
`    `fmq_client_set_inbox (client, argv [2]);
`    `fmq_client_set_resync (client, true);
`    `fmq_client_subscribe (client, "/");

`    `**while** (true) {
`        `*//  Get message from fmq_client API*
`        `zmsg_t *msg = fmq_client_recv (client);
`        `**if** (!msg)
`            `**break**;`              `*//  Interrupted*
`        `char *command = zmsg_popstr (msg);
`        `**if** (streq (command, "DELIVER")) {
`            `char *filename = zmsg_popstr (msg);
`            `char *fullname = zmsg_popstr (msg);
`            `printf ("I: received %s (%s)**\n**", filename, fullname);
`            `free (filename);
`            `free (fullname);
`        }`
`        `free (command);
`        `zmsg_destroy (&msg);
`    }`
`    `fmq_server_destroy (&server);
`    `fmq_client_destroy (&client);
`    `**return** 0;
}

Note how we work with physical paths in this tool. The server publishes the physical path `/home/ph/Music` and maps this to the virtual path `/`. The client subscribes to `/` and receives all files in `/media/3230-6364/MUSIC`. I could use any structure within the server directory, and it would be copied faithfully to the client's inbox. Note the API method `fmq_client_set_resync()`, which causes a server-to-client synchronization.



| [Getting an Official Port Number](http://zguide.zeromq.org/page:all#Getting-an-Official-Port-Number) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-228) [next](http://zguide.zeromq.org/page:all#header-230) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

We've been using port 5670 in the examples for FILEMQ. Unlike all the previous examples in this book, this port isn't arbitrary but [was assigned](http://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.txt) by the Internet [Assigned Numbers Authority (IANA)](http://www.iana.org/), which "is responsible for the global coordination of the DNS Root, IP addressing, and other Internet protocol resources".

I'll explain very briefly when and how to request registered port numbers for your application protocols. The main reason is to ensure that your applications can run in the wild without conflict with other protocols. Technically, if you ship any software that uses port numbers between 1024 and 49151, you should be using only IANA registered port numbers. Many products don't bother with this, however, and tend instead to use the IANA list as "ports to avoid".

If you aim to make a public protocol of any importance, such as FILEMQ, you're going to want an IANA-registered port. I'll explain briefly how to do this:

- Document your protocol clearly, as IANA will want a specification of how you intend to use the port. It does not have to be a fully-formed protocol specification, but must be solid enough to pass expert review.

- Decide what transport protocols you want: UDP, TCP, SCTP, and so on. With ZeroMQ you will usually only want TCP.

- Fill in the application on iana.org, providing all the necessary information.

- IANA will then continue the process by email until your application is accepted or rejected.

Note that you don't request a specific port number; IANA will assign you one. It's therefore wise to start this process before you ship software, not afterwards.



# Chapter 8 -分布式计算框架 A Framework for Distributed Computing

我们经历了理解ZeroMQ的许多方面的旅程。到目前为止，您可能已经开始使用我介绍的技术以及您自己发现的其他技术来构建自己的产品。您将开始面对如何使这些产品在现实世界中工作的问题。

但什么是“真实世界”呢?我想说的是，它正在成为一个由越来越多的移动部件组成的世界。有些人使用“物联网”这个词，这意味着我们将看到一个新的设备类别，它比我们目前的智能手机、平板电脑、笔记本电脑和服务器数量更多，但也更愚蠢。然而，我不认为数据是这样的。是的，有越来越多的设备，但它们一点也不傻。他们聪明、强大，而且越来越强大。

我把这种机制称为“成本引力”，它的作用是每18到24个月将技术成本降低一半。换句话说，我们的全球计算能力每两年翻一番，一次又一次。未来充满了数万亿的设备，它们都是功能强大的多核计算机:它们运行的不是精简版的“操作系统”，而是完整的操作系统和应用程序。

这就是我们以ZeroMQ为目标的世界。当我们谈到“规模”时，我们指的不是数百台计算机，甚至数千台。想象一下，每一个人的周围都环绕着微型智能机器，它们也许还能自我复制，填补每一个空间，覆盖每一堵墙，填补每一道裂缝，最终，它们成为我们的一部分，以至于我们在出生前就拥有了它们，而它们却随着我们走向死亡。

这些由微型机器组成的云，通过使用互联网协议的短程无线连接，一直在相互通信。它们创造网状网络，像神经信号一样传递信息和任务。它们增强了我们的记忆力、视觉、我们交流的方方面面以及身体机能。正是ZeroMQ为他们的对话、活动以及工作和信息的交流提供了动力。

现在，要想在今天实现这一点，我们需要解决一系列技术问题。这些问题包括:同龄人如何发现彼此?它们如何与现有的网络(如Web)通信?他们如何保护自己携带的信息?我们如何跟踪和监控他们，了解他们在做什么?然后，我们需要做大多数工程师忘记的事情:将这个解决方案打包到一个普通开发人员非常容易使用的框架中。

这就是我们在本章将要尝试的:为分布式应用程序构建一个作为API、协议和实现的框架。这不是一个小挑战，但我经常说ZeroMQ使这些问题变得简单，所以让我们看看这是否仍然正确。

我们将讨论:
- 分布式计算的需求
- 近距离网络WiFi的优点和缺点
- 使用UDP和TCP发现
- 基于消息的API
- 创建一个新的开源项目
- 对等连接(和谐模式)
- 跟踪同伴的出现和消失
- 没有中央协调的组消息传递
- 大规模测试与模拟
- 处理高水位和同行受阻
- 分布式记录和监控



## Design for The Real World

无论是通过WiFi连接一屋子的移动设备，还是通过模拟以太网连接一群虚拟的盒子，我们都会遇到同样的问题。它们是:

- 发现:我们如何了解网络上的其他节点?我们是使用发现服务、集中中介还是某种广播信标?
- 存在:我们如何跟踪其他节点何时出现和消失?我们是否使用某种中央注册服务，或心跳或信标?
- 连接性:我们如何将一个节点连接到另一个节点?我们是使用本地网络、广域网络，还是使用中央消息代理进行转发?
- 点到点消息传递:如何将消息从一个节点发送到另一个节点?我们是将其发送到节点的网络地址，还是通过集中的消息代理使用一些间接寻址?
- 组消息传递:如何将消息从一个节点发送到其他节点组?我们是通过集中的消息代理工作，还是使用像ZeroMQ这样的pub-sub模型?
- 测试和模拟:我们如何模拟大量节点，以便正确地测试性能?我们是必须购买24台Android平板电脑，还是可以使用纯软件模拟?
- 分布式日志:我们如何跟踪这个节点云，以便检测性能问题和故障?我们是创建一个主日志服务，还是允许每个设备记录它周围的世界?
- 内容分发:如何将内容从一个节点发送到另一个节点?我们是使用FTP或HTTP这样的以服务器为中心的协议，还是使用FileMQ这样的分散协议?

如果我们能够很好地解决这些问题，以及将来会出现的进一步的问题(比如安全性和广域桥接)，我们就会得到一个框架，我可以称之为“真正酷的分布式应用程序”，或者我的孙子们称之为“我们这个世界赖以运行的软件”。

你应该已经从我的反问中猜到，我们可以朝两大方向前进。一是把一切集中起来。另一种是把所有东西都分配。我赌权力下放。如果你想要集中化，你并不真的需要ZeroMQ;您还可以使用其他选项。
大致是这样的。第一，移动部件的数量随着时间呈指数级增长(每24个月翻一番)。第二，这些部件不再使用电线，因为到处拖着电缆会很无聊。第三，未来的应用程序将使用Chapter 6 - The ZeroMQ Community中的“仁慈的暴君”模式（the Benevolent Tyrant pattern）在这些块的集群中运行。第四，今天要构建这样的应用程序是非常困难的，甚至是不可能的。第五，让我们用我们已经建立的所有技术和工具使它变得便宜和容易。第六、partay !



## The Secret Life of WiFi

未来很明显是无线的，尽管许多大企业依靠将数据集中到云计算中来生存，但未来看起来并不那么集中。我们网络边缘的设备每年都在变得更智能，而不是更笨。他们渴望工作和信息来消化并从中获利。而且他们也不拖着电缆到处走，除非是为了供电。它完全是无线的，而且越来越多，它是802.11品牌的WiFi，按字母顺序排列。



### Why Mesh Isn't Here Yet
作为我们未来如此重要的一部分，WiFi存在着一个不常被讨论的大问题，但任何押注于它的人都需要意识到这一点。世界上的电话公司几乎在每个政府正常运作的国家都建立了盈利丰厚的手机卡特尔，其基础是让政府相信，如果没有电波和想法的垄断权，世界将分崩离析。从技术上讲，我们称之为“监管俘获”和“专利”，但事实上，这只是敲诈和腐败的一种形式。如果你，国家，给我，一个企业，向市场征税的权利，并禁止所有真正的竞争对手，我将给你5%。不够吗?10%怎么样?好的，15%加上零食。如果您拒绝，我们将停止服务。

但WiFi却偷偷溜过了这一关，借用了未经许可的空域，借助开放的、未申请专利的、非常创新的互联网协议栈。所以今天，我们面临着一种奇怪的情况，如果我使用我们资助了几十年的国家支持的基础设施，从首尔到布鲁塞尔每分钟要花几欧元，但如果我能找到一个不受监管的WiFi接入点，就什么都不用花。哦，我还可以做视频，发送文件和照片，下载整个家庭电影，所有这些都以同样惊人的价格，正好是0。如果我想用我实际付费的服务把一张照片寄回家，上帝会帮助我的。这比我拿着的相机还贵。

这是我们长期忍受“相信我们，我们是专家”专利制度所付出的代价。但更重要的是，这对科技行业的大部分企业——尤其是拥有反互联网GSM、GPRS、3G和LTE技术专利的芯片组制造商，以及将电信运营商视为主要客户的制造商——来说，是一个巨大的经济刺激，促使它们积极抑制WiFi的发展。当然，正是这些公司扩充了IEEE委员会对WiFi的定义。
这种对律师驱动的“创新”的怒斥，是为了让你思考“如果WiFi真的是免费的呢?”这种情况总有一天会发生，就在不远的将来，值得一赌。我们会看到一些事情发生。首先，更积极地使用空域，特别是在没有干扰风险的近距离通信领域。其次，随着我们学会并行使用更多空域，容量将大幅提高。三是加快标准化进程。最后，在设备上为真正有趣的连接提供更广泛的支持。

现在，把电影从手机上传到电视上被认为是“前沿”。这是荒谬的。让我们变得真正有野心。在体育场里，人们观看比赛，实时分享照片和高清视频，创造出一种特别的活动，让整个空间充斥着数字狂热。我应该能够在一个小时内从我周围的人那里收集到tb级的图像。为什么要通过Twitter或Facebook和那个小小的昂贵的移动数据连接呢?如果一个家里有几百台设备通过网络互相通话，那么当有人按门铃时，门廊里的灯光就会把视频传输到你的手机或电视上，你会怎么想?如果有一辆车可以和你的手机通话，播放你的dubstep播放列表，而不需要你接上电线，你会觉得怎么样?
更严重的问题是，为什么我们的数字社会掌握在被监控、审查、记录、用来跟踪我们与谁交谈、收集不利于我们的证据，然后当当局认为我们拥有太多言论自由时就会关闭?我们正在经历的隐私的丧失只是一个问题，当它是片面的，但随后的问题是灾难性的。一个真正的无线世界将绕过所有的中央审查。这就是互联网是如何设计的，从技术上讲，它是相当可行的(这是最好的一种可行方式)。


### Some Physics

天真的分布式软件开发人员认为网络无限快，而且非常可靠。虽然这对于以太网上的简单应用程序来说几乎是正确的，但是WiFi很快证明了神奇思维和科学之间的区别。也就是说，WiFi很容易在压力下崩溃，有时我想知道怎么有人敢在真正的工作中使用它。WiFi越好，天花板就越高，但速度永远无法阻止我们触碰它。
要理解WiFi在技术上的表现，你需要理解一个基本的物理定律:连接两个点所需的功率会随着距离的平方而增加。就像我在达拉斯学到的那样，在大房子里长大的人的声音会成倍地提高。对于WiFi网络，这意味着当两台收音机相距越远，它们要么使用更多的能量，要么降低信号速率。在用户认为这款设备坏得无可救药之前，你只能从电池中抽出这么多电量。因此，即使WiFi网络的速率是一定的，但是接入点(AP)和客户机之间的实际比特率取决于两者之间的距离。当你把你的无线电话从美联社移开时，两台试图互相通话的收音机将首先增加它们的功率，然后降低它们的比特率。
如果我们想要构建健壮的分布式应用程序，而不是像木偶一样在它们后面挂线，那么这种效果的一些后果是我们应该注意的:

- 如果有一组设备与AP通信，当AP与最慢的设备通信时，整个网络必须等待。这就像在派对上不得不对指定的司机重复一个笑话，而这个司机没有幽默感，仍然完全清醒，而且可悲的是，他对语言的掌握很差。
- 如果您使用单播TCP并向多个设备发送消息，AP必须将数据包分别发送到每个设备，是的，您知道这一点，这也是以太网的工作原理。但现在要明白，一个遥远的(或低功率的)设备意味着一切都在等待最慢的设备赶上来。
- 如果您使用多播或广播(它们在大多数情况下工作相同)，AP将一次性向整个网络发送单个数据包，这非常棒，但是它将以尽可能慢的比特率(通常为1Mbps)完成这项工作。您可以在某些APs中手动调整此速率。这只会降低AP的覆盖范围。您还可以购买更昂贵的AP，这些AP具有更多的智能，并且能够计算出它们可以安全使用的最高比特率。您还可以使用支持IGMP (Internet Group Management Protocol, Internet Group Management Protocol)和ZeroMQ的PGM传输的企业APs只发送到订阅的客户机。然而，我不认为这样的APs会得到广泛应用。

当您试图将更多的设备添加到AP时，性能会迅速下降，以至于增加一个设备就会破坏所有人的整个网络。许多AP解决这个问题的方法是在客户端达到某个限制时随机断开连接，比如移动热点的4到8台设备、消费AP的30-50台设备、企业AP的100台设备。


## What's the Current Status?

尽管WiFi作为一种企业技术在某种程度上逃到了野外，让人感到不舒服，但它已经比免费的Skype通话更有用了。它并不理想，但它的工作很好，足以让我们解决一些有趣的问题。让我给你一个快速的状态报告。

首先，点对点与访问点对客户机。传统的WiFi都是ap客户端。每个数据包必须从客户端A到AP，然后再到客户端b。你减少了50%的带宽，但这只是问题的一半。我讲过逆幂律。如果A和B非常接近，但是都远离AP，那么它们都将使用较低的比特率。假设您的AP在车库中，而您正在客厅中尝试将视频从您的手机传输到电视。好运！

有一种老式的“ad-hoc”模式，允许A和B互相通信，但是速度太慢，没有任何乐趣可言，当然，它在所有移动芯片组上都是禁用的。实际上，它在芯片组制造商善意提供给硬件制造商的绝密驱动程序中是禁用的。有一个新的隧道直接链接设置(TDLS)协议，它允许两个设备创建一个直接链接，使用AP进行发现，但不用于流量。此外，还有一个“5G”WiFi标准(这是一个营销术语，所以要加引号)，可以将链接速度提升到千兆比特。TDLS和5G结合在一起，使从手机到电视的高清电影流媒体成为可能。我认为TDLS将受到各种方式的限制，以安抚电信运营商。

最后，我们在2012年看到了802.11s网格协议的标准化，经过了10年左右的快速工作。Mesh完全删除了访问点，至少在它存在并被广泛使用的假想未来是这样的。设备之间直接通信，并维护邻居的路由表，让它们转发数据包。想象一下，AP软件嵌入到每个设备中，但是足够聪明(它没有听起来那么令人印象深刻)，可以执行多个跳转。

从移动数据敲诈勒索中赚钱的人都不希望看到802.11可用，因为城市范围内的网格对于底线来说是一场噩梦，所以它发生得越慢越好。唯一有能力(而且，我认为地对地导弹)使网格技术得到广泛应用的大型组织是美国陆军。但mesh将会出现，我打赌到2020年左右，802.11将在消费电子产品中得到广泛应用。

第二，如果我们没有点对点，我们今天还能信任APs到什么程度?好吧，如果你去美国的星巴克(Starbucks)，用两台通过免费WiFi连接的笔记本电脑尝试ZeroMQ的“Hello World”，你会发现它们无法连接。为什么?答案就在名字里:“attwifi”。AT&T是一个很好的老牌电信公司，它讨厌WiFi，而且很可能为星巴克和其他公司提供廉价的服务，这样独立运营商就无法进入市场。但你购买的任何接入点都将支持客户端到应用程序到客户端访问，而且在美国以外，我从来没有发现过像AT&T那样被锁定的公共AP。

第三,性能。美联社显然是一个瓶颈;即使你把A和B按字面意思放在AP旁边，你也无法获得超过其广告速度一半的速度。更糟糕的是，如果在同一空域有其他AP，它们会互相大喊大叫。在我家，WiFi几乎无法工作，因为楼下两幢房子的邻居都有一个扩音器。即使在不同的频道，它也会干扰我们家里的WiFi。在我现在坐的咖啡馆里，有十几个广播网。实际上，只要我们依赖于基于ap的WiFi，我们就会受到随机干扰和不可预测的性能。

第四,电池寿命。例如，WiFi在空闲时比蓝牙更饥渴，这并没有内在的原因。他们使用相同的无线电和低电平帧。主要的区别在于调优和协议。为了让无线节电工作得更好，设备大多需要睡眠，并且每隔一段时间才会向其他设备发送一次信号。为了实现这一点，他们需要同步他们的时钟。这对于手机来说是很合适的，这就是为什么我的旧翻盖手机一次充电可以运行五天。当WiFi工作时，它会消耗更多的能量。目前的功放技术效率也很低，这意味着你从电池中吸收的能量要比你往空气中泵的能量多得多(这些废物会变成一个热手机)。随着人们越来越关注移动WiFi，功率放大器也在不断改进。

最后，移动接入点。如果我们不能信任集中的APs，如果我们的设备足够聪明，能够运行完整的操作系统，我们就不能让它们作为APs工作吗?我很高兴你问了这个问题。是的，我们可以，而且效果很好。特别是因为你可以在软件中打开或关闭它，在像Android这样的现代操作系统上。同样，美国电信运营商是破坏和平的罪魁祸首，他们大多讨厌这个功能，并在他们控制的手机上杀死或破坏它。更聪明的电信运营商意识到，这是一种扩大其“最后一英里”的方式，可以为更多用户带来更高价值的产品，但骗子不会在智能上竞争。



| [Conclusions](http://zguide.zeromq.org/page:all#Conclusions) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-235) [next](http://zguide.zeromq.org/page:all#header-237) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

WiFi不是以太网，尽管我相信未来的ZeroMQ应用程序将会有一个非常重要的分散式无线存在，但这并不是一条容易的路。您期望从以太网获得的大部分基本可靠性和容量都丢失了。当您在WiFi上运行分布式应用程序时，您必须考虑频繁的超时、随机延迟、任意断开连接、整个接口上下浮动等等。

无线网络技术的发展被形容为“缓慢而无趣”。试图利用分散式无线的应用程序和框架大多不存在，或者很差。目前唯一的近距离网络开源框架是高通的AllJoyn。但有了ZeroMQ，我们证明了现有球员的惰性和老朽的无能并不是我们坐视不管的理由。当我们准确地理解问题时，我们就能解决它们。我们想象的，我们就能实现。



## Discovery

发现是网络编程的重要组成部分，也是ZeroMQ开发人员的一流问题。每个zmq_connect（）调用都提供一个端点字符串，而且必须来自某个地方。到目前为止我们看到的示例没有发现：它们连接的端点在代码中被硬编码为字符串。虽然这对于示例代码来说很好，但它对于实际应用程序来说并不理想。网络表现不佳。事情发生了变化，而这正是我们处理变革的方式，它定义了我们的长期成功。



### Service Discovery

Let's start with definitions. Network discovery is finding out what other peers are on the network. Service discovery is learning what those peers can do for us. Wikipedia defines a "network service" as "a service that is hosted on a computer network", and "service" as "a set of related software functionalities that can be reused for different purposes, together with the policies that should control its usage". It's not very helpful. Is Facebook a network service?

In fact the concept of "network service" has changed over time. The number of moving pieces keeps doubling every 18-24 months, breaking old conceptual models and pushing for ever simpler, more scalable ones. A service is, for me, a system-level application that other programs can talk to. A network service is one accessible remotely (as compared to, e.g., the "grep" command, which is a command-line service).

In the classic BSD socket model, a service maps 1-to-1 to a network port. A computer system offers a number of services like "FTP", and "HTTP", each with assigned ports. The BSD API has functions like `getservbyname` to map a service name to a port number. So a classic service maps to a network endpoint: if you know a server's IP address and then you can find its FTP service, if that is running.

In modern messaging, however, services don't map 1-to-1 to endpoints. One endpoint can lead to many services, and services can move around over time, between ports, or even between systems. Where is my cloud storage today? In a realistic large distributed application, therefore, we need some kind of service discovery mechanism.

There are many ways to do this and I won't try to provide an exhaustive list. However there are a few classic patterns:

- We can force the old 1-to-1 mapping from endpoint to service, and simply state up-front that a certain TCP port number represents a certain service. Our protocol then should let us check this ("Are the first 4 bytes of the request 'HTTP'?").

- We can bootstrap one service off another; connecting to a well-known endpoint and service, asking for the "real" service, and getting an endpoint back in return. This gives us a service lookup service. If the lookup service allows it, services can then move around as long as they update their location.

- We can proxy one service through another, so that a well-known endpoint and service will provide other services indirectly (i.e. by forwarding messages to them). This is for instance how our Majordomo service-oriented broker works.

- We can exchange lists of known services and endpoints, that change over time, using a gossip approach or a centralized approach (like the Clone pattern), so that each node in a distributed network can build-up an eventually consistent map of the whole network.

- We can create further abstract layers in between network endpoints and services, e.g. assigning each node a unique identifier, so we get a "network of nodes" where each node may offer some services, and may appear on random network endpoints.

- We can discover services opportunistically, e.g. by connecting to endpoints and then asking them what services they offer. "Hi, do you offer a shared printer? If so, what's the maker and model?"

There's no "right answer". The range of options is huge, and changes over time as the scale of our networks grows. In some networks the knowledge of what services run where can literally become political power. ZeroMQ imposes no specific model but makes it easy to design and build the ones that suit us best. However, to build service discovery, we must start by solving network discovery.



| [Network Discovery](http://zguide.zeromq.org/page:all#Network-Discovery) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-238) [next](http://zguide.zeromq.org/page:all#header-240) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Here is a list of the solutions I know for network discovery:

- *Use hard-coded endpoint strings*, i.e., fixed IP addresses and agreed ports. This worked in internal networks a decade ago when there were a few "big servers" and they were so important they got static IP addresses. These days however it's no use except in examples or for in-process work (threads are the new Big Iron). You can make it hurt a little less by using DNS but this is still painful for anyone who's not also doing system administration as a side-job.

- *Get endpoint strings from configuration files*. This shoves name resolution into user space, which hurts less than DNS but that's like saying a punch in the face hurts less than a kick in the groin. You now get a non-trivial management problem. Who updates the configuration files, and when? Where do they live? Do we install a distributed management tool like Salt Stack?

- *Use a message broker*. You still need a hard-coded or configured endpoint string to connect to the broker, but this approach reduces the number of different endpoints in the network to one. That makes a real impact, and broker-based networks do scale nicely. However, brokers are single points of failure, and they bring their own set of worries about management and performance.

- *Use an addressing broker*. In other words use a central service to mediate address information (like a dynamic DNS setup) but allow nodes to send each other messages directly. It's a good model but still creates a point of failure and management costs.

- *Use helper libraries, like ZeroConf*, that provide DNS services without any centralized infrastructure. It's a good answer for certain applications but your mileage will vary. Helper libraries aren't zero cost: they make it more complex to build the software, they have their own restrictions, and they aren't necessarily portable.

- *Build system-level discovery* by sending out ARP or ICMP ECHO packets and then querying every node that responds. You can query through a TCP connection, for example, or by sending UDP messages. Some products do this, like the Eye-Fi wireless card.

- *Do user-level brute-force discovery* by trying to connect to every single address in the network segment. You can do this trivially in ZeroMQ since it handles connections in the background. You don't even need multiple threads. It's brutal but fun, and works very well in demos and workshops. However it doesn't scale, and annoys decent-thinking engineers.

- *Roll your own UDP-based discovery protocol*. Lots of people do this (I counted about 80 questions on this topic on StackOverflow). UDP works well for this and it's technically clear. But it's technically tricky to get right, to the point where any developer doing this the first few times will get it dramatically wrong.

- *Gossip discovery protocols*. A fully-interconnected network is quite effective for smaller numbers of nodes (say, up to 100 or 200). For large numbers of nodes, we need some kind of gossip protocol. That is, where the nodes we can reasonable discover (say, on the same segment as us), tell us about nodes that are further away. Gossip protocols go beyond what we need these days with ZeroMQ, but will likely be more common in the future. One example of a wide-area gossip model is mesh networking.



| [The Use Case](http://zguide.zeromq.org/page:all#The-Use-Case) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-239) [next](http://zguide.zeromq.org/page:all#header-241) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Let's define our use case more explicitly. After all, all these different approaches have worked and still work to some extent. What interests me as architect is the future, and finding designs that can continue to work for more than a few years. This means identifying long term trends. Our use case isn't here and now, it's ten or twenty years from today.

Here are the long term trends I see in distributed applications:

- *The overall number of moving pieces keeps increasing*. My estimate is that it doubles every 24 months, but how fast it increases matters less than the fact that we keep adding more and more nodes to our networks. They're not just boxes but also processes and threads. The driver here is cost, [which keeps falling](http://cultureandempire.com/). In a decade, the average teenager will carry 30-50 devices, all the time.

- *Control shifts away from the center*. Possibly data too, though we're still far from understanding how to build simple decentralized information stores. In any case, the star topology is slowly dying and being replaced by clouds of clouds. In the future there's going to be much more traffic within a local environment (home, office, school, bar) than between remote nodes and the center. The maths here are simple: remote communications cost more, run more slowly and are less natural than close-range communications. It's more accurate both technically and socially to share a holiday video with your friend over local WiFi than via Facebook.

- *Networks are increasingly collaborative, less controlled*. This means people bringing their own devices and expecting them to work seamlessly. The Web showed one way to make this work but we're reaching the limits of what the Web can do, as we start to exceed the average of one device per person.

- *The cost of connecting a new node to a network must fall proportionally*, if the network is to scale. This means reducing the amount of configuration a node needs: less pre-shared state, less context. Again, the Web solved this problem but at the cost of centralization. We want the same plug and play experience but without a central agency.

In a world of trillions of nodes, the ones you talk to most are the ones closest to you. This is how it works in the real world and it's the sanest way of scaling large-scale architectures. Groups of nodes, logically or physically close, connected by bridges to other groups of nodes. A local group will be anything from half-a-dozen nodes to a few thousand nodes.

So we have two basic use cases:

- **Discovery for proximity networks**, that is, a set of nodes that find themselves close to each other. We can define "close to each other" as being "on the same network segment". It's not going to be true in all cases but it's true enough to be a useful place to start.

- **Discovery across wide area networks**, that is, bridging of proximity networks together. We sometimes call this "federation". There are many ways to do federation but it's complex and something to cover elsewhere. For now, let's assume we do federation using a centralized broker or service.

So we are left with the problem of proximity networking. I want to just plug things into the network and have them talking to each other. Whether they're tablets in a school or a bunch of servers in a cloud, the less upfront agreement and coordination, the cheaper it is to scale. So configuration files and brokers and any kind of centralized service are all out.

I also want to allow any number of applications on a box, both because that's how the real world works (people download apps), and so that I can simulate large networks on my laptop. Upfront simulation is the only way I know to be sure a system will work when it's loaded in real-life. You'd be surprised how engineers just hope things will work. "Oh, I'm sure that bridge will stay up when we open it to traffic". If you haven't simulated and fixed the three most likely failures, they'll still be there on opening day.

Running multiple instances of a service on the same machine - without upfront coordination - means we have to use ephemeral ports, i.e., ports assigned randomly for services. Ephemeral ports rule out brute-force TCP discovery and any DNS solution including ZeroConf.

Finally, discovery has to happen in user space because the apps we're building will be running on random boxes that we do not necessarily own and control. For example, other people's mobile devices. So any discovery that needs root permissions is excluded. This rules out ARP and ICMP and once again ZeroConf since that also needs root permissions for the service parts.



| [Technical Requirements](http://zguide.zeromq.org/page:all#Technical-Requirements) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-240) [next](http://zguide.zeromq.org/page:all#header-242) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Let's recap the requirements:

- *The simplest possible solution that works*. There are so many edge cases in ad-hoc networks that every extra feature or functionality becomes a risk.

- *Supports ephemeral ports*, so that we can run realistic simulations. If the only way to test is to use real devices, it becomes impossibly expensive and slow to run tests.

- *No root access needed*, it must run 100% in user space. We want to ship fully-packaged applications onto devices like mobile phones that we don't own and where root access isn't available.

- *Invisible to system administrators*, so we do not need their help to run our applications. Whatever technique we use should be friendly to the network and available by default.

- *Zero configuration* apart from installing the applications themselves. Asking the users to do any configuration is giving them an excuse to not use the applications.

- *Fully portable* to all modern operating systems. We can't assume we'll be running on any specific OS. We can't assume any support from the operating system except standard user-space networking. We can assume ZeroMQ and CZMQ are available.

- *Friendly to WiFi networks* with up to 100-150 participants. This means keeping messages small and being aware of how WiFi networks scale and how they break under pressure.

- *Protocol-neutral*, i.e., our beaconing should not impose any specific discovery protocol. I'll explain what this means a little later.

- *Easy to re-implement in any given language*. Sure, we have a nice C implementation, but if it takes too long to re-implement in another language, that excludes large chunks of the ZeroMQ community. So, again, simple.

- *Fast response time*. By this, I mean a new node should be visible to its peers in a very short time, a second or two at most. Networks change shape rapidly. It's OK to take longer, even 30 seconds, to realize a peer has disappeared.

From the list of possible solutions I collected, the only option that isn't disqualified for one or more reasons is to build our own UDP-based discovery stack. It's a little disappointing that after so many decades of research into network discovery, this is where we end up. But the history of computing does seem to go from complex to simple, so maybe it's normal.



| [A Self-Healing P2P Network in 30 Seconds](http://zguide.zeromq.org/page:all#A-Self-Healing-P-P-Network-in-Seconds) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-241) [next](http://zguide.zeromq.org/page:all#header-243) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

I mentioned brute-force discovery. Let's see how that works. One nice thing about software is to brute-force your way through the learning experience. As long as we're happy to throw away work, we can learn rapidly simply by trying things that may seem insane from the safety of the armchair.

I'll explain a brute-force discovery approach for ZeroMQ that emerged from a workshop in 2012. It is remarkably simple and stupid: connect to every IP address in the room. If your network segment is 192.168.55.x, for instance, you do this:

```
connect to tcp://192.168.55.1:9000
connect to tcp://192.168.55.2:9000
connect to tcp://192.168.55.3:9000
...
connect to tcp://192.168.55.254:9000
```

Which in ZeroMQ-speak looks like this:

int address;
**for** (address = 1; address < 255; address++)
`    `zsocket_connect (listener, "tcp://192.168.55.%d:9000", address);

The stupid part is where we assume that connecting to ourselves is fine, where we assume that all peers are on the same network segment, where we waste file handles as if they were free. Luckily these assumptions are often totally accurate. At least, often enough to let us do fun things.

The loop works because ZeroMQ connect calls are *asynchronous and opportunistic*. They lie in the shadows like hungry cats, waiting patiently to pounce on any innocent mouse that dared start up a service on port 9000. It's simple, effective, and worked first time.

It gets better: as peers leave and join the network, they'll automatically reconnect. We've designed a self-healing peer to peer network, in 30 seconds and three lines of code.

It won't work for real cases though. Poorer operating systems tend to run out of file handles, and networks tend to be more complex than one segment. And if one node squats a couple of hundred file handles, large-scale simulations (with many nodes on one box or in one process) are out of the question.

Still, let's see how far we can go with this approach before we throw it out. Here's a tiny decentralized chat program that lets you talk to anyone else on the same network segment. The code has two threads: a listener and a broadcaster. The listener creates a SUB socket and does the brute-force connection to all peers in the network. The broadcaster accepts input from the console and sends it on a PUB socket:

[dechat: Decentralized Chat in C](javascript:;)


[Java](http://zguide.zeromq.org/java:dechat) | [Python](http://zguide.zeromq.org/py:dechat) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

```
dechat 192.168.55.122 eth0 Joe
```



| [Preemptive Discovery over Raw Sockets](http://zguide.zeromq.org/page:all#Preemptive-Discovery-over-Raw-Sockets) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-242) [next](http://zguide.zeromq.org/page:all#header-244) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

One of the great things about short-range wireless is the proximity. WiFi maps closely to the physical space, which maps closely to how we naturally organize. In fact, the Internet is quite abstract and this confuses a lot of people who kind of "get it" but in fact don't really. With WiFi, we have technical connectivity that is potentially super-tangible. You see what you get and you get what you see. Tangible means easy to understand and that should mean love from users instead of the typical frustration and seething hatred.

Proximity is the key. We have a bunch of WiFi radios in a room, happily beaconing to each other. For lots of applications, it makes sense that they can find each other and start chatting without any user input. After all, most real world data isn't private, it's just highly localized.

I'm in a hotel room in Gangnam, Seoul, with a 4G wireless hotspot, a Linux laptop, and an couple of Android phones. The phones and laptop are talking to the hotspot. The `ifconfig`command says my IP address is 192.168.1.2. Let me try some `ping` commands. DHCP servers tend to dish out addresses in sequence, so my phones are probably close by, numerically speaking:

```
$ ping 192.168.1.1
PING 192.168.1.1 (192.168.1.1) 56(84) bytes of data.
64 bytes from 192.168.1.1: icmp_req=1 ttl=64 time=376 ms
64 bytes from 192.168.1.1: icmp_req=2 ttl=64 time=358 ms
64 bytes from 192.168.1.1: icmp_req=4 ttl=64 time=167 ms
^C
--- 192.168.1.1 ping statistics ---
3 packets transmitted, 2 received, 33% packet loss, time 2001ms
rtt min/avg/max/mdev = 358.077/367.522/376.967/9.445 ms
```

Found one! 150-300 msec round-trip latency… that's a surprisingly high figure, something to keep in mind for later. Now I ping myself, just to try to double-check things:

```
$ ping 192.168.1.2
PING 192.168.1.2 (192.168.1.2) 56(84) bytes of data.
64 bytes from 192.168.1.2: icmp_req=1 ttl=64 time=0.054 ms
64 bytes from 192.168.1.2: icmp_req=2 ttl=64 time=0.055 ms
64 bytes from 192.168.1.2: icmp_req=3 ttl=64 time=0.061 ms
^C
--- 192.168.1.2 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1998ms
rtt min/avg/max/mdev = 0.054/0.056/0.061/0.009 ms
```

The response time is a bit faster now, which is what we'd expect. Let's try the next couple of addresses:

```
$ ping 192.168.1.3
PING 192.168.1.3 (192.168.1.3) 56(84) bytes of data.
64 bytes from 192.168.1.3: icmp_req=1 ttl=64 time=291 ms
64 bytes from 192.168.1.3: icmp_req=2 ttl=64 time=271 ms
64 bytes from 192.168.1.3: icmp_req=3 ttl=64 time=132 ms
^C
--- 192.168.1.3 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2001ms
rtt min/avg/max/mdev = 132.781/231.914/291.851/70.609 ms
```

That's the second phone, with the same kind of latency as the first one. Let's continue and see if there are any other devices connected to the hotspot:

```
$ ping 192.168.1.4
PING 192.168.1.4 (192.168.1.4) 56(84) bytes of data.
^C
--- 192.168.1.4 ping statistics ---
3 packets transmitted, 0 received, 100% packet loss, time 2016ms
```

And that is it. Now, `ping` uses raw IP sockets to send `ICMP_ECHO` messages. The useful thing about `ICMP_ECHO` is that it gets a response from any IP stack that has not deliberately had echo switched off. That's still a common practice on corporate websites who fear the old "ping of death" exploit where malformed messages could crash the machine.

I call this *preemptive discovery* because it doesn't take any cooperation from the device. We don't rely on any cooperation from the phones to see them sitting there; as long as they're not actively ignoring us, we can see them.

You might ask why this is useful. We don't know that the peers responding to `ICMP_ECHO` run ZeroMQ, that they are interested in talking to us, that they have any services we can use, or even what kind of device they are. However, knowing that there's *something* on address 192.168.1.3 is already useful. We also know how far away the device is, relatively, we know how many devices are on the network, and we know the rough state of the network (as in, good, poor, or terrible).

It isn't even hard to create `ICMP_ECHO` messages and send them. A few dozen lines of code, and we could use ZeroMQ multithreading to do this in parallel for addresses stretching out above and below our own IP address. Could be kind of fun.

However, sadly, there's a fatal flaw in my idea of using `ICMP_ECHO` to discover devices. To open a raw IP socket requires root privileges on a POSIX box. It stops rogue programs getting data meant for others. We can get the power to open raw sockets on Linux by giving sudo privileges to our command (ping has the so-called *sticky bit* set). On a mobile OS like Android, it requires root access, i.e., rooting the phone or tablet. That's out of the question for most people and so `ICMP_ECHO` is out of reach for most devices.

*Expletive deleted!* Let's try something in user space. The next step most people take is UDP multicast or broadcast. Let's follow that trail.



| [Cooperative Discovery Using UDP Broadcasts](http://zguide.zeromq.org/page:all#Cooperative-Discovery-Using-UDP-Broadcasts) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-243) [next](http://zguide.zeromq.org/page:all#header-245) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Multicast tends to be seen as more modern and "better" than broadcast. In IPv6, broadcast doesn't work at all: you must always use multicast. Nonetheless, all IPv4 local network discovery protocols end up using UDP broadcast anyhow. The reasons: broadcast and multicast end up working much the same, except broadcast is simpler and less risky. Multicast is seen by network admins as kind of dangerous, as it can leak over network segments.

If you've never used UDP, you'll discover it's quite a nice protocol. In some ways, it reminds us of ZeroMQ, sending whole messages to peers using a two different patterns: one-to-one, and one-to-many. The main problems with UDP are that (a) the POSIX socket API was designed for universal flexibility, not simplicity, (b) UDP messages are limited for practical purposes to about 1,500 bytes on LANs and 512 bytes on the Internet, and (c) when you start to use UDP for real data, you find that messages get dropped, especially as infrastructure tends to favor TCP over UDP.

Here is a minimal ping program that uses UDP instead of `ICMP_ECHO`:

[udpping1: UDP discovery, model 1 in C](javascript:;)


[C++](http://zguide.zeromq.org/cpp:udpping1) | [Java](http://zguide.zeromq.org/java:udpping1) | [Python](http://zguide.zeromq.org/py:udpping1) | [Ada | Basic | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

```
Pinging peers...
Found peer 192.168.1.2:9999
Pinging peers...
Found peer 192.168.1.2:9999
```

If I switch off all networking and try again, sending a message fails, as I'd expect:

```
Pinging peers...
sendto: Network is unreachable
```

Working on the basis of *solve the problems currently aiming at your throat*, let's fix the most urgent issues in this first model. These issues are:

- Using the 255.255.255.255 broadcast address is a bit dubious. On the one hand, this broadcast address means precisely "send to all nodes on the local network, and don't forward". However, if you have several interfaces (wired Ethernet, WiFi) then broadcasts will go out on your default route only, and via just one interface. What we want to do is either send our broadcast on each interface's broadcast address, or find the WiFi interface and its broadcast address.

- Like many aspects of socket programming, getting information on network interfaces is not portable. Do we want to write nonportable code in our applications? No, this is better hidden in a library.

- There's no handling for errors except "abort", which is too brutal for transient problems like "your WiFi is switched off". The code should distinguish between soft errors (ignore and retry) and hard errors (assert).

- The code needs to know its own IP address and ignore beacons that it sent out. Like finding the broadcast address, this requires inspecting the available interfaces.

The simplest answer to these issues is to push the UDP code into a separate library that provides a clean API, like this:

*//  Constructor*
**static** udp_t *
`    `udp_new (int port_nbr);

*//  Destructor*
**static** void
`    `udp_destroy (udp_t **self_p);

*//  Returns UDP socket handle*
**static** int
`    `udp_handle (udp_t *self);

*//  Send message using UDP broadcast*
**static** void
`    `udp_send (udp_t *self, byte *buffer, size_t length);

*//  Receive message from UDP broadcast*
**static** ssize_t
`    `udp_recv (udp_t *self, byte *buffer, size_t length);

Here is the refactored UDP ping program that calls this library, which is much cleaner and nicer:

[udpping2: UDP discovery, model 2 in C](javascript:;)


[Java](http://zguide.zeromq.org/java:udpping2) | [Python](http://zguide.zeromq.org/py:udpping2) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

Now, there are more problems sizing us up and wondering if they can make lunch out of us. First, IPv4 versus IPv6 and multicast versus broadcast. In IPv6, broadcast doesn't exist at all; one uses multicast. From my experience with WiFi, IPv4 multicast and broadcast work identically except that multicast breaks in some situations where broadcast works fine. Some access points do not forward multicast packets. When you have a device (e.g., a tablet) that acts as a mobile AP, then it's possible it won't get multicast packets. Meaning, it won't see other peers on the network.

The simplest plausible solution is simply to ignore IPv6 for now, and use broadcast. A perhaps smarter solution would be to use multicast and deal with asymmetric beacons if they happen.

We'll stick with stupid and simple for now. There's always time to make it more complex.



| [Multiple Nodes on One Device](http://zguide.zeromq.org/page:all#Multiple-Nodes-on-One-Device) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-244) [next](http://zguide.zeromq.org/page:all#header-246) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

So we can discover nodes on the WiFi network, as long as they're sending out beacons as we expect. So I try to test with two processes. But when I run udpping2 twice, the second instance complains "'Address already in use' on bind" and exits. Oh, right. UDP and TCP both return an error if you try to bind two different sockets to the same port. This is right. The semantics of two readers on one socket would be weird to say the least. Odd/even bytes? You get all the 1s, I get all the 0's?

However, a quick check of stackoverflow.com and some memory of a socket option called `SO_REUSEADDR` turns up gold. If I use that, I can bind several processes to the same UDP port, and they will all receive any message arriving on that port. It's almost as if the guys who designed this were reading my mind! (That's way more plausible than the chance that I may be reinventing the wheel.)

A quick test shows that `SO_REUSEADDR` works as promised. This is great because the next thing I want to do is design an API and then start dozens of nodes to see them discovering each other. It would be really cumbersome to have to test each node on a separate device. And when we get to testing how real traffic behaves on a large, flaky network, the two alternatives are simulation or temporary insanity.

And I speak from experience: we were, this summer, testing on dozens of devices at once. It takes about an hour to set up a full test run, and you need a space shielded from WiFi interference if you want any kind of reproducibility (unless your test case is "prove that interference kills WiFi networks faster than Orval can kill a thirst".

If I were a whiz Android developer with a free weekend, I'd immediately (as in, it would take me two days) port this code to my phone and get it sending beacons to my PC. But sometimes lazy is more profitable. I *like* my Linux laptop. I like being able to start a dozen threads from one process, and have each thread acting like an independent node. I like not having to work in a real Faraday cage when I can simulate one on my laptop.



| [Designing the API](http://zguide.zeromq.org/page:all#Designing-the-API) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-245) [next](http://zguide.zeromq.org/page:all#header-247) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

I'm going to run N nodes on a device, and they are going to have to discover each other, as well as a bunch of other nodes out there on the local network. I can use UDP for local discovery as well as remote discovery. It's arguably not as efficient as using, e.g., the ZeroMQ inproc:// transport, but it has the great advantage that the exact same code will work in simulation and in real deployment.

If I have multiple nodes on one device, we clearly can't use the IP address and port number as node address. I need some logical node identifier. Arguably, the node identifier only has to be unique within the context of the device. My mind fills with complex stuff I could make, like supernodes that sit on real UDP ports and forward messages to internal nodes. I hit my head on the table until the idea of *inventing new concepts* leaves it.

Experience tells us that WiFi does things like disappear and reappear while applications are running. Users click on things, which does interesting things like change the IP address halfway through a session. We cannot depend on IP addresses, nor on established connections (in the TCP fashion). We need some long-lasting addressing mechanism that survives interfaces and connections being torn down and then recreated.

Here's the simplest solution I can see: we give every node a UUID, and specify that nodes, represented by their UUIDs, can appear or reappear at certain IP address:port endpoints, and then disappear again. We'll deal with recovery from lost messages later. A UUID is 16 bytes. So if I have 100 nodes on a WiFi network, that's (double it for other random stuff) 3,200 bytes a second of beacon data that the air has to carry just for discovery and presence. Seems acceptable.

Back to concepts. We do need some names for our API. At the least we need a way to distinguish between the node object that is "us", and node objects that are our peers. We'll be doing things like creating an "us" and then asking it how many peers it knows about and who they are. The term "peer" is clear enough.

From the developer point of view, a node (the application) needs a way to talk to the outside world. Let's borrow a term from networking and call this an "interface". The interface represents us to the rest of the world and presents the rest of the world to us, as a set of other peers. It automatically does whatever discovery it must. When we want to talk to a peer, we get the interface to do that for us. And when a peer talks to us, it's the interface that delivers us the message.

This seems like a clean API design. How about the internals?

- The interface must be multithreaded so that one thread can do I/O in the background, while the foreground API talks to the application. We used this design in the Clone and Freelance client APIs.

- The interface background thread does the discovery business; bind to the UDP port, send out UDP beacons, and receive beacons.

- We need to at least send UUIDs in the beacon message so that we can distinguish our own beacons from those of our peers.

- We need to track peers that appear, and that disappear. For this, I'll use a hash table that stores all known peers and expire peers after some timeout.

- We need a way to report peers and events to the caller. Here we get into a juicy question. How does a background I/O thread tell a foreground API thread that stuff is happening? Callbacks maybe? *Heck no.* We'll use ZeroMQ messages, of course.

The third iteration of the UDP ping program is even simpler and more beautiful than the second. The main body, in C, is just ten lines of code.

[udpping3: UDP discovery, model 3 in C](javascript:;)


[Java](http://zguide.zeromq.org/java:udpping3) | [Python](http://zguide.zeromq.org/py:udpping3) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

[interface: UDP ping interface in C](javascript:;)


[Java](http://zguide.zeromq.org/java:interface) | [Python](http://zguide.zeromq.org/py:interface) | [Ada | Basic | C++ | C# | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

```
--------------------------------------
[006] JOINED
[032] 418E98D4B7184844B7D5E0EE5691084C
--------------------------------------
[004] LEFT
[032] 418E98D4B7184844B7D5E0EE5691084C
```

What's nice about a ZeroMQ-message based API is that I can wrap this any way I like. For instance, I can turn it into callbacks if I really want those. I can also trace all activity on the API very easily.

Some notes about tuning. On Ethernet, five seconds (the expiry time I used in this code) seems like a lot. On a badly stressed WiFi network, you can get ping latencies of 30 seconds or more. If you use a too-aggressive value for the expiry, you'll disconnect nodes that are still there. On the other side, end user applications expect a certain liveliness. If it takes 30 seconds to report that a node has gone, users will get annoyed.

A decent strategy is to detect and report disappeared nodes rapidly, but only delete them after a longer interval. Visually, a node would be green when it's alive, then gray for a while as it went out of reach, then finally disappear. We're not doing this now, but will do it in the real implementation of the as-yet-unnamed framework we're making.

As we will also see later, we have to treat any input from a node, not just UDP beacons, as a sign of life. UDP may get squashed when there's a lot of TCP traffic. This is perhaps the main reason we're not using an existing UDP discovery library: it's necessary to integrate this tightly with our ZeroMQ messaging for it to work.



| [More About UDP](http://zguide.zeromq.org/page:all#More-About-UDP) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-246) [next](http://zguide.zeromq.org/page:all#header-248) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

So we have discovery and presence working over UDP IPv4 broadcasts. It's not ideal, but it works for the local networks we have today. However we can't use UDP for real work, not without additional work to make it reliable. There's a joke about UDP but sometimes you'll get it, and sometimes you won't.

We'll stick to TCP for all one-to-one messaging. There is one more use case for UDP after discovery, which is multicast file distribution. I'll explain why and how, then shelve that for another day. The why is simple: what we call "social networks" is just augmented culture. We create culture by sharing, and this means more and more sharing works that we make or remix. Photos, documents, contracts, tweets. The clouds of devices we're aiming towards do more of this, not less.

Now, there are two principal patterns for sharing content. One is the pub-sub pattern where one node sends out content to a set of other nodes simultaneously. Second is the "late joiner" pattern, where a node arrives somewhat later and wants to catch up to the conversation. We can deal with the late joiner using TCP unicast. But doing TCP unicast to a group of clients at the same time has some disadvantages. First, it can be slower than multicast. Second, it's unfair because some will get the content before others.

Before you jump off to design a UDP multicast protocol, realize that it's not a simple calculation. When you send a multicast packet, the WiFi access point uses a low bit rate to ensure that even the furthest devices will get it safely. Most normal APs don't do the obvious optimization, which is to measure the distance of the furthest device and use that bit rate. Instead, they just use a fixed value. So if you have a few devices close to the AP, multicast will be insanely slow. But if you have a roomful of devices which all want to get the next chapter of the textbook, multicast can be insanely effective.

The curves cross at about 6-12 devices depending on the network. In theory, you could measure the curves in real time and create an adaptive protocol. That would be cool but probably too hard for even the smartest of us.

If you do sit down and sketch out a UDP multicast protocol, realize that you need a channel for recovery, to get lost packets. You'd probably want to do this over TCP, using ZeroMQ. For now, however, we'll forget about multicast UDP and assume all traffic goes over TCP.



| [Spinning Off a Library Project](http://zguide.zeromq.org/page:all#Spinning-Off-a-Library-Project) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-247) [next](http://zguide.zeromq.org/page:all#header-249) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

At this stage, however, the code is growing larger than an example should be, so it's time to create a proper GitHub project. It's a rule: build your projects in public view, and tell people about them as you go so your marketing and community building starts on Day 1. I'll walk through what this involves. I explained in [Chapter 6 - The ZeroMQ Community](http://zguide.zeromq.org/page:all#the-community) about growing communities around projects. We need a few things:

- A name
- A slogan
- A public github repository
- A README that links to the C4 process
- License files
- An issue tracker
- Two maintainers
- A first bootstrap version

The name and slogan first. The trademarks of the 21st century are domain names. So the first thing I do when spinning off a project is to look for a domain name that might work. Quite randomly, one of our old messaging projects was called "Zyre" and I have the domain name for it. The full name is a backronym: the ZeroMQ Realtime Exchange framework.

I'm somewhat shy about pushing new projects into the ZeroMQ community too aggressively, and normally would start a project in either my personal account or the iMatix organization. But we've learned that moving projects after they become popular is counterproductive. My predictions of a future filled with moving pieces are either valid or wrong. If this chapter is valid, we might as well launch this as a ZeroMQ project from the start. If it's wrong, we can delete the repository later or let it sink to the bottom of a long list of forgotten starts.

Start with the basics. The protocol (UDP and ZeroMQ/TCP) will be ZRE (ZeroMQ Realtime Exchange protocol) and the project will be Zyre. I need a second maintainer, so I invite my friend Dong Min (the Korean hacker behind JeroMQ, a pure-Java ZeroMQ stack) to join. He's been working on very similar ideas so is enthusiastic. We discuss this and we get the idea of building Zyre on top of JeroMQ, as well as on top of CZMQ and `libzmq`. This would make it a lot easier to run Zyre on Android. It would also give us two fully separate implementations from the start, which is always a good thing for a protocol.

So we take the FileMQ project I built in [Chapter 7 - Advanced Architecture using ZeroMQ](http://zguide.zeromq.org/page:all#advanced-architecture) as a template for a new GitHub project. The GNU autoconf tools are quite decent, but have a painful syntax. It's easiest to copy existing project files and modify them. The FileMQ project builds a library, has test tools, license files, man pages, and so on. It's not too large so it's a good starting point.

I put together a README to summarize the goals of the project and point to C4. The issue tracker is enabled by default on new GitHub projects, so once we've pushed the UDP ping code as a first version, we're ready to go. However, it's always good to recruit more maintainers, so I create an issue "Call for maintainers" that says:

> If you'd like to help click that lovely green "Merge Pull Request" button and get eternal good karma, add a comment confirming that you've read and understand the C4 process at http://rfc.zeromq.org/spec:22.

Finally, I change the issue tracker labels. By default, GitHub offers the usual variety of issue types, but with C4 we don't use them. Instead, we need just two labels ("Urgent", in red, and "Ready", in black).



| [Point-to-Point Messaging](http://zguide.zeromq.org/page:all#Point-to-Point-Messaging) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-248) [next](http://zguide.zeromq.org/page:all#header-250) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

I'm going to take the last UDP ping program and build a point-to-point messaging layer on top of that. Our goal is that we can detect peers as they join and leave the network, that we can send messages to them, and that we can get replies. It is a nontrivial problem to solve and takes Min and me two days to get a "Hello World" version working.

We had to solve a number of issues:

- What information to send in the UDP beacon, and how to format it.
- What ZeroMQ socket types to use to interconnect nodes.
- What ZeroMQ messages to send, and how to format them.
- How to send a message to a specific node.
- How to know the sender of any message so we could send a reply.
- How to recover from lost UDP beacons.
- How to avoid overloading the network with beacons.

I'll explain these in enough detail so that you understand why we made each choice we did, with some code fragments to illustrate. We tagged this code as [version 0.1.0](https://github.com/zeromq/zyre/zipball/v0.1.0) so you can look at the code: most of the hard work is done in `zre_interface.c`.



| [UDP Beacon Framing](http://zguide.zeromq.org/page:all#UDP-Beacon-Framing) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-249) [next](http://zguide.zeromq.org/page:all#header-251) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Sending UUIDs across the network is the bare minimum for a logical addressing scheme. However, we have a few more aspects to get working before this will work in real use:

- We need some protocol identification so that we can check for and reject invalid packets.
- We need some version information so that we can change this protocol over time.
- We need to tell other nodes how to reach us via TCP, i.e., a ZeroMQ port they can talk to us on.

Let's start with the beacon message format. We probably want a fixed protocol header that will never change in future versions and a body that depends on the version.

**Figure 67 - ZRE discovery message**

![fig67.png](https://github.com/imatix/zguide/raw/master/images/fig67.png)

The version can be a 1-byte counter starting at 1. The UUID is 16 bytes and the port is a 2-byte port number because UDP nicely tells us the sender's IP address for every message we receive. This gives us a 22-byte frame.

The C language (and a few others like Erlang) make it simple to read and write binary structures. We define the beacon frame structure:

\#define BEACON_PROTOCOL`     `"ZRE"
\#define BEACON_VERSION`      `0x01

**typedef** **struct** {
`    `byte protocol [3];
`    `byte version;
`    `uuid_t uuid;
`    `uint16_t port;
} beacon_t;

This makes sending and receiving beacons quite simple. Here is how we send a beacon, using the `zre_udp` class to do the nonportable network calls:

*//  Beacon object*
beacon_t beacon;

*//  Format beacon fields*
beacon.protocol [0] = 'Z';
beacon.protocol [1] = 'R';
beacon.protocol [2] = 'E';
beacon.version = BEACON_VERSION;
memcpy (beacon.uuid, self->uuid, **sizeof** (uuid_t));
beacon.port = htons (self->port);

*//  Broadcast the beacon to anyone who is listening*
zre_udp_send (self->udp, (byte *) &beacon, **sizeof** (beacon_t));

When we receive a beacon, we need to guard against bogus data. We're not going to be paranoid against, for example, denial-of-service attacks. We just want to make sure that we're not going to crash when a bad ZRE implementation sends us erroneous frames.

To validate a frame, we check its size and header. If those are OK, we assume the body is usable. When we get a UUID that isn't ourselves (recall, we'll get our own UDP broadcasts back), we can treat this as a peer:

*//  Get beacon frame from network*
beacon_t beacon;
ssize_t size = zre_udp_recv (self->udp,
`    `(byte *) &beacon, **sizeof** (beacon_t));

*//  Basic validation on the frame*
**if** (size != **sizeof** (beacon_t)
||`  `beacon.protocol [0] != 'Z'
||`  `beacon.protocol [1] != 'R'
||`  `beacon.protocol [2] != 'E'
||`  `beacon.version != BEACON_VERSION)
`    `**return** 0;`               `*//  Ignore invalid beacons*

*//  If we got a UUID and it's not our own beacon, we have a peer*
**if** (memcmp (beacon.uuid, self->uuid, **sizeof** (uuid_t))) {
`    `char *identity = s_uuid_str (beacon.uuid);
`    `s_require_peer (self, identity,
`        `zre_udp_from (self->udp), ntohs (beacon.port));
`    `free (identity);
}



| [True Peer Connectivity (Harmony Pattern)](http://zguide.zeromq.org/page:all#True-Peer-Connectivity-Harmony-Pattern) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-250) [next](http://zguide.zeromq.org/page:all#header-252) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Because ZeroMQ is designed to make distributed messaging easy, people often ask how to interconnect a set of true peers (as compared to obvious clients and servers). It is a thorny question and ZeroMQ doesn't really provide a single clear answer.

TCP, which is the most commonly-used transport in ZeroMQ, is not symmetric; one side must bind and one must connect and though ZeroMQ tries to be neutral about this, it's not. When you connect, you create an outgoing message pipe. When you bind, you do not. When there is no pipe, you cannot write messages (ZeroMQ will return `EAGAIN`).

Developers who study ZeroMQ and then try to create N-to-N connections between sets of equal peers often try a ROUTER-to-ROUTER flow. It's obvious why: each peer needs to address a set of peers, which requires ROUTER. It usually ends with a plaintive email to the list.

Experience teaches us that ROUTER-to-ROUTER is particularly difficult to use successfully. At a minimum, one peer must bind and one must connect, meaning the architecture is not symmetrical. But also because you simply can't tell when you are allowed to safely send a message to a peer. It's a Catch-22: you can talk to a peer after it's talked to you, but the peer can't talk to you until you've talked to it. One side or the other will be losing messages and thus has to retry, which means the peers cannot be equal.

I'm going to explain the Harmony pattern, which solves this problem, and which we use in Zyre.

We want a guarantee that when a peer "appears" on our network, we can talk to it safely without ZeroMQ dropping messages. For this, we have to use a DEALER or PUSH socket that *connects out to the peer* so that even if that connection takes some non-zero time, there is immediately a pipe and ZeroMQ will accept outgoing messages.

A DEALER socket cannot address multiple peers individually. But if we have one DEALER per peer, and we connect that DEALER to the peer, we can safely send messages to a peer as soon as we've connected to it.

Now, the next problem is to know who sent us a particular message. We need a reply address that is the UUID of the node who sent any given message. DEALER can't do this unless we prefix every single message with that 16-byte UUID, which would be wasteful. ROUTER does do it if we set the identity properly before connecting to the router.

And so the Harmony pattern comes down to these components:

- One ROUTER socket that we bind to a ephemeral port, which we broadcast in our beacons.
- One DEALER socket *per peer* that we connect to the peer's ROUTER socket.
- Reading from our ROUTER socket.
- Writing to the peer's DEALER socket.

The next problem is that discovery isn't neatly synchronized. We can get the first beacon from a peer *after* we start to receive messages from it. A message comes in on the ROUTER socket and has a nice UUID attached to it, but no physical IP address and port. We have to force discovery over TCP. To do this, our first command to any new peer to which we connect is an `OHAI` command with our IP address and port. This ensure that the receiver connects back to us before trying to send us any command.

Here it is, broken down into steps:

- If we receive a UDP beacon from a new peer, we connect to the peer through a DEALER socket.
- We read messages from our ROUTER socket, and each message comes with the UUID of the sender.
- If it's an `OHAI` message, we connect back to that peer if not already connected to it.
- If it's any other message, we *must* already be connected to the peer (a good place for an assertion).
- We send messages to each peer using the per-peer DEALER socket, which *must* be connected.
- When we connect to a peer, we also tell our application that the peer exists.
- Every time we get a message from a peer, we treat that as a heartbeat (it's alive).

If we were not using UDP but some other discovery mechanism, I'd still use the Harmony pattern for a true peer network: one ROUTER for input from all peers, and one DEALER per peer for output. Bind the ROUTER, connect the DEALER, and start each conversation with an `OHAI` equivalent that provides the return IP address and port. You would need some external mechanism to bootstrap each connection.



| [Detecting Disappearances](http://zguide.zeromq.org/page:all#Detecting-Disappearances) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-251) [next](http://zguide.zeromq.org/page:all#header-253) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Heartbeating sounds simple but it's not. UDP packets get dropped when there's a lot of TCP traffic, so if we depend on UDP beacons, we'll get false disconnections. TCP traffic can be delayed for 5, 10, even 30 seconds if the network is really busy. So if we kill peers when they go quiet, we'll have false disconnections.

Because UDP beacons aren't reliable, it's tempting to add in TCP beacons. After all, TCP will deliver them reliably. However, there's one little problem. Imagine that you have 100 nodes on a network, and each node sends a TCP beacon once a second. Each beacon is 22 bytes, not counting TCP's framing overhead. That is 100 * 99 * 22 bytes per second, or 217,000 bytes/second just for heartbeating. That's about 1-2% of a typical WiFi network's ideal capacity, which sounds OK. But when a network is stressed or fighting other networks for airspace, that extra 200K a second will break what's left. UDP broadcasts are at least low cost.

So what we do is switch to TCP heartbeats only when a specific peer hasn't sent us any UDP beacons in a while. And then we send TCP heartbeats only to that one peer. If the peer continues to be silent, we conclude it's gone away. If the peer comes back with a different IP address and/or port, we have to disconnect our DEALER socket and reconnect to the new port.

This gives us a set of states for each peer, though at this stage the code doesn't use a formal state machine:

- Peer visible thanks to UDP beacon (we connect using IP address and port from beacon)
- Peer visible thanks to `OHAI` command (we connect using IP address and port from command)
- Peer seems alive (we got a UDP beacon or command over TCP recently)
- Peer seems quiet (no activity in some time, so we send a `HUGZ` command)
- Peer has disappeared (no reply to our `HUGZ` commands, so we destroy peer)

There's one remaining scenario we didn't address in the code at this stage. It's possible for a peer to change IP addresses and ports without actually triggering a disappearance event. For example, if the user switches off WiFi and then switches it back on, the access point can assign the peer a new IP address. We'll need to handle a disappeared WiFi interface on our node by unbinding the ROUTER socket and rebinding it when we can. Because this is not central to the design now, I decide to log an issue on the GitHub tracker and leave it for a rainy day.



| [Group Messaging](http://zguide.zeromq.org/page:all#Group-Messaging) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-252) [next](http://zguide.zeromq.org/page:all#header-254) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Group messaging is a common and very useful pattern. The concept is simple: instead of talking to a single node, you talk to a "group" of nodes. The group is just a name, a string that you agree on in the application. It's precisely like using the pub-sub prefixes in PUB and SUB sockets. In fact, the only reason I say "group messaging" and not "pub-sub" is to prevent confusion, because we're not going to use PUB-SUB sockets for this.

PUB-SUB sockets would almost work. But we've just done such a lot of work to solve the late joiner problem. Applications are inevitably going to wait for peers to arrive before sending messages to groups, so we have to build on the Harmony pattern rather than start again beside it.

Let's look at the operations we want to do on groups:

- We want to join and leave groups.
- We want to know what other nodes are in any given group.
- We want to send a message to (all nodes in) a group.

These look familiar to anyone who's used Internet Relay Chat, except that we have no server. Every node will need to keep track of what each group represents. This information will not always be fully consistent across the network, but it will be close enough.

Our interface will track a set of groups (each an object). These are all the known groups with one or more member node, excluding ourselves. We'll track nodes as they leave and join groups. Because nodes can join the network at any time, we have to tell new peers what groups we're in. When a peer disappears, we'll remove it from all groups we know about.

This gives us some new protocol commands:

- `JOIN` - we send this to all peers when we join a group.
- `LEAVE` - we send this to all peers when we leave a group.

Plus, we add a `groups` field to the first command we send (renamed from `OHAI` to `HELLO` at this point because I need a larger lexicon of command verbs).

Lastly, let's add a way for peers to double-check the accuracy of their group data. The risk is that we miss one of the above messages. Though we are using Harmony to avoid the typical message loss at startup, it's worth being paranoid. For now, all we need is a way to detect such a failure. We'll deal with recovery later, if the problem actually happens.

I'll use the UDP beacon for this. What we want is a rolling counter that simply tells how many join and leave operations ("transitions") there have been for a node. It starts at 0 and increments for each group we join or leave. We can use a minimal 1-byte value because that will catch all failures except the astronomically rare "we lost precisely 256 messages in a row" failure (this is the one that hits during the first demo). We will also put the transitions counter into the `JOIN`, `LEAVE`, and `HELLO` commands. And to try to provoke the problem, we'll test by joining/leaving several hundred groups with a high-water mark set to 10 or so.

It's time to choose verbs for the group messaging. We need a command that means "talk to one peer" and one that means "talk to many peers". After some attempts, my best choices are `WHISPER` and `SHOUT`, and this is what the code uses. The `SHOUT` command needs to tell the user the group name, as well as the sender peer.

Because groups are like pub-sub, you might be tempted to use this to broadcast the `JOIN`and `LEAVE` commands as well, perhaps by creating a "global" group that all nodes join. My advice is to keep groups purely as user-space concepts for two reasons. First, how do you join the global group if you need the global group to send out a `JOIN` command? Second, it creates special cases (reserved names) which are messy.

It's simpler just to send `JOIN`s and `LEAVE`s explicitly to all connected peers, period.

I'm not going to work through the implementation of group messaging in detail because it's fairly pedantic and not too exciting. The data structures for group and peer management aren't optimal, but they're workable. We use the following:

- A list of groups for our interface, which we can send to new peers in a `HELLO`command;
- A hash of groups for other peers, which we update with information from `HELLO`, `JOIN`, and `LEAVE` commands;
- A hash of peers for each group, which we update with the same three commands.

At this stage, I'm starting to get pretty happy with the binary serialization (our codec generator from [Chapter 7 - Advanced Architecture using ZeroMQ](http://zguide.zeromq.org/page:all#advanced-architecture)), which handles lists and dictionaries as well as strings and integers.

This version is tagged in the repository as v0.2.0 and you can [download the tarball](https://github.com/zeromq/zyre/tags) if you want to check what the code looked like at this stage.



| [Testing and Simulation](http://zguide.zeromq.org/page:all#Testing-and-Simulation) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-253) [next](http://zguide.zeromq.org/page:all#header-255) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

When you build a product out of pieces, and this includes a distributed framework like Zyre, the only way to know that it will work properly in real life is to simulate real activity on each piece.



| [On Assertions](http://zguide.zeromq.org/page:all#On-Assertions) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-254) [next](http://zguide.zeromq.org/page:all#header-256) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

The proper use of assertions is one of the hallmarks of a professional programmer.

Our confirmation bias as creators makes it hard to test our work properly. We tend to write tests to prove the code works, rather than trying to prove it doesn't. There are many reasons for this. We pretend to ourselves and others that we can be (could be) perfect, when in fact we consistently make mistakes. Bugs in code are seen as "bad", rather than "inevitable", so psychologically we want to see fewer of them, not uncover more of them. "He writes perfect code" is a compliment rather than a euphemism for "he never takes risks so his code is as boring and heavily used as cold spaghetti".

Some cultures teach us to aspire to perfection and punish mistakes in education and work, which makes this attitude worse. To accept that we're fallible, and then to learn how to turn that into profit rather than shame is one of the hardest intellectual exercises in any profession. We leverage our fallibilities by working with others and by challenging our own work sooner, not later.

One trick that makes it easier is to use assertions. Assertions are not a form of error handling. They are executable theories of fact. The code asserts, "At this point, such and such must be true" and if the assertion fails, the code kills itself.

The faster you can prove code incorrect, the faster and more accurately you can fix it. Believing that code works and proving that it behaves as expected is less science, more magical thinking. It's far better to be able to say, "`libzmq` has five hundred assertions and despite all my efforts, not one of them fails".

So the Zyre code base is scattered with assertions, and particularly a couple on the code that deals with the state of peers. This is the hardest aspect to get right: peers need to track each other and exchange state accurately or things stop working. The algorithms depend on asynchronous messages flying around and I'm pretty sure the initial design has flaws. It always does.

And as I test the original Zyre code by starting and stopping instances of `zre_ping` by hand, every so often I get an assertion failure. Running by hand doesn't reproduce these often enough, so let's make a proper tester tool.



| [On Up-Front Testing](http://zguide.zeromq.org/page:all#On-Up-Front-Testing) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-255) [next](http://zguide.zeromq.org/page:all#header-257) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Being able to fully test the real behavior of individual components in the laboratory can make a 10x or 100x difference to the cost of your project. That confirmation bias engineers have to their own work makes up-front testing incredibly profitable, and late-stage testing incredibly expensive.

I'll tell you a short story about a project we worked on in the late 1990's. We provided the software and other teams provided the hardware for a factory automation project. Three or four teams brought their experts on-site, which was a remote factory (funny how the polluting factories are always in remote border country).

One of these teams, a firm specializing in industrial automation, built ticket machines: kiosks, and software to run on them. Nothing unusual: swipe a badge, choose an option, receive a ticket. They assembled two of these kiosks on-site, each week bringing some more bits and pieces. Ticket printers, monitor screens, special keypads from Israel. The stuff had to be resistant against dust because the kiosks sat outside. Nothing worked. The screens were unreadable in the sun. The ticket printers continually jammed and misprinted. The internals of the kiosk just sat on wooden shelving. The kiosk software crashed regularly. It was comedic except that the project really, *really* had to work and so we spent weeks and then months on-site helping the other teams debug their bits and pieces until it worked.

A year later, there was a second factory, and the same story. By this time the client, was getting impatient. So when they came to the third and largest factory, a year later, we jumped up and said, "please let us make the kiosks and the software and everything".

We made a detailed design for the software and hardware and found suppliers for all the pieces. It took us three months to search the Internet for each component (in those days, the Internet was a lot slower), and another two months to get them assembled into stainless-steel bricks each weighing about twenty kilos. These bricks were two feet square and eight inches deep, with a large flat-screen panel behind unbreakable glass, and two connectors: one for power, one for Ethernet. You loaded up the paper bin with enough for six months, then screwed the brick into a housing, and it automatically booted, found its DNS server, loaded its Linux OS and then application software. It connected to the real server, and showed the main menu. You got access to the configuration screens by swiping a special badge and then entering a code.

The software was portable so we could test that as we wrote it, and as we collected the pieces from our suppliers we kept one of each so we had a disassembled kiosk to play with. When we got our finished kiosks, they all worked immediately. We shipped them to the client, who plugged them into their housing, switched them on, and went to business. We spent a week or so on-site, and in ten years, one kiosk broke (the screen died, and was replaced).

Lesson is, test upfront so that when you plug the thing in, you know precisely how it's going to behave. If you haven't tested it upfront, you're going to be spending weeks and months in the field ironing out problems that should never have been there.



| [The Zyre Tester](http://zguide.zeromq.org/page:all#The-Zyre-Tester) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-256) [next](http://zguide.zeromq.org/page:all#header-258) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

During manual testing, I did hit an assertion rarely. It then disappeared. Because I don't believe in magic, I know that meant the code was still wrong somewhere. So, the next step was heavy-duty testing of the Zyre v0.2.0 code to try to break its assertions, and get a good idea of how it will behave in the field.

We packaged the discovery and messaging functionality as an *interface* object that the main program creates, works with, and then destroys. We don't use any global variables. This makes it easy to start large numbers of interfaces and simulate real activity, all within one process. And if there's one thing we've learned from writing lots of examples, it's that ZeroMQ's ability to orchestrate multiple threads in a single process is *much* easier to work with than multiple processes.

The first version of the tester consists of a main thread that starts and stops a set of child threads, each running one interface, each with a ROUTER, DEALER, and UDP socket (R, D, and U in the diagram).

**Figure 68 - Zyre Tester Tool**

![fig68.png](https://github.com/imatix/zguide/raw/master/images/fig68.png)

The nice thing is that when I am connected to a WiFi access point, all Zyre traffic (even between two interfaces in the same process) goes across the AP. This means I can fully stress test any WiFi infrastructure with just a couple of PCs running in a room. It's hard to emphasize how valuable this is: if we had built Zyre as, say, a dedicated service for Android, we'd literally need dozens of Android tablets or phones to do any large-scale testing. Kiosks, and all that.

The focus is now on breaking the current code, trying to prove it wrong. There's *no point* at this stage in testing how well it runs, how fast it is, how much memory it uses, or anything else. We'll work up to trying (and failing) to break each individual functionality, but first, we try to break some of the core assertions I've put into the code.

These are:

- The first command that any node receives from a peer MUST be `HELLO`. In other words, messages *cannot* be lost during the peer-to-peer connection process.

- The state each node calculates for its peers matches the state each peer calculates for itself. In other words, again, no messages are lost in the network.

- When my application sends a message to a peer, we have a connection to that peer. In other words, the application only "sees" a peer after we have established a ZeroMQ connection to it.

With ZeroMQ, there are several cases where we may lose messages. One is the "late joiner" syndrome. Two is when we close sockets without sending everything. Three is when we overflow the high-water mark on a ROUTER or PUB socket. Four is when we use an unknown address with a ROUTER socket.

Now, I *think* Harmony gets around all these potential cases. But we're also adding UDP to the mix. So the first version of the tester simulates an unstable and dynamic network, where nodes come and go randomly. It's here that things will break.

Here is the main thread of the tester, which manages a pool of 100 threads, starting and stopping each one randomly. Every ~750 msecs it either starts or stops one random thread. We randomize the timing so that threads aren't all synchronized. After a few minutes, we have an average of 50 threads happily chatting to each other like Korean teenagers in the Gangnam subway station:

int main (int argc, char *argv [])
{
`    `*//  Initialize context for talking to tasks*
`    `zctx_t *ctx = zctx_new ();
`    `zctx_set_linger (ctx, 100);

`    `*//  Get number of interfaces to simulate, default 100*
`    `int max_interface = 100;
`    `int nbr_interfaces = 0;
`    `**if** (argc > 1)
`        `max_interface = atoi (argv [1]);

`    `*//  We address interfaces as an array of pipes*
`    `void **pipes = zmalloc (**sizeof** (void *) * max_interface);

`    `*//  We will randomly start and stop interface threads*
`    `**while** (!zctx_interrupted) {
`        `uint index = randof (max_interface);
`        `*//  Toggle interface thread*
`        `**if** (pipes [index]) {
`            `zstr_send (pipes [index], "STOP");
`            `zsocket_destroy (ctx, pipes [index]);
`            `pipes [index] = NULL;
`            `zclock_log ("I: Stopped interface (%d running)",
`                `--nbr_interfaces);
`        }`
`        `**else** {
`            `pipes [index] = zthread_fork (ctx, interface_task, NULL);
`            `zclock_log ("I: Started interface (%d running)",
`                `++nbr_interfaces);
`        }`
`        `*//  Sleep ~750 msecs randomly so we smooth out activity*
`        `zclock_sleep (randof (500) + 500);
`    }`
`    `zctx_destroy (&ctx);
`    `**return** 0;
}

Note that we maintain a *pipe* to each child thread (CZMQ creates the pipe automatically when we use the `zthread_fork` method). It's via this pipe that we tell child threads to stop when it's time for them to leave. The child threads do the following (I'm switching to pseudo-code for clarity):

```
create an interface
while true:
    poll on pipe to parent, and on interface
    if parent sent us a message:
        break
    if interface sent us a message:
        if message is ENTER:
            send a WHISPER to the new peer
        if message is EXIT:
            send a WHISPER to the departed peer
        if message is WHISPER:
            send back a WHISPER 1/2 of the time
        if message is SHOUT:
            send back a WHISPER 1/3 of the time
            send back a SHOUT 1/3 of the time
    once per second:
        join or leave one of 10 random groups
destroy interface
```



| [Test Results](http://zguide.zeromq.org/page:all#Test-Results) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-257) [next](http://zguide.zeromq.org/page:all#header-259) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Yes, we broke the code. Several times, in fact. This was satisfying. I'll work through the different things we found.

Getting nodes to agree on consistent group status was the most difficult. Every node needs to track the group membership of the whole network, as I already explained in the section "Group Messaging". Group messaging is a pub-sub pattern. `JOIN`s and `LEAVE`s are analogous to subscribe and unsubscribe messages. It's essential that none of these ever get lost, or we'll find nodes dropping randomly off groups.

So each node counts the total number of `JOIN`s and `LEAVE`s it's ever done, and broadcasts this status (as 1-byte rolling counter) in its UDP beacon. Other nodes pick up the status, compare it to their own calculations, and if there's a difference, the code asserts.

The first problem was that UDP beacons get delayed randomly, so they're useless for carrying the status. When a beacons arrives late, the status is inaccurate and we get a *false negative*. To fix this, we moved the status information into the `JOIN` and `LEAVE` commands. We also added it to the `HELLO` command. The logic then becomes:

- Get initial status for a peer from its `HELLO` command.
- When getting a `JOIN` or `LEAVE` from a peer, increment the status counter.
- Check that the new status counter matches the value in the `JOIN` or `LEAVE` command
- If it doesn't, assert.

Next problem we got was that messages were arriving unexpectedly on new connections. The Harmony pattern connects, then sends `HELLO` as the first command. This means the receiving peer should always get `HELLO` as the first command from a new peer. We were seeing `PING`, `JOIN`, and other commands arriving.

This turned out to be due to CZMQ's ephemeral port logic. An ephemeral port is just a dynamically assigned port that a service can get rather than asking for a fixed port number. A POSIX system usually assigns ephemeral ports in the range 0xC000 to 0xFFFF. CZMQ's logic is to look for a free port in this range, bind to that, and return the port number to the caller.

This sounds fine, until you get one node stopping and another node starting close together, and the new node getting the port number of the old node. Remember that ZeroMQ tries to re-establish a broken connection. So when the first node stopped, its peers would retry to connect. When the new node appears on that same port, suddenly all the peers connect to it and start chatting like they're old buddies.

It's a general problem that affects any larger-scale dynamic ZeroMQ application. There are a number of plausible answers. One is to not reuse ephemeral ports, which is easier said than done when you have multiple processes on one system. Another solution would be to select a random port each time, which at least reduces the risk of hitting a just-freed port. This brings the risk of a garbage connection down to perhaps 1/1000 but it's still there. Perhaps the best solution is to accept that this can happen, understand the causes, and deal with it on the application level.

We have a stateful protocol that always starts with a `HELLO` command. We know that it's possible for peers to connect to us, thinking we're an existing node that went away and came back, and send us other commands. Step one is when we discover a new peer, to destroy any existing peer connected to the same endpoint. It's not a full answer but at least it's polite. Step two is to ignore anything coming in from a new peer until that peer says `HELLO`.

This doesn't require any change to the protocol, but it must be specified in the protocol when we come to it: due to the way ZeroMQ connections work, it's possible to receive unexpected commands from a *well-behaving* peer and there is no way to return an error code or otherwise tell that peer to reset its connection. Thus, a peer must discard any command from a peer until it receives `HELLO`.

In fact, if you draw this on a piece of paper and think it through, you'll see that you never get a `HELLO` from such a connection. The peer will send `PING`s and `JOIN`s and `LEAVE`s and then eventually time out and close, as it fails to get any heartbeats back from us.

You'll also see that there's no risk of confusion, no way for commands from two peers to get mixed into a single stream on our DEALER socket.

When you are satisfied that this works, we're ready to move on. This version is tagged in the repository as v0.3.0 and you can [download the tarball](https://github.com/zeromq/zyre/tags) if you want to check what the code looked like at this stage.

Note that doing heavy simulation of lots of nodes will probably cause your process to run out of file handles, giving an assertion failure in `libzmq`. I raised the per-process limit to 30,000 by running (on my Linux box):

```
ulimit -n 30000
```



| [Tracing Activity](http://zguide.zeromq.org/page:all#Tracing-Activity) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-258) [next](http://zguide.zeromq.org/page:all#header-260) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

To debug the kinds of problems we saw here, we need extensive logging. There's a lot happening in parallel, but every problem can be traced down to a specific exchange between two nodes, consisting of a set of events that happen in strict sequence. We know how to make very sophisticated logging, but as usual it's wiser to make just what we need and no more. We have to capture:

- Time and date for each event.
- In which node the event occurred.
- The peer node, if any.
- What the event was (e.g., which command arrived).
- Event data, if any.

The very simplest technique is to print the necessary information to the console, with a timestamp. That's the approach I used. Then it's simple to find the nodes affected by a failure, filter the log file for only messages referring to them, and see exactly what happened.



| [Dealing with Blocked Peers](http://zguide.zeromq.org/page:all#Dealing-with-Blocked-Peers) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-259) [next](http://zguide.zeromq.org/page:all#header-261) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

In any performance-sensitive ZeroMQ architecture, you need to solve the problem of flow control. You cannot simply send unlimited messages to a socket and hope for the best. At the one extreme, you can exhaust memory. This is a classic failure pattern for a message broker: one slow client stops receiving messages; the broker starts to queue them, and eventually exhausts memory and the whole process dies. At the other extreme, the socket drops messages, or blocks, as you hit the high-water mark.

With Zyre we want to distribute messages to a set of peers, and we want to do this fairly. Using a single ROUTER socket for output would be problematic because any one blocked peer would block outgoing traffic to all peers. TCP does have good algorithms for spreading the network capacity across a set of connections. And we're using a separate DEALER socket to talk to each peer, so in theory each DEALER socket will send its queued messages in the background reasonably fairly.

The normal behavior of a DEALER socket that hits its high-water mark is to block. This is usually ideal, but it's a problem for us here. Our current interface design uses one thread that distributes messages to all peers. If one of those send calls were to block, all output would block.

There are a few options to avoid blocking. One is to use `zmq_poll()` on the whole set of DEALER sockets, and only write to sockets that are ready. I don't like this for a couple of reasons. First, the DEALER socket is hidden inside the peer class, and it is cleaner to allow each class to handle this opaquely. Second, what do we do with messages we can't yet deliver to a DEALER socket? Where do we queue them? Third, it seems to be side-stepping the issue. If a peer is really so busy it can't read its messages, something is wrong. Most likely, it's dead.

So no polling for output. The second option is to use one thread per peer. I quite like the idea of this because it fits into the ZeroMQ design pattern of "do one thing in one thread". But this is going to create *a lot* of threads (square of the number of nodes we start) in the simulation, and we're already running out of file handles.

A third option is to use a nonblocking send. This is nicer and it's the solution I choose. We can then provide each peer with a reasonable outgoing queue (the HWM) and if that gets full, treat it as a fatal error on that peer. This will work for smaller messages. If we're sending large chunks—e.g., for content distribution—we'll need a credit-based flow control on top.

Therefore the first step is to prove to ourselves that we can turn the normal blocking DEALER socket into a nonblocking socket. This example creates a normal DEALER socket, connects it to some endpoint (so that there's an outgoing pipe and the socket will accept messages), sets the high-water mark to four, and then sets the send timeout to zero:

[eagain: Checking EAGAIN on DEALER socket in C](javascript:;)


[C#](http://zguide.zeromq.org/cs:eagain) | [Java](http://zguide.zeromq.org/java:eagain) | [Python](http://zguide.zeromq.org/py:eagain) | [Ada | Basic | C++ | Clojure | CL | Delphi | Erlang | F# | Felix | Go | Haskell | Haxe | Lua | Node.js | Objective-C | ooc | Perl | PHP | Q | Racket | Ruby | Scala | Tcl](http://zguide.zeromq.org/main:translate)

```
Sending message 0
Sending message 1
Sending message 2
Sending message 3
Sending message 4
Resource temporarily unavailable
```

The next step is to decide what a reasonable high-water mark would be for a peer. Zyre is meant for human interactions; that is, applications that chat at a low frequency, such as two games or a shared drawing program. I'd expect a hundred messages per second to be quite a lot. Our "peer is really dead" timeout is 10 seconds. So a high-water mark of 1,000 seems fair.

Rather than set a fixed HWM or use the default (which randomly also happens to be 1,000), we calculate it as 100 * the timeout. Here's how we configure a new DEALER socket for a peer:

*//  Create new outgoing socket (drop any messages in transit)*
self->mailbox = zsocket_new (self->ctx, ZMQ_DEALER);

*//`  `Set our caller "From" identity so that receiving node knows//`  `who each message came from.*
zsocket_set_identity (self->mailbox, reply_to);

*//  Set a high-water mark that allows for reasonable activity*
zsocket_set_sndhwm (self->mailbox, PEER_EXPIRED * 100);

*//  Send messages immediately or return EAGAIN*
zsocket_set_sndtimeo (self->mailbox, 0);

*//  Connect through to peer node*
zsocket_connect (self->mailbox, "tcp://%s", endpoint);

And finally, what do we do when we get an `EAGAIN` on a peer? We don't need to go through all the work of destroying the peer because the interface will do this automatically if it doesn't get any message from the peer within the expiration timeout. Just dropping the last message seems very weak; it will give the receiving peer gaps.

I'd prefer a more brutal response. Brutal is good because it forces the design to a "good" or "bad" decision rather than a fuzzy "should work but to be honest there are a lot of edge cases so let's worry about it later". Destroy the socket, disconnect the peer, and stop sending anything to it. The peer will eventually have to reconnect and re-initialize any state. It's kind of an assertion that 100 messages a second is enough for anyone. So, in the `zre_peer_send` method:

int
zre_peer_send (zre_peer_t *self, zre_msg_t **msg_p)
{
`    `assert (self);
`    `**if** (self->connected) {
`        `**if** (zre_msg_send (msg_p, self->mailbox) && errno == EAGAIN) {
`            `zre_peer_disconnect (self);
`            `**return** -1;
`        }`
`    }`
`    `**return** 0;
}

Where the disconnect method looks like this:

void
zre_peer_disconnect (zre_peer_t *self)
{
`    `*//  If connected, destroy socket and drop all pending messages*
`    `assert (self);
`    `**if** (self->connected) {
`        `zsocket_destroy (self->ctx, self->mailbox);
`        `free (self->endpoint);
`        `self->endpoint = NULL;
`        `self->connected = false;
`    }`
}



| [Distributed Logging and Monitoring](http://zguide.zeromq.org/page:all#Distributed-Logging-and-Monitoring) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-260) [next](http://zguide.zeromq.org/page:all#header-262) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Let's look at logging and monitoring. If you've ever managed a real server (like a web server), you know how vital it is to have a capture of what is going on. There are a long list of reasons, not least:

- To measure the performance of the system over time.
- To see what kinds of work are done the most, to optimize performance.
- To track errors and how often they occur.
- To do postmortems of failures.
- To provide an audit trail in case of dispute.

Let's scope this in terms of the problems we think we'll have to solve:

- We want to track key events (such as nodes leaving and rejoining the network).
- For each event, we want to track a consistent set of data: the date/time, node that observed the event, peer that created the event, type of event itself, and other event data.
- We want to be able to switch logging on and off at any time.
- We want to be able to process log data mechanically because it will be sizable.
- We want to be able to monitor a running system; that is, collect logs and analyze in real time.
- We want log traffic to have minimal effect on the network.
- We want to be able to collect log data at a single point on the network.

As in any design, some of these requirements are hostile to each other. For example, collecting log data in real time means sending it over the network, which will affect network traffic to some extent. However, as in any design, these requirements are also hypothetical until we have running code so we can't take them too seriously. We'll aim for *plausibly good enough* and improve over time.



| [A Plausible Minimal Implementation](http://zguide.zeromq.org/page:all#A-Plausible-Minimal-Implementation) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-261) [next](http://zguide.zeromq.org/page:all#header-263) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Arguably, just dumping log data to disk is one solution, and it's what most mobile applications do (using "debug logs"). But most failures require correlation of events from two nodes. This means searching lots of debug logs by hand to find the ones that matter. It's not a very clever approach.

We want to send log data somewhere central, either immediately, or opportunistically (i.e., store and forward). For now, let's focus on immediate logging. My first idea when it comes to sending data is to use Zyre for this. Just send log data to a group called "LOG", and hope someone collects it.

But using Zyre to log Zyre itself is a Catch-22. Who logs the logger? What if we want a verbose log of every message sent? Do we include logging messages in that or not? It quickly gets messy. We want a logging protocol that's independent of Zyre's main ZRE protocol. The simplest approach is a pub-sub protocol, where all nodes publish log data on a PUB socket and a collector picks that up via a SUB socket.

**Figure 69 - Distributed Log Collection**

![fig69.png](https://github.com/imatix/zguide/raw/master/images/fig69.png)

The collector can, of course, run on any node. This gives us a nice range of use cases:

- A passive log collector that stores log data on disk for eventual statistical analysis; this would be a PC with sufficient hard disk space for weeks or months of log data.

- A collector that stores log data into a database where it can be used in real time by other applications. This might be overkill for a small workgroup, but would be snazzy for tracking the performance of larger groups. The collector could collect log data over WiFi and then forward it over Ethernet to a database somewhere.

- A live meter application that joined the Zyre network and then collected log data from nodes, showing events and statistics in real time.

The next question is how to interconnect the nodes and collector. Which side binds, and which connects? Both ways will work here, but it's marginally better if the PUB sockets connect to the SUB socket. If you recall, ZeroMQ's internal buffers only pop into existence when there are connections. It means as soon as a node connects to the collector, it can start sending log data without loss.

How do we tell nodes what endpoint to connect to? We may have any number of collectors on the network, and they'll be using arbitrary network addresses and ports. We need some kind of service announcement mechanism, and here we can use Zyre to do the work for us. We could use group messaging, but it seems neater to build service discovery into the ZRE protocol itself. It's nothing complex: if a node provides a service X, it can tell other nodes about that when it sends them a `HELLO` command.

We'll extend the `HELLO` command with a *headers* field that holds a set of name=value pairs. Let's define that the header `X-ZRELOG` specifies the collector endpoint (the SUB socket). A node that acts as a collector can add a header like this (for example):

```
X-ZRELOG=tcp://192.168.1.122:9992
```

When another node sees this header, it simply connects its PUB socket to that endpoint. Log data now gets distributed to all collectors (zero or more) on the network.

Making this first version was fairly simple and took half a day. Here are the pieces we had to make or change:

- We made a new class `zre_log` that accepts log data and manages the connection to the collector, if any.
- We added some basic management for peer headers, taken from the `HELLO` command.
- When a peer has the `X-ZRELOG` header, we connect to the endpoint it specifies.
- Where we were logging to stdout, we switched to logging via the `zre_log` class.
- We extended the interface API with a method that lets the application set headers.
- We wrote a simple logger application that manages the SUB socket and sets the `X-ZRELOG` header.
- We send our own headers when we send a `HELLO` command.

This version is tagged in the Zyre repository as v0.4.0 and you can [download the tarball](https://github.com/zeromq/zyre/tags) if you want to see what the code looked like at this stage.

At this stage, the log message is just a string. We'll make more professionally structured log data in a little while.

First, a note on dynamic ports. In the `zre_tester` app that we use for testing, we create and destroy interfaces aggressively. One consequence is that a new interface can easily reuse a port that was just freed by another application. If there's a ZeroMQ socket somewhere trying to connect this port, the results can be hilarious.

Here's the scenario I had, which caused a few minutes' confusion. The logger was running on a dynamic port:

- Start logger application
- Start tester application
- Stop logger
- Tester receives invalid message (and asserts as designed)

As the tester created a new interface, that reused the dynamic port freed by the (just stopped) logger, and suddenly the interface began to receive log data from nodes on its mailbox. We saw a similar situation before, where a new interface could reuse the port freed by an old interface and start getting old data.

The lesson is, if you use dynamic ports, be prepared to receive random data from ill-informed applications that are reconnecting to you. Switching to a static port stopped the misbehaving connection. That's not a full solution though. There are two more weaknesses:

- As I write this, `libzmq` doesn't check socket types when connecting. The [ZMTP/2.0 protocol](http://rfc.zeromq.org/spec:15) does announce each peer's socket type, so this check is doable.

- The ZRE protocol has no fail-fast (assertion) mechanism; we need to read and parse a whole message before realizing that it's invalid.

Let's address the second one. Socket pair validation wouldn't solve this fully anyway.



| [Protocol Assertions](http://zguide.zeromq.org/page:all#Protocol-Assertions) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-262) [next](http://zguide.zeromq.org/page:all#header-264) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

As Wikipedia puts it, "Fail-fast systems are usually designed to stop normal operation rather than attempt to continue a possibly flawed process." A protocol like HTTP has a fail-fast mechanism in that the first four bytes that a client sends to an HTTP server must be "HTTP". If they're not, the server can close the connection without reading anything more.

Our ROUTER socket is not connection-oriented so there's no way to "close the connection" when we get bad incoming messages. However, we can throw out the entire message if it's not valid. The problem is going to be worse when we use ephemeral ports, but it applies broadly to all protocols.

So let's define a *protocol assertion* as being a unique signature that we place at the start of each message and which identities the intended protocol. When we read a message, we check the signature and if it's not what we expect, we discard the message silently. A good signature should be hard to confuse with regular data and give us enough space for a number of protocols.

I'm going to use a 16-bit signature consisting of a 12-bit pattern and a 4-bit protocol ID. The pattern %xAAA is meant to stay away from values we might otherwise expect to see at the start of a message: %x00, %xFF, and printable characters.

**Figure 70 - Protocol Signature**

![fig70.png](https://github.com/imatix/zguide/raw/master/images/fig70.png)

As our protocol codec is generated, it's relatively easy to add this assertion. The logic is:

- Get first frame of message.
- Check if first two bytes are %xAAA with expected 4-bit signature.
- If so, continue to parse rest of message.
- If not, skip all "more" frames, get first frame, and repeat.

To test this, I switched the logger back to using an ephemeral port. The interface now properly detects and discards any messages that don't have a valid signature. If the message has a valid signature and is *still* wrong, that's a proper bug.



| [Binary Logging Protocol](http://zguide.zeromq.org/page:all#Binary-Logging-Protocol) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-263) [next](http://zguide.zeromq.org/page:all#header-265) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Now that we have the logging framework working properly, let's look at the protocol itself. Sending strings around the network is simple, but when it comes to WiFi we really cannot afford to waste bandwidth. We have the tools to work with efficient binary protocols, so let's design one for logging.

This is going to be a pub-sub protocol and in ZeroMQ v3.x we do publisher-side filtering. This means we can do multi-level logging (errors, warnings, information) if we put the logging level at the start of the message. So our message starts with a protocol signature (two bytes), a logging level (one byte), and an event type (one byte).

In the first version, we send UUID strings to identify each node. As text, these are 32 characters each. We can send binary UUIDs, but it's still verbose and wasteful. We don't care about the node identifiers in the log files. All we need is some way to correlate events. So what's the shortest identifier we can use that's going to be unique enough for logging? I say "unique enough" because while we really want zero chance of duplicate UUIDs in the live code, log files are not so critical.

The simplest plausible answer is to hash the IP address and port into a 2-byte value. We'll get some collisions, but they'll be rare. How rare? As a quick sanity check, I write a small program that generates a bunch of addresses and hashes them into 16-bit values, looking for collisions. To be sure, I generate 10,000 addresses across a small number of IP addresses (matching a simulation setup), and then across a large number of addresses (matching a real-life setup). The hashing algorithm is a *modified Bernstein*:

uint16_t hash = 0;
**while** (*endpoint)
`    `hash = 33 * hash ^ *endpoint++;

I don't get any collisions over several runs, so this will work as identifier for the log data. This adds four bytes (two for the node recording the event, and two for its peer in events that come from a peer).

Next, we want to store the date and time of the event. The POSIX `time_t` type was previously 32 bits, but because this overflows in 2038, it's a 64-bit value. We'll use this; there's no need for millisecond resolution in a log file: events are sequential, clocks are unlikely to be that tightly synchronized, and network latencies mean that precise times aren't that meaningful.

We're up to 16 bytes, which is decent. Finally, we want to allow some additional data, formatted as text and depending on the type of event. Putting this all together gives the following message specification:

```
<class
    name = "zre_log_msg"
    script = "codec_c.gsl"
    signature = "2"
>
This is the ZRE logging protocol - raw version.
<include filename = "license.xml" />

<!-- Protocol constants -->
<define name = "VERSION" value = "1" />

<define name = "LEVEL_ERROR" value = "1" />
<define name = "LEVEL_WARNING" value = "2" />
<define name = "LEVEL_INFO" value = "3" />

<define name = "EVENT_JOIN" value = "1" />
<define name = "EVENT_LEAVE" value = "2" />
<define name = "EVENT_ENTER" value = "3" />
<define name = "EVENT_EXIT" value = "4" />

<message name = "LOG" id = "1">
    <field name = "level" type = "number" size = "1" />
    <field name = "event" type = "number" size = "1" />
    <field name = "node" type = "number" size = "2" />
    <field name = "peer" type = "number" size = "2" />
    <field name = "time" type = "number" size = "8" />
    <field name = "data" type = "string" />
Log an event
</message>

</class>
```

This generates 800 lines of perfect binary codec (the `zre_log_msg` class). The codec does protocol assertions just like the main ZRE protocol does. Code generation has a fairly steep starting curve, but it makes it so much easier to push your designs past "amateur" into "professional".



| [Content Distribution](http://zguide.zeromq.org/page:all#Content-Distribution) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-264) [next](http://zguide.zeromq.org/page:all#header-266) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

We now have a robust framework for creating groups of nodes, letting them chat to each other, and monitoring the resulting network. Next step is to allow them to distribute content as files.

As usual, we'll aim for the very simplest plausible solution and then improve that step-by-step. At the very least we want the following:

- An application can tell the Zyre API, "Publish this file", and provide the path to a file that exists somewhere in the file system.
- Zyre will distribute that file to all peers, both those that are on the network at that time, and those that arrive later.
- Each time an interface receives a file it tells its application, "Here is this file".

We might eventually want more discrimination, e.g., publishing to specific groups. We can add that later if it's needed. In [Chapter 7 - Advanced Architecture using ZeroMQ](http://zguide.zeromq.org/page:all#advanced-architecture) we developed a file distribution system (FileMQ) designed to be plugged into ZeroMQ applications. So let's use that.

Each node is going to be a file publisher and a file subscriber. We bind the publisher to an ephemeral port (if we use the standard FileMQ port 5670, we can't run multiple interfaces on one box), and we broadcast the publisher's endpoint in the `HELLO` message, as we did for the log collector. This lets us interconnect all nodes so that all subscribers talk to all publishers.

We need to ensure that each node has its own directory for sending and receiving files (the outbox and the inbox). Again, it's so we can run multiple nodes on one box. Because we already have a unique ID per node, we just use that in the directory name.

Here's how we set up the FileMQ API when we create a new interface:

sprintf (self->fmq_outbox, ".outbox/%s", self->identity);
mkdir (self->fmq_outbox, 0775);

sprintf (self->fmq_inbox, ".inbox/%s", self->identity);
mkdir (self->fmq_inbox, 0775);

self->fmq_server = fmq_server_new ();
self->fmq_service = fmq_server_bind (self->fmq_server, "tcp://*:*");
fmq_server_publish (self->fmq_server, self->fmq_outbox, "/");
fmq_server_set_anonymous (self->fmq_server, true);
char publisher [32];
sprintf (publisher, "tcp://%s:%d", self->host, self->fmq_service);
zhash_update (self->headers, "X-FILEMQ", strdup (publisher));

*//  Client will connect as it discovers new nodes*
self->fmq_client = fmq_client_new ();
fmq_client_set_inbox (self->fmq_client, self->fmq_inbox);
fmq_client_set_resync (self->fmq_client, true);
fmq_client_subscribe (self->fmq_client, "/");

And when we process a `HELLO` command, we check for the `X-FILEMQ` header field:

*//  If peer is a FileMQ publisher, connect to it*
char *publisher = zre_msg_headers_string (msg, "X-FILEMQ", NULL);
**if** (publisher)
`    `fmq_client_connect (self->fmq_client, publisher);

The last thing is to expose content distribution in the Zyre API. We need two things:

- A way for the application to say, "Publish this file"
- A way for the interface to tell the application, "We received this file".

In theory, the application can publish a file just by creating a symbolic link in the outbox directory, but as we're using a hidden outbox, this is a little difficult. So we add an API method `publish`:

*//  Publish file into virtual space*
void
zre_interface_publish (zre_interface_t *self,
`                       `char *filename, char *external)
{
`    `zstr_sendm (self->pipe, "PUBLISH");
`    `zstr_sendm (self->pipe, filename);`  `*//  Real file name*
`    `zstr_send`  `(self->pipe, external);`  `*//  Location in virtual space*
}

The API passes this to the interface thread, which creates the file in the outbox directory so that the FileMQ server will pick it up and broadcast it. We could literally copy file data into this directory, but because FileMQ supports symbolic links, we use that instead. The file has a ".ln" extension and contains one line, which contains the actual pathname.

Finally, how do we notify the recipient that a file has arrived? The FileMQ `fmq_client` API has a message, "DELIVER", for this, so all we have to do in `zre_interface` is grab this message from the `fmq_client` API and pass it on to our own API:

zmsg_t *msg = fmq_client_recv (fmq_client_handle (self->fmq_client));
zmsg_send (&msg, self->pipe);

This is complex code that does a lot at once. But we're only at around 10K lines of code for FileMQ and Zyre together. The most complex Zyre class, `zre_interface`, is 800 lines of code. This is compact. Message-based applications do keep their shape if you're careful to organize them properly.



| [Writing the Unprotocol](http://zguide.zeromq.org/page:all#Writing-the-Unprotocol) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-265) [next](http://zguide.zeromq.org/page:all#header-267) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

We have all the pieces for a formal protocol specification and it's time to put the protocol on paper. There are two reasons for this. First, to make sure that any other implementations talk to each other properly. Second, because I want to get an official port for the UDP discovery protocol and that means doing the paperwork.

Like all the other unprotocols we developed in this book, the protocol lives on [the ZeroMQ RFC site](http://rfc.zeromq.org/spec:20). The core of the protocol specification is the ABNF grammar for the commands and fields:

```
zre-protocol    = greeting *traffic

greeting        = S:HELLO
traffic         = S:WHISPER
                / S:SHOUT
                / S:JOIN
                / S:LEAVE
                / S:PING R:PING-OK

;   Greet a peer so it can connect back to us
S:HELLO         = header %x01 ipaddress mailbox groups status headers
header          = signature sequence
signature       = %xAA %xA1
sequence        = 2OCTET        ; Incremental sequence number
ipaddress       = string        ; Sender IP address
string          = size *VCHAR
size            = OCTET
mailbox         = 2OCTET        ; Sender mailbox port number
groups          = strings       ; List of groups sender is in
strings         = size *string
status          = OCTET         ; Sender group status sequence
headers         = dictionary    ; Sender header properties
dictionary      = size *key-value
key-value       = string        ; Formatted as name=value

; Send a message to a peer
S:WHISPER       = header %x02 content
content         = FRAME         ; Message content as ZeroMQ frame

; Send a message to a group
S:SHOUT         = header %x03 group content
group           = string        ; Name of group
content         = FRAME         ; Message content as ZeroMQ frame

; Join a group
S:JOIN          = header %x04 group status
status          = OCTET         ; Sender group status sequence

; Leave a group
S:LEAVE         = header %x05 group status

; Ping a peer that has gone silent
S:PING          = header %06

; Reply to a peer's ping
R:PING-OK       = header %07
```



| [Example Zyre Application](http://zguide.zeromq.org/page:all#Example-Zyre-Application) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-266) [next](http://zguide.zeromq.org/page:all#header-268) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Let's now make a minimal example that uses Zyre to broadcast files around a distributed network. This example consists of two programs:

- A *listener* that joins the Zyre network and reports whenever it receives a file.
- A *sender* that joins a Zyre network and broadcasts exactly one file.

The listener is quite short:

\#include <zre.h>

int main (int argc, char *argv [])
{
`    `zre_interface_t *interface = zre_interface_new ();
`    `**while** (true) {
`        `zmsg_t *incoming = zre_interface_recv (interface);
`        `**if** (!incoming)
`            `**break**;
`        `zmsg_dump (incoming);
`        `zmsg_destroy (&incoming);
`    }`
`    `zre_interface_destroy (&interface);
`    `**return** 0;
}

And the sender isn't much longer:

\#include <zre.h>

int main (int argc, char *argv [])
{
`    `**if** (argc < 2) {
`        `puts ("Syntax: sender filename virtualname");
`        `**return** 0;
`    }`
`    `printf ("Publishing %s as %s**\n**", argv [1], argv [2]);
`    `zre_interface_t *interface = zre_interface_new ();
`    `zre_interface_publish (interface, argv [1], argv [2]);
`    `**while** (true) {
`        `zmsg_t *incoming = zre_interface_recv (interface);
`        `**if** (!incoming)
`            `**break**;
`        `zmsg_dump (incoming);
`        `zmsg_destroy (&incoming);
`    }`
`    `zre_interface_destroy (&interface);
`    `**return** 0;
}



| [Conclusions](http://zguide.zeromq.org/page:all#Conclusions) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-267) [next](http://zguide.zeromq.org/page:all#header-269) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

Building applications for unstable decentralized networks is one of the end games for ZeroMQ. As the cost of computing falls every year, such networks become more and more common, be it consumer electronics or virtual boxes in the cloud. In this chapter, we've pulled together many of the techniques from the book to build Zyre, a framework for proximity computing over a local network. Zyre isn't unique; there are and have been many attempts to open this area for applications: ZeroConf, SLP, SSDP, UPnP, DDS. But these all seem to end up too complex or otherwise too difficult for application developers to build on.

Zyre isn't finished. Like many of the projects in this book, it's an ice breaker for others. There are some major unfinished areas, which we may address in later editions of this book or versions of the software.

- High-level APIs: the message-based API that Zyre offers now is usable but still rather more complex than I'd like for average developers. If there's one target we absolutely cannot miss, it's raw *simplicity*. This means we should build high-level APIs, in lots of languages, which hide all the messaging, and which come down to simple methods like start, join/leave group, get message, publish file, stop.

- Security: how do we build a fully decentralized security system? We might be able to leverage public key infrastructure for some work, but that requires that nodes have their own Internet access, which isn't guaranteed. The answer is, as far as we can tell, to use any existing secure peer-to-peer link (TLS, BlueTooth, perhaps NFC) to exchange a session key and use a symmetric cipher. Symmetric ciphers have their advantages and disadvantages.

- Nomadic content: how do I, as a user, manage my content across multiple devices? The Zyre + FileMQ combination might help, for local network use, but I'd like to be able to do this across the Internet as well. Are there cloud services I could use? Is there something I could make using ZeroMQ?

- Federation: how do we scale a local-area distributed application across the globe? One plausible answer is federation, which means creating clusters of clusters. If 100 nodes can join together to create a local cluster, then perhaps 100 clusters can join together to create a wide-area cluster. The challenges are then quite similar: discovery, presence, and group messaging.



| [Postface](http://zguide.zeromq.org/page:all#Postface) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-268) [next](http://zguide.zeromq.org/page:all#header-270) |
| ------------------------------------------------------ | ------------------------------------------------------------ |
|                                                        |                                                              |



| [Tales from Out There](http://zguide.zeromq.org/page:all#Tales-from-Out-There) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-269) [next](http://zguide.zeromq.org/page:all#header-271) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

I asked some of the contributors to this book to tell us what they were doing with ZeroMQ. Here are their stories.



| [Rob Gagnon's Story](http://zguide.zeromq.org/page:all#Rob-Gagnon-s-Story) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-270) [next](http://zguide.zeromq.org/page:all#header-272) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

"We use ZeroMQ to assist in aggregating thousands of events occurring every minute across our global network of telecommunications servers so that we can accurately report and monitor for situations that require our attention. ZeroMQ made the development of the system not only easier, but faster to develop and more robust and fault-tolerant than we had originally planned in our original design.

"We're able to easily add and remove clients from the network without the loss of any message. If we need to enhance the server portion of our system, we can stop and restart it as well without having to worry about stopping all of the clients first. The built-in buffering of ZeroMQ makes this all possible."



| [Tom van Leeuwen's Story](http://zguide.zeromq.org/page:all#Tom-van-Leeuwen-s-Story) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-271) [next](http://zguide.zeromq.org/page:all#header-273) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

"I was looking at creating some kind of service bus connecting all kinds of services together. There were already some products that implemented a broker, but they did not have the functionality I needed. By accident, I stumbled upon ZeroMQ, which is awesome. It's very lightweight, lean, simple and easy to follow because the guide is very complete and reads very well. I've actually implemented the Titanic pattern and the Majordomo broker with some additions (client/worker authentication and workers sending a catalog explaining what they provide and how they should be addressed).

"The beautiful thing about ZeroMQ is the fact that it is a library and not an application. You can mold it however you like and it simply puts boring things like queuing, reconnecting, TCP sockets and such to the background, making sure you can concentrate on what is important to you. I've implemented all kinds of workers/clients and the broker in Ruby, because that is the main language we use for development, but also some PHP clients to connect to the bus from existing PHP webapps. We use this service bus for cloud services, connecting all kinds of platform devices to a service bus exposing functionality for automation.

"ZeroMQ is very easy to understand and if you spend a day with the guide, you'll have good knowledge of how it works. I'm a network engineer, not a software developer, but managed to create a very nice solution for our automation needs! ZeroMQ: Thank you very much!"



| [Michael Jakl's Story](http://zguide.zeromq.org/page:all#Michael-Jakl-s-Story) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-272) [next](http://zguide.zeromq.org/page:all#header-274) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

"We use ZeroMQ for distributing millions of documents per day in our distributed processing pipeline. We started out with big message queuing brokers that had their own respective issues and problems. In the quest of simplifying our architecture, we chose ZeroMQ to do the wiring. So far it had a huge impact in how our architecture scales and how easy it is to change and move the components. The plethora of language bindings lets us choose the right tool for the job without sacrificing interoperability in our system. We don't use a lot of sockets (less than 10 in our whole application), but that's all we needed to split a huge monolithic application into small independent parts.

"All in all, ZeroMQ lets me keep my sanity and helps my customers stay within budget."



| [Vadim Shalts's Story](http://zguide.zeromq.org/page:all#Vadim-Shalts-s-Story) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-273) [next](http://zguide.zeromq.org/page:all#header-275) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

"I am team leader in the company ActForex, which develops software for financial markets. Due to the nature of our domain, we need to process large volumes of prices quickly. In addition, it's extremely critical to minimize latency in processing orders and prices. Achieving a high throughput is not enough. Everything must be handled in a soft real time with a predictable ultra low latency per price. The system consists of multiple components exchanging messages. Each price can take a lot of processing stages, each of which increases total latency. As a consequence, low and predictable latency of messaging between components becomes a key factor of our architecture.

"We investigated different solutions to find something suitable for our needs. We tried different message brokers (RabbitMQ, ActiveMQ Apollo, Kafka), but failed to reach a low and predictable latency with any of them. In the end, we chose ZeroMQ used in conjunction with ZooKeeper for service discovery. Complex coordination with ZeroMQ requires a relatively large effort and a good understanding, as a result of the natural complexity of multithreading. We found that an external agent like ZooKeeper is better choice for service discovery and coordination while ZeroMQ can be used primarily for simple messaging. ZeroMQ fit perfectly into our architecture. It allowed us to achieve the desired latency using minimal efforts. It saved us from a bottleneck in the processing of messages and made processing time very stable and predictable.

"I can decidedly recommend ZeroMQ for solutions where low latency is important."



| [How This Book Happened](http://zguide.zeromq.org/page:all#How-This-Book-Happened) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-274) [next](http://zguide.zeromq.org/page:all#header-276) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

When I set out to write a ZeroMQ book, we were still debating the pros and cons of forks and pull requests in the ZeroMQ community. Today, for what it's worth, this argument seems settled: the "liberal" policy that we adopted for `libzmq` in early 2012 broke our dependency on a single prime author, and opened the floor to dozens of new contributors. More profoundly, it allowed us to move to a gently organic evolutionary model that was very different from the older forced-march model.

The reason I was confident this would work was that our work on the Guide had, for a year or more, shown the way. True, the text is my own work, which is perhaps as it should be. Writing is not programming. When we write, we tell a story and one doesn't want different voices telling one tale; it feels strange.

For me the real long-term value of the book is the repository of examples: about 65,000 lines of code in 24 different languages. It's partly about making ZeroMQ accessible to more people. People already refer to the Python and PHP example repositories—two of the most complete—when they want to tell others how to learn ZeroMQ. But it's also about learning programming languages.

Here's a loop of code in Tcl:

**while** **{**1**}** **{**
`    `*# Process all parts of the message*
`    `zmq message message
`    `frontend recv_msg message
`    `**set** more **[**frontend getsockopt RCVMORE**]**
`    `backend send_msg message **[expr** **{**$more?"SNDMORE":""**}]**
`    `message close
`    `**if** **{**!$more**}** **{**
`        `**break** **;** *# Last message part*
`    `**}**
**}**

And here's the same loop in Lua:

**while** **true** **do**
`    `*--  Process all parts of the message*
`    `**local** msg = frontend:recv()
`    `**if** (frontend:getopt(zmq.RCVMORE) == 1) **then**
`        `backend:send(msg, zmq.SNDMORE)
`    `**else**
`        `backend:send(msg, 0)
`        `**break**;`      `*--  Last message part*
`    `**end**
**end**

And this particular example (`rrbroker`) exists in C#, C++, CL, Clojure, Erlang, F#, Go, Haskell, Haxe, Java, Lua, Node.js, Perl, PHP, Python, Ruby, Scala, Tcl, and of course C. This code base, all provided as open source under the MIT/X11 license, may form the basis for other books or projects.

But what this collection of translations says most profoundly is this: the language you choose is a detail, even a distraction. The power of ZeroMQ lies in the patterns it gives you and lets you build, and these transcend the comings and goings of languages. My goal as a software and social architect is to build structures that can last generations. There seems no point in aiming for mere decades.



| [Removing Friction](http://zguide.zeromq.org/page:all#Removing-Friction) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-275) [next](http://zguide.zeromq.org/page:all#header-277) |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

I'll explain the technical tool chain we used in terms of the friction we removed. In this book we're telling a story and the goal is to reach as many people as possible, as cheaply and smoothly as we can.

The core idea was to host the text and examples on GitHub and make it easy for anyone to contribute. It turned out to be more complex than that, however.

Let's start with the division of labor. I'm a good writer and can produce endless amounts of decent text quickly. But what was impossible for me was to provide the examples in other languages. Because the core ZeroMQ API is in C, it seemed logical to write the original examples in C. Also, C is a neutral choice; it's perhaps the only language that doesn't create strong emotions.

How to encourage people to make translations of the examples? We tried a few approaches and finally what worked best was to offer a "choose your language" link on every single example in the text, which took people either to the translation or to a page explaining how they could contribute. The way it usually works is that as people learn ZeroMQ in their preferred language, they contribute a handful of translations or fixes to the existing ones.

At the same time, I noticed a few people quite determinedly translating *every single*example. This was mainly binding authors who realized that the examples were a great way to encourage people to use their bindings. For their efforts, I extended the scripts to produce language-specific versions of the book. Instead of including the C code, we'd include the Python, or PHP code. Lua and Haxe also got their dedicated versions.

Once we have an idea of who works on what, we know how to structure the work itself. It's clear that to write and test an example, what you want to work on is *source code*. So we import this source code when we build the book, and that's how we make language-specific versions.

I like to write in a plain text format. It's fast and works well with source control systems like git. Because the main platform for our websites is Wikidot, I write using Wikidot's very readable markup format.

At least in the first chapters, it was important to draw pictures to explain the flow of messages between peers. Making diagrams by hand is a lot of work, and when we want to get final output in different formats, image conversion becomes a chore. I started with Ditaa, which turns text diagrams into PNGs, then later switched to asciitosvg, which produces SVG files, which are rather better. Since the figures are text diagrams, embedded in the prose, it's remarkably easy to work with them.

By now you'll realize that the toolchain we use is highly customized, though it uses a lot of external tools. All are available on Ubuntu, which is a mercy, and the whole custom toolchain is in the zguide repository in the bin subdirectory.

Let's walk through the editing and publishing process. Here is how we produce the online version:

```
bin/buildguide
```

Which works as follows:

- The original text sits in a series of text files (one per chapter).
- The examples sit in the examples subdirectory, classified per language.
- We take the text and process this using a custom Perl script, mkwikidot, into a set of Wikidot-ready files.
- We do this for each of the languages that get their own version.
- We extract the graphics and call asciitosvg and rasterize on each one to produce image files, which we store in the images subdirectory.
- We extract inline listings (which are not translated) and stores these in the listings subdirectory.
- We use pygmentize on each example and listing to create a marked-up page in Wikidot format.
- We upload all changed files to the online wiki using the Wikidot API.

Doing this from scratch takes a while. So we store the SHA1 signatures of every image, listing, example, and text file, and only process and upload changes, and that makes it easy to publish a new version of the text when people make new contributions.

To produce the PDF and Epub formats, we do the following:

```
bin/buildpdfs
```

Which works as follows:

- We use the custom mkdocbook Perl program on the input files to produce a DocBook output.
- We push the DocBook format through docbook2ps and ps2pdf to create clean PDFs in each language.
- We push the DocBook format through db2epub to create Epub books and in each language.
- We upload the PDFs to the public wiki using the Wikidot API.

When creating a community project, it's important to lower the "change latency", which is the time it takes for people to see their work live or, at least, to see that you've accepted their pull request. If that is more than a day or two, you've often lost your contributor's interest.



| [Licensing](http://zguide.zeromq.org/page:all#Licensing) | [top](http://zguide.zeromq.org/page:all#top) [prev](http://zguide.zeromq.org/page:all#header-276) [next](http://zguide.zeromq.org/page:all#header-278) |
| -------------------------------------------------------- | ------------------------------------------------------------ |
|                                                          |                                                              |

I want people to reuse this text in their own work: in presentations, articles, and even other books. However, the deal is that if they remix my work, others can remix theirs. I'd like credit, and have no argument against others making money from their remixes. Thus, the text is licensed under cc-by-sa.

For the examples, we started with GPL, but it rapidly became clear this wasn't workable. The point of examples is to give people reusable code fragments so they will use ZeroMQ more widely, and if these are GPL, that won't happen. We switched to MIT/X11, even for the larger and more complex examples that conceivably would work as LGPL.

However, when we started turning the examples into standalone projects (as with Majordomo), we used the LGPL. Again, remixability trumps dissemination. Licenses are tools; use them with intent, not ideology.





[Help](http://www.wikidot.com/doc)  | [Terms of Service](http://www.wikidot.com/legal:terms-of-service)  | [Privacy](http://www.wikidot.com/legal:privacy-policy)  | [Report a bug](javascript:;)  | [Flag as objectionable](javascript:;)

Powered by 