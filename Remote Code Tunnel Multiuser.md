# Remote Access to computers for multiple linux users

[Pre-requisites](./Remote%20Code%20Tunnel.md)

To allow the same thing for a team where every individual in the team has a linux user on your server:

TLDR;

Run an instance of the service per user.


Details:

We are now creating it for another user called `demo`

The administrator creates a copy of the original service as follows:

```bash

sudo cp /etc/systemd/system/code-tunnel.service /etc/systemd/system/code-tunnel-demo.service
```

Edit the file to have `demo` user specific parameters. Here's the output of what we did:

```bash
[Unit]
Description=Visual Studio Code Tunnel for user demo
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=10
User=demo
ExecStart=/usr/share/code/bin/code-tunnel "--verbose" "--cli-data-dir" "/home/demo/.vscode/cli" "tunnel" "service" "internal-run"

[Install]
WantedBy=default.target
```

The changes are in `Description` (optional), `User` and `ExecStart` parameters.

Now, enable and start the service (observe the demo suffix)

```bash
sudo systemctl enable code-tunnel-demo
sudo systemctl start code-tunnel-demo
```

Start the service

```bash
sudo systemctl status code-tunnel-demo
```

Good luck !
