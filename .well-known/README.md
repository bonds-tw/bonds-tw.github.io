# `.well-known/apple-app-site-association`

宣告哪一個 App 可以接管 `bonds-tw.github.io` 的 Universal Links。

## 值從哪裡來（別用猜的）

`appIDs` 的格式是 `<TeamID>.<BundleID>`：

| 部分 | 值 | 怎麼確認 |
| --- | --- | --- |
| Team ID | `538MCM44UX` | `security find-identity -v -p codesigning` → `Savori Inc. (538MCM44UX)`；描述檔 `TeamName` 亦為 Savori Inc. |
| Bundle ID | `tw.bonds.backupTW` | `backupTW.xcodeproj` 的 `PRODUCT_BUNDLE_IDENTIFIER` |

2026-08-09 之前這裡寫的是 `G4JB6X9K6T.org.denkeni.backupTW`——**上游作者的團隊與
Bundle ID**，不是 bonds-tw 的。那是 fork 時一起帶過來、沒人改的值。

## ⚠️ 兩件還沒解決的事

### 1. Content-Type 是 `application/octet-stream`，不是 `application/json`

Apple 的文件要求 AASA 以 `application/json` 提供。GitHub Pages 依副檔名決定
Content-Type，而這個檔案照慣例沒有副檔名，所以會是 `octet-stream`，
**而 GitHub Pages 不提供設定 header 的方式**。

實務上 Apple 的 AASA CDN 常常仍能取用，但這不是可以依賴的行為。真的要
Universal Links 上線，比較穩的做法是把這個網域改由能設 header 的地方提供
（例如 Cloudflare Workers，`mashbean.net` 就是那樣跑的），或改用自訂網域並
在前面放一層 CDN。

驗證方式：

```sh
curl -sI https://bonds-tw.github.io/.well-known/apple-app-site-association | grep -i content-type
```

### 2. App 端目前沒有 Associated Domains entitlement

它先前為了繞過 PPQCheck 被移除了（個人團隊不支援該能力）。現在簽章團隊已是
組織帳號 Savori Inc.，可以加回來——**在加回去之前，這個檔案再正確也不會生效**。
