FORK FROM [sentry-webpack-plugin](https://github.com/getsentry/sentry-webpack-plugin)

## Usage:

```javascript
const SentryWebpackPlugin = require("@js-core/sentry-webpack-plugin");

const config = {
  plugins: [
    new SentryCliPlugin({
        url: "https://xxx/",
        authToken: "xxxx",
        org: "xxx",
        project: "xxx",
        include: "./dist",
        ignore: ["node_modules", "webpack.config.js"],
        urlPrefix: `cdn address`,
        // 以下是新增选项
        replacePattern: [{ 
          asset: "app.min.js",  // 表示要对源码执行 handler 配置的处理
          handler: "./scripts/add-comments.js" // 
        }],
        // replacePattern: "./scripts/replace.js", 也支持传入字符串
        cleanUp: "./scripts/clean-map-files.js", // 执行清理工作
        releaseHook: "./scripts/release-hook.js", // 支持自定义release名称
    }),
  ],
};
```

`./scripts/add-comments.js` 文件如下:

```javascript
const crypto = require('crypto');

module.exports = function (code, { asset, pattern, compilation }) {
  const options = this.options;
  const hash = crypto.createHash('md5').update(code).digest('hex');
  return `${code}
  // hash values: ${hash}
  // built date: ${new Date()}`;
}
```

`./scripts/clean-map-files.js` 文件如下:

```javascript
const fs = require("fs");
module.exports = function(opts, compilation) {
  Object.keys(compilation.assets).forEach(fileName => {
    if (fileName.endWith(".js.map")) { 
      fs.unlink(fileName);
    }
  })
};
```