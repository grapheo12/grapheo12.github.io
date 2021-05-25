+++
title = "Lamport Clocks"
date = 2021-05-25

[taxonomies]
tags = ["distributed systems", "logical clocks"]
+++

# Problem with Physical Clocks

There are a lot of good stuff that a wall clock does.
For example, telling you the time. :-)

But when it comes to ordering a set of events,
a simple wall clock will not be sufficient.
Well, it is sufficient, if all of us follow the same clock.
But in a distributed sense it is often not the case.

<!-- more -->

Consider the following (somewhat phoney) example:

Ram and Shyam meet at Howrah Station
and they sync their clocks with the station clock exactly at 10 am.
Then they go to watch a cricket match at Eden Gardens and sit very far away.
Shyam doesn't know that his watch is faulty and has stopped for an hour in midst of the match.

Ram's job is to note what the bowler does, while Sam records the batsman.

At the end of the day, their notebooks look like these:

```
[Ram]
3:00:00 PM: Shami gives a powerful yorker! 

[Shyam]
2:00:10 PM: Kohli is out!
```

Can you see the problem?
The event of Shami's bowling **happened before** Kohli getting out.
But Shyam's clock was behind, so the event of Kohli getting out received a lower timestamp.
If they merged their notebooks, the resulting log would be incoherent.


# Logical Clocks

To establish a causal ordering among the events,
the concept of Logical clocks have been formulated.
These do not depend upon Physical clocks
but in general, progress forward in time with new events happening.

Few examples of Logical clocks are: **Lamport Clock**, **Vector Clock** etc.

We will talk about Lamport clocks in this post.

## Notations

- `a -> b` means event a happened before b
- `a || b` means events a and b happened concurrently
- `L(a)` is the Lamport Timestamp of an event a.
- `N(a)` is the node number on which an event occurs.


# Lamport Clock

This is one of the earliest formulations of Logical clocks and is remarkably simple too.
It was developed by Leslie Lamport.

Each node maintains a counter (`t`).
The value of `t` at the time of an event `a` is the Lamport timestamp of the event `L(a)`.

`t` is incremented as follows:

```
// Initial condition
t := 0

// For an internal event in the node
t++
PerformEvent(t)

// Sending message to another node
t++
Send(t, msg)

// Receiving message from another node
t', msg := Recv()
t = max(t, t') + 1
```

This simple algorithm ensures that if `a -> b`, `L(a) < L(b)`.
The converse, however, is not true.
If `L(a) < L(b)` then either `a -> b` or `a || b`.

It is not possible to distinguish "happens before" and "concurrent" cases using Lamport Clocks.
We need to use the Vector Clock for it.


## Total Ordering of Events

Lamport clock provides a neat way of establishing a total order among the events.
Since events in different nodes may have the same timestamp,
we define the total order by the pair `(L(a), N(a))`.

The relation is as follows: `a < b <=> (L(a) < L(b)) || (L(a) = L(b) && N(a) < N(b))`

This ordering is useful in places where an ordering of events coming from different nodes is required,
eg, in consensus, in distributed database transaction processing etc.


# The Code

Here is a small example of Lamport Clocks written in Golang.
The nodes are just goroutines and they communicate among themselves using a set of channels.

Each nodes does the following indefinitely:

1. Randomly chooses whether to perform an internal event or send a message
2. If doing internal event, sleeps for a random amount of time.
3. If sending message, sends message to a randomly selected node.


## Message

We send this struct as our message:

```go
type Message struct {
	timestamp int32
	message   string
}
```

Here `timestamp` is the Lamport Timestamp.

## The Node

The node clearly has 2 parts:

1. One that performs the internal event or sends messages
2. The other that waits to receive a message

These 2 have to run concurrently with the internal timestamp (`t`) being common to both.
Thus the places where `t` is accessed will become Critical sections.

```go
func Node(id int, conn []chan Message, n int) {
	var t int32
	t = 0 // Lamport timestamp

    // Receiving part
    

    // Event and Sending part
}
```

The receiving part is simple:

```go
go func() {
    // Receiving section
    for msg := range conn[id] {
        t_temp := int32(math.Max(float64(t), float64(msg.timestamp)))   // max(t, t')
        t_temp++    // max(t, t') + 1
        atomic.StoreInt32(&t, t_temp)
        fmt.Printf("[Node %d][Time %d] Message received: %s\n", id, t, msg.message)
    }
}()
```

See the use of `atomic.StoreInt32` for atomic operations on `t`.

The internal event and sending message part is as follows:

```go
for {
    what := rand.Intn(2)
    if what == 0 {
        // Perform an internal event
        d := time.Duration(rand.Int63n((maxSleep + 1)))

        atomic.AddInt32(&t, 1)
        fmt.Printf("[Node %d][Time %d] Internal Event: Sleeping for %d nanoseconds\n", id, t, d)
        time.Sleep(d)
        fmt.Printf("[Node %d][Time %d] Internal Event: Sleeping complete\n", id, t)
    } else {
        // Send message
        recipient := rand.Intn(n)
        atomic.AddInt32(&t, 1)
        fmt.Printf("[Node %d][Time %d] Sending message to %d\n", id, t, recipient)
        message := fmt.Sprintf("Node %d says Hi!", id)
        conn[recipient] <- Message{
            timestamp: t,
            message:   message,
        }
        fmt.Printf("[Node %d][Time %d] Sent message to %d\n", id, t, recipient)
    }
}
```

## Putting it altogether

Here's the full code if someone wants to run it on their machine:

```go
/*
File: lamport.go
Author: Shubham Mishra

Implementation of Lamport clocks
Nodes are go routines
*/
package main

import (
	"fmt"
	"math"
	"math/rand"
	"os"
	"strconv"
	"sync"
	"sync/atomic"
	"time"
)

type Message struct {
	timestamp int32
	message   string
}

var maxSleep int64
var wg sync.WaitGroup

/*
Node represents the behaviour of a node.
It randomly chooses whether to perform an internal event
or send message to a randomly selected node.

id = Unique node identifier,
conn = Array of channels used to send message (represents Network),
n = Max nodes
*/
func Node(id int, conn []chan Message, n int) {
	var t int32
	t = 0 // Lamport timestamp

	go func() {
		// Receiving section
		for msg := range conn[id] {
			t_temp := int32(math.Max(float64(t), float64(msg.timestamp)))
			t_temp++
			atomic.StoreInt32(&t, t_temp)
			fmt.Printf("[Node %d][Time %d] Message received: %s\n", id, t, msg.message)
		}
	}()

	for {
		what := rand.Intn(2)
		if what == 0 {
			// Perform an internal event
			d := time.Duration(rand.Int63n((maxSleep + 1)))

			atomic.AddInt32(&t, 1)
			fmt.Printf("[Node %d][Time %d] Internal Event: Sleeping for %d nanoseconds\n", id, t, d)
			time.Sleep(d)
			fmt.Printf("[Node %d][Time %d] Internal Event: Sleeping complete\n", id, t)
		} else {
			// Send message
			recipient := rand.Intn(n)
			atomic.AddInt32(&t, 1)
			fmt.Printf("[Node %d][Time %d] Sending message to %d\n", id, t, recipient)
			message := fmt.Sprintf("Node %d says Hi!", id)
			conn[recipient] <- Message{
				timestamp: t,
				message:   message,
			}
			fmt.Printf("[Node %d][Time %d] Sent message to %d\n", id, t, recipient)
		}
	}

	wg.Done()

}

func main() {
	n, err := strconv.Atoi(os.Args[1])
	if err != nil {
		fmt.Println("Usage: go run lamport.go number_of_nodes")
		os.Exit(1)
	}
	conn := make([]chan Message, n)

	for i := 0; i < n; i++ {
		conn[i] = make(chan Message)
	}
	maxSleep = 5e+9
	for i := 0; i < n; i++ {
		wg.Add(1)
		go Node(i, conn, n)
	}
	wg.Wait()

}
```

## Output

Let us run it with 10 nodes and observe the timestamps in one of the nodes:

```bash
go run lamport.go 10 | grep -E "\[Node 1\]"
```

```log
[Node 1][Time 1] Internal Event: Sleeping for 4712270262 nanoseconds
[Node 1][Time 4] Message received: Node 8 says Hi!
[Node 1][Time 5] Message received: Node 4 says Hi!
[Node 1][Time 6] Message received: Node 2 says Hi!
[Node 1][Time 14] Message received: Node 6 says Hi!
[Node 1][Time 14] Internal Event: Sleeping complete
[Node 1][Time 15] Sending message to 6
[Node 1][Time 15] Sent message to 6
[Node 1][Time 16] Sending message to 1
[Node 1][Time 16] Sent message to 1
[Node 1][Time 17] Internal Event: Sleeping for 4713211716 nanoseconds
[Node 1][Time 18] Message received: Node 1 says Hi!
[Node 1][Time 23] Message received: Node 3 says Hi!
[Node 1][Time 23] Internal Event: Sleeping complete
[Node 1][Time 24] Internal Event: Sleeping for 3108013669 nanoseconds
[Node 1][Time 34] Message received: Node 4 says Hi!
```

See how there is a sudden jump in timestamp from 24 to 34 in the last line.

This shows the `max` operation at work and that node 4 had quite a lot of events happening when node 1 went to sleep for 3 seconds.

# References

I have merely scratched the surface.
I mean, what's a post about distributed systems doing without a timing diagram!

These, however, are far better resources:

- [Wikipedia](https://en.wikipedia.org/wiki/Lamport_timestamp)
- [Martin Kleppmann's Lecture](https://www.youtube.com/watch?v=x-D8iFU1d-o)