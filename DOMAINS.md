# Domain Redirect Cookbook

This is the quick reference for parking vanity domains on GitHub repos using Cloudflare + Namecheap (updated **November 19, 2025**).

1. **Tokens & CLI**
   - Install `cli4` via Homebrew (`brew install cloudflare-cli4`).
   - Store the Cloudflare API token in `~/.profile` as `CF_API_TOKEN`, then `source ~/.profile` so `cli4` picks it up.

2. **Onboard the zone in Cloudflare**
   - `cli4 --get zones name==example.com` shows status; `step:2` means nameserver swap pending.
   - If the zone does not exist, add it in the Cloudflare UI (Websites → Add site) and choose the free plan.

3. **Swap Namecheap nameservers**
   - In Namecheap → Domain tab, change Nameservers → Custom DNS → use the two Cloudflare NS values (e.g., `emma` + `scott`).
   - Propagation usually finishes within 30–60 minutes; verify with `dig example.com @1.1.1.1`.

4. **Create proxied apex record**
   - `cli4 --post type=A name=example.com content=192.0.2.1 proxied=true ttl==1 'zones/:example.com/dns_records'`.
   - Keep the wildcard/`www` records proxied too; Cloudflare terminates TLS regardless of the placeholder IP.

5. **Add redirect**
   - Use a Page Rule to forward every request:  
     `cli4 --post 'targets=[{"target":"url","constraint":{"operator":"matches","value":"*example.com/*"}}]' 'actions=[{"id":"forwarding_url","value":{"url":"https://github.com/YOU/REPO","status_code":301}}]' status=active priority==1 'zones/:example.com/pagerules'`
   - (If you run out of Page Rule quota, migrate to Rulesets.)

6. **Verify**
   - `dig +short example.com @1.1.1.1` should return Cloudflare Anycast IPs.
   - `curl -I --resolve example.com:443:<CF_IP> https://example.com` should issue a 301 to the GitHub repo even before the local DNS cache refreshes.
   - Once local DNS TTLs expire (or after flushing the resolver), plain `curl -I https://example.com` succeeds without `--resolve`.

7. **Current redirects**
   - `codexbar.app` → `https://github.com/steipete/codexbar` (Page Rule `7875b61b0932a3bdf9fcbea43d13e77f`)
   - `trimmy.app` → `https://github.com/steipete/Trimmy` (Page Rule `0a4e482483f3e7775f28e85b652286f8`)

Keep this file updated whenever you add more domains or switch to the new Redirect Ruleset API.
