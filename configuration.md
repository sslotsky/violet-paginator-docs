# Configuration

This package works out of the box if your server matches certain assumptions, but more often than not, there will be some differences. For this reason, `violet-paginator` allows users to override every single assumption that we make about communicating with the server.

## [Global Configuration](global_configuration.md)

## Single List Configuration

You might want to override some defaults for a specific list rather than for all lists. For example, it may be the case that each endpoint that returns a list has a different property name to contain the results.

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