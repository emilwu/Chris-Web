# 用戶認證模組功能測試計劃

## 文件資訊
- **模組名稱**: UserAuthModule (用戶認證與權限管理模組)
- **文件版本**: v1.0
- **建立日期**: 2025/05/31
- **最後更新**: 2025/05/31
- **測試負責人**: QA 團隊
- **狀態**: 草稿
- **參考文件**: 
  - [`docs/mod/user_auth_mdd.md`](../../mod/user_auth_mdd.md) - 模組設計文件
  - [`docs/test-plan/overall_test_strategy.md`](../overall_test_strategy.md) - 總體測試策略
  - [`docs/user_flows/core_user_flows.md`](../../user_flows/core_user_flows.md) - 核心用戶流程
  - [`docs/mvp_definition.md`](../../mvp_definition.md) - MVP 定義

---

## 1. 引言

### 1.1 目的
本文件定義用戶認證與權限管理模組的詳細功能測試計劃，確保：
- 用戶認證功能安全可靠
- 權限控制機制正確實作
- 會話管理符合安全要求
- 異常情況得到適當處理

### 1.2 範圍
本測試計劃涵蓋：
- **認證功能測試**: 登入、登出、Token 管理
- **權限控制測試**: 角色權限、資源存取控制
- **安全功能測試**: 密碼策略、會話安全、失敗鎖定
- **接口測試**: 認證相關 API 接口
- **整合測試**: 與其他模組的認證整合

### 1.3 測試目標
- 功能覆蓋率達到 100%（基於 MDD 功能列表）
- 安全測試覆蓋率達到 100%（基於 OWASP 標準）
- 接口測試覆蓋率達到 100%（基於 API 規格）
- 缺陷發現率 > 95%（在系統測試前發現）

---

## 2. 測試功能點列表

### 2.1 核心功能點
基於 [`docs/mod/user_auth_mdd.md`](../../mod/user_auth_mdd.md) 中定義的功能需求：

| 功能ID | 功能名稱 | 功能描述 | 優先級 | 測試類型 |
|--------|---------|---------|--------|---------|
| F001 | 用戶登入驗證 | 驗證用戶憑證並生成會話 | 高 | 功能測試 |
| F002 | 用戶登出處理 | 清除會話並撤銷 Token | 高 | 功能測試 |
| F003 | JWT Token 管理 | Token 生成、驗證、刷新 | 高 | 功能測試 |
| F004 | 會話狀態管理 | 會話創建、維護、過期處理 | 高 | 功能測試 |
| F005 | 密碼驗證與加密 | 密碼強度驗證和安全存儲 | 高 | 安全測試 |
| F006 | 角色定義與管理 | 角色創建、修改、刪除 | 中 | 功能測試 |
| F007 | 權限分配與檢查 | 權限分配和存取控制 | 高 | 功能測試 |
| F008 | 資源存取控制 | 基於角色的資源存取 | 高 | 功能測試 |
| F009 | 權限中介層實現 | 請求攔截和權限驗證 | 高 | 接口測試 |
| F010 | 密碼強度驗證 | 密碼複雜度規則檢查 | 中 | 功能測試 |
| F011 | 登入失敗鎖定 | 多次失敗後帳號鎖定 | 高 | 安全測試 |
| F012 | 會話超時處理 | 自動會話過期和清理 | 中 | 功能測試 |
| F013 | 安全日誌記錄 | 認證相關操作記錄 | 中 | 功能測試 |

### 2.2 用戶故事對應
基於 [`docs/user_flows/core_user_flows.md`](../../user_flows/core_user_flows.md) 中相關的用戶故事：

| 用戶故事ID | 用戶故事描述 | 相關功能點 | 測試重點 |
|-----------|-------------|-----------|---------|
| US-001 | 研究員登入系統進行資料輸入 | F001, F003, F007 | 登入流程、權限驗證 |
| US-003 | 審核人員登入系統進行審核 | F001, F003, F008 | 角色權限、資源存取 |
| 通用 | 用戶安全登出系統 | F002, F004 | 會話清理、Token 撤銷 |

---

## 3. 測試環境與配置

### 3.1 測試環境要求
參考 [`docs/environment_configs/staging_env_sot.md`](../../environment_configs/staging_env_sot.md)：

**硬體需求**:
- CPU: 2 vCPU
- Memory: 4GB RAM
- Storage: 20GB SSD

**軟體需求**:
- 作業系統: Ubuntu 20.04 LTS
- 資料庫: PostgreSQL 13+
- 應用伺服器: Node.js 18+
- Redis: 6.0+ (會話存儲)

### 3.2 測試配置
**環境配置**:
- 測試環境 URL: https://staging.hwayo.com
- 資料庫連接: PostgreSQL staging instance
- Redis 連接: Redis staging cluster

**測試帳號**:
| 角色 | 帳號 | 權限範圍 | 用途 |
|------|------|---------|------|
| 研究員 | researcher_test@hwayo.com | 數據輸入權限 | 基本用戶功能測試 |
| 審核人員 | reviewer_test@hwayo.com | 審核權限 | 審核流程測試 |
| 管理員 | admin_test@hwayo.com | 系統管理權限 | 管理功能測試 |
| 客戶 | customer_test@hwayo.com | 報告查看權限 | 客戶功能測試 |

---

## 4. 測試數據準備

### 4.1 基礎測試數據
**正常測試數據**:
- 有效用戶帳號: 各角色測試帳號
- 有效密碼: 符合密碼策略的密碼
- 有效 Token: 正常生成的 JWT Token

**邊界測試數據**:
- 最小密碼長度: 8 字符
- 最大密碼長度: 128 字符
- 會話最短時間: 1 分鐘
- 會話最長時間: 8 小時

**異常測試數據**:
- 無效帳號: 不存在的用戶名
- 無效密碼: 錯誤密碼、弱密碼
- 過期 Token: 已過期的 JWT Token
- 惡意輸入: SQL 注入、XSS 攻擊字符

### 4.2 測試數據管理
**數據準備方式**:
- 自動生成腳本: `scripts/test-data/auth-test-data.sql`
- 手動準備清單: 測試帳號創建步驟
- 數據重置方法: 每次測試前重置測試用戶狀態

---

## 5. 測試案例

### 5.1 功能測試案例

#### 5.1.1 用戶登入功能測試

**測試案例 TC-AUTH-001**
- **測試標題**: 有效憑證登入成功
- **測試目標**: 驗證用戶使用正確憑證能夠成功登入
- **前置條件**: 
  - 測試環境正常運行
  - 測試用戶帳號存在且未被鎖定
- **測試步驟**:
  1. 訪問登入頁面
  2. 輸入有效的用戶名和密碼
  3. 點擊登入按鈕
- **測試數據**: researcher_test@hwayo.com / ValidPassword123!
- **預期結果**: 
  - 登入成功，跳轉到用戶工作台
  - 生成有效的 JWT Token
  - 記錄成功登入日誌
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]
- **備註**: 核心功能，必須通過

**測試案例 TC-AUTH-002**
- **測試標題**: 無效憑證登入失敗
- **測試目標**: 驗證用戶使用錯誤憑證無法登入
- **前置條件**: 
  - 測試環境正常運行
  - 測試用戶帳號存在
- **測試步驟**:
  1. 訪問登入頁面
  2. 輸入有效用戶名和錯誤密碼
  3. 點擊登入按鈕
- **測試數據**: researcher_test@hwayo.com / WrongPassword
- **預期結果**: 
  - 登入失敗，顯示錯誤訊息
  - 不生成 Token
  - 記錄失敗登入日誌
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]
- **備註**: 安全性關鍵測試

**測試案例 TC-AUTH-003**
- **測試標題**: 帳號鎖定機制測試
- **測試目標**: 驗證多次登入失敗後帳號被鎖定
- **前置條件**: 
  - 測試環境正常運行
  - 測試用戶帳號存在且未被鎖定
- **測試步驟**:
  1. 連續 5 次使用錯誤密碼嘗試登入
  2. 第 6 次使用正確密碼嘗試登入
- **測試數據**: researcher_test@hwayo.com / 錯誤密碼 x5, 正確密碼 x1
- **預期結果**: 
  - 前 5 次登入失敗
  - 第 6 次登入被拒絕，提示帳號已鎖定
  - 記錄帳號鎖定日誌
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]
- **備註**: 安全防護機制

#### 5.1.2 用戶登出功能測試

**測試案例 TC-AUTH-004**
- **測試標題**: 正常登出功能
- **測試目標**: 驗證用戶能夠正常登出並清除會話
- **前置條件**: 
  - 用戶已成功登入系統
  - 存在有效的會話和 Token
- **測試步驟**:
  1. 在已登入狀態下點擊登出按鈕
  2. 確認登出操作
  3. 嘗試訪問需要認證的頁面
- **測試數據**: 已登入的測試用戶
- **預期結果**: 
  - 成功登出，跳轉到登入頁面
  - Token 被撤銷
  - 無法訪問需要認證的頁面
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]
- **備註**: 會話安全關鍵功能

#### 5.1.3 權限控制功能測試

**測試案例 TC-AUTH-005**
- **測試標題**: 角色權限驗證
- **測試目標**: 驗證不同角色用戶只能訪問對應權限的資源
- **前置條件**: 
  - 不同角色的測試用戶已登入
  - 系統中存在不同權限級別的資源
- **測試步驟**:
  1. 研究員用戶嘗試訪問審核功能
  2. 審核人員用戶嘗試訪問管理功能
  3. 管理員用戶訪問所有功能
- **測試數據**: 各角色測試用戶
- **預期結果**: 
  - 研究員無法訪問審核功能，返回 403 錯誤
  - 審核人員無法訪問管理功能，返回 403 錯誤
  - 管理員可以訪問所有功能
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]
- **備註**: 權限控制核心功能

### 5.2 接口測試案例

#### 5.2.1 登入 API 測試

**測試案例 TC-AUTH-API-001**
- **測試標題**: POST /api/auth/login 正常登入
- **測試目標**: 驗證登入 API 在正常參數下的回應
- **API 端點**: POST /api/auth/login
- **請求參數**: 
  ```json
  {
    "email": "researcher_test@hwayo.com",
    "password": "ValidPassword123!"
  }
  ```
- **預期回應**: 
  ```json
  {
    "status": "success",
    "data": {
      "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
      "user": {
        "id": "user_id",
        "email": "researcher_test@hwayo.com",
        "role": "researcher",
        "permissions": ["data_input", "report_view"]
      },
      "expiresIn": 28800
    }
  }
  ```
- **狀態碼**: 200
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]

**測試案例 TC-AUTH-API-002**
- **測試標題**: POST /api/auth/login 無效憑證
- **測試目標**: 驗證登入 API 對無效憑證的錯誤處理
- **API 端點**: POST /api/auth/login
- **請求參數**: 
  ```json
  {
    "email": "researcher_test@hwayo.com",
    "password": "WrongPassword"
  }
  ```
- **預期回應**: 
  ```json
  {
    "status": "error",
    "message": "Invalid credentials",
    "code": "AUTH_INVALID_CREDENTIALS"
  }
  ```
- **狀態碼**: 401
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]

#### 5.2.2 Token 驗證 API 測試

**測試案例 TC-AUTH-API-003**
- **測試標題**: GET /api/auth/verify 有效 Token 驗證
- **測試目標**: 驗證 Token 驗證 API 的正常功能
- **API 端點**: GET /api/auth/verify
- **請求標頭**: 
  ```
  Authorization: Bearer [valid_jwt_token]
  ```
- **預期回應**: 
  ```json
  {
    "status": "success",
    "data": {
      "valid": true,
      "user": {
        "id": "user_id",
        "email": "researcher_test@hwayo.com",
        "role": "researcher"
      }
    }
  }
  ```
- **狀態碼**: 200
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]

### 5.3 整合測試案例

#### 5.3.1 認證與其他模組整合測試

**測試案例 TC-AUTH-INT-001**
- **測試標題**: 認證模組與數據輸入模組整合
- **測試目標**: 驗證認證後的用戶能夠正常使用數據輸入功能
- **測試場景**: 研究員登入後進行數據輸入操作
- **測試步驟**:
  1. 研究員用戶登入系統
  2. 訪問數據輸入頁面
  3. 填寫並提交實驗數據
  4. 驗證數據保存和權限檢查
- **預期結果**: 
  - 登入成功並獲得有效 Token
  - 能夠訪問數據輸入頁面
  - 數據提交成功並記錄操作者資訊
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]

**測試案例 TC-AUTH-INT-002**
- **測試標題**: 認證模組與審核系統整合
- **測試目標**: 驗證審核人員的權限控制和審核操作
- **測試場景**: 審核人員登入後進行審核操作
- **測試步驟**:
  1. 審核人員用戶登入系統
  2. 訪問待審核項目列表
  3. 查看具體審核項目詳情
  4. 執行審核操作（核准/退回）
- **預期結果**: 
  - 審核人員能夠訪問審核功能
  - 能夠查看分配給自己的審核項目
  - 審核操作成功並記錄審核人資訊
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]

---

## 6. 測試執行與缺陷管理

### 6.1 測試執行計劃
**執行順序**:
1. **單元測試**: 開發團隊執行認證邏輯單元測試
2. **功能測試**: QA 團隊執行認證功能測試
3. **接口測試**: 自動化執行 API 測試
4. **安全測試**: 專門的安全測試
5. **整合測試**: QA + 開發團隊協作執行

**執行時程**:
| 測試階段 | 開始日期 | 結束日期 | 負責人 | 狀態 |
|---------|---------|---------|--------|------|
| 功能測試 | 待定 | 待定 | QA 團隊 | 待開始 |
| 接口測試 | 待定 | 待定 | 自動化測試 | 待開始 |
| 安全測試 | 待定 | 待定 | 安全團隊 | 待開始 |
| 整合測試 | 待定 | 待定 | QA + 開發 | 待開始 |

### 6.2 缺陷管理流程
**缺陷報告工具**: Jira
**缺陷分類標準**: 參考 [`docs/test-plan/overall_test_strategy.md`](../overall_test_strategy.md) 第 7.3 節

**缺陷優先級定義**:
- **P1 - 緊急**: 無法登入、系統安全漏洞
- **P2 - 高**: 權限控制失效、Token 管理問題
- **P3 - 中**: 登出異常、會話管理問題
- **P4 - 低**: 介面問題、提示訊息問題

### 6.3 測試完成標準
**功能測試完成標準**:
- 所有 P1、P2 缺陷已修復並驗證
- 功能測試案例執行率 ≥ 95%
- 功能測試案例通過率 ≥ 90%

**安全測試完成標準**:
- 通過 OWASP Top 10 安全檢查
- 無高危安全漏洞
- 認證機制符合企業安全標準

---

## 7. 自動化考量

### 7.1 自動化測試範圍
**適合自動化的測試**:
- 登入/登出 API 測試 (100% 自動化)
- Token 驗證測試 (100% 自動化)
- 權限檢查測試 (90% 自動化)
- 回歸測試案例 (80% 自動化)

**不適合自動化的測試**:
- 帳號鎖定解鎖流程（需要時間等待）
- 複雜的權限邊界測試
- 安全滲透測試

### 7.2 自動化工具和框架
**測試框架**: Jest + Supertest
**API 測試工具**: Postman + Newman
**安全測試工具**: OWASP ZAP
**CI/CD 整合**: GitHub Actions

### 7.3 自動化測試維護
**維護策略**:
- 每次 API 變更後更新測試腳本
- 定期更新測試數據和憑證
- 自動化測試結果集成到 CI/CD 流程

---

## 8. 風險與應對措施

### 8.1 測試風險識別
| 風險項目 | 風險等級 | 影響描述 | 應對措施 |
|---------|---------|---------|---------|
| 測試環境不穩定 | 中 | 影響測試執行進度 | 準備備用測試環境 |
| 安全測試工具限制 | 中 | 無法發現所有安全問題 | 結合手動滲透測試 |
| 測試數據洩露 | 高 | 測試憑證被惡意使用 | 定期更換測試憑證 |
| 權限配置複雜 | 中 | 測試案例設計困難 | 詳細記錄權限矩陣 |

### 8.2 依賴性風險
**外部依賴**:
- Redis 服務: 會話存儲依賴，需確保服務穩定
- PostgreSQL: 用戶數據存儲，需要數據備份

**內部依賴**:
- 日誌模組: 認證日誌記錄依賴
- 通知模組: 安全事件通知依賴

---

## 9. 測試報告與追蹤

### 9.1 測試進度追蹤
**追蹤指標**:
- 測試案例執行進度: 已執行/總數
- 缺陷發現和修復進度: 開放/已修復
- 安全測試覆蓋率: 已測試/總安全點

### 9.2 測試報告格式
**日報告**: 每日認證功能測試執行狀況
**週報告**: 週度測試進度和安全問題總結
**最終報告**: 認證模組測試完成總結和安全評估

---

## 10. 附錄

### 10.1 測試案例執行記錄表
| 案例ID | 執行日期 | 執行人 | 結果 | 備註 |
|--------|---------|--------|------|------|
| TC-AUTH-001 | 待執行 | 待分配 | 待執行 | 核心登入功能 |
| TC-AUTH-002 | 待執行 | 待分配 | 待執行 | 安全性測試 |
| TC-AUTH-003 | 待執行 | 待分配 | 待執行 | 帳號鎖定機制 |

### 10.2 缺陷追蹤記錄表
| 缺陷ID | 發現日期 | 嚴重性 | 狀態 | 修復日期 | 驗證結果 |
|--------|---------|--------|------|---------|---------|
| 待發現 | - | - | - | - | - |

### 10.3 測試環境配置清單
**環境檢查清單**:
- [ ] 測試環境可正常訪問
- [ ] PostgreSQL 數據庫連接正常
- [ ] Redis 服務運行正常
- [ ] 測試用戶帳號已創建
- [ ] JWT 密鑰配置正確
- [ ] 安全測試工具已安裝配置

---

## 文件版本歷史
| 版本 | 日期 | 修改內容 | 修改人 |
|------|------|---------|--------|
| v1.0 | 2025/05/31 | 初始版本建立 | Roo (Architect) |

## 審核和批准
| 角色 | 姓名 | 簽名 | 日期 |
|------|------|------|------|
| 測試負責人 | 待定 | 待簽名 | 待定 |
| QA 主管 | 待定 | 待簽名 | 待定 |
| 專案經理 | 待定 | 待簽名 | 待定 |