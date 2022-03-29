# SEO & Meta tags

At some point, you'll want to change the title of your app.
You can do so by changing the `title` key in `redwood.toml`

```diff title="redwood.toml"
[web]
- title = "Redwood App"
+ title = "My Cool App"
  port = 8910
  apiUrl = "/.redwood/functions"
```

If you don't specify another, `title` is used for all your pages.
It'll also be used for the title template.

### Title template

You probably want some consistency with the page title.
That's what the title template is for.

Pass the `titleTemplate` to the `RedwoodProvider` component to have a title template for every page:

```jsx title="web/src/App.js"
import { FatalErrorBoundary, RedwoodProvider } from '@redwoodjs/web'
import { RedwoodApolloProvider } from '@redwoodjs/web/apollo'

import FatalErrorPage from 'src/pages/FatalErrorPage'
import Routes from 'src/Routes'

import './scaffold.css'
import './index.css'

const App = () => (
  <FatalErrorBoundary page={FatalErrorPage}>
    // highlight-next-line
    <RedwoodProvider titleTemplate="%PageTitle | %AppTitle">
      <RedwoodApolloProvider>
        <Routes />
      </RedwoodApolloProvider>
    </RedwoodProvider>
  </FatalErrorBoundary>
)

export default App

```

You can write the format you like.

_Examples  :_
```jsx
"%PageTitle | %AppTitle" => "Home Page | Redwood App"

"%AppTitle · %PageTitle" => "Redwood App · Home Page"

"%PageTitle : %AppTitle" => "Home Page : Redwood App"
```

So now in your page you only need to write the title of the page.

## Adding to page `<head>`

If you want to change the title of your page, or add elements to the `<head>` of the page, use the `Head` component.

Let's say you want to change the title of your About page.

```jsx title="web/src/pages/AboutPage/AboutPage.js"
// highlight-next-line
import { Head } from '@redwoodjs/web'

const AboutPage = () => {
  return (
    <div>
      <h2>AboutPage</h2>
      // highlight-start
      <Head>
        <title>About the team</title>
      </Head>
      // highlight-end
```

Any valid `<head>` tags goes, but just to make things easier, there's a utility component. See [MetaTags](#setting-meta-tags-open-graph-directives).

### What about nested tags?

Redwood uses [react-helmet-async](https://github.com/staylor/react-helmet-async) underneath, which will use the tags furthest down your component tree.

For example, if you set title in your Layout, and a title in your Page, it'll render the one in Page - this way you can override the tags you wish, while sharing the tags defined in Layout.


> **Side note**
> for these headers to appear to bots and scrapers e.g. for twitter to show your title, you have to make sure your page is prerendered
> If your content is static you can use Redwood's built in [Prerender](prerender.md). For dynamic tags, check the [Dynamic head tags](#dynamic-tags)

## Setting meta tags / open graph directives
Often we want to set more than just the title - most commonly to set "og" headers. Og standing for
[open graph](https://ogp.me/) of course.

Redwood provides a convenience component `<MetaTags>` to help you get all the relevant tags with one go (but you can totally choose to do them yourself)

Here's an example setting some common headers, including how to set an `og:image`
```jsx
import { MetaTags } from '@redwoodjs/web'

const AboutPage = () => {
  return (
    <div>
      <h2>AboutPage</h2>
      <MetaTags
        title="About page"
        description="About the awesome team"
        ogUrl="https://awesomeredwoodapp.com/start"
        ogContentUrl="https://awesomeredwoodapp.com/static/og.png"
        robots={['nofollow']}
        locale={}
      />
      <p className="font-light">This is the about page!</p>
    </div>
  )
}

export default AboutPage
```

This is great not just for link unfurling on say Facebook or Slack, but also for SEO. Take a look at the [source](https://github.com/redwoodjs/redwood/blob/main/packages/web/src/components/MetaTags.tsx#L83) if you're curious what tags get set here.


## Dynamic tags

Great - so far we can see the changes, and bots will pick up our tags if we've prerendered the page, but

What if you want to set the header based on the `QUERY` of a Cell?

_Just keep in mind, that Cells are currently not prerendered_ - so it'll be visible to your users, but not to link scrapers and bots.

> **`<head>`s up**
>
> For dynamic tags to appear to bots and link scrapers you have to setup an external prerendering service. If you're on Netlify you can use their [built-in one](https://docs.netlify.com/site-deploys/post-processing/prerendering/). Otherwise you can follow [this great cookbook](https://community.redwoodjs.com/t/cookbook-getting-og-and-meta-tags-working-with-nginx-pre-render-io-and-docker/2014) from the Redwood community


Let's say that in PostCell, we want to set the title to match the post:

```jsx title="web/src/components/PostCell/PostCell.js"
// highlight-next-line
import { MetaTags } from '@redwoodjs/web'
import Post from 'src/components/Post/Post'

export const QUERY = gql`
  query FindPostById($id: Int!) {
    post: post(id: $id) {
      title
      snippet
      author {
        name
      }
    }
  }
`

export const Loading = /* ... */

export const Empty = /* ... */

export const Success = ({ post }) => {
  return (
    <>
      // highlight-start
      <MetaTags
        title={post.title}
        author={post.author.name}
        description={post.snippet}
      />
      // highlight-end
      <Post post={post} />
    </>
  )
}
```

Once the success component renders, it'll update your page's title and set the relevant meta tags for you!
