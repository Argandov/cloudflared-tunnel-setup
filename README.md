# Cloudflared Tunnel Setup

## Initial Setup

This repo is the same thing than the official docs:
- [Cloudflare tunnel docs](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/tunnel-guide)
But I distilled the most common operations for easy access

[Cloudflare daemon installers link](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/tunnel-guide#set-up-a-tunnel-locally-cli-setup)

```bash
# Example for rpm Packages:
wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-x86_64.rpm
rpm -i cloudflared*.rpm

# Authorize to CF via the browser
cloudflared tunnel login 
cloudflared tunnel create my-new-tunnel 

touch $HOME/.cloudflared/config.yaml
vi $HOME/.cloudflared/config.yaml # USE CONFIG BELOW

# my-subdomain = CNAME for the tunnel (subdomain.my.domain)
cloudflared tunnel route dns my-new-tunnel my-subdomain
# Run the tunnel in the background, and detach processes:
cloudflared tunnel run <UUID or NAME>  &
jobs # Check which job number cloudflared is running on
disown %1 # If this is a clean server it will normally be the No. 1
# This can be ran or configured as a service

```

## Running the Tunnel

Use the tunnel configuration created at ```cloudflared tunnel create xxxx```

1. If local application uses self-signed certificates:

```yaml
tunnel: e0a4642b-f5d9-4836-a60f-example
credentials-file: /file-path/.cloudflared/e0a4642b-f5d9-4836-a60f-example.json

originRequest:
   originServerName: CustomAppName
   noTLSVerify: true
   disableChunkedEncoding: true

ingress:
  - hostname:
    service: https://127.0.0.1:443
```

2. If Local application does not use self-signed certs:

```yaml
url: http://localhost:8000
tunnel: <Tunnel-UUID>
credentials-file: /root/.cloudflared/<Tunnel-UUID>.json
```

## Running the tunnel

```bash
SCRIPT_FILE=$/HOME/run-cloudflared.sh
echo '#!/bin/sh' > $SCRIPT_FILE
echo 'cloudflared tunnel run <UUID or NAME>' >> $SCRIPT_FILE
chmod +x $SCRIPT_FILE
sudo cp $SCRIPT_FILE /etc/init.d/cloudflared-run.sh
```

TO-DO: CONDENSE INTO 1 SCRIPT
