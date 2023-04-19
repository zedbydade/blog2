---
icon: fas fa-info-circle
title: Rails Performance Guide
date: 2023-04-18 11:04
categories: [RAILS]
tags: [ruby, puma]
order: 4
---

# Introduction

Rails performance, scale, and speed have been a topic since the modern web early days. At the root, Ruby is not the most potent lang in the world, but, with the excellent Ruby developer experience and the magic of Rails, you can delivery excellent and stable products that can handle millions of requests. In that post, i will share some things i learning through my Rails experience that can help you make nice product xd.

# Frontend/HTTP layer

Developers often underestimate the impact of frontend performance optimization because it doesn't affect the infrastructure cost. Rendering a website consists of multiples requests. Adding correct HTTP caching headers or configuring HTTP2 to enable multiplexing could speed up your page Time from 10s to 6s. And your POs will love it. A easy way to get most of the HTTP layer config in order is to proxy your app's traffic via [Cloudflare](https://www.cloudflare.com/)

ps: Don't send GET requests with body.

# Redundant SQL queries

ActiveRecord is the greater Rails villain of all time, you should protect the database against him. N+1 queries are the top performance killer for Rails applications. It's not uncommon to see a single request generating hundreds of SQL queries because of a missing eager loading. I'm not sure if the world needed another post about N+1 queries in Ruby on Rails, so i will just leave that link here [includes](https://apidock.com/rails/ActiveRecord/QueryMethods/includes)
# Server configuration

Most Rails apps' performance is IO-bound because of the SQL queries and internal HTTP calls. That is a perfect case for leveraging concurrency in Ruby. That's why I always recommend using [Puma](https://github.com/puma/puma) because of its multithreading capabilities. You can check out the following Heroku docs for general tips on configurint puma workers, and threads count.

ps: If you're using AWS EC2, make sure always to provision the newest generation instance types, i.3, m5.large instead of m4.large.

# Conclusion

Every performance audit that I’ve conducted had its unique challenges. But, the tips I’ve described should apply to most Ruby on Rails projects.
A small development time investment can often significantly impact your customers’ UX, project’s scalability, and infrastructure cost.
