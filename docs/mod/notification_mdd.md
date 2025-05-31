# 通知與提醒模組設計文件 (MDD)

## 文件資訊
- **模組名稱**: NotificationModule (通知與提醒模組)
- **文件版本**: v1.0
- **建立日期**: 2025/05/30
- **最後更新**: 2025/05/30
- **階段標記**: [MVP]

## 1. 模組概述

### 1.1 模組目標
提供統一的通知和提醒服務，支援多種通知通道，確保重要訊息及時傳達給相關用戶，提升系統的互動性和用戶體驗。

### 1.2 業務價值
- 確保重要訊息及時傳達
- 提升用戶參與度和響應速度
- 減少人工追蹤和提醒工作
- 支援個性化通知偏好設定
- 提供完整的通知歷史記錄

### 1.3 模組邊界
**包含功能**:
- Email 通知發送
- 系統內通知
- 排程提醒功能
- 通知模板管理
- 通知歷史追蹤

**不包含功能**:
- SMS 簡訊通知 (Phase 1)
- 即時通訊整合 (Phase 1)
- 推播通知 (Phase 2)

## 2. 功能需求

### 2.1 核心功能列表

#### 2.1.1 通知發送功能
- **F001**: Email 通知發送
- **F002**: 系統內通知
- **F003**: 批量通知發送
- **F004**: 通知狀態追蹤

#### 2.1.2 模板管理功能
- **F005**: 通知模板設計
- **F006**: 模板變數管理
- **F007**: 多語系模板支援
- **F008**: 模板預覽測試

#### 2.1.3 排程功能
- **F009**: 定時通知發送
- **F010**: 週期性提醒
- **F011**: 條件觸發通知
- **F012**: 提醒規則管理

#### 2.1.4 偏好設定功能
- **F013**: 用戶通知偏好
- **F014**: 通知頻率控制
- **F015**: 通知通道選擇
- **F016**: 免打擾時間設定

## 3. 技術設計

### 3.1 資料模型

#### 3.1.1 通知實體
```typescript
interface Notification {
  id: string;
  type: NotificationType;
  channel: NotificationChannel;
  recipientId: string;
  recipientEmail?: string;
  subject: string;
  content: string;
  templateId?: string;
  templateData?: Record<string, any>;
  status: NotificationStatus;
  priority: NotificationPriority;
  scheduledAt?: Date;
  sentAt?: Date;
  readAt?: Date;
  metadata: NotificationMetadata;
  createdAt: Date;
}

enum NotificationType {
  TASK_ASSIGNED = 'task_assigned',
  REVIEW_REQUIRED = 'review_required',
  REPORT_READY = 'report_ready',
  DEADLINE_REMINDER = 'deadline_reminder',
  SYSTEM_ALERT = 'system_alert'
}

enum NotificationChannel {
  EMAIL = 'email',
  IN_APP = 'in_app',
  SMS = 'sms'
}

enum NotificationStatus {
  PENDING = 'pending',
  SENT = 'sent',
  DELIVERED = 'delivered',
  READ = 'read',
  FAILED = 'failed'
}
```

#### 3.1.2 通知模板實體
**參考 SOT**: [`docs/master_data_model.md`](../master_data_model.md) - 6.1 notification_templates (通知模板表)

```typescript
interface NotificationTemplate {
  id: bigint;                    // 通知模板唯一識別碼 (BIGSERIAL)
  name: string;                  // 模板名稱 (VARCHAR(100), UNIQUE)
  type: string;                  // 通知類型 (VARCHAR(50))
  channel: string;               // 通知渠道 (VARCHAR(20))
  subjectTemplate: string;       // 主題模板 (VARCHAR(200))
  contentTemplate: string;       // 內容模板 (TEXT)
  variables: object;             // 變數定義 (JSONB)
  status: string;                // 模板狀態 (VARCHAR(20))
  createdBy: bigint;             // 建立者 ID (BIGINT, 參照 users.id)
  createdAt: Date;               // 建立時間 (TIMESTAMP WITH TIME ZONE)
  updatedAt: Date;               // 更新時間 (TIMESTAMP WITH TIME ZONE)
}

#### 3.1.3 通知實體
**參考 SOT**: [`docs/master_data_model.md`](../master_data_model.md) - 6.2 notifications (通知表)

```typescript
interface Notification {
  id: bigint;                    // 通知唯一識別碼 (BIGSERIAL)
  templateId?: bigint;           // 模板 ID (BIGINT, 參照 notification_templates.id)
  recipientId: bigint;           // 接收者 ID (BIGINT, 參照 users.id)
  channel: string;               // 通知渠道 (VARCHAR(20))
  subject: string;               // 通知主題 (VARCHAR(200))
  content: string;               // 通知內容 (TEXT)
  status: string;                // 通知狀態 (VARCHAR(20))
  priority: string;              // 優先級 (VARCHAR(20))
  metadata: object;              // 元數據 (JSONB)
  scheduledAt?: Date;            // 排程時間 (TIMESTAMP WITH TIME ZONE)
  sentAt?: Date;                 // 發送時間 (TIMESTAMP WITH TIME ZONE)
  deliveredAt?: Date;            // 送達時間 (TIMESTAMP WITH TIME ZONE)
  readAt?: Date;                 // 閱讀時間 (TIMESTAMP WITH TIME ZONE)
  retryCount: number;            // 重試次數 (INTEGER)
  errorMessage?: string;         // 錯誤訊息 (TEXT)
  createdAt: Date;               // 建立時間 (TIMESTAMP WITH TIME ZONE)
  updatedAt: Date;               // 更新時間 (TIMESTAMP WITH TIME ZONE)
}
```

### 3.2 核心服務設計

#### 3.2.1 通知服務
```typescript
class NotificationService {
  // 發送通知
  async sendNotification(notification: CreateNotificationRequest): Promise<Notification>;
  
  // 批量發送
  async sendBatchNotifications(notifications: CreateNotificationRequest[]): Promise<Notification[]>;
  
  // 排程通知
  async scheduleNotification(notification: CreateNotificationRequest, scheduledAt: Date): Promise<Notification>;
  
  // 取消通知
  async cancelNotification(notificationId: string): Promise<void>;
  
  // 標記已讀
  async markAsRead(notificationId: string, userId: string): Promise<void>;
}
```

#### 3.2.2 Email 服務
```typescript
class EmailService {
  private transporter: nodemailer.Transporter;
  
  async sendEmail(to: string, subject: string, content: string): Promise<EmailResult> {
    const mailOptions = {
      from: process.env.SMTP_FROM,
      to,
      subject,
      html: content
    };
    
    const result = await this.transporter.sendMail(mailOptions);
    return {
      messageId: result.messageId,
      accepted: result.accepted,
      rejected: result.rejected
    };
  }
}
```

### 3.3 通知模板系統

#### 3.3.1 模板範例
```handlebars
<!-- 任務分派通知模板 -->
<div style="font-family: Arial, sans-serif; max-width: 600px;">
  <h2>新的審核任務</h2>
  <p>親愛的 {{reviewerName}}，</p>
  
  <p>您有一個新的審核任務需要處理：</p>
  
  <div style="background: #f5f5f5; padding: 15px; margin: 15px 0;">
    <strong>任務詳情：</strong><br>
    檢驗項目: {{sampleName}}<br>
    提交人員: {{submitterName}}<br>
    提交時間: {{formatDate submittedAt 'YYYY-MM-DD HH:mm'}}<br>
    截止時間: {{formatDate dueDate 'YYYY-MM-DD HH:mm'}}
  </div>
  
  <p>
    <a href="{{reviewUrl}}" style="background: #007bff; color: white; padding: 10px 20px; text-decoration: none;">
      開始審核
    </a>
  </p>
  
  <p>謝謝！<br>Hwayo 檢驗系統</p>
</div>
```

## 4. API 接口設計

### 4.1 通知管理 API

**POST /api/notifications**
```typescript
interface CreateNotificationRequest {
  type: NotificationType;
  channel: NotificationChannel;
  recipientId: string;
  templateId?: string;
  templateData?: Record<string, any>;
  subject?: string;
  content?: string;
  priority?: NotificationPriority;
  scheduledAt?: Date;
}
```

**GET /api/notifications/my-notifications**
```typescript
interface MyNotificationsResponse {
  success: boolean;
  data: {
    notifications: Notification[];
    unreadCount: number;
    total: number;
  };
}
```

## 5. 部署配置

### 5.1 環境變數
```bash
# Email 配置
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASS=your-password
SMTP_FROM=noreply@hwayo.com

# 通知配置
NOTIFICATION_BATCH_SIZE=100
NOTIFICATION_RETRY_ATTEMPTS=3
NOTIFICATION_QUEUE_SIZE=10000
```

### 5.2 資料庫 Schema
```sql
-- 通知表
CREATE TABLE notifications (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  type VARCHAR(50) NOT NULL,
  channel VARCHAR(20) NOT NULL,
  recipient_id UUID NOT NULL,
  recipient_email VARCHAR(255),
  subject VARCHAR(500),
  content TEXT NOT NULL,
  template_id UUID,
  template_data JSONB,
  status VARCHAR(20) DEFAULT 'pending',
  priority INTEGER DEFAULT 5,
  scheduled_at TIMESTAMP,
  sent_at TIMESTAMP,
  read_at TIMESTAMP,
  metadata JSONB,
  created_at TIMESTAMP DEFAULT NOW()
);

-- 通知模板表
CREATE TABLE notification_templates (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  type VARCHAR(50) NOT NULL,
  channel VARCHAR(20) NOT NULL,
  subject VARCHAR(500),
  content TEXT NOT NULL,
  variables JSONB,
  is_active BOOLEAN DEFAULT true,
  created_by UUID NOT NULL,
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- 索引
CREATE INDEX idx_notifications_recipient ON notifications(recipient_id, status);
CREATE INDEX idx_notifications_scheduled ON notifications(scheduled_at) WHERE status = 'pending';
```

## 6. 模組接口定義

### 6.1 對外提供的接口

#### 6.1.1 發送通知接口
**接口名稱**: `POST /api/notifications`
**用途**: 接收來自其他模組的通知發送請求
**調用方**: 流程引擎、審核系統、報告產生器等所有模組

基於 [`docs/architecture/module_interaction_analysis.md`](../architecture/module_interaction_analysis.md) 中的接口 7：

```typescript
interface SendWorkflowNotificationRequest {
  type: 'task_assigned' | 'review_required' | 'report_ready' | 'deadline_reminder';
  recipientId: string;
  templateId: string;
  templateData: {
    workflowInstanceId: string;
    taskId?: string;
    reportId?: string;
    sampleName: string;
    dueDate?: Date;
    actionUrl: string;
    [key: string]: any;
  };
  priority: 'low' | 'medium' | 'high';
  scheduledAt?: Date;             // 可選的排程發送時間
}

interface SendNotificationResponse {
  success: boolean;
  data: {
    notificationId: string;
    status: 'queued' | 'sent' | 'failed';
    estimatedDeliveryTime?: Date;
  };
}
```

#### 6.1.2 通知狀態查詢接口
**接口名稱**: `GET /api/notifications/:notificationId/status`
**用途**: 查詢通知發送狀態
**調用方**: 前端介面、其他模組

```typescript
interface NotificationStatusResponse {
  success: boolean;
  data: {
    notificationId: string;
    status: 'queued' | 'sending' | 'sent' | 'failed' | 'delivered';
    sentAt?: Date;
    deliveredAt?: Date;
    failureReason?: string;
    retryCount: number;
  };
}
```

#### 6.1.3 批量通知接口
**接口名稱**: `POST /api/notifications/batch`
**用途**: 批量發送通知
**調用方**: 流程引擎、系統管理模組

```typescript
interface BatchNotificationRequest {
  notifications: Array<{
    recipientId: string;
    type: string;
    templateId: string;
    templateData: Record<string, any>;
    priority: 'low' | 'medium' | 'high';
  }>;
  batchOptions: {
    maxConcurrency: number;
    delayBetweenSends: number;
  };
}
```

### 6.2 依賴的外部接口

#### 6.2.1 用戶認證模組接口
**依賴接口**: `GET /api/auth/user/:userId`
**用途**: 獲取通知接收者的聯絡資訊和偏好設定
**調用場景**: 發送通知前獲取用戶資訊

```typescript
interface NotificationUserInfoRequest {
  userId: string;
  includeContactInfo: boolean;
  includePreferences: boolean;
}

interface NotificationUserInfoResponse {
  success: boolean;
  data: {
    id: string;
    username: string;
    email: string;
    phone?: string;
    notificationPreferences: {
      email: boolean;
      sms: boolean;
      inApp: boolean;
      preferredTime?: string;
    };
  };
}
```

#### 6.2.2 審計日誌模組接口
**依賴接口**: `POST /api/audit/log`
**用途**: 記錄所有通知發送操作的審計日誌
**調用頻率**: 每次通知發送、失敗、重試操作

```typescript
interface NotificationAuditLogRequest {
  eventType: 'system_event';
  category: 'notification';
  userId?: string;
  resource: 'notifications';
  action: 'send' | 'deliver' | 'fail' | 'retry';
  details: {
    notificationId: string;
    recipientId: string;
    notificationType: string;
    channel: 'email' | 'sms' | 'in_app';
    templateId: string;
    deliveryStatus: string;
    failureReason?: string;
    retryCount?: number;
  };
  result: 'success' | 'failure';
  severity: 'info' | 'warning' | 'error';
}
```

## 7. 依賴關係

### 7.1 直接依賴的核心模組
根據 [`docs/architecture/system_architecture.md`](../architecture/system_architecture.md) 中的模組依賴圖：

1. **用戶認證模組 (UserAuthModule)**
   - **依賴原因**: 獲取通知接收者的聯絡資訊和偏好設定
   - **交互接口**: `GET /api/auth/user/:userId`
   - **調用場景**: 發送通知前獲取用戶資訊
   - **依賴強度**: 強依賴（通知發送必需的用戶資訊）

2. **審計日誌模組 (AuditLogModule)**
   - **依賴原因**: 記錄所有通知操作的完整追蹤
   - **交互接口**: `POST /api/audit/log`
   - **調用場景**: 通知發送、失敗、重試等操作
   - **依賴強度**: 強依賴（合規要求）

### 7.2 被依賴的模組
本模組被以下模組依賴：

1. **流程引擎 (WorkflowEngineModule)**
   - **依賴接口**: `POST /api/notifications`
   - **依賴原因**: 發送工作流程相關通知（任務分派、狀態變更等）

2. **審核系統 (ReviewSystemModule)**
   - **依賴接口**: `POST /api/notifications`
   - **依賴原因**: 發送審核相關通知（任務分派、審核完成等）

3. **報告產生器 (ReportGeneratorModule)**
   - **依賴接口**: `POST /api/notifications`
   - **依賴原因**: 發送報告相關通知（報告完成、下載通知等）

4. **客戶入口 (CustomerPortalModule)**
   - **依賴接口**: `POST /api/notifications`
   - **依賴原因**: 發送客戶相關通知（報告可用、存取提醒等）

### 7.3 技術基礎設施依賴
- **Redis**: 通知佇列、模板快取、發送狀態追蹤
- **PostgreSQL**: 通知歷史、模板配置、用戶偏好儲存
- **Bull Queue**: 非同步通知處理和重試機制
- **nodemailer**: Email 發送服務
- **handlebars**: 通知模板渲染引擎

### 7.4 外部服務依賴
- **SMTP 服務**: Email 發送（如 SendGrid、AWS SES）
- **SMS 服務**: 簡訊發送（如 Twilio、AWS SNS）
- **推播服務**: 行動裝置推播通知
- **WebSocket 服務**: 即時通知推送

## 7. 驗收標準 (Acceptance Criteria)

### 7.1 通知發送功能驗收標準

#### AC-NOTIFICATION-001: Email 通知發送 (F001)
**Given** 系統需要發送 Email 通知
**When** 調用通知發送 API
**Then**
- Email 在 30 秒內發送完成
- 收件人地址正確驗證
- 郵件主題和內容正確渲染
- 支援 HTML 格式和純文字格式
- 發送狀態正確記錄
- 發送失敗時自動重試 (最多 3 次)
- 記錄發送日誌到審計系統

#### AC-NOTIFICATION-002: 系統內通知 (F002)
**Given** 用戶登入系統
**When** 查看系統內通知
**Then**
- 通知列表在 1 秒內載入完成
- 未讀通知正確標示
- 通知內容格式正確顯示
- 支援通知標記為已讀
- 通知數量統計準確
- 支援通知刪除功能

#### AC-NOTIFICATION-003: 批量通知發送 (F003)
**Given** 需要發送批量通知
**When** 執行批量發送
**Then**
- 支援同時發送最多 100 個通知
- 批量發送在 5 分鐘內完成
- 發送進度即時更新
- 部分失敗不影響其他通知
- 提供批量發送結果報告
- 支援發送速率限制

#### AC-NOTIFICATION-004: 通知狀態追蹤 (F004)
**Given** 通知已發送
**When** 查詢通知狀態
**Then**
- 狀態查詢回應時間 < 500ms
- 狀態資訊準確完整 (pending, sent, delivered, failed)
- 包含發送時間戳
- 失敗通知提供錯誤原因
- 支援狀態變更歷史查詢

### 7.2 模板管理功能驗收標準

#### AC-NOTIFICATION-005: 通知模板設計 (F005)
**Given** 管理員建立通知模板
**When** 模板配置生效
**Then**
- 模板語法驗證通過
- 支援 Handlebars 模板語法
- 變數綁定正確運作
- 模板預覽功能正常
- 模板版本控制有效
- 支援模板複製和匯入匯出

#### AC-NOTIFICATION-006: 模板變數管理 (F006)
**Given** 模板包含動態變數
**When** 渲染模板
**Then**
- 變數替換準確無誤
- 支援條件邏輯 (if/else)
- 支援迴圈結構 (each)
- 處理空值和未定義變數
- 支援日期時間格式化
- 支援數值格式化

#### AC-NOTIFICATION-007: 模板預覽測試 (F008)
**Given** 管理員編輯模板
**When** 使用預覽功能
**Then**
- 預覽在 2 秒內生成
- 預覽結果與實際發送一致
- 支援測試資料輸入
- 可預覽不同通知通道效果
- 錯誤提示清晰具體

### 7.3 排程功能驗收標準

#### AC-NOTIFICATION-008: 定時通知發送 (F009)
**Given** 設定定時通知
**When** 到達排程時間
**Then**
- 通知準時發送 (誤差 < 1 分鐘)
- 排程任務正確執行
- 支援一次性和週期性排程
- 排程狀態正確更新
- 支援排程取消和修改

#### AC-NOTIFICATION-009: 週期性提醒 (F010)
**Given** 設定週期性提醒
**When** 週期觸發
**Then**
- 週期計算準確 (每日、每週、每月)
- 提醒內容動態更新
- 支援提醒暫停和恢復
- 週期結束自動停止
- 提醒歷史完整記錄

#### AC-NOTIFICATION-010: 條件觸發通知 (F011)
**Given** 設定條件觸發規則
**When** 條件滿足
**Then**
- 條件評估準確可靠
- 觸發回應時間 < 5 秒
- 支援複雜條件邏輯
- 避免重複觸發
- 條件變更歷史可追蹤

### 7.4 用戶偏好功能驗收標準

#### AC-NOTIFICATION-011: 用戶通知偏好 (F013)
**Given** 用戶設定通知偏好
**When** 發送通知
**Then**
- 偏好設定正確套用
- 支援通知類型選擇
- 支援通知通道選擇
- 偏好變更即時生效
- 預設偏好合理設定

#### AC-NOTIFICATION-012: 通知頻率控制 (F014)
**Given** 設定通知頻率限制
**When** 頻繁觸發通知
**Then**
- 頻率限制正確執行
- 超過限制時合併通知
- 重要通知不受限制影響
- 頻率統計準確
- 支援不同類型不同頻率

#### AC-NOTIFICATION-013: 免打擾時間設定 (F016)
**Given** 用戶設定免打擾時間
**When** 在免打擾時間內
**Then**
- 非緊急通知延遲發送
- 緊急通知正常發送
- 延遲通知在適當時間發送
- 免打擾狀態正確顯示
- 支援不同時區設定

### 7.5 整合功能驗收標準

#### AC-NOTIFICATION-014: 模組間整合
**Given** 其他模組請求發送通知
**When** 調用通知 API
**Then**
- API 回應時間 < 1 秒
- 請求格式驗證正確
- 支援同步和非同步發送
- 錯誤處理機制完善
- 整合文件清晰完整

#### AC-NOTIFICATION-015: 用戶資訊整合
**Given** 需要獲取用戶聯絡資訊
**When** 調用用戶認證模組
**Then**
- 用戶資訊獲取成功率 > 99%
- 聯絡資訊準確有效
- 偏好設定正確載入
- 快取機制有效運作
- 資料同步及時

### 7.6 效能與可靠性驗收標準

#### AC-NOTIFICATION-016: 系統效能要求
**Given** 系統處於正常負載狀態
**When** 執行通知操作
**Then**
- 單個通知發送時間 < 30 秒
- 批量通知處理速度 > 20 個/分鐘
- 系統支援同時 1000 個通知佇列
- 記憶體使用合理 (< 2GB)
- CPU 使用率 < 70%

#### AC-NOTIFICATION-017: 可靠性保證
**Given** 系統長時間運行
**When** 處理大量通知
**Then**
- 通知發送成功率 > 95%
- 系統無記憶體洩漏
- 佇列處理穩定可靠
- 失敗通知自動重試
- 系統故障自動恢復

#### AC-NOTIFICATION-018: 錯誤處理
**Given** 通知發送過程中發生錯誤
**When** 錯誤被檢測到
**Then**
- 錯誤被正確分類和記錄
- 提供明確的錯誤診斷資訊
- 支援手動重試機制
- 錯誤不影響其他通知
- 錯誤統計和報告完整

### 7.7 安全性與合規驗收標準

#### AC-NOTIFICATION-019: 資料安全
**Given** 通知包含敏感資料
**When** 進行安全檢查
**Then**
- 敏感資料傳輸加密
- 通知內容適當脫敏
- 存取權限嚴格控制
- 資料保存期限合規
- 符合隱私保護法規

#### AC-NOTIFICATION-020: 審計追蹤
**Given** 執行任何通知操作
**When** 操作完成
**Then**
- 所有操作完整記錄到審計日誌
- 日誌包含完整的上下文資訊
- 支援合規性報告生成
- 日誌查詢效能良好
- 日誌資料完整性保護

## 8. 結論

通知與提醒模組為系統提供了完整的訊息傳達機制，通過多通道支援和靈活的模板系統，能夠有效提升用戶體驗和系統互動性。