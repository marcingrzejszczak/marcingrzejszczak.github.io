---
layout: post
title: "JSON Assert 0.3.0 released"
date: 2016-03-27 23:17:21 +0200
comments: true
categories: articles
tags:
- jsonassert
- testing
---

I'm pleased to announce that JSON Assert version 0.3.0 got released! The following features have been added

- [Building String JSON Path](https://github.com/marcingrzejszczak/jsonassert/issues/6)
- [Pass fields as array of Strings](https://github.com/marcingrzejszczak/jsonassert/issues/7)

and in 0.2.2 the [annoying warning message got removed](https://github.com/marcingrzejszczak/jsonassert/issues/5)

<!-- more -->

## Building String JSON Path

Writing JSON Paths to assert JSON is no fun at all... That's why JSON Assert was created in the first place.
One doesn't always want to use this library to perform assertions though. But what one wants to profit from
is the fluent interface to create the JSON Path expression.

That's why with 0.3.0 you can use the new class called `JsonPath`. It has a single static method `builder()`
with which you can... well... build the JSON Path. Remember to call `jsonPath()` to get its String value.

So for instance running this code:

`JsonPath.builder().field("some").field("nested").field("anothervalue").isEqualTo(4).jsonPath()`

would result in creating the following String JSON Path representation:

`$.some.nested[?(@.anothervalue == 4)]`

Other examples:

```
JsonPath.builder().field("some").field("nested").array("withlist").contains("name").isEqualTo("name1").jsonPath() === '''$.some.nested.withlist[*][?(@.name == 'name1')]'''
JsonPath.builder().field("some").field("nested").field("json").isEqualTo("with \"val'ue").jsonPath() === '''$.some.nested[?(@.json == 'with "val\\'ue')]'''
JsonPath.builder().field("some", "nested", "json").isEqualTo("with \"val'ue").jsonPath() === '''$.some.nested[?(@.json == 'with "val\\'ue')]'''
```

## Pass fields as array of Strings

This is a small, handy feature that allows you to write less code. Often you iterate over a JSON that has plenty of fields. With the 0.3.0 release
instead of writing:

`assertThat(json).field("some").field("nested").field("json").isEqualTo("with \"val'ue")`

you can write

`assertThat(json1).field("some", "nested", "json").isEqualTo("with \"val'ue")`

You get a method that allows you to traverse the JSON fields by passing an array of field names.

## Contact

Remember that JSON Assert has its own [Gitter channel](https://gitter.im/marcingrzejszczak/jsonassert) so in case of questions do not hesitate to contact me there.
