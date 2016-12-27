# Filtering

We provide list filtering functionality by exposing four different actions. Calling any of these actions will trigger a
request for the first page of data given the updated filters.

## setFilter(field, value)

Use this action to set a value for a single filter. As an example, you might have a checkbox that filters inactive items out of the data set.
You could define an action creator that uses `setFilter` like this:

```javascript
import api from 'ROOT/api'
import { composables } from 'violet-paginator'

export default function fetchRecipes(pageInfo) {
  ...
}

const pageActions = composables({ listId: 'recipes' })

export function showActive(showingActive) {
  return pageActions.setFilter('active', showingActive || undefined);
}
```

Note that in this case, we set the value to `undefined` when `showingActive` is false, which results in the removal of that filter from the URL.
You could set the filter to `false` instead if you actually wanted to see _only_ inactive recipes.

The component that uses our new action creator could look like this:

```javascript
import { Map } from 'immutable'
import React, { PropTypes } from 'react'
import { connect } from 'react-redux'
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
    filters: state.recipes.get('filters')
  }),
  { showActive: actions.showActive }
)(ShowActive)
```

Example URL's after filtering:

```
// after checking box
https://brewed-dev.herokuapp.com/v1/recipes?page=1&results_per_page=15&active=true

// after unchecking box
https://brewed-dev.herokuapp.com/v1/recipes?page=1&results_per_page=15
```

## setFilters(filters)

Call this action if you want to set multiple filters at once. The function accepts a plain JavaScript object with all the filters you wish to set. Perhaps you want to filter your list to exclude both inactive and outdated items:

```javascript
import api from 'ROOT/api'
import { composables } from 'violet-paginator'

export default function fetchRecipes(pageInfo) {
  ...
}

const pageActions = composables({ listId: 'recipes' })

export function showCurrentActiveItems() {
  return pageActions.setFilters({
    active: true,
    last_activity: { gt: '2015-10-31' }
  });
}
```
## resetFilters(filters)

This is similar to `setFilters` in that it sets multiple filters at once. However, this action will also clear any filters that are not specified by the `filters` argument. 

## toggleFilterItem(field, value)

Use this action if the filter you're maintaining represents an array of values. A possible use case might be filtering an employee list by one or more job titles:

```javascript
import api from 'ROOT/api'
import { composables } from 'violet-paginator'

export default function fetchRecipes(pageInfo) {
  ...
}

const pageActions = composables({ listId: 'recipes' })

export function toggleDevelopers() {
  return pageActions.toggleFilterItem('job_title', 'dev');
}

export function toggleExecutives() {
  return pageActions.toggleFilterItem('job_title', 'exec');
}
```
