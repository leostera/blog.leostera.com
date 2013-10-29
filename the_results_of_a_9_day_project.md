# The results of a ~~9~~ 10 day project

## Foreword / Results (or tl;dr)

9 days have passed since I started working on Message.Me –to be fair, it's been about 17, but I've only worked 9 on it: the first 7 and the last 2.

It was my first attempt at a scalable, fail-over capable, hybrid HTTP/WS messaging service. And I think I did ok, considering I had to build a bunch of packages for the frontend and all, and the benchmark worker was strictly trying to replicate a user, I got:

##### Measures for POST (while still doing socket.send):

| Type   | Cores | Request  | Total Time | Avg Lat | Med Lat | Min Lat | Max Lat |
| ------ | ----- | -------- | ---------- | ------- | ------- | ------- | ------- |
| 1 on 1 | 8     | 800      | 11s        | 27.7ms  | 27ms    | 9ms     | 144ms   |
| 1 on 2 | 8     | 1600     | 14.3s      | 33.9ms  | 31ms    | 8ms     | 142ms   |
| 1 on 5 | 8     | 4000     | 25.3s      | 64.6ms  | 42ms    | 7ms     | 244ms   |
| 1 on * | 8     | 5900     | 36.1s      | 92.5s   | 46ms    | 10ms    | 336s    |
| 1 on N | 8     | 10100    | 1m15s      | 102.9s  | 47ms    | 10ms    | 438s    |

##### Full roundtrip measurements (From POST to socket.send)

| Type   | Cores | Messages | Total Time | Avg Lat | Med Lat | Min Lat | Max Lat |
| ------ | ----- | -------- | ---------- | ------- | ------- | ------- | ------- |
| 1 on 1 | 8     | 2200     | 1m31s      | 12.6s   | 23s     | 3ms     | 84s     |
| 1 on 2 | 8     | 2430     | 1m54s      | 10.3s   | 29ms    | 3ms     | 105s    |
| 1 on 5 | 8     | 4150     | 2m2s       | 2.7s    | 46ms    | 8ms     | 135s    |
| 1 on * | 8     | 4150     | 2m2s       | 2.7s    | 46ms    | 8ms     | 135s    |
| 1 on N | 8     | 8200     | 3m20s      | 16.5s   | 33ms    | 6ms     | 208s    |

This was ran locally on a quad-core 2.3ghz i7 late-2012 Mac mini, with the bottleneck being my 1mbps upstream (yeah, *that* slow). That's 8 workers, sending 100 messages to each other.

The type field specifies the number of recipients a message:

* `1 on 1` would be the regular conversation two people have.
* `1 on 2` would be someone dividing risks when asking out 2 girls for the movies.
* `1 on 5` would be an average group conversation.
* `1 on *` is the whole spectrum (including massive spammers) as it's random from 1 to N.
* `1 on N` is stress testing by using all online users.

I'd say this numbers are pretty sweet –*even thou there's a lot of room for improvement*.

## Motivation

It all started when a friend of mine, Omar Siddiqui – [@buctik](https://twitter.com/buctik), asked me to build a messaging app that used web sockets. It really just started there. `0.1.0` of `ng2` was just baked and it felt the right thing to do to try it out, build some modules and just have some fun with it.

I should tell, I'm a big picture guy. I take a little idea and it's fairly easy for me to see something way, way bigger than what was presented. This doesn't mean my big picture is worth a shot, or even accurate at all, but it's the way I see things I guess. And I thought

> hey, what if actually made a real-time messaging app leveraging aws that could send like a fudge load of messages and support sockets failover recovery and all whistles and bells, and have an angularjs powered client to make it even more awesome

So yeah, I'm motivated by making awesome systems. 

##### Disclaimer: I suck at UI.

## Implementation

So the first day started and I think I worked only on the client. I spent a lot of time inspecting the ins and outs of the `OAuth2` protocol, and how `AWS SQS` really works. I think I was crunching about 1.something kLOC a day.

I committed the baddest mistake: I didn't test.

And I committed an even worse one: the server was a piece of shit. 

Amongst other things I managed to:

* architect the system on `AWS`
* make updates to `ng2`
* craft 5 modules for angular: `loading`, `auth`, `auth-facebook`, `debug` and `ws`
* make a very simple `dependency injector` for node based on angular's one
* make the first db design draft
* a little `cors middleware` for connect
* design the UI twice –I'm bad, thou working on it :)
* devops

And learned some CSS3 animations on the way.

#### But what's `ng2`?
Please, have a read [here](http://ng2.github.io)
#### Where are all those angular modules?
They are [here](https://github.com/ng2)