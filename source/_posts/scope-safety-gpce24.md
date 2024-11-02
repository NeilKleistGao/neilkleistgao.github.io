---
title: "[GPCE24'] Seamless Scope-Safe Metaprogramming Through Polymorphic Subtype Inference (Short Paper)"
date: 2024-02-20 12:40:47
tags:
  - Research
  - Type System
category: Papers
cover: images/pasadena.jpg
---

[DOI](https://doi.org/10.1145/3689484.3690733)

## Abstract

Practical metaprogramming applications often involve manipulating open code fragments, which is easy to get wrong in the absence of static verification that all variable occurrences remain correctly bound. Many approaches have been proposed to verify the type- and scope-safety of metaprograms, but these approaches are either incomplete or cumbersome, imposing heavy type annotation or proof obligation burdens on metaprogrammers. In this short paper, we propose a new type system to statically keep track of the context requirements of code fragments. Our system uses a novel combination of Boolean-algebraic subtyping and first-class polymorphic type inference techniques to alleviate the annotation burden. The former provides the ability to encode scope requirements as unions of types and the latter allows these types to be locally quantified through a flexible form of polymorphic subtyping. We formalize this type system and demonstrate its implementation in the nascent MLscript functional and object-oriented programming language.
