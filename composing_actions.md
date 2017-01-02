# Composing Actions

`violet-paginator` is a plugin for redux apps, and as such, it dispatches its own actions and stores state in its own reducer.
To give you complete control of the pagination state, the API provides access to all of these actions via the
[composables](composables.md) function. This allows you the flexibility to call them directly as part of a more complex operation.
The most common use case for this would be [updating an item within the list](updating_items.md). 

As an example, consider a datatable where one column has a checkbox that's supposed to mark an item as active or inactive. Assuming that you have a `listId` of `'recipes'`, you could write an action creator like this to toggle the active state of a recipe within the list:

```javascript
import api from 'ROOT/api'
import { composables } from 'violet-paginator'

const pageActions = composables({ listId: 'recipes' })

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

Now you can bring this action creator into your connected component using `connect` and `mapDispatchToProps`:

```javascript
import { toggleActive } from './actions'

export function Recipes({ toggle }) {
  ...
}

export default connect(
  undefined,
  { toggle: toggleActive }
)(Recipes)
```

Finally, the `format` function for the `active` column in your data table might look like this:

```javascript
  const activeColumn = recipe => (
    <input
      type="checkbox"
      checked={recipe.get('active')}
      onClick={toggle}
    />
  )
```

