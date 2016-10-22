# VioletSortLink

This component requests the first page of data, sorted by a given field. The example below shows two sort links as they would appear in the [`VioletDataTable`](violetdatatable.md) component. The arrow in the `Name` column indicates that we have sorted the list by the `name` field in ascending order. Clicking the link would switch to descending order and flip the arrow.

![](Screen Shot 2016-10-22 at 2.56.12 PM.png)

In addition to the `listId` and the `fetch` function, `VioletSortLink` requires the `field` to sort by and the `text` to display.

```javascript
import { connect } from 'react-redux'
import { VioletSortLink } from 'violet-paginator'
import fetchRecipes from './actions'

export function TableHeader({ fetch }) {
  const props = {
    listId: 'recipes',
    fetch
  }
  
  return (
    <thead>
      <tr>
        <td>
          <VioletSortLink {...props} field="name" text="Name" />
          <VioletSortLink {...props} field="created_at" text="Date Created" />
        </td>
      </tr>
    </thead>
  )
}

export default connect(
  () => ({}),
  { fetch: fetchRecipes }
)(TableHeader)
```

See the section on [Single List Configuration](single_list_configuration.md) for optional configuration properties.