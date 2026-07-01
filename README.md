# SIM Alive

一个由 [zhujufeng](https://github.com/zhujufeng) 维护的海外 SIM 保号工具。

在线使用：

https://zhujufeng.github.io/sim-alive/

## 它是什么

很多海外手机号、漫游卡或低频使用 SIM，需要定期产生一次短信、通话或移动数据记录，才不容易被运营商判定为长期闲置。

SIM Alive 做的事情很简单：在你关闭 Wi-Fi、使用移动数据访问时，点击一次按钮，浏览器会下载同目录下约 120KB 的 `payload.txt`，用来产生一次轻量的移动数据使用记录。

它不是 Giffgaff 专用工具。Giffgaff 是常见使用场景之一；其他需要定期产生移动数据记录的海外 SIM，也可以参考这个思路。是否真正满足保号规则，以对应运营商的最新规则和账单记录为准。

## 功能特点

- 单次约 120KB，避免下载过大浪费余额
- 每次请求都带随机参数，降低浏览器和 CDN 缓存命中的概率
- 使用 `fetch` 的 `cache: "no-store"`，并附带 no-cache 请求头
- 页面不自动加载大图，避免打开页面本身消耗过多移动数据
- 显示本次实际拉取的数据大小
- 使用记录只保存在当前浏览器本地
- 无后台、无登录、无统计、无第三方接口
- 完全静态，可部署到 GitHub Pages、Cloudflare Pages、Gitee Pages

## 使用步骤

1. 插入需要保号的海外 SIM。
2. 先用 Wi-Fi 打开 https://zhujufeng.github.io/sim-alive/。
3. 等页面加载完成后，关闭 Wi-Fi，确认手机正在使用该 SIM 的移动数据。
4. 尽量关闭其他 App 的移动数据权限，只保留浏览器。
5. 点击 `开始保号`。
6. 页面显示完成后，立即关闭移动数据。
7. 到运营商 App、官网、USSD 或账单中确认是否产生记录。

也可以直接用移动数据打开页面。当前页面不会自动加载大图，打开页面本身只会产生少量 HTML/CSS/JS 流量；主要消耗来自点击按钮后的 `payload.txt`。

## 适用场景

- Giffgaff 等英国 SIM
- 长期备用的海外手机号
- 偶尔才需要接码、收短信或旅行使用的 SIM
- 需要定期产生移动数据使用记录的预付费卡

不适合：

- 明确要求短信或通话活跃的运营商
- 对单次流量有最低门槛且高于本项目 payload 的运营商
- 已停机、欠费、未开通漫游数据的 SIM

## 防缓存机制

本项目有防缓存设计，但它是客户端层面的防缓存。

核心代码：

```js
const url = new URL("payload.txt", location.href);
url.searchParams.set("v", `${Date.now()}-${Math.random().toString(36).slice(2)}`);

const res = await fetch(url, {
  cache: "no-store",
  headers: {
    "Cache-Control": "no-store, max-age=0",
    Pragma: "no-cache"
  }
});
```

这样做可以让每次点击都请求一个不同的 URL，并提醒浏览器不要使用缓存。

需要注意：GitHub Pages 是静态托管，不能像自有服务器一样给单个文件配置响应头。因此这里不承诺“绝对不缓存”，但对这种轻量保号场景已经足够实用。若你需要强控制响应头，可以部署到 Cloudflare Worker、Vercel Function 或自己的服务器。

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

### 这个工具适用于所有国外手机号吗？

不保证。它适用于“产生移动数据记录即可保持活跃”的场景。如果运营商要求短信、通话、充值或特定消费，仍要按运营商规则来。

### 可以用 Wi-Fi 测试吗？

可以测试网页是否正常，但 Wi-Fi 不会消耗 SIM 的移动数据。真正保号时要关闭 Wi-Fi。

### 为什么要关闭其他 App 的联网权限？

后台 App 可能同步消息、照片、邮件或系统服务，偷跑流量可能远高于这个工具本身。

### 为什么选择约 120KB？

太小的请求可能不稳定，太大又浪费余额。120KB 是一个偏保守的折中值。

### 会上传隐私吗？

不会。它只请求本站同目录的 `payload.txt`，使用记录存在浏览器 `localStorage`，没有后台服务器。

## 安全提示

- 使用前建议自行查看源码。
- 操作前关闭 Wi-Fi，操作后关闭移动数据。
- 本项目不能保证运营商规则不变化，最终以对应运营商规则和账单为准。

## License

MIT
