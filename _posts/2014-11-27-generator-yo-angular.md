---
layout:     post
title:      "generator-yo-angular"
subtitle:   "A yeoman generator for AngularJs project."
date:       2014-11-27 12:00:00
author:     "bob76828"
---

# A Yeoman generator for AngularJs project #

This is a yeoman generator for AngularJs project.

It takes advantage of the gulp build tool and makes use of bower and npm for dependency management.

[Fork me on GitHub](https://github.com/bob76828/generator-yo-angular)

-----

## GENERATED DIRECTORY STRUCTURE ##

    assets/
      app/
        main/
          content.html
          mainCtrl.js
        app.js
        initializer.js
        route.js
      common/
        controllers/
          headerCtrl.js
          directives/
        layout/
          footer.html
          header.html
          layout.html
        models/
        partials/
        services/
      css/
        includes/
          _main.scss
        app.scss
      images/
      vendor/
      index.html
    bower_components/    
    node_modules/
    .bowerrc
    .editorconfig
    .gitignore
    .jshintrc
    bower.json
    gulpfile.js
    package.json

-----

## FEATURES ##
- follows the project structure for [angular seed][1]
- using 'gulp' command to develop app in development environment
- using 'gulp production' command to release app in production environment
- after run 'gulp production' command, all app files are compiled into release folder
- a static server is run at port 3000 with livereload support

-----

## Prerequisites ##
- node.js [http://nodejs.org/][2]
- npm [http://www.npmjs.org/][3]
- bower [http://bower.io/][4]
- gulp.js [http://gulpjs.com/][5]
- yo [https://github.com/yeoman/yo][6]
- yeoman-generator [https://github.com/yeoman/generator-generator][7]

-----

## Generators ##

Available generators:

* yo-angular
* yo-angular:page
* yo-angular:directive
* yo-angular:model]
* yo-angular:controller
* yo-angular:service

**Note: Generators are to be run from the root directory of your app.**

### App ###
Sets up a new AngularJS app, generating all the boilerplate you need to get started.
  
Example:

```bash
yo yo-angular
```
  
### Page ###
Generates a controller and view in assets/app/.
  
Example:

```bash
yo yo-angular:page
```

Produces `assets/app/user/userCtrl.js`:

```javascript
"use strict";

angular.module('ng.controller').controller('userCtrl' ,
  function($scope) {

  }
);

```

Produces `assets/app/user/content.html`:

```html
<div>
  user
</div>
```

  
### Directive ###
Generates a directive in assets/common/directives/.
  
Example:

```bash
yo yo-angular:directive
```
  
Produces `assets/common/directives/user.js`:

```javascript
"use strict";

angular.module('ng.directive').directive('user', [function () {
  return {
    restrict: 'A',
    link: function (scope) {

    },
    controller: function($scope){

    }
  };
}]);
```
  
### Model ###
Generates a model in assets/common/models/.
  
Example:

```bash
yo yo-angular:model
```
  
Produces `assets/common/models/userModel.js`:

```javascript
"use strict";

angular.module('ng.model').factory('userModel', ['$resource', function($resource) {
  function userModel() {
    this.url = '';
    return $resource(this.url, {id: '@id'},{
      'get':    {method:'GET','url':this.url},
      'getAll':  {method:'GET', isArray:true,'url':this.url},
      'update': {method:'PUT','url':this.url},
      'remove': {method:'DELETE','url':this.url}
    });
  }
  return new userModel();
}]);

```
  
### Controller ###
Generates a controller in assets/common/controllers/.
  
Example:

```bash
yo yo-angular:controller
```
  
Produces `assets/common/controllers/userCtrl.js`:

```javascript
"use strict";

angular.module('ng.controller').controller('userCtrl' ,
  function($scope) {

  }
);

```
  
### Service ###
Generates a service in assets/common/services/.

Example:

```bash
yo yo-angular:service
```
  
Produces `assets/common/services/userService.js`:

```javascript
"use strict";

angular.module('ng.service').controller('userService' ,
  function userService() {

  }
  return new userService();
);

```
  
----

## USAGE ##
1) npm install -g bob76828/generator-yo-angular

2) mkdir myApp && cd myApp && yo yo-angular

3) gulp

6) start developing

----

## Support ##
For questions and issues: [https://github.com/bob76828/generator-yo-angular/issues][8]


  [1]: https://github.com/angular/angular-seed
  [2]: http://nodejs.org/
  [3]: http://www.npmjs.org/
  [4]: http://bower.io/
  [5]: http://gulpjs.com/
  [6]: https://github.com/yeoman/yo
  [7]: https://github.com/yeoman/generator-generator
  [8]: https://github.com/henyojess/generator-gulp-ng/issues