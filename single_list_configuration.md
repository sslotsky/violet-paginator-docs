# Single List Configuration

You might want to override some defaults for a specific list rather than for all lists. For example, it may be the case that each endpoint that returns a list has a different property name to contain the results.

## Using Component Props
To demonstrate, let's use the example from our [Quick Tutorial](introduction.md), but this time we'll tell `violet-paginator` that our results are contained in the `recipes` field of the server response:

```javascript
  const config = {
    fetch,
    listId: 'recipes',
    resultsProp: 'recipes'
  };

  const paginator = (
    <VioletPaginator {...config} />
  )

  return (
    <section>
      {paginator}
      <VioletDataTable {...config} />
      {paginator}
    </section>
  )
```


Note that we can also add a `totalCountProp` property to the `config` above if our server data uses a different field name to store that information. We can also set an initial page size and initial filters.

```javascript
  const config = {
    fetch,
    listId: 'recipes',
    resultsProp: 'recipes',
    totalCountProp: 'totalRecipes',
    pageSize: 25,
    filters: { active: true }
  };
```

Resulting URL for first page:

```
https://brewed-dev.herokuapp.com/v1/recipes?page=1&results_per_page=25&active=true
```

## Intercepting the Promise

Another way to adapt an API response for `violet-paginator` is to intercept the promise and translate it into the expected format. You can easily do this by adding a `then()` to the promise that your `fetch` function returns. Example:

```javascript
export default function fetchRecipes(pageInfo) {
  return () => {
    return api.recipes.index(pageInfo.query).then(resp => ({
      total_count: resp.data.totalCount,
      results: resp.data.recipes
    }))
  }
}
```

This technique is also useful if you want `violet-paginator` to manage a non-paginated list. In cases like these, the server will typically return a simple array, so you could translate it for `violet-paginator` like this:

```javascript
export default function fetchRecipes() {
  return () => {
    return api.recipes.index().then(resp => ({
      total_count: resp.data.length,
      results: resp.data
    }))
  }
}
```

Intercepting the promise is also helpful if you want to save some bit of server data in your own reducer. For example, an endpoint might return more information than just the `total_count` and the `results`. Perhaps an advanced search endpoint also reports the unfiltered count:

```javascript
export default function fetchRecipes(pageInfo) {
  return dispatch => {
    return api.recipes.index(pageInfo.query).then(resp => {
      dispatch({ type: actionTypes.SET_UNFILTERED_COUNT, count: resp.data.unfiltered_count })

      return resp;
    });
  }
}
```