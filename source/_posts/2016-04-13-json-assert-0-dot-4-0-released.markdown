---
layout: post
title: "JSON Assert 0.4.0 released"
date: 2016-04-13 23:10:51 -0400
comments: true
categories: articles
tags:
- jsonassert
- testing
---

I'm pleased to announce that JSON Assert version 0.4.0 got released! The following feature has been added

- [Retrieving value basing on the JSON Path](https://github.com/marcingrzejszczak/jsonassert/issues/8)

<!-- more -->

## Retrieving value basing on the JSON Path

Wouldn’t it be great to retrieve the value from the JSON via the JSON Path? There you go!

```groovy
given:
    String json = ''' [ {
                            "some" : {
                                "nested" : {
                                    "json" : "with value",
                                    "anothervalue": 4,
                                    "withlist" : [
                                        { "name" :"name1"} ,
                                        {"name": "name2"},
                                        {"anothernested": { "name": "name3"} }
                                    ]
                                }
                            }
                        },
                        {
                            "someother" : {
                                "nested" : {
                                    "json" : true,
                                    "anothervalue": 4,
                                    "withlist" : [
                                        { "name" :"name1"} , {"name": "name2"}
                                    ],
                                    "withlist2" : [
                                        "a", "b"
                                    ]
                                }
                            }
                        }
                    ]
'''
    expect:
        JsonPath.builder(json).array().field("some").field("nested").field("json").read(String) == 'with value'
        JsonPath.builder(json).array().field("some").field("nested").field("anothervalue").read(Integer) == 4
        assertThat(json).array().field("some").field("nested").array("withlist").field("name").read(List) == ['name1', 'name2']
        assertThat(json).array().field("someother").field("nested").array("withlist2").read(List) == ['a', 'b']
        assertThat(json).array().field("someother").field("nested").field("json").read(Boolean) == true
```

The `JsonVerifiable` extends the `JsonReader` that allows you to call the `read(Class<T> clazz)` method to retrieve the value from the JSON basing on the JSON Path.

## Contact

Remember that JSON Assert has its own [Gitter channel](https://gitter.im/marcingrzejszczak/jsonassert) so in case of questions do not hesitate to contact me there.
