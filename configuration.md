# Configuration

This package works out of the box if your server matches certain assumptions, but more often than not, there will be some differences. For this reason, `violet-paginator` allows users to override every single assumption that we make about communicating with the server.

## Global Configuration

Use the `configurePageParams` method to override defaults application wide. Example configuration:

```javascript
import { configurePageParams } from 'violet-paginator'

configurePageParams({
  perPage: 'results_per_page',
  sortOrder: 'sort_reverse',
  sortReverse: true // Means that a boolean will be used to indicate sort direction.
})

```

An example URL with this configuration:

```
https://brewed-dev.herokuapp.com/v1/recipes?page=6&results_per_page=15&sort=name&sort_reverse=true
```

Another example config:

```javascript
configurePageParams({
  perPage: 'page_size',
  sortOrder: 'direction'
})
```

And a corresponding example URL:

```
https://www.example.com/v1/users?page=6&page_size=15&sort=name&direction=asc
```

The complete list of configuration options and their defaults can be found in the [pageInfoTranslator](https://github.com/sslotsky/violet-paginator/blob/master/src/pageInfoTranslator.js):

Property Name | Default Value | Description
---|:---:|:---
page | `'page'` | The page number being requested
perPage | `'pageSize'` | The page size being requested
sort | `'sort'` | The field to sort by when requesting a page
sortOrder | `'sortOrder'` | The sort direction for the requested page
sortReverse | `false` | Use a boolean to indicate sort direction
totalCount | `'total_count'` | The name of the property on the server response that indicates total record count
results | `'results'` | The name of the property on the server that contains the page of results
id | `'id'` | The name of the property on the record to be used as the unique identifer

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