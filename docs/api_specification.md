# Hwayo 檢驗管理系統 - API 接口規格 (SOT)

## 文件資訊
- **文件名稱**: API 接口規格 (API Specification)
- **建立日期**: 2025/05/31
- **階段**: 子任務 4.2 - 定義 API 接口規格並確立 SOT
- **狀態**: 已完成
- **版本**: 1.0
- **參考文件**: 
  - [`docs/architecture/module_interaction_analysis.md`](architecture/module_interaction_analysis.md)
  - [`docs/architecture/system_architecture.md`](architecture/system_architecture.md)
  - [`docs/master_data_model.md`](master_data_model.md)
  - 各模組 MDD 文件 (位於 `docs/mod/` 目錄下)

## SOT 聲明 (Source of Truth Declaration)

**本文件為 Hwayo 檢驗管理系統的 API 接口規格唯一真實來源 (SOT)。**

所有系統開發、前端整合、第三方整合、模組間通信等相關工作，在涉及 API 接口時，必須以本文件為準。任何對 API 規格的變更，都必須先更新本文件，並通知所有相關開發人員和 API 消費者。

### SOT 適用範圍
- 內部模組間 API 通信
- 前端應用 API 調用
- 第三方系統整合 API
- 客戶入口 API 存取
- API Gateway 路由配置
- API 文檔生成
- API 測試腳本開發

## 1. API 設計概述

### 1.1 設計原則
1. **RESTful 架構**: 遵循 REST 設計原則，使用標準 HTTP 方法
2. **向後兼容**: 嚴格的向後兼容策略，不允許破壞性變更
3. **版本控制**: 通過 URL 路徑進行版本控制 (`/api/v1/`, `/api/v2/`)
4. **統一響應格式**: 標準化的成功和錯誤響應結構
5. **安全優先**: 完整的認證授權機制和資料保護
6. **文檔完整**: 詳細的 OpenAPI 規範和使用範例

### 1.2 API 分類
- **內部模組 API** (`/api/v1/internal/`): 模組間通信接口
- **管理後台 API** (`/api/v1/admin/`): 系統管理和配置接口
- **用戶操作 API** (`/api/v1/`): 一般用戶操作接口
- **客戶入口 API** (`/api/v1/customer/`): 客戶報告存取接口
- **第三方整合 API** (`/api/v1/external/`): 外部系統整合接口

### 1.3 認證與授權
- **JWT Bearer Token**: 主要認證機制
- **API Key**: 第三方系統認證
- **Customer Access Token**: 客戶報告存取令牌
- **Role-Based Access Control (RBAC)**: 基於角色的權限控制

### 1.4 版本管理策略
- **URL 路徑版本控制**: `/api/v1/`, `/api/v2/`
- **向後兼容承諾**: 同一主版本內不允許破壞性變更
- **新功能添加**: 通過新的端點或可選參數添加
- **廢棄通知**: 提前 6 個月通知 API 廢棄計劃
- **過渡期支持**: 新版本發布後至少支持舊版本 12 個月

## 2. 標準響應格式

### 2.1 成功響應格式
```json
{
  "success": true,
  "data": {
    // 實際數據內容
  },
  "meta": {
    "timestamp": "2025-05-31T01:49:00Z",
    "version": "v1",
    "requestId": "req_123456789"
  }
}
```

### 2.2 分頁響應格式
```json
{
  "success": true,
  "data": [
    // 數據項目陣列
  ],
  "meta": {
    "pagination": {
      "page": 1,
      "pageSize": 20,
      "totalItems": 150,
      "totalPages": 8,
      "hasNext": true,
      "hasPrev": false
    },
    "timestamp": "2025-05-31T01:49:00Z",
    "version": "v1",
    "requestId": "req_123456789"
  }
}
```

### 2.3 錯誤響應格式
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "請求參數驗證失敗",
    "details": [
      {
        "field": "email",
        "message": "電子郵件格式不正確"
      }
    ]
  },
  "meta": {
    "timestamp": "2025-05-31T01:49:00Z",
    "version": "v1",
    "requestId": "req_123456789"
  }
}
```

## 3. 標準錯誤碼

### 3.1 HTTP 狀態碼使用
- **200 OK**: 請求成功
- **201 Created**: 資源建立成功
- **204 No Content**: 請求成功但無返回內容
- **400 Bad Request**: 請求參數錯誤
- **401 Unauthorized**: 未認證或認證失敗
- **403 Forbidden**: 無權限存取
- **404 Not Found**: 資源不存在
- **409 Conflict**: 資源衝突
- **422 Unprocessable Entity**: 請求格式正確但語義錯誤
- **429 Too Many Requests**: 請求頻率超限
- **500 Internal Server Error**: 伺服器內部錯誤

### 3.2 業務錯誤碼
```yaml
# 認證相關錯誤 (AUTH_*)
AUTH_INVALID_CREDENTIALS: "認證憑證無效"
AUTH_TOKEN_EXPIRED: "認證令牌已過期"
AUTH_INSUFFICIENT_PERMISSIONS: "權限不足"
AUTH_ACCOUNT_LOCKED: "帳戶已被鎖定"

# 驗證相關錯誤 (VALIDATION_*)
VALIDATION_ERROR: "請求參數驗證失敗"
VALIDATION_REQUIRED_FIELD: "必填欄位缺失"
VALIDATION_INVALID_FORMAT: "欄位格式不正確"
VALIDATION_VALUE_OUT_OF_RANGE: "欄位值超出允許範圍"

# 業務邏輯錯誤 (BUSINESS_*)
BUSINESS_WORKFLOW_STATE_INVALID: "工作流程狀態無效"
BUSINESS_REVIEW_ALREADY_COMPLETED: "審核已完成，無法重複操作"
BUSINESS_REPORT_GENERATION_FAILED: "報告生成失敗"
BUSINESS_INSUFFICIENT_DATA: "數據不足，無法執行操作"

# 資源相關錯誤 (RESOURCE_*)
RESOURCE_NOT_FOUND: "資源不存在"
RESOURCE_ALREADY_EXISTS: "資源已存在"
RESOURCE_ACCESS_DENIED: "資源存取被拒絕"
RESOURCE_LOCKED: "資源已被鎖定"

# 系統相關錯誤 (SYSTEM_*)
SYSTEM_MAINTENANCE: "系統維護中"
SYSTEM_OVERLOAD: "系統負載過高"
SYSTEM_INTEGRATION_ERROR: "外部系統整合錯誤"
```

## 4. 核心 API 端點規格

### 4.1 用戶認證模組 API

#### 4.1.1 用戶登入
```yaml
POST /api/v1/auth/login
```

**請求體**:
```json
{
  "username": "string",     // 用戶名稱或電子郵件
  "password": "string",     // 密碼
  "rememberMe": "boolean"   // 可選，是否記住登入狀態
}
```

**成功響應** (200):
```json
{
  "success": true,
  "data": {
    "accessToken": "string",
    "refreshToken": "string",
    "expiresIn": 3600,
    "user": {
      "id": "string",
      "username": "string",
      "email": "string",
      "fullName": "string",
      "roles": ["string"],
      "permissions": ["string"]
    }
  }
}
```

#### 4.1.2 刷新令牌
```yaml
POST /api/v1/auth/refresh
```

**請求體**:
```json
{
  "refreshToken": "string"
}
```

#### 4.1.3 用戶登出
```yaml
POST /api/v1/auth/logout
Authorization: Bearer {accessToken}
```

#### 4.1.4 權限驗證
```yaml
POST /api/v1/internal/auth/verify-permission
Authorization: Bearer {accessToken}
```

**請求體**:
```json
{
  "userId": "string",
  "resource": "string",
  "action": "string",
  "context": {
    "recordId": "string",
    "departmentId": "string"
  }
}
```

### 4.2 數據輸入模組 API

#### 4.2.1 建立數據記錄
```yaml
POST /api/v1/data/records
Authorization: Bearer {accessToken}
```

**請求體**:
```json
{
  "testCaseId": "string",
  "formTemplateId": "string",
  "formData": {
    // 動態表單數據，結構由 formTemplate 定義
  },
  "attachments": [
    {
      "filename": "string",
      "fileData": "string",  // Base64 編碼
      "mimeType": "string"
    }
  ]
}
```

#### 4.2.2 更新數據記錄
```yaml
PUT /api/v1/data/records/{recordId}
Authorization: Bearer {accessToken}
```

#### 4.2.3 獲取數據記錄
```yaml
GET /api/v1/data/records/{recordId}
Authorization: Bearer {accessToken}
```

#### 4.2.4 更新記錄狀態 (內部 API)
```yaml
PUT /api/v1/internal/data/records/{recordId}/status
Authorization: Bearer {accessToken}
```

**請求體**:
```json
{
  "status": "string",           // draft, submitted, under_review, approved, rejected
  "workflowInstanceId": "string",
  "updatedBy": "string",
  "comment": "string"
}
```

### 4.3 工作流程引擎 API

#### 4.3.1 啟動工作流程實例
```yaml
POST /api/v1/workflow/instances
Authorization: Bearer {accessToken}
```

**請求體**:
```json
{
  "definitionId": "string",
  "entityId": "string",
  "entityType": "string",
  "variables": {
    "submitterId": "string",
    "sampleType": "string",
    "urgencyLevel": "string",
    "specialInstructions": "string"
  }
}
```

#### 4.3.2 完成工作流程任務
```yaml
POST /api/v1/workflow/tasks/{taskId}/complete
Authorization: Bearer {accessToken}
```

**請求體**:
```json
{
  "result": "string",           // approved, rejected, requires_modification
  "comments": [
    {
      "type": "string",
      "content": "string",
      "fieldPath": "string",
      "severity": "string"
    }
  ],
  "nextAction": "string",
  "metadata": {
    "reviewDuration": "number",
    "issuesFound": "number",
    "criticalIssues": "number"
  }
}
```

#### 4.3.3 獲取用戶任務列表
```yaml
GET /api/v1/workflow/tasks/my-tasks
Authorization: Bearer {accessToken}
Query Parameters:
  - status: string (optional)
  - priority: string (optional)
  - page: number (optional, default: 1)
  - pageSize: number (optional, default: 20)
```

#### 4.3.4 報告完成事件 (內部 API)
```yaml
POST /api/v1/internal/workflow/events/report-completed
Authorization: Bearer {accessToken}
```

**請求體**:
```json
{
  "workflowInstanceId": "string",
  "reportId": "string",
  "reportUrl": "string",
  "fileSize": "number",
  "generationTime": "number",
  "status": "string",
  "errorMessage": "string"
}
```

### 4.4 審核系統 API

#### 4.4.1 建立審核任務 (內部 API)
```yaml
POST /api/v1/internal/review/tasks
Authorization: Bearer {accessToken}
```

**請求體**:
```json
{
  "dataRecordId": "string",
  "workflowInstanceId": "string",
  "reviewType": "string",
  "assignedTo": "string",
  "dueDate": "string",
  "priority": "string",
  "metadata": {
    "sampleType": "string",
    "submitterName": "string",
    "submissionDate": "string"
  }
}
```

#### 4.4.2 獲取審核任務詳情
```yaml
GET /api/v1/review/tasks/{taskId}
Authorization: Bearer {accessToken}
```

#### 4.4.3 提交審核結果
```yaml
POST /api/v1/review/tasks/{taskId}/submit
Authorization: Bearer {accessToken}
```

**請求體**:
```json
{
  "result": "string",
  "comments": [
    {
      "type": "string",
      "content": "string",
      "fieldPath": "string",
      "severity": "string"
    }
  ],
  "digitalSignature": {
    "signatureData": "string",
    "certificateInfo": "string"
  }
}
```

#### 4.4.4 添加審核評論
```yaml
POST /api/v1/review/tasks/{taskId}/comments
Authorization: Bearer {accessToken}
```

### 4.5 報告產生器 API

#### 4.5.1 觸發報告生成 (內部 API)
```yaml
POST /api/v1/internal/reports/generate
Authorization: Bearer {accessToken}
```

**請求體**:
```json
{
  "dataRecordId": "string",
  "templateId": "string",
  "workflowInstanceId": "string",
  "generationOptions": {
    "format": "string",
    "includeRawData": "boolean",
    "includeCharts": "boolean",
    "watermark": "string"
  },
  "metadata": {
    "approvedBy": "string",
    "approvalDate": "string",
    "reportType": "string"
  }
}
```

#### 4.5.2 獲取報告狀態
```yaml
GET /api/v1/reports/{reportId}/status
Authorization: Bearer {accessToken}
```

#### 4.5.3 下載報告
```yaml
GET /api/v1/reports/{reportId}/download
Authorization: Bearer {accessToken}
```

#### 4.5.4 獲取報告列表
```yaml
GET /api/v1/reports
Authorization: Bearer {accessToken}
Query Parameters:
  - status: string (optional)
  - testCaseId: string (optional)
  - dateFrom: string (optional)
  - dateTo: string (optional)
  - page: number (optional)
  - pageSize: number (optional)
```

### 4.6 通知模組 API

#### 4.6.1 發送通知 (內部 API)
```yaml
POST /api/v1/internal/notifications
Authorization: Bearer {accessToken}
```

**請求體**:
```json
{
  "type": "string",
  "recipientId": "string",
  "templateId": "string",
  "templateData": {
    "workflowInstanceId": "string",
    "taskId": "string",
    "reportId": "string",
    "sampleName": "string",
    "dueDate": "string",
    "actionUrl": "string"
  },
  "priority": "string",
  "scheduledAt": "string"
}
```

#### 4.6.2 獲取用戶通知
```yaml
GET /api/v1/notifications/my-notifications
Authorization: Bearer {accessToken}
Query Parameters:
  - status: string (optional)
  - type: string (optional)
  - page: number (optional)
  - pageSize: number (optional)
```

#### 4.6.3 標記通知為已讀
```yaml
PUT /api/v1/notifications/{notificationId}/read
Authorization: Bearer {accessToken}
```

### 4.7 客戶入口 API

#### 4.7.1 發布報告到客戶入口 (內部 API)
```yaml
POST /api/v1/internal/customer-portal/reports/publish
Authorization: Bearer {accessToken}
```

**請求體**:
```json
{
  "reportId": "string",
  "customerId": "string",
  "reportMetadata": {
    "title": "string",
    "reportNumber": "string",
    "sampleName": "string",
    "testDate": "string",
    "expirationDate": "string"
  },
  "accessSettings": {
    "maxDownloads": "number",
    "accessDuration": "number",
    "requiresPassword": "boolean"
  }
}
```

#### 4.7.2 客戶存取報告
```yaml
GET /api/v1/customer/reports/{accessToken}
```

#### 4.7.3 客戶下載報告
```yaml
GET /api/v1/customer/reports/{accessToken}/download
```

#### 4.7.4 獲取客戶報告列表
```yaml
GET /api/v1/customer/reports
Authorization: Bearer {customerAccessToken}
Query Parameters:
  - status: string (optional)
  - dateFrom: string (optional)
  - dateTo: string (optional)
```

### 4.8 審計日誌 API

#### 4.8.1 記錄審計事件 (內部 API)
```yaml
POST /api/v1/internal/audit/log
Authorization: Bearer {accessToken}
```

**請求體**:
```json
{
  "eventType": "string",
  "category": "string",
  "userId": "string",
  "sessionId": "string",
  "resource": "string",
  "action": "string",
  "details": {
    "entityType": "string",
    "entityId": "string",
    "oldValues": {},
    "newValues": {},
    "workflowInstanceId": "string"
  },
  "result": "string",
  "severity": "string"
}
```

#### 4.8.2 查詢審計日誌
```yaml
GET /api/v1/admin/audit/logs
Authorization: Bearer {accessToken}
Query Parameters:
  - eventType: string (optional)
  - userId: string (optional)
  - resource: string (optional)
  - dateFrom: string (optional)
  - dateTo: string (optional)
  - page: number (optional)
  - pageSize: number (optional)
```

#### 4.8.3 記錄安全事件 (內部 API)
```yaml
POST /api/v1/internal/audit/security-events
Authorization: Bearer {accessToken}
```

## 5. 管理後台 API

### 5.1 用戶管理
```yaml
# 獲取用戶列表
GET /api/v1/admin/users

# 建立用戶
POST /api/v1/admin/users

# 更新用戶
PUT /api/v1/admin/users/{userId}

# 停用用戶
DELETE /api/v1/admin/users/{userId}

# 重設密碼
POST /api/v1/admin/users/{userId}/reset-password
```

### 5.2 角色權限管理
```yaml
# 獲取角色列表
GET /api/v1/admin/roles

# 建立角色
POST /api/v1/admin/roles

# 更新角色權限
PUT /api/v1/admin/roles/{roleId}

# 分配用戶角色
POST /api/v1/admin/users/{userId}/roles
```

### 5.3 系統配置
```yaml
# 獲取系統配置
GET /api/v1/admin/config

# 更新系統配置
PUT /api/v1/admin/config

# 獲取系統狀態
GET /api/v1/admin/system/status
```

## 6. 第三方整合 API

### 6.1 Webhook 端點
```yaml
# 接收外部系統通知
POST /api/v1/external/webhooks/{source}
Authorization: API-Key {apiKey}
```

### 6.2 數據同步 API
```yaml
# 同步檢驗數據
POST /api/v1/external/sync/test-data
Authorization: API-Key {apiKey}

# 獲取報告狀態
GET /api/v1/external/reports/{reportId}/status
Authorization: API-Key {apiKey}
```

## 7. 安全性規範

### 7.1 認證機制
- **JWT Bearer Token**: 內部用戶認證
- **API Key**: 第三方系統認證
- **Customer Access Token**: 客戶報告存取
- **Session Management**: 會話管理和超時控制

### 7.2 授權控制
- **Role-Based Access Control (RBAC)**: 基於角色的權限控制
- **Resource-Level Permissions**: 資源級別權限檢查
- **Context-Aware Authorization**: 上下文感知授權

### 7.3 資料保護
- **HTTPS Only**: 所有 API 僅支援 HTTPS
- **Request/Response Encryption**: 敏感資料加密傳輸
- **Data Masking**: 敏感資料遮罩處理
- **Audit Logging**: 完整的操作審計記錄

### 7.4 API 安全
- **Rate Limiting**: API 呼叫頻率限制
- **Input Validation**: 嚴格的輸入驗證
- **SQL Injection Prevention**: SQL 注入防護
- **XSS Protection**: 跨站腳本攻擊防護

## 8. 效能與可靠性

### 8.1 效能要求
- **響應時間**: 95% 的 API 請求在 500ms 內響應
- **吞吐量**: 支援每秒 1000 個併發請求
- **可用性**: 99.9% 的服務可用性

### 8.2 快取策略
- **Redis 快取**: 用戶會話和權限資訊快取
- **CDN 快取**: 靜態資源和報告檔案快取
- **Database Query Cache**: 資料庫查詢結果快取

### 8.3 錯誤處理
- **Graceful Degradation**: 優雅降級處理
- **Circuit Breaker**: 熔斷器模式
- **Retry Mechanism**: 自動重試機制
- **Fallback Strategy**: 備用策略

## 9. 監控與可觀測性

### 9.1 API 監控指標
- **Request Count**: 請求數量統計
- **Response Time**: 響應時間分佈
- **Error Rate**: 錯誤率監控
- **Throughput**: 吞吐量監控

### 9.2 分散式追蹤
- **Correlation ID**: 請求關聯 ID 追蹤
- **Distributed Tracing**: 跨服務請求追蹤
- **Performance Profiling**: 效能分析

### 9.3 日誌記錄
- **Structured Logging**: 結構化日誌記錄
- **Log Aggregation**: 日誌聚合分析
- **Real-time Alerting**: 即時告警機制

## 10. API 文檔與測試

### 10.1 OpenAPI 規範
本文件的完整 OpenAPI 3.x 規範將以 YAML 格式提供在 `docs/api_specification.yaml` 文件中，包含：
- 完整的端點定義
- 請求/響應 Schema
- 認證配置
- 錯誤碼定義
- 使用範例

### 10.1.1 API 文檔預覽建議
可以使用各種工具來預覽和交互 [`docs/api_specification.yaml`](docs/api_specification.yaml:1) 文件：

- **Swagger Editor**: 一個基於瀏覽器的編輯器，您可以在其中加載 YAML 文件並即時查看呈現的 UI 和進行交互。訪問 [Swagger Editor](https://editor.swagger.io/) 並將 YAML 內容貼上或導入文件。
- **Swagger UI**: 可以將 Swagger UI 整合到您的專案中，或使用公開的 Docker 映像在本地運行。例如，使用 Docker：
  ```bash
  docker run -p 8080:8080 -e SWAGGER_JSON_URL=https://raw.githubusercontent.com/YOUR_REPO/YOUR_PROJECT/YOUR_BRANCH/docs/api_specification.yaml swaggerapi/swagger-ui
  ```
  (請將 `YOUR_REPO`, `YOUR_PROJECT`, `YOUR_BRANCH` 替換為您的實際 GitHub 倉庫信息，或者將 YAML 文件掛載到容器中)
- **Redocly CLI**: Redocly 提供了強大的 CLI 工具，可以生成靜態 HTML 文檔或在本地啟動預覽伺服器。
  ```bash
  # 安裝 Redocly CLI (如果尚未安裝)
  npm install -g @redocly/cli
  # 預覽
  redocly preview-docs docs/api_specification.yaml
  # 或生成靜態 HTML
  redocly build-docs docs/api_specification.yaml -o public/api-docs.html
  ```
- **VS Code 擴展**: 有許多 VS Code 擴展（例如 "OpenAPI (Swagger) Editor" 或 "Swagger Viewer"）可以直接在編輯器中預覽 OpenAPI 文件。

選擇適合您團隊工作流程的工具，以方便地查閱和測試 API 規格。
### 10.2 API 測試
- **Unit Tests**: 單元測試覆蓋
- **Integration Tests**: 整合測試
- **Contract Tests**: 契約測試
- **Load Tests**: 負載測試

### 10.3 文檔生成
- **Swagger UI**: 互動式 API 文檔
- **Redocly**: 美化的 API 文檔
- **Postman Collection**: API 測試集合

## 11. 版本演進計劃

### 11.1 當前版本 (v1.0)
- 核心 MVP 功能 API
- 基礎認證授權
- 標準 CRUD 操作

### 11.2 未來版本規劃
- **v1.1**: 增強搜尋和篩選功能
- **v1.2**: 批量操作 API
- **v2.0**: GraphQL 支援
- **v2.1**: 即時通知 WebSocket API

## 12. 總結

本 API 規格文件定義了 Hwayo 檢驗管理系統的完整 API 接口，涵蓋了：

### 12.1 設計特點
1. **完整性**: 涵蓋所有核心業務流程的 API 端點
2. **一致性**: 統一的請求/響應格式和錯誤處理
3. **安全性**: 完整的認證授權和資料保護機制
4. **可擴展性**: 支援未來功能擴展的設計架構
5. **向後兼容**: 嚴格的版本控制和兼容性保證

### 12.2 關鍵決策
1. **RESTful 設計**: 採用標準 REST 架構，提供一致的 API 體驗
2. **JWT 認證**: 使用 JWT Bearer Token 作為主要認證機制
3. **版本控制**: 通過 URL 路徑進行版本控制，確保向後兼容
4. **標準化錯誤處理**: 統一的錯誤碼和響應格式
5. **內外部 API 分離**: 明確區分內部模組 API 和外部整合 API

### 12.3 實施建議
1. **優先實施核心 API**: 先實施用戶認證、數據輸入、工作流程等核心 API
2. **建立 API Gateway**: 統一的 API 入口點，處理認證、限流、路由
3. **完善監控體系**: 建立完整的 API 監控和告警機制
4. **自動化測試**: 建立完整的 API 測試套件
5. **文檔維護**: 保持 API 文檔與實際實施的同步更新

**下一步行動**: 建議切換到 Code 模式，根據此規格實施具體的 API 端點和相關基礎設施。