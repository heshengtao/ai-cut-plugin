# AI CUT 应用扩展市场索引

这是 [AI CUT](https://github.com/heshengtao/ai-cut) 的**应用扩展**（能加画布节点、命令与文案的第三方扩展）的商店仓库。
应用默认从这里读 `index.json`：

```
https://heshengtao.github.io/ai-cut-plugin/index.json
```

> 「应用扩展」不是 OFX 插件。OFX 是视频特效（走系统的 OpenFX 插件宿主）；应用扩展是 npm 包，
> 装进 daemon 之后能给画布加节点、给命令面板加动作。两者在界面上分属「OFX 插件」与「应用扩展」。

## 目前状态

索引是**空的**（`plugins: []`），等第一批应用扩展发布后再加。空索引是合法状态：应用的「发现」
页会显示空态，不会报错。

## 怎么把一个应用扩展加进来

1. 扩展作者发一个 npm 包（或一个 `.tgz` 直链），包里要有扩展清单，并声明 `engines.aicut`
   （例如 `">=1.0.0 <2.0.0"`）。格式见 [插件文档](https://github.com/heshengtao/ai-cut/blob/main/docs/plugins.md)。
2. 在 `index.json` 的 `plugins` 里加一条：`name`、可选 `title` / `description` / `author` /
   `homepage`，以及 `versions`。**每个版本都必须带**：
   - `version`：精确 semver；
   - `publishTimestamp`：Unix 秒；
   - `source`：`{ "type": "npm" }` 或 `{ "type": "tgz", "url": "https://…" }`；
   - `sha256`：64 位十六进制的包摘要（daemon 会校验，缺了整份索引会被拒绝）；
   - `size`：包字节数（大于 0）。
3. 提交并推送 `index.json`。应用下一次刷新（缓存 10 分钟）就能看到；也可以在
   「设置 → 插件市场」里手动改索引源。

示例：

```jsonc
{
  "name": "@you/plugin-name",
  "title": { "zh": "中文名", "en": "English name" },
  "description": { "zh": "一句话说明" },
  "author": "You",
  "versions": [
    {
      "version": "0.1.0",
      "publishTimestamp": 1795000000,
      "source": { "type": "npm" },
      "engines": { "aicut": ">=1.0.0 <2.0.0" },
      "sha256": "0123456789abcdef0123456789abcdef0123456789abcdef0123456789abcdef",
      "size": 12345
    }
  ]
}
```

## 安全

索引**没有签名**：标题、作者、标签都是市场自述，不代表任何验证。daemon 会校验每个版本的
`sha256`，应用在安装前也要求用户确认——装应用扩展等于在这台机器上执行第三方代码。

## 许可

索引数据以 MIT 发布；各扩展的许可见其自身分发渠道。
