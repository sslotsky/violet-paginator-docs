# Composing Actions

The actions that `violet-paginator` dispatches are exposed through the package API via the [`register`](registerconfig.md) function. This allows you the flexibility to call them directly as part of a more complex operation. The most common use case for this would be [updating an item within the list](updating_items.md). 

As an example, consider a datatable where one column has a checkbox that's supposed to mark an item as active or inactive.