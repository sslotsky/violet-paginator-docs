# Updating Items
If you want to be able to edit your records from within the list view, we've got you covered. `violet-paginator` exposes both synchronous and asynchronous actions that allow you to modify the properties of a list item, 


## Async Update

```javascript
updateAsync(id, data, update)
```

The `updateAsync` is intended to be your one-stop-shop for updates. This action executes the following operations:

1. Calls `updateItem` to merge the properties from the `data` argument. This will normally be the same data you submit to the server, but can really be anything you want.
2. Calls `updatingItem` to indicate that the item is waiting for an update from the server.
3. Call the `update` promise, provided as the last argument to `updateAsync`. 
4. If the `update` promise succeeds, call `updateItem` again using the resolved data.
5. If the `update` promise fails, use `resetItem` to revert the item to its previous state. Then dispatch `itemError` to indicate that the item failed to update.

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

Note that `violet-paginator` will handle your promise failure if it occurs. If you want to handle that error yourself, just make sure you throw it along to us:

```javascript
export function toggleActive(recipe) {
  const data = {
    active: !recipe.get('active')
  }

  const update = api.recipes.update(data).catch(err => {
    toastr.error('An error occured!')
    throw err
  })
  
  return pageActions.updateAsync(
    recipe.get('id'),
    data,
    update
  )
}
```

We expect that `updateAsync` will cover most of your use cases,you may desire more fine grained control. To provide this, we expose every operation that `updateAsync` performs so that you can organize them anyway you want to.

## Instant Update

```javascript
updateItem(itemId, data)
```

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

## Resetting an Item

While `updateItem` will merge in all provided data, `resetItem` will actually overwrite the item with this data. This is actually used by `violet-paginator` to revert items when promises fail for asynchronous updates. This is necessary because we update the item's data before calling the promise. If the promise fails, then, the item needs to snap back to its previous state. We achieve this by caching the current version of the item before calling the promise and feeding it to `resetItem` in the event of a failure. For an example, let's just look at how `violet-paginator` implements `updateAsync`:

```javascript
  const updateAsync = (itemId, data, update) =>
    (dispatch, getState) => {
      const item = getPaginator(getState(), id).get('results')
        .find(r => r.get(identifier) === itemId)

      dispatch(basic.updateItem(itemId, data))
      dispatch(basic.updatingItem(itemId))
      return update.then(serverUpdate =>
        dispatch(basic.updateItem(itemId, serverUpdate))
      ).catch(err => {
        dispatch(basic.resetItem(itemId, item.toJS()))
        return dispatch(basic.itemError(itemId, err))
      })
    }
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

