# QR 收藏冊 v2

一個可部署到 GitHub Pages 的靜態網站原型。支援：
- 新增 QR 內容／網址，並自動產生 QR 圖
- 使用相機掃描 QR Code
- 從相簿圖片辨識 QR Code
- 修改名稱、標記已使用／未使用、刪除、搜尋及篩選
- JSON 匯出／匯入
- Firebase Authentication（Google 登入）＋ Firestore 雲端同步（需自行設定 Firebase）

## A. 先直接測試本機功能
1. 解壓縮此資料夾。
2. 用瀏覽器開啟 `index.html`。
3. 新增網址測試 QR 圖、名稱編輯、使用狀態和刪除。
4. 相機通常需要 HTTPS 或 localhost；直接以 `file://` 開啟時，相機可能無法使用。
5. 本機模式資料存在目前瀏覽器的 localStorage。

## B. 設定 Firebase 雲端同步（免費方案通常足夠個人測試）
1. 前往 https://console.firebase.google.com/ ，建立 Firebase project。
2. 在 Project settings → General → Your apps 新增 Web app，複製 Firebase config。
3. 編輯 `index.html`，找到 `const firebaseConfig = { ... }`，將 `PASTE_API_KEY`、`PASTE_PROJECT_ID`、`PASTE_APP_ID` 等替換成 Firebase 提供的真實值。
4. Firebase Console → Authentication → Sign-in method，啟用 **Google** 登入。
5. Authentication → Settings → Authorized domains，加入部署網域，例如 `你的帳號.github.io`。本機測試可加入 `localhost`。
6. Firestore Database → Create database，建立資料庫。
7. Firestore → Rules，貼上以下規則後發布：

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /users/{userId}/items/{itemId} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

此規則限制每個登入使用者只能讀寫自己的 `/users/{uid}/items/` 路徑。不要把資料庫設成所有人皆可讀寫。

## C. 部署成公開網址（GitHub Pages）
1. 登入 GitHub，建立一個新的 **Public repository**，例如 `qr-collection`。
2. 上傳 `index.html` 到 repository 根目錄（README 可一起上傳）。
3. 進入 repository → Settings → Pages。
4. 在 Build and deployment 選 `Deploy from a branch`，Branch 選 `main`，資料夾選 `/ (root)`，按 Save。
5. 等待部署完成，網址通常是 `https://你的GitHub帳號.github.io/qr-collection/`。
6. 把這個完整網域加進 Firebase Authentication 的 Authorized domains。
7. 用部署網址開啟，點「登入同步」並使用 Google 登入。

## 注意事項
- GitHub Pages 是公開網站；任何人都能開啟網站，但 Firebase 資料依 Google 登入帳號分隔，並由 Firestore Rules 保護。
- Firebase web config 中的 apiKey 不是資料庫存取安全邊界；真正的存取控制必須靠 Authentication 和 Firestore Rules。
- 目前這版尚未將本機既有收藏自動合併至雲端。登入前請先匯出備份；第一次啟用雲端同步時，雲端清單會以 Firestore 資料為準。
- 若你希望同一組 QR 收藏由多個 Google 帳號共同管理，需要另外設計共享收藏功能。
- 網頁使用 Firebase CDN、QRCode.js 與 jsQR CDN，因此第一次載入及掃描需網路連線。
# qrcode
qrcode
