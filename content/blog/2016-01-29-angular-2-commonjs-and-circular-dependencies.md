---
title: Angular 2, CommonJS and Circular Dependencies
author: michael-bromley
type: post
date: 2016-01-29T08:50:04+00:00
aliases:
  - blog/507/angular-2-commonjs-and-circular-dependencies
categories:
  - post
tags:
  - Angular
  - JavaScript

---
Yesterday I lost several hours to the following error in an Angular 2 app I am working on:

```Text
Cannot resolve all parameters for 'BaseDataService'(Http, undefined, ODataService). 
Make sure that all the parameters are decorated with Inject or have valid type 
annotations and that 'BaseDataService' is decorated with Injectable.
```

The `undefined` in this case is my redux AppStore service. Other dependencies (Http, ODataService) worked just fine. Even weirder - I was successfully injecting AppStore into several other components without issue. Why did it fail only in this case?

After stepping through first my app code, then the Angular 2 framework code, and finally the Webpack-generated JS, I figured out that the problem originated while Webpack is walking the module dependency graph and populated the `exports` property of each CommonJS module.

A little bit more research revealed that I was running into a problem with the way the CommonJS module format (as used by Webpack and Browserify) deals with circular (or "cyclic") dependencies. If you are interested in the details, read the [section on cyclic dependencies][1] in Axel Rauschmayer's _Exploring ES6_.

**In short: If you run into this error, carefully check your modules for circular dependencies**. In my case, the circle consisted of about 4 "hops" from one module to the next until getting back to the start, so it was by no means obvious.

I hope this article gets a good ranking on Google and saves someone else the pain I just went through.

 [1]: http://exploringjs.com/es6/ch_modules.html#sec_cyclic-dependencies