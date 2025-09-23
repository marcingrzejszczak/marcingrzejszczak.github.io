---
title: "Introduction To Groovy Runtime"
summary: "Hi! I'm very happy to share my presentation regarding Groovy metaprogramming and AST transforms ."
date: 2014-02-04

# Featured image
# Place an image named `featured.jpg/png` in this page's folder and customize its options here.
image:
  caption: 'Image credit: [**Unsplash**](https://unsplash.com)'

authors:
  - admin

tags:
  - Blog

content_meta:
  trending: false
---
Hi!
I'm very happy to share my presentation regarding
**Groovy metaprogramming and AST transforms**
. The slides are available at
[SlideShare](https://www.slideshare.net/MarcinGrzejszczak/introduction-to-groovy-runtime-metaprogramming-and-ast-transforms)
and the code is available at
[TooMuchCoding Bitbucket](https://bitbucket.org/gregorin1987/too-much-coding/src/e5ab7c69ab7b2796075fd6f087fbf31346aa2d2b/Groovy/ast/?at=default)
and
[TooMuchCoding Github](https://github.com/marcingrzejszczak/too-much-coding/tree/master/Groovy/ast)
repositories. If you have any problems with reading any part of the slides or sth just post a comment here and I'll tr to help you :)
Enjoy!
[]()
** Introduction to Groovy runtime metaprogramming and AST transforms **
from
**Marcin Grzejszczak**
I had a discussion with one of the readers regarding issues related to creating an AST transformation that would be used on classes (and not on scripts). Often you can see that such a transformation does not work for you even though it should. The problem might be that you have the AST transformation compiled in the same time toghether with the class that you annotated with AST transformation related annotation.
What you have to remember about is that your AST transformation related classes have to be compiled prior to using (that's why you can often find that your AST transformation is working when you right click on your annotated class in your IDE (or Groovy console) and manually compile that particular class. That's because you compile that particular class when other classes have already been compiled. That's the very same scenario as with compiling a script that is using an AST transformation - first your tranformations are compiled and afterwards at runtime the script gets compiled.
Please check the additional repository
[ast_examples](https://github.com/marcingrzejszczak/ast_examples)
that consists of the same examples as in the
[TooMuchCoding ](https://github.com/marcingrzejszczak/too-much-coding)
repository together with an additional AST transformation that is set on a class.
