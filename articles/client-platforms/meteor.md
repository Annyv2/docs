---
title: Meteor Tutorial
name: Meteor
alias:
  - Meteor
language: 
  - Javascript
framework:
  - Meteor
image: /media/platforms/meteor.svg
tags:
  - quickstart
snippets:
  dependencies: client-platforms/meteor/dependencies
  setup: client-platforms/meteor/setup
  use: client-platforms/meteor/use
---

## Meteor Tutorial


<%= include('../_includes/package', {
  pkgRepo: 'meteor-auth0',
  pkgBranch: 'master',
  pkgPath: 'examples/auth0-meteor-sample',
  pkgFilePath: null,
  pkgType: 'server' + account.clientParam
}) %>

**If you have an existing application, follow the steps below.**

### 1. Installation

Install our Meteor package from [Atmosphere](https://atmospherejs.com/) on your meteor application by running this command on your project folder.

```
meteor add auth0:lock
``` 

__Note:__  this package is designed to work with Meteor 1.2.1, so please run `meteor update` in the root of your project if you're using an older version.

### 2. Before getting started

Create an `.env` file on the root of your application folder, and configure it with the following contents:

````bash
# .env file
AUTH0_CLIENT_ID=${account.clientId}
AUTH0_DOMAIN=${account.namespace}
````

Our meteor package reads this information and creates a Lock instance with these settings.

### 3. Implement the login

To implement the login, call the `.show()` method of Auth0's `lock` instance on the client of your Meteor project.

```js
#app.js file
if (Meteor.isClient) {
	
  Template.Auth0Login.events({
    'click button.login': function () {
      lock.show();
    },
    
  });
}
```

In this case we are using a Meteor Template called `Auth0Login`.  

To discover all the available arguments for `lock.show`, see [.show\(\[options, callback\]\)](/libraries/lock#-show-options-callback-).

### 4. Retrieve user information

You can retrieve information about the user's profile using the `currentUser` helper. The information will be under `currentUser.services.auth0`.

```html
#app.html

<template name="Auth0Login">
  {{#if currentUser}}
    <div>Your name is: <b>{{currentUser.services.auth0.name}}</b></div>
    <button class="logout">Log Out</button>
  {{else}}
    <button class="login">Log In</button>
  {{/if}}
</template>

```

You can access the user profile from the server through the `Meteor.user()` global getter, the information will be under the `services.auth0` object.

```js
var userName = Meteor.user().services.auth0.name;
```

To discover all the available properties of a user's profile, see [user-profile](/user-profile). Note that the properties available depend on the social provider used.

### 5. Log out

To log out you can just call `Meteor.logout();` on the client side of your application. 

```js
'click button.logout': function () {
      Meteor.logout();
 }
```

### 6. All done!

You have completed the implementation of Login and Signup with Auth0 and Meteor.
