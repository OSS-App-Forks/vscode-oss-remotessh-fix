# VSCode OSS Remote-SSH fix
### Run this in the server after initial connection fails:
```bash
sed -i 's/if(this._environmentService.isBuilt)/if(!this._environmentService.isBuilt)/' \
    .vscode-server-oss/bin/6261075646f055b99068d3688932416f2346dd3b/out/vs/server/node/server.main.js
```
### To automate this on all servers
Replace the following line in `~/.vscode-oss/extensions/ms-vscode-remote.remote-ssh-0.92.0/out/extension.js`:
```
From:
\n\ttar -xf vscode-server.tar.gz\n\tTAR_EXIT

To:
\n\ttar -xf vscode-server.tar.gz\n\tsed -i 's/if(this._environmentService.isBuilt)/if(!this._environmentService.isBuilt)/' vscode-server*/out/vs/server/node/server.main.js\n\tTAR_EXIT
```
```diff
1c1
< \n\ttar -xf vscode-server.tar.gz\n\tTAR_EXIT
---
> \n\ttar -xf vscode-server.tar.gz\n\tsed -i 's/if(this._environmentService.isBuilt)/if(!this._environmentService.isBuilt)/' vscode-server*/out/vs/server/node/server.main.js\n\tTAR_EXIT
```
Pro-tip: Use [js-beautify](https://www.npmjs.com/package/js-beautify) to make extension.js more readable
