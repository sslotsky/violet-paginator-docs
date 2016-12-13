# Server Side Rendering

If you are using [SSR](http://redux.js.org/docs/recipes/ServerRendering.html), you can supply your paginated components with a page of preloaded data using the `preloaded` prop:

```javascript
import React, { Component } from 'react'
import { VioletPaginator, VioletDataTable } from 'violet-paginator'
import { connect } from 'react-redux'
import * as actions from './actions'

const results = [{
  name: 'Ewe and IPA'
}, {
  name: 'Pouty Stout'
}]

const preloaded = {
  results: results.slice(0, 1),
  totalCount: 2
}

export function App({ fetch }) {
  const props = {
    pageSize: 1,
    totalCountProp: 'totalCount',
    fetch,
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

export default connect(
  undefined,
  { fetch: actions.fetch }
)(App)
```
