# 日程工作台 · 手机版（PWA）部署说明

这个文件夹里的内容，可以直接上传到任何支持 HTTPS 的静态网站托管，然后在 iPhone 上「添加到主屏幕」，当成 App 用。

```
pwa/
├── index.html              应用本体（和电脑上的是同一个文件）
├── manifest.webmanifest    应用名称、图标、启动方式
├── sw.js                   离线缓存（断网也能打开）
└── icons/                  主屏幕图标
```

---

## 一、上传（二选一）

### 方式 A：GitHub Pages（免费，推荐）

1. 注册 / 登录 GitHub，点右上角 **+ → New repository**
2. 名字随便填，比如 `schedule`，选 **Public**，创建
3. 进入仓库，点 **Add file → Upload files**，把本文件夹里的**全部文件和 icons 文件夹**拖进去，提交
4. 仓库 **Settings → Pages**：
   - Source 选 **Deploy from a branch**
   - Branch 选 **main**，目录选 **/ (root)**，保存
5. 等 1–2 分钟，页面顶部会出现网址，形如：
   `https://你的用户名.github.io/schedule/`

### 方式 B：Cloudflare Pages（免费，不用 Git）

1. 打开 `pages.cloudflare.com`，登录后选 **Create a project → Direct Upload**
2. 把整个 `pwa` 文件夹拖进去，部署
3. 会得到一个 `https://xxx.pages.dev` 的网址

> 关键点：必须通过 **https://** 打开，直接双击本地文件是不行的（iOS 不允许）。

---

## 二、装到 iPhone 主屏幕

1. 用 **Safari** 打开上面的网址（一定要 Safari，微信/Chrome 里不行）
2. 点底部的 **分享** 按钮（方框带向上箭头）
3. 往下找到 **添加到主屏幕** → 添加
4. 桌面上就会出现六边形图标，点开是全屏、无地址栏

首次打开是空的，需要把电脑上的数据导进去：电脑上点 **导出**，把 JSON 发到手机（微信/隔空投送/邮件都行），在手机应用里点 **导入** 选择那个文件。

---

## 三、两台设备的数据怎么同步

**页面是同一份，数据是各自本地的。** 具体说：

| | 会同步吗 | 怎么处理 |
|---|---|---|
| 界面 / 功能更新 | ✅ 会 | 只需改一次 `index.html` 并重新上传，两边刷新就是新版 |
| 任务内容 | ✅ 会（需配置） | 按下面的「云同步」设置一次 |
| 删除任务 | ❌ 不会 | 需要两边各删一次 |

### 云同步（GitHub Gist，推荐）

应用里已经内置了同步功能，用你 GitHub 账号下的一个**私有 Gist** 当中转，不经过任何第三方服务器。

**第一步：生成 Token（只需一次）**

1. 登录 GitHub → 右上角头像 → **Settings**
2. 左侧最下面 **Developer settings**
3. **Personal access tokens → Tokens (classic) → Generate new token (classic)**
4. Note 随便填（比如 `schedule-sync`），Expiration 选 **No expiration**
5. 权限只勾 **gist** 这一项
6. 生成后复制那串 `ghp_...`（只显示一次，先粘到备忘录里）

**第二步：两台设备各填一次**

1. 打开应用 → 顶栏 **同步** 按钮
2. 粘贴 Token → **立即同步**
3. 第一次会自动创建一个叫 `dsh-schedule-workbench-sync` 的私有 Gist
4. 另一台设备填**同一个 Token**、点一次同步即可

之后：

- 改动后约 2.5 秒自动上传
- 打开应用、切回窗口时自动拉取
- 每 2 分钟自动拉一次
- 冲突按「修改时间较晚的为准」合并

⚠️ Token 保存在本机浏览器里，别把设备借给别人；如果泄露，去 GitHub 点 **Revoke** 即可作废。

### 手动同步（不想用 Token 时）

导出 JSON → 发到另一台设备 → 导入。导入是**合并**模式，不会覆盖已有任务，按任务 ID 和更新时间取较新的那份。

⚠️ 删除操作两边不会自动同步，需要各删一次。

---

## 四、以后修改应用

改完电脑上的 `日程工作台.html` 后：

1. 把它复制成 `pwa/index.html` 覆盖
2. 重新上传到 GitHub / Cloudflare
3. 手机重新打开即可（Service Worker 是「网络优先」，会自动拿新版）

如果图标或名称变了，记得同步改 `manifest.webmanifest` 和 `icons/`。
