---
title: Routing
---

## Adding Routes

Here's how you can add routes to your app (using React Router):

```js
import Foo from './foo.jsx';

addRoute({
  name: 'foo',
  path: '/foo',
  component: Foo
});
```

If on the other hand you've previously registered a component with `registerComponent`, you can simply specify the `componentName` property instead:

```js
addRoute({
  name: 'foo',
  path: '/',
  componentName: 'Foo'
});
```

Finally, to change the index (`/`) route, you can just do:

```js
addRoute({
  name: 'foo',
  path: '/',
  component: Foo
});
```

## Child Routes

You can declare a new route as a child of an existing one using the following syntax:

```js
addRoute({
  name: 'foo',
  path: '/',
  component: Foo
}, 'nameOfParentRoute');
```

## Custom Layouts

By default, Vulcan will use the `Components.Layout` component as a layout. However, you can also specify a different layout on a per-route basis: 

```js
addRoute({
  name: 'foo',
  path: '/',
  component: Foo,
  layoutName: 'AdminLayout'
});
```

Note that this supposes you've previously registered the `AdminLayout` component using `registerComponent`.

## Dynamic Imports

Vulcan supports dynamically loading specific routes, meaning their content will not be included in the original JavaScript bundle but instead will only be loaded when the user actually visits that route.

The following static route:

```js
import Admin from './Admin.jsx';

addRoute({
  name: 'admin',
  path: '/admin',
  component: Admin
});
```

Can be changed to a dynamic route using `getDynamicComponent` and `import(...)`:

```js
import { getDynamicComponent } from 'meteor/vulcan:core';

addRoute({
  name: 'admin',
  path: '/admin',
  component: () => getDynamicComponent(import('./Admin.jsx'))
});
```

Note that components are imported as soon as the `import()` statement runs. This is why it's important to wrap the `getDynamicComponent()` block in a function call (`() => ...`) to delay its execution and ensure dynamic components are not loaded prematurely. 

Learn more about [dynamic imports here](https://blog.meteor.com/announcing-meteor-1-5-b82be66571bb).

## Overriding Routes

You might sometimes need to override an existing route from another package. To do so, you can simply redeclare a route of the same name. If this is not possible for some reason (for example, it's a dynamically loaded route that uses a local path), you can also override specific route properties like this:

```js
import { RoutesTable } from 'meteor/vulcan:core';

RoutesTable.admin.layoutName = 'AdminLayout';
```

## Update Callbacks

A common pattern with React Router is running callbacks after the route update (to enable things like custom scrolling behavior, for example).

You can use the `router.onUpdate` callback hook to add your own callbacks:

```js
addCallback('router.onUpdate', sendGoogleAnalyticsRequest);
```

## Using React Router In Your Components

If you need to access router properties (such as the current route, path, or query parameters) inside a component, you'll need to wrap that component with the `withRouter` HoC (higher-order component):

```js
import React, { PropTypes, Component } from 'react';
import { withRouter } from 'react-router'

class SearchForm extends Component{

  render() {
    // this.props.router is accessible
  }
}

export default withRouter(SearchForm);
``` 

#### Alternative Approach

React Router is initialized in the `vulcan:routing` package, and the routing API lets you add routes without having to modify the package's code. However, for more complex router customizations you can also disable the `vulcan:routing` package altogether and replace it with your own React Router code. 
