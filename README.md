# 今晚看什麼：電影推薦網站

依類別、語言、片長與分級推薦電影，並列出台灣現正熱映與即將上映片單，可直接連到威秀、秀泰的單片頁面。

純靜態網站，不需要資料庫、後端或建置步驟。

```
movie-picker-github/
├── index.html       ← 網站本體（片單資料寫在檔案裡的 <script> 中）
├── _headers         ← Cloudflare 回應標頭設定
├── .gitignore
└── README.md
```

## 第一步：放上 GitHub

**用網頁上傳（不用安裝任何東西）**

1. 到 GitHub 按右上角 **+** → **New repository**，名稱例如 `movie-picker`，按 **Create repository**。
2. 在新 repo 頁面點 **uploading an existing file**。
3. 把這個資料夾**裡面的所有東西**（index.html、_headers、README.md、.gitignore）拖進去，按 **Commit changes**。
   - .gitignore 是隱藏檔，Mac 在 Finder 按 `Cmd + Shift + .` 才看得到；漏掉也不影響網站。

**用指令上傳**

```bash
cd movie-picker-github
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/你的帳號/movie-picker.git
git push -u origin main
```

## 第二步：連到 Cloudflare Pages

1. 登入 Cloudflare 後台 → **Workers & Pages** → **建立** → **Pages** → **連線至 Git**。
2. 授權 GitHub，選剛剛的 `movie-picker` repo。
3. 建置設定這樣填：
   - 架構預設（Framework preset）：**None**
   - 建置命令（Build command）：**留空**
   - 建置輸出目錄（Build output directory）：**留空**（或填 `/`）
4. 按 **儲存並部署**，完成後會拿到 `movie-picker.pages.dev` 網址。

這個資料夾也能直接用 GitHub Pages：repo 的 Settings → Pages → 來源選 main 分支、根目錄 `/`。

之後只要在 GitHub 改檔案並 commit，Cloudflare 會自動重新部署，大約一分鐘生效。

## 本機預覽（選用）

直接用瀏覽器打開 `index.html` 就能看。想模擬 Cloudflare 環境的話：

```bash
npx wrangler pages dev .
```

## 更新片單

打開 `index.html`，搜尋 `const M=[`，每部電影一行：

| 欄位 | 意思 |
| --- | --- |
| `t` / `e` | 中文 / 英文片名 |
| `y` | 上映日 `YYYY-MM-DD`（片庫只寫年份） |
| `g` | 類別陣列，例如 `["動作","科幻"]` |
| `l` | 語言：華語、英語、日語、韓語、其他 |
| `r` | 分級：0 普遍、1 保護、2 輔12、3 輔15、4 限制，未公布寫 `null` |
| `m` | 片長（分鐘），未知寫 `null` |
| `lib:1` / `re:1` / `imax:1` | 經典片庫 / 重映 / IMAX |
| `d` | 簡介 |

影城單片連結：`const VS=`（威秀片號）、`const ST=`（秀泰片號），key 要和片名 `t` 一字不差。
片號是影城網址最後的數字，例如威秀 `detail.aspx?id=9014` → `9014`，秀泰 `programs/13075/` → `13075`。

「現正熱映／即將上映」依瀏覽當天日期自動分類，不需手動移動。
