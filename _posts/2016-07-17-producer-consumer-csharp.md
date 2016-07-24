---
layout: post
title: Producer - consumer pattern for arbitrary number of endpoints and with limited buffer
author: Martin Rotter
date: 2016-07-17 09:00:00 +0200
category: it-programming
language: en
---

Buffer is implemented by Buffer class. This class is simply wrapped array.
<!--more-->

```csharp
public class Buffer {
  private T[] _data;
  private Semaphore _semaphoreNonEmpty;
  private Semaphore _semaphoreNonFull;

  public Buffer(int capacity) {
    _data = new T[capacity];
    _semaphoreNonEmpty = new Semaphore(0, capacity);
    _semaphoreNonFull = new Semaphore(capacity, capacity);
  }

  public T this[int i] {
    get {
      return _data[i];
    }
    set {
      _data[i] = value;
    }
  }

  public int Length {
    get {
      return _data.Length;
    }
  }

  public bool WaitNonEmpty() {
    return _semaphoreNonEmpty.WaitOne();
  }

  public bool WaitNonFull() {
    return _semaphoreNonFull.WaitOne();
  }

  public int SignalNonEmpty() {
    return _semaphoreNonEmpty.Release();
  }

  public int SignalNonFull() {
    return _semaphoreNonFull.Release();
  }
}
```

Buffer offers quite interesting functionality for constrolling its accessors. They can use thread-blocking functions which unblock if buffer is not full/empty.

Producers and consumers are then implemented in a straightforward way.

```csharp
public class Producer {
  private static Random _generator = new Random(DateTime.Now.Millisecond);

  public static int Produce() {
    // Simulate "work" here.
    Thread.Sleep(_generator.Next(100, 200));
    // Create sample data.
    int data = _generator.Next(101);
    Console.WriteLine("Produced data: {0}", data);
    // Return result of "work".
    return data;
  }

  private Buffer _buffer;
  private static int _index = 0;

  public static object Object = new object();

  public Producer(Buffer buffer) {
    _buffer = buffer;
  }

  public void Run() {
    int data;

    while (true) {
      data = Produce();
      _buffer.WaitNonFull();
      lock (Object) {
        _buffer[_index] = data;
        _index = (_index + 1) % _buffer.Length;
      }
      _buffer.SignalNonEmpty();
    }
  }
}

public class Consumer {
  private static Random _generator = new Random(DateTime.Now.Millisecond ^ 2);

  public static void Consume(int data) {
    // Simulate "work" here.
    Thread.Sleep(_generator.Next(1000, 2001));
    // Process data.
    Console.WriteLine("Consumed data: {0}", data);
  }

  private Buffer _buffer;
  private static int _index = 0;

  public static object Object = new object();

  public Consumer(Buffer buffer) {
    _buffer = buffer;
  }

  public void Run() {
    int data;

    while (true) {
      _buffer.WaitNonEmpty();
      lock (Object) {
        data = _buffer[_index];
        _index = (_index + 1) % _buffer.Length;
      }
      _buffer.SignalNonFull();
      Consume(data);
    }
  }
}
```

Testing of code is very easy, some producers and consumers are created along with buffer.

```csharp
class Program {
  static Buffer buffer = new Buffer(5);

  static void Main(string[] args) {
    Buffer buffer = new Buffer(2);
    List threads = new List();

    for (int i = 0; i < 5; i++) {
      threads.Add(new Thread(CreateProducer));
      threads.Last().Start();
    }

    for (int i = 0; i < 5; i++) {
      threads.Add(new Thread(CreateConsumer));
      threads.Last().Start();
    }

    foreach (var thread in threads) {
      thread.Join();
    }
  }

  static void CreateProducer() {
    Producer producer = new Producer(buffer);
    producer.Run();
  }

  static void CreateConsumer() {
    Consumer consumer = new Consumer(buffer);
    consumer.Run();
  }
}
```