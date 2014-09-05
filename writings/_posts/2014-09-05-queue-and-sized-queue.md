---
layout: writing
title: "Queue and SizedQueue: hidden gems in the Ruby standard library"
description: "Queue and SizedQueue: hidden gems in the Ruby standard library"
---

Imagine (it won't be hard, because it's pretty common), you have this code snippet:

```ruby
list.each do |item|
  do_something(item)
end
```

We will use it to show two among the many hidden gems in the Ruby standard library [[1]](#note1).

So, we have our simple code snippet, which problems could we have with it?

For example, it might be slow, because the process we apply on each item is:

```ruby
list.each do |item|
  do_something_slow(item)
end
```

For example, calculating a Fibonacci sequence:

```ruby
list.each do |item|
  fibonacci(item)
end
```

Or, something more realistic, doing some HTTP request:

```ruby
list.each do |item|
  http.post(item)
end
```

What can we do? Well, paralelizing the processes seems something obvious to do when it's possible. A very simple way to do it is a parallel map. Some languages have it built in. It's not the case in Ruby but it's trivial to implement:

```ruby
list.map do |item|
  Thread.new do
    do_something(item)
  end
end.each(&:join)
```

For each item in the list, we spawn a thread doing the job, and later we join them all, that is, wait for them to finish.

Now, there is another problem here, because the list could be huge:

```ruby
huge_list.map do |item|
  Thread.new do
    do_something(item)
  end
end.each(&:join)
```

For example, it could be the biggest file in your hard drive:

```ruby
huge_list = File.open('huge.txt') # [[2]](#note2)
```

Or, even bigger than huge, some infinite stream:

```ruby
huge_list = TCPSocket.new('somewhere.com', 1234)
```

This is some kind of paralelism antipattern, where the number of threads depends linearly on the size on the input. From a theorical point of view, [Amdahl's law](http://en.wikipedia.org/wiki/Amdahl's_law) states that the benefits of more parallel processing units are decreasing. From a practical one, you just can't spawn 10,000,000 threads in Ruby. Or, for that matter, in anything else.

The way to overcome this problem is a thread pool, which sounds very sophisticate but it's a simple idea. It's a way to have a fixed set of threads doing the work, instead of creating new ones each time. There are gems implementing this, but we can very easily implement our own using something in the standard library. Meet `Queue`.

```ruby
require 'thread'

num_workers = 20
queue = Queue.new
threads = num_workers.times.map do
  Thread.new do
    until (item = queue.pop) == :END
      do_something(item)
    end
  end
end
list.each { |item| queue << item }
num_workers.times { queue << :END }
threads.each(&:join)
```

A `Queue` is just a thread safe array. No matter which thread is reading or writing the state is always consistent. So we decide on a number of threads (20), create a queue, and spawn those 20 threads, getting items from the queue and processing them. Then we will iterate on the list, queuing all the items. Our workers will be there, picking them up one by one. We will finally queue 20 `:END` items [[3]](#note3), which will make the workers finish.

Great! Now, this code has a problem. We have a producer and a number of consumers working independently, which means that if the producer produces faster than the consumers consume, the items will accumulate in the queue. And, in the same way that we can't spawn 10,000,000 threads, we probably don't want to load our huge file or infinite stream in memory. Meet `SizedQueue`.

```ruby
require 'thread'

num_workers = 20
queue = SizedQueue.new(num_workers * 2)
threads = num_workers.times.map do
  Thread.new do
    until (item = queue.pop) == :END
      do_something(item)
    end
  end
end
list.each { |item| queue << item }
num_workers.times { queue << :END }
threads.each(&:join)
```

This example is almost the same, but instead of `Queue`, it uses `SizedQueue`. This variant is initialized with a `size`, which is the maximum number of items we want to have in the queue (in our case, arbitrarily set to twice the number of workers, 40). When that number is reached, `<<` will block the thread until there is space (when the consumers get more items). Problem solved!

## Bonus track: refactoring

This code is ugly and you know it. Don't add code like this in your applications. Build abstractions using these ideas, and use them in your code. Don't mix concurrency and business logic code. For example (this is a very simplistic one, with almost the same code copied and pasted, and already a big improvement):

```ruby
require 'thread'

class WorkersPool
  def initialize(num_workers)
    @num_workers = num_workers
  end

  def process(enumerable, &block)
    queue = SizedQueue.new(@num_workers * 2)
    threads = @num_workers.times.map do
      Thread.new do
        until (item = queue.pop) == :END
          block.call(item)
        end
      end
    end
    enumerable.each { |item| queue << item }
    @num_workers.times { queue << :END }
    threads.each(&:join)
  end
end

workers = WorkersPool.new(20)

workers.process(huge_list) do |item|
  do_something(item)
end
```

### Notes

1. <a name="note1" href="javascript:history.go(-1)">↩</a> They're not actually gems, that's the joke ;)
2. <a name="note2" href="javascript:history.go(-1)">↩</a> This works exactly the same because `File`, like all `IO`, includes `Enumerable`.
3. <a name="note3" href="javascript:history.go(-1)">↩</a> This is a bit ugly and actually, very easy to improve by using a specific object that avoids any collision (which is not the case of an arbitrary symbol, which could be included in the list, breaking everything). I used the symbol here because it makes the code simpler to understand at the first view, but any *real* implementation should be a bit more like:

```ruby
require 'thread'

num_workers = 20
queue = Queue.new
end_object = Object.new
threads = num_workers.times.map do
  Thread.new do
    until (item = queue.pop) == end_object
      do_something(item)
    end
  end
end
list.each { |item| queue << item }
num_workers.times { queue << end_object }
threads.each(&:join)
```
