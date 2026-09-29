# vendor-ops

Skills for the systems a team *coordinates in* rather than builds — issue trackers and
knowledge wikis. Separate from `agent-skills`, which is engineering practice and workstation
operations, and from `delivery-skills`, which is a delivery pipeline.

The failure modes here are not bugs. They are writing into the wrong workspace, touching
someone else's record, filing a document where nobody looks for it, and a write call that
returns success without changing anything.

## Skills

| Skill | Covers |
|---|---|
| `linear-hygiene` | confirm the workspace before any read or write; only touch your own tickets; the API calls that fail silently |
| `outline-doc` | the wiki as system of record over repo markdown; collection choice; the patch that no-ops inside a blockquote |
| `compliance-remediation` | compliance platforms read live state, not code; map each fix to who can apply it; rank by audit yield |

## Profiles

No skill here contains a workspace identifier. Each opens with a `## Profile` block naming
only the keys it reads, and resolves `~/<workspace>/.claude/workspace.config.md` — the
workspace the caller named, or the single match of `~/*/.claude/workspace.config.md`.

That file holds the tracker workspace id, ticket prefix, wiki base URL, collection ids and
the orgs to keep out of a written artifact. It lives in the workspace it describes, never
here.

## Contributing

Match the anatomy the skills already use: frontmatter `name` and a `description` starting
with "Use when", then Overview, When to Use, Core Process, Common Rationalizations,
Verification.

A skill earns its lines from something that actually cost time. Record the trap and the fix,
not general advice — and never a workspace literal. If a skill needs one, it becomes a
profile key.
