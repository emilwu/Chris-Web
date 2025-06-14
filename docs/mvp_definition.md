# MVP 定義文件 (MVP Definition Document)

## 文件資訊
- **專案名稱**: Hwayo 檢驗流程線上化系統 MVP
- **文件版本**: v1.0
- **建立日期**: 2025/05/30
- **最後更新**: 2025/05/30

## 1. MVP 概述

### 1.1 MVP 目標
將 Hwayo 公司的紙本檢驗流程轉換為線上化系統，專注於內部流程數位化，確保核心檢驗流程的完整性和品質。

### 1.2 MVP 核心價值
- 消除紙本報表的手寫流程
- 建立標準化的線上審核機制
- 提供客戶線上報告接收功能
- 實現檢驗資料的集中管理

## 2. MVP 範圍定義

### 2.1 包含功能 (In Scope)

#### 2.1.1 用戶認證與權限管理
- OAuth2 或 SSO 登入機制
- 角色基礎的權限控制 (研究員、審核人員、管理員、客戶)
- 基本的帳號管理功能

#### 2.1.2 實驗數據輸入介面
- 自訂欄位表單系統
- 支援數值、文字、圖表上傳
- 基本的資料驗證機制

#### 2.1.3 流程引擎 (Workflow Engine)
- 任務自動分派：研究員提交 → 審核人員
- 狀態管理：草稿 / 審核中 / 已核准 / 已退回
- 基本的流程追蹤功能

#### 2.1.4 報告產生器
- 基本樣板引擎
- 公司企業識別套用
- PDF 匯出功能 (Word 匯出為 Phase 2)

#### 2.1.5 審核與簽核系統
- 意見註記功能
- 基本的差異檢查
- 簽核記錄與時間戳記

#### 2.1.6 通知與提醒
- Email 通知機制
- 基本的到期提醒功能

#### 2.1.7 客戶入口
- 安全連結報告下載
- 基本的歷史查詢功能

#### 2.1.8 審計與日誌
- 基本操作記錄
- 報表版本管理

### 2.2 不包含功能 (Out of Scope)

#### 2.2.1 客戶送件線上化
- 線上需求表單
- FedEx API 整合
- 樣本追蹤系統

#### 2.2.2 進階通知功能
- SMS 通知
- Line/Teams/Slack 整合

#### 2.2.3 AI 智能化功能
- AI 報價建議
- 檢驗方式推薦
- 異常值檢測

#### 2.2.4 進階功能
- 多語系支援
- 手機端原生 App
- 進階分析報表

## 3. 目標用戶畫像

### 3.1 研究員 (Primary User)
- **背景**: 具備專業檢驗知識，熟悉傳統紙本流程
- **技術水平**: 中等，能使用基本電腦操作
- **主要需求**: 快速、準確的資料輸入，清晰的流程指引
- **使用頻率**: 每日多次使用

### 3.2 審核人員 (Primary User)
- **背景**: 資深檢驗專家，負責品質控制
- **技術水平**: 中等，重視系統穩定性
- **主要需求**: 高效的審核工具，完整的追蹤記錄
- **使用頻率**: 每日使用

### 3.3 管理員 (Secondary User)
- **背景**: IT 或管理背景，負責系統維護
- **技術水平**: 較高，能處理系統設定
- **主要需求**: 系統管理工具，權限控制
- **使用頻率**: 週期性使用

### 3.4 客戶 (End User)
- **背景**: 多樣化，技術水平不一
- **技術水平**: 基本到中等
- **主要需求**: 簡單易用的報告接收方式
- **使用頻率**: 按需使用

## 4. 核心用戶故事

### 4.1 研究員用戶故事

#### US-001: 資料輸入
**作為** 研究員  
**我希望** 能在線上表單中輸入實驗數據  
**以便** 取代手寫報表，提高準確性和效率  

**驗收標準**:
- 能建立新的檢驗案例
- 支援多種資料類型輸入 (數值、文字、檔案)
- 具備基本的資料驗證
- 能儲存草稿並稍後繼續編輯

#### US-002: 報告提交
**作為** 研究員  
**我希望** 能提交完成的報告給審核人員  
**以便** 進入正式的審核流程  

**驗收標準**:
- 能將草稿狀態變更為待審核
- 系統自動通知相關審核人員
- 提交後無法直接修改 (需退回才能修改)

### 4.2 審核人員用戶故事

#### US-003: 審核報告
**作為** 審核人員  
**我希望** 能檢視並審核研究員提交的報告  
**以便** 確保報告品質符合標準  

**驗收標準**:
- 能查看待審核的報告列表
- 能檢視報告內容和原始數據
- 能添加審核意見和建議
- 能核准或退回報告

#### US-004: 簽核發送
**作為** 審核人員  
**我希望** 能簽核通過的報告並發送給客戶  
**以便** 完成檢驗流程  

**驗收標準**:
- 能對報告進行數位簽核
- 系統記錄簽核時間和人員
- 能自動發送報告給客戶

### 4.3 客戶用戶故事

#### US-005: 報告接收
**作為** 客戶  
**我希望** 能線上接收和下載檢驗報告  
**以便** 快速取得檢驗結果  

**驗收標準**:
- 能透過安全連結存取報告
- 能下載 PDF 格式報告
- 能查看報告狀態和完成時間

## 5. MVP 成功標準

### 5.1 功能性標準
- 所有核心用戶故事均能正常執行
- 系統能處理完整的檢驗流程 (輸入 → 審核 → 發送)
- 報告產生功能正常運作

### 5.2 效能標準
- 系統回應時間 < 3 秒
- 支援同時 10 位用戶操作
- 系統可用性 > 95%

### 5.3 品質標準
- 零資料遺失
- 審核流程完整性 100%
- 用戶滿意度 > 80%

### 5.4 業務標準
- 內部用戶採用率 > 90%
- 報告產出時間縮短 > 25%
- 錯誤率降低 > 30%

## 6. MVP 限制與假設

### 6.1 技術限制
- 初期僅支援 PDF 報告匯出
- 基本的通知功能 (僅 Email)
- 簡化的權限管理

### 6.2 業務限制
- 僅處理內部流程，不包含客戶送件
- 保留部分人工流程作為備援

## 7. MVP 整體驗收標準

### 7.1 業務目標驗收標準

#### AC-MVP-001: 內部流程數位化
**Given** 系統已部署並配置完成
**When** 研究員、審核人員開始使用系統進行日常檢驗流程
**Then**
- 內部用戶採用率達到 90% 以上
- 紙本報表使用率降低至 10% 以下
- 所有檢驗案例都能透過系統完成完整流程

#### AC-MVP-002: 流程效率提升
**Given** 系統運行穩定且用戶已熟悉操作
**When** 比較系統上線前後的流程效率指標
**Then**
- 報告產出時間縮短 25% 以上
- 審核流程時間縮短 30% 以上
- 錯誤率降低 30% 以上

#### AC-MVP-003: 客戶服務改善
**Given** 客戶入口功能正常運作
**When** 客戶透過系統接收檢驗報告
**Then**
- 客戶線上接收報告比例達到 80% 以上
- 客戶滿意度達到 80% 以上
- 報告下載成功率達到 95% 以上

### 7.2 系統功能驗收標準

#### AC-MVP-004: 核心用戶故事完整性
**Given** 所有核心模組已開發完成
**When** 執行端到端測試流程
**Then**
- 所有 5 個核心用戶故事 (US-001 至 US-005) 均能正常執行
- 每個用戶故事的所有驗收標準均通過測試
- 異常處理流程能正確運作

#### AC-MVP-005: 系統整合完整性
**Given** 8 個核心模組均已部署
**When** 執行完整的檢驗流程 (從數據輸入到客戶接收)
**Then**
- 模組間數據傳遞無遺失
- 工作流程狀態轉換正確
- 通知機制正常運作
- 審計日誌完整記錄

### 7.3 效能與品質驗收標準

#### AC-MVP-006: 系統效能要求
**Given** 系統在生產環境運行
**When** 進行效能測試
**Then**
- 系統回應時間 < 3 秒 (95% 的請求)
- 支援同時 10 位用戶操作無效能衰減
- 系統可用性 > 95%
- 資料庫查詢效能符合預期

#### AC-MVP-007: 資料完整性與安全性
**Given** 系統處理實際檢驗資料
**When** 進行資料完整性和安全性檢查
**Then**
- 零資料遺失事件
- 所有敏感資料均已加密
- 用戶權限控制正確運作
- 審計日誌記錄完整且不可篡改

### 7.4 用戶體驗驗收標準

#### AC-MVP-008: 用戶滿意度
**Given** 系統已上線並運行一個月
**When** 進行用戶滿意度調查
**Then**
- 內部用戶 (研究員、審核人員) 滿意度 > 80%
- 客戶滿意度 > 80%
- 用戶培訓完成率 > 95%
- 系統使用問題回報 < 5 件/週

#### AC-MVP-009: 系統易用性
**Given** 新用戶首次使用系統
**When** 進行易用性測試
**Then**
- 新用戶能在 30 分鐘內完成基本操作培訓
- 核心功能操作步驟 ≤ 5 步
- 錯誤訊息清晰易懂
- 幫助文件覆蓋所有主要功能

### 7.5 合規性與稽核驗收標準

#### AC-MVP-010: 稽核要求滿足
**Given** 系統記錄所有操作活動
**When** 進行稽核檢查
**Then**
- 所有用戶操作均有完整日誌記錄
- 報告版本管理機制正常運作
- 數位簽核具有法律效力
- 資料保存期限符合法規要求

#### AC-MVP-011: 備份與災難恢復
**Given** 系統建立備份與災難恢復機制
**When** 進行災難恢復測試
**Then**
- 資料備份每日自動執行且可驗證
- 系統故障恢復時間 < 4 小時
- 資料恢復完整性 100%
- 業務連續性計劃可有效執行