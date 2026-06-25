# Retell + Twilio 語音客服機器人 (Voice AI Customer Service)

這個專案展示了如何結合 **Retell AI** (語音對話模型) 與 **Twilio** (雲端通訊平台)，建立一個全自動的電話語音客服系統。
本範例以「宮廟解籤服務」為情境，帶領您完成從 AI Agent 設定、專有名詞知識庫建立，到 SIP Trunking 串接的完整流程。

---

## 🌟 功能特色 (Features)

*   **自訂對話流程 (Voice Conversation Flow)：** 依據受訪者意願引導不同對話分支 (例如：詢問宮廟名稱、籤詩名稱、使用語系等)。
*   **強化語音辨識 (Knowledge Base)：** 匯入專有名詞辭典，讓 STT (語音轉文字) 能精準辨識如「艋舺龍山寺」、「六十甲子籤」等特殊詞彙。
*   **通話後資料萃取 (Post-call Data Extraction)：** 通話結束後，自動由 AI (GPT-5.4-Nano) 萃取出受訪者姓名、電話、宮廟名稱等結構化資料。
*   **SIP 串接：** 使用 Twilio Elastic SIP Trunking 與 Retell 橋接，實現真實電話撥打。

---

## 🛠️ 環境與前置作業 (Prerequisites)

1.  準備一個 [Retell AI](https://www.retellai.com/) 帳號。
2.  準備一個 [Twilio](https://www.twilio.com/) 帳號 (可使用 Free Trial)。
3.  本專案內附的 `AI_Agent` 檔案與 `KB` (Knowledge Base) 檔案。

---

## 🚀 部署教學 (Step-by-Step Guide)

### 第一階段：Retell AI 環境設定與檔案匯入

1. **註冊/登入帳號：** 前往 Retell 官網完成帳號註冊與登入。
2. **匯入知識庫 (Knowledge Base)：**
   * 進入左側選單的 `Knowledge Base`。
   * 點擊新建/匯入，將本專案提供的 **`KB`** 檔案上傳。
   * *💡 說明：此步驟是為了讓語音辨識 (STT) 能準確聽懂宮廟解籤相關的專有名詞。*
3. **匯入 AI 機器人 (AI Agent)：**
   * 進入左側選單的 `Agents`。
   * 點擊新建/匯入，將本專案提供的 **`AI_Agent`** 檔案匯入。
4. **Agent 細部設定確認：**
   * **Voice & Language:** 設定為 `Chinese, May (Minmax)`。
   * **Global Prompt:** 選擇 `Gpt-5-Nano` (挑選免費/便宜的模型即可)。
   * **Knowledge Base:** 將剛剛建立好的 KB 關聯進這個 Agent 裡。
   * **Post-call Data Extraction:** 確保已設定好要萃取的欄位 (宮廟名稱、籤詩名稱、語系、受訪者姓名、電話號碼)，模型選擇 `Gpt-5.4-Nano`。
   * **發布：** 設定完成後，請務必點擊 **Publish** 發布此 Agent。

### 第二階段：Twilio SIP Trunk 設定

1. **申請電話號碼：** 登入 Twilio，開始一個 Free Trial number。
2. **建立 SIP Trunk：**
   * 在上方搜尋欄輸入 `SIP`，找到 **Elastic SIP Trunks**。
   * 點擊 `Create new SIP Trunk`。
3. **General 設定：**
   * `Friendly name`: 隨便命名。
   * `Call Transfer`: 勾選 Enabled。
   * `Enable PSTN Transfer`: 勾選 Enabled。
   * 按下 Save。
4. **Termination 設定：**
   * `Termination SIP URI`: 自行設定一個好記的名稱。
   * `Authentication` -> `Credential Lists`: 新增一組帳號密碼 (**⚠️請務必將此帳號密碼抄下來**)。
   * 按下 Save。
5. **Origination 設定：**
   * 新增 Origination URIs (點擊加號)。
   * `Origination SIP URI` 欄位填入：`sip:sip.retellai.com`。
   * 按下 Add 並 Save。
6. **Number 設定：**
   * 右上角點擊 `Add a number` (Add an Existing Number)。
   * 系統會自動帶出最初申請的免費電話，點選 `Add Selected`。

### 第三階段：串接 Retell 與 Twilio

1. 回到 Retell 平台，點擊左側選單 `Deploy` > `Phone number`。
2. `Outbound Call Agent` > `Call Agent`：選擇第一階段發布好的機器人。
3. 點擊中間的加號，選擇 **Connect to your number via SIP trunking**。
4. 填寫以下資訊：
   * **Phone number:** 填寫 Twilio 拿到的電話號碼 (記得包含 `+` 號與國碼)。
   * **Termination URI:** 填寫第二階段自訂的 URI，後面必須加上 `.pstn.twilio.com`。
   * **SIP Trunk User Name / Password:** 填入第二階段 Termination 中設定的帳號與密碼。
5. 按下 Save 完成串接。

---

## 📞 測試 Outbound 撥打 (Testing)

1. 在 Retell 的 Phone Number 頁面，點擊右側欄最上方的 **Make an outbound call**。
2. *(初次使用)* 系統可能會跳出需要進行證件/身分驗證。
3. 驗證完成後，輸入自己的手機號碼 (例如台灣號碼 `+8869xxxxxxxx`) 進行測試。
4. 按下 Call。

---

## 📊 通話分析與資料萃取 (Analytics)

通話結束後，您可以至 Retell 的 **Call History** 中查看：
* 完整的通話錄音 (Audio)。
* 完整的逐字稿對話 (Transcription)。
* 模型成功萃取出的結構化資料 (Data) (如：受訪者、宮廟名、籤詩名等)。

---

## ⚠️ 已知問題與限制 (Limitations)

在測試撥出 (Outbound) 時，如果遇到以下錯誤：
* `SIP status code: 403 SIP error category: telephony_provider_permission_denied`
* `Forbidden`

**原因：** Twilio 的免費測試帳號 (Free Trial) 有權限限制，通常無法直接進行未經許可的 Outbound 撥打。
**結論：** 雖然 Twilio 試用帳號限制了撥出功能，但 Retell AI 的電話機器人對話流程與資料萃取功能，在測試環境 (Playground) 中已證實可順利運作並完成大部分預期功能。
