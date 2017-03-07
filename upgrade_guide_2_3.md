# Upgrading From Version 2

If you are coming from version 1, please [read this first](upgrade_guide.md) for instructions.

Version 3.0.0 comes with some new features and performance improvements. We've also changed some state management details
and made some tweaks to how async updates are performed. All of these are described briefly in this document, and more
extensively in the relevant sections.

Please note that most of these changes involve inline updating, which is not used in the majority of cases. If all you're doing
is paginating and filtering, a great deal of this information will not apply to you.

## New / Improved Features

### Better Performance for VioletDataTable

Two now mechanisms were introduced to make inline updating more performant: the `tabulateLean()` decorator and the `DataRow` component.
These are both utilized by `VioletDataTable` so that when an async update is being performed on a row, only that row will re-render,
with the others being completely unaffected.

Rather than injecting the current page of results, `tabulateLean()` injects only the `ids`, and prevents re-renders unless those `ids`
have changed (e.g. if a different page has been requested). You can then iterate over the `ids` and pass each into a `DataRow` component
to render the rows. The source code for `VioletDataTable` is an excellent reference to see these both in action. You can also read more
about them [here](lean_update_table.md).

### Augmenting Reducers!

You might want to manage some additional state that's relevant to your paginated list. In previous versions, you would have to
keep a separate reducer that's tied to your pagination list to accomplish this. Not anymore. You can now add custom properties
and reducer handlers to your pagination reducers. You can do this during your `createReducer` call by adding additional properties
to the `initialSettings` object, and then declaring an `augmentedWith` object that contains your reducer handlers. This small unit
test demonstrates this capability pretty well:

```javascript
describe('pagination reducer', () => {
  context('when augmented', () => {
    it('responds to given actions', () => {
      const config = {
        listId: 'customized',
        initialSettings: {
          customField: undefined
        },
        augmentWith: {
          CUSTOM_ACTION: (state, action) => state.set('customField', action.value)
        }
      }

      const reducer = createPaginator(config)
      const action = {
        type: 'CUSTOM_ACTION',
        value: 'someVal'
      }

      expect(reducer(undefined, action)).toEqual(defaultPaginator.set('customField', action.value))
    })
  })
```

This is considered advanced usage and usually shouldn't be necessary. It's also worth noting that you will be working with Immutable
objects in your augmented reducer handlers. While we do try to avoid making you think about Immutable when injecting props into your
components, in this case it simply cannot be avoided.

For more information, see the section on [augmenting reducers](augmenting_reducers.md).

### Better tracking of updates in progress

Previous versions were very naive when attempting to indicate that an item is being updated on the server. As soon as the server responded,
the reducer would clear the updating flag for the item being updated, even if there are other requests in progress. Version 3 fixes this by
channeling all individual item updates through a counting semaphore. You can now let your users mash buttons as much as they like. Leave that
decision to your UX engineer, we have you covered. As long as you use `updateAsync` to perform asynchronous updates, the `isUpdating` selector
should always be accurate for a given record.

## Breaking Changes

### Serialized component props

In previous versions, decorated components would receive the `results` and other pieces of state as Immutable objects. In version 3
we will serialize these to plain javascript objects and arrays. `violet-paginator` will still manage its state behind the scenes with
Immutable, but we don't want to force you to learn this library just so that you can use ours.

### Error state gone!

We used to keep some reducer state to indicate if an item had any errors. For example, if a list allows inline editing and a server
request to update an item returns an error response, we would store this in our reducer. This is discontinued in version 3. We will
no longer attempt to keep any error state pertaining to individual items. If you want to keep error state in version 3, you can augment
your pagination reducer, which is a new feature that will be described earlier in this document.

### updateAsync

#### Server Responses

The `updateAsync` action will no longer attempt to make use of your server response. This feature got in the way more than it helped.
If users still want to apply an update using the server response, they can do so by attaching a promise handler to `updateAsync`:

```javascript
export function toggleActive(recipe) {
  const data = {
    active: !recipe.get('active')
  }

  return dispatch =>
    dispatch(pageActions.updateAsync(
      recipe.get('id'),
      data,
      api.recipes.update(data)
    )).then(resp =>
      dispatch(pageActions.updateItem(recipe.get('id'), resp.data))
    )
}
```

#### Server Errors

In versions 1.x and 2.x, `updateAsync` would handle server errors by flagging the item as errored, resetting the record state, and then
swallowing the exception. However, as mentioned above, in version 3, `violet-paginator` is no longer keeping any record of an item's errors
in the reducer. Instead, after resetting the record state, we will throw the exception for you to handle as you see fit, e.g:

```javascript
export function toggleActive(recipe) {
  const data = {
    active: !recipe.get('active')
  }

  return pageActions.updateAsync(
    recipe.get('id'),
    data,
    api.recipes.update(data)
  ).catch(resp =>
    dispatch(toastr.error(resp.error.message))
  )
}
```

See the [Updating Items](updating_items.md) documentation for more information.

### removeAsync

In previous versions, `removeAsync` would remove an item from the list after the request completed. This causes a bit of a UX problem.
Imagine you have 30 records and are viewing 15 per page, and on the first page, you delete the 15th record. Page 1 now displays records
1-14. Now if I click to go to the second page, the server will return records 17-30, because the deletion actually causes record 16 to
appear on the first page. So we effectively skip over it.

To address this, the default behavior for `removeAsync` is now to expire the list when the request completes, causing it to refresh.
However, `removeAsync` now accepts a third argument named `expire` which defaults to `true`, so if you still would like to just remove
the item instead of refresh, you can pass in `false` as the third argument to this method.
`

### isUpdating

The `isUpdating` function is still the correct function to determine if an item is currently being updating. However, the internal
implementation of that function has changed. If you are reading directly from the reducer to determine whether or not an item
is updating, your logic may no longer be correct. You should switch to `isUpdating`. Here is an example usage that also demonstrates
some other handy selectors.

```javascript
import { getItem, isUpdating, isRemoving } from 'violet-paginator'

export default connect(
  (state, ownProps) => {
    const { listId, itemId } = ownProps

    return {
      record: getItem(state, listId, itemId).toJS(),
      updating: isUpdating(state, listId, itemId),
      removing: isRemoving(state, listId, itemId)
    }
  }
)(Component)
```

### Component markup and names

Links are now buttons in version 3. This was done mainly for accessibility reasons. If you are using any of our premade
components that use pagination links or sort links, you will probably have to restyle them.

Some components were also renamed:

Old|New
---|:---
`SortLink`|`ColumnHeader`
`PageLink`|`PageNumber`
