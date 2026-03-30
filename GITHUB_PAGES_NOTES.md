# GitHub Pages 部署筆記

## 問題現象
推送代碼到 GitHub 後，訪問 GitHub Pages 網址顯示 404

## 根本原因
GitHub Pages 在你啟用設定後，需要時間構建網站。推送代碼後構建流程可能還在進行中。

## 解決步驟

### 1. 檢查 GitHub Pages 設定是否啟用
在 GitHub 網頁上：
- 進入 Repository → Settings → Pages
- 確認 "Source" 設定為 `main branch` / `main` (或 `main /` root)
- 確認 HTTPS 已啟用

### 2. 檢查構建狀態
- 在 Repository → Actions 頁面查看
- 找到最新的 "pages build and deployment" 工作流
- 確認狀態是 ✅ (完成) 或 ⏳ (進行中)

### 3. 等待構建完成
GitHub Pages 構建通常需要 **1-5 分鐘**
- 如果看到 ⏳ 進行中：等待完成
- 如果看到 ❌ 失敗：檢查錯誤日誌

### 4. 清除瀏覽器緩存
```bash
# 快捷鍵清除
Windows/Linux: Ctrl + Shift + Delete
Mac: Cmd + Shift + Delete

# 或用無痕視窗重新訪問
```

## 檢查清單
- [ ] 已在 GitHub Settings 啟用 Pages
- [ ] 源分支設為 `main`
- [ ] `index.html` 已在根目錄
- [ ] 代碼已推送到 GitHub (`git push`)
- [ ] Actions 頁面顯示最新構建已完成 ✅
- [ ] 清除瀏覽器緩存後訪問網站

## GitHub Pages 文檔
https://docs.github.com/en/pages/getting-started-with-github-pages
