在串接 Google 相關服務（如 Google Drive、Sheets、Gmail、Calendar、Maps API、YouTube Data API 等）時，「**Credential（憑證）設定**」幾乎是所有開發者都會遇到的第一道門檻。許多初學者在設定時常出現困惑，例如：

- OAuth 2.0、API Key、Service Account 有什麼差別？
    
- 為什麼 Token 會過期？
    
- 為什麼 API 回傳 403 / 401？
    
- 什麼情境該用哪一種 Credential？
    

本文將從**概念理解 → 類型比較 → 設定流程 → 實務情境 → 常見錯誤**，一步步說明 Google Credential 的設定與使用邏輯，協助你建立清楚、可維護的 Google API 串接策略。

---

## 一、什麼是 Google Credential？

**Google Credential** 是 Google 用來驗證「你是誰」以及「你可以做什麼」的一組身分識別與授權機制。

簡單來說，它解決了三個核心問題：

1. **你是誰（Authentication）**  
    → 驗證請求來源是否可信
    
2. **你能做什麼（Authorization）**  
    → 限制可存取的 API 與資料範圍（Scope）
    
3. **誰在負責（Accountability）**  
    → 每一次 API 呼叫都能被追蹤、控管、計費與撤銷
    

在 Google 生態系中，所有 API 呼叫幾乎都必須透過 Credential 才能進行。

---

## 二、Google Credential 的三大類型

Google Cloud Platform（GCP）中最常見的 Credential 類型有三種：

### 1️⃣ API Key（最簡單、限制最多）

**API Key** 是一組字串，用來標示「是哪個專案在呼叫 API」。

**特性**

- 不涉及使用者身分
- 不需要登入流程
- 設定最簡單
- 安全性最低（容易被盜用）

**適合情境**

- 公開資料
- 不涉及個人帳戶資料
- 只需要「識別專案」即可的 API

**常見用途**

- Google Maps JavaScript API
- Places API
- Geocoding API

⚠️ 不適合用於存取 Google Drive、Gmail、Calendar 等「使用者私有資料」。

---

### 2️⃣ OAuth 2.0 Client（最常見、彈性最高）

**OAuth 2.0** 是目前 Google API 最主流的授權方式，適用於「**需要存取使用者資料**」的情境。

**特性**

- 需要使用者登入 Google 帳號
- 使用者可明確同意授權範圍（Scopes）
- 可撤銷、可更新
- 安全性高、流程較複雜

**適合情境**

- Web App / Mobile App
- SaaS 服務
- 自動化工具（如 n8n、Zapier）
- 任何「替使用者操作 Google 服務」的應用

**常見用途**

- Google Drive API
- Google Sheets API
- Gmail API
- Calendar API

OAuth 2.0 的核心概念是：

> **使用者不是把帳密給你，而是給你「有限時間 + 有範圍的存取權」。**

---

### 3️⃣ Service Account（機器對機器）

**Service Account** 是「沒有真人使用者」的 Google 帳號，專門給系統或後端服務使用。

**特性**

- 不需要使用者互動
- 使用 JSON Key 檔案
- 適合後端自動化
- 權限由管理者直接指定

**適合情境**

- 後端服務
- Server-to-server
- CI/CD、自動報表、資料同步
- Google Cloud 內部服務整合

**常見用途**

- BigQuery
- Cloud Storage
- 後端批次處理
- 企業內部系統

⚠️ 若要存取「個人 Google Drive」，仍需額外設定檔案共享權限。

---

## 三、OAuth 2.0 的核心流程概念

OAuth 2.0 雖然流程較複雜，但可以拆解為四個關鍵步驟：

1. **使用者登入 Google**
2. **顯示授權畫面（Scopes）**
3. **取得 Authorization Code**
4. **交換 Access Token / Refresh Token**

其中：

- **Access Token**：短效（通常 1 小時）
- **Refresh Token**：用來換新 Access Token

這樣的設計能確保：

- Token 洩漏時影響有限
- 使用者可隨時撤銷授權
- 系統能長期穩定運作

---

## 四、Google Cloud Console 設定流程（以 OAuth 為例）

### Step 1：建立或選擇專案

- 進入 Google Cloud Console
- 建立一個專案（Project）
### Step 2：啟用 API

- 到「API 與服務 → 程式庫」
- 啟用需要的 API（如 Drive API、Sheets API）

### Step 3：設定 OAuth 同意畫面

- 設定應用名稱
- 設定使用者類型（Internal / External）
- 填寫必要資訊
- 設定 Scopes

⚠️ 若是 External App，需注意審核問題。

### Step 4：建立 Credential

- 類型選擇「OAuth client ID」
- 選擇應用類型（Web / Desktop / Mobile）
- 設定 Redirect URI

### Step 5：取得 Client ID / Client Secret

- 用於程式或自動化工具中

---

## 五、實務中常見錯誤與原因

### ❌ 401 Unauthorized

- Access Token 過期
- Token 未正確帶入 Header

### ❌ 403 Forbidden
- Scope 不足
- API 尚未啟用
- 沒有資源存取權限（例如 Drive 檔案未共享）

### ❌ redirect_uri_mismatch
- Redirect URI 與 Console 設定不一致

### ❌ invalid_grant
- Refresh Token 被撤銷
- Token 與 Client 不匹配
---

## 六、實務建議與最佳做法

1. **依情境選擇 Credential**
    - 公開資料 → API Key
    - 使用者資料 → OAuth 2.0
    - 後端系統 → Service Account
2. **最小權限原則（Least Privilege）**
    - Scope 能少就少
    - 避免使用全權限
3. **Credential 不寫死在前端**
    - 使用環境變數
    - 避免洩漏
4. **定期檢查與輪替**
    - 移除未使用的 Credential
    - 監控 API 使用量

---

## 七、結語

Google Credential 的設定，表面看起來只是「拿一組金鑰」，但實際上牽涉的是**身分驗證、授權、安全性與系統架構設計**。

當你能清楚理解：

- 不同 Credential 的角色
- OAuth 2.0 的授權邏輯
- Token 的生命週期
- 權限與責任的劃分
你就不只是「把 API 串起來」，而是能設計一套**安全、可擴充、可維護的 Google 整合架構**。