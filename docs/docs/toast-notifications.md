# Toast Notifications

Did you know that those little popup notifications that you sometimes see at the top of a page after you've performed an action are affectionately known as "toast" notifications?
Because they pop up like a piece of toast from a toaster!

![Example toast animation](https://user-images.githubusercontent.com/300/110032806-71024680-7ced-11eb-8d69-7f462929815e.gif)

Redwood supports these notifications out of the box thanks to the [react-hot-toast](https://react-hot-toast.com/) package.
Their [docs](https://react-hot-toast.com/docs) are very thorough, but here's enough to get you going.

### Displaying a Toast

Wherever you want your notifications to be output, include the `Toaster` component:

```jsx title="web/src/pages/HomePage/Homepage.js"
// highlight-next-line
import { Toaster } from '@redwoodjs/web/toast'

const HomePage = () => {
  return (
    <main>
      {/* JSX... */}
      // highlight-next-line
      <Toaster />
    </main>
  )
}

export default HomePage
```

### Triggering a Toast

Once you've included the `Toaster` component, to show a notification, call one of the `toast` object's methods:

```jsx title="web/src/components/UserForm/UserForm.js"
// highlight-next-line
import { toast } from '@redwoodjs/web/toast'

const UserForm = () => {
  onSubmit: () => {
    try {
      {/* Code to save a record... */}
      // highlight-next-line
      toast.success('User created!')
    } catch (e) {
      // highlight-next-line
      toast.error("Error creating user...")
    }
  }

  return (
    // JSX...
  )
})
```
