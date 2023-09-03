---

title: "Using TSD to improve javascript intellisense of IDE"
date: 2015-07-30 23:27:10 +0800
comments: true
categories: Frontend
tags: [JavaScript, TypeScript, VSCode, Tool]
---

As we know, it's very difficult to make the IDE to recognise the function of JavaScript, we called code intellisense, because JavaScript is weak language. So how to solve this problem. There is a way.

`TSD` is TypeScript Definition manager for DefinitelyTyped. It can search and install `TypeScript` definition files directly from repository. The `TypeScript` definition file actually is TypeScript file, and TypeScript is strong type language. So IDE can intellisense the code, like Java and C#.

`TypeScipt` is also like `CoffeeScript`, it can be compiled to `JavaScript`. It is developed by Microsoft company. To See the TypeScript, go to this official [website](http://www.typescriptlang.org/).

<!-- more -->

If you used the `NPM` before, I believe you also can use `TSD` quickly. The `TSD` usage is very similar with `NPM`. And also it has `tsd.json` file to contains javascript libraries type definitions which you want to make them intellisense, just like `package.json` file of `NPM`.

To install it, we can using `NPM` command.

```
npm install tsd -g
```

Use below command to search the DefinitelyTyped file of JavaScript library.

```
tsd query <javascript library name> -i -r
```

for example, we search the definitelyTyped file of angular:

```
tsd query angular -i -r
```
this will print below information:

```
- angularjs / angular
   -> jquery > jquery
    v latest
    v 1.0.0
    v 1.2.0
   >> Angular JS 1.4            : angularjs.org
    @ Diego Vilar               : github.com/diegovilar
    < angular (external module)
```

To install the definitelyTyped file into your project. Just follow below commands:

```
tsd install angular --save
```
also like using NPM to install the package. For tsd, this will create `tsd.json` file firstly, and then create `typing` directory, put the related definitelytyped file which is the `.d.ts` extension TypeScript file. 

The example of `tsd.json` file:

``` json
{
  "version": "v4",
  "repo": "borisyankov/DefinitelyTyped",
  "ref": "master",
  "path": "typings",
  "bundle": "typings/tsd.d.ts",
  "installed": {
    "angularjs/angular.d.ts": {
      "commit": "383a3bc8e654a77eb8bd07d9feb7208735dcb6fa"
    },
    "jquery/jquery.d.ts": {
      "commit": "383a3bc8e654a77eb8bd07d9feb7208735dcb6fa"
    }
  }
}
```

The example of `tsd.d.ts` file, this file is reference the definitelytyped file. and it under the `typings` directory.

``` javascript
	/// <reference path="angularjs/angular.d.ts" />
	/// <reference path="jquery/jquery.d.ts" />
```

See below screenshots of files or directories:

{% img /assets/images/legacy/tsd-file-tree.png 300 %}

Go to [https://github.com/DefinitelyTyped/tsd](https://github.com/DefinitelyTyped/tsd) to see the tsd command details.

And TSD has supports many javascript libraries, you can search them which you want in [definitelytyped repositorty](http://definitelytyped.org/tsd/).

Currently, I found this is only work on [Visual Studio Code](https://code.visualstudio.com/), The `VSCode` is cross platform web develop tools, include Linux, Windows and Mac OS.

To make the VSCode intellisense for javascript. you need to add tsd.d.ts file reference codes to your javascript file, like below.

``` javascript
 /// <reference path="../../typings/tsd.d.ts"/>
```

Okay, lets test the `underscore` javascript library, first install definitely typed file with TSD, and then put the reference code to the js file which is you are working.

``` javascript
/**
 * Created by yushi on 30/07/15.
 */
 
/// <reference path="../../typings/tsd.d.ts"/>
  
function sayHello(name){
    return "Hello " + name;
}
```
So when you invoke the undercore functions, the IDE will intellisense the method.

![intellisense](/assets/images/legacy/tsd-intellisense.png)

