# obsidian-link

一個靜態轉址網頁：把一般的 `https://` 連結轉成 `obsidian://` 連結，讓**不支援自訂連結格式的 App**（例如 Telegram）也能一鍵打開 Obsidian 裡的某份筆記。

## 為什麼需要它

Telegram Bot API 不接受 `obsidian://` 連結：

- 訊息按鈕（inline keyboard）會被拒絕：`Unsupported URL protocol`
- 訊息文字裡的 `<a href="obsidian://…">` 會被拿掉
- 直接貼出的 `obsidian://…` 不會變成可以點的連結

所以改用一般的 `https://` 連結，點開這個網頁後，再由網頁跳轉到 Obsidian。

## 用法

```
https://<github-id>.github.io/obsidian-link/#vault=<vault 名稱>&file=<筆記路徑>
```

| 參數 | 說明 |
|---|---|
| `vault` | Obsidian vault 的名稱 |
| `file` | 筆記在 vault 裡的路徑，不含 `.md` |

兩個參數都要做 URL 編碼（空白、中文、`/` 都要編碼）。

**範例**：打開 vault `MyVault` 裡的 `Notes/Daily/2026-01-01`

```
https://menglin31.github.io/obsidian-link/#vault=MyVault&file=Notes%2FDaily%2F2026-01-01
```

### 在程式裡產生連結（Python）

```python
from urllib.parse import urlencode, quote

def obsidian_link(vault: str, file: str) -> str:
    return "https://menglin31.github.io/obsidian-link/#" + urlencode(
        {"vault": vault, "file": file}, quote_via=quote)
```

### 放進 Telegram 訊息按鈕

```python
reply_markup = {"inline_keyboard": [[
    {"text": "📖 打開筆記", "url": obsidian_link("MyVault", "Notes/Daily/2026-01-01")}
]]}
```

## 點擊後會發生什麼（iPhone）

1. Telegram 用內建瀏覽器打開這個網頁
2. 網頁自動跳轉，iOS 跳出「要在 Obsidian 中打開嗎？」
3. 按「打開」，Obsidian 打開指定的筆記

如果沒有自動跳轉，網頁上有「打開 Obsidian」按鈕可以手動點。

## 隱私與安全

- **筆記路徑不會送到伺服器。** 參數放在網址的 `#` 後面，瀏覽器不會把 `#` 之後的內容送給 GitHub，只在你的裝置上讀取。
- **網頁裡沒有任何個人資訊。** 不寫死 vault 名稱或資料夾，是一個通用工具。
- **只會跳轉到 Obsidian。** 程式只會產生 `obsidian://open` 連結，不能被拿來轉到其他網站。
- 不載入任何外部程式、字型或統計工具；設定了 `noindex`，不讓搜尋引擎收錄；設定 `no-referrer`，不把來源網址送給其他網站。

## 檔案

| 檔案 | 說明 |
|---|---|
| `index.html` | 整個轉址網頁（HTML + 一小段 JavaScript），沒有其他依賴 |

## 部署

使用 GitHub Pages：Settings → Pages → Source 選 `Deploy from a branch`，Branch 選 `main`、`/ (root)`。
