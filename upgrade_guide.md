# Upgrading From Version 1

Version 2.0.0 came with a few breaking changes, but upgrading should be painless with the tips below.

## Mounting the Reducer

The main change in 2.0.0 is in how state is managed internally. Previously, `violet-paginator` used a single `pagination` reducer that managed
a list of paginators. Now, we use
[higher order reducers](http://redux.js.org/docs/recipes/reducers/ReusingReducerLogic.html#customizing-behavior-with-higher-order-reducers),
meaning that you will have one reducer for each paginated list in your app.

**Old Way**

```javascript
import { pagination } from 'violet-paginator'

export default combineReducers({
  pagination
})
```

**New Way**

```javascript
import { createReducer } from 'violet-paginator'

export default combineReducers({
  // Without a second argument, violet-paginator expects reducer to be mounted at state.recipes
  recipes: createReducer('recipes'),
  // If the reducer name doesn't match the listId, provide a locator function that tells violet-paginator
  // where to get the reducer with the given listId
  clients: createReducer('customers', state => state.clients)
})
```
## Accessing the State

Because all of the paginated lists were previously managed in a single reducer, a list had to be looked up by the `listId`, whereas
now we have much more direct access.

**Old Way**

```javascript
import { getPaginator } from 'violet-paginator'

export default connect(
  state => ({ results: getPaginator(state, 'recipes').get('results') })
)(RecipeGrid)
```

**New Way**

```javascript
export default connect(
  state => ({ results: state.recipes.get('results') })
)(RecipeGrid)
```

## Rendering Pre-Made Components

In Version 1, a `fetch` function was a required prop for every one of our premade components. In Version 2, the `fetch` is specified
during the initial setup, so you never have to worry about it again. Our components therefore no longer require this property.

## Decorating Custom Components

In Version 1, there was one decorator to rule them all, and it was called `paginate`. This decorator injected every prop that your custom
component could possibly need. Version 2 comes with a number of different decorators for cases when your components don't require all of the props.

```javascript
// Supports 'previous' and 'next' links
export defaut decorators.flip(MyFlipperComponent)

// Supports full pagination controls
export default decorators.paginate(MyPaginationComponent)

// Supports grids/datatables
export default decorators.dataGrid(MyDataGridComponent)

// Supprts controls for changing the page size
export default decorators.pageSizer(MyPageSizeDropdown)

// Supports a control for sorting the list by the field name
export default decorators.sorter(MySortLink)

// The kitchen sink! Injects properties from all decorators
export default decorators.paginatedGrid(MyPaginatedGridComponent)
```

For more on using decorators or creating your own, [check the docs on decorators](the_paginationwrapper.md).

## Composing Actions

Previously, `violet-paginator` exported `composables` and `simpleComposables` to help you create complex action creators
built on top of ours. There are two notable changes to this scheme:

1. `simpleComposables` will no longer be exported.
2. `composables` no longer requires a fetch function.

Composing actions now entails a call to `composables`, which requires a `listId` property: 

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
