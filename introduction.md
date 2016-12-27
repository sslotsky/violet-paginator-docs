# Quick Tutorial

## Objectives

This tutorial demonstrates the most basic usage of `violet-paginator`. We will retrieve pages of records from the server and display them with pagination controls using nothing more than a `fetch` function, a `listId`, and some of our pre-made components.

## Initial Setup

Start by downloading the package to your project.

```
npm i --save violet-paginator
```

Next, you'll need to mount a reducer like in the example below.

```javascript
import { createPaginator } from 'violet-paginator'
import { combineReducers } from 'redux'
import users from './users/reducer'
import { fetch } from './recipes/actions'

export default combineReducers({
  users,
  recipes: createPaginator({
    listId: 'recipes', // expects reducer to be mounted at state.recipes
    fetch
  })
})
```

**Take note** how the `listId` matches the reducer name (they're both called `recipes`). This is default usage.
If you don't want them to match, you'll need to specify a locator that specifies how to resolve the reducer.

```javascript
export default combineReducers({
  users,
  recipes: createPaginator({
    listId: 'recipesList',
    locator: state => state.recipes, // locator function accepts the state and returns the correct reducer
    fetch
  })
})
```

[Look here](create_paginator.md) for more details on using `createPaginator`.

## Fetching

In order for `violet-paginator` to fetch a page of records, users need to supply a `fetch` function.
The only hard requirement is that the function returns a promise. When `violet-paginator` calls your `fetch` function,
it will supply a `pageInfo` object. This object contains a `query` property, which contains the request parameters for pagination.
It also contains filtering and sorting parameters, if applicable. Example fetch function:

```javascript
import api from 'ROOT/api'
import * as actionTypes from './actionTypes'

export default function fetchRecipes(pageInfo) {
  return () => {
    return api.recipes.index(pageInfo.query)
  }
}
```

### Assumptions

By default, `violet-paginator` will make some assumptions about your server data. Namely, it assumes that the response contains two properties:

1. A `total_count` property indicating the total number of records across all page
2. A `results` property that contains a single page of records. 

**If your server uses differently named properties**, `violet-paginator` provides a few different, easy ways to override these defaults.
You can read about them in the [configuration](configuration.md) section.


## Displaying

With the reducer created, we just pass the corresponding `listId` into our premade components:

```javascript
import React, { PropTypes } from 'react'
import { I18n } from 'react-redux-i18n'
import { VioletDataTable, VioletPaginator } from 'violet-paginator'

export default function Grid() {
  const headers = [{
    field: 'name',
    text: I18n.t('recipes.name')
  }, {
    field: 'created_at',
    text: I18n.t('recipes.created_at')
  }, {
    field: 'boil_time',
    sortable: false,
    text: I18n.t('recipes.boil_time')
  }]

  const paginator = (
    <VioletPaginator listId="recipes" />
  )

  return (
    <section>
      {paginator}
      <VioletDataTable listId="recipes" headers={headers} />
      {paginator}
    </section>
  )
}
```

That's it! You now have a datatable with full pagination controls above and beneath.

### Quick Note on Styling

Our components were built to be styled with [fontawesome](http://fontawesome.io/) and the
[violet css framework](https://github.com/kkestell/violet).
If you wish to use these styles, it's up to you to include them in your project. `violet` is available via
[CDN](https://cdn.jsdelivr.net/violet/0.0.1/violet.min.css), but can also easily be
[downloaded from github](https://github.com/kkestell/violet/blob/master/build/violet/violet.min.css) and dropped into your project.
