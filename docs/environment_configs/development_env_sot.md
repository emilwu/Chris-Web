# Hwayo 開發環境配置 SOT

## 文件資訊
- **文件名稱**: 開發環境配置唯一真實來源 (SOT)
- **環境類型**: Development Environment
- **建立日期**: 2025/05/31
- **階段**: 子任務 5.3 - 定義詳細環境配置並確立 SOT
- **狀態**: 已完成
- **維護責任**: DevOps 團隊 + 開發團隊
- **參考文件**: 
  - [`docs/architecture/system_architecture.md`](../architecture/system_architecture.md)
  - [`planning/project_development_dod_guide.md`](../../planning/project_development_dod_guide.md)

## 1. 環境概述

### 1.1 環境目的
- **主要用途**: 本地開發、功能開發、初步測試
- **使用者**: 開發團隊成員
- **特性**: 快速部署、完整功能、開發友好的配置
- **資料隔離**: 使用模擬資料和測試資料集

### 1.2 環境特點
- ✅ 快速啟動和重啟
- ✅ 詳細的除錯資訊和日誌
- ✅ 熱重載 (Hot Reload) 支援
- ✅ 開發工具整合
- ✅ 模擬外部服務

## 2. 作業系統與基礎環境

### 2.1 作業系統需求
- **推薦 OS**: 
  - macOS 12+ (Apple Silicon/Intel)
  - Ubuntu 20.04+ LTS
  - Windows 11 (with WSL2)
- **容器平台**: Docker Desktop 4.15+
- **記憶體需求**: 最少 8GB RAM (推薦 16GB)
- **磁碟空間**: 最少 20GB 可用空間

### 2.2 開發工具需求
- **IDE**: VS Code 1.75+ (推薦擴充套件清單見附錄)
- **版本控制**: Git 2.30+
- **套件管理**: npm 9.x / yarn 3.x
- **API 測試**: Postman 或 Insomnia

## 3. 核心技術棧版本

### 3.1 Node.js 環境
```yaml
runtime:
  nodejs: "18.19.0"  # LTS 版本
  npm: "10.2.3"
  typescript: "5.3.3"
  
package_manager:
  preferred: "npm"
  alternative: "yarn@3.6.4"
```

### 3.2 TypeScript 配置
```json
{
  "compilerOptions": {
    "target": "ES2022",
    "module": "commonjs",
    "lib": ["ES2022"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true
  }
}
```

## 4. 資料庫配置

### 4.1 PostgreSQL (主資料庫)
```yaml
database:
  engine: "postgresql"
  version: "15.5"
  host: "localhost"
  port: 5432
  database: "hwayo_dev"
  username: "hwayo_dev_user"
  password: "dev_password_123"  # 開發環境可使用簡單密碼
  
connection_pool:
  min_connections: 2
  max_connections: 10
  idle_timeout: "30s"
  
extensions:
  - "uuid-ossp"
  - "pg_trgm"
  - "btree_gin"
```

### 4.2 MongoDB (審計日誌)
```yaml
mongodb:
  version: "6.0.12"
  host: "localhost"
  port: 27017
  database: "hwayo_audit_dev"
  username: "audit_dev_user"
  password: "audit_dev_123"
  
collections:
  - "audit_logs"
  - "system_events"
  - "user_activities"
```

### 4.3 Redis (快取與訊息佇列)
```yaml
redis:
  version: "7.2.3"
  host: "localhost"
  port: 6379
  password: ""  # 開發環境無密碼
  
databases:
  cache: 0
  sessions: 1
  queue: 2
  
memory_policy: "allkeys-lru"
max_memory: "256mb"
```

## 5. 應用服務配置

### 5.1 Web 應用程式
```yaml
web_app:
  framework: "React 18.2.0"
  build_tool: "Vite 5.0.0"
  port: 3000
  
development:
  hot_reload: true
  source_maps: true
  dev_tools: true
  
environment_variables:
  NODE_ENV: "development"
  REACT_APP_API_URL: "http://localhost:3001/api"
  REACT_APP_WS_URL: "ws://localhost:3001"
```

### 5.2 API 閘道
```yaml
api_gateway:
  framework: "Express.js 4.18.2"
  port: 3001
  
middleware:
  - "cors"
  - "helmet"
  - "morgan"
  - "compression"
  
rate_limiting:
  enabled: false  # 開發環境關閉
  
environment_variables:
  NODE_ENV: "development"
  PORT: 3001
  LOG_LEVEL: "debug"
  DB_HOST: "localhost"
  DB_PORT: 5432
  DB_NAME: "hwayo_dev"
  DB_USER: "hwayo_dev_user"
  DB_PASSWORD: "dev_password_123"
  REDIS_URL: "redis://localhost:6379"
  MONGODB_URL: "mongodb://audit_dev_user:audit_dev_123@localhost:27017/hwayo_audit_dev"
```

### 5.3 業務服務
```yaml
services:
  auth_service:
    port: 3002
    jwt_secret: "dev_jwt_secret_key_not_for_production"
    jwt_expiry: "24h"
    
  workflow_engine:
    port: 3003
    max_concurrent_workflows: 10
    
  notification_service:
    port: 3004
    email_provider: "mock"  # 使用模擬服務
    sms_provider: "mock"
```

## 6. 檔案儲存配置

### 6.1 本地檔案儲存
```yaml
file_storage:
  type: "local"
  base_path: "./storage/uploads"
  max_file_size: "10MB"
  allowed_types:
    - "application/pdf"
    - "image/jpeg"
    - "image/png"
    - "text/csv"
    - "application/vnd.ms-excel"
    
backup:
  enabled: false  # 開發環境不需要備份
```

### 6.2 MinIO (S3 相容儲存模擬)
```yaml
minio:
  version: "RELEASE.2023-12-02T10-51-33Z"
  host: "localhost"
  port: 9000
  console_port: 9001
  access_key: "minioadmin"
  secret_key: "minioadmin"
  
buckets:
  - "hwayo-reports-dev"
  - "hwayo-attachments-dev"
  - "hwayo-templates-dev"
```

## 7. 網路配置

### 7.1 端口分配
```yaml
ports:
  web_app: 3000
  api_gateway: 3001
  auth_service: 3002
  workflow_engine: 3003
  notification_service: 3004
  
  postgresql: 5432
  mongodb: 27017
  redis: 6379
  minio: 9000
  minio_console: 9001
```

### 7.2 域名配置
```yaml
domains:
  web: "http://localhost:3000"
  api: "http://localhost:3001"
  minio: "http://localhost:9000"
  
cors_origins:
  - "http://localhost:3000"
  - "http://127.0.0.1:3000"
```

## 8. 環境變數清單

### 8.1 應用程式環境變數
```bash
# 基本配置
NODE_ENV=development
LOG_LEVEL=debug
PORT=3001

# 資料庫連線
DB_HOST=localhost
DB_PORT=5432
DB_NAME=hwayo_dev
DB_USER=hwayo_dev_user
DB_PASSWORD=dev_password_123
DB_SSL=false

# Redis 配置
REDIS_URL=redis://localhost:6379
REDIS_PASSWORD=

# MongoDB 配置
MONGODB_URL=mongodb://audit_dev_user:audit_dev_123@localhost:27017/hwayo_audit_dev

# JWT 配置
JWT_SECRET=dev_jwt_secret_key_not_for_production
JWT_EXPIRY=24h

# 檔案儲存
STORAGE_TYPE=local
STORAGE_PATH=./storage/uploads
MAX_FILE_SIZE=10485760

# MinIO 配置
MINIO_ENDPOINT=localhost:9000
MINIO_ACCESS_KEY=minioadmin
MINIO_SECRET_KEY=minioadmin
MINIO_USE_SSL=false

# 外部服務 (模擬)
EMAIL_PROVIDER=mock
SMS_PROVIDER=mock
NOTIFICATION_WEBHOOK_URL=http://localhost:3004/webhook

# 除錯配置
DEBUG=hwayo:*
ENABLE_QUERY_LOGGING=true
ENABLE_REQUEST_LOGGING=true
```

## 9. 安全設置

### 9.1 開發環境安全考量
- **密碼複雜度**: 簡化以便開發 (生產環境需強化)
- **HTTPS**: 非必要 (本地開發使用 HTTP)
- **防火牆**: 依賴本機防火牆設定
- **資料加密**: 敏感欄位仍需加密 (測試加密功能)

### 9.2 存取控制
```yaml
access_control:
  admin_users:
    - "dev@hwayo.local"
  
  default_permissions:
    - "read:own_data"
    - "write:own_data"
    - "read:public_reports"
  
  cors_policy:
    allow_origins: ["http://localhost:3000"]
    allow_methods: ["GET", "POST", "PUT", "DELETE", "OPTIONS"]
    allow_headers: ["Content-Type", "Authorization"]
```

## 10. 資源需求估算

### 10.1 硬體需求
```yaml
minimum_requirements:
  cpu: "2 cores"
  memory: "8GB RAM"
  storage: "20GB SSD"
  network: "100Mbps"

recommended_requirements:
  cpu: "4 cores"
  memory: "16GB RAM"
  storage: "50GB SSD"
  network: "1Gbps"
```

### 10.2 容器資源限制
```yaml
docker_limits:
  postgresql:
    memory: "512MB"
    cpu: "0.5"
  
  mongodb:
    memory: "256MB"
    cpu: "0.3"
  
  redis:
    memory: "128MB"
    cpu: "0.2"
  
  minio:
    memory: "256MB"
    cpu: "0.3"
  
  app_services:
    memory: "256MB"
    cpu: "0.5"
```

## 11. 日誌與監控

### 11.1 日誌配置
```yaml
logging:
  level: "debug"
  format: "json"
  
  destinations:
    console: true
    file: "./logs/app.log"
    
  rotation:
    max_size: "10MB"
    max_files: 5
    
  categories:
    - "application"
    - "database"
    - "security"
    - "performance"
```

### 11.2 開發監控
```yaml
monitoring:
  health_checks:
    enabled: true
    interval: "30s"
    
  metrics:
    enabled: true
    endpoint: "/metrics"
    
  profiling:
    enabled: true
    cpu_profiling: true
    memory_profiling: true
```

## 12. 容器化配置

### 12.1 Docker Compose 概要
```yaml
version: '3.8'

services:
  # 資料庫服務
  postgres:
    image: postgres:15.5
    environment:
      POSTGRES_DB: hwayo_dev
      POSTGRES_USER: hwayo_dev_user
      POSTGRES_PASSWORD: dev_password_123
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
      
  mongodb:
    image: mongo:6.0.12
    environment:
      MONGO_INITDB_ROOT_USERNAME: audit_dev_user
      MONGO_INITDB_ROOT_PASSWORD: audit_dev_123
      MONGO_INITDB_DATABASE: hwayo_audit_dev
    ports:
      - "27017:27017"
    volumes:
      - mongodb_data:/data/db
      
  redis:
    image: redis:7.2.3
    ports:
      - "6379:6379"
    volumes:
      - redis_data:/data
      
  minio:
    image: minio/minio:RELEASE.2023-12-02T10-51-33Z
    command: server /data --console-address ":9001"
    environment:
      MINIO_ROOT_USER: minioadmin
      MINIO_ROOT_PASSWORD: minioadmin
    ports:
      - "9000:9000"
      - "9001:9001"
    volumes:
      - minio_data:/data

volumes:
  postgres_data:
  mongodb_data:
  redis_data:
  minio_data:
```

### 12.2 應用程式 Dockerfile 範例
```dockerfile
FROM node:18.19.0-alpine

WORKDIR /app

# 複製 package files
COPY package*.json ./
RUN npm ci --only=production

# 複製應用程式碼
COPY . .

# 建置 TypeScript
RUN npm run build

# 暴露端口
EXPOSE 3001

# 健康檢查
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3001/health || exit 1

# 啟動應用程式
CMD ["npm", "start"]
```

## 13. 基礎設施即代碼 (IaC) 考量

### 13.1 本地開發 IaC
```yaml
infrastructure:
  tool: "Docker Compose"
  config_file: "docker-compose.dev.yml"
  
  advantages:
    - "一鍵啟動完整環境"
    - "版本控制基礎設施配置"
    - "團隊環境一致性"
    
  management:
    start: "docker-compose up -d"
    stop: "docker-compose down"
    reset: "docker-compose down -v && docker-compose up -d"
```

### 13.2 未來雲端 IaC 準備
```yaml
cloud_readiness:
  target_platform: "AWS / Azure / GCP"
  iac_tool: "Terraform"
  
  migration_path:
    1. "容器化所有服務"
    2. "定義基礎設施模板"
    3. "建立 CI/CD 管道"
    4. "實施監控和告警"
```

## 14. 開發工作流程

### 14.1 環境啟動流程
```bash
# 1. 克隆專案
git clone <repository-url>
cd hwayo-project

# 2. 安裝依賴
npm install

# 3. 啟動基礎設施
docker-compose -f docker-compose.dev.yml up -d

# 4. 等待服務就緒
./scripts/wait-for-services.sh

# 5. 執行資料庫遷移
npm run db:migrate

# 6. 載入測試資料
npm run db:seed

# 7. 啟動應用程式
npm run dev
```

### 14.2 常用開發指令
```bash
# 開發服務器
npm run dev              # 啟動開發服務器
npm run dev:watch        # 監視模式啟動

# 資料庫操作
npm run db:migrate       # 執行資料庫遷移
npm run db:rollback      # 回滾資料庫
npm run db:seed          # 載入測試資料
npm run db:reset         # 重置資料庫

# 測試
npm run test             # 執行單元測試
npm run test:integration # 執行整合測試
npm run test:e2e         # 執行端到端測試

# 程式碼品質
npm run lint             # 程式碼檢查
npm run format           # 程式碼格式化
npm run type-check       # TypeScript 類型檢查

# 建置
npm run build            # 建置生產版本
npm run build:dev        # 建置開發版本
```

## 15. 故障排除指南

### 15.1 常見問題
| 問題 | 可能原因 | 解決方案 |
|------|----------|----------|
| 資料庫連線失敗 | Docker 服務未啟動 | `docker-compose up -d postgres` |
| 端口衝突 | 其他服務佔用端口 | 修改 docker-compose.yml 端口映射 |
| 記憶體不足 | 容器資源限制 | 增加 Docker Desktop 記憶體分配 |
| 熱重載不工作 | 檔案監視問題 | 重啟開發服務器 |

### 15.2 除錯工具
```yaml
debugging_tools:
  database:
    - "pgAdmin 4" # PostgreSQL 管理
    - "MongoDB Compass" # MongoDB 管理
    - "RedisInsight" # Redis 管理
    
  api:
    - "Postman" # API 測試
    - "Insomnia" # API 測試替代方案
    
  monitoring:
    - "Docker Desktop Dashboard"
    - "VS Code Docker Extension"
    
  logging:
    - "Docker logs"
    - "Application logs in ./logs/"
```

## 16. 維護與更新

### 16.1 定期維護任務
- **每週**: 更新 npm 套件 (`npm audit fix`)
- **每月**: 更新 Docker 映像檔
- **每季**: 檢查技術棧版本更新

### 16.2 版本升級策略
```yaml
upgrade_strategy:
  nodejs: "跟隨 LTS 版本"
  dependencies: "定期安全更新"
  docker_images: "月度更新"
  
  testing_required:
    - "單元測試通過"
    - "整合測試通過"
    - "基本功能驗證"
```

## 17. 附錄

### 17.1 VS Code 推薦擴充套件
```json
{
  "recommendations": [
    "ms-vscode.vscode-typescript-next",
    "bradlc.vscode-tailwindcss",
    "ms-vscode.vscode-json",
    "redhat.vscode-yaml",
    "ms-vscode-remote.remote-containers",
    "ms-azuretools.vscode-docker",
    "esbenp.prettier-vscode",
    "dbaeumer.vscode-eslint",
    "ms-vscode.vscode-jest",
    "humao.rest-client"
  ]
}
```

### 17.2 開發環境檢查清單
- [ ] Node.js 18.19.0 已安裝
- [ ] Docker Desktop 已安裝並運行
- [ ] Git 已配置
- [ ] VS Code 已安裝推薦擴充套件
- [ ] 專案依賴已安裝 (`npm install`)
- [ ] Docker 服務已啟動 (`docker-compose up -d`)
- [ ] 資料庫已遷移 (`npm run db:migrate`)
- [ ] 測試資料已載入 (`npm run db:seed`)
- [ ] 應用程式可正常啟動 (`npm run dev`)
- [ ] 所有服務健康檢查通過

---

**文件維護**: 此文件應隨著專案發展持續更新，確保開發環境配置的準確性和時效性。