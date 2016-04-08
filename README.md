# Purpose

There are many good styleguides already available for Angular: [here](https://github.com/toddmotto/angular-styleguide) and [here](https://github.com/johnpapa/angular-styleguide)  So, why do we need another one?

This styleguide is different in that it is focused on teaching & learning Angular.  The syntax and structures advocated by other styleguides are great if you're already an Angular guru with plenty of experience, but can be extremely difficult for students new to Angular, with varying levels of JS knowledge to understand.  When we teach we generally increase complexity over time.  This guide aims to set guidelines to ensure that syntax across lessons is the same, while slowly introducing additional syntactical complexity.

**tldr; This style-guide is aimed at students new to Angular, not angular professionals.**

# Immediate Guidelines:
These guidelines are suggested for all code given to students.  

### prefix data- on attributes and directives

Use `data-` prefixed attributes instead of raw angular attributes/directives.  These pass HTML validators.

Avoid:

```html
 <div ng-controller="MainController as main">
```
Recommended:

```html
 <div data-ng-controller="MainController as main">
```

### controller as syntax

Use Controller as syntax, which promotes the demystifying use of dot syntax in HTML to indicate where variables come from.  This also means it is possible to safely use scalars on the controller.  You must also avoid the use of `$scope` and use `this` instead in your controllers.

Avoid:

```html
<div ng-controller="CustomerController">
    {{ name }}
</div>
```

Recommended:

```html
<div ng-controller="CustomerController as customer">
    {{ customer.name }}
</div>
```

### define named functions for each component
Declare named functions for controllers and other components.

Avoid:

```js
angular
  .module('app', [])
  .controller('MainController', function MainController () {

  })
  .service('SomeFactory', function SomeFactory () {

  });
```

Recommended:

```js
angular
  .module('app', [])
  .controller('MainController', MainController)
  .factory('SomeFactory', SomeFactory);


function MainController () {

}
function SomeFactory () {

}
 ```

### Use dot chaining to build the app rather than repetition of an app variable
In general multi-line dot-chaining is more prone to error, but since we're also avoiding in-line call-backs, the issue should be greatly reduced and overall readability should remain high.  This also follows what other style-guides suggest.  

Avoid:

```js
var app = angular.module('app', []);
app.controller('MainController', function() {
});
app.factory('SomeFactory', function() {
});
```

Recommended:

```js
angular
  .module('app', [])
  .controller('MainController', MainController)
  .factory('SomeFactory', SomeFactory);
```

### Capture `this` in a `vm` or other variable in controllers

The context of `this` could be changed in a function within a controller.  Capturing in another name avoids this issue.  Note how this rule works with controller-as.  
While many students will never run into an issue using just `this` in their controllers; this rule is trivial and might help them on projects.

Avoid:

```js
function PostsController() {
    this.title = 'Some Title';
}
```

Prefer:

```js
function PostsController() {
    var vm = this;
    vm.title = 'Some Title';
}
```


<!-- NAMING -->
# Naming

### Suffix should identify the type of module.

Use `mapController` rather than `map`. Use `phoneService` rather than `phone`

### Use UpperCamelCase for controllers, directives, etc.

Some style guides use UpperCamelCase such as `DoAwesomeController`.  This is primarily in preparation for ES6 and classes.  While not absolutely necessary, being consistent about this keeps it clear.

Avoid:

```
function libraryController() { }
function authorDirective() { }
```

Prefer:

```
function LibraryController() { }
function AuthorDirective() { }
```

### use long-form names for controllers and other components

Use `mapController` not `mapCtrl`.

### Filenames identify component separated by `.`

The syntax should be `{feature}.{component}.js` or `logger.service.js`, `library.controller.js`.






<!-- DIRECTIVES -->
# When working with custom directives
Use these guidelines when introducing and working with directives.

### Directives not controllers should manipulate the DOM.

In general DOM manipulations should be done in directives not controllers or services.  This excludes built-ins such as `ngShow`, `ngHide`, angular animations and templates.  CSS and animations can also be used independently.

### Controller-as in Directives

Remember to specify `controllerAs` in directives.  
It's also extremely common to use `vm` as the `controllerAs` name; _using another name may be disambiguating_ for students.

Avoid:

```js
function dragUploadDirective () {

  var directive = {
    controller: uploadController,
    scope: {
     city: '@'
    },
    template: '<div>{{prop}}</div>'
  };

  return directive;
}

```

Prefer:

```js
function dragUploadDirective () {
  var directive = {
    controllerAs: 'uploads', // note: often called 'vm' in the wild
    controller: uploadController,
    scope: {
     city: '@'
    },
    template: '<div>{{uploads.prop}}</div>'
  };

  return directive;
}
```

[code from](https://github.com/toddmotto/angular-styleguide#directives)

### Don't in-line functions in directives

Controllers should always be outside the directive.  One-liners may be OK, but in general separate functions.  


Avoid:

```js
function flowDirective() {
  var directive = {
    controller: ['$http', function($http) {
      // ...
    }],
    controllerAs: 'vm',
    ....
  }

    return directive;
  }
}
```


Prefer:

```js
function flowDirective() {
  var directive = {
    controller: flowSourceController,
    controllerAs: 'vm',
    ....
  }

    return directive;
  }
}

flowSourceController.$inject = ['$http']
function flowSourceController($http) { }
```

### Don't ng-* prefix directives.

This could conflict with newer versions.

Avoid:

```js
<div ng-upload>
```

Prefer:

```js
<div drag-upload>
```


### Custom directive elements do not need data-
Custom elements prefixed with `data-` won't pass validators.  (data- only applies to attributes.)

Note: All custom elements **must** contain a dash.

Avoid:

```js
<data-fancy-calendar>
```

Prefer:

```js
<fancy-calendar>
```


# When working with factories and services
Use these guidelines when introducing factories and services.

### Factories vs. Services

Use Factories.  This conforms with [John Papa's styleguide](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md#style-y040).  
Services and Factories are exceedingly similar and the differences are very tricky.  We don't need to teach both.

> It should be noted that Services are closer to the way we teach controllers and might therefore be easier for students.  An argument could be made for flipping this rule as long as it's done consistently.  

Service Pattern - Avoid:

```js
// service
function someService(){
	this.doSomething = function(){
		//…
	}
}
```

Factory Pattern - Prefer:

```js
// factory
function someFactory(){
	var dataObj = {};
	dataObj.doSomething = function(){
		//...
	}
	return dataObj;
}
```

### Do not call Factories services

Calling a Factory a Service or naming it `someService` is confusing.  The difference between the two is already [quite](http://stackoverflow.com/questions/14324451/angular-service-vs-angular-factory) [confusing](http://stackoverflow.com/questions/16596569/angularjs-what-is-a-factory) without mixing up the terminology.


# When introducing minification
Use these rules when working with MEAN or Rails+Angular stacks or anywhere else where you might encounter minification.  They can be introduced after students have gained some familiarity with Angular.

### Use $inject vs. inline annotation for dependency injection

Use `$inject` vs. inline annotation.  

This has better readability and lower likelihood of syntax errors.  Try to keep the `$inject` call near the function it refers to.

Avoid:

```js
angular
  .module('app')
  .controller('PhoneController', ['$location', '$routeParams', PhoneController]);

function PhoneController($location, $routeParams) {...}
```

Prefer:

```js
angular
  .module('app')
  .controller('PhoneController', PhoneController);

PhoneController.$inject = ['$location', '$routeParams'];
function PhoneController($location, $routeParams) {...}
```

> For a comparison see the [official angular tutorial](https://docs.angularjs.org/tutorial/step_05)


#### Consider using `ng-strict-di` with `$inject`

If you're following the above use of `$inject` ng-annotate is unnecessary.  However, you should consider using `ng-strict-di` to alert you to missing annotations.  [Reference](https://docs.angularjs.org/api/ng/directive/ngApp)

Prefer:

```js
<div ng-app="ngAppStrictDemo" ng-strict-di>
```



# Introduce eventually
These rules should be mentioned at some point, but not right away.

### mention (but don't use) $scope in controllers

Since we're using controller-as and `this` in our controllers, `$scope` will only rarely be used.  However, students are going to come across blogs, older code and stackoverflow posts that use `$scope` frequently.  We should mention how this works, at least in writing, but not right away.

> Note: when using $scope one should always pass objects, not scalars.
> Use `$scope.obj = {}` rather than `$scope.foo = 'adsf'`
