---
layout: post
title: Getting a dd progress bar using pv
tags: [ dd, pv, shell scripting ]
---

I neeeded to use dd to blank two IDE disks I have here around. My first problem was to find where to plug those, because I have no box with an IDE bus. Fortunatelly I still keep a FastTrack IDE to PCI board which I could to plug those disks.

After checking their contents I decide to blank them with zeros. I thought, something like this would do the trick:

{% raw %}
       dd if=/dev/zero of=/dev/ada1 bs=1M
{% endraw %}

This command is for FreeBSD, with Linux I'd just replace the disk:

{% raw %}
       dd if=/dev/zero of=/dev/sdb bs=1M
{% endraw %}

However, there's a problem, **dd** does not show any progress bar. And so there you are, waiting for the dd command to finish. Here comes **pv** to the rescue. We can pipe the output of the dd command through pv and then get a progress bar. The output of pv can be piped to another dd instance and write those zerost to the disk.

{% raw %}
       dd if=/dev/zero bs=1M | pv | dd of=/dev/ada1
{% endraw %}

With no commands pv will output the amount of information written (using the best unit, K, M, G) and the speed. 

In case we wanted a real progress bar (which will go from 0 to 100%) or an ETA pv would need to know the amount of informatio to be written. In my example, I needed to get the raw space of the target disk, and **gpart** provides that information.

{% raw %}
       dd if=/dev/zero bs=1M | pv --size `gpart list ada1|tail -n 4| grep Mediasize| cut -f 1 -d "(" | cut -f 2 -d ":"`| dd of=/dev/ada1
{% endraw %}

On Linux I'd do something like this (untested):

{% raw %}
       dd if=/dev/zero bs=1M | pv --size `cat /proc/partition | grep "sdb$" |cut -f 3 -d " "` | dd of=/dev/ada1
{% endraw %}

We would get this:

{% raw %}
       18,6GB 0:29:11 [20,3MB/s]] [=====>                       ] 25% ETA 1:21:31
{% endraw %}

Hope you enjoy this command.
