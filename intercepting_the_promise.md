# Intercepting the Promise

One way to adapt an API response for `violet-paginator` is to intercept the promise and translate it into the expected format. You can easily do this by adding a `then()` to the promise that your `fetch` function returns. Example:

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

      return resp
    })
  }
}
```

