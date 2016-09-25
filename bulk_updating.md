# Bulk Updating

In addition to allowing for [inline updating](updating_items.md), `violet-paginator` also exposes functions that can update every item within the list. This might be handy when implementing something like an inbox where you want to mark all messages as `read` or `important`. As with single item updating, bulk updating has both an immediate and an asynchronous version.

## Instant Update

Use the `updateAll` action if you want to update every list item with a given list of properties. For example, assume you want an action creator that toggles the `read` state of all items:

```javascript
import { simpleComposables } from 'violet-paginator'

const pageActions = simpleComposables('messages')

export function toggleRead() {
  return pageActions.updateAll(
    { active: !recipe.get('active') }
  )
}
```

## Indicate Bulk Update in Process

## Bulk Update Error

## Async Bulk Update

```javascript
updateAllAsync(data, update, reset=false)
```

The `updateAllAsync` action combines all of the above into a single abstraction. This action performs the following operations:

1. Merge the properties from the `data` argument into each list item (uses `updateAll`). This will normally be the same data being submitted to the server, but can really be anything you want.
2. Update the state to indicate that the item is waiting for an update from the server (i.e. calls `updatingAll`)
3. Call the `update` function passed in as the second argument to `updateAllAsync`. 
4. If the `update` promise succeeds:
  5. With normal settings, call `updateAll` again using the resolved data. This leaves it up to the success handler for the `update` promise to return the data that should be applied (or an empty object if it doesn't matter).
  6. With `reset=true`, reset the result list using the resolved data. This is for instances where the response from the server describes the updated list of records. The success handler for the `update` promise will return the list and `updateAllAsync` will dispatch the `resetResults` action to apply the update.
7. If the `update` promise fails, use `resetResults` to revert the list to its previous state. Then dispatch `bulkError` to indicate that the request failed.

Example:

```javascript
import api from 'ROOT/api'
import { simpleComposables } from 'violet-paginator'
import * as actionTypes from './actionTypes'

const pageActions = simpleComposables(listId)

export function markAllRead() {
  const data = {
    read: true
  }

  return pageActions.updateAllAsync(
    data,
    api.recipes.update(data).then(() => {})  // return a blank object if the response content doesn't matter
  )
}
```

