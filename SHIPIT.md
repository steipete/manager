# Ship/Release Snapshot — 2025-11-17

## How to regenerate this list
```bash
# assumes author from git config (name/email)
since="30 days ago"
author_pattern="$(printf '%s\n%s' "$(git config --global user.name || git config user.name)" "$(git config --global user.email || git config user.email)" | sed 's/[].[^$\\*|/]/\\&/g' | paste -sd'|' -)"
# find repos with >10 commits in the last 30 days
for repo in ~/Projects/*; do
  [ -d "$repo/.git" ] || continue
  count=$(git -C "$repo" log --since="$since" --extended-regexp --author="$author_pattern" --pretty='%H' 2>/dev/null | wc -l | tr -d ' ')
  [ "$count" -gt 10 ] && printf "%4d %s\n" "$count" "$(basename "$repo")"
done | sort -nr

# for the shortlisted repos, list November tags + npm version
since_tags="2025-11-01"
for repo in mcporter oracle wingman tmuxwatch Trimmy poltergeist-pitui codexbar poltergeist markdansi Commander TauTUI sweetlink Peekaboo homebrew-tap sweetistics steipete AXorcist; do
  path=~/Projects/$repo
  echo "\n=== $repo ==="
  git -C "$path" for-each-ref --sort=creatordate --format '%(refname:short) %(creatordate:iso8601)' refs/tags | awk -v s="$since_tags" '$2" "$3 >= s {print}'
  [ -f "$path/package.json" ] && node -e "const p=require(process.argv[1]); console.log('package.json', p.name, p.version);" "$path/package.json"
done
```

## Snapshot as of 2025-11-17
- Shipped (tags/releases this month): mcporter (v0.6.1, npm 0.6.1), oracle (v1.1.0, npm 1.1.0), tmuxwatch (v0.9.2, brew updated), Trimmy (v0.2.3), poltergeist / poltergeist-pitui (v2.0.0, npm 2.2.0), codexbar (v0.2.0), markdansi (v0.1.2), Commander (v0.1.0), homebrew-tap updated for mcporter 0.6.0.
- Not shipped yet (no new Nov release): wingman, TauTUI, sweetlink (pkg 0.2.0), Peekaboo (pkg 3.0.0), sweetistics (pkg 1.0.0), steipete, AXorcist. Brew tap for poltergeist still needs the 2.0.0 bump. (agent-scripts is just a scripts collection—no releases expected.)

## Commitments for November
- Ship poltergeist via Homebrew tap (brew formula update to 2.0.0+).
- Ship sweetlink (tag + npm).
- Stretch: ship Peekaboo if ready.
