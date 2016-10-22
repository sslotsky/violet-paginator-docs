# Filtering

We provide list filtering functionality by exposing four different actions. Calling any of these actions will trigger a request for the first page of data given the updated filters.

## setFilter(field, value)

Use this action to set a value for a single filter. As an example, you might have a checkbox that filters inactive items out of the data set. You could define an action creator that uses `setFilter` like this:

```javascript
import api from 'ROOT/api'
import { composables } from 'violet-paginator'

export default function fetchRecipes(pageInfo) {
  ...
}

const pageActions = composables({
  listId: 'recipes',
  fetch: fetchRecipes
})

export function showActive(showingActive) {
  return pageActions.setFilter('active', showingActive || undefined);
}
```

Note that in this case, we set the value to `undefined` when `showingActive` is false, which results in the removal of that filter from the URL. You could set the filter to `false` instead if you actually wanted to see _only_ inactive recipes.

The component that uses our new action creator could look like this:

```javascript
import { Map } from 'immutable'
import React, { PropTypes } from 'react'
import { connect } from 'react-redux'
import { getPaginator } from 'violet-paginator'
import * as actions from './actions'

export function ShowActive({ filters, showActive }) {
  const showingActive = !!filters.get('active')
  const onClick = () => showActive(!showingActive)

  return (
    <label>
      Show only active recipes:
      <input type="checkbox" checked={showingActive} onClick={onClick} />
    </label>
  )
}

ShowActive.propTypes = {
  filters: PropTypes.instanceOf(Map).isRequired,
  showActive: PropTypes.func.isRequired
}

export default connect(
  state => ({
    filters: getPaginator(state, 'recipes').get('filters')
  }),
  { showActive: actions.showActive }
)(ShowActive)
```


## setFilters(filters)



## resetFilters(filters)

## toggleFilterItem(field, value)