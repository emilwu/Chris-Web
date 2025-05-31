# 客戶入口模組設計文件 (MDD)

## 文件資訊
- **模組名稱**: CustomerPortalModule (客戶入口模組)
- **文件版本**: v1.0
- **建立日期**: 2025/05/30
- **最後更新**: 2025/05/30
- **階段標記**: [MVP]

## 1. 模組概述

### 1.1 模組目標
為客戶提供安全、便捷的報告存取入口，支援報告下載、歷史查詢和狀態追蹤，提升客戶服務體驗和滿意度。

### 1.2 業務價值
- 提供24/7自助服務能力
- 提升客戶滿意度和服務效率
- 減少人工客服工作量
- 建立透明的服務追蹤機制
- 強化客戶關係管理

### 1.3 模組邊界
**包含功能**:
- 安全連結存取
- 報告下載管理
- 歷史記錄查詢
- 狀態追蹤顯示
- 基本客戶資料管理

**不包含功能**:
- 客戶註冊功能 (由管理員建立)
- 線上支付 (Phase 2)
- 即時客服 (Phase 2)

## 2. 功能需求

### 2.1 核心功能列表

#### 2.1.1 存取控制功能
- **F001**: 安全連結生成與驗證
- **F002**: 客戶身份認證
- **F003**: 會話管理
- **F004**: 存取權限控制

#### 2.1.2 報告管理功能
- **F005**: 報告列表顯示
- **F006**: 報告詳情查看
- **F007**: 報告下載功能
- **F008**: 下載歷史記錄

#### 2.1.3 查詢功能
- **F009**: 歷史報告查詢
- **F010**: 狀態追蹤查詢
- **F011**: 進度時間軸顯示
- **F012**: 搜尋和篩選

## 3. 技術設計

### 3.1 資料模型

#### 3.1.1 客戶實體
```typescript
interface Customer {
  id: string;
  companyName: string;
  contactName: string;
  email: string;
  phone: string;
  address: string;
  status: CustomerStatus;
  createdAt: Date;
  updatedAt: Date;
}

enum CustomerStatus {
  ACTIVE = 'active',
  INACTIVE = 'inactive',
  SUSPENDED = 'suspended'
}
```

#### 3.1.2 存取令牌實體
```typescript
interface AccessToken {
  id: string;
  customerId: string;
  reportId: string;
  token: string;
  expiresAt: Date;
  accessCount: number;
  maxAccess: number;
  isActive: boolean;
  createdAt: Date;
  lastAccessAt?: Date;
}
```

#### 3.1.3 下載記錄實體
```typescript
#### 3.1.3 客戶實體
**參考 SOT**: [`docs/master_data_model.md`](../master_data_model.md) - 7.1 customers (客戶表)

```typescript
interface Customer {
  id: bigint;                    // 客戶唯一識別碼 (BIGSERIAL)
  customerCode: string;          // 客戶代碼 (VARCHAR(50), UNIQUE)
  companyName: string;           // 公司名稱 (VARCHAR(200))
  contactPerson?: string;        // 聯絡人 (VARCHAR(100))
  email?: string;                // 電子郵件 (VARCHAR(255))
  phone?: string;                // 電話號碼 (VARCHAR(20))
  address?: string;              // 地址 (TEXT)
  status: string;                // 客戶狀態 (VARCHAR(20))
  preferences: object;           // 偏好設定 (JSONB)
  createdAt: Date;               // 建立時間 (TIMESTAMP WITH TIME ZONE)
  updatedAt: Date;               // 更新時間 (TIMESTAMP WITH TIME ZONE)
  deletedAt?: Date;              // 刪除時間 (TIMESTAMP WITH TIME ZONE, 軟刪除)
}

#### 3.1.4 客戶存取令牌實體
**參考 SOT**: [`docs/master_data_model.md`](../master_data_model.md) - 7.2 customer_access_tokens (客戶存取令牌表)

```typescript
interface CustomerAccessToken {
  id: bigint;                    // 存取令牌唯一識別碼 (BIGSERIAL)
  customerId: bigint;            // 客戶 ID (BIGINT, 參照 customers.id)
  testReportId: bigint;          // 測試報告 ID (BIGINT, 參照 test_reports.id)
  tokenHash: string;             // 令牌雜湊值 (VARCHAR(255), UNIQUE)
  accessType: string;            // 存取類型 (VARCHAR(20))
  expiresAt: Date;               // 過期時間 (TIMESTAMP WITH TIME ZONE)
  maxDownloads?: number;         // 最大下載次數 (INTEGER)
  downloadCount: number;         // 下載次數 (INTEGER)
  ipWhitelist?: string;          // IP 白名單 (TEXT)
  status: string;                // 令牌狀態 (VARCHAR(20))
  createdBy: bigint;             // 建立者 ID (BIGINT, 參照 users.id)
  createdAt: Date;               // 建立時間 (TIMESTAMP WITH TIME ZONE)
  lastAccessedAt?: Date;         // 最後存取時間 (TIMESTAMP WITH TIME ZONE)
}

#### 3.1.5 報告下載記錄實體
**參考 SOT**: [`docs/master_data_model.md`](../master_data_model.md) - 7.3 report_downloads (報告下載記錄表)

```typescript
interface ReportDownload {
  id: bigint;                    // 下載記錄唯一識別碼 (BIGSERIAL)
  customerAccessTokenId: bigint; // 客戶存取令牌 ID (BIGINT, 參照 customer_access_tokens.id)
  testReportId: bigint;          // 測試報告 ID (BIGINT, 參照 test_reports.id)
  customerId: bigint;            // 客戶 ID (BIGINT, 參照 customers.id)
  ipAddress: string;             // IP 位址 (VARCHAR(45))
  userAgent: string;             // 用戶代理 (VARCHAR(500))
  downloadMethod: string;        // 下載方式 (VARCHAR(50))
  downloadedAt: Date;            // 下載時間 (TIMESTAMP WITH TIME ZONE)
}
```

### 3.2 核心服務設計

#### 3.2.1 客戶入口服務
```typescript
class CustomerPortalService {
  // 生成存取連結
  async generateAccessLink(reportId: string, customerId: string): Promise<AccessLink>;
  
  // 驗證存取令牌
  async validateAccessToken(token: string): Promise<TokenValidationResult>;
  
  // 獲取客戶報告列表
  async getCustomerReports(customerId: string): Promise<CustomerReport[]>;
  
  // 記錄下載行為
  async recordDownload(reportId: string, customerId: string, metadata: DownloadMetadata): Promise<void>;
}
```

#### 3.2.2 安全存取服務
```typescript
class SecureAccessService {
  // 生成安全令牌
  generateSecureToken(reportId: string, customerId: string, expirationHours: number = 72): string {
    const payload = {
      reportId,
      customerId,
      exp: Math.floor(Date.now() / 1000) + (expirationHours * 3600),
      iat: Math.floor(Date.now() / 1000)
    };
    
    return jwt.sign(payload, process.env.ACCESS_TOKEN_SECRET);
  }
  
  // 驗證令牌
  async verifyToken(token: string): Promise<TokenPayload> {
    try {
      const payload = jwt.verify(token, process.env.ACCESS_TOKEN_SECRET) as TokenPayload;
      
      // 檢查令牌是否仍然有效
      const accessToken = await this.getAccessToken(token);
      if (!accessToken || !accessToken.isActive) {
        throw new Error('Token已失效');
      }
      
      // 檢查存取次數限制
      if (accessToken.accessCount >= accessToken.maxAccess) {
        throw new Error('存取次數已達上限');
      }
      
      return payload;
    } catch (error) {
      throw new Error('無效的存取令牌');
    }
  }
}
```

### 3.3 前端設計

#### 3.3.1 客戶入口主頁面
```typescript
const CustomerPortal: React.FC = () => {
  const [reports, setReports] = useState<CustomerReport[]>([]);
  const [loading, setLoading] = useState(true);
  const { token } = useParams<{ token: string }>();
  
  useEffect(() => {
    if (token) {
      validateTokenAndLoadReports(token);
    }
  }, [token]);
  
  const validateTokenAndLoadReports = async (accessToken: string) => {
    try {
      const validation = await validateAccessToken(accessToken);
      if (validation.isValid) {
        const customerReports = await getCustomerReports(validation.customerId);
        setReports(customerReports);
      }
    } catch (error) {
      message.error('存取連結無效或已過期');
    } finally {
      setLoading(false);
    }
  };
  
  return (
    <div className="customer-portal">
      <Header />
      <div className="portal-content">
        <ReportsList reports={reports} />
        <DownloadHistory customerId={validation?.customerId} />
      </div>
    </div>
  );
};
```

#### 3.3.2 報告下載組件
```typescript
interface ReportDownloadProps {
  report: CustomerReport;
  onDownload: (reportId: string) => void;
}

const ReportDownload: React.FC<ReportDownloadProps> = ({ report, onDownload }) => {
  const [downloading, setDownloading] = useState(false);
  
  const handleDownload = async () => {
    setDownloading(true);
    try {
      const downloadUrl = await getReportDownloadUrl(report.id);
      
      // 建立隱藏的下載連結
      const link = document.createElement('a');
      link.href = downloadUrl;
      link.download = report.fileName;
      document.body.appendChild(link);
      link.click();
      document.body.removeChild(link);
      
      onDownload(report.id);
      message.success('報告下載成功');
    } catch (error) {
      message.error('下載失敗，請稍後再試');
    } finally {
      setDownloading(false);
    }
  };
  
  return (
    <Card className="report-card">
      <div className="report-info">
        <h3>{report.title}</h3>
        <p>報告編號: {report.reportNumber}</p>
        <p>生成日期: {formatDate(report.generatedAt)}</p>
        <p>檔案大小: {formatFileSize(report.fileSize)}</p>
      </div>
      
      <div className="report-actions">
        <Button 
          type="primary" 
          icon={<DownloadOutlined />}
          loading={downloading}
          onClick={handleDownload}
        >
          下載報告
        </Button>
        
        <Button 
          type="link" 
          icon={<EyeOutlined />}
          onClick={() => previewReport(report.id)}
        >
          預覽
        </Button>
      </div>
      
      <div className="report-status">
        <Tag color={getStatusColor(report.status)}>
          {getStatusText(report.status)}
        </Tag>
      </div>
    </Card>
  );
};
```

## 4. API 接口設計

### 4.1 存取驗證 API

**GET /api/customer-portal/validate/:token**
```typescript
interface ValidateTokenResponse {
  success: boolean;
  data: {
    isValid: boolean;
    customerId?: string;
    customerName?: string;
    expiresAt?: Date;
    accessCount?: number;
    maxAccess?: number;
  };
}
```

### 4.2 報告管理 API

**GET /api/customer-portal/reports**
```typescript
interface CustomerReportsResponse {
  success: boolean;
  data: {
    reports: CustomerReport[];
    total: number;
  };
}
```

**GET /api/customer-portal/reports/:id/download**
```typescript
// 返回預簽名的下載 URL 或直接文件流
interface ReportDownloadResponse {
  success: boolean;
  data: {
    downloadUrl: string;
    expiresAt: Date;
  };
}
```

## 5. 安全考量

### 5.1 存取安全
- **令牌加密**: JWT 令牌加密儲存
- **時效控制**: 存取連結時效限制
- **次數限制**: 存取次數上限控制
- **IP 限制**: 可選的 IP 位址限制

### 5.2 資料安全
- **資料隔離**: 客戶資料嚴格隔離
- **敏感資訊**: 敏感資訊遮罩處理
- **傳輸加密**: HTTPS 強制加密
- **下載追蹤**: 完整的下載行為記錄

### 5.3 防護機制
- **防爬蟲**: 反爬蟲機制
- **頻率限制**: API 呼叫頻率限制
- **異常檢測**: 異常存取行為檢測
- **自動封鎖**: 可疑行為自動封鎖

## 6. 部署配置

### 6.1 環境變數
```bash
# 存取控制配置
ACCESS_TOKEN_SECRET=your-secret-key
ACCESS_TOKEN_EXPIRES_HOURS=72
MAX_ACCESS_COUNT=10

# 下載配置
DOWNLOAD_RATE_LIMIT=5
DOWNLOAD_TIMEOUT=300000
MAX_CONCURRENT_DOWNLOADS=3

# 安全配置
ENABLE_IP_RESTRICTION=false
ENABLE_DOWNLOAD_TRACKING=true
```

### 6.2 資料庫 Schema
```sql
-- 客戶表
CREATE TABLE customers (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  company_name VARCHAR(255) NOT NULL,
  contact_name VARCHAR(255) NOT NULL,
  email VARCHAR(255) NOT NULL,
  phone VARCHAR(50),
  address TEXT,
  status VARCHAR(20) DEFAULT 'active',
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

-- 存取令牌表
CREATE TABLE access_tokens (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  customer_id UUID NOT NULL REFERENCES customers(id),
  report_id UUID NOT NULL,
  token VARCHAR(500) NOT NULL UNIQUE,
  expires_at TIMESTAMP NOT NULL,
  access_count INTEGER DEFAULT 0,
  max_access INTEGER DEFAULT 10,
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT NOW(),
  last_access_at TIMESTAMP
);

-- 下載記錄表
CREATE TABLE download_records (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  customer_id UUID NOT NULL REFERENCES customers(id),
  report_id UUID NOT NULL,
  downloaded_at TIMESTAMP DEFAULT NOW(),
  ip_address INET,
  user_agent TEXT,
  file_size BIGINT,
  download_duration INTEGER
);

-- 索引
CREATE INDEX idx_access_tokens_token ON access_tokens(token);
CREATE INDEX idx_access_tokens_expires ON access_tokens(expires_at) WHERE is_active = true;
CREATE INDEX idx_download_records_customer ON download_records(customer_id);
```

## 7. 監控與維運

### 7.1 監控指標
- **存取成功率**: 成功存取 / 總存取嘗試
- **下載成功率**: 成功下載 / 總下載嘗試
- **平均下載時間**: 報告下載平均耗時
- **客戶活躍度**: 活躍客戶數量統計

### 7.2 告警規則
- **存取異常**: 異常存取行為檢測
- **下載失敗**: 下載失敗率過高
- **令牌濫用**: 令牌異常使用
- **系統錯誤**: 客戶入口系統錯誤

## 8. 模組接口定義

### 8.1 對外提供的接口

#### 8.1.1 報告發布接口
**接口名稱**: `POST /api/customer-portal/reports/publish`
**用途**: 接收來自報告產生器的報告發布請求
**調用方**: 報告產生器模組

基於 [`docs/architecture/module_interaction_analysis.md`](../architecture/module_interaction_analysis.md) 中的接口 8：

```typescript
interface PublishReportRequest {
  reportId: string;
  customerId: string;
  reportMetadata: {
    title: string;
    reportNumber: string;
    sampleName: string;
    testDate: Date;
    expirationDate?: Date;        // 報告有效期
  };
  accessSettings: {
    maxDownloads: number;
    accessDuration: number;       // 存取有效期(小時)
    requiresPassword: boolean;
  };
}

interface PublishReportResponse {
  success: boolean;
  data: {
    accessToken: string;
    accessUrl: string;
    expiresAt: Date;
  };
}
```

#### 8.1.2 客戶報告查詢接口
**接口名稱**: `GET /api/customer-portal/reports`
**用途**: 提供客戶可存取的報告列表
**調用方**: 客戶端前端介面

```typescript
interface CustomerReportsRequest {
  customerId?: string;
  accessToken?: string;
  status?: 'available' | 'expired' | 'downloaded';
  dateRange?: {
    startDate: Date;
    endDate: Date;
  };
}

interface CustomerReportsResponse {
  success: boolean;
  data: {
    reports: Array<{
      reportId: string;
      title: string;
      reportNumber: string;
      sampleName: string;
      testDate: Date;
      publishedAt: Date;
      expiresAt?: Date;
      downloadCount: number;
      maxDownloads: number;
      status: 'available' | 'expired' | 'downloaded';
    }>;
    total: number;
  };
}
```

#### 8.1.3 報告存取驗證接口
**接口名稱**: `POST /api/customer-portal/access/verify`
**用途**: 驗證客戶的報告存取權限
**調用方**: 客戶端前端介面

```typescript
interface VerifyAccessRequest {
  accessToken: string;
  reportId: string;
  action: 'view' | 'download';
}

interface VerifyAccessResponse {
  success: boolean;
  data: {
    hasAccess: boolean;
    remainingDownloads: number;
    expiresAt: Date;
    requiresPassword: boolean;
  };
}
```

### 8.2 依賴的外部接口

#### 8.2.1 用戶認證模組接口
**依賴接口**: `POST /api/auth/verify-permission`
**用途**: 驗證客戶的報告存取權限
**調用場景**: 客戶存取報告前的權限檢查

```typescript
interface CustomerAccessPermissionRequest {
  userId: string;
  resource: 'customer_reports';
  action: 'read' | 'download';
  context: {
    reportId: string;
    customerId: string;
    accessToken?: string;
  };
}
```

#### 8.2.2 報告產生器接口
**依賴接口**: `GET /api/reports/:reportId/download`
**用途**: 獲取報告檔案供客戶下載
**調用場景**: 客戶請求下載報告時

```typescript
interface CustomerReportDownloadRequest {
  reportId: string;
  accessToken: string;
  downloadReason: 'customer_access' | 'customer_download';
}
```

#### 8.2.3 通知模組接口
**依賴接口**: `POST /api/notifications`
**用途**: 發送客戶相關通知
**調用場景**: 報告可用通知、存取提醒、過期警告等

```typescript
interface CustomerNotificationRequest {
  type: 'report_available' | 'access_reminder' | 'expiry_warning';
  recipientId: string;
  templateId: string;
  templateData: {
    reportId: string;
    reportTitle: string;
    accessUrl: string;
    expiresAt?: Date;
    remainingDownloads?: number;
  };
  priority: 'low' | 'medium' | 'high';
}
```

#### 8.2.4 審計日誌模組接口
**依賴接口**: `POST /api/audit/log`
**用途**: 記錄所有客戶存取操作的審計日誌
**調用頻率**: 每次報告存取、下載、查詢操作

```typescript
interface CustomerAccessAuditLogRequest {
  eventType: 'user_action';
  category: 'data_access';
  userId?: string;
  sessionId?: string;
  resource: 'customer_reports';
  action: 'view' | 'download' | 'access_attempt' | 'access_denied';
  details: {
    reportId: string;
    customerId: string;
    accessToken: string;
    ipAddress: string;
    userAgent: string;
    downloadCount?: number;
    accessMethod: 'token' | 'direct_link';
  };
  result: 'success' | 'failure';
  severity: 'info' | 'warning' | 'error';
}
```

## 9. 依賴關係

### 9.1 直接依賴的核心模組
根據 [`docs/architecture/system_architecture.md`](../architecture/system_architecture.md) 中的模組依賴圖：

1. **用戶認證模組 (UserAuthModule)**
   - **依賴原因**: 驗證客戶身份和報告存取權限
   - **交互接口**: `POST /api/auth/verify-permission`
   - **調用場景**: 客戶存取報告前的權限檢查
   - **依賴強度**: 強依賴（安全控制必需）

2. **報告產生器 (ReportGeneratorModule)**
   - **依賴原因**: 獲取報告檔案供客戶下載
   - **交互接口**: `GET /api/reports/:reportId/download`
   - **調用場景**: 客戶請求下載報告時
   - **依賴強度**: 強依賴（核心功能）

3. **通知模組 (NotificationModule)**
   - **依賴原因**: 發送客戶相關通知
   - **交互接口**: `POST /api/notifications`
   - **調用場景**: 報告可用通知、存取提醒等
   - **依賴強度**: 中依賴（用戶體驗增強）

4. **審計日誌模組 (AuditLogModule)**
   - **依賴原因**: 記錄所有客戶存取操作的完整追蹤
   - **交互接口**: `POST /api/audit/log`
   - **調用場景**: 所有客戶存取和下載操作
   - **依賴強度**: 強依賴（合規要求）

### 9.2 被依賴的模組

1. **報告產生器 (ReportGeneratorModule)**
   - **依賴接口**: `POST /api/customer-portal/reports/publish`
   - **依賴原因**: 報告生成完成後發布到客戶入口

### 9.3 技術基礎設施依賴
- **PostgreSQL**: 客戶存取記錄、報告中繼資料儲存
- **Redis**: 存取令牌快取、下載計數快取
- **MinIO/S3**: 報告檔案儲存（透過報告產生器）
- **jsonwebtoken**: 存取令牌生成和驗證
- **express-rate-limit**: API 頻率限制

### 9.4 外部服務依賴
- **CDN 服務**: 報告檔案快速分發
- **安全掃描服務**: 下載檔案安全檢查
- **地理位置服務**: 存取來源地理分析

## 9. 未來擴展

### 9.1 Phase 1 擴展
- **客戶帳號系統**: 完整的客戶註冊登入
- **個人化設定**: 客戶偏好設定
- **通知訂閱**: 報告完成通知訂閱

### 9.2 Phase 2 擴展
- **行動端支援**: 手機端優化界面
- **即時客服**: 線上客服整合
- **多語系支援**: 國際化客戶支援

## 10. 驗收標準 (Acceptance Criteria)

### 10.1 報告存取功能驗收標準

#### AC-PORTAL-001: 安全連結存取 (F001)
**Given** 客戶收到報告可用通知
**When** 點擊安全連結存取報告
**Then**
- 連結驗證在 1 秒內完成
- 存取令牌有效性正確檢查
- IP 白名單驗證 (如適用)
- 存取次數限制正確執行
- 過期連結正確拒絕存取
- 存取記錄完整記錄到審計日誌
- 錯誤訊息清晰友善

#### AC-PORTAL-002: 報告列表顯示 (F002)
**Given** 客戶成功存取客戶入口
**When** 查看可用報告列表
**Then**
- 報告列表在 2 秒內載入完成
- 顯示報告基本資訊 (名稱、日期、狀態)
- 報告按日期降序排列
- 支援報告搜尋和篩選
- 顯示報告下載狀態和剩餘次數
- 過期報告正確標示

#### AC-PORTAL-003: 報告詳細資訊 (F003)
**Given** 客戶選擇特定報告
**When** 查看報告詳細資訊
**Then**
- 報告詳情在 1 秒內載入
- 顯示完整的報告中繼資料
- 包含檢驗項目、日期、結果摘要
- 顯示報告狀態和有效期
- 提供報告預覽功能 (如支援)
- 下載按鈕狀態正確

### 10.2 報告下載功能驗收標準

#### AC-PORTAL-004: 報告下載處理 (F004)
**Given** 客戶請求下載報告
**When** 點擊下載按鈕
**Then**
- 下載權限驗證通過
- 下載在 10 秒內開始
- 下載進度正確顯示
- 支援斷點續傳
- 下載完成後檔案完整性驗證
- 下載次數正確遞減
- 下載記錄完整記錄

#### AC-PORTAL-005: 下載限制控制 (F005)
**Given** 報告設定下載限制
**When** 達到下載限制
**Then**
- 下載次數正確統計
- 達到限制時禁止下載
- 提供清晰的限制說明
- 支援管理員重置下載次數
- 限制狀態即時更新

#### AC-PORTAL-006: 檔案完整性保證 (F006)
**Given** 客戶下載報告檔案
**When** 檔案下載完成
**Then**
- 檔案大小正確
- 檔案格式有效 (PDF)
- 檔案內容完整無損
- 檔案可正常開啟和閱讀
- 檔案包含正確的數位簽章

### 10.3 安全控制功能驗收標準

#### AC-PORTAL-007: 存取令牌管理 (F007)
**Given** 系統生成存取令牌
**When** 令牌被使用
**Then**
- 令牌格式符合 JWT 標準
- 令牌包含必要的聲明資訊
- 令牌有效期正確設定
- 令牌簽章驗證通過
- 過期令牌自動失效
- 令牌撤銷機制有效

#### AC-PORTAL-008: IP 白名單控制 (F008)
**Given** 報告設定 IP 白名單
**When** 客戶從不同 IP 存取
**Then**
- IP 地址正確識別
- 白名單比對準確
- 非白名單 IP 正確拒絕
- 拒絕存取記錄到安全日誌
- 支援 IP 範圍設定
- 支援動態 IP 白名單更新

#### AC-PORTAL-009: 存取頻率限制 (F009)
**Given** 設定存取頻率限制
**When** 客戶頻繁存取
**Then**
- 存取頻率正確統計
- 超過限制時暫時阻止存取
- 限制時間正確計算
- 提供友善的限制提示
- 支援不同客戶不同限制

### 10.4 用戶體驗功能驗收標準

#### AC-PORTAL-010: 響應式設計 (F010)
**Given** 客戶使用不同設備存取
**When** 在各種螢幕尺寸下操作
**Then**
- 桌面端 (≥1024px) 完整功能支援
- 平板端 (768-1023px) 主要功能可用
- 手機端 (≤767px) 基本功能可用
- 觸控操作友善
- 載入速度在各設備上均可接受

#### AC-PORTAL-011: 多語系支援 (F011)
**Given** 客戶選擇不同語言
**When** 切換語言設定
**Then**
- 界面文字正確翻譯
- 日期時間格式本地化
- 錯誤訊息本地化
- 支援至少中文和英文
- 語言設定持久保存

#### AC-PORTAL-012: 無障礙設計 (F012)
**Given** 使用輔助技術的客戶
**When** 存取客戶入口
**Then**
- 支援螢幕閱讀器
- 鍵盤導航功能完整
- 色彩對比度符合標準
- 圖片包含替代文字
- 表單標籤正確關聯

### 10.5 整合功能驗收標準

#### AC-PORTAL-013: 報告產生器整合
**Given** 報告產生器發布新報告
**When** 調用客戶入口 API
**Then**
- 報告中繼資料正確接收
- 存取權限正確設定
- 通知正確觸發
- 報告狀態正確更新
- 整合過程無資料遺失

#### AC-PORTAL-014: 通知系統整合
**Given** 需要發送客戶通知
**When** 觸發通知事件
**Then**
- 通知內容準確完整
- 通知發送及時
- 通知連結正確有效
- 支援多種通知類型
- 通知發送狀態可追蹤

### 10.6 效能與可靠性驗收標準

#### AC-PORTAL-015: 系統效能要求
**Given** 系統處於正常負載狀態
**When** 客戶存取客戶入口
**Then**
- 頁面載入時間 < 3 秒
- 報告列表載入時間 < 2 秒
- 下載開始時間 < 10 秒
- 支援同時 100 位客戶存取
- 系統回應時間穩定

#### AC-PORTAL-016: 可靠性保證
**Given** 系統長時間運行
**When** 處理客戶請求
**Then**
- 系統可用性 > 99%
- 下載成功率 > 98%
- 無資料遺失事件
- 系統故障自動恢復
- 錯誤處理機制完善

#### AC-PORTAL-017: 擴展性支援
**Given** 客戶數量增長
**When** 系統負載增加
**Then**
- 支援水平擴展
- 效能線性增長
- 資源使用合理
- 快取機制有效
- 負載均衡正常

### 10.7 安全性與合規驗收標準

#### AC-PORTAL-018: 資料安全
**Given** 客戶存取敏感報告
**When** 進行安全檢查
**Then**
- 所有傳輸加密 (HTTPS)
- 敏感資料適當保護
- 存取權限嚴格控制
- 會話管理安全
- 符合資料保護法規

#### AC-PORTAL-019: 審計追蹤
**Given** 客戶執行任何操作
**When** 操作完成
**Then**
- 所有操作完整記錄到審計日誌
- 日誌包含完整的上下文資訊
- 支援合規性報告生成
- 日誌查詢效能良好
- 日誌資料完整性保護

#### AC-PORTAL-020: 隱私保護
**Given** 處理客戶個人資料
**When** 進行隱私檢查
**Then**
- 最小化資料收集
- 資料使用目的明確
- 支援資料刪除請求
- 隱私政策清晰透明
- 符合 GDPR 等法規要求

## 11. 結論

客戶入口模組為客戶提供了安全、便捷的自助服務平台，通過完善的存取控制和用戶體驗設計，能夠有效提升客戶滿意度和服務效率。模組化的設計為未來的功能擴展提供了良好的基礎。

關鍵設計特點：
1. **安全性**: 多層次的安全控制機制
2. **便利性**: 簡潔直觀的用戶界面
3. **可追溯性**: 完整的存取和下載記錄
4. **可擴展性**: 為未來功能擴展預留空間

通過本模組的實施，系統將具備專業的客戶服務能力，為客戶提供優質的數位化服務體驗。