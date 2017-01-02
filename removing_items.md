# Removing Items

## Async Removal

```javascript
removeAsync(itemId, remove)
```

List views often have a control that allows users to delete an item. To support this, `violet-paginator` exposes a `removeAsync` action. This accepts the id of the item to remove, and a `remove` promise representing the call to the server to delete. It then performs the following operations:

1. Call `removingItem` to indicate that the item is waiting for removal. You can react to this state with the `isRemoving` function. This is useful if you want to show some in-progress state or filter the item out prematurely.
2. If the `remove` promise succeeds, call `removeItem` which will remove the item from the list.
3. If the `remove` promise fails:
  4.  Revert the item using `resetItem`. If you were reacting to the `isRemoving` state in step 1, this step will revert that state. That means that your item will come back if you were filtering it out prematurely.
  4. Call `itemError` to record the failure to remove that item.


Example usage:

```javascript
import api from 'ROOT/api'
import { composables } from 'violet-paginator'
import * as actionTypes from './actionTypes'

const pageActions = composables({ listId: 'recipes' })

export function deleteRecipe(recipe) {
  const id = recipe.get('id')
  
  return pageActions.removeAsync(
    id,
    api.recipes.delete(id)
  )
}
```

## Removal in Progress

If you want to check for a removal in progress, use the `isRemoving` function:

```javascript
import React, { PropTypes } from 'react'
import { connect } from 'react-redux'
import { isRemoving } from 'violet-paginator'

export function ListItem({ isRemoving }) {
  ...
})

export default connect(
  (state, ownProps) => ({ isRemoving: isRemoving(state, 'myList', ownProps.itemId })
)(ListItem)
```

