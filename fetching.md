# Fetching

In a nutshell: you tell us *how* to fetch your records, and we do all the fetching. `violet-paginator` will call your `fetch` function
when your component first renders and when certain user actions occur. Actions that will trigger a fetch include clicking pagination
links and sorting links. Updating filters will also trigger a fetch.

In the simplest case, your fetch function will look a lot like this:

```javascript
export default function fetchRecipes(pageInfo) {
  return () => {
    return api.recipes.index(pageInfo.query)
  }
}
```

When `violet-paginator` calls your function, it will supply the `pageInfo` argument, which includes the `query` object.
This object contains all the request parameters that `violet-paginator` manages. This includes:

1. Pagination parameters
2. Sorting parameters, if present
3. Filtering parameters, if present

However, the `fetch` function can also be used as an opportunity to translate the server response into the proper format.
You can also use it to save server data in your own reducer before returning it to `violet-paginator`. See the documentation for
[Intercepting the Promise](intercepting_the_promise.md) for examples.
