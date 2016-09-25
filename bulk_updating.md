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