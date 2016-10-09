# Quick Tutorial

## Objectives

This tutorial demonstrates the most basic usage of `violet-paginator`. We will retrieve pages of records from the server and display them with pagination controls using nothing more than a `fetch` function, a `listId`, and some of our pre-made components.

## Initial Setup

Start by downloading the package to your project.

```
npm i --save violet-paginator
```

Next, you'll need to mount the reducer like in the example below. **Note:** it's important not to rename the reducer. This package expects to find it at `state.pagination`.

```javascript
import { pagination } from 'violet-paginator'
import { combineReducers } from 'redux'
import users from './users/reducer'

export default combineReducers({
  users,
  pagination
})
```

## Fetching

In order for `violet-paginator` to fetch a page of records, users need to supply a `fetch` function. The only hard requirement is that the function returns a promise. When `violet-paginator` calls your `fetch` function, it will supply a `pageInfo` object. This object contains a `query` property, which contains the request parameters for pagination and filtering, if applicable. Example fetch function:

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

By default, `violet-paginator` will make some assumptions about your server data. Namely, it assumes that the response contains a `total_count` property indicating the total number of records across all pages, and a `results` property that contains a single page of records. **If your server uses differently named properties**, `violet-paginator` provides a few different ways to override these defaults. You can read about them in the [configuration]() section.
