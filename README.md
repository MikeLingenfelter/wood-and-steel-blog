# wood-and-steel-blog

Source for [woodandsteelworkshoppe.com](https://woodandsteelworkshoppe.com), a maker blog built with [Hugo](https://gohugo.io) and deployed to Cloudflare Workers.

## Local development

```sh
brew install hugo
hugo server -D                       # preview, including drafts
hugo new posts/my-post/index.md      # new post (draft by default)
```

Hugo version is pinned by the `HUGO_VERSION` build variable in Cloudflare (currently `0.170.1`). Keep it in step with your local version.

## Writing a new post

Every post goes through a branch and a pull request. Merging to `main` publishes it.

1. **Branch from an up-to-date `main`.**
   ```sh
   git checkout main && git pull
   git checkout -b post/my-post
   ```
2. **Create the post** as a page bundle (a folder holding `index.md` plus its photos and files):
   ```sh
   hugo new posts/my-post/index.md
   ```
   The folder name becomes the URL (`/posts/my-post/`). New posts start with `draft = true`.
3. **Preview while you write.** `hugo server -D` live-reloads and includes drafts.
4. **Fill in the front matter** (see below), then write the post in Markdown. Put photos and downloads in the same folder.
5. **Publish:** set `draft = false` (or delete the line). Drafts are left out of the production build, so a post still marked as a draft will not appear on the live site after merging.
6. **Check the production build** with `hugo --gc --minify` (the command Cloudflare runs). It must finish without errors.
7. **Commit, push and open a pull request.**
   ```sh
   git add content/posts/my-post
   git commit -m "Add post: my post"
   git push -u origin post/my-post
   gh pr create
   ```
8. **Merge the PR.** Cloudflare builds `main` and deploys it. Check the live site afterwards, then delete the branch (`git checkout main && git pull && git branch -d post/my-post`).

Downscale photos to about 2400px on the long edge before committing. Hugo resizes them at build time, so the originals do not need to be in the repo.

### Front matter

| Field | Required | Notes |
| --- | --- | --- |
| `title` | yes | Generated from the folder name. Edit it. |
| `date` | yes | Set to the time you create the post. Change it to backdate or reorder. |
| `draft` | yes | `true` hides the post from production. |
| `categories` | yes | Exactly one of `woodworking`, `metalworking`, `3d-printing`, `cad`, `other`. |
| `tags` | no | Free-form: a tool, material or technique, such as `['dovetails', 'oak']`. |
| `summary` | recommended | Shown on the home page and in post lists. |
| `cover` | no | A photo in the post's folder, such as `'cover.jpg'`. |
| `series`, `weight` | no | See "Multi-part builds". |
| `attachments` | no | See "Posts with downloads". |
| `models` | no | Reserved for interactive 3D models. Not rendered yet. |

### Types of posts

**Standard post (project write-up, how-to, tool review, shop notes).**
Pick the category, add tags, write a summary, and add a cover photo. Embed photos with `![Alt text](photo.jpg)` and always write alt text.

**Multi-part build.** Give every part the same `series` name and order them with `weight`. Each part gets navigation to the others.
```toml
series = ['Workbench build']
weight = 1   # part 2 gets 2, and so on
```
Each part is its own post in its own folder, such as `workbench-1-lumber`, `workbench-2-base`. Pick the category the part is mostly about.

**Posts with downloads (cut lists, STL, STEP, PDFs).** Put the files in the post's folder and list them in the front matter. They appear in a "Downloads" section under the post.
```toml
attachments = ['cut-list.pdf', 'bracket.stl']
```
Files over 25 MiB cannot be served from the site. Upload them to the R2 bucket and use the full URL instead:
```toml
attachments = ['https://files.woodandsteelworkshoppe.com/bracket-large.step']
```

**CAD and 3D printing posts.** Use the `cad` or `3d-printing` category, attach the model files as above, and include screenshots or renders as cover and inline images. Add `models = ['bracket.stl']` now if you like. It is ignored until the interactive viewer is built.

**Quick or off-topic post.** Use `categories = ['other']` and keep the front matter minimal: title, date, category and summary.

### Housekeeping

- `content/posts/hello-workshop` is a placeholder draft. Delete it once your first real post is ready.
- Keep the Hugo version in step with `HUGO_VERSION` in Cloudflare (see above) so local and production builds match.
