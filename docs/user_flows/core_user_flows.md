# Hwayo 核心使用者流程設計

## 文件資訊
- **文件名稱**: 核心使用者流程設計
- **建立日期**: 2025/05/30
- **階段**: 子任務 3.3 - 細化核心使用者流程
- **狀態**: 已完成
- **參考文件**: 
  - [`docs/mvp_definition.md`](../mvp_definition.md)
  - [`planning/productBrief.md`](../../planning/productBrief.md)
  - [`docs/architecture/system_architecture.md`](../architecture/system_architecture.md)

## 1. 流程設計概述

### 1.1 設計原則
- **用戶中心**: 以用戶角色和需求為核心設計流程
- **系統一致性**: 確保流程與系統架構設計一致
- **異常處理**: 涵蓋正常路徑、異常路徑和邊界條件
- **可追蹤性**: 每個步驟都有明確的系統響應和狀態變更

### 1.2 涵蓋範圍
本文件針對 [`docs/mvp_definition.md`](../mvp_definition.md) 中定義的 5 個核心用戶故事設計詳細流程：
- **US-001**: 研究員資料輸入
- **US-002**: 研究員報告提交
- **US-003**: 審核人員審核報告
- **US-004**: 審核人員簽核發送
- **US-005**: 客戶報告接收

## 2. 研究員核心流程

### 2.1 US-001: 研究員資料輸入流程

#### 2.1.1 正常路徑流程圖

```mermaid
flowchart TD
    A[研究員登入系統] --> B{身份驗證}
    B -->|成功| C[進入數據輸入介面]
    B -->|失敗| A1[顯示錯誤訊息]
    A1 --> A
    
    C --> D[選擇檢驗類型]
    D --> E[系統載入對應表單模板]
    E --> F[開始填寫實驗數據]
    
    F --> G[輸入基本資訊]
    G --> H[上傳檢驗數據]
    H --> I[附加檔案上傳]
    I --> J{資料驗證}
    
    J -->|驗證通過| K[儲存為草稿]
    J -->|驗證失敗| L[顯示錯誤提示]
    L --> M[修正錯誤資料]
    M --> J
    
    K --> N[系統記錄操作日誌]
    N --> O[顯示儲存成功訊息]
    O --> P{繼續編輯?}
    
    P -->|是| F
    P -->|否| Q[返回工作台]
    
    %% 樣式定義
    classDef userAction fill:#e3f2fd,stroke:#1565c0,stroke-width:2px
    classDef systemAction fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef decision fill:#fff3e0,stroke:#ef6c00,stroke-width:2px
    classDef error fill:#ffebee,stroke:#c62828,stroke-width:2px
    
    class A,C,D,F,G,H,I,M userAction
    class E,K,N,O,Q systemAction
    class B,J,P decision
    class A1,L error
```

#### 2.1.2 異常路徑處理

```mermaid
flowchart TD
    A[資料輸入過程中] --> B{異常情況}
    
    B -->|網路中斷| C[本地暫存資料]
    C --> D[顯示離線提示]
    D --> E[網路恢復後自動同步]
    
    B -->|檔案過大| F[壓縮檔案]
    F --> G{壓縮成功?}
    G -->|是| H[重新上傳]
    G -->|否| I[提示檔案過大錯誤]
    I --> J[建議分批上傳]
    
    B -->|資料格式錯誤| K[顯示格式要求]
    K --> L[提供範例檔案]
    L --> M[用戶修正後重試]
    
    B -->|權限不足| N[檢查用戶權限]
    N --> O[聯繫管理員]
    
    B -->|系統維護| P[顯示維護通知]
    P --> Q[提供預計恢復時間]
    
    %% 樣式
    classDef normal fill:#e3f2fd,stroke:#1565c0
    classDef error fill:#ffebee,stroke:#c62828
    classDef solution fill:#e8f5e8,stroke:#2e7d32
    
    class A normal
    class B,G decision
    class I,N,P error
    class C,E,F,H,J,K,L,M,O,Q solution
```

### 2.2 US-002: 研究員報告提交流程

#### 2.2.1 完整提交流程

```mermaid
flowchart TD
    A[研究員開啟草稿報告] --> B[檢視報告完整性]
    B --> C{必填欄位檢查}
    
    C -->|有缺失| D[標示缺失欄位]
    D --> E[補充必要資訊]
    E --> C
    
    C -->|完整| F[預覽報告格式]
    F --> G{確認提交?}
    
    G -->|否| H[返回編輯]
    H --> B
    
    G -->|是| I[執行最終驗證]
    I --> J{驗證結果}
    
    J -->|失敗| K[顯示驗證錯誤]
    K --> L[修正問題]
    L --> I
    
    J -->|成功| M[變更狀態為待審核]
    M --> N[分派審核任務]
    N --> O[發送通知給審核人員]
    O --> P[記錄提交日誌]
    P --> Q[顯示提交成功]
    Q --> R[鎖定報告編輯權限]
    R --> S[返回工作台]
    
    %% 平行處理
    O --> T[更新工作流程狀態]
    O --> U[觸發 SLA 計時器]
    
    %% 樣式
    classDef userAction fill:#e3f2fd,stroke:#1565c0,stroke-width:2px
    classDef systemAction fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef decision fill:#fff3e0,stroke:#ef6c00,stroke-width:2px
    classDef validation fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
    
    class A,E,H,L userAction
    class D,F,M,N,O,P,Q,R,S,T,U systemAction
    class C,G,J decision
    class I,K validation
```

## 3. 審核人員核心流程

### 3.1 US-003: 審核人員審核報告流程

#### 3.1.1 審核主流程

```mermaid
flowchart TD
    A[審核人員登入] --> B[查看待審核列表]
    B --> C[選擇報告進行審核]
    C --> D[載入報告詳細內容]
    
    D --> E[檢視實驗數據]
    E --> F[核對原始資料]
    F --> G[檢查計算結果]
    G --> H[驗證格式規範]
    
    H --> I{發現問題?}
    
    I -->|是| J[記錄審核意見]
    J --> K[標記問題區域]
    K --> L[提供修改建議]
    L --> M{問題嚴重程度}
    
    M -->|輕微| N[標記為需修正]
    M -->|嚴重| O[標記為退回]
    
    I -->|否| P[確認數據正確性]
    P --> Q[檢查報告完整性]
    Q --> R[標記為通過審核]
    
    N --> S[發送修正通知]
    O --> T[發送退回通知]
    R --> U[發送通過通知]
    
    S --> V[更新報告狀態]
    T --> V
    U --> V
    
    V --> W[記錄審核日誌]
    W --> X[返回待審核列表]
    
    %% 樣式
    classDef userAction fill:#e3f2fd,stroke:#1565c0,stroke-width:2px
    classDef systemAction fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef decision fill:#fff3e0,stroke:#ef6c00,stroke-width:2px
    classDef review fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
    
    class A,B,C,E,F,G,J,K,L userAction
    class D,S,T,U,V,W,X systemAction
    class I,M decision
    class H,N,O,P,Q,R review
```

#### 3.1.2 審核決策分支流程

```mermaid
flowchart TD
    A[審核決策點] --> B{審核結果}
    
    B -->|通過| C[準備簽核流程]
    C --> D[生成審核通過記錄]
    D --> E[觸發報告生成任務]
    E --> F[通知研究員審核通過]
    
    B -->|需修正| G[生成修正清單]
    G --> H[設定修正期限]
    H --> I[發送修正通知]
    I --> J[報告狀態變更為修正中]
    J --> K[等待研究員修正]
    
    B -->|退回| L[填寫退回原因]
    L --> M[生成詳細退回報告]
    M --> N[發送退回通知]
    N --> O[報告狀態變更為已退回]
    O --> P[研究員需重新提交]
    
    %% 後續流程
    K --> Q{修正完成?}
    Q -->|是| R[重新進入審核流程]
    Q -->|逾期| S[發送逾期提醒]
    S --> T[升級處理]
    
    %% 樣式
    classDef decision fill:#fff3e0,stroke:#ef6c00,stroke-width:2px
    classDef pass fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef modify fill:#fff8e1,stroke:#f57c00,stroke-width:2px
    classDef reject fill:#ffebee,stroke:#c62828,stroke-width:2px
    
    class A,B,Q decision
    class C,D,E,F pass
    class G,H,I,J,K,R,S,T modify
    class L,M,N,O,P reject
```

### 3.2 US-004: 審核人員簽核發送流程

#### 3.2.1 簽核發送主流程

```mermaid
flowchart TD
    A[審核通過的報告] --> B[進入簽核流程]
    B --> C[載入報告最終版本]
    C --> D[執行最終檢查]
    
    D --> E{最終檢查結果}
    E -->|通過| F[執行數位簽核]
    E -->|發現問題| G[返回審核流程]
    
    F --> H[記錄簽核時間戳]
    H --> I[記錄簽核人員資訊]
    I --> J[生成最終報告 PDF]
    
    J --> K{報告生成成功?}
    K -->|失敗| L[重試報告生成]
    L --> M{重試次數檢查}
    M -->|未超限| J
    M -->|超限| N[通知技術支援]
    
    K -->|成功| O[上傳到客戶入口]
    O --> P[生成安全下載連結]
    P --> Q[發送客戶通知郵件]
    
    Q --> R[更新報告狀態為已完成]
    R --> S[記錄完成日誌]
    S --> T[觸發 SLA 完成記錄]
    T --> U[發送內部完成通知]
    U --> V[歸檔報告]
    
    %% 樣式
    classDef userAction fill:#e3f2fd,stroke:#1565c0,stroke-width:2px
    classDef systemAction fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef decision fill:#fff3e0,stroke:#ef6c00,stroke-width:2px
    classDef critical fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
    classDef error fill:#ffebee,stroke:#c62828,stroke-width:2px
    
    class A,D,F userAction
    class B,C,H,I,J,O,P,Q,R,S,T,U,V systemAction
    class E,K,M decision
    class F,H,I critical
    class G,L,N error
```

## 4. 客戶核心流程

### 4.1 US-005: 客戶報告接收流程

#### 4.1.1 客戶接收主流程

```mermaid
flowchart TD
    A[客戶收到通知郵件] --> B[點擊安全連結]
    B --> C{連結驗證}
    
    C -->|無效| D[顯示連結過期錯誤]
    D --> E[提供聯繫方式]
    
    C -->|有效| F[進入客戶入口頁面]
    F --> G[顯示報告資訊]
    G --> H[提供下載選項]
    
    H --> I{選擇操作}
    
    I -->|線上預覽| J[載入 PDF 預覽]
    J --> K[顯示報告內容]
    K --> L{滿意報告?}
    
    I -->|直接下載| M[驗證下載權限]
    M --> N{權限檢查}
    N -->|通過| O[開始下載]
    N -->|失敗| P[顯示權限錯誤]
    
    O --> Q[記錄下載日誌]
    Q --> R[更新下載統計]
    
    L -->|是| S[可選擇下載]
    L -->|否| T[提供意見回饋]
    T --> U[發送回饋給審核人員]
    
    S --> M
    
    %% 歷史查詢分支
    I -->|查看歷史| V[載入歷史報告列表]
    V --> W[顯示報告狀態]
    W --> X[選擇特定報告]
    X --> J
    
    %% 樣式
    classDef userAction fill:#e3f2fd,stroke:#1565c0,stroke-width:2px
    classDef systemAction fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef decision fill:#fff3e0,stroke:#ef6c00,stroke-width:2px
    classDef error fill:#ffebee,stroke:#c62828,stroke-width:2px
    
    class A,B,T,X userAction
    class F,G,H,J,K,M,O,Q,R,U,V,W systemAction
    class C,I,L,N decision
    class D,E,P error
```

#### 4.1.2 客戶入口安全機制

```mermaid
flowchart TD
    A[客戶存取請求] --> B[檢查 IP 白名單]
    B --> C{IP 驗證}
    
    C -->|失敗| D[記錄可疑存取]
    D --> E[發送安全警告]
    E --> F[拒絕存取]
    
    C -->|通過| G[驗證時間戳]
    G --> H{連結是否過期?}
    
    H -->|是| I[顯示過期訊息]
    I --> J[提供重新申請選項]
    
    H -->|否| K[檢查存取次數]
    K --> L{超過存取限制?}
    
    L -->|是| M[顯示限制訊息]
    M --> N[記錄異常存取]
    
    L -->|否| O[允許存取]
    O --> P[記錄正常存取]
    P --> Q[更新存取計數]
    Q --> R[載入客戶介面]
    
    %% 樣式
    classDef security fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px
    classDef normal fill:#e8f5e8,stroke:#2e7d32,stroke-width:2px
    classDef warning fill:#fff3e0,stroke:#ef6c00,stroke-width:2px
    classDef error fill:#ffebee,stroke:#c62828,stroke-width:2px
    
    class A,B,G,K security
    class O,P,Q,R normal
    class I,J,M warning
    class D,E,F,N error
    class C,H,L decision
```

## 5. 跨角色整合流程

### 5.1 完整檢驗流程整合圖

```mermaid
sequenceDiagram
    participant R as 研究員
    participant S as 系統
    participant A as 審核人員
    participant C as 客戶
    participant N as 通知服務
    
    Note over R,C: 完整檢驗流程
    
    %% 資料輸入階段
    R->>S: 登入系統
    S->>R: 載入工作台
    R->>S: 建立新檢驗案例
    S->>R: 提供表單模板
    R->>S: 輸入實驗數據
    S->>S: 驗證數據格式
    S->>R: 儲存草稿成功
    
    %% 提交審核階段
    R->>S: 提交報告審核
    S->>S: 最終驗證
    S->>S: 變更狀態為待審核
    S->>N: 觸發審核通知
    N->>A: 發送審核任務通知
    
    %% 審核階段
    A->>S: 查看待審核報告
    S->>A: 載入報告內容
    A->>S: 執行審核檢查
    
    alt 審核通過
        A->>S: 標記審核通過
        S->>S: 準備簽核流程
        A->>S: 執行數位簽核
        S->>S: 生成最終報告
        S->>N: 觸發客戶通知
        N->>C: 發送報告完成通知
    else 需要修正
        A->>S: 提交修正意見
        S->>N: 觸發修正通知
        N->>R: 發送修正要求
        R->>S: 修正後重新提交
        Note over R,A: 重複審核流程
    else 退回重做
        A->>S: 退回報告
        S->>N: 觸發退回通知
        N->>R: 發送退回通知
        Note over R: 需重新開始流程
    end
    
    %% 客戶接收階段
    C->>S: 點擊安全連結
    S->>S: 驗證存取權限
    S->>C: 載入客戶入口
    C->>S: 下載報告
    S->>S: 記錄下載日誌
    S->>C: 提供 PDF 檔案
```

### 5.2 異常處理整合流程

```mermaid
flowchart TD
    A[系統異常發生] --> B{異常類型}
    
    B -->|用戶操作異常| C[記錄用戶行為]
    C --> D[提供操作指引]
    D --> E[用戶重試操作]
    
    B -->|系統服務異常| F[觸發服務監控]
    F --> G[自動重啟服務]
    G --> H{重啟成功?}
    H -->|是| I[恢復正常服務]
    H -->|否| J[升級技術支援]
    
    B -->|數據異常| K[數據完整性檢查]
    K --> L[備份數據恢復]
    L --> M[通知相關用戶]
    
    B -->|網路異常| N[切換備用連線]
    N --> O[維持服務可用性]
    
    B -->|安全異常| P[立即鎖定帳戶]
    P --> Q[發送安全警告]
    Q --> R[啟動安全調查]
    
    %% 統一處理
    E --> S[記錄異常日誌]
    I --> S
    J --> S
    M --> S
    O --> S
    R --> S
    
    S --> T[分析異常模式]
    T --> U[優化系統穩定性]
    
    %% 樣式
    classDef normal fill:#e8f5e8,stroke:#2e7d32
    classDef warning fill:#fff3e0,stroke:#ef6c00
    classDef error fill:#ffebee,stroke:#c62828
    classDef security fill:#f3e5f5,stroke:#7b1fa2
    
    class A,B decision
    class C,D,E,F,G,I,K,L,M,N,O normal
    class H decision
    class J,Q warning
    class P,R security
    class S,T,U normal
```

## 6. 邊界條件與特殊情況

### 6.1 系統負載邊界處理

```mermaid
flowchart TD
    A[系統負載監控] --> B{負載狀態}
    
    B -->|正常| C[維持正常服務]
    B -->|高負載| D[啟動負載均衡]
    B -->|超載| E[啟動限流機制]
    
    D --> F[分散請求到多節點]
    F --> G[優化資源分配]
    
    E --> H[優先處理關鍵操作]
    H --> I[延遲非關鍵任務]
    I --> J[通知用戶系統繁忙]
    
    J --> K{負載是否降低?}
    K -->|是| L[逐步恢復服務]
    K -->|否| M[啟動緊急擴容]
    
    L --> N[通知用戶服務恢復]
    M --> O[增加服務器資源]
    O --> P[重新分配負載]
    
    %% 樣式
    classDef normal fill:#e8f5e8,stroke:#2e7d32
    classDef warning fill:#fff3e0,stroke:#ef6c00
    classDef critical fill:#ffebee,stroke:#c62828
    
    class A,C,F,G,L,N,O,P normal
    class D,H,I,J warning
    class E,M critical
    class B,K decision
```

### 6.2 數據一致性保障

```mermaid
flowchart TD
    A[數據操作請求] --> B[開始事務]
    B --> C[執行業務邏輯]
    C --> D{操作成功?}
    
    D -->|是| E[提交事務]
    D -->|否| F[回滾事務]
    
    E --> G[更新快取]
    G --> H[同步相關系統]
    H --> I[記錄成功日誌]
    
    F --> J[恢復原始狀態]
    J --> K[記錄錯誤日誌]
    K --> L[通知用戶操作失敗]
    
    %% 並發控制
    A --> M[檢查資源鎖定]
    M --> N{資源可用?}
    N -->|否| O[等待或拒絕請求]
    N -->|是| P[獲取資源鎖]
    P --> B
    
    I --> Q[釋放資源鎖]
    L --> Q
    
    %% 樣式
    classDef normal fill:#e8f5e8,stroke:#2e7d32
    classDef transaction fill:#e3f2fd,stroke:#1565c0
    classDef error fill:#ffebee,stroke:#c62828
    
    class A,G,H,I,M,P,Q normal
    class B,C,E,F transaction
    class J,K,L,O error
    class D,N decision
```

## 7. 效能與可用性考量

### 7.1 系統回應時間優化

```mermaid
flowchart TD
    A[用戶請求] --> B[負載均衡器]
    B --> C[API 閘道]
    C --> D{請求類型}
    
    D -->|查詢操作| E[檢查快取]
    E --> F{快取命中?}
    F -->|是| G[返回快取結果]
    F -->|否| H[查詢資料庫]
    H --> I[更新快取]
    I --> J[返回查詢結果]
    
    D -->|寫入操作| K[直接處理]
    K --> L[非同步更新快取]
    L --> M[返回操作結果]
    
    D -->|複雜操作| N[加入任務佇列]
    N --> O[非同步處理]
    O --> P[通知處理完成]
    
    %% 效能監控
    G --> Q[記錄回應時間]
    J --> Q
    M --> Q
    P --> Q
    
    Q --> R{超過 SLA?}
    R -->|是| S[觸發效能警告]
    R -->|否| T[維持正常監控]
    
    %% 樣式
    classDef fast fill:#e8f5e8,stroke:#2e7d32
    classDef normal fill:#e3f2fd,stroke:#1565c0
    classDef slow fill:#fff3e0,stroke:#ef6c00
    classDef monitor fill:#f3e5f5,stroke:#7b1fa2
    
    class E,F,G fast
    class A,B,C,H,I,J,K,L,M normal
    class N,O,P slow
    class Q,R,S,T monitor
    class D,F,R decision
```

## 8. 總結

### 8.1 流程設計特點

1. **完整性**: 涵蓋所有核心用戶故事的詳細流程
2. **一致性**: 與系統架構設計保持一致
3. **健壯性**: 包含異常處理和邊界條件
4. **可追蹤性**: 每個步驟都有明確的日誌記錄
5. **用戶友好**: 考慮用戶體驗和操作便利性

### 8.2 關鍵設計決策

1. **狀態管理**: 使用明確的狀態轉換確保流程可控
2. **權限控制**: 在每個關鍵節點進行權限驗證
3. **通知機制**: 及時通知相關用戶流程進展
4. **錯誤處理**: 提供清晰的錯誤訊息和恢復路徑
5. **效能優化**: 使用快取和非同步處理提升用戶體驗

### 8.3 實施建議

1. **分階段實施**: 優先實現正常路徑，再完善異常處理
2. **用戶測試**: 與實際用戶進行流程驗證和優化
3. **監控機制**: 建立完整的流程監控和分析
4. **持續改進**: 基於用戶回饋持續優化流程設計

這些使用者流程設計為 Hwayo 檢驗管理系統的開發提供了詳細的指引，確保系統能夠滿足各類用戶的實際需求，同時保持高效、穩定的運行。