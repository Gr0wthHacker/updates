# Consuming this feed

Notes for jackgierlich.com (and anyone else that wants to render the updates).

## At build time (recommended)

Site bundles a fresh feed on every deploy.

```ts
// scripts/build-updates-feed.ts (runs during `npm run build`)
const REPO = "Gr0wthHacker/updates";
const list = await fetch(
  `https://api.github.com/repos/${REPO}/contents/updates`,
).then((r) => r.json());

const updates = await Promise.all(
  list
    .filter((f: any) => f.name.endsWith(".md"))
    .map(async (f: any) => {
      const raw = await fetch(f.download_url).then((r) => r.text());
      const { data, content } = parseFrontmatter(raw); // gray-matter, etc.
      return { ...data, body: content, filename: f.name };
    }),
);

const feed = updates
  .filter((u) => u.visibility !== "draft")
  .sort((a, b) => (a.date < b.date ? 1 : -1));

writeFileSync("src/generated/updates.json", JSON.stringify(feed, null, 2));
```

CSP stays tight (`connect-src 'self'`) because the fetch happens at build time
on the build machine, not in the browser.

To rebuild on every push here without manual intervention, wire a Netlify
build hook to a GitHub Action in this repo (`workflow_dispatch` → `curl
$NETLIFY_HOOK`).

## At runtime (alternative)

If a consumer needs live updates without rebuilds, fetch
`https://api.github.com/repos/Gr0wthHacker/updates/contents/updates` plus
each file's `download_url`. Beware: unauthenticated GitHub API is rate-limited
to 60 req/hr per IP. Cache aggressively.

CSP would need `connect-src 'self' https://api.github.com
https://raw.githubusercontent.com`.

## Validating before push

```bash
# JSON Schema validation against schema/update.schema.json
npx ajv-cli validate -s schema/update.schema.json -d "updates/*.md"
# (or use any frontmatter-aware YAML validator)
```

## Output shape

Each entry, post-parse:

```json
{
  "filename": "2026-06-02-public-updates-log-launched.md",
  "title": "Launched this very repo as a public updates log",
  "date": "2026-06-02",
  "category": "launch",
  "venture": "personal",
  "tags": ["meta", "infra", "jackgierlich.com"],
  "status": "shipped",
  "link": "https://github.com/Gr0wthHacker/updates",
  "visibility": "public",
  "body": "Most of what I ship is private...",
  "slug": "public-updates-log-launched"
}
```

## Possible UI on jackgierlich.com

- Dock badge: count of updates shipped this month.
- New "Updates" window: filterable list by venture / category / tag.
- Terminal `updates` command: pretty-print most-recent N.
- StatusBar marquee: most recent headline.

Whichever ones get built, they all read the same generated JSON. Don't fan out
the parsing.
