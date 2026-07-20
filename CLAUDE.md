# ARZS 圖片工具站 — 部署任務

## 你的任務

把這個資料夾部署到 GitHub Pages，綁定自訂網域 `tools.arzs.com.tw`。

## 專案結構（已完成，不要修改內容）

```
.
├── CLAUDE.md          ← 本檔案
├── LICENSE            ← GPL-2.0（必須保留，開源授權要求）
└── docs/              ← GitHub Pages 發佈目錄
    ├── CNAME          ← 已寫入 tools.arzs.com.tw
    ├── index.html     ← SVGcode 向量化工具（官方 build + ARZS 品牌化）
    ├── assets/        ← SVGcode 的 JS/CSS/workers
    ├── sw.js          ← Service Worker
    └── toolbox/
        └── index.html ← ARZS 圖片工具箱（AI 去背 × 向量化，單檔自足）
```

## 執行步驟

### 1. 建 repo 並推送
```bash
gh repo create arzs-svg-tool --public --description "ARZS 圖片工具站：AI 去背 × 圖片轉向量 SVG（基於開源 SVGcode，GPL-2.0）"
git init
git add .
git commit -m "ARZS 圖片工具站初始部署"
git branch -M main
git remote add origin https://github.com/$(gh api user -q .login)/arzs-svg-tool.git
git push -u origin main
```

### 2. 開啟 GitHub Pages
```bash
gh api repos/$(gh api user -q .login)/arzs-svg-tool/pages \
  -X POST \
  -f "source[branch]=main" \
  -f "source[path]=/docs"
```
如果 API 回 409（已存在）就跳過。如果 gh api 失敗，改用網頁指引使用者手動操作：
repo → Settings → Pages → Source: main / /docs。

### 3. 設定自訂網域
```bash
gh api repos/$(gh api user -q .login)/arzs-svg-tool/pages \
  -X PUT \
  -f cname="tools.arzs.com.tw"
```

### 4. 完成後告訴使用者
輸出以下資訊給使用者（東大金）：

1. repo 網址
2. 需要手動到 **Cloudflare** 加一條 DNS 記錄：
   - 類型：`CNAME`
   - 名稱：`tools`
   - 目標：`<GitHub帳號>.github.io`
   - Proxy 狀態：**DNS only（灰雲）** ← 重要，GitHub Pages 要驗證網域
3. DNS 生效後（幾分鐘～1小時），回 GitHub repo → Settings → Pages 勾選 **Enforce HTTPS**
4. 驗證網址：
   - `https://tools.arzs.com.tw/` → SVGcode 向量化工具（介面可切繁中）
   - `https://tools.arzs.com.tw/toolbox/` → ARZS 圖片工具箱（去背+向量一條龍）

## ⚠️ 血淚教訓（2026-07-21）：這個 repo 必須維持 public，否則全站 404

- 本站靠 **GitHub Pages** 服務 `tools.arzs.com.tw`。**免費方案的私有 repo 不能跑 Pages。**
- `2026-07-19` 那波「全 repo 轉 GitHub Private」把本 repo 一起轉私有，Pages 被自動停用，
  全站 404（Shopify 嵌入也跟著掛）。**這個 repo 是 GPL-2.0 開源工具、無任何 ARZS 機密，
  本來就該 public，不要再把它轉 private。**
- 正式綁定的 repo 是 **`arzs-svg-tool`**（不是 `arzs-vector-bg-remove`——後者是重複品，
  已移除 CNAME 並封存）。本機資料夾的 remote 指向 `arzs-svg-tool`。
- 若哪天又 404，先查：(1) repo 是否又變 private？(2) `gh api repos/Arzs-bot/arzs-svg-tool/pages`
  是否還在？(3) 憑證是否為 `CN=tools.arzs.com.tw`（不是 `*.github.io`）。

## 注意事項

- **不要改動 docs/ 裡的任何檔案內容**——SVGcode 是官方 build 成品，改壞了會整站掛掉
- **LICENSE 必須進 repo**——GPL-2.0 授權要求，repo 也必須維持 public
- docs/index.html 的資源是絕對路徑（/assets/...），所以自訂網域是必要的，
  不綁網域直接用 username.github.io/arzs-svg-tool/ 訪問會 404
- toolbox/index.html 是完全自足的單檔（Potrace 引擎內嵌），
  唯一的外部依賴是 AI 去背模型（首次使用時從 imgly CDN 下載約 40MB）
- Service Worker（sw.js）會快取整站；日後更新 push 後要等 SW 更新週期，
  驗證新版用無痕視窗

## 驗收清單

- [ ] repo 建立且 public
- [ ] Pages 啟用（main / /docs）
- [ ] custom domain 設定為 tools.arzs.com.tw
- [ ] 告知使用者 Cloudflare DNS 設定值
- [ ] 提供兩個驗證網址
