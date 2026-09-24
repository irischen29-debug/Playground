# CLAUDE.md — PlayGround (共用規範)

這個檔案適用於此目錄下的所有專案（Finance、CookBook 等）。每個子專案也有自己的 CLAUDE.md，記錄該專案專屬的細節 — 兩邊都要讀。

## 部署紀律

- 動手部署到正式環境前一定要先問我，就算這一步任務本身做得很乾淨也一樣。累積到一定量再部署，不要每做完一個小改動就部署。
- 部署一律用 Vercel CLI 手動執行（`vercel deploy --prod --yes` / `npx vercel deploy --prod --yes`），不是 push 就自動部署。
- **部署失敗時先確認登入狀態，它的錯誤訊息會誤導。** CLI 的憑證會過期（npx 拉到新版本後也可能掉），但這時 deploy 回的是 `Not authorized` 或 `You do not have access to the specified account` — 看起來像權限或 scope 的問題，實際上只是登出了。用 `npx vercel whoami` 判斷，回 `Logged out` 就請使用者自己跑 `npx vercel login`（要在瀏覽器完成驗證，代跑不了，而且會寫入 PlayGround 以外的設定目錄）。不要花時間去猜 `--scope`。
- **但 `whoami` 還回得出帳號時，就不是登出，原樣重試一次。** 2026-09-13 遇過一次：第一次 `deploy` 直接回 `Not authorized`、連上傳進度條都沒出現，可是 `whoami` 正常，而且同一組憑證幾分鐘前才剛成功部署過；原封不動重試就過了。2026-09-14 又遇到一次，徵兆一模一樣、重試一樣就過；但同一天稍後的部署第一次就成功——所以這是**偶發**，不是「第一次一定失敗」的規律，不要為它養成每次先空跑一趟的習慣。結論：`Not authorized` 不等於登出，遇到就原樣重試，不要急著重新登入或動 scope。判斷依據是「有沒有出現上傳進度條」——失敗的那幾次連傳都還沒開始。2026-09-15 那次還多了一條線索：失敗前 `npx` 剛好拉了新版 CLI（`The following package was not found and will be installed: vercel@…`），重試就過。看到那行安裝訊息之後第一次 deploy 失敗，幾乎可以直接當成這個已知狀況處理，重試就好。
- **部署完要驗證正式站真的換版了**，不要只信 CLI 回 `"status": "ok"`。比對 `curl` 正式網址拿到的 `assets/index-*.js` / `.css` hash 跟本機 `npm run build` 的輸出是否一致；不一致就是沒生效。

## Spec-driven development（SDD）

- 專案使用 `simple-sdd` skill：提案 → 實作 → 歸檔。
- 工作項目放在 `sdd/<短名稱>/`（`proposal.md` + `tasks.md`），驗收通過後歸檔到 `sdd/archive/<日期>-<短名稱>/`。
- 開新提案前，先檢查 `sdd/archive/`（以及目前還沒歸檔的 `sdd/` 資料夾）有沒有相關的既有工作，避免重複開新的。

## 測試習慣

- 驗證改動時，優先實際操作真實的 app（瀏覽器 / Playwright），不要只信任 build 或 type-check 通過。過去有幾個真實的 bug 只有實際操作才抓得到。對正式環境的後端測試完要記得清掉測試資料。

## 專案共同型態

這裡的專案大多是同一種形狀：React + Vite 前端、Supabase（Postgres + Auth + RLS）後端、部署在 Vercel，單一使用者的個人工具，email/password 登入，mobile-first PWA。

## 協作習慣

這些原本記在某一台電腦的 Claude 記憶裡，換電腦會跟不過去，所以寫在這裡。

- **回覆一律用繁體中文**，包括進度回報和長篇總結。程式碼、指令、commit message 照原本慣例（commit message 用英文沒關係）。一長串工具呼叫之後最容易不自覺切成英文，送出前檢查第一句。
- **動到 PlayGround 以外的地方（寫入、修改、刪除）一律先警示、先問**，就算看起來無害也一樣；純讀取不受限。同一種動作問過一次、得到允許，之後就不用再問。
- **「驗收」和「歸檔」是兩件事。** 驗收 = 我接受了、任務打勾。歸檔 = 一次做完四步，不要一步一步問：整理文件（讓 sdd 紀錄跟實際做出來的一致，包括過程中改了但沒寫下的東西）→ commit → push → 把 `sdd/<名稱>/` 移到 `sdd/archive/<日期>-<名稱>/`。
- **問過、定案的事不要再問**，把決定帶著往下做。「啟動自動模式」= 剩下的任務一路做完、最後部署一次再回報，中間不要逐步確認。
- **同一類的小修改累積起來一起部署**，不要每改一行就 build + 部署 + 驗證一輪。
- **實測是我的步驟。** 這台（或任何一台）電腦上沒有 Supabase 的登入帳密，登入之後的畫面你測不到，build 和獨立的邏輯測試就是上限。流程是：部署 → 給我一份照順序點的清單（風險最高的放前面），說清楚哪些只過了型別檢查 → 我在 iPhone 上測。不要為了驗收開本機 dev server —— 手機才是真正的測試環境。我回「驗收」或「歸檔」就代表測過了。
- **給我的正式網址一律用短網域**（`npx vercel project ls` 的「Latest Production URL」），不要用 `…-projects.vercel.app` 這種團隊別名。Deployment Protection 保持開著 —— 關掉的話舊部署網址全部變公開，曾經讓我把舊版加到主畫面。
