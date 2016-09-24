# Expiration

`violet-paginator` caches every list that you initialize within its reducer. This can help with performance if you're navigating through a SPA that displays different paginated lists on different pages; once you've loaded the list, it will still be there when you navigate back to the page where it's located, so there's no need to reload from the server.

However, sometimes it might be the case that a user action should invalidate the cache. For example, a banking website might show a list of transactions on one side with a control to transfer between accounts on the other. After making a transfer, you want the list of transactions to update to show the new transaction. Expiration is a great way to achieve this: if the list is in view, it will reload immediately, but otherwise, it will wait until next time it renders and reload then.

## expire()

Marking a list expired is easy as long as you have the `listId`. Here's an example of making a custom action creator that calls `expire`:

```javascript
import { simpleComposables } from 'violet-paginator'

const pageActions = simpleComposables('recipes')

export function expireList() {
  return pageActions.expire()
}
```

Now you can dispatch the `expireList` action from anywhere in the application, and your `'recipes'` list will be expired.

## expireAll()

Sometimes you may prefer the nuclear option of simply expiring every list in the cache. Here's how you could build a button that expires everything:

```javascript
import React, { PropTypes } from 'react'
import { expireAll } from 'violet-paginator'
import { connect } from 'react-redux'

export function Expire({ expire }) {
  return (
    <button onClick={expire}>Expire All</button>
  )
}

Expire.propTypes = {
  expire: PropTypes.func.isRequired
}

export default connect(
  () => ({}),
  { expire: expireAll }
)(Expire)
```

