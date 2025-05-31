# Hwayo 專案開發指南

## 文件資訊
- **文件名稱**: Hwayo 專案開發指南
- **建立日期**: 2025/05/31
- **階段**: 子任務 5.4 - 撰寫開發指南文件
- **狀態**: 已完成
- **維護責任**: 技術領導 + 開發團隊
- **版本**: v1.0

---

## 1. 引言與專案概覽

### 1.1 專案目標與範圍

**Hwayo 檢驗流程線上化系統** 旨在將傳統紙本檢驗流程轉換為高效的線上化管理平台。

**核心目標**:
- 消除紙本報表的手寫流程，提升效率並減少錯誤
- 建立標準化的線上審核機制，確保報告品質
- 提供客戶線上報告接收功能，改善客戶體驗
- 實現檢驗資料的集中管理和數據庫記錄

**詳細專案資訊請參考**:
- [`planning/productBrief.md`](../planning/productBrief.md) - 專案簡報與背景
- [`docs/mvp_definition.md`](mvp_definition.md) - MVP 定義與範圍

### 1.2 指南目的與適用讀者

本指南為 Hwayo 專案開發團隊提供統一的開發標準、流程規範和技術指引，確保：
- 開發品質的一致性和可維護性
- 團隊協作的高效性
- 專案交付的可預測性

**適用讀者**: 全端開發工程師、前端工程師、後端工程師、DevOps 工程師、QA 工程師、專案經理

---

## 2. 技術棧與架構

### 2.1 核心技術選型

**後端技術棧**:
- **運行環境**: Node.js 18.x LTS
- **開發語言**: TypeScript 5.x
- **Web 框架**: Express.js 4.x
- **資料庫**: PostgreSQL 15.x (主資料庫)
- **快取**: Redis 7.x
- **訊息佇列**: Redis/RabbitMQ
- **日誌資料庫**: MongoDB 6.x

**前端技術棧**:
- **框架**: React 18.x + TypeScript
- **狀態管理**: Redux Toolkit
- **UI 組件庫**: Material-UI (MUI) 5.x
- **路由**: React Router 6.x
- **HTTP 客戶端**: Axios

**開發工具**:
- **代碼品質**: ESLint + Prettier
- **測試框架**: Jest + React Testing Library
- **API 文檔**: Swagger/OpenAPI 3.0
- **版本控制**: Git + GitHub

### 2.2 系統架構概覽

系統採用微服務導向的模組化架構，詳細架構設計請參考：
- [`docs/architecture/system_architecture.md`](architecture/system_architecture.md) - 完整系統架構設計

**核心模組概覽**:
- **用戶認證模組** ([`docs/mod/user_auth_mdd.md`](mod/user_auth_mdd.md))
- **數據輸入模組** ([`docs/mod/data_input_mdd.md`](mod/data_input_mdd.md))
- **流程引擎模組** ([`docs/mod/workflow_engine_mdd.md`](mod/workflow_engine_mdd.md))
- **審核系統模組** ([`docs/mod/review_system_mdd.md`](mod/review_system_mdd.md))
- **報告產生器模組** ([`docs/mod/report_generator_mdd.md`](mod/report_generator_mdd.md))
- **通知模組** ([`docs/mod/notification_mdd.md`](mod/notification_mdd.md))
- **客戶入口模組** ([`docs/mod/customer_portal_mdd.md`](mod/customer_portal_mdd.md))
- **審計日誌模組** ([`docs/mod/audit_log_mdd.md`](mod/audit_log_mdd.md))

---

## 3. 環境設置與配置

### 3.1 開發環境設置

**詳細開發環境配置請參考**:
- [`docs/environment_configs/development_env_sot.md`](environment_configs/development_env_sot.md)

**基本需求**:
- **作業系統**: macOS 12+, Ubuntu 20.04+, 或 Windows 11 (with WSL2)
- **記憶體**: 最少 8GB RAM (推薦 16GB)
- **Docker**: Docker Desktop 4.15+
- **IDE**: VS Code 1.75+ (推薦擴充套件清單見環境配置文件)

### 3.2 測試/預備環境

**詳細配置請參考**:
- [`docs/environment_configs/staging_env_sot.md`](environment_configs/staging_env_sot.md)

### 3.3 生產環境

**詳細配置請參考**:
- [`docs/environment_configs/production_env_sot.md`](environment_configs/production_env_sot.md)

### 3.4 環境變量與敏感配置管理

**配置管理原則**:
- 使用 `.env` 文件管理本地開發配置
- 敏感資訊 (API 金鑰、資料庫密碼) 絕不提交到版本控制
- 生產環境使用環境變量或安全的配置管理服務
- 所有配置項目必須在相應的環境 SOT 文件中記錄

---

## 4. 開發流程與實踐

### 4.1 開發順序與迭代計劃

**完整開發計劃請參考**:
- [`docs/development_plan/mvp_development_sequence_and_sprints.md`](development_plan/mvp_development_sequence_and_sprints.md)

**開發順序概覽**:
1. **Sprint 1-2**: 基礎設施建立 (用戶認證、審計日誌)
2. **Sprint 3-4**: 核心業務模組 (數據輸入、流程引擎)
3. **Sprint 5-6**: 業務功能模組 (審核系統、通知)
4. **Sprint 7-8**: 輸出服務模組 (報告產生器、客戶入口)

### 4.2 任務分解與估時

**詳細任務分解請參考**:
- [`docs/development_plan/task_breakdown_and_estimation.md`](development_plan/task_breakdown_and_estimation.md)

**估時方法**:
- 採用理想人天 (Ideal Person-Days) 作為估時單位
- 使用三點估算法：樂觀、最可能、悲觀估時
- 期望估時公式：(O + 4M + P) / 6

### 4.3 版本控制策略

**分支策略**: 採用 GitHub Flow 簡化版本

**分支命名規範**:
```
main                    # 主分支，隨時可部署
feature/[task-id]-[description]  # 功能分支
hotfix/[issue-id]-[description]  # 緊急修復分支
release/[version]       # 發布分支
```

**Commit Message 規範**:
```
<type>(<scope>): <subject>

<body>

<footer>
```

**Type 類型**:
- `feat`: 新功能
- `fix`: 錯誤修復
- `docs`: 文檔更新
- `style`: 代碼格式調整
- `refactor`: 代碼重構
- `test`: 測試相關
- `chore`: 建置或輔助工具變動

**範例**:
```
feat(auth): add OAuth2 login functionality

Implement OAuth2 authentication with Google and Microsoft providers.
Includes user session management and role-based access control.

Closes #123
```

### 4.4 代碼審查 (Code Review) 流程

**審查標準**:
1. **功能性**: 代碼是否實現了預期功能
2. **可讀性**: 代碼是否清晰易懂
3. **效能**: 是否存在效能問題
4. **安全性**: 是否存在安全漏洞
5. **測試覆蓋**: 是否有適當的測試

**審查流程**:
1. 開發者建立 Pull Request
2. 自動化測試執行 (CI/CD)
3. 至少一位資深開發者審查
4. 解決所有審查意見
5. 合併到主分支

### 4.5 CI/CD 流程初步設想

**持續整合 (CI)**:
- 代碼提交觸發自動化測試
- 代碼品質檢查 (ESLint, SonarQube)
- 安全性掃描
- 建置驗證

**持續部署 (CD)**:
- 自動部署到測試環境
- 整合測試執行
- 手動批准後部署到生產環境

---

## 5. 編碼規範與風格指南

### 5.1 TypeScript/JavaScript 編碼規範

**基礎規範**: 採用 Airbnb TypeScript Style Guide

**ESLint 配置**:
```json
{
  "extends": [
    "@typescript-eslint/recommended",
    "airbnb-typescript",
    "prettier"
  ],
  "rules": {
    "@typescript-eslint/no-unused-vars": "error",
    "@typescript-eslint/explicit-function-return-type": "warn"
  }
}
```

**Prettier 配置**:
```json
{
  "semi": true,
  "trailingComma": "es5",
  "singleQuote": true,
  "printWidth": 80,
  "tabWidth": 2
}
```

### 5.2 命名約定

**遵循駝峰命名法 (camelCase)**:

**變數與函數**:
```typescript
// ✅ 正確
const userName = 'john_doe';
const calculateTotalAmount = (items: Item[]) => { ... };

// ❌ 錯誤
const user_name = 'john_doe';
const calculate_total_amount = (items: Item[]) => { ... };
```

**類別與介面**:
```typescript
// ✅ 正確
class UserService { ... }
interface ApiResponse { ... }

// ❌ 錯誤
class userService { ... }
interface apiResponse { ... }
```

**檔案命名**:
```
// ✅ 正確
userService.ts
apiClient.ts
reportGenerator.ts

// ❌ 錯誤 (Android Style 不能有點號)
user.service.ts
api.client.ts
```

**常數**:
```typescript
// ✅ 正確
const API_BASE_URL = 'https://api.example.com';
const MAX_RETRY_ATTEMPTS = 3;
```

### 5.3 註釋規範

**函數註釋**:
```typescript
/**
 * 計算檢驗報告的總金額
 * @param items - 檢驗項目清單
 * @param discountRate - 折扣率 (0-1)
 * @returns 計算後的總金額
 */
function calculateTotalAmount(items: Item[], discountRate: number): number {
  // 實作邏輯
}
```

**複雜邏輯註釋**:
```typescript
// 根據檢驗項目類型計算不同的費率
// 標準檢驗: 基礎費率
// 緊急檢驗: 基礎費率 * 1.5
// 特殊檢驗: 基礎費率 * 2.0
const rate = getItemRate(item.type);
```

### 5.4 API 設計和實現指南

**API 規格參考**:
- [`docs/api_specification.yaml`](api_specification.yaml) - 完整 API 規格定義

**RESTful API 設計原則**:
```
GET    /api/v1/reports           # 取得報告清單
GET    /api/v1/reports/:id       # 取得特定報告
POST   /api/v1/reports           # 建立新報告
PUT    /api/v1/reports/:id       # 更新報告
DELETE /api/v1/reports/:id       # 刪除報告
```

**回應格式標準**:
```typescript
// 成功回應
{
  "success": true,
  "data": { ... },
  "message": "操作成功"
}

// 錯誤回應
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "輸入資料驗證失敗",
    "details": [...]
  }
}
```

### 5.5 資料庫操作指南

**資料模型參考**:
- [`docs/master_data_model.md`](master_data_model.md) - 完整資料模型定義

**ORM 使用規範**:
```typescript
// ✅ 正確：使用 TypeORM 查詢建構器
const reports = await reportRepository
  .createQueryBuilder('report')
  .leftJoinAndSelect('report.reviewer', 'reviewer')
  .where('report.status = :status', { status: 'pending' })
  .getMany();

// ❌ 避免：原生 SQL (除非必要)
const reports = await reportRepository.query('SELECT * FROM reports WHERE status = $1', ['pending']);
```

**資料庫遷移**:
- 所有 Schema 變更必須透過遷移檔案
- 遷移檔案必須包含向上和向下遷移
- 生產環境遷移需要備份和回滾計劃

---

## 6. 測試策略與實踐

### 6.1 測試層級與策略

**測試金字塔**:
```
    /\
   /  \     E2E Tests (10%)
  /____\    
 /      \   Integration Tests (20%)
/________\  Unit Tests (70%)
```

**單元測試**:
- 覆蓋率目標：80% 以上
- 測試框架：Jest + React Testing Library
- 所有業務邏輯函數必須有單元測試

**整合測試**:
- API 端點測試
- 資料庫操作測試
- 外部服務整合測試

**端到端測試**:
- 關鍵用戶流程測試
- 使用 Cypress 或 Playwright

### 6.2 測試框架和工具

**後端測試**:
```typescript
// Jest 單元測試範例
describe('ReportService', () => {
  it('should calculate total amount correctly', () => {
    const service = new ReportService();
    const result = service.calculateTotal([
      { amount: 100, quantity: 2 },
      { amount: 50, quantity: 1 }
    ]);
    expect(result).toBe(250);
  });
});
```

**前端測試**:
```typescript
// React Testing Library 範例
import { render, screen } from '@testing-library/react';
import ReportList from './ReportList';

test('renders report list', () => {
  render(<ReportList reports={mockReports} />);
  expect(screen.getByText('檢驗報告清單')).toBeInTheDocument();
});
```

### 6.3 測試數據管理

**測試資料原則**:
- 使用工廠模式建立測試資料
- 每個測試案例使用獨立的測試資料
- 測試結束後清理測試資料

**Mock 資料範例**:
```typescript
const mockReport = {
  id: 'test-report-1',
  title: '測試檢驗報告',
  status: 'draft',
  createdAt: new Date('2025-01-01'),
  researcher: mockUser
};
```

### 6.4 缺陷管理流程

**缺陷分級**:
- **Critical**: 系統無法使用
- **High**: 核心功能受影響
- **Medium**: 一般功能問題
- **Low**: 介面或文字問題

**缺陷處理流程**:
1. 發現缺陷 → 建立 Issue
2. 分級與指派
3. 修復與測試
4. 驗證與關閉

---

## 7. 部署流程 (初步)

### 7.1 部署環境概覽

**環境層級**:
1. **開發環境** (Development): 本地開發與功能測試
2. **測試環境** (Staging): 整合測試與用戶驗收測試
3. **生產環境** (Production): 正式服務環境

### 7.2 部署步驟概述

**自動化部署流程**:
1. 代碼合併到主分支
2. 觸發 CI/CD Pipeline
3. 執行自動化測試
4. 建置 Docker 映像檔
5. 部署到測試環境
6. 執行整合測試
7. 手動批准後部署到生產環境

### 7.3 回滾策略

**回滾觸發條件**:
- 關鍵功能無法正常運作
- 效能嚴重下降
- 安全性問題

**回滾步驟**:
1. 立即停止新版本部署
2. 切換到前一個穩定版本
3. 驗證系統功能正常
4. 通知相關團隊
5. 分析問題原因

---

## 8. 安全開發指南

### 8.1 OWASP Top 10 防範

**注入攻擊防範**:
```typescript
// ✅ 正確：使用參數化查詢
const user = await userRepository.findOne({
  where: { email: userEmail }
});

// ❌ 錯誤：字串拼接
const query = `SELECT * FROM users WHERE email = '${userEmail}'`;
```

**認證與授權**:
- 使用強密碼政策
- 實施多因子認證 (MFA)
- JWT Token 設定適當的過期時間
- 實施角色基礎存取控制 (RBAC)

### 8.2 輸入驗證與輸出編碼

**輸入驗證**:
```typescript
import Joi from 'joi';

const reportSchema = Joi.object({
  title: Joi.string().min(1).max(100).required(),
  content: Joi.string().min(1).required(),
  type: Joi.string().valid('standard', 'urgent', 'special').required()
});
```

**輸出編碼**:
- HTML 輸出使用適當的編碼
- JSON 回應避免 XSS 攻擊
- 檔案下載驗證檔案類型

### 8.3 依賴庫安全管理

**安全實踐**:
- 定期更新依賴庫
- 使用 `npm audit` 檢查安全漏洞
- 避免使用已知有安全問題的套件
- 實施依賴庫許可證檢查

### 8.4 敏感數據處理

**敏感資料保護**:
- 密碼使用 bcrypt 雜湊
- 敏感資料加密儲存
- 日誌中避免記錄敏感資訊
- 實施資料遮罩機制

```typescript
// 密碼雜湊範例
import bcrypt from 'bcrypt';

const hashPassword = async (password: string): Promise<string> => {
  const saltRounds = 12;
  return await bcrypt.hash(password, saltRounds);
};
```

---

## 9. 溝通與協作

### 9.1 團隊溝通渠道

**主要溝通工具**:
- **即時通訊**: Slack / Microsoft Teams
- **視訊會議**: Zoom / Google Meet
- **專案管理**: Jira / GitHub Projects
- **文檔協作**: Confluence / Notion

### 9.2 會議安排

**定期會議**:
- **每日站會** (Daily Standup): 15 分鐘，同步進度與障礙
- **Sprint 規劃會議**: 每 2 週，規劃下個 Sprint 任務
- **Sprint 回顧會議**: 每 2 週，檢討改進點
- **代碼審查會議**: 每週，討論代碼品質議題

### 9.3 文檔管理與更新責任

**文檔維護原則**:
- 代碼變更時同步更新相關文檔
- 每個模組指定文檔維護負責人
- 定期檢查文檔的準確性和完整性

**文檔更新責任**:
- **API 文檔**: 後端開發者負責
- **UI 組件文檔**: 前端開發者負責
- **部署文檔**: DevOps 工程師負責
- **用戶手冊**: 產品經理與 QA 負責

---

## 10. 附錄

### 10.1 術語表

| 術語 | 定義 |
|------|------|
| MVP | Minimum Viable Product，最小可行產品 |
| SOT | Single Source of Truth，唯一真實來源 |
| MDD | Module Design Document，模組設計文件 |
| RBAC | Role-Based Access Control，角色基礎存取控制 |
| CI/CD | Continuous Integration/Continuous Deployment，持續整合/持續部署 |
| API | Application Programming Interface，應用程式介面 |
| ORM | Object-Relational Mapping，物件關聯對映 |

### 10.2 重要資源連結

**核心 SOT 文件**:
- [`docs/master_data_model.md`](master_data_model.md) - 資料模型 SOT
- [`docs/api_specification.yaml`](api_specification.yaml) - API 規格 SOT
- [`docs/environment_configs/development_env_sot.md`](environment_configs/development_env_sot.md) - 開發環境 SOT
- [`docs/environment_configs/staging_env_sot.md`](environment_configs/staging_env_sot.md) - 測試環境 SOT
- [`docs/environment_configs/production_env_sot.md`](environment_configs/production_env_sot.md) - 生產環境 SOT

**架構與設計文件**:
- [`docs/architecture/system_architecture.md`](architecture/system_architecture.md) - 系統架構設計
- [`docs/ui_prototypes/mvp_detailed_prototypes.md`](ui_prototypes/mvp_detailed_prototypes.md) - 界面原型

**開發計劃文件**:
- [`docs/development_plan/mvp_development_sequence_and_sprints.md`](development_plan/mvp_development_sequence_and_sprints.md) - 開發順序與迭代計劃
- [`docs/development_plan/task_breakdown_and_estimation.md`](development_plan/task_breakdown_and_estimation.md) - 任務分解與估時

**模組設計文件**:
- [`docs/mod/user_auth_mdd.md`](mod/user_auth_mdd.md) - 用戶認證模組
- [`docs/mod/data_input_mdd.md`](mod/data_input_mdd.md) - 數據輸入模組
- [`docs/mod/workflow_engine_mdd.md`](mod/workflow_engine_mdd.md) - 流程引擎模組
- [`docs/mod/review_system_mdd.md`](mod/review_system_mdd.md) - 審核系統模組
- [`docs/mod/report_generator_mdd.md`](mod/report_generator_mdd.md) - 報告產生器模組
- [`docs/mod/notification_mdd.md`](mod/notification_mdd.md) - 通知模組
- [`docs/mod/customer_portal_mdd.md`](mod/customer_portal_mdd.md) - 客戶入口模組
- [`docs/mod/audit_log_mdd.md`](mod/audit_log_mdd.md) - 審計日誌模組

**外部資源**:
- [TypeScript 官方文檔](https://www.typescriptlang.org/docs/)
- [React 官方文檔](https://react.dev/)
- [Node.js 官方文檔](https://nodejs.org/docs/)
- [PostgreSQL 官方文檔](https://www.postgresql.org/docs/)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)

---

**文檔維護說明**: 本指南應隨著專案發展持續更新，確保內容的準確性和實用性。任何重大變更都應通知全體開發團隊並更新相關培訓材料。