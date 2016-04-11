---
layout: post
title: "UpToDate Gradle Plugin migrated"
date: 2016-04-11 13:28:43 +0200
comments: true
categories:
- uptodate
- gradle
- gradle plugin
---

Time for another release here at [Too Much Coding blog](http://toomuchcoding.com)! This time it will be a short post :) I'm happy to announce that the [UpToDate Gradle Plugin](https://github.com/marcingrzejszczak/uptodate-gradle-plugin) has finally found a new home!

<!-- more -->

## Introduction

 Since my leaving the company owning the original UpToDate Gradle Plugin repository, the project is almost not maintained at all. For quite a long time any development was done mostly by me and actually I was the author of most of the its code (like in the case of [Stub Runner](/blog/2016/04/06/accurest-stubrunner-released/) ). That's why I've decided to fork the code, repackage it and start versioning from 1.0.0.

## What is UpToDate Gradle Plugin?

 Gradle plugin that tells you what libs have new versions on Maven Central, so when you come back to a project, you know what you can update.

## How to use it?

### Step 1: Add dependency to Maven Central and to the plugin

 ```
 buildscript {
     repositories {
         mavenCentral()
     }
     dependencies {
         classpath 'com.toomuchcoding:uptodate-gradle-plugin:1.0.0'
     }
 }
 ```

### Step 2: Add the plugin to your build (gradle.build)

 ```
 apply plugin: 'com.toomuchcoding.uptodate'
 ```

 And now you can run the plugin with

 ```
 gradle uptodate
 ```

### Step 3: Read the readme ;)

For more information just read the project's [Readme](https://github.com/marcingrzejszczak/uptodate-gradle-plugin).

## How to migrate to `com.toomuchcoding:uptodate-gradle-plugin`?

If you're using the old version of the code just change

```
com.ofg
```

into

```
com.toomuchcoding
```

and that should be it :) Oh, and change the version. I'm starting versioning from 1.0.0.

## I've got questions - where to contact you?

Talk to me at the [project's Gitter](https://gitter.im/marcingrzejszczak/uptodate-gradle-plugin).
