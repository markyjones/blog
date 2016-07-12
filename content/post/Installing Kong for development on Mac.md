+++
date = "2016-06-26T11:00:01+01:00"
draft = false
title = "Installing Kong for development on Mac"

+++

By far the easiest way I have found to get Mashape's Kong installed for is [via Docker](https://github.com/Mashape/docker-kong). This takes seconds and is about as pain free any install can get. How about installing to develop Kong though?

I began following the excellent HelloWorld plugin guide [here](http://streamdata.io/blog/developing-an-helloworld-kong-plugin/) and I found that the environment setup steps in the article a little light and although I have seen Vagrent scripts [here](https://github.com/Mashape/kong-vagrant/blob/master/README.md) and other guides like [this](https://gist.github.com/nijikokun/189f84226f092dccd359) Mac OS installer script I believe the best route - as with most Mac installs - was directly via the Brew install approach [here](https://github.com/Mashape/homebrew-kong/blob/master/README.md).

Marco Palladino (aka thefosk and founder of Mashape) hosts a gist for Linux and Mac installation [here](https://gist.github.com/thefosk/75d2d718ceba66169d0e ) but if you look at the script it essentially just uses Brew if OSX is detected there is also a detailed debugging on Kong guide [here](http://lua-programming.blogspot.co.uk/2015/12/how-to-debug-kong-plugins-on-windows.html?m=1) which covers using this [Brew](https://github.com/thibaultCha/homebrew-kong/blob/master/README.md) when using OSX although using the ``$ brew install kong --with-cassandra`` option.

So I found that (presuming you have openssl installed via Brew)

```
$ brew update
$ brew tap mashape/kong
$ brew install kong
```

Blows up with

> Error: Failed installing dependency: https://luarocks.org/luacrypto-0.3.2-1.src.rock - Could not find header file for OPENSSL
> No file openssl/evp.h in /usr/local/include
> No file openssl/evp.h in /usr/include
> You may have to install OPENSSL in your system and/or pass OPENSSL_DIR or OPENSSL_INCDIR to the luarocks command.
> Example: luarocks install luacrypto OPENSSL_DIR=/usr/local

the ``make install`` build step failed due to an OpenSSL error... Some Googling later I came up with

```
$ brew link --force openssl
$ brew install kong
```

Only to notice it was **already** a known troubleshooting step(!) on the official brew [readme](https://github.com/Mashape/homebrew-kong) (note to self read the full install steps first!)

In order to start Kong I found that it was missing a Kong.yml locally

```
$ kong start
```

> [ERR] No configuration file at: /etc/kong/kong.yml

The script [here](http://m.blog.csdn.net/article/details?id=49895497) does solves this task e.g. Via symbolic link

```
$ sudo mkdir /etc/kong
$ sudo ln -s /usr/local/lib/luarocks/rocks/kong/0.8.3-0/conf/kong.yml /etc/kong/kong.yml
```

Or just clone it

```
$ sudo mkdir /etc/kong
$ sudo cp /usr/local/lib/luarocks/rocks/kong/0.8.3-0/conf/kong.yml /etc/kong/kong.yml
```

Start it again

```
$ kong start
```

> [ERR] All hosts tried for query failed. 127.0.0.1:9042: connection refused for socket with peer 127.0.0.1:9042.
> [ERR] Could not start Kong

If you don't have Cassandra running locally or remotely e.g. via docker then get it running and configure your kong.yml appropriately (I personally used docker).

Now Kong should start

```
$ kong start
$ curl http://localhost:8001
```

Having managed to now build and install Kong I was able to follow through the Kong [HelloWorld](http://streamdata.io/blog/developing-an-helloworld-kong-plugin/) plugin tutorial up to the point of testing. Running

```
$ git clone https://github.com/Mashape/kong.git
$ make test
```

gave me an error about `@busted` being missing...

> make: busted: No such file or directory
> make: *** [test] Error 1

a quick Google gave me a boy band…(Hmmm) after a head slap moment I realised it was a Lua module for unit testing [here](http://olivinelabs.com/busted/) and I simply needed to install it like so.

```
$ luarocks install busted
```

Next small hurdle is that the tests require a Kong_TEST.yml file in the root folder - simply cloning the existing Kong.yml file sufficed (assuming you are in the root of the source code)...

```
$ cp ./kong.yml ./kong_TEST.yml
```

> ●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●●
> 173 successes / 0 failures / 0 errors / 0 pending : 0.908241 seconds

Now I was up and running with compile, install and testing and could get on with the HelloWorld blog article and also look at the debuging in the other [article](http://lua-programming.blogspot.co.uk/2015/12/how-to-debug-kong-plugins-on-windows.html?m=1).
