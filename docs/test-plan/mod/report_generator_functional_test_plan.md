# 報告產生器模組功能測試計劃

## 文件資訊
- **模組名稱**: ReportGeneratorModule (報告產生器模組)
- **文件版本**: v1.0
- **建立日期**: 2025/05/31
- **最後更新**: 2025/05/31
- **測試負責人**: QA 團隊
- **狀態**: 草稿
- **參考文件**: 
  - [`docs/mod/report_generator_mdd.md`](../../mod/report_generator_mdd.md) - 模組設計文件
  - [`docs/test-plan/overall_test_strategy.md`](../overall_test_strategy.md) - 總體測試策略
  - [`docs/user_flows/core_user_flows.md`](../../user_flows/core_user_flows.md) - 核心用戶流程
  - [`docs/mvp_definition.md`](../../mvp_definition.md) - MVP 定義

---

## 1. 引言

### 1.1 目的
本文件定義報告產生器模組的詳細功能測試計劃，確保：
- 報告樣板管理功能完整
- 動態內容填充正確
- PDF 生成品質良好
- 企業識別正確套用
- 批量處理功能穩定

### 1.2 範圍
本測試計劃涵蓋：
- **樣板管理測試**: 樣板設計、版本控制、預覽測試
- **內容生成測試**: 動態填充、圖表處理、表格生成
- **格式化測試**: 企業識別、佈局管理、樣式控制
- **輸出功能測試**: PDF 生成、品質檢查、批量處理
- **整合測試**: 與審核系統、客戶入口的整合

### 1.3 測試目標
- 功能覆蓋率達到 100%（基於 MDD 功能列表）
- 報告格式覆蓋率達到 100%（所有支援的報告類型）
- PDF 品質測試覆蓋率達到 100%（各種內容組合）
- 缺陷發現率 > 95%（在系統測試前發現）

---

## 2. 測試功能點列表

### 2.1 核心功能點
基於 [`docs/mod/report_generator_mdd.md`](../../mod/report_generator_mdd.md) 中定義的功能需求：

| 功能ID | 功能名稱 | 功能描述 | 優先級 | 測試類型 |
|--------|---------|---------|--------|---------|
| F001 | 報告樣板設計與配置 | 報告樣板的設計和配置 | 高 | 功能測試 |
| F002 | 樣板版本控制 | 樣板的版本管理 | 中 | 功能測試 |
| F003 | 樣板預覽與測試 | 樣板的預覽和測試功能 | 中 | 功能測試 |
| F004 | 樣板分類管理 | 樣板的分類和管理 | 低 | 功能測試 |
| F005 | 動態資料填充 | 動態數據的填充 | 高 | 功能測試 |
| F006 | 圖表和圖片處理 | 圖表和圖片的處理 | 中 | 功能測試 |
| F007 | 表格自動生成 | 表格的自動生成 | 高 | 功能測試 |
| F008 | 計算欄位處理 | 計算欄位的處理 | 中 | 功能測試 |
| F009 | 企業識別套用 | 企業識別的套用 | 高 | 功能測試 |
| F010 | 頁面佈局管理 | 頁面佈局的管理 | 中 | 功能測試 |
| F011 | 字體和樣式控制 | 字體和樣式的控制 | 中 | 功能測試 |
| F012 | 頁首頁尾處理 | 頁首頁尾的處理 | 中 | 功能測試 |
| F013 | PDF 生成與優化 | PDF 的生成和優化 | 高 | 功能測試 |
| F014 | 浮水印和安全設定 | 浮水印和安全設定 | 中 | 安全測試 |
| F015 | 批量報告生成 | 批量報告的生成 | 中 | 效能測試 |
| F016 | 報告品質檢查 | 報告品質的檢查 | 高 | 功能測試 |

### 2.2 報告類型測試
基於業務需求的不同報告類型：

| 報告類型 | 報告描述 | 主要內容 | 測試重點 |
|---------|---------|---------|---------|
| 微生物檢驗報告 | 微生物檢驗結果報告 | 檢驗數據、圖表、結論 | 數據準確性、圖表生成 |
| 化學檢驗報告 | 化學檢驗結果報告 | 化學分析數據、表格 | 表格格式、計算準確性 |
| 綜合檢驗報告 | 多項檢驗綜合報告 | 多種檢驗結果整合 | 內容整合、版面配置 |
| 品質證書 | 產品品質證書 | 合格證明、簽章 | 格式標準化、安全性 |

### 2.3 用戶故事對應
基於 [`docs/user_flows/core_user_flows.md`](../../user_flows/core_user_flows.md) 中相關的用戶故事：

| 用戶故事ID | 用戶故事描述 | 相關功能點 | 測試重點 |
|-----------|-------------|-----------|---------|
| US-004 | 審核人員簽核發送 | F013, F014, F016 | 報告生成、品質檢查 |
| US-005 | 客戶報告接收 | F009, F010, F013 | 企業識別、PDF 品質 |

---

## 3. 測試環境與配置

### 3.1 測試環境要求
參考 [`docs/environment_configs/staging_env_sot.md`](../../environment_configs/staging_env_sot.md)：

**硬體需求**:
- CPU: 4 vCPU (PDF 生成需要較高運算能力)
- Memory: 8GB RAM (大型報告生成需求)
- Storage: 50GB SSD (報告檔案存儲)

**軟體需求**:
- 作業系統: Ubuntu 20.04 LTS
- PDF 引擎: Puppeteer + Chrome Headless
- 圖表庫: Chart.js, D3.js
- 應用伺服器: Node.js 18+

### 3.2 測試配置
**環境配置**:
- 測試環境 URL: https://staging.hwayo.com
- PDF 生成服務: 獨立微服務
- 樣板存儲: 檔案系統或 S3

**測試資源**:
| 資源類型 | 資源描述 | 用途 |
|---------|---------|------|
| 樣板檔案 | 各種報告樣板 | 樣板功能測試 |
| 測試數據 | 各種檢驗數據 | 內容填充測試 |
| 企業資源 | Logo、字體、色彩 | 企業識別測試 |
| 參考報告 | 標準報告範例 | 品質比較基準 |

---

## 4. 測試數據準備

### 4.1 基礎測試數據
**檢驗數據**:
- 微生物檢驗數據: 菌落計數、鑑定結果
- 化學檢驗數據: 濃度、pH 值、重金屬含量
- 物理檢驗數據: 溫度、濕度、重量測量

**圖表數據**:
- 趨勢圖數據: 時間序列檢驗結果
- 柱狀圖數據: 不同項目比較數據
- 圓餅圖數據: 成分比例數據

**企業資源**:
- 公司 Logo: PNG、SVG 格式
- 企業字體: 標準字體檔案
- 色彩配置: 企業標準色彩代碼

### 4.2 測試數據管理
**數據準備方式**:
- 自動生成腳本: `scripts/test-data/report-test-data.sql`
- 樣板檔案庫: `templates/` 目錄下的各種樣板
- 測試資源庫: `assets/test/` 目錄下的測試資源

---

## 5. 測試案例

### 5.1 功能測試案例

#### 5.1.1 樣板管理功能測試

**測試案例 TC-REPORT-001**
- **測試標題**: 報告樣板載入和配置
- **測試目標**: 驗證系統能正確載入和配置報告樣板
- **前置條件**: 
  - 報告生成服務正常運行
  - 樣板檔案已準備
- **測試步驟**:
  1. 上傳新的報告樣板檔案
  2. 配置樣板參數和變數
  3. 設定樣板適用的檢驗類型
  4. 儲存樣板配置
  5. 測試樣板載入功能
- **測試數據**: 微生物檢驗報告樣板
- **預期結果**: 
  - 樣板檔案成功上傳
  - 配置參數正確保存
  - 樣板能正確載入使用
  - 樣板預覽功能正常
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]
- **備註**: 核心功能，必須通過

**測試案例 TC-REPORT-002**
- **測試標題**: 樣板版本控制測試
- **測試目標**: 驗證樣板版本控制功能的正確性
- **前置條件**: 
  - 已存在樣板的初始版本
  - 具有樣板管理權限
- **測試步驟**:
  1. 修改現有樣板內容
  2. 創建新版本並保存
  3. 檢查版本歷史記錄
  4. 測試版本回滾功能
  5. 驗證不同版本的差異
- **測試數據**: 化學檢驗報告樣板 v1.0 和 v1.1
- **預期結果**: 
  - 新版本正確創建
  - 版本歷史完整記錄
  - 版本回滾功能正常
  - 版本差異正確顯示
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]
- **備註**: 樣板管理重要功能

#### 5.1.2 動態內容填充測試

**測試案例 TC-REPORT-003**
- **測試標題**: 動態資料填充測試
- **測試目標**: 驗證檢驗數據能正確填充到報告樣板中
- **前置條件**: 
  - 報告樣板已配置
  - 檢驗數據已準備
- **測試步驟**:
  1. 選擇檢驗案例和對應樣板
  2. 觸發報告生成流程
  3. 檢查數據填充的準確性
  4. 驗證計算欄位的正確性
  5. 確認格式化的一致性
- **測試數據**: 完整的微生物檢驗數據集
- **預期結果**: 
  - 所有數據正確填充
  - 計算欄位結果準確
  - 日期時間格式正確
  - 數值格式符合要求
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]
- **備註**: 數據準確性關鍵功能

**測試案例 TC-REPORT-004**
- **測試標題**: 表格自動生成測試
- **測試目標**: 驗證系統能根據數據自動生成表格
- **前置條件**: 
  - 表格樣板已配置
  - 表格數據已準備
- **測試步驟**:
  1. 準備多行檢驗數據
  2. 觸發表格生成功能
  3. 檢查表格結構和內容
  4. 驗證表格格式和樣式
  5. 測試動態行數處理
- **測試數據**: 包含 20 筆檢驗項目的數據
- **預期結果**: 
  - 表格結構正確生成
  - 所有數據正確顯示
  - 表格樣式符合設計
  - 動態行數正確處理
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]
- **備註**: 表格是報告重要組成部分

#### 5.1.3 圖表生成功能測試

**測試案例 TC-REPORT-005**
- **測試標題**: 圖表生成和嵌入測試
- **測試目標**: 驗證系統能生成圖表並正確嵌入報告
- **前置條件**: 
  - 圖表配置已設定
  - 圖表數據已準備
- **測試步驟**:
  1. 準備趨勢圖數據
  2. 配置圖表參數和樣式
  3. 生成圖表並嵌入報告
  4. 檢查圖表品質和清晰度
  5. 驗證圖表與數據的一致性
- **測試數據**: 7 天連續檢驗結果數據
- **預期結果**: 
  - 圖表正確生成
  - 圖表清晰度良好
  - 數據與圖表一致
  - 圖表樣式符合設計
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]
- **備註**: 視覺化展示重要功能

#### 5.1.4 PDF 生成功能測試

**測試案例 TC-REPORT-006**
- **測試標題**: PDF 生成品質測試
- **測試目標**: 驗證生成的 PDF 報告品質和格式
- **前置條件**: 
  - 報告內容已準備完成
  - PDF 生成引擎正常運行
- **測試步驟**:
  1. 生成包含各種內容的完整報告
  2. 檢查 PDF 檔案大小和品質
  3. 驗證文字清晰度和字體
  4. 檢查圖片和圖表品質
  5. 測試 PDF 的可讀性和列印效果
- **測試數據**: 綜合檢驗報告（包含文字、表格、圖表）
- **預期結果**: 
  - PDF 檔案大小合理（< 5MB）
  - 文字清晰可讀
  - 圖表品質良好
  - 列印效果正常
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]
- **備註**: 最終輸出品質關鍵

**測試案例 TC-REPORT-007**
- **測試標題**: 企業識別套用測試
- **測試目標**: 驗證企業識別元素正確套用到報告中
- **前置條件**: 
  - 企業識別資源已配置
  - 報告樣板支援企業識別
- **測試步驟**:
  1. 配置企業 Logo 和色彩
  2. 設定企業字體和樣式
  3. 生成帶有企業識別的報告
  4. 檢查 Logo 位置和大小
  5. 驗證色彩和字體的一致性
- **測試數據**: 標準企業識別資源包
- **預期結果**: 
  - Logo 正確顯示在指定位置
  - 企業色彩正確套用
  - 字體樣式符合企業標準
  - 整體視覺效果專業
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]
- **備註**: 企業形象重要功能

#### 5.1.5 批量處理功能測試

**測試案例 TC-REPORT-008**
- **測試標題**: 批量報告生成測試
- **測試目標**: 驗證系統能穩定處理批量報告生成
- **前置條件**: 
  - 多個檢驗案例已準備
  - 系統資源充足
- **測試步驟**:
  1. 選擇 10 個檢驗案例
  2. 觸發批量報告生成
  3. 監控生成進度和狀態
  4. 檢查所有報告的完整性
  5. 驗證生成時間和效能
- **測試數據**: 10 個不同類型的檢驗案例
- **預期結果**: 
  - 所有報告成功生成
  - 生成時間在可接受範圍內（< 5 分鐘）
  - 報告內容完整準確
  - 系統穩定無錯誤
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]
- **備註**: 效能和穩定性測試

### 5.2 接口測試案例

#### 5.2.1 報告生成 API 測試

**測試案例 TC-REPORT-API-001**
- **測試標題**: POST /api/reports/generate 報告生成
- **測試目標**: 驗證報告生成 API 的功能
- **API 端點**: POST /api/reports/generate
- **請求參數**: 
  ```json
  {
    "inspectionId": "inspection_12345",
    "templateId": "microbiology_template_v1",
    "format": "pdf",
    "options": {
      "includeCharts": true,
      "includeWatermark": true,
      "quality": "high"
    }
  }
  ```
- **預期回應**: 
  ```json
  {
    "status": "success",
    "data": {
      "reportId": "report_12345",
      "inspectionId": "inspection_12345",
      "status": "generating",
      "estimatedTime": 120,
      "createdAt": "2025-05-31T10:00:00Z"
    }
  }
  ```
- **狀態碼**: 202
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]

**測試案例 TC-REPORT-API-002**
- **測試標題**: GET /api/reports/status 報告狀態查詢
- **測試目標**: 驗證報告生成狀態查詢 API
- **API 端點**: GET /api/reports/{reportId}/status
- **請求參數**: 
  ```
  reportId: report_12345
  ```
- **預期回應**: 
  ```json
  {
    "status": "success",
    "data": {
      "reportId": "report_12345",
      "status": "completed",
      "progress": 100,
      "downloadUrl": "/api/reports/report_12345/download",
      "fileSize": 2048576,
      "completedAt": "2025-05-31T10:02:00Z"
    }
  }
  ```
- **狀態碼**: 200
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]

#### 5.2.2 樣板管理 API 測試

**測試案例 TC-REPORT-API-003**
- **測試標題**: GET /api/templates 樣板列表獲取
- **測試目標**: 驗證樣板列表獲取 API
- **API 端點**: GET /api/templates?category=microbiology&status=active
- **請求參數**: 
  ```
  category: microbiology
  status: active
  ```
- **預期回應**: 
  ```json
  {
    "status": "success",
    "data": {
      "templates": [
        {
          "templateId": "microbiology_template_v1",
          "name": "微生物檢驗報告樣板",
          "category": "microbiology",
          "version": "1.0",
          "status": "active",
          "createdAt": "2025-05-01T00:00:00Z"
        }
      ],
      "total": 1
    }
  }
  ```
- **狀態碼**: 200
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]

### 5.3 整合測試案例

#### 5.3.1 與審核系統整合測試

**測試案例 TC-REPORT-INT-001**
- **測試標題**: 審核完成觸發報告生成
- **測試目標**: 驗證審核完成後能自動觸發報告生成
- **測試場景**: 審核簽核完成後自動生成報告
- **測試步驟**:
  1. 審核人員完成檢驗案例審核
  2. 主管完成簽核操作
  3. 系統自動觸發報告生成
  4. 檢查報告生成狀態
  5. 驗證報告內容完整性
- **預期結果**: 
  - 簽核完成後自動觸發報告生成
  - 報告生成狀態正確更新
  - 報告內容包含審核意見
  - 簽核資訊正確顯示
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]

#### 5.3.2 與客戶入口整合測試

**測試案例 TC-REPORT-INT-002**
- **測試標題**: 報告生成後客戶通知
- **測試目標**: 驗證報告生成完成後客戶能收到通知
- **測試場景**: 報告生成完成後通知客戶下載
- **測試步驟**:
  1. 報告生成完成
  2. 系統觸發客戶通知
  3. 客戶收到報告完成通知
  4. 客戶登入客戶入口查看報告
  5. 驗證報告下載功能
- **預期結果**: 
  - 客戶及時收到報告完成通知
  - 客戶入口正確顯示新報告
  - 報告下載功能正常
  - 報告內容完整準確
- **實際結果**: [執行時填寫]
- **測試狀態**: [Pass/Fail/Blocked/Skip]

---

## 6. 測試執行與缺陷管理

### 6.1 測試執行計劃
**執行順序**:
1. **單元測試**: 開發團隊執行報告生成邏輯測試
2. **功能測試**: QA 團隊執行報告功能測試
3. **接口測試**: 自動化執行 API 測試
4. **品質測試**: 專門的 PDF 品質測試
5. **整合測試**: QA + 開發團隊協作執行

**執行時程**:
| 測試階段 | 開始日期 | 結束日期 | 負責人 | 狀態 |
|---------|---------|---------|--------|------|
| 功能測試 | 待定 | 待定 | QA 團隊 | 待開始 |
| 接口測試 | 待定 | 待定 | 自動化測試 | 待開始 |
| 品質測試 | 待定 | 待定 | QA 團隊 | 待開始 |
| 整合測試 | 待定 | 待定 | QA + 開發 | 待開始 |

### 6.2 缺陷管理流程
**缺陷報告工具**: Jira
**缺陷分類標準**: 參考 [`docs/test-plan/overall_test_strategy.md`](../overall_test_strategy.md) 第 7.3 節

**缺陷優先級定義**:
- **P1 - 緊急**: 無法生成報告、PDF 損壞、數據錯誤
- **P2 - 高**: 格式錯誤、圖表異常、樣板問題
- **P3 - 中**: 樣式問題、效能緩慢
- **P4 - 低**: 介面美化、提示訊息優化

### 6.3 測試完成標準
**功能測試完成標準**:
- 所有 P1、P2 缺陷已修復並驗證
- 功能測試案例執行率 ≥ 95%
- 功能測試案例通過率 ≥ 90%

**品質測試完成標準**:
- PDF 品質測試 100% 通過
- 所有報告類型生成正常
- 企業識別正確套用

---

## 7. 自動化考量

### 7.1 自動化測試範圍
**適合自動化的測試**:
- 報告生成 API 測試 (100% 自動化)
- 數據填充準確性測試 (90% 自動化)
- 批量生成效能測試 (85% 自動化)
- 回歸測試案例 (80% 自動化)

**不適合自動化的測試**:
- PDF 視覺品質評估
- 企業識別視覺效果檢查
- 複雜的樣板設計測試

### 7.2 自動化工具和框架
**測試框架**: Jest + Supertest
**PDF 測試工具**: PDF-lib, PDF2pic
**視覺測試工具**: Puppeteer + 截圖比較
**CI/CD 整合**: GitHub Actions

### 7.3 自動化測試維護
**維護策略**:
- 樣板變更時更新測試腳本
- 定期更新參考報告基準
- 自動化測試結果集成到 CI/CD 流程

---

## 8. 風險與應對措施

### 8.1 測試風險識別
| 風險項目 | 風險等級 | 影響描述 | 應對措施 |
|---------|---------|---------|---------|
| PDF 生成服務不穩定 | 高 | 影響報告生成測試 | 準備備用 PDF 引擎 |
| 大型報告生成緩慢 | 中 | 影響效能測試 | 優化測試數據大小 |
| 樣板配置複雜 | 中 | 測試案例設計困難 | 建立標準樣板庫 |
| 企業資源版權問題 | 低 | 測試資源使用限制 | 使用測試專用資源 |

### 8.2 依賴性風險
**外部依賴**:
- PDF 生成引擎: Puppeteer/Chrome 穩定