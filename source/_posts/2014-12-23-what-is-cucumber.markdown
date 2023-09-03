---

title: "What is cucumber"
date: 2014-12-23 00:23:25 +0800
comments: true
categories: Test
tags: [Cucumber, Test]
---

`Cucumber` is a tool that can execute `plain-text` functional descriptions. And also it's  `BDD(Behaviour Driven Development)` `automated tests`. It is written by `Ruby`, and now it supports more than 40 languages, such as `Java`, `C#` and `Scala` etc.

### Install
Firstly, we need to install `Ruby` and `RubyGems`, to check whether the installation of Ruby and RubyGems is successful. You can inputx the commands to terminal, just like as below:
```
~  ruby --version
ruby 2.0.0p481 (2014-05-08 revision 45883) [universal.x86_64-darwin14]
~  gem --version
2.0.14  
```

And then we can run the command below to install `Cucumber`:
```
~  gem install cucumber
```

After this, put below command to see how to use it.
```
~  cucumber --help
```
<!--more-->

### Gherkin
`Gherkin` is a language that can be understand by `Cucumber`. It's a `Business Readable`, `Domain Specific Language`. Very easy to understand what the functional want to do.    

The `Gherkin` has two purpose, documention and automated test. The structure is Treetop grammer than is part of Cucumber codebase and use indentation to format it.   

 The `Gherkin` has two conventions:
 + Single `Gherkin` source file contains a description of single feature.
 + The source file is feature extention file(`*.feature`).

Like `YAML(Yet Another Markup Language)`, Gherkin is a line-oriented language that uses indentation to define structure. Most lines start with keyword. And you can start with a hash sign(#) to comment the text.

A Gherkin source file usually looks like this

``` gherkin
 1: Feature: Some terse yet descriptive text of what is desired
 2:   Textual description of the business value of this feature
 3:   Business rules that govern the scope of the feature
 4:   Any additional information that will make the feature easier to understand
 5: 
 6:   Scenario: Some determinable business situation
 7:     Given some precondition
 8:       And some other precondition
 9:      When some action by the actor
10:       And some other action
11:       And yet another action
12:      Then some testable outcome is achieved
13:       And something else we can check happens too
14: 
15:   Scenario: A different situation
```
Above the codes, It's very similar with `Given-When-Then` the `Feature`, `Scenario`, `Given`, `And`, `When`, `Then` all are keywords of `Gherkin`.