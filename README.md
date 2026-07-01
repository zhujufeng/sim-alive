# Giffgaff 保号助手

一个由 [zhujufeng](https://github.com/zhujufeng) 维护的开源 Giffgaff 保号小工具。

在线使用：

https://zhujufeng.github.io/gg-keeper/

## 功能特点

- 每次点击拉取约 120KB 数据
- 加时间戳和随机参数，尽量避免浏览器缓存
- 显示本次实际拉取的数据大小
- 使用记录只保存在当前浏览器本地
- 无后台、无登录、无统计、无第三方接口
- 完全静态，可部署到 GitHub Pages、Cloudflare Pages、Gitee Pages

## 使用步骤

1. 插入 Giffgaff SIM 卡。
2. 关闭 Wi-Fi，确保手机正在使用移动数据。
3. 尽量关闭其他应用的移动数据权限，只保留浏览器。
4. 打开 https://zhujufeng.github.io/gg-keeper/。
5. 点击 `开始保号`。
6. 页面显示完成后，立即关闭移动数据。
7. 可拨打 `*100#` 查询余额变化。

## 手机设置建议

### iOS

1. 打开 `设置 -> 蜂窝网络`。
2. 关闭不需要联网的 App 蜂窝数据权限。
3. 只保留 Safari 或你要使用的浏览器。
4. 建议关闭 `Wi-Fi 助理`。

### Android

不同品牌入口略有差异，通常在：

- `设置 -> 移动网络 -> 流量管理 -> 应用联网`
- `设置 -> 应用管理 -> 权限管理 -> 联网控制`

建议只允许一个浏览器使用移动数据，其他 App 暂时关闭。

## 技术原理

页面点击按钮后，会请求同目录下的 `payload.txt`：

```js
const bust = `${Date.now()}-${Math.random().toString(36).slice(2)}`;
const res = await fetch(`payload.txt?${bust}`, { cache: "no-store" });
```

`payload.txt` 大小约为 120KB。URL 上的随机参数和 `cache: "no-store"` 用来降低浏览器直接读取缓存的概率。

## 文件说明

- `index.html`：页面、样式和交互逻辑
- `payload.txt`：约 120KB 的随机文本，用于产生实际下载流量
- `README.md`：项目说明
- `LICENSE`：MIT 开源许可证

## 自己部署

### GitHub Pages

1. Fork 或复制本仓库。
2. 进入仓库 `Settings -> Pages`。
3. Source 选择 `Deploy from a branch`。
4. Branch 选择 `main`，目录选择 `/ (root)`。
5. 保存后等待 1-2 分钟。
6. 访问 `https://你的用户名.github.io/仓库名/`。

### Cloudflare Pages

1. 登录 Cloudflare。
2. 进入 `Workers & Pages -> Create`。
3. 连接本 GitHub 仓库。
4. 构建命令留空，输出目录选择 `/`。
5. 部署完成后访问 Cloudflare 分配的域名。

## 常见问题

### 为什么不是直接发短信保号？

发短信也可以，但很多用户只是想低成本产生一次有效使用记录。这个工具走移动数据，成本通常更低。

### 为什么要关闭其他 App 的联网权限？

因为后台 App 可能同步消息、照片、邮件或系统服务，实际偷跑流量可能远高于这个工具本身。

### 可以用 Wi-Fi 测试吗？

可以测试网页是否正常，但 Wi-Fi 不会消耗 Giffgaff SIM 的移动数据。真正保号时要关闭 Wi-Fi。

### 为什么选择约 120KB？

太小的请求可能不稳定，太大又浪费余额。120KB 是一个偏保守的折中值。

### 这个工具会上传隐私吗？

不会。它只请求本站同目录的 `payload.txt`，使用记录存在浏览器 `localStorage`，没有后台服务器。

## 安全提示

- 本项目开源，建议使用前自行查看源码。
- 操作前关闭 Wi-Fi，操作后关闭移动数据。
- 本项目不能保证运营商计费规则不变化，实际以 Giffgaff 规则和账单为准。

## 致谢

本项目参考了社区里类似的 Giffgaff 保号工具思路，并按自己的使用场景做了开源整理。

## License

MIT
