# Using Decorators

Calling any of our decorator functions on your component will wrap it in a component called the `PaginationWrapper`. This process adds properties
to your component to allow it to interact with `violet-paginator`, and also adds lifecycle hooks so that a new page of results will be requiested
at the appropriate time. As an example, see the `Flipper` component below:

```javascript
import React, { PropTypes } from 'react'
import classNames from 'classnames'
import { flip } from './decorators'
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

export default flip(Flipper)
```

The `hasPreviousPage` and `hasNextPage` properties are both injected simply by calling `flip`. 

## flip()

The `flip()` decorator is for simple 'previous' and 'next' links.

Injected Property | Description
---|:---
`pageActions`|A set of actions targeting the relevant list
`paginator`|The pagination state for the relevant list
`hasPreviousPage`|Indicates whether the current page has a previous page
`hasNextPage`|Indicates whether the current page has a next page

## paginate()

The `paginate()` decorator is for full pagination controls, including specific page links as well as 'previous' and 'next' links.

Injected Property | Description
---|:---
`pageActions`|A set of actions targeting the relevant list
`paginator`|The pagination state for the relevant list
`hasPreviousPage`|Indicates whether the current page has a previous page
`hasNextPage`|Indicates whether the current page has a next page
`currentPage`|Current page number
`totalPages`|The total number of pages

## tabulate()

The `tabulate()` decorator is for components that render the results list.

Injected Property | Description
---|:---
`pageActions`|A set of actions targeting the relevant list
`paginator`|The pagination state for the relevant list
`results`|The records that make up the list
`isLoading`|Indicates that the list is being fetched
`updating`|Indicates which items within the list are being updated
`removing`|Indicates which items within the list are being removed

## stretch()

The `stretch()` decorator is for a component that can change the page size.

Injected Property | Description
---|:---
`pageActions`|A set of actions targeting the relevant list
`paginator`|The pagination state for the relevant list
`pageSize`|Maximum number of results per page

## sort()

The `sort()` decorator is for a component that sorts the list by a given field.

Injected Property | Description
---|:---
`pageActions`|A set of actions targeting the relevant list
`paginator`|The pagination state for the relevant list
`sort`|The field to sort by
`sortReverse`|Indicates whether to sort in reverse order


## violatePaginator()

The kitchen sink of decorators. This includes all the properties injected by the decorators listed above.

Injected Property | Description
---|:---
`pageActions`|A set of actions targeting the relevant list
`paginator`|The pagination state for the relevant list
`pageSize`|Maximum number of results per page
`results`|The records that make up the list
`isLoading`|Indicates that the list is being fetched
`updating`|Indicates which items within the list are being updated
`removing`|Indicates which items within the list are being removed
`totalPages`|The total number of pages
`sort`|The field to sort by
`sortReverse`|Indicates whether to sort in reverse order

## decorate

Allows you to create your own decorator. This function accepts two arguments:

1. The component class or function to decorate
2. A `decorator` function that accepts the component's `props` as an argument and returns an object.

An example `decorator` function to mimick our own `flip()` decorator would look like this:

```javascript
export const flipper = props => {
  const { paginator } = props

  const totalPages =
    Math.ceil(paginator.get('totalCount') / paginator.get('pageSize'))

  const page = paginator.get('page')

  return {
    hasPreviousPage: page > 1,
    hasNextPage: page < totalPages
  }
}
```

Putting it all together, to use this `decorator` with the `flipper` function, you might define a node module like this:

```javascript
import { decorators } from 'violet-paginator'
import { flipper }  from './myCustomSelectors/flipper'

export default function flip(Component) {
  return decorators.decorate(Component, flipper)
}
```

You could then use your decorator to decorate your custom flipper component:

```javascript
import React from 'react'
import flip from './myCustomDecorators/flip'

export function MyCustomFlipper({ hasPreviousPage, hasNextPage }) {
  ...
})

export default flip(MyCustomFlipper)
```
