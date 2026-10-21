# PDFNest 项目资产与部署文档

- **项目名称**：pdfnest（纯客户端浏览器端 PDF / 图像处理工具箱）
- **官方仓库**：https://github.com/OmerMirza9/pdfnest
- **GitHub 维护仓库**：https://github.com/GD2021/pdfnest
- **自定义主域名**：https://pdf.files.cloudns.org
- **架构特点**：Zero Backend 纯前端架构，采用 Cloudflare Workers 静态资产托管（wrangler [assets]），所有文件操作均在浏览器本地沙箱执行。

---

## 规范命名资产清单

| 资源类型 | Cloudflare 资源名称 / 域名 | 归属 Zone / 绑定 | 说明 |
|---|---|---|---|
| **Worker 服务** | `pdfnest` | Account: `Waka` (`fc6f79e77dca61be461e2dedb82244f5`) | Workers Static Assets 托管服务 |
| **自定义域名** | `pdf.files.cloudns.org` | `files.cloudns.org` (`a1bd0c4beb28cdbf11528aec0c473bea`) | 挂载于 Worker，由 Google Trust Services 提供通配符 SSL 证书保护 |
| **安全策略** | `_headers` 安全响应标头 | `ASSETS` | 自动应用 X-Frame-Options、nosniff 及静态资源缓存策略 |

---

## 自动化同步与部署配置

- **Workflow 文件**：`.github/workflows/sync-and-deploy.yml`
- **定时触发**：每天北京时间凌晨 **04:00**（UTC 20:00）自动执行
- **支持手动**：支持在 GitHub 仓库 Actions 页面手动点击 `Run workflow` 触发
- **执行逻辑**：
  1. 拉取官方上游 `OmerMirza9/pdfnest:main`；
  2. 比对是否有新提交，若有则自动合并到 `GD2021/pdfnest:main` 并推送到仓库；
  3. 执行 `npx wrangler deploy` 重新发布至 Cloudflare Workers。
- **仓库 Secrets 配置**：
  - `CLOUDFLARE_ACCOUNT_ID`：`fc6f79e77dca61be461e2dedb82244f5`（已配置）
  - `CLOUDFLARE_API_TOKEN`：已配置（支持全自动云端构建与部署）

---

## 本地日常维护指引

进入本地项目目录：
```bash
cd /Users/jack/Desktop/运维/Cloudflare_项目/pdfnest
```

- **本地即时部署到 Cloudflare**：
  ```bash
  npx wrangler deploy
  ```
- **本地启动调试预览**：
  ```bash
  npx wrangler dev
  ```
- **手工同步上游更新**：
  ```bash
  git fetch upstream main
  git merge upstream/main
  git push origin main
  ```
