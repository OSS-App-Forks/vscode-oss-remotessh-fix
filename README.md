# VSCode OSS Remote-SSH fix (nov 2022)

### Ensure to Replace `"serverApplicationName": "code-server-oss"` With `"serverApplicationName": "code-server"` in `product.json` of your VSCode
### Run this in the server after initial connection fails:
```bash
sed -i 's/if(this.n.isBuilt)re/if(!this.n.isBuilt)re/' \
    .vscode-server-oss/bin/5235c6bb189b60b01b1f49062f4ffa42384f8c91/out/vs/server/node/server.main.js
```

### *Update!* This seems like a much easier workaround!: https://github.com/OSS-App-Forks/vscode-oss-remotessh-fix/issues/1#issuecomment-1455545947


===
### To automate this on all servers
Replace the following line in `~/.vscode-oss/extensions/ms-vscode-remote.remote-ssh-0.92.0/out/extension.js`:
```
From:
\n\ttar -xf vscode-server.tar.gz\n\tTAR_EXIT

To:
\n\ttar -xf vscode-server.tar.gz\n\tsed -i 's/if(this.n.isBuilt)re/if(!this.n.isBuilt)re/' vscode-server*/out/vs/server/node/server.main.js\n\tTAR_EXIT
```
```diff
1c1
< \n\ttar -xf vscode-server.tar.gz\n\tTAR_EXIT
---
> \n\ttar -xf vscode-server.tar.gz\n\tsed -i 's/if(this.n.isBuilt)re/if(!this.n.isBuilt)re/' vscode-server*/out/vs/server/node/server.main.js\n\tTAR_EXIT
```
Pro-tip: Use [js-beautify](https://www.npmjs.com/package/js-beautify) to make extension.js more readable

### Automating even further ...
 -  First create this wrapper script in a dor belonging to your host's `$PATH`:
 ```bash
$ cat ~/bin/sedstr
#!/bin/bash
old="$1"
new="$2"
file="${3:--}"
escOld=$(sed 's/[^^\\]/[&]/g; s/\^/\\^/g; s/\\/\\\\/g' <<< "$old")
escNew=$(sed 's/[&/\]/\\&/g' <<< "$new")
sed -i "s/$escOld/$escNew/g" "$file"
```
 - Now use this wrapper to modify the minified js:
 ```bash
 #NOTE! Only tested this in bash!
 set +H
 ~/bin/sedstr "\n\ttar -xf vscode-server.tar.gz\n\tTAR_EXIT" "\n\ttar -xf vscode-server.tar.gz\n\tsed -i 's/if(this.n.isBuilt)re/if(!this.n.isBuilt)re/' vscode-server*/out/vs/server/node/server.main.js\n\tTAR_EXIT" ~/.vscode-oss/extensions/ms-vscode-remote.remote-ssh-0.92.0/out/extension.js
 ```
