---
layout: post
title:  "Vagrant port forwarding"
date:   2015-08-29 14:05:00
category: macosx
css: test/solarized-dark.css
tagline: "DevNotes"
tags : [vagrant, virtualbox, port forwarding, macosx]
email : eim@protonmail.ch
github : eim
author: Eugene Mukhanov
---
{% include JB/setup %}


### Home pages:
+ [VirtualBox home page](https://www.virtualbox.org)
+ [Vagrant home page](https://www.vagrantup.com)
+ [Rsync home page](https://rsync.samba.org)
+ [Lsyncd home page](https://github.com/axkibe/lsyncd)

## Overview

In order to improve the speed of file transfer between MacOS X host and VirtualBox VM is managed by vagrant I'm using lsyncd + rsync. Port numbers less than 1024 are forbidden.
It's very easy to forward port 873 to 1873.

### Using

Install vagrant plugin:

{% highlight bash %}
vagrant plugin install vagrant-triggers  
{% endhighlight %}

Add command into configuraton:

{% highlight ruby %}
config.trigger.after [:up, :reload, :provision], :stdout => do
system('echo "rdr pass inet proto tcp from any to any port 873 -> 1873" | sudo pfctl -ef - >/dev/null 2>&1; echo "Add Port Forwarding (873->1873)"')
end
{% endhighlight %}

Define ip address

{% highlight ruby %}
config.trigger.after [:up, :reload, :provision], :stdout => do
system('echo "rdr pass inet proto tcp from any to 127.0.0.1 port 873 -> 127.0.0.1 port 1873" | sudo pfctl -ef - >/dev/null 2>&1')
end
{% endhighlight %}

Multiline

{% highlight ruby %}
config.trigger.after [:up, :reload, :provision], :stdout => do
system('echo "
rdr pass inet proto tcp from any to 127.0.0.1 port 873 -> 127.0.0.1 port 1873
rdr pass inet proto tcp from any to 127.0.0.1 port 443 -> 127.0.0.1 port 1443
" | sudo pfctl -ef - >/dev/null 2>&1')
end
{% endhighlight %}
