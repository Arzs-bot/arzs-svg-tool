# ARZS 圖片工具站 CHANGELOG

## 2026-07-21

### 修復 tools.arzs.com.tw 全站 404（Shopify 嵌入載不出來）
- 症狀：Shopify 頁面嵌入工具後 404；直連 `tools.arzs.com.tw/` 與 `/toolbox/` 皆 404，
  且憑證是 GitHub 預設 `*.github.io`（非本網域）。
- 根因：`2026-07-19` 那波「全 repo 轉 GitHub Private」把本站 repo（`arzs-svg-tool`）
  一起轉成私有 → **免費方案的私有 repo 會自動停用 GitHub Pages** → 全站 404。
  程式碼完全沒壞，純部署層問題。
- 另發現亂源：存在兩個內容重複、都寫同一 CNAME 的 repo
  （`arzs-svg-tool` 與 `arzs-vector-bg-remove`）。逐檔 sha 比對確認內容 100% 相同，
  僅 `arzs-svg-tool` 缺一個 `.gitignore`。
- 處理：
  - 補 `.gitignore` 進 `arzs-svg-tool`（與本機同步）
  - 本機 remote 從 `arzs-vector-bg-remove` 改指向 `arzs-svg-tool`，reset 對齊歷史
  - `arzs-svg-tool` 轉 **public** → 啟用 Pages（main /docs）→ 綁 `tools.arzs.com.tw`
  - Let's Encrypt 憑證自動發放（state=authorized）→ 開 Enforce HTTPS
  - 重複的 `arzs-vector-bg-remove`：移除其 CNAME + 封存（archive 唯讀）
    （token 無 delete_repo scope，未實際刪除；如需徹底刪除由東大金於網頁操作）
- 驗收：憑證 `CN=tools.arzs.com.tw`；`/` 與 `/toolbox/` 皆 HTTP 200；
  `/toolbox` 301 導向 `/toolbox/`。

## 2026-07-18

### 初始部署
- 建立 GitHub repo（Arzs-bot/arzs-svg-tool），部署至 GitHub Pages
- 綁定自訂網域 tools.arzs.com.tw（GoDaddy CNAME → arzs-bot.github.io）
- 工具一：SVGcode 向量化（docs/index.html，官方 build + ARZS 品牌化）
- 工具二：ARZS 圖片工具箱（docs/toolbox/index.html，AI 去背 × 向量化）

### toolbox UI 改版
- 配色改為 ARZS Horizon 主題風格（白底、黑字、Inter、scheme-1 對應色）
- 按鈕圓角 14px、卡片圓角 4px（對應 Shopify 主題設定）
- 容器加寬 1100px → 1320px，dropzone 加高

### toolbox 功能修復
- 修 AI 去背模型載入失敗：補 publicPath → staticimgly.com
- 修 onnxruntime-web bare specifier：加 importmap + wasmPaths
- 修 resultBox 透明疊圖問題：原圖與結果各自裁切不重疊

### toolbox 介面定版
- 改為左右雙欄（原圖左、結果右），移除比對滑桿
- 結果欄獨立縮放：滾輪縮放（以游標為中心）+ 按鈕控制
- 向量化預設色彩層級 5 → 3（路徑更簡潔）
- 動到的檔案：docs/toolbox/index.html

### 測試結果
- AI 去背：正常（首次下載 ~80MB 模型後快取）
- 縮放：滾輪、按鈕皆正常
- 向量化：Potrace 演算法，建議搭配「去背＋向量化」模式效果最佳
