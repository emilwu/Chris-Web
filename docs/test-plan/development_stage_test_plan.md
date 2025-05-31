# Hwayo 專案開發階段測試計劃

## 文件資訊
- **文件名稱**: Hwayo 檢驗流程線上化系統 - 開發階段測試計劃
- **建立日期**: 2025/05/31
- **階段**: 子任務 6.2 - 細化各階段測試計劃
- **狀態**: 已完成
- **維護責任**: 開發團隊 + QA 團隊
- **版本**: v1.0
- **參考文件**: 
  - [`docs/test-plan/overall_test_strategy.md`](overall_test_strategy.md)
  - [`docs/api_specification.yaml`](../api_specification.yaml)
  - [`docs/environment_configs/development_env_sot.md`](../environment_configs/development_env_sot.md)
  - [`planning/project_development_dod_guide.md`](../../planning/project_development_dod_guide.md)
  - [`docs/hwayo_project_development_guidelines.md`](../hwayo_project_development_guidelines.md)

---

## 1. 引言

### 1.1 目的
本文件定義 Hwayo 專案開發階段的詳細測試計劃，確保：
- 代碼品質在開發過程中得到持續保證
- 單元測試和組件測試覆蓋所有核心功能
- API 接口符合規格要求並具備穩定性
- 開發團隊能夠快速獲得測試反饋

### 1.2 範圍
本計劃涵蓋開發階段的以下測試活動：
- **單元測試 (Unit Testing)**: 函數、方法、類別層級的測試
- **組件測試 (Component Testing)**: 模組層級的整合測試
- **API 接口測試 (API Testing)**: 基於 [`docs/api_specification.yaml`](../api_specification.yaml) 的接口測試

### 1.3 測試環境
基於 [`docs/environment_configs/development_env_sot.md`](../environment_configs/development_env_sot.md) 定義的開發環境配置。

---

## 2. 單元測試 (Unit Testing)

### 2.1 詳細策略與範圍

**測試策略**:
基於 [`docs/test-plan/overall_test_strategy.md`](overall_test_strategy.md) 中定義的測試左移理念，實施測試驅動開發 (TDD)。

**測試範圍**:
- 所有業務邏輯函數和方法
- 資料驗證和轉換邏輯
- 錯誤處理機制
- 工具函數和輔助方法
- 狀態管理邏輯

**不包含範圍**:
- 外部 API 調用（使用 Mock）
- 資料庫操作（使用 Mock）
- 檔案系統操作（使用 Mock）

### 2.2 各模組的單元測試重點

基於 `docs/mod/` 目錄下的模組設計文件 (MDD)：

#### 2.2.1 用戶認證模組 ([`docs/mod/user_auth_mdd.md`](../mod/user_auth_mdd.md))
```typescript
// 測試重點範例
describe('UserAuthService', () => {
  // 密碼驗證邏輯
  it('should validate password strength correctly')
  it('should hash password with proper salt')
  
  // JWT Token 處理
  it('should generate valid JWT tokens')
  it('should verify JWT tokens correctly')
  it('should handle expired tokens')
  
  // 權限驗證
  it('should validate user permissions')
  it('should handle role-based access control')
});
```

#### 2.2.2 資料輸入模組 ([`docs/mod/data_input_mdd.md`](../mod/data_input_mdd.md))
```typescript
describe('DataInputService', () => {
  // 資料驗證
  it('should validate input data format')
  it('should sanitize user input')
  it('should handle invalid data gracefully')
  
  // 資料轉換
  it('should transform data to internal format')
  it('should preserve data integrity during transformation')
});
```

#### 2.2.3 工作流程引擎 ([`docs/mod/workflow_engine_mdd.md`](../mod/workflow_engine_mdd.md))
```typescript
describe('WorkflowEngine', () => {
  // 狀態轉換
  it('should transition workflow states correctly')
  it('should validate state transition rules')
  it('should handle invalid state transitions')
  
  // 條件評估
  it('should evaluate workflow conditions')
  it('should handle complex conditional logic')
});
```

#### 2.2.4 審核系統 ([`docs/mod/review_system_mdd.md`](../mod/review_system_mdd.md))
```typescript
describe('ReviewService', () => {
  // 審核邏輯
  it('should assign reviews to appropriate reviewers')
  it('should calculate review priorities')
  it('should handle review conflicts')
  
  // 審核狀態管理
  it('should track review progress')
  it('should handle review timeouts')
});
```

#### 2.2.5 報告產生器 ([`docs/mod/report_generator_mdd.md`](../mod/report_generator_mdd.md))
```typescript
describe('ReportGenerator', () => {
  // 報告生成邏輯
  it('should generate reports with correct data')
  it('should apply proper formatting')
  it('should handle missing data gracefully')
  
  // 模板處理
  it('should process report templates correctly')
  it('should substitute variables properly')
});
```

#### 2.2.6 通知模組 ([`docs/mod/notification_mdd.md`](../mod/notification_mdd.md))
```typescript
describe('NotificationService', () => {
  // 通知邏輯
  it('should format notifications correctly')
  it('should determine notification recipients')
  it('should handle notification preferences')
  
  // 通知排程
  it('should schedule notifications properly')
  it('should handle notification retries')
});
```

### 2.3 工具與框架

**後端測試框架**:
```yaml
primary_framework: "Jest"
language: "TypeScript"
coverage_tool: "Istanbul"
mock_library: "Jest Mock Functions"

test_structure:
  - unit_tests/
    - services/
    - utils/
    - models/
    - controllers/
```

**前端測試框架**:
```yaml
primary_framework: "Jest + React Testing Library"
component_testing: "Storybook"
snapshot_testing: "Jest Snapshots"
mock_library: "MSW (Mock Service Worker)"

test_structure:
  - src/
    - components/__tests__/
    - hooks/__tests__/
    - utils/__tests__/
    - services/__tests__/
```

### 2.4 環境要求

**開發環境配置** (連結到 [`docs/environment_configs/development_env_sot.md`](../environment_configs/development_env_sot.md)):

```yaml
node_version: "18.x"
npm_version: "9.x"
test_database: "PostgreSQL 15 (Test Instance)"
redis_instance: "Redis 7.x (Test Instance)"

environment_variables:
  NODE_ENV: "test"
  DATABASE_URL: "postgresql://test_user:test_pass@localhost:5433/hwayo_test"
  REDIS_URL: "redis://localhost:6380"
  JWT_SECRET: "test_jwt_secret_key"
```

**測試資料庫設定**:
- 每次測試執行前自動重置
- 使用測試專用的資料庫實例
- 支援並行測試執行

### 2.5 覆蓋率目標與度量方法

**覆蓋率目標** (基於 [`docs/test-plan/overall_test_strategy.md`](overall_test_strategy.md)):
- **代碼覆蓋率**: ≥ 80%
- **分支覆蓋率**: ≥ 75%
- **函數覆蓋率**: ≥ 90%

**度量工具**:
```bash
# 覆蓋率報告生成
npm run test:coverage

# 覆蓋率閾值檢查
jest --coverage --coverageThreshold='{"global":{"branches":75,"functions":90,"lines":80,"statements":80}}'
```

**覆蓋率報告**:
- HTML 格式報告：`coverage/lcov-report/index.html`
- LCOV 格式：`coverage/lcov.info`
- JSON 格式：`coverage/coverage-final.json`

### 2.6 自動化執行方案

**本地開發環境**:
```bash
# 執行所有單元測試
npm run test:unit

# 監視模式（開發時使用）
npm run test:unit:watch

# 覆蓋率報告
npm run test:unit:coverage
```

**CI/CD 整合**:
```yaml
# GitHub Actions 範例
name: Unit Tests
on: [push, pull_request]

jobs:
  unit-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run test:unit:coverage
      - uses: codecov/codecov-action@v3
        with:
          file: ./coverage/lcov.info
```

---

## 3. 組件測試 (Component Testing)

### 3.1 詳細策略與範圍

**測試策略**:
- 測試完整模組的功能整合
- 使用真實的內部依賴，Mock 外部依賴
- 驗證模組的公開 API 接口
- 測試模組間的資料流

**測試範圍**:
- 每個核心模組的完整功能
- 模組內部各層次的協作
- 模組的錯誤處理機制
- 模組的配置和初始化

### 3.2 前端組件和後端組件的測試方法

#### 3.2.1 前端組件測試

**React 組件測試策略**:
```typescript
// 組件測試範例
describe('DataInputForm Component', () => {
  // 渲染測試
  it('should render all form fields correctly')
  it('should display validation errors')
  
  // 交互測試
  it('should handle form submission')
  it('should validate input on blur')
  
  // 狀態管理測試
  it('should update state on input change')
  it('should reset form after submission')
  
  // 整合測試
  it('should integrate with validation service')
  it('should call API on form submission')
});
```

**測試工具配置**:
```javascript
// jest.config.js
module.exports = {
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/src/setupTests.js'],
  moduleNameMapping: {
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy'
  }
};
```

#### 3.2.2 後端組件測試

**服務層組件測試**:
```typescript
describe('DataInputService Integration', () => {
  // 服務整合測試
  it('should process data input end-to-end')
  it('should handle validation errors properly')
  it('should integrate with workflow engine')
  
  // 資料庫整合測試
  it('should persist data correctly')
  it('should handle database constraints')
  
  // 外部服務整合測試（使用 Mock）
  it('should integrate with notification service')
  it('should handle external service failures')
});
```

### 3.3 工具與框架

**前端組件測試**:
```yaml
testing_framework: "Jest + React Testing Library"
component_isolation: "Storybook"
visual_regression: "Chromatic"
accessibility_testing: "jest-axe"

test_utilities:
  - "@testing-library/react"
  - "@testing-library/jest-dom"
  - "@testing-library/user-event"
```

**後端組件測試**:
```yaml
testing_framework: "Jest + Supertest"
database_testing: "Testcontainers"
mock_services: "MSW (Mock Service Worker)"

test_utilities:
  - "supertest" # HTTP 請求測試
  - "testcontainers" # 資料庫容器
  - "faker" # 測試資料生成
```

---

## 4. API 接口測試 (API Testing)

### 4.1 詳細策略與範圍

**測試策略** (基於 [`docs/api_specification.yaml`](../api_specification.yaml)):
- 驗證所有 API 端點的功能正確性
- 測試請求/回應格式的符合性
- 驗證錯誤處理和狀態碼
- 測試認證和授權機制

**API 測試範圍**:
基於 [`docs/api_specification.yaml`](../api_specification.yaml) 中定義的 API 標籤：

#### 4.1.1 認證 API (Authentication)
```yaml
test_endpoints:
  - POST /auth/login
  - POST /auth/logout
  - POST /auth/refresh
  - GET /auth/profile

test_scenarios:
  - 有效憑證登入
  - 無效憑證處理
  - Token 刷新機制
  - 會話過期處理
```

#### 4.1.2 資料記錄 API (DataRecords)
```yaml
test_endpoints:
  - POST /data-records
  - GET /data-records
  - GET /data-records/{id}
  - PUT /data-records/{id}
  - DELETE /data-records/{id}

test_scenarios:
  - CRUD 操作完整性
  - 資料驗證規則
  - 權限控制
  - 分頁和排序
```

#### 4.1.3 工作流程 API (Workflows)
```yaml
test_endpoints:
  - POST /workflows
  - GET /workflows/{id}
  - PUT /workflows/{id}/transition
  - GET /workflows/{id}/history

test_scenarios:
  - 工作流程創建
  - 狀態轉換
  - 歷史記錄查詢
  - 並行處理
```

#### 4.1.4 審核 API (Reviews)
```yaml
test_endpoints:
  - GET /reviews
  - POST /reviews/{id}/approve
  - POST /reviews/{id}/reject
  - PUT /reviews/{id}/reassign

test_scenarios:
  - 審核任務分配
  - 審核決策處理
  - 審核重新分配
  - 審核歷史追蹤
```

#### 4.1.5 報告 API (Reports)
```yaml
test_endpoints:
  - POST /reports/generate
  - GET /reports/{id}
  - GET /reports/{id}/download
  - GET /reports

test_scenarios:
  - 報告生成請求
  - 報告狀態查詢
  - 報告下載
  - 報告列表查詢
```

### 4.2 測試數據準備

**測試資料策略**:
```yaml
data_management:
  - 使用 Faker.js 生成測試資料
  - 準備標準測試資料集
  - 建立資料清理機制
  - 支援並行測試隔離

test_data_categories:
  - valid_data: "正常業務場景資料"
  - invalid_data: "異常和邊界值資料"
  - edge_cases: "邊界條件資料"
  - security_data: "安全測試資料"
```

**資料準備腳本**:
```typescript
// test-data-setup.ts
export const testDataSets = {
  validUser: {
    email: 'test@hwayo.com',
    password: 'SecurePass123!',
    role: 'researcher'
  },
  
  validDataRecord: {
    title: 'Test Experiment',
    data: { /* 實驗資料 */ },
    metadata: { /* 元資料 */ }
  },
  
  invalidInputs: [
    { field: 'email', value: 'invalid-email', error: 'INVALID_EMAIL' },
    { field: 'password', value: '123', error: 'WEAK_PASSWORD' }
  ]
};
```

### 4.3 工具 (例如 Postman/Newman, RestAssured)

**主要測試工具**:

#### 4.3.1 Postman + Newman
```yaml
usage: "API 文檔和手動測試"
automation: "Newman CLI 自動化執行"
features:
  - 集合管理和版本控制
  - 環境變數管理
  - 自動化測試腳本
  - 測試報告生成

collection_structure:
  - Authentication.postman_collection.json
  - DataRecords.postman_collection.json
  - Workflows.postman_collection.json
  - Reviews.postman_collection.json
  - Reports.postman_collection.json
```

#### 4.3.2 Supertest (Node.js)
```typescript
// API 自動化測試範例
describe('Data Records API', () => {
  it('should create a new data record', async () => {
    const response = await request(app)
      .post('/api/v1/data-records')
      .set('Authorization', `Bearer ${authToken}`)
      .send(testDataSets.validDataRecord)
      .expect(201);
      
    expect(response.body).toHaveProperty('id');
    expect(response.body.title).toBe(testDataSets.validDataRecord.title);
  });
  
  it('should validate required fields', async () => {
    const response = await request(app)
      .post('/api/v1/data-records')
      .set('Authorization', `Bearer ${authToken}`)
      .send({}) // 空資料
      .expect(400);
      
    expect(response.body.errors).toContain('title is required');
  });
});
```

### 4.4 自動化方案與 CI/CD 整合

**自動化執行流程**:
```bash
# 本地執行
npm run test:api

# 使用 Newman 執行 Postman 集合
newman run collections/DataRecords.postman_collection.json \
  --environment environments/development.postman_environment.json \
  --reporters cli,html \
  --reporter-html-export reports/api-test-report.html
```

**CI/CD 整合**:
```yaml
# GitHub Actions API 測試
name: API Tests
on: [push, pull_request]

jobs:
  api-tests:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
        env:
          POSTGRES_PASSWORD: postgres
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
    
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      
      - name: Install dependencies
        run: npm ci
      
      - name: Start application
        run: npm run start:test &
        
      - name: Wait for application
        run: npx wait-on http://localhost:3000/health
      
      - name: Run API tests
        run: npm run test:api
      
      - name: Run Postman collections
        run: |
          npm install -g newman
          newman run collections/*.postman_collection.json \
            --environment environments/ci.postman_environment.json \
            --reporters cli,junit \
            --reporter-junit-export results/api-test-results.xml
```

### 4.5 關鍵指標 (例如成功率、響應時間)

**效能指標**:
```yaml
response_time_targets:
  - GET_endpoints: "< 500ms"
  - POST_endpoints: "< 1000ms"
  - PUT_endpoints: "< 800ms"
  - DELETE_endpoints: "< 300ms"

success_rate_targets:
  - overall_success_rate: "> 99%"
  - authentication_success: "> 99.5%"
  - data_operations: "> 99%"

monitoring_metrics:
  - average_response_time
  - 95th_percentile_response_time
  - error_rate_by_endpoint
  - throughput_per_minute
```

**測試報告指標**:
```typescript
// 測試報告範例結構
interface ApiTestReport {
  summary: {
    totalTests: number;
    passed: number;
    failed: number;
    successRate: number;
  };
  
  performance: {
    averageResponseTime: number;
    p95ResponseTime: number;
    slowestEndpoint: string;
  };
  
  coverage: {
    endpointsCovered: number;
    totalEndpoints: number;
    coveragePercentage: number;
  };
  
  errors: Array<{
    endpoint: string;
    method: string;
    error: string;
    frequency: number;
  }>;
}
```

---

## 5. 測試執行計劃

### 5.1 測試執行順序
1. **單元測試**: 每次代碼提交時執行
2. **組件測試**: 功能完成時執行
3. **API 測試**: 接口實現完成時執行

### 5.2 測試自動化觸發條件
- **代碼提交**: 觸發單元測試
- **Pull Request**: 觸發完整測試套件
- **每日構建**: 執行完整回歸測試
- **發布前**: 執行所有測試並生成報告

### 5.3 測試失敗處理流程
1. 自動通知相關開發人員
2. 阻止代碼合併（如果是 PR）
3. 記錄失敗原因和修復時間
4. 更新測試案例（如需要）

---

## 6. 測試報告與度量

### 6.1 測試報告格式
- **每日測試報告**: 自動生成並發送給團隊
- **週度測試總結**: 包含趨勢分析和改善建議
- **里程碑測試報告**: 重要功能完成時的詳細報告

### 6.2 關鍵度量指標
- 測試覆蓋率趨勢
- 測試執行時間
- 缺陷發現率
- 修復時間統計

---

## 7. 風險與緩解措施

### 7.1 主要風險
- **測試環境不穩定**: 建立自動化環境重置機制
- **測試資料污染**: 實施測試資料隔離策略
- **測試執行時間過長**: 優化測試並行執行

### 7.2 緩解措施
- 定期檢查和維護測試環境
- 建立測試資料管理最佳實踐
- 持續優化測試執行效率

---

## 8. 附錄

### 8.1 測試工具配置範例
詳細的工具配置檔案和設定說明。

### 8.2 測試資料範本
標準測試資料的結構和範例。

### 8.3 常見問題解決方案
開發階段測試常見問題的解決方法。