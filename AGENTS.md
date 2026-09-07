# YUN CPA Hugo 網站操作指引

本專案是以 Hugo Extended 與 PaperMod 建立的靜態網站，正式網址為 `https://www.yuncpa.tw/`，由 GitHub Pages 發布。此文件的範圍是本機編輯、預覽與 GitHub Pages 發布。

## 專案慣例

- 專案根目錄是 `C:\Users\Peter\Dropbox\GPT\web`。
- 主要設定在 `hugo.yaml`；佈景主題位於 `themes/PaperMod`。
- 優先以 `assets/css/extended/yuncpa.css` 覆寫外觀，不直接修改 PaperMod 原始檔，避免日後更新主題時遺失變更。
- 自訂 HTML 放在專案的 `layouts/_partials/`。目前 `extend_head.html` 載入 Noto Sans TC，`extend_post_content.html` 顯示作者資訊與相關文章。
- 中文內容使用 UTF-8。文章檔名採簡短英文小寫 slug，例如 `content/posts/triangular-trade-income-tax.md`；中文標題寫在 front matter 的 `title`。
- 首頁綠色介紹區保持精簡；文章內頁在寬螢幕將目錄置於正文右側並於捲動時停留。首頁文章清單不顯示文章目錄。
- 保留現有配色、Noto Sans TC 字型、作者卡片、相關文章、麵包屑、閱讀時間與字數顯示，除非使用者明確要求改動。

## 開始工作

先查看 `git status --short`，保留使用者尚未提交的變更，不要覆寫或還原不相關檔案。

本機預覽：

```powershell
Set-Location 'C:\Users\Peter\Dropbox\GPT\web'
hugo server -D
```

瀏覽器開啟 `http://localhost:1313/`。預覽結束時按 `Ctrl+C`。

若 PowerShell 找不到 `hugo`，可直接執行目前已安裝的版本：

```powershell
& 'C:\Users\Peter\AppData\Local\Microsoft\WinGet\Packages\Hugo.Hugo.Extended_Microsoft.Winget.Source_8wekyb3d8bbwe\hugo.exe' server -D
```

## 建立或編輯文章

建立新文章前，先確認目標檔案是否已存在。若已存在，直接編輯原檔，不要再次執行 `hugo new`，也不要刪除或覆蓋內容以排除 `target path conflicts with existing content` 錯誤。

文章文字以使用者提供的 Markdown 或指定來源為準：

- 未經使用者明確要求，不得自行摘要、改寫、潤飾、增補、刪節或調整文章立場與措辭。
- 使用者只要求排版、目錄、字型或網站功能時，不得改動文章正文。
- Front matter 的 `tags` 完全以新聞稿或使用者指定來源所列標籤為準，不得自行新增、改名、合併、刪除或依內容推測標籤。來源未列標籤時，不主動補上，應先詢問使用者。
- 從網頁建立 Markdown 時，先確認使用者要「忠實整理」還是「摘要改寫」；未指定時不得把 AI 摘要當成原文。
- 若網頁內容、已發布內容與本機 Markdown 不一致，先列出差異並詢問哪一份是準確版本，不得自行同步或選擇其一覆蓋。
- Markdown 標記（例如 `##`、`**`、連結及 front matter）在網頁上會轉為標題、粗體、超連結及頁面資料；呈現方式可以不同，但正文文字不得因此改寫。
- 製作網站發布版時，刪除只供 Obsidian 使用的「所屬 MOC」段落及其清單；這項刪除不影響 front matter 的 `tags`，也不得連帶刪除其他正文。

```powershell
hugo new content posts/example-article.md
```

文章至少應有 `title`、`date`、`draft`、`categories`、`tags` 與摘要。需要在 `hugo server -D` 中預覽草稿；準備發布時將 `draft` 設為 `false`。

文章內使用二級、三級標題建立目錄。文章只有足夠的標題層級時，右側目錄才有實際內容。

## 新文章固定流程

新文章必須依序完成以下階段，不得跳步：

1. Codex 依使用者提供的來源產生可發布的 Hugo Markdown，保留正文與來源標籤，設置必要 front matter，但不發布。
2. 使用者直接在 `content/posts` 的 Markdown 檔案中修正文稿。此階段 Codex 不得自行改寫使用者修正後的文字。
3. 使用者表示修正完成後，先執行 Hugo 本機預覽與建置檢查，讓使用者查看首頁及文章內頁；發現內容疑義時只回報，不自行改稿。
4. 只有使用者看過預覽並明確要求發布後，才可提交並推送至 GitHub Pages，再確認線上部署成功。

產生 Markdown、使用者修稿、預覽及發布是四個獨立階段。完成前一階段不代表獲准執行下一階段。

## 完成前檢查

每次修改後至少執行：

```powershell
hugo --gc --minify
git diff --check
git status --short
```

Hugo 必須成功建置。PaperMod 的 Hugo deprecation warning 可記錄但不視為本次修改失敗；其他錯誤需先處理。若改動外觀，應用瀏覽器實際查看首頁與一篇文章內頁。

## 發布規則

- 修改本機網站不等於發布。
- 只有使用者明確要求「發布」時，才可建立 commit 並 push。
- 發布前再次確認差異中只有本次工作，並向使用者說明將發布哪些內容。
- 推送至 `main` 後，`.github/workflows/hugo.yaml` 會自動建置並部署 GitHub Pages。
- 推送成功不代表網站已完成更新；應確認 GitHub Pages workflow 成功後才回報發布完成。
- 不要修改自訂網域或 DNS，除非使用者明確要求。

典型發布命令：

```powershell
git add -- <本次修改的檔案>
git commit -m "描述本次網站修改"
git push origin main
```

不要使用 `git add .` 將不相關檔案一起發布。
