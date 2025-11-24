# DNS Playbook

Last updated **November 24, 2025**. Paired with `DOMAINS.md`, this file captures the checklist for moving vanity domains onto Cloudflare and wiring GitHub redirects.

## Tokens & CLI
- Homebrew install: `brew install cloudflare-cli4`.
- Export the Cloudflare token in `~/.profile` (`export CF_API_TOKEN="..."`) and `source ~/.profile` before invoking `cli4`.

## Onboarding Flow
1. Add the zone in Cloudflare (Websites → Add site → Free plan).
2. Use `cli4 --get zones name==example.com` to confirm the `step` and nameserver requirements.
3. Swap nameservers at Namecheap to the Cloudflare pair (typically `emma` + `scott`). No API automation exists for this step.
4. Wait for propagation (check with `dig example.com @1.1.1.1`).

## DNS Records
- Create a proxied apex `A` record so Cloudflare terminates HTTPS even if the backend is just a placeholder IP:  
  `cli4 --post type=A name=example.com content=192.0.2.1 proxied=true ttl==1 'zones/:example.com/dns_records'`
- Keep the existing wildcard/`www` records proxied.

## Redirect Rules
- Free plan → Page Rules (quota 3). Command template:  
  ```
  cli4 --post \
    'targets=[{"target":"url","constraint":{"operator":"matches","value":"*example.com/*"}}]' \
    'actions=[{"id":"forwarding_url","value":{"url":"https://github.com/ORG/REPO","status_code":301}}]' \
    status=active priority==1 \
    'zones/:example.com/pagerules'
  ```
- When quota runs out, migrate to Rulesets with the same token but add `Rulesets:Edit`.

## Verification
- `dig +short example.com @1.1.1.1` should return Cloudflare IPs.
- Force requests before local caches expire: `curl -I --resolve example.com:443:<cf_ip> https://example.com`.
- After TTL expiry or a resolver flush (`sudo dscacheutil -flushcache && sudo killall -HUP mDNSResponder`), normal curls succeed without `--resolve`.

## Active Domains
- `codexbar.app` → GitHub repo, Page Rule `7875b61b0932a3bdf9fcbea43d13e77f`.
- `trimmy.app` → GitHub repo, Page Rule `0a4e482483f3e7775f28e85b652286f8`.
- `askoracle.dev` → GitHub repo (`https://github.com/steipete/oracle`), Page Rule `c75b8a53d97d444c7f5d718025e041ab` (Cloudflare nameservers `kenneth` / `melany` already active).
- `mcporter.dev` → GitHub repo (`https://github.com/steipete/mcporter`), Page Rule `2d81a7a746b395e101d64c59bee4d672`, Cloudflare nameservers `emma` / `scott` still need to replace Namecheap defaults before the redirect goes live.
- `sweetlink.dev` → GitHub repo (`https://github.com/steipete/sweetlink`), Page Rule `5c63b6071b4bbd6fa350ae8efa850900`, Cloudflare nameservers `kenneth` / `melany` active.
- `sweetlink.ai` → GitHub repo (`https://github.com/steipete/sweetlink`), Page Rule `07f8cd77c7f1301e652f76b161b060c8`; needs Namecheap nameservers switched to Cloudflare `emma` / `scott` to activate.
- `repobar.app` → GitHub repo (`https://github.com/steipete/RepoBar`), Page Rule `b045f847ac881694ba24bf3f24e0f7e6`, Cloudflare nameservers `emma` / `scott` active.
- `repobar.com` → GitHub repo (`https://github.com/steipete/RepoBar`), Page Rule `e0305d7bf41aa9b56adfb483360dd5cc`, Cloudflare nameservers `emma` / `scott` active.
- `repobar.dev` → GitHub repo (`https://github.com/steipete/RepoBar`), Page Rule `4f03d9ab06bfd3302b75c85894d7140c`, Cloudflare nameservers `emma` / `scott` active.
- `repobar.ai` → GitHub repo (`https://github.com/steipete/RepoBar`), Page Rule `ae95538cf0c0dc1144b7c17d67aa6040`; switch Namecheap nameservers to Cloudflare `emma` / `scott` to activate.
- `warelay.ai` → GitHub repo (`https://github.com/steipete/warelay`), Page Rule `d4a5dc81743ec53cf85c1b5c33a144a5`, Cloudflare nameservers `emma` / `scott` active.
- `warelay.app` → GitHub repo (`https://github.com/steipete/warelay`), Page Rule `ddac8b9136c553b8a17dd49fa65a7c8d`, Cloudflare nameservers `emma` / `scott` active.
- `warelay.bot` → GitHub repo (`https://github.com/steipete/warelay`), Page Rule `2c92074fafc5e58af9c24cf369dc1aa7`, Cloudflare nameservers `emma` / `scott` active.
- `warelay.com` → GitHub repo (`https://github.com/steipete/warelay`), Page Rule `99859e0db704f3ee0fca099f9d8db901`, Cloudflare nameservers `emma` / `scott` active.
- `warelay.dev` → GitHub repo (`https://github.com/steipete/warelay`), Page Rule `bf8e31a358bdd5000fbe15b89e653405`, Cloudflare nameservers `emma` / `scott` active.
- `warelay.to` → GitHub repo (`https://github.com/steipete/warelay`), Page Rule `c3fe671bc98cfba48696ee8d784a6374`, Cloudflare nameservers `emma` / `scott` active (certificate provisioning may take a few minutes after DNS activation).

Update both `DOMAINS.md` and this file whenever you onboard another domain.
