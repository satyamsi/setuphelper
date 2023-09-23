# setup helper
Interesting things to help setting up work environment

## Remote Access to computers

Free models: 

VSCode provides tunneling and allows VSCode to access remote machines/shell/repos checked out on remote machines. Here's a [link](https://code.visualstudio.com/docs/remote/tunnels) to the background. 

It supports which is a code-tunnel service but it runs it as a user and I faced issues where I could not use it across reboots of the machine etc. Here's a simple trick:

If you used the instructions in the link above and executed the following:

```bash
code tunnel service install
```

You should have a file `$HOME/.vscode/cli/code-tunnel.service`

Make it a first-class systemctl service:

```bash
sudo cp ./.vscode/cli/code-tunnel.service /etc/systemd/system/
```

Add a user to this file. Mine looks like this:

```bash
[Unit]
Description=Visual Studio Code Tunnel
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=10
User=satyam
ExecStart=/usr/share/code/bin/code-tunnel "--verbose" "--cli-data-dir" "/home/satyam/.vscode/cli" "tunnel" "service" "internal-run"

[Install]
WantedBy=default.target
```
where I have added `User=satyam` line above.

Now enable and start the service and check the status:

```bash
sudo systemctl enable code-tunnel
sudo systemctl start code-tunnel 
```

Check status:

```bash
sudo systemctl status code-tunnel
```

Here's a sample output:

```
● code-tunnel.service - Visual Studio Code Tunnel
     Loaded: loaded (/etc/systemd/system/code-tunnel.service; enabled; preset: disabled)
     Active: active (running) since Fri 2023-09-22 23:27:24 PDT; 10h ago
   Main PID: 4256 (code-tunnel)
      Tasks: 33 (limit: 611535)
     Memory: 8.5M
        CPU: 2.156s
     CGroup: /system.slice/code-tunnel.service
             └─4256 /usr/share/code/bin/code-tunnel --verbose --cli-data-dir /home/user/.vscode/cli tunnel service internal-run

Sep 23 10:17:48 user-gpu code-tunnel[4256]: [2023-09-23 10:17:48] trace [rpc.5] [codeserver.6] [10:17:48] [<unknown>][6435f6aa][ExtensionHost>
Sep 23 10:17:48 user-gpu code-tunnel[4256]: [2023-09-23 10:17:48] trace [rpc.5] [codeserver.6] [10:17:48] [<unknown>][6435f6aa][ExtensionHost>
Sep 23 10:17:48 user-gpu code-tunnel[4256]: [2023-09-23 10:17:48] trace [rpc.5] Attached to server
Sep 23 10:17:49 user-gpu code-tunnel[4256]: [2023-09-23 10:17:49] debug [russh::server::encrypted] handler.window_adjusted ChannelId(2)
Sep 23 10:17:50 user-gpu code-tunnel[4256]: [2023-09-23 10:17:50] debug [russh::server::encrypted] handler.window_adjusted ChannelId(2)
Sep 23 10:19:26 user-gpu code-tunnel[4256]: [2023-09-23 10:19:26] debug [tunnels::connections::ws] received liveness pong
Sep 23 10:21:26 user-gpu code-tunnel[4256]: [2023-09-23 10:21:26] debug [tunnels::connections::ws] received liveness pong
Sep 23 10:22:47 user-gpu code-tunnel[4256]: [2023-09-23 10:22:47] trace [rpc.5] [codeserver.6] New EH opened, aborting shutdown
Sep 23 10:22:47 user-gpu code-tunnel[4256]: [2023-09-23 10:22:47] trace [rpc.5] [codeserver.6] [10:22:47] New EH opened, aborting shutdown
Sep 23 10:23:26 user-gpu code-tunnel[4256]: [2023-09-23 10:23:26] debug [tunnels::connections::ws] received liveness pong
```

Ensure the service is enabled
```bash
     Loaded: loaded (/etc/systemd/system/code-tunnel.service; enabled; preset: disabled)
```

And active 
```bash
     Active: active (running) since Fri 2023-09-22 23:27:24 PDT; 10h ago
```

Connect to it using VSCode and test it across reboots ! 

Voila ! 


