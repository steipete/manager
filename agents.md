# AGENTS.md

READ ~/Projects/agent-scripts/{AGENTS.md,TOOLS.md} BEFORE ANYTHING (skip if files missing).

Domain redirect (Cloudflare + Namecheap) quick steps:
- Ensure `CF_API_TOKEN` is loaded (`source ~/.profile`) and use `cli4`.
- List zone IDs fast: `cli4 --json --get /zones | jq -r '.[] | \"\\(.name)\\t\\(.id)'`.
- Add proxied placeholder A records: `cli4 --post type=A name=example.com content=192.0.2.1 proxied=true ttl==1 'zones/:example.com/dns_records'` and same for `*.example.com`.
- Set the 301 forward Page Rule: `cli4 --post 'targets=[{\"target\":\"url\",\"constraint\":{\"operator\":\"matches\",\"value\":\"*example.com/*\"}}]' 'actions=[{\"id\":\"forwarding_url\",\"value\":{\"url\":\"https://github.com/ORG/REPO\",\"status_code\":301}}]' status=active priority==1 'zones/:example.com/pagerules'`.
- Verify: `dig +short example.com` shows CF anycast; `curl -I https://example.com` returns 301.
