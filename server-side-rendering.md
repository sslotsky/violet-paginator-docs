# Server Side Rendering

If you are using [SSR](http://redux.js.org/docs/recipes/ServerRendering.html), there are two ways that you can specify preloaded data
to be rendered in the table: by leveraging the `initialize` action, or be passing at as component props.

## Preloading with `initialize()`

As documented in the section on [Composing Actions](composing_actions.md), the `composables` function can be used as a factory to
generate a list of action creators specific to your list. You can also specify some preloaded data within the `composables` call,
and then call the `initialize()` action. This will cause your data to be loaded into the reducer, so that when your component renders
on the client, it will not need to fetch the data:

```javascript
const pageActions = composables({ listId: 'posts', preloaded: payload })
return store.dispatch(pageActions.initialize())
```

## Component props

This method is less common, but preloaded data can also be passed directly into your decorated components for the same effect:

```javascript
import React, { Component } from 'react'
import { VioletPaginator, VioletDataTable } from 'violet-paginator'

const results = [{
  name: 'Ewe and IPA'
}, {
  name: 'Pouty Stout'
}]

const preloaded = {
  results: results.slice(0, 1),
  totalCount: 2
}

export default function App() {
  const props = {
    preloaded,
    listId: 'recipes'
  }

  const headers = [{
    field: 'name',
    text: 'Name',
    sortable: false
  }]

  return(
    <div>
      <VioletPaginator {...props} />
      <VioletDataTable {...props} headers={headers} />
    </div>
  )
}
```
