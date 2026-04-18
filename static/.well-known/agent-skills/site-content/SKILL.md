# Read site content

Use this skill when you want to read or index the public writing on `faizshaikh.xyz`.

## What the site exposes

- `https://faizshaikh.xyz/sitemap.xml` for the canonical page list
- `https://faizshaikh.xyz/index.json` for the site's search index
- `https://faizshaikh.xyz/index.xml` for the main RSS feed

## Best way to consume the site

1. Start with `sitemap.xml` if you need the full set of public URLs.
2. Use `index.json` if you want a compact corpus of posts, summaries, and permalinks.
3. Fetch the canonical page URL when you need the full article text and page metadata.
4. Prefer post pages and thought pages over archive, tag, and search UI pages.

## Notes

- This is a static Hugo site, not an API product.
- There is no OAuth, OIDC, MCP server, or WebMCP surface on this domain.
- Public pages may be indexed for search and agent retrieval, but the site's robots policy blocks training-oriented crawlers.
