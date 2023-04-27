---
icon: fas fa-info-circle
title: Rails and amazon SQS
date: 2023-04-27 11:04
categories: [RAILS, AWS]
tags: [ruby, aws]
order: 4
---

# Introduction

For those not familiar with Amazon SQS, it's a reliable and high-scalable fully managed queue service. If you need to process in background, and all that jazz, but without the overhead of maging your queue service, go for it. It can be also free.

This post is about AMAZON SQS FIFO Queues, which is a type of queue designed to guarantee that messsages are processed exactly once and in the exact order that they are sent.

# Shoryuken

The example in this post are based On [Shoryuken](https://github.com/ruby-shoryuken/shoryuken), a SQS thread based messages processor in Ruby.

Shoryuken empowers the SDK by adding support for continuously polling and thread based processing, besides that, it also adds abstractions simplyfing the integration with SQS.

# Fifo Queues

There are two key attributes when working with FIFO Queues: Message group ID and Message Deduplication Id.

## Message Group Id

For the Message Group ID you can either use the same message group for all messages, or group them by some business logic, let's say customer_id, or whatever that makes sense to you. The messages will be received in the order they were sent to their group.

Shoryuken automatically sets the message group id to ShoryukenMessage if it is not provided


```ruby
Tuturu.perform_async('hello')

Tuturu.perform_async('hello', message_group_id: )
```

## Message deduplication Id

FOr exactly-once processing, FIFO Queues use the Message Deduplication Id for identifyng and rejecting duplicates sent within a 5 minutes interval. The Message Deduplication is auto generated based on the message body if you enable content-based deduplication when creating your queue.

Shoryuken automatically sets the Deduplication Id to a SHA-256 hash of the message body if it is not provived.

```ruby
Tuturu.perform_async('hello')

Tuturu.perform_async('hello', message_deduplication_id: '' )
```

# Getting started

A few steps for using message groupd and Shoryuken.

## Create a FIFO Queue

```ruby
shoryuken sqs create queue.fifo
```

## Create a worker

```ruby
class HelloWorker
  include Shoryuken::Worker

  shoryuken_options queue: 'queue.fifo', auto_delete: true

  def perform(sqs_msg, name)
    puts "Hello, #{name}"
  end
end
```

## Send messages

```ruby
HelloWorker.perform_async('tuturu 1', message_group_id: 'hello')
HelloWorker.perform_async('tuturu 2', message_group_id: 'hello')
HelloWorker.perform_async('tuturu 3', message_group_id: 'hello')
```

## Start Shoryuken

```ruby
shoryuken -q queue -r ./hello_worker.rb
```

That's it. Shoryuken will start processing your messages.


# Sequential processing per group

By default, Shoryuken tries to receive as many messages as it can, limited to the number of the available threads (see concurrency) and the SQS limit of 10 messages per request. If there are 10 available threads, it may receive up to 10 messages for the same Message Group ID and process them all in parallel.

If you want to sequentially process one message at a time, set max_number_of_messages: 1 (see receive options), with that, if you send messages as follows;

```ruby
ExpireTuturu.perform_async(date)
ActivateTuturu.perform_async(date)
RenewTuturu.perform_async(date)
```

Shoryuken will receive the message for ExpireTuturu, process, receive the message for ActivateTuturu, process, receive the message for RenewTuturu and process, one by one.

# Should I use FIFO Queues?

## If you want to reject duplicated messages sent within a 5 minutes interval.

Yes.

## If you want to control the order that the messages are received.

Yes.

## If you want to control the order, but you want to receive duplicates.

Yes. Just set message_deduplication_id to something unique and SQS won't reject as duplicate.

Same applies if you want to reject duplicates and dont want to control the order, just set the message_group_id to something unique.

## If you don't care about the order and duplicates.


No. Go with standard queues.

## If you want per-messsages delay

No. FIFO queues don't support per-message delays, only per-queue delays.
