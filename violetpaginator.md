# VioletPaginator


This is a fully featured pagination component. It includes link for the previous and next page, for the first and last page,
and for up to three pages on each side of the current page. The example below shows the component on page 6 of 35. 

![](Screen Shot 2016-10-09 at 2.48.41 PM.png)
Rendering this component requires only a `listId`.

```javascript
import React from 'react'
import { VioletPaginator } from 'violet-paginator'

export default function Recipes() {
  return (
    <section>
      <VioletPaginator listId="recipes" />
    </section>
  )
}
```
