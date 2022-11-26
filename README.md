# VSCode OSS Remote-SSH fix
### Run this in the server after initial connection:
```bash
sed -i 's/if(this._environmentService.isBuilt)/if(!this._environmentService.isBuilt)/' \
    .vscode-server-oss/bin/6261075646f055b99068d3688932416f2346dd3b/out/vs/server/node/server.main.js
```
