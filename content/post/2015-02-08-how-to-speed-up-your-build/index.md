---
title: "How To Speed Up Your Build (from 90 to 8 minutes)"
summary: "How we cut a large monolith’s Gradle build from ~90 minutes to ~8 minutes using profiling, smarter task configuration, parallelism, project restructuring, and a beefier build machine."
date: 2015-02-08
authors:
  - admin
tags:
  - Gradle
content_meta:
  trending: false
---
> [!NOTE]
> This blog was written in 2015 and was one of my most successful posts. It's not even about the number of readers, but it's more about how successful we were about cutting down the build time. By doing this we've been able to massively save money, improve delivery time and increase code quality.

Even though I was supposed to write a series of blog posts about [micro-infra-spring](/tags/micro-infra-spring/) here at the [Too Much Coding blog](https://toomuchcoding.com/), today I’ll write about how we managed to decrease our biggest project’s build time **from 90 to 8 minutes**!

<!--more-->

## Context

At one of the companies I worked for, we faced a big problem with pull-request (PR) build times. We had a monolithic application we were in the process of slicing into microservices, but until that was finished we had to build the big app for every PR. We needed much faster CI feedback so PR builds wouldn’t queue endlessly. You can imagine the frustration when developers couldn’t merge to `master` because they were stuck waiting.

### Structure

- Over **200 Gradle modules**.
- **A dozen+ big “country” projects** that produce fat JARs.
- A **core module**: when it changes, we must rebuild all big projects to ensure nothing breaks.
- A few older countries use **GWT**; we also run **JS tasks**.

### Initial stats

Before we started optimizing, the full build (all countries) took **~90 minutes**.  
**Current build time:** ~90 minutes.

---

## 1) Profile your build

First, we ran the build with the `--profile` switch. Gradle then generated useful stats to guide optimization. If you do any performance work, **measure first**.

See: [Gradle docs on profiling](https://gradle.org/docs/current/userguide/tutorial_gradle_command_line.html#sec:profiling_build).

---

## 2) Exclude long-running tasks in dev mode

We were spending a lot of time on **JS minification** and **GWT compilation**. We added a custom property `-PdevMode` to disable/optimize long-running tasks for developer builds:

- exclude JS minification;
- optimize GWT compilation.

**Gain:** ~40 minutes  
**Current build time:** ~50 minutes

---

## 3) Check your tests

Together with the one and only [Adam Chudzik](https://github.com/achudzik), we created a super-beta [Gradle Test Profiler](https://github.com/marcingrzejszczak/gradle-test-profiler) that outputs a CSV of tests sorted by execution time. We wanted **quick, easy wins** without huge refactors.

We found a single test taking **~50 seconds** for a feature that wasn’t (and would never be) enabled in production. Disabling it gave a quick win. (There were many other tests worth improving—duplication, setup, etc.—but we focused on speed first.)

**Gain:** ~1 minute  
**Overall gain:** ~41 minutes  
**Current build time:** ~49 minutes

---

## 4) Turn on `--parallel` (at least for compilation)

We decided to go parallel. With `--parallel`, Gradle builds modules concurrently (it figures out thread count). See [Parallel project execution](https://gradle.org/docs/current/userguide/multi_project_builds.html#sec:parallel_execution).

It’s marked incubating, and we originally hit `BindException`s (port allocation). After chatting with [Szczepan Faber](https://twitter.com/szczepiq), we learned the feature was mature enough—our **ports** were the real problem.

As a stop-gap, we compiled in parallel but ran tests sequentially.

**Gain:** ~4 minutes (8-core laptop)  
**Overall gain:** ~45 minutes  
**Current build time:** ~45 minutes

---

## 5) Don’t be a jerk—prepare tests for parallelization

That stop-gap felt dirty, so we fixed the **port collisions** properly:

- randomized fixed ports in tests,
- patched [micro-infra-spring](https://github.com/4finance/micro-infra-spring) so **WireMock** and **ZooKeeper** also randomize ports,
- ran full build with parallel execution.

We expected huge wins. The result was… modest.

**Gain:** ~5 minutes  
**Overall gain:** ~50 minutes  
**Current build time:** ~40 minutes

---

## 6) Revisit your project structure

We dug deeper with `htop`, `iotop`, `vmstat`, etc. CPU usage suggested partial underutilization—as if something serialized the build.

**Root cause:** a module produced a **test JAR** used as a `testCompile` dependency across many projects. Most modules were **waiting** for that module to be **compiled and tested**. It also had **slow integration tests**, so only after those completed could other modules proceed.

### Simple source moves can unlock parallelism

We moved the slow tests to a **separate module**, unblocking dependents. Then we split those slow integration tests into **two modules** to balance the build (each ~3.5 minutes).

**Gain:** ~10 minutes  
**Overall gain:** ~60 minutes  
**Current build time:** ~30 minutes

---

## 7) Don’t skimp on machine power

We invested in a larger AWS instance (e.g., **32 cores, 60 GB RAM**) to fully exploit parallelism (think `c3.8xlarge`-class). Cost was about **$1.68/hour** at the time.

If management balks, do the math: what’s more expensive—**the instance** or **developers waiting** 77 minutes × builds per day?

**Gain:** ~22 minutes  
**Overall gain:** ~82 minutes  
**Current build time:** ~8 minutes

---

## What else can we do?

Yes, we can go further:

- Review slow tests and fix root causes.
- De-duplicate integration tests that check the same logic.
- We use **Liquibase**; merging old changesets would speed startup (we had ~100 executed per Spring context boot, ~30 seconds).
- Reduce Spring context scope to speed up context boots.
- Buy an even more powerful machine 😉

And the **best** long-term option?

### Split the monolith into microservices and ship in 5 minutes 😉

---

## Summary

Hopefully this shows concrete, reproducible ways to **speed up large Gradle builds**:

1. **Measure** (`--profile`), then act.
2. **Skip/optimize** long tasks in dev builds.
3. **Find slow tests** and remove obvious outliers.
4. **Go parallel**, and **fix test data/ports** to support it.
5. **Restructure modules** to unblock parallelism.
6. **Use bigger machines** when it pays back developer time.

The work is tricky (and sometimes frustrating), but the results can be dramatic.
