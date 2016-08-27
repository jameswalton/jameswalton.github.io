---
layout: post
title: "Mac OSX Notifications with Guard"
date: 2013-02-11 15:42
comments: true
categories:
- Guard
- RSpec
- notifications
- OSX
---
I've recently started using guard to monitor my rails application and run the appropriate tests. Guard is great, but alt-tabbing between text editor and terminal can be a bit of a time waster.
To alleviate this, users of Mountain Lion can have their RSpec test status displayed using the notification centre, in a similar fashion that Growl used to. This means you get regular feedback while still having focus on the text editor (and it even works in full screen mode!)

To get it working, add the 'terminal-notifier-guard' gem to the development group in your Gemfile.

{% highlight ruby %}
group :development do
  gem 'terminal-notifier-guard'
end
{% endhighlight %}

After that, install the gem in your project using: {% highlight bash %} bundle install {% endhighlight %}

The next time you fire up guard you should see something simlar to the following which lets you know that TerminalNotifier is sending notifications.

{% highlight bash %}
James-iMac% guard
15:55:35 - INFO - Guard uses TerminalNotifier to send notifications.
15:55:35 - INFO - Guard uses TerminalTitle to send notifications.
15:55:36 - INFO - Guard::RSpec is running
15:55:36 - INFO - Running all specs
... spec output ...
15:55:41 - INFO - Guard is now watching at '/Users/james/rails/blog'
{% endhighlight %}

![RSpec results using OSX Notifications](/img/guard-rspec-osx-notification.png)

This should be the end result - short and sweet RSpec notifications.
