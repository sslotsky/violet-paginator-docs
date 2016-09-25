# Updating Items

If you want to be able to edit your records from within the list, we've got you covered. `violet-paginator` exposes actions that allow you to modify the properties of a list item, 

## Instant Update

Use the `updateItem` action if you want to update the values of a list item's properties. For example, assume you want an action creator that toggles the `active` state of a list item:

```javascript
import { simpleComposables } from 'violet-paginator'

const pageActions = simpleComposables('recipes')

export function toggleActive(recipe) {
  return pageActions.updateItem(
    recipe.get('id'),
    { active: !recipe.get('active') }
  )
}
```

## Indicate Updating State

In some cases you may want to indicate that an item within a list is waiting for an update from the server. Dispatching the `updatingItem` action will set the appropriate state, which can be checked with the `isUpdating` function.

### Setting the state

```javascript
import { simpleComposables } from 'violet-paginator'

const pageActions = simpleComposables('recipes')

export function updatingRecipe(recipe) {
  return pageActions.updatingItem(recipe.get('id'))
}
```

### Checking the state

```javascript
import { isUpdating } from 'violet-paginator'
import { connect } from 'react-redux'

export default connect(
  (state, ownProps) => ({
    updating: isUpdating(state, 'recipes', ownProps.params.id)
  })
)(Recipe)
```

## Indicate Item in Error

If there was a problem updating an item on the server, you may wish to inform the user by styling the corresponding row in the table.

### Setting the state

```javascript
import { simpleComposables } from 'violet-paginator'

const pageActions = simpleComposables('recipes')

export function recipeError(recipe, error) {
  return pageActions.itemError(recipe.get('id'), error)
}
```

### Checking the state

## Async Update

```javascript
updateAsync(id, data, update)
```

The `updateAsync` action combines all of the above into a single abstraction. This action performs the following operations:

1. Merge the properties from the `data` argument into the item (uses `updateItem`). This will normally be the same data being submitted to the server, but can really be anything you want.
2. Update the state to indicate that the item is waiting for an update from the server (i.e. calls `updatingItem`)
3. Call the `update` function passed in as the last argument to `updateAsync`. 
4. If the `update` promise succeeds, call `updateItem` again using the resolved data.
5. If the `update` promise fails, use `updateItem` to revert the item to its previous state. Then dispatch `itemError` to indicate that the item failed to update.

Example:

```javascript
import api from 'ROOT/api'
import { simpleComposables } from 'violet-paginator'
import * as actionTypes from './actionTypes'

const pageActions = simpleComposables(listId)

export function toggleActive(recipe) {
  const data = {
    active: !recipe.get('active')
  }

  return pageActions.updateAsync(
    recipe.get('id'),
    data,
    api.recipes.update(data)
  )
}
```

