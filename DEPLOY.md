# Onion Demo · 公网静态部署

`site/` 是一个**纯静态**站点(数据已内联进 HTML,仅外链 IBM Plex 字体 CDN),
不依赖任何服务器。把这两个文件放到任意静态托管即公网可查:

- `index.html` —— 产品 landing 页(根路径)
- `dashboard.built.html` —— 实时数据大屏(landing 页内「实时大屏」按钮跳转)

已在本目录初始化好 git 提交,可直接推送。下面三条路线任选其一。

---

## 路线 A · GitHub Pages(推荐,和参考站 cast-claw 同路,永久免费)

需要你自己的 GitHub 账号(认证这步我无法代做)。

### A1. 有 gh CLI(最省事)
```bash
# 一次性安装并登录(浏览器交互)
brew install gh && gh auth login

cd site
gh repo create onion-demo --public --source=. --push
gh api -X POST repos/{owner}/onion-demo/pages -f source[branch]=main -f source[path]=/ 2>/dev/null \
  || echo "若报错,去仓库 Settings → Pages 手动把 Source 设为 main / (root)"
```
几十秒后访问:`https://<你的用户名>.github.io/onion-demo/`

### A2. 无 gh、用 git 推送
1. 到 https://github.com/new 建一个空仓库(如 `onion-demo`,Public,**不要**勾初始化)。
2. 回到本目录推送:
```bash
cd site
git branch -M main
git remote add origin https://github.com/<你的用户名>/onion-demo.git
git push -u origin main          # 会提示输入用户名 + Personal Access Token
```
3. 仓库 **Settings → Pages → Source** 选 `main` / `(root)`,保存。
4. 一两分钟后:`https://<你的用户名>.github.io/onion-demo/`

### A3. 完全不想用命令行
到新建的仓库页点 **Add file → Upload files**,把 `index.html` 和
`dashboard.built.html` 拖进去 Commit,再按第 3 步开 Pages。

---

## 路线 B · Netlify Drop(最快,零命令零 git)

1. 打开 https://app.netlify.com/drop
2. 把整个 `site/` 文件夹拖进去。
3. 立刻得到一个 `https://<随机名>.netlify.app` 公网网址。
   (注册账号后可改成固定子域名并永久保留;不注册的临时站点会过期。)

---

## 路线 C · Cloudflare Pages

1. https://pages.cloudflare.com → Create a project。
2. 连上面 A 建好的 GitHub 仓库,或用 `Direct Upload` 拖 `site/`。
3. 构建命令留空、输出目录填 `/`(纯静态无需构建)。
4. 得到 `https://<项目名>.pages.dev`。

---

## 更新数据后怎么重新发布

大屏数据是内联在 `dashboard.built.html` 里的。服务器上重新跑出新 run 后:

```bash
# 在 CastCodex 根目录,把服务器上新的 built 大屏拉回本地 site/
./cc 'cat demo/dashboard.built.html' > site/dashboard.built.html   # 或用 viz.py 重新构建
cd site && git add -A && git commit -m "update dashboard" && git push   # 路线A:推送即自动重新发布
```
Netlify/Cloudflare 若连了 GitHub 仓库,push 后同样会自动重新部署。

---

## 备注
- 站点纯静态,GPU 服务器**关机也不影响**公网访问。
- 字体走公网 CDN,失败会自动回退系统字体,不影响功能与布局。
- 不含任何密钥/凭证(已确认),可放心公开。
