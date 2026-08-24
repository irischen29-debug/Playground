# CLAUDE.md — PlayGround (共用規範)

這個檔案適用於此目錄下的所有專案（Finance、CookBook 等）。每個子專案也有自己的 CLAUDE.md，記錄該專案專屬的細節 — 兩邊都要讀。

## 部署紀律

- 動手部署到正式環境前一定要先問我，就算這一步任務本身做得很乾淨也一樣。累積到一定量再部署，不要每做完一個小改動就部署。
- 部署一律用 Vercel CLI 手動執行（`vercel deploy --prod --yes` / `npx vercel deploy --prod --yes`），不是 push 就自動部署。

## Spec-driven development（SDD）

- 專案使用 `simple-sdd` skill：提案 → 實作 → 歸檔。
- 工作項目放在 `sdd/<短名稱>/`（`proposal.md` + `tasks.md`），驗收通過後歸檔到 `sdd/archive/<日期>-<短名稱>/`。
- 開新提案前，先檢查 `sdd/archive/`（以及目前還沒歸檔的 `sdd/` 資料夾）有沒有相關的既有工作，避免重複開新的。

## 測試習慣

- 驗證改動時，優先實際操作真實的 app（瀏覽器 / Playwright），不要只信任 build 或 type-check 通過。過去有幾個真實的 bug 只有實際操作才抓得到。對正式環境的後端測試完要記得清掉測試資料。

## 專案共同型態

這裡的專案大多是同一種形狀：React + Vite 前端、Supabase（Postgres + Auth + RLS）後端、部署在 Vercel，單一使用者的個人工具，email/password 登入，mobile-first PWA。
