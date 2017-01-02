# Single List Configuration

You might want to override some defaults for a specific list rather than for all lists. For example,
it may be the case that each endpoint that returns a list has a different property name to contain the results.

## During Initial Setup

You can specify a number of overrides for a given list during the `createPaginator` call at the initial setup:

```javascript
export default combineReducers({
  users,
  recipes: createPaginator({
    listId: 'recipes',
    fetch,
    initialSettings: {
      pageSize: 25, // Request 25 records per page
      sort: 'name', // Tell the server to sort records by the name field
      filters: {
        active: true // Tell the server to return only active records
      }
    },
    pageParams: {
      resultsProp: 'recipes', // Property of server response that contains the results
      totalCountProp: 'total' // Property of server response that contains the total record count
    }
  })
})
```

Resulting URL for first page:

```
https://brewed-dev.herokuapp.com/v1/recipes?page=1&results_per_page=25&active=true
```

## Intercepting the Promise

Another way to adapt an API response for `violet-paginator` is to intercept the promise and translate it into the expected format.
You can easily do this by adding a `then()` to the promise that your `fetch` function returns. Example:

```javascript
export default function fetchRecipes(pageInfo) {
  return () => {
    return api.recipes.index(pageInfo.query).then(resp => ({
      data: {
        total_count: resp.data.totalCount,
        results: resp.data.recipes
      }
    }))
  }
}
```

This technique is also useful in other ways. You can read more about them in the documentation on [Intercepting the Promise](intercepting_the_promise.md).
