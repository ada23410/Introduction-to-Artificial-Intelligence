### Content Engineering v.s. Prompt Engineering 差異
- 從系統輸入與輸出的角度來看，兩者並無本質上的區別。然而在應用層面上，**Prompt Engineering** 側重於「如何說話」以誘導模型；而 **Content Engineering** 則側重於「提供什麼內容」，透過結構化的知識輸入（如 RAG 架構或上下文 context），確保輸出結果的精準度與事實根據。這標誌著我們從單純的「下指令」轉變為對「高品質數據與資訊流」的治理。
- 在 GPT-3 時代，模型的推理能力與對人類意圖的對齊（Alignment）尚不成熟，因此極度依賴 **Prompt Engineering** 的技巧來克服模型的隨機性。當時，開發者必須精確掌握咒語般的關鍵字，才能「挖掘」出隱藏在模型權重中的正確答案。那是一個「技巧勝過內容」的過渡階段。。
- 過去廣為流傳的技巧，如「逐步思考（Chain of Thought）」、情緒勒索（例如：這對我的職涯很重要）甚至是給予虛擬小費，其核心邏輯是透過特定語句來激發模型更深層的推理路徑。
- 然而，隨著模型迭代（特別是 GPT-4o 或 o1 系列），底層邏輯已經過大規模的對齊訓練與強化學習，模型現在具備更強的自我優化能力。這類「心理暗示」的邊際效應已大幅遞減；與其花心思在語氣上的操作，不如專注於**結構化輸入**與**明確的邏輯框架**，這才是現代內容工程的核心。。
### User Prompt技巧
1. 提供前提 (Context Provision)
	- 為模型設定明確的任務邊界（Boundary）與背景資訊。這不只是告訴 AI「你是誰」，而是界定當前任務的時空背景、目標對象與成功標準，從而降低模型輸出的隨機性，使其更符合預期目標。
2. 少樣本學習與範例 (In-Context Learning)
	- task description
	- examples
	- prompt
	- 透過任務描述 + 多組範例（Few-shot）的結構，引導模型捕捉特定的運算邏輯或風格。
	- 以 Gemini 1.5 為例，透過載入稀有語言（如卡拉蒙語）的文法手冊與字典，模型能在未經重新訓練的情況下，僅憑上下文即具備翻譯能力。這證明了高品質的「例句」與「對照組」是提升模型產出正確率的最有效手段。
3. System prompt
	- **系統指令集（System Instructions）**：這是定義模型「人格核心」與「運行規則」的頂層設計，通常包含以下構面：
		- **角色定義與價值觀**：確立身份定位、專業度及背後遵循的哲學原則。
		- **行為準則與邊界**：明確的操作限制、安全性禁忌（Safety Guardrails）與錯誤處理機制。
		- **輸出規格控制**：規定回應風格、格式（如 JSON）、事實性檢查基準（如 Time-cutoff）以及互動反饋的語氣。
	- 基本身份與產品資訊
	- 使用說明與限制
	- 互動態度與使用者回饋
	- 安全與禁止風險
	- 回應風格與模式
	- 知識與事實性（e.g.Jan 2025）
	- 自我定位與哲學原則
	- 錯誤處理與互動細節
4. Dialogue History(短期記憶)
	- **短期對話紀錄 (Dialogue History)**：雖然單純的對話無法改變模型參數，但透過上下文緩衝區（Context Window），模型能維持短期的邏輯連貫性。
	- **長期記憶 (Long-term Memory)**：2024 年後，先進模型（如 ChatGPT）引入了跨對話的記憶功能。系統能主動提取使用者偏好或過去事實進行持久化儲存，實現「個人化」的持續迭代，而不僅僅是單次任務的互動。
5. 其他相關資料
	- **外部知識整合 (RAG, Retrieval-Augmented Generation)**： 透過動態接入搜尋引擎或企業私有資料庫，補足模型知識庫的即時性與專業深度。
	- **關鍵警語**：RAG並非萬靈丹（Silver Bullet）。系統的表現上限取決於「檢索內容的品質」。若資料來源不精準或格式混亂，反而會導致模型產生更嚴重的幻覺（Hallucination）。因此，**資料清洗與知識治理**才是 RAG 成功的核心要素。

### 語言模型使用工具
1. **定義工具溝通協議**：在上下文（Context）中建立明確的指令解析框架。透過預定義的標記語言（如 XML 標籤 `<tool></tool>` 或 JSON Schema），讓模型理解何時應停止生成文本，轉而發出工具呼叫（Tool Call）訊號。這種結構化描述能有效降低模型在理解工具用途與參數輸入時的錯誤率。
2. **精準參數化定義**：明確定義特定工具的操作介面（API Interface）。例如，定義一個獲取溫度的函數 `get_weather(location, time)`，必須在系統指令中詳述參數的資料型態與格式需求（如：location 需為城市名稱，time 需符合 ISO 8601 格式）。精準的工具描述（Description Engineering）是確保模型能根據使用者意圖，正確填入參數並觸發外部動作的關鍵。

### Tool Use
- **遵循多樣化的通訊規格**：不同模型對消息格式（Message Format）的要求各異（如 OpenAI 的 ChatML 與 Anthropic 的規格）。在開發與對接前，建議至 **Hugging Face** 查閱該模型的 **Chat Template** 與專屬控制標記（Control Tokens）。使用錯誤的格式會嚴重損害模型的推理品質與指令遵循能力。
- Computer use:
	- **代理人操作模式（Agentic Interaction）**：當前領先模型如 **Claude (Computer Use)** 與 **ChatGPT (Advanced Voice/Agent)** 已進入代理人時代。模型不再僅能生成文字，更能直接觀察螢幕（視覺輸入）、規劃行動路徑（Reasoning），並操作滑鼠與鍵盤來完成複雜跨軟體任務。
	- **高度整合的情境框架（Context Orchestration）**：一個成熟的代理人系統需平衡多維度的資訊輸入，建議採取以下結構化層次：
		- **指令層 (Instructions)**：包含 User Prompt（任務具體要求與範例）與 System Prompt（角色定位、行為準則）。
		- **記憶層 (Memory)**：動態載入對話歷史（短期）與使用者偏好儲存（長期）。
		- **知識層 (External Assets)**：透過 RAG 檢索的外部即時資訊。
		- **執行層 (Execution)**：包含工具定義（Tool Definitions）與思維鏈路（Reasoning Trace）。
    

> [!IMPORTANT] **避免情境過載 (Avoid Context Stuffing)**：並非把所有資訊塞入 Context 就能提升效果。過多的無關雜訊會導致「注意力稀釋（Attention Dilution）」。應透過**動態檢索**與**摘要技術**，確保模型始終只處理最高信噪比（High Signal-to-Noise Ratio）的內容。
	- context：
		- User prompt(include examples)
		- System prompt(persona, behaviors, etc. )
		- Dialogue history
		- Memory
		- Relevant information from other sources
		- Too use
		- Reasoning
	- 避免塞爆content engineering。

### AI Agent
**代理人運作循環**：一個自主代理人（AI Agent）的行為模式建立在與環境的持續互動上，其核心循環包含：
- **感知（Observation）**：觀察並獲取環境狀態與輸入資訊。
- **目標設定（Goal Setting）**：釐清任務終點與成功判別標準。
- **行動（Action）**：根據推理結果執行特定操作（如呼叫 API）。
- **環境（Environment）**：執行動作後產生的反饋與狀態轉變，進而觸發下一次感知。
- 免費試用工具AI Agent：Gemini CLI（有風險）。
- 儘管現代模型（如 Gemini 1.5 Pro）擁有極大的 Context Window（情境窗口），但**「可輸入」不等於「可理解」**。實驗證明，當輸入資訊量大幅增加時，模型在未達到物理上限前，其注意力的精準度就會開始下降，導致產出結果的連貫性與邏輯度受損。
- **資訊密度的平衡**：在多數情況下，豐富的背景資料能優化回覆品質。然而，一旦超過特定臨界點，**「雜訊」會開始掩蓋「訊號」**，導致模型在龐大的資訊流中產生混亂，最終使輸出品質呈現倒 U 型曲線的衰退。
- Lost in the Middle：當關鍵資訊被置於長文本的**中間段落**時，模型的檢索表現會顯著下降（模型往往對開頭與結尾的印象最深）。
- **情境退化現象（Context Rot）**：隨著對話輪次增加與上下文長度累積，模型的推理能力會發生系統性的退化。這不僅是注意力發散的問題，還包含模型對先前指令的權重減弱、幻覺比例提高以及邏輯一致性的崩塌。

### How to use Content Engineering
1. 檢索增強生成與動態篩選 (Advanced RAG)
	- **多維度的精準檢索機制**：RAG 不應只是單純的資料掛載，而是一場精密的篩選過程：
	- **內容篩選與重排序（Reranking）**：從語義向量庫提取初步結果後，透過 Reranking 模型針對任務相關性進行二次評分，確保塞入 Context 的是「含金量」最高的事實片段。
	- **工具版本 RAG**：當工具箱過於龐大時，應針對「工具描述文件」進行檢索，只將當前任務所需的 API 說明放入 Context，避免模型因工具過多而混亂。
	- **動態記憶流（Memory Stream）**：參考「史丹佛小鎮（Generative Agents, 2023）」的架構，不將全量記憶塞入上下文，而是建立**記憶流**。透過**時間衰減（Recency）**、**重要性（Importance/Reflection）**、**語義相關性（Relevance）**三大指標計算權重，僅檢索「最相關且最重要」的記憶片段。
2. 回饋與範例策略 (StreamBench & Examples)
	- 根據 StreamBench 等研究指出，語言模型對負面限制（不要做什麼）的遵循率相對較低，但對成功路徑（做對了什麼）的模仿能力極強。在 Content Engineering 中，與其列舉錯誤案例，不如提供高品質的**成功範例（Golden Examples）**，這能最顯著地提升模型的輸出正確率。
3. 記憶壓縮與遞迴處理 (Context Compression) - 
	- 為了應對 Context Rot，必須實施系統化的記憶治理：
		- **關鍵片段抽取**：當對話達到特定閾值（如 100 輪）時，觸發**遞迴總結（Recursive Summarization）**，僅保留核心事實與決策點，捨棄冗長過程。
		- **雙層記憶架構**：效仿 ChatGPT 的運作模式，區分顯性對話歷史（User 可見）與隱性語境權重（後台持久化存儲）。透過遞迴技術不斷更新「使用者特徵與背景知識」，實現長期互動下的低負載、高精準度。
4. 多代理人協作架構 (Multi-Agent Systems) 
	- **多代理人分工與整合（Multi-Agent Orchestration）**：面對超大規模任務（如撰寫跨領域綜述論文），單一 Agent 往往會因資訊量過大而導致邏輯崩塌。
	- **專才化與並行處理**：將任務拆解，讓多個 Agent 各司其職（例如：領域 A 專家、領域 B 專家、結構校對員、總結協調員）。
	- **由繁入簡的資訊流**：先由子代理人進行海量文獻的局部摘要，再由核心代理人針對摘要後的「精煉內容」進行高層次的論述撰寫。這解決了單一模型無法一次讀取上千萬篇論文的物理與邏輯限制。