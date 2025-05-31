# Hwayo 專案系統架構設計

## 文件資訊
- **文件名稱**: 系統架構設計
- **建立日期**: 2025/05/30
- **階段**: 子任務 3.2 - 繪製模組依賴圖與系統架構圖
- **狀態**: 已完成
- **參考文件**: 
  - [`docs/architecture/module_interaction_analysis.md`](module_interaction_analysis.md)
  - [`planning/productBrief.md`](../planning/productBrief.md)
  - MVP 核心模組 MDD 文件

## 1. 高層次系統架構圖

### 1.1 系統上下文圖 (C4 Context Diagram)

```mermaid
C4Context
    title Hwayo 檢驗實驗室管理系統 - 系統上下文圖
    
    Person(researcher, "研究員", "負責實驗數據輸入與報告草稿")
    Person(reviewer, "審核人員", "負責數據審核與報告簽核")
    Person(admin, "管理員", "負責系統管理與權限設定")
    Person(customer, "客戶", "接收檢驗報告")
    
    System(hwayo, "Hwayo 檢驗管理系統", "線上化檢驗流程管理平台")
    
    System_Ext(email, "Email 服務", "發送通知郵件")
    System_Ext(sms, "SMS 服務", "發送簡訊通知")
    System_Ext(storage, "檔案儲存服務", "儲存報告與附件")
    
    Rel(researcher, hwayo, "輸入實驗數據", "HTTPS")
    Rel(reviewer, hwayo, "審核數據與報告", "HTTPS")
    Rel(admin, hwayo, "系統管理", "HTTPS")
    Rel(customer, hwayo, "下載檢驗報告", "HTTPS")
    
    Rel(hwayo, email, "發送通知", "SMTP")
    Rel(hwayo, sms, "發送簡訊", "API")
    Rel(hwayo, storage, "儲存檔案", "API")
```

### 1.2 系統容器圖 (C4 Container Diagram)

```mermaid
C4Container
    title Hwayo 檢驗管理系統 - 容器圖
    
    Person(users, "系統用戶", "研究員、審核人員、管理員、客戶")
    
    Container(web, "Web 應用程式", "React/Vue.js", "提供用戶介面")
    Container(api, "API 閘道", "Node.js/Express", "統一 API 入口點")
    Container(auth, "認證服務", "Node.js", "用戶認證與權限管理")
    Container(workflow, "流程引擎", "Node.js", "業務流程管理")
    Container(notification, "通知服務", "Node.js", "訊息通知處理")
    
    ContainerDb(maindb, "主資料庫", "PostgreSQL", "儲存業務資料")
    ContainerDb(logdb, "日誌資料庫", "MongoDB", "儲存審計日誌")
    ContainerDb(cache, "快取", "Redis", "快取常用資料")
    ContainerDb(queue, "訊息佇列", "Redis/RabbitMQ", "非同步任務處理")
    
    Container_Ext(storage, "檔案儲存", "AWS S3/MinIO", "儲存報告與附件")
    
    Rel(users, web, "使用", "HTTPS")
    Rel(web, api, "API 呼叫", "HTTPS/REST")
    Rel(api, auth, "認證驗證", "HTTP")
    Rel(api, workflow, "流程操作", "HTTP")
    Rel(api, notification, "發送通知", "HTTP")
    
    Rel(auth, maindb, "讀寫", "SQL")
    Rel(workflow, maindb, "讀寫", "SQL")
    Rel(workflow, queue, "發布任務", "TCP")
    Rel(notification, queue, "消費任務", "TCP")
    
    Rel(api, cache, "快取", "TCP")
    Rel(workflow, logdb, "寫入日誌", "TCP")
    Rel(notification, storage, "存取檔案", "HTTPS")
```

## 2. 模組依賴關係圖

### 2.1 MVP 核心模組依賴圖

```mermaid
graph TD
    %% 定義模組
    UA[用戶認證模組<br/>UserAuthModule]
    DI[數據輸入模組<br/>DataInputModule]
    WF[流程引擎<br/>WorkflowEngineModule]
    RV[審核系統<br/>ReviewSystemModule]
    RG[報告產生器<br/>ReportGeneratorModule]
    NT[通知模組<br/>NotificationModule]
    CP[客戶入口<br/>CustomerPortalModule]
    AL[審計日誌<br/>AuditLogModule]
    
    %% 認證模組作為基礎依賴
    UA --> DI
    UA --> WF
    UA --> RV
    UA --> RG
    UA --> CP
    
    %% 主要業務流程依賴
    DI --> WF
    DI --> AL
    
    WF --> RV
    WF --> RG
    WF --> NT
    WF --> AL
    
    RV --> NT
    RV --> AL
    
    RG --> CP
    RG --> AL
    
    NT --> AL
    CP --> AL
    
    %% 樣式定義
    classDef coreModule fill:#e1f5fe,stroke:#01579b,stroke-width:2px
    classDef supportModule fill:#f3e5f5,stroke:#4a148c,stroke-width:2px
    classDef infraModule fill:#e8f5e8,stroke:#1b5e20,stroke-width:2px
    
    class UA,WF coreModule
    class DI,RV,RG,CP supportModule
    class NT,AL infraModule
```

### 2.2 模組層次架構圖

```mermaid
graph TB
    subgraph "展示層 (Presentation Layer)"
        UI[Web 用戶介面]
        API[API 閘道]
    end
    
    subgraph "業務邏輯層 (Business Logic Layer)"
        subgraph "核心業務模組"
            WF[流程引擎]
            UA[用戶認證]
        end
        
        subgraph "功能模組"
            DI[數據輸入]
            RV[審核系統]
            RG[報告產生器]
            CP[客戶入口]
        end
    end
    
    subgraph "基礎設施層 (Infrastructure Layer)"
        NT[通知模組]
        AL[審計日誌]
        DB[(資料庫)]
        CACHE[(快取)]
        QUEUE[(訊息佇列)]
        STORAGE[(檔案儲存)]
    end
    
    %% 連接關係
    UI --> API
    API --> WF
    API --> UA
    API --> DI
    API --> RV
    API --> RG
    API --> CP
    
    WF --> NT
    WF --> AL
    RV --> NT
    RG --> NT
    
    DI --> DB
    WF --> DB
    RV --> DB
    RG --> DB
    CP --> DB
    UA --> DB
    
    NT --> QUEUE
    RG --> STORAGE
    CP --> STORAGE
    
    WF --> CACHE
    UA --> CACHE
    
    AL --> DB
    
    %% 樣式
    classDef presentation fill:#bbdefb,stroke:#1976d2
    classDef business fill:#c8e6c9,stroke:#388e3c
    classDef infrastructure fill:#ffcdd2,stroke:#d32f2f
    
    class UI,API presentation
    class WF,UA,DI,RV,RG,CP business
    class NT,AL,DB,CACHE,QUEUE,STORAGE infrastructure
```

## 3. 數據流架構圖

### 3.1 主要業務數據流

```mermaid
flowchart TD
    A[研究員提交實驗數據] --> B[數據輸入模組驗證]
    B --> C[流程引擎建立工作流程實例]
    C --> D[自動分派審核任務]
    D --> E[通知模組發送任務通知]
    E --> F[審核人員執行審核]
    F --> G{審核結果}
    
    G -->|通過| H[觸發報告生成]
    G -->|不通過| I[退回修改]
    
    H --> J[報告產生器生成 PDF]
    J --> K[發布到客戶入口]
    K --> L[通知客戶報告完成]
    
    I --> M[通知研究員修改]
    M --> A
    
    %% 橫跨整個流程的基礎服務
    N[審計日誌記錄] -.-> B
    N -.-> C
    N -.-> F
    N -.-> H
    N -.-> K
    
    O[用戶認證驗證] -.-> A
    O -.-> F
    O -.-> L
    
    %% 樣式
    classDef process fill:#e3f2fd,stroke:#1565c0
    classDef decision fill:#fff3e0,stroke:#ef6c00
    classDef service fill:#f1f8e9,stroke:#558b2f
    
    class A,B,C,D,F,H,J,K,M process
    class G decision
    class N,O service
```

### 3.2 系統間通信架構

```mermaid
sequenceDiagram
    participant UI as Web UI
    participant API as API Gateway
    participant AUTH as 認證服務
    participant WF as 流程引擎
    participant RV as 審核系統
    participant RG as 報告產生器
    participant NT as 通知服務
    participant DB as 資料庫
    participant QUEUE as 訊息佇列
    
    UI->>API: 提交實驗數據
    API->>AUTH: 驗證用戶權限
    AUTH-->>API: 權限確認
    API->>WF: 啟動檢驗流程
    WF->>DB: 建立流程實例
    WF->>RV: 建立審核任務
    WF->>QUEUE: 發布通知任務
    QUEUE->>NT: 處理通知任務
    NT-->>API: 任務建立完成
    
    Note over RV: 審核人員執行審核
    RV->>WF: 提交審核結果
    WF->>QUEUE: 發布報告生成任務
    QUEUE->>RG: 處理報告生成
    RG->>DB: 儲存報告資訊
    RG-->>WF: 報告生成完成
    WF->>QUEUE: 發布客戶通知任務
    QUEUE->>NT: 發送客戶通知
```

## 4. 技術架構說明

### 4.1 架構設計原則

1. **模組化設計**: 每個模組具有明確的職責邊界，降低耦合度
2. **分層架構**: 清晰的展示層、業務邏輯層、基礎設施層分離
3. **事件驅動**: 使用訊息佇列實現模組間的非同步通信
4. **可擴展性**: 支援水平擴展和模組獨立部署
5. **安全性**: 統一的認證授權機制和完整的審計追蹤

### 4.2 核心模組職責

#### 4.2.1 核心業務模組
- **用戶認證模組**: 提供統一的身份驗證和權限管理服務
- **流程引擎**: 管理檢驗業務流程的狀態轉換和任務分派

#### 4.2.2 功能模組
- **數據輸入模組**: 處理實驗數據的輸入、驗證和儲存
- **審核系統**: 管理審核任務的分派、執行和結果處理
- **報告產生器**: 根據模板生成標準化的檢驗報告
- **客戶入口**: 提供客戶存取報告的安全介面

#### 4.2.3 基礎設施模組
- **通知模組**: 處理各種通知訊息的發送
- **審計日誌**: 記錄所有系統操作的完整追蹤

### 4.3 關鍵交互模式

#### 4.3.1 同步交互
- 用戶認證驗證
- 數據驗證和儲存
- 即時狀態查詢

#### 4.3.2 非同步交互
- 通知發送
- 報告生成
- 審計日誌記錄

#### 4.3.3 事件驅動
- 工作流程狀態變更
- 任務分派和完成
- 系統間狀態同步

## 5. 部署架構考量

### 5.1 微服務部署策略

```mermaid
graph TB
    subgraph "負載均衡層"
        LB[Load Balancer]
    end
    
    subgraph "應用服務層"
        subgraph "Web 服務"
            WEB1[Web App 1]
            WEB2[Web App 2]
        end
        
        subgraph "API 服務"
            API1[API Gateway 1]
            API2[API Gateway 2]
        end
        
        subgraph "業務服務"
            AUTH[認證服務]
            WF[流程引擎]
            NT[通知服務]
        end
    end
    
    subgraph "資料層"
        DB[(主資料庫)]
        CACHE[(Redis 快取)]
        QUEUE[(訊息佇列)]
    end
    
    LB --> WEB1
    LB --> WEB2
    LB --> API1
    LB --> API2
    
    WEB1 --> API1
    WEB2 --> API2
    
    API1 --> AUTH
    API1 --> WF
    API2 --> AUTH
    API2 --> WF
    
    WF --> NT
    AUTH --> DB
    WF --> DB
    NT --> QUEUE
    
    API1 --> CACHE
    API2 --> CACHE
```

### 5.2 擴展性設計

1. **水平擴展**: Web 應用和 API 服務支援多實例部署
2. **資料庫分離**: 讀寫分離和業務資料與日誌資料分離
3. **快取策略**: 多層快取提升系統效能
4. **非同步處理**: 訊息佇列處理耗時操作

## 6. 安全架構

### 6.1 安全層次

```mermaid
graph TD
    subgraph "網路安全層"
        HTTPS[HTTPS 加密]
        WAF[Web 應用防火牆]
    end
    
    subgraph "應用安全層"
        AUTH[身份認證]
        AUTHZ[權限授權]
        RATE[頻率限制]
    end
    
    subgraph "資料安全層"
        ENCRYPT[資料加密]
        BACKUP[備份機制]
        AUDIT[審計追蹤]
    end
    
    HTTPS --> AUTH
    WAF --> AUTHZ
    AUTH --> ENCRYPT
    AUTHZ --> BACKUP
    RATE --> AUDIT
```

### 6.2 安全控制措施

1. **傳輸安全**: 全站 HTTPS 加密
2. **身份驗證**: JWT Token 機制
3. **權限控制**: 基於角色的存取控制 (RBAC)
4. **資料保護**: 敏感資料加密儲存
5. **審計追蹤**: 完整的操作日誌記錄

## 7. 監控與可觀測性

### 7.1 監控架構

```mermaid
graph LR
    subgraph "應用層監控"
        APP[應用程式指標]
        API[API 效能監控]
        ERR[錯誤追蹤]
    end
    
    subgraph "基礎設施監控"
        SYS[系統資源監控]
        DB[資料庫監控]
        NET[網路監控]
    end
    
    subgraph "業務監控"
        BIZ[業務指標]
        SLA[SLA 監控]
        ALERT[告警系統]
    end
    
    APP --> ALERT
    API --> ALERT
    ERR --> ALERT
    SYS --> ALERT
    DB --> ALERT
    NET --> ALERT
    BIZ --> SLA
    SLA --> ALERT
```

### 7.2 關鍵監控指標

1. **系統指標**: CPU、記憶體、磁碟、網路使用率
2. **應用指標**: 回應時間、吞吐量、錯誤率
3. **業務指標**: 流程完成時間、審核通過率、客戶滿意度
4. **安全指標**: 登入失敗次數、異常存取模式

## 8. 總結

本系統架構設計基於 Hwayo 檢驗實驗室的業務需求，採用模組化、分層化的設計原則，確保系統的：

1. **可維護性**: 清晰的模組邊界和職責分離
2. **可擴展性**: 支援業務成長和功能擴展
3. **可靠性**: 完整的錯誤處理和恢復機制
4. **安全性**: 多層次的安全防護措施
5. **可觀測性**: 全面的監控和追蹤能力

此架構為 MVP 階段提供了堅實的技術基礎，同時為後續階段的功能擴展預留了充分的空間。

## 9. 下一步行動

1. **詳細設計**: 基於此架構進行各模組的詳細技術設計
2. **技術選型**: 確定具體的技術棧和第三方服務
3. **開發規劃**: 制定模組開發的優先順序和時程
4. **環境準備**: 建立開發、測試、生產環境
5. **團隊協作**: 建立開發團隊的協作流程和標準