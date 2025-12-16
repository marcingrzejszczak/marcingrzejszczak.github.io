---
title: "Json Assert Lib Released"
summary: "I'm really happy to present the JSON Assert library - over-the-weekend project that came out from the AccuREST library . This post will describe the rationale behind creating this tool and how to use it."
date: 2016-02-27

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com)'

authors:
  - me

tags:
  - Blog

content_meta:
  trending: false
---
I'm really happy to present the
[JSON Assert library](https://github.com/marcingrzejszczak/jsonassert)
- over-the-weekend project that came out from the
[AccuREST library](https://github.com/Codearte/accurest)
. This post will describe the rationale behind creating this tool and how to use it.
[]()
#### Rationale
In AccuREST (the Consumer Driven Contracts implementation library) we're creating tests of the server side. For more information on what is AccuREST and what Consumer Driven Contracts is check the
[AccurREST wiki](https://github.com/Codearte/accurest/wiki)
. Anyways, we're checking if the response from the server matches the one described in the contract.
So having such a Groovy DSL:
```groovy
io.codearte.accurest.dsl.GroovyDsl.make {
  priority 1    request {
    
    method 'POST'        url '/users/password'        headers {
      
      header 'Content-Type': 'application/json'
    }
    body (
    email: $(stub(optional(regex(email()))),
    test('abc@abc.com')),
    callback_url: $(stub(regex(hostname())),
    test('https: //partners.com'))        )
  }
  response {
    
    status 404        headers {
      
      header 'Content-Type': 'application/json'
    }
    body (
    code: value(stub("123123"),
    test(optional("123123"))),
    message: "User not found by email = [${
      value(test(regex(email())),
      stub('not.existing@user.com'))
    }
    ]"        )
  }
  
}
```
Resulted in creation of the following server side response verification
```groovy
given: def request = given (
)    .header('Content-Type',
'application/json')    .body (
'{
  "email":"abc@abc.com",
  "callback_url":"https: //partners.com"
}
') when: def response = given (
).spec(request)    .post("/users/password") then: response.statusCode == 404  response.header('Content-Type')  == 'application/json' and: DocumentContext parsedJson = JsonPath.parse(response.body.asString())  !parsedJson.read('''$[?(@.code =~ /(123123)?/)]''',
JSONArray).empty  !parsedJson.read('''$[?(@.message =~ /User not found by email = \\[[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{
  2,
  4
}
\\]/)]''',
JSONArray).empty
```
AccuREST users stated that their biggest problem is this part:
```
!parsedJson.read('''$[?(@.code =~ /(123123)?/)]''', JSONArray).empty  !parsedJson.read('''$[?(@.message =~ /User not found by email = \\[[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,4}\\]/)]''', JSONArray).empty
```
They said that JSON Paths are too difficult for them to read.
That's why I've created the
[JSON Assert library](https://github.com/marcingrzejszczak/jsonassert)
. So that instead of the aforementioned code one gets sth like this:
```
assertThatJson(parsedJson).field('code').matches('123123?')  assertThatJson(parsedJson).field('message').matches('User not found by email = \\[[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,4}\\]/)]');
```
#### How to add it to your project
If your using Gradle just add (
[check the latest version number](https://search.maven.org/#search%7Cga%7C1%7Cg%3A%22com.blogspot.toomuchcoding%22%20a%3A%22jsonassert%22)
):
```groovy
testCompile `com.blogspot.toomuchcoding: jsonassert: 0.1.2`
```
and if Maven just add:
```xml
<dependency>
  <groupId>com.blogspot.toomuchcoding</groupId>
  <artifactId>jsonassert</artifactId>
  <version>0.1.2</version>
</dependency>
```
#### How to use it
Since almost everything in JSON Assert is package scoped you have access to two public classes. One of which is the
[JsonAssertion](https://github.com/marcingrzejszczak/jsonassert/blob/master/src/main/java/com/blogspot/toomuchcoding/jsonassert/JsonAssertion.java)
class. It gives you a couple of public methods that give you the entry point to the fluent interface of the library.
You can check the JavaDocs of the
[JsonVerifiable](https://github.com/marcingrzejszczak/jsonassert/blob/master/src/main/java/com/blogspot/toomuchcoding/jsonassert/JsonVerifiable.java)
interface in order to see what kind of methods can be used.
#### Examples
Best examples are
[tests](https://github.com/marcingrzejszczak/jsonassert/blob/master/src/test/groovy/com/blogspot/toomuchcoding/jsonassert/JsonAssertionSpec.groovy)
. I'll show you a couple of them here.
**Example 1**
Having a JSON:
```
[ {                                "some" : {                                    "nested" : {                                        "json" : "with value",                                        "anothervalue": 4,                                        "withlist" : [                                            { "name" :"name1"} , {"name": "name2"}, {"anothernested": { "name": "name3"} }                                        ]                                    }                                }                            },                            {                                "someother" : {                                    "nested" : {                                        "json" : "with value",                                        "anothervalue": 4,                                        "withlist" : [                                            { "name" :"name1"} , {"name": "name2"}                                        ]                                    }                                }                            }                        ]
```
Instead of writing:
```
$[*].some.nested.withlist[*].anothernested[?(@.name == 'name3')]
```
you can write
```groovy
assertThat(json).array().field("some").field("nested").array("withlist").field("anothernested").field("name").isEqualTo("name3")
```
**Example 2**
Having a JSON:
```
{    "property1": [        { "property2": "test1"},        { "property3": "test2"}    ]}
```
Instead of writing:
```
$.property1[*][?(@.property2 == 'test1')]
```
you can write
```groovy
assertThat(json).array("property1").contains("property2").isEqualTo("test1")
```
#### Future plans
It would be nice to:
- integrate with [AssertJ](https://joel-costigliola.github.io/assertj/)
- add more [JSON Path features](https://github.com/jayway/JsonPath#functions) (functions, filters etc.)
#### Links
- [JSON Assert](https://github.com/marcingrzejszczak/jsonassert)
- [AccuREST](https://github.com/Codearte/accurest/wiki)
- [Video about AccuREST ](https://www.youtube.com/watch?v=daafmTYFoDU)by [Olga Maciaszek-Sharma](https://twitter.com/olga_maciaszek)
- [Jayway JSON Path](https://github.com/jayway/JsonPath)
- [AssertJ](https://joel-costigliola.github.io/assertj/)
