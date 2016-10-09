# Global Configuration

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

