---
layout: post
title: "Installing node.js on a Raspberry Pi"
date: 2013-04-12 10:00
comments: true
categories:
- Raspberry Pi
- node.js
---
It's easy to install node.js on a Raspberry Pi and makes for a great lightweight webserver to serve as an interface for web projects.

Before installing node you'll need to install build-essential (and git-core if you are going to use git for version control).

{% highlight bash %}
sudo aptitude install build-essential git-core
{% endhighlight %}

I went with v0.11.0 of node, but you can check out the versions available [here](http://nodejs.org/dist/).
Building from source took a couple of hours, but the install step was relatively quick.

{% highlight bash %}
wget http://nodejs.org/dist/v0.11.0/node-v0.11.0.tar.gz
tar xzf node-v0.11.0.tar.gz
cd node-v0.11.0.tar.gz
./configure
make
sudo make install
{% endhighlight %}

When node is finally installed, you can create a simple app to check everything is working correctly.

{% highlight js %}
var http = require('http');
http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('It works!\n');
}).listen(8124, "127.0.0.1");
console.log('Server running at http://127.0.0.1:8124/');
{% endhighlight %}

Firing up your browser and hitting [http://127.0.0.1:8124/](http://127.0.0.1:8124/) should give you a page with the message "It works!"
Or you can quickly verify with curl:

{% highlight bash %}
% curl http://127.0.0.1:8124
It works!
{% endhighlight %}
