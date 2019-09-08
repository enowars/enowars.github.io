---
layout: post
title: Architectural Overview
tags: ENOWARS3 ENOWARS
author: Trolldemorted
---

After we started receiving questions about our setup, we realized a blog post about it was long overdue! This post will give you a brief introduction into the ecosystem we used to deliver ENOWARS 3.


```
                                                    +------------------+
                                                    | DNS Server       |
+-----------------+      +-------------------+      +------------------+
|   Elastic Stack |      | Casino Checker    |
+-----------------+      +-------------------+      +------------------+
                                                    | APT Repository   |
+-----------------+      +-------------------+      +------------------+
|                 |      | Telescopy Checker |
|   EnoEngine     |      +-------------------+      +------------------+
|   PostGreSQL    |                                 | Gamerouter       |
|   EnoLauncher   |      +-------------------+      +------------------+
|                 |      | Explotify Checker |
+-----------------+      +-------------------+      +------------------+
                                                    | EnoScoreboard    |
                                                    +------------------+
```

## [EnoEngine](https://github.com/enowars/EnoEngine)
The EnoEngine is responsible for
- Starting and ending rounds, i.e. generating flags and noises (noises are things a service must handle successfully, but will not give points when captured) and scheduling tasks to deploy and check them
- Orchestrating the checkers to deploy/check flags/noises
- Accepting submitted flags from the teams

Splitting the engine and launcher (the thing that communicates with the checkers) into seperate processes was a good decision, as it allowed us to seamlessly restart the engine.


## [Checkers](https://github.com/enowars/enochecker)
Every service has a checker, which offers a REST API the launcher uses to order flag deployments and checks. Every checker uses a mongodb to persist data (like usernames, passwords), so loadbalancing between multiple instances of a checker is easy.


## [Elastic Stack](https://www.elastic.co)
Log management in distributed is super important if you want to identify problems quickly, so the engine, launcher and checkers can log json data to be indexed in an elastic stack. This enables us to efficiently filter log messages for things like "related to flag ENOABC" or "related to team 17 and round 19 and service 3".

## DNS Server

## APT Repository
As those of you who played ENOWARS 3 know, we distributed our services via apt. This allows us to conveniently last-minute fix our services, and might enable us to not build any dedicated vulnerable virtual machines for ENOWARS 4 - a simple `apt install enowars` could suffice.

## Gamerouter

## EnoScoreboard


