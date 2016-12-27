# Bulk Updating

In addition to allowing for [inline updating](updating_items.md), `violet-paginator` also exposes functions that can update every item within the list. This might be handy when implementing something like an inbox where you want to mark all messages as `read` or `important`. As with single item updating, bulk updating has both an immediate and an asynchronous version.

## Async update

### `updateItemsAsync(itemIds, data, update)`

Similar to [updateAsync](updating_items.md#async-update), but accepts a list of ids instead of a single id.

1. Call `updateItems` to merge the properties from the `data` argument into each specified item.
2. Call `updatingItems` to indicate that all specified items are waiting for an update from the server.
3. Call the `update` promise, provided as the last argument to `updateItemsAsync`.
4. If the `update` promise fails, use `resetResults` to revert the result list to its previous state. Then dispatch `markItemsErrored` to indicate that the items failed to update.

Example:

```javascript
import api from 'ROOT/api'
import { composables } from 'violet-paginator'
import * as actionTypes from './actionTypes'

const pageActions = composables({ listId: 'recipes' })

export function markAllRead(messageIds) {
  const data = {
    read: true
  }

  return pageActions.updateItemsAsync(
    messageIds,
    data,
    api.messages.markAllRead(messageIds, data)
  )
}
```

### `updateAllAsync(data, update, reset=false)`

This calls `updateItemsAsync` passing in all of the ids in the current results list.

1. Dispatch `updateItemsAsync` providing the ids for the current results list
2. If the `update` promise succeeds:
  3. With normal settings, call `updateItems` using the resolved data. This leaves it up to the success handler for the `update` promise to return the data that should be applied (or an empty object if it doesn't matter).
  4. With `reset=true`, reset the result list using the resolved data. This is for instances where the response from the server describes the updated list of records. The success handler for the `update` promise will return the list and `updateAllAsync` will dispatch the `resetResults` action to apply the update.

Example:

```javascript
import api from 'ROOT/api'
import { composables } from 'violet-paginator'
import * as actionTypes from './actionTypes'

const pageActions = composables({ listId: 'messages' })

export function markAllRead(messageIds) {
  const data = {
    read: true
  }

  return pageActions.updateAllAsync(
    data,
    api.messages.markAllRead(messageIds, data).then(() => {})  // return a blank object if the response content doesn't matter
  )
}
```

## Instant Update

### `updateItems(itemIds, data)`

Use the `updateItems` action to instantly apply the provided `data` to the items specified by the `itemIds`. Example:

```javascript
import { composables } from 'violet-paginator'

const pageActions = composables({ listId: 'recipes' })

export function toggleRead(recipeIds) {
  return pageActions.updateAll(
    recipeIds,
    { active: !recipe.get('active') }
  )
}
```

### `updateAll()` (will soon be deprecated)

Use the `updateAll` action to apply an instant update to every item in the list. Example:

```javascript
import { composables } from 'violet-paginator'

const pageActions = composables({ listId: 'messages' })

export function toggleRead() {
  return pageActions.updateAll(
    { active: !recipe.get('active') }
  )
}
```
