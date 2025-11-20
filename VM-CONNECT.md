# VM connect / port-forward quickstart

Use these steps to let the Mac host reach services inside the VM and to copy local resources (GitHub SSH key, repos).

## Prereqs
- VM user: `steipete`
- Host user: `steipete`
- Host SSH server enabled (System Settings → General → Sharing → Remote Login **on**).

## 1) Get the host side IP that the VM sees
On the host (Mac): `ipconfig getifaddr en0`  
Typical host-only IP from the VM: `192.168.64.1`

## 2) Open reverse SSH tunnel from the VM to the host
Run inside the VM:
```
ssh -N -R 22080:localhost:80 steipete@192.168.64.1
# For Chrome devtools (9222):
ssh -N -R 29222:localhost:9222 steipete@192.168.64.1
```
This exposes VM ports 80/9222 on the host as 127.0.0.1:22080/29222.

## 3) Enable direct SSH into the VM (once)
On the VM:
```
sudo systemsetup -setremotelogin on
```
Then from the host you can `ssh steipete@192.168.64.2`.

## 4) Copy GitHub key from host to VM
From the host:
```
ssh -o StrictHostKeyChecking=accept-new steipete@192.168.64.2 'mkdir -p ~/.ssh && chmod 700 ~/.ssh'
scp ~/.ssh/github_rsa ~/.ssh/github_rsa.pub steipete@192.168.64.2:~/.ssh/
ssh steipete@192.168.64.2 'chmod 600 ~/.ssh/github_rsa && chmod 644 ~/.ssh/github_rsa.pub'
```

## 5) Sync projects into the VM
From the host:
```
rsync -az --delete ~/Projects/agent-scripts ~/Projects/mcporter ~/Projects/oracle steipete@192.168.64.2:~/Projects/
```

## 6) Quick checks
- Tunnel: `netstat -an | grep 22080` on host should show LISTEN.
- Service reachability: `curl http://127.0.0.1:22080` on host should hit the VM’s port 80 app.
- SSH into VM: `ssh steipete@192.168.64.2` should not prompt for a password after the key copy.

## Troubleshooting
- If SSH is refused: rerun `sudo systemsetup -setremotelogin on` in VM.
- If tunnel says “Bad remote forwarding specification”, ensure there is a space before `steipete@192.168.64.1`.
- If curl resets, start the service on the VM at the forwarded port (80/9222).
- If SSH still asks for a password after copying keys:
  - On the VM: `cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys` (or append the public key you actually use) then `chmod 600 ~/.ssh/authorized_keys`.
  - Ensure ownership/perms: `sudo chown -R steipete:staff ~/.ssh && chmod 700 ~/.ssh`.
  - Prefer the unencrypted `id_ed25519` key; `github_rsa` is encrypted and will prompt unless you load it into `ssh-agent`.
