---
title: "Testing Argus"
date: 2018-10-02T10:54:33-05:00
---


{{% notice warning %}}
Performance testing is an advanced feature. If you are just starting out with argus it is recommended that you hit your back button and skip this until you are more familiar with using Argus.
{{% /notice %}}

As you use argus for some time, and add more and more things to be monitored, it is natural to wonder how well argus can handle the number of things you are throwing at it, or whether it is time to upgrade to a faster server.

As you monitor more and more, argus will use more memory and more cpu. Standard tools such as 'ps' and 'top' are useful for this. But you're already running the most advanced monitoring system in the world, why not have argus monitor itself?

Argus provides 2 interfaces to various bits of internal performance data:

## argusctl status

Running the command <tt>argusctl status</tt> will display information about the running argus:

<pre>    ARGUS/2.0 200 OK
    status:   running
    version:  3.2
    objects:  312
    notifies: 294
    services: 198
    uptime:   0:28:40
    idle:     92.47% 95.28% 95.44%
    monrate:  12.77 10.21 10.40 per second
</pre>

Most interesting are the percent idle (the above argus is doing very little work and quite idle), and the monrate, the number of tests argus is doing per second. These are both given as 1, 5, and 15 minute averages.

## argusctl self

Running the command <tt>argusctl self</tt> will display additional information about the running argus:

<pre>    200 OK
cpurate:           0.004958954098553818
cpurate15:         0.0051087599999999995
cpurate5:          0.005105664717112987
currfd:            14
darpgraphdrops:    0
darpgraphqueue:    0
goroutines:        142
idlerate:          0.9950410459014464
idlerate15:        0.9948912400000002
idlerate5:         0.9948943352828871
mem.alloc:         17047992
mem.stack:         2260992
mem.sys:           44194832
monrate:           37.304954657802796
monrate15:         37.140000000000015
monrate5:          37.135964140187056
notifies:          0
objects:           726
pingidle:          8
pingqueue:         16
resolvdrops:       0
resolvidle:        2
resolvqueries:     160
resolvqueue:       0
resolvtimeouts:    0
runs:              13274
runsexpress:       2440
runsprio:          10613
schedqueue:        0
services:          303
uptime:            350
workdefer:         0
workdeferexpress:  0
workdeferprio:     2230
workidle:          112
workqueue:         0
workqueueexpress:  0
workqueueprio:     0
</pre>

## Service Self ...

Argus also exports various data to itself as a Service, so you can do testing and graphing. Some useful examples:

<pre>    Group "Myself" {
        graph: yes
        Service Self/idlerate {
            title:  Percent Idle
            expr:  x * 100
            # let someone know when it is time to upgrade h/w
            minvalue:  20
            messagedn: time to buy faster server
        }

        Service Self/monrate {
            title:  Monitoring Rate
            ylabel: tests per second
            calc:   ave-rate
        }

        Service Prog {
            # how much memory is argus using?
            # your ps may be different
            command:        ps -p $ARGUS_PID -o vsz | tail -1
            uname:          VSZ
            title:          Memory Use
            ylabel:         kBytes
        }
    }
</pre>

