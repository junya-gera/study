6/5(Wed)
Next.js のプロジェクトに npx storybook@latest init コマンドで
storybook をインストールしようとしたらエラーが発生。

```
$ npx storybook@latest init
C:\Users\junya-wada\AppData\Local\npm-cache\_npx\bc7e1e37fcb46ffc\node_modules\@storybook\cli\bin\index.js:23
  throw error;
  ^

Error: Cannot find module 'recast'
Require stack:
- C:\Users\junya-wada\AppData\Local\npm-cache\_npx\bc7e1e37fcb46ffc\node_modules\@storybook\csf-tools\dist\index.js
- C:\Users\junya-wada\AppData\Local\npm-cache\_npx\bc7e1e37fcb46ffc\node_modules\@storybook\core-common\dist\index.js
- C:\Users\junya-wada\AppData\Local\npm-cache\_npx\bc7e1e37fcb46ffc\node_modules\@storybook\telemetry\dist\index.js
- C:\Users\junya-wada\AppData\Local\npm-cache\_npx\bc7e1e37fcb46ffc\node_modules\@storybook\cli\dist\generate.js
- C:\Users\junya-wada\AppData\Local\npm-cache\_npx\bc7e1e37fcb46ffc\node_modules\@storybook\cli\bin\index.js
    at Module._resolveFilename (node:internal/modules/cjs/loader:1134:15)
    at Module._load (node:internal/modules/cjs/loader:975:27)
    at Module.require (node:internal/modules/cjs/loader:1225:19)
    at require (node:internal/modules/helpers:177:18)
    at Object.<anonymous> (C:\Users\junya-wada\AppData\Local\npm-cache\_npx\bc7e1e37fcb46ffc\node_modules\@storybook\csf-tools\dist\index.js:1:1853)
    at Module._compile (node:internal/modules/cjs/loader:1356:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1414:10)
    at Module.load (node:internal/modules/cjs/loader:1197:32)
    at Module._load (node:internal/modules/cjs/loader:1013:12)
    at Module.require (node:internal/modules/cjs/loader:1225:19) {
  code: 'MODULE_NOT_FOUND',
  requireStack: [
    'C:\\Users\\junya-wada\\AppData\\Local\\npm-cache\\_npx\\bc7e1e37fcb46ffc\\node_modules\\@storybook\\csf-tools\\dist\\index.js',
    'C:\\Users\\junya-wada\\AppData\\Local\\npm-cache\\_npx\\bc7e1e37fcb46ffc\\node_modules\\@storybook\\core-common\\dist\\index.js',
    'C:\\Users\\junya-wada\\AppData\\Local\\npm-cache\\_npx\\bc7e1e37fcb46ffc\\node_modules\\@storybook\\telemetry\\dist\\index.js',
    'C:\\Users\\junya-wada\\AppData\\Local\\npm-cache\\_npx\\bc7e1e37fcb46ffc\\node_modules\\@storybook\\cli\\dist\\generate.js',
    'C:\\Users\\junya-wada\\AppData\\Local\\npm-cache\\_npx\\bc7e1e37fcb46ffc\\node_modules\\@storybook\\cli\\bin\\index.js'
  ]
}

Node.js v18.19.0
```

npx storybook init としたらインストールできた。
バージョンは同じ 8.1.5 だった。

storybook に tailwind を適用させたかったが、何をやってもだめだった。
https://zenn.dev/nbr41to/articles/2b05d056308dff

https://zenn.dev/nianjiang/articles/a6b5516cd5ebb1