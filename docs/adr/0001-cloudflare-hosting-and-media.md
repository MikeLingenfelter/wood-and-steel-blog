# Cloudflare Workers hosting, photos in Git, large files in R2

The Hugo site is deployed to Cloudflare Workers Static Assets from GitHub, not Cloudflare Pages, because Cloudflare now steers new projects to Workers and puts new features there. Photos live in the repo as Hugo page bundles and are resized at build time, so Cloudflare Images (paid, duplicates Hugo's image processing) is not used. Attachments too large for the 25 MiB per-file limit, which applies to both Pages and Workers, are served from an R2 bucket on a custom subdomain, `files.woodandsteelworkshoppe.com`.

## Considered Options

- **Cloudflare Pages**: simpler dashboard setup, but no longer where Cloudflare invests.
- **Cloudflare Images**: no build-time work, but paid and redundant with Hugo.
- **Git LFS for large files**: does not work well with Cloudflare build pipelines.

## Consequences

- Downscale originals (about 2400px long edge) before committing photos. If the repo passes roughly 1 GB, move media to R2.
- The R2 custom domain must be a zone in the same Cloudflare account as the bucket.
