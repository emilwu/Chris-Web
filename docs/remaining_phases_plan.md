# Hwayo 檢驗流程線上化系統 - 剩餘階段開發計劃

本文檔旨在規劃 Hwayo 檢驗流程線上化系統專案，依照 [`planning/project_development_dod_guide.md`](planning/project_development_dod_guide.md) 框架，完成階段3至階段7的子任務。所有任務執行時需參考專案簡報 [`planning/productBrief.md`](planning/productBrief.md)。

## 總體說明
- **專案目標：** 根據 [`planning/productBrief.md`](planning/productBrief.md) 實現檢驗流程線上化。
- **方法論：** 遵循 [`planning/project_development_dod_guide.md`](planning/project_development_dod_guide.md) 的階段和 DoD 標準。
- **協調模式：** Orchestrator 模式負責協調，Architect 模式負責文件撰寫。

---

## 階段 3：模組依賴、使用者流程與架構決策

**目標：**
- 清晰定義 MVP 階段核心模組之間的依賴關係和交互方式。
- 詳細定義 MVP 核心功能對應的關鍵使用者流程和系統活動。
- 確立關鍵的架構設計原則和技術選型決策，並記錄其理由。

**子任務列表：**

1.  **子任務 3.1：進行模組交互分析與初步接口定義**
    *   **行動：** 分析各核心模組之間為完成 MVP 功能所必需的數據流和控制流。初步定義關鍵的模組間 API 接口（請求、響應、協議）。
    *   **依賴項：** 階段2產出的各模組 MDD 文件。
    *   **產出物：** [`docs/architecture/module_interaction_analysis.md`](docs/architecture/module_interaction_analysis.md)
    *   **狀態：** 已完成 (2025/5/30 下午11:49:10)
    *   **委派模式：** Architect
    *   **註：** Architect 模式已完成模組交互分析與初步接口定義。

2.  **子任務 3.2：繪製模組依賴圖與系統架構圖**
    *   **行動：** 使用圖表（如 Mermaid `graph` 或 `C4 Model` 的上下文圖/容器圖）清晰展示核心模組之間的依賴關係及整體系統架構。
    *   **依賴項：** 子任務 3.1 的產出 ([`docs/architecture/module_interaction_analysis.md`](docs/architecture/module_interaction_analysis.md))。
    *   **產出物：** [`docs/architecture/system_architecture.md`](docs/architecture/system_architecture.md) (包含高層次的系統架構圖、模組依賴關係圖)。
    *   **狀態：** 已完成 (2025/5/30 下午11:52:08)
    *   **委派模式：** Architect
    *   **註：** Architect 模式已完成系統架構圖的繪製。

3.  **子任務 3.3：細化核心使用者流程**
    *   **行動：** 針對 MVP 的核心用戶故事，設計詳細的使用者流程圖 (User Flow Diagram) 或活動圖 (Activity Diagram)，明確各步驟的操作者和系統響應，覆蓋正常路徑、主要異常路徑和邊界條件。
    *   **依賴項：** [`docs/mvp_definition.md`](docs/mvp_definition.md) 中的核心用戶故事。
    *   **產出物：** [`docs/user_flows/core_user_flows.md`](docs/user_flows/core_user_flows.md) (包含使用者流程圖或活動圖)。
    *   **狀態：** 已完成 (2025/5/30 下午11:58:40)
    *   **委派模式：** Architect
    *   **註：** Architect 模式已完成核心使用者流程的細化。

4.  **子任務 3.4：確立並記錄關鍵架構決策**
    *   **行動：** 討論並確定關鍵的技術選型（如主要編程語言、框架、數據庫類型）、核心的架構模式。記錄這些決策及其背後的理由、優缺點分析和潛在風險。
    *   **依賴項：** [`docs/mvp/mvp_service_framework.md`](docs/mvp/mvp_service_framework.md), [`docs/architecture/system_architecture.md`](docs/architecture/system_architecture.md)。
    *   **產出物：** 更新 [`docs/architecture/system_architecture.md`](docs/architecture/system_architecture.md) (記錄重要的架構決策和設計原則)。在 [`memory-bank/decisionLog.md`](memory-bank/decisionLog.md) 中記錄關鍵架構決策。
    *   **狀態：** 已完成 (2025/5/31 上午1:27:00)
    *   **委派模式：** Architect
    *   **註：** Architect 模式已完成關鍵架構決策的確立與記錄。相關決策已更新至 [`docs/architecture/system_architecture.md`](docs/architecture/system_architecture.md) 並記錄於 [`memory-bank/decisionLog.md`](memory-bank/decisionLog.md)。

5.  **子任務 3.5：更新模組 MDD 文件**
    *   **行動：** 根據本階段的分析，更新各模組 MDD (`docs/mod/[module_name]_mdd.md`) 中的接口定義和依賴關係描述。
    *   **依賴項：** 子任務 3.1 ([`docs/architecture/module_interaction_analysis.md`](docs/architecture/module_interaction_analysis.md)), 3.2 ([`docs/architecture/system_architecture.md`](docs/architecture/system_architecture.md)), 3.4 (關鍵架構決策)。
    *   **產出物：** 更新後的各模組 MDD 文件。
    *   **狀態：** 已完成 (2025/5/31 上午1:37:11)
    *   **委派模式：** Architect
    *   **註：** Architect 模式已完成所有核心模組 MDD 文件的更新。

6.  **子任務 3.6：更新開發所需文件清單**
    *   **行動：** 與使用者討論並確認本階段衍生的、下一開發階段所需的技術文件，並將其包含預期內容摘要/目的，記錄/更新至 [`docs/required_development_documents.md`](docs/required_development_documents.md)。
    *   **依賴項：** 本階段所有產出物。
    *   **產出物：** 更新後的 [`docs/required_development_documents.md`](docs/required_development_documents.md)。
    *   **狀態：** 已完成 (2025/5/31 上午1:41:31)
    *   **委派模式：** Architect
    *   **註：** Architect 模式已完成開發所需文件清單的更新。階段 3 所有子任務完成。

---

## 階段 4：數據模型 (SOT)、接口規格與界面原型

**目標：**
- 定義專案的整體數據模型和核心數據結構，並確立其為唯一真實來源 (SOT)。
- 詳細定義模組間及對外暴露的 API 接口規格。
- 為 MVP 的關鍵使用者界面創建更詳細的原型。

**子任務列表：**

1.  **子任務 4.1：設計詳細數據模型並確立 SOT**
    *   **行動：** 基於模組功能需求和使用者流程，設計數據庫的詳細實體、屬性、關係及索引策略。使用 ERD 和/或 Class Diagram 清晰展示。明確指定 [`docs/master_data_model.md`](docs/master_data_model.md) 為數據模型 SOT。
    *   **依賴項：** 階段3產出的使用者流程 ([`docs/user_flows/core_user_flows.md`](docs/user_flows/core_user_flows.md)) 和更新後的模組 MDD (位於 `docs/mod/` 目錄下)。
    *   **產出物：** [`docs/master_data_model.md`](docs/master_data_model.md) (包含數據字典、ERD/Class Diagram、DB Schema 定義)。
    *   **狀態：** 已完成 (2025/5/31 上午1:48:03)
    *   **委派模式：** Architect
    *   **註：** Architect 模式已完成詳細數據模型設計並確立 SOT。

2.  **子任務 4.2：定義 API 接口規格並確立 SOT**
    *   **行動：** 為所有核心模組間交互的內部 API 以及需要對外暴露的公開 API，定義詳細的接口規格 (JSON、HTTP 方法、URL、參數、響應、錯誤碼、認證)。使用 OpenAPI (Swagger) 規範編寫 API 文檔，並確立 `docs/api_specification.md` 為 API 規格 SOT。
    *   **依賴項：** 階段3產出的模組交互分析 ([`docs/architecture/module_interaction_analysis.md`](docs/architecture/module_interaction_analysis.md)) 和初步接口定義，以及子任務 4.1 產出的數據模型 SOT ([`docs/master_data_model.md`](docs/master_data_model.md))。
    *   **產出物：** [`docs/api_specification.yaml`](docs/api_specification.yaml) 和 [`docs/api_specification.md`](docs/api_specification.md) (包含所有核心 API 的詳細規格)。
    *   **狀態：** 已完成 (2025/5/31 上午1:53:30)
    *   **委派模式：** Architect
    *   **註：** Architect 模式已完成 API 接口規格設計並確立 SOT。

3.  **子任務 4.3：細化界面原型並進行評審**
    *   **行動：** 針對 MVP 的核心使用者流程和關鍵界面，創建中高保真線框圖或交互原型。與使用者和團隊成員評審原型，收集反饋並迭代。
    *   **依賴項：** 階段3產出的核心使用者流程文件 ([`docs/user_flows/core_user_flows.md`](docs/user_flows/core_user_flows.md))。
    *   **產出物：** `docs/ui_prototypes/mvp_detailed_prototypes.md` (或連結到原型工具的共享鏈接)。
    *   **狀態：** 已完成 (2025/5/31 上午2:26:17)
    *   **委派模式：** Architect (或協調 UI/UX 設計師)
    *   **註：** Architect 模式已完成界面原型設計。

4.  **子任務 4.4：更新模組 MDD 文件**
    *   **行動：** 更新各模組 MDD (`docs/mod/[module_name]_mdd.md`) 中涉及數據模型和接口的部分，確保與 SOT 文件一致。
    *   **依賴項：** 子任務 4.1 ([`docs/master_data_model.md`](docs/master_data_model.md)), 4.2 ([`docs/api_specification.yaml`](docs/api_specification.yaml)) 的產出。
    *   **產出物：** 更新後的各模組 MDD 文件。
    *   **狀態：** 已完成 (2025/5/31 上午2:36:10)
    *   **委派模式：** Architect
    *   **註：** Architect 模式已完成所有核心模組 MDD 文件的更新，確保與 SOT 文件一致。

5.  **子任務 4.5：更新開發所需文件清單**
    *   **行動：** 與使用者討論並確認本階段衍生的、下一開發階段所需的技術文件，並將其包含預期內容摘要/目的，記錄/更新至 [`docs/required_development_documents.md`](docs/required_development_documents.md)。
    *   **依賴項：** 本階段所有產出物。
    *   **產出物：** 更新後的 [`docs/required_development_documents.md`](docs/required_development_documents.md)。
    *   **狀態：** 已完成 (2025/5/31 上午2:42:30)
    *   **委派模式：** Architect
    *   **註：** Architect 模式已完成開發所需文件清單的更新。階段 4 所有子任務完成。

---

## 階段 5：開發計劃、環境配置SOT與開發指南制定

**目標：**
- 制定詳細的開發順序、任務分解及注意事項。
- 創建一份綜合性的開發指示文件。
- 為各環境定義詳細的配置參數和管理策略，並確立環境配置的 SOT。

**子任務列表：**

1.  **子任務 5.1：規劃開發順序與迭代**
    *   **行動：** 綜合考慮模組依賴、業務優先級、技術風險和資源，確定 MVP 階段各核心模組和主要功能的詳細開發順序和迭代計劃 (Sprints)。
    *   **依賴項：** 階段3 ([`docs/architecture/system_architecture.md`](docs/architecture/system_architecture.md), [`docs/user_flows/core_user_flows.md`](docs/user_flows/core_user_flows.md)) 和階段4 ([`docs/master_data_model.md`](docs/master_data_model.md), [`docs/api_specification.yaml`](docs/api_specification.yaml), `docs/ui_prototypes/mvp_detailed_prototypes.md`) 的產出物。
    *   **產出物：** 開發順序和迭代計劃文檔 (可整合入開發指南或獨立文件)。
    *   **狀態：** 進行中
    *   **委派模式：** Architect (或協調專案經理)
    *   **註：** 已委派給 Architect 模式。

2.  **子任務 5.2：進行任務分解與初步估時**
    *   **行動：** 將各模組的開發工作進一步分解為更小、可管理、可追蹤的開發任務或用戶故事，並進行初步的工作量估算。
    *   **依賴項：** 子任務 5.1 的產出，各模組 MDD。
    *   **產出物：** 詳細任務列表和估時 (可整合入開發指南或項目管理工具)。
    *   **狀態：** 待辦
    *   **委派模式：** Architect (或協調專案經理/技術主管)

3.  **子任務 5.3：定義詳細環境配置並確立 SOT**
    *   **行動：** 為開發、測試、預生產和生產等每個環境，詳細定義其網絡配置、服務器規格、數據庫連接信息、第三方服務憑證管理方式、環境變量等。創建獨立的環境定義文件 (`docs/env/[environment_name]_environment.md`) 並確立其為 SOT。創建 `docs/env/environment_variables_definition.md` 作為環境變量總覽 SOT。
    *   **依賴項：** [`docs/env/preliminary_environment_overview.md`](docs/env/preliminary_environment_overview.md)。
    *   **產出物：**
        *   `docs/env/development_environment.md`
        *   `docs/env/testing_environment.md`
        *   `docs/env/staging_environment.md`
        *   `docs/env/production_environment.md`
        *   `docs/env/environment_variables_definition.md`
    *   **狀態：** 待辦
    *   **委派模式：** Architect (或協調 DevOps 工程師)

4.  **子任務 5.4：撰寫開發指南文件**
    *   **行動：** 整合專案概覽、技術棧、環境配置指引 (引用 SOT)、核心數據模型 (引用 SOT)、API 規格 (引用 SOT)、各模組開發指南 (含 MDD 連結)、重要架構決策、關鍵流程參考、版本控制策略、編碼規範、代碼審查流程、CI/CD 流程初步構想等資訊，形成 [`docs/b2b_project_development_guidelines.md`](docs/b2b_project_development_guidelines.md)。
    *   **依賴項：** 專案至今所有相關規劃文件和 SOT 文件。
    *   **產出物：** [`docs/b2b_project_development_guidelines.md`](docs/b2b_project_development_guidelines.md)。
    *   **狀態：** 待辦
    *   **委派模式：** Architect

5.  **子任務 5.5：更新開發所需文件清單**
    *   **行動：** 與使用者討論並確認本階段衍生的、下一開發階段所需的技術文件，並將其包含預期內容摘要/目的，記錄/更新至 [`docs/required_development_documents.md`](docs/required_development_documents.md)。
    *   **依賴項：** 本階段所有產出物。
    *   **產出物：** 更新後的 [`docs/required_development_documents.md`](docs/required_development_documents.md)。
    *   **狀態：** 待辦
    *   **委派模式：** Architect

---

## 階段 6：測試計劃、驗收標準與部署策略定義

**目標：**
- 為 MVP 階段及後續各主要開發階段制定全面、詳細的測試計劃。
- 明確各階段和各核心模組的驗收標準。
- 定義 MVP 階段的部署策略和流程。

**子任務列表：**

1.  **子任務 6.1：完善總體測試策略**
    *   **行動：** 回顧並完善 [`docs/test-plan/overall_test_strategy.md`](docs/test-plan/overall_test_strategy.md) (如果已存在，否則創建)，確保其涵蓋測試左移、自動化策略、缺陷管理流程等。
    *   **依賴項：** 專案整體目標和質量要求。
    *   **產出物：** 更新或創建的 [`docs/test-plan/overall_test_strategy.md`](docs/test-plan/overall_test_strategy.md)。
    *   **狀態：** 待辦
    *   **委派模式：** Architect (或協調 QA 主管)

2.  **子任務 6.2：細化各階段測試計劃**
    *   **行動：**
        *   創建/更新 [`docs/test-plan/development_stage_test_plan.md`](docs/test-plan/development_stage_test_plan.md): 詳細定義單元測試、組件測試、API 接口測試的範圍、策略、工具、環境要求和指標。
        *   創建/更新 [`docs/test-plan/validation_stage_test_plan.md`](docs/test-plan/validation_stage_test_plan.md): 詳細定義功能測試、端到端測試、UAT、效能測試、安全性測試的範圍、目標、策略、測試案例重點、環境要求和成功標準。
        *   創建/更新 [`docs/test-plan/deployment_stage_test_plan.md`](docs/test-plan/deployment_stage_test_plan.md): 詳細定義部署驗證、冒煙測試、生產環境配置驗證、數據遷移驗證、回滾測試等的範圍、流程、環境和檢查點。
    *   **依賴項：** API SOT, 環境配置 SOT, 模組 MDD。
    *   **產出物：** 更新或創建的上述三個測試計劃文件。
    *   **狀態：** 待辦
    *   **委派模式：** Architect (或協調 QA 主管)

3.  **子任務 6.3：完善模組功能測試計劃**
    *   **行動：** 基於標準模板 ([`docs/test-plan/module_functional_test_plan_template.md`](docs/test-plan/module_functional_test_plan_template.md) - 如不存在則先創建)，為 MVP 階段的每個核心模組更新或創建具體的功能測試計劃，包含詳細的測試案例。
    *   **依賴項：** 各模組 MDD, 用戶故事。
    *   **產出物：** 各核心模組的功能測試計劃文件 (例如 `docs/test-plan/mod/user_auth_functional_test_plan.md`)。
    *   **狀態：** 待辦
    *   **委派模式：** Architect (或協調 QA 主管)

4.  **子任務 6.4：定義詳細驗收標準**
    *   **行動：** 為 MVP 階段整體以及每個核心模組的每個主要功能點，定義清晰、可衡量的驗收標準。這些標準應與用戶故事和 MRD 中的成功指標緊密相關。記錄在各模組 MDD 或用戶故事中。
    *   **依賴項：** [`docs/mvp_definition.md`](docs/mvp_definition.md), [`docs/project_mrd.md`](docs/project_mrd.md), 各模組 MDD。
    *   **產出物：** 更新後的各模組 MDD 或用戶故事文件，包含明確的驗收標準。
    *   **狀態：** 待辦
    *   **委派模式：** Architect (或協調業務分析師/產品負責人)

5.  **子任務 6.5：制定 MVP 部署策略與計劃**
    *   **行動：** 創建 [`docs/deploy/mvp_deployment_plan.md`](docs/deploy/mvp_deployment_plan.md)，詳細描述 MVP 的部署策略、部署步驟、回滾計劃、部署後監控方案和檢查清單。創建 [`docs/deploy/deployment_documents_checklist.md`](docs/deploy/deployment_documents_checklist.md)。
    *   **依賴項：** 環境配置 SOT, 系統架構文件。
    *   **產出物：**
        *   [`docs/deploy/mvp_deployment_plan.md`](docs/deploy/mvp_deployment_plan.md)
        *   [`docs/deploy/deployment_documents_checklist.md`](docs/deploy/deployment_documents_checklist.md)
    *   **狀態：** 待辦
    *   **委派模式：** Architect (或協調 DevOps 工程師)

6.  **子任務 6.6：更新開發所需文件清單**
    *   **行動：** 與使用者討論並確認本階段衍生的、下一開發階段所需的技術文件，並將其包含預期內容摘要/目的，記錄/更新至 [`docs/required_development_documents.md`](docs/required_development_documents.md)。
    *   **依賴項：** 本階段所有產出物。
    *   **產出物：** 更新後的 [`docs/required_development_documents.md`](docs/required_development_documents.md)。
    *   **狀態：** 待辦
    *   **委派模式：** Architect

---

## 階段 7：持續驗證、迭代與知識沉澱

**目標：**
- 在整個開發流程的每個關鍵斷點或重要決策點，利用 Research Mode 或其他外部信息源，對規劃的正確性、技術方案的可行性、市場趨勢的符合度等進行驗證。
- 確保專案方向與外部最佳實踐和技術發展保持一致，並根據驗證結果進行必要的調整和迭代。
- 將驗證過程和決策沉澱為知識資產。

**子任務列表：**

1.  **子任務 7.1：識別驗證節點與主題**
    *   **行動：** 在專案計劃的每個階段轉換點、重要的技術選型決策前、複雜功能設計完成後、遇到未知領域或重大不確定性時，均視為潛在的驗證節點。明確需要驗證的具體問題或主題。
    *   **依賴項：** 專案整體規劃。
    *   **產出物：** 驗證節點與主題列表 (可記錄在 [`memory-bank/research_log.md`](memory-bank/research_log.md) 或專案管理工具中)。
    *   **狀態：** 持續進行
    *   **委派模式：** Architect / Orchestrator

2.  **子任務 7.2：執行研究驗證**
    *   **行動：** 針對特定的規劃內容或技術決策，向 Research Mode Agent 提出明確問題，或通過其他方式搜集行業報告、競品分析、技術文檔等信息。
    *   **依賴項：** 子任務 7.1 的產出。
    *   **產出物：** 研究結果和初步分析。
    *   **狀態：** 按需執行
    *   **委派模式：** Architect / Orchestrator (可協調 Research Mode)

3.  **子任務 7.3：記錄與分析驗證結果**
    *   **行動：** 將研究的問題、過程、獲取的關鍵資訊、分析結論以及對現有規劃的潛在影響記錄在 [`memory-bank/research_log.md`](memory-bank/research_log.md)。
    *   **依賴項：** 子任務 7.2 的產出。
    *   **產出物：** 更新後的 [`memory-bank/research_log.md`](memory-bank/research_log.md)。
    *   **狀態：** 持續進行
    *   **委派模式：** Architect / DocWriter

4.  **子任務 7.4：團隊討論與決策調整**
    *   **行動：** 基於驗證結果和研究日誌，團隊共同討論是否需要對現有的規劃、設計或技術選型進行調整。如果決定調整，則返回到相應的規劃階段進行修改，並更新相關文件和 SOT。
    *   **依賴項：** 子任務 7.3 的產出。
    *   **產出物：** 決策記錄 (更新到 [`memory-bank/decisionLog.md`](memory-bank/decisionLog.md))，可能更新的規劃文件。
    *   **狀態：** 按需執行
    *   **委派模式：** Orchestrator (協調相關人員)

5.  **子任務 7.5：知識沉澱與共享**
    *   **行動：** 將重要的研究發現、決策過程和最終結論更新到相關的 Memory Bank 文件（如 [`decisionLog.md`](memory-bank/decisionLog.md), [`activeContext.md`](memory-bank/activeContext.md), [`systemPatterns.md`](memory-bank/systemPatterns.md)）中。確保團隊成員可以方便地查閱。
    *   **依賴項：** 子任務 7.4 的產出。
    *   **產出物：** 更新後的 Memory Bank 文件。
    *   **狀態：** 持續進行
    *   **委派模式：** DocWriter

6.  **子任務 7.6：更新開發所需文件清單 (持續)**
    *   **行動：** 在本階段的迭代過程中，若有新的技術文件需求產生，及時更新至 [`docs/required_development_documents.md`](docs/required_development_documents.md)。
    *   **依賴項：** 本階段所有活動。
    *   **產出物：** 更新後的 [`docs/required_development_documents.md`](docs/required_development_documents.md)。
    *   **狀態：** 持續進行
    *   **委派模式：** Architect

---

**附註：**
- 本計劃為初步規劃，實際執行時可能根據專案進展和使用者反饋進行調整。
- 每個子任務完成後，應更新 [`docs/roo_todo.md`](docs/roo_todo.md) 中的任務狀態。
- 所有產出物應按照專案文件管理規範存檔，並在 [`code_index.md`](code_index.md) 中正確索引。
- 關鍵決策應記錄於 [`memory-bank/decisionLog.md`](memory-bank/decisionLog.md)。