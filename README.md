Cloudflare Tunnel Setup Guide (macOS / Linux)

This guide explains step-by-step how to set up a Cloudflare Tunnel on your local machine using the command line (terminal).
It is primarily for macOS and Linux users.

1. Install cloudflared

First, install the cloudflared CLI tool.

# macOS (using Homebrew)
brew install cloudflare/cloudflare/cloudflared

# Or manual install (example for macOS ARM64)
wget https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-darwin-arm64
chmod +x cloudflared-darwin-arm64
sudo mv cloudflared-darwin-arm64 /usr/local/bin/cloudflared

# Linux users: download the correct binary from
# https://github.com/cloudflare/cloudflared/releases

Check the installation:
cloudflared --version

2. Clone example configuration files (optional but recommended)

git clone https://github.com/Mnaw161329/cloudflared.git

This repository contains useful example config files and templates.

3. Move necessary files to ~/.cloudflared/

# Create the directory if it doesn't exist
mkdir -p ~/.cloudflared

# Copy files from the cloned repo (choose what you need)
cp cloudflared/* ~/.cloudflared/

4. Authenticate with Cloudflare (fix or renew cert.pem)

Run this command to log in to your Cloudflare account:

cloudflared tunnel login

A browser window will open. Log in with your Cloudflare account.
This will generate or renew cert.pem in ~/.cloudflared/.

5. Create your tunnel

Give your tunnel a name and create it:

cloudflared tunnel create your-tunnel-name

Example:
cloudflared tunnel create www.zawseng.site

Success output will show a Tunnel UUID and create a credentials file like:
~/.cloudflared/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.json

6. Create config file

Create and edit your config file:

nano ~/Desktop/cloudflared/yourTunnel-config.yml

Copy the example from the cloned folder and modify it.
Important parts:

tunnel: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx   # Your tunnel UUID or name
credentials-file: /Users/yourusername/.cloudflared/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.json

ingress:
  - hostname: www.yourdomain.com
    service: http://localhost:80               # Change to your local server port (e.g., 3000, 8080)
  - hostname: yourdomain.com                   # Optional: for apex domain
    service: http://localhost:80
  - service: http_status:404                   # REQUIRED: catch-all rule (must be last)

Save: Ctrl+O -> Enter -> Ctrl+X

Validate config (recommended):
cloudflared tunnel --config ~/Desktop/cloudflared/yourTunnel-config.yml ingress validate

7. Add DNS routing

This automatically creates proxied CNAME records in your Cloudflare DNS:

cloudflared tunnel route dns your-tunnel-name www.yourdomain.com
cloudflared tunnel route dns your-tunnel-name yourdomain.com

Example:
cloudflared tunnel route dns www.zawseng.site www.zawseng.site
cloudflared tunnel route dns www.zawseng.site zawseng.site

8. Run the tunnel

Start the tunnel:

cloudflared tunnel --config ~/Desktop/cloudflared/yourTunnel-config.yml run your-tunnel-name

Example:
cloudflared tunnel --config ~/Desktop/cloudflared/www-config.yml run www.zawseng.site

Keep the terminal open. The tunnel will stay connected.
To stop: press Ctrl + C

Bonus: Run as a background service

To run automatically in the background:

cloudflared service install --config ~/Desktop/cloudflared/yourTunnel-config.yml

(Note: starting the service may require additional steps depending on your OS.)

After completing these steps, your local server will be securely accessible worldwide via https://www.yourdomain.com through Cloudflare.

For debugging, add --loglevel debug to the run command.

Good luck!
