# Single List Configuration

You might want to override some defaults for a specific list rather than for all lists. For example, it may be the case that each endpoint that returns a list has a different property name to contain the results.

## Using Component Props

To demonstrate, let's modify the example from our [Quick Tutorial](introduction.md). We'll tell `violet-paginator` to look for our results within the `recipes` field of the server response:

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
      data: {
        total_count: resp.data.totalCount,
        results: resp.data.recipes
      }
    }))
  }
}
```

This technique is also useful in other ways. You can read more about them in the documentation on [Intercepting the Promise](intercepting_the_promise.md).