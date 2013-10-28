# The results of a ~~9~~ 10 day project

## Foreword (or tl;dr)

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

This was ran locally on a quad-core 2.3ghz i7 late-2012 Mac mini, with the bottleneck being my 1mbps upstream (yeah, *that* slow). That's 8 workers, sending 100 messages each.

The type field specifies the number of recipients a message:

* `1 on 1` would be the regular conversation two people have.
* `1 on 2` would be someone dividing risks when asking out 2 girls for the movies.
* `1 on 5` would be an average group conversation.
* `1 on *` is the whole spectrum (including massive spammers) as it's random from 1 to N.
* `1 on N` is stress testing.

I'd say this numbers are pretty sweet –*even thou there's a lot of room for improvement*.