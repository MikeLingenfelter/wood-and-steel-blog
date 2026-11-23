# wood-and-steel-blog

Source for [woodandsteelworkshoppe.com](https://woodandsteelworkshoppe.com), a maker blog built with [Hugo](https://gohugo.io) and deployed to Cloudflare Workers.

## Local development

```sh
brew install hugo
hugo server -D                       # preview, including drafts
hugo new posts/my-post/index.md      # new post (draft by default)
```

Hugo version is pinned by the `HUGO_VERSION` build variable in Cloudflare (currently `0.170.1`). Keep it in step with your local version.
