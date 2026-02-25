# 課程報名與現場報到掃描系統

一個基於 **Google Apps Script + Google Sheets** 的輕量級活動報名與 QR Code 掃描報到系統。  
適合中小型課程、講座、工作坊、訓練營等活動使用，零伺服器成本、易於維護。

## 功能特色

- 學員線上報名（姓名、性別、身分證後4碼、手機、Email、選擇課程）
- 報名後自動寄送確認信，審核通過後可批次寄送 QR Code 報到憑證
- QR Code 內容為唯一8碼數字（隨機4碼 + 身分證後4碼），防偽且不重複
- 現場使用手機掃描 QR Code 即時報到，狀態更新為「已報到」
- 報到成功自動記錄詳細資訊至「報到紀錄」分頁（含秒級時間）
- 支援多場次/多課程（每個課程對應一個8位日期欄位）
- 管理員可透過 Apps Script 手動批次發送 QR Code email

## 系統架構

- **前端**：兩個靜態 HTML 頁面（register.html + scan.html）
- **後端**：Google Apps Script（Web App 形式）
- **資料庫**：Google Sheets（三個分頁：記錄、課程清單、報到紀錄）
- **QR Code 生成**：使用 QuickChart API（免費、可靠）

## 檔案說明

| 檔案              | 說明                          | 使用者角色     |
|-------------------|-------------------------------|----------------|
| register.html     | 學員報名頁面                  | 一般學員       |
| scan.html         | 現場報到掃描頁面              | 管理員/工作人員 |
| README.md         | 本說明文件                    | 所有人         |

## 部署步驟

### 步驟 1：建立 Google Sheet

1. 新建一個 Google 試算表，取得 Spreadsheet ID（網址中 `/d/` 後面的那一串）
2. 建立三個分頁，並設定標題行（第一行）：

   **分頁 1：記錄**
   - A1: 姓名
   - B1: 性別
   - C1: 身分證後4碼
   - D1: 手機
   - E1: 電子郵件
   - F1: 唯一碼
   - G1: 已發送email
   - H1: 報名時間
   - I1 以後：課程日期欄（例如 20260225、20260301）

   **分頁 2：課程清單**
   - A1: 課程名稱
   - B1: 課程內容
   - C1: 日期（8碼，例如 20260225）
   - A2~：填入課程資料（至少 1 筆）

   **分頁 3：報到紀錄**（可先空白，程式會自動建立標題）
   - A1: 姓名
   - B1: 性別
   - C1: 身分證後4碼
   - D1: 唯一碼
   - E1: 課程名稱
   - F1: 報到時間

### 步驟 2：建立並部署 Google Apps Script

1. 開啟 Google Sheet → 擴充功能 → Apps Script
2. 全部刪除預設程式碼，貼上專案中的 Apps Script 程式碼
3. 把 `SPREADSHEET_ID` 改成你的試算表 ID
4. 儲存專案（取個名字，例如「課程報名系統」）
5. 點擊左側「部署」→「新增部署」
   - 類型：Web 應用程式
   - 執行身分：Me（你的帳號）
   - 誰有存取權：任何人（Anyone）
6. 部署後複製「Web 應用程式 URL」，格式類似：
https://script.google.com/macros/s/AKfycbz5XCUsdSbhtmK46_d6i6rUsEZssaF5y82XOCIzm2e7tvTJ0HEZkUDmRADf8prFd4YTug/exec


### 步驟 3：修改 HTML 檔案

1. 打開 `register.html` 和 `scan.html`
2. 找到這一行：
```javascript
const GAS_URL = "https://script.google.com/macros/s/AKfycbz5XCUsdSbhtmK46_d6i6rUsEZssaF5y82XOCIzm2e7tvTJ0HEZkUDmRADf8prFd4YTug/exec";