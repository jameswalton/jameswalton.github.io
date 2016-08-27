---
layout: post
title: "Installing Ruby 2 and RVM on a Raspberry Pi"
date: 2013-03-03 23:06
comments: true
categories:
- Ruby
- RVM
- Raspberry Pi
---
I need to run some Ruby scripts on my Raspberry Pi, so I decided to go about installing Ruby using RVM.
After booting up my Pi, I connected to it via SSH and proceeded to install Git, RVM and Ruby.

Before you start, the installation of RVM and Ruby look almost 2 hours on my Pi, so be prepared for a long wait.

* Boot up your Raspberry Pi

* Install Git and dependencies on your Pi
{% highlight bash %}
sudo aptitude update
sudo aptitude install git-core
{% endhighlight %}


* Install RVM and the latest stable version of Ruby. At the time of writing, this was ruby-2.0.0-p0.
{% highlight bash %}
curl -L https://get.rvm.io | bash -s stable --ruby
{% endhighlight %}

Almost two hours later, RVM and Ruby 2.0.0 is installed.

To load RVM into the current shell you'll need to run the following command, relative to your install

{% highlight bash %}
pi@raspberrypi ~ $ source /home/pi/.rvm/scripts/rvm
pi@raspberrypi ~ $ ruby -v
ruby 2.0.0p0 (2013-02-24 revision 39474) [armv6l-linux-eabihf]
{% endhighlight %}
