# The PaginationWrapper

Calling the `paginate` function on your component will wrap it in a component called the `PaginationWrapper`, which injects all of the props your custom component needs to interact with `violet-paginator`. As an example, see the `Flipper` component below:

```javascript
import React, { PropTypes } from 'react'
import classNames from 'classnames'
import paginate from './PaginationWrapper'
import { Prev } from './Prev'
import { Next } from './Next'

export function Flipper(props) {
  const prevClasses = classNames({ disabled: !props.hasPreviousPage })
  const nextClasses = classNames({ disabled: !props.hasNextPage })

  return (
    <ul className="pagination">
      <li className={prevClasses}>
        <Prev {...props} />
      </li>
      <li className={nextClasses}>
        <Next {...props} />
      </li>
    </ul>
  )
}

Flipper.propTypes = {
  hasPreviousPage: PropTypes.bool,
  hasNextPage: PropTypes.bool
}

export default paginate(Flipper)
```

The `hasPreviousPage` and `hasNextPage` properties are both injected simply by calling `paginate`. 

## Required Props
To render a component that's wrapped in a `paginate` call, you must provide a couple of properties:

Property Name | Required | Description
---|:---|---
`listId`| true | A unique identifier for the relevant list
`fetch`| true | The function used to fetch results


## Injected Props

Property Name | Description
---|---
`actions`|A set of actions targeting the relevant list
`paginator`|The pagination state for the relevant list
`hasPreviousPage`|Indicates whether the current page has a previous page
`hasNextPage`|Indicates whether the current page has a next page
`currentPage`|Current page number
`pageSize`|Maximum number of results per page
`results`|The records that make up the list
`isLoading`|Indicates that the list is being fetched
`updating`|Indicates which items within the list are being updated
`removing`|Indicates which items within the list are being removed
`totalPages`|The total number of pages
