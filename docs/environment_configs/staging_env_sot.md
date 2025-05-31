# Hwayo 測試/預備環境配置 SOT

## 文件資訊
- **文件名稱**: 測試/預備環境配置唯一真實來源 (SOT)
- **環境類型**: Testing/Staging Environment
- **建立日期**: 2025/05/31
- **階段**: 子任務 5.3 - 定義詳細環境配置並確立 SOT
- **狀態**: 已完成
- **維護責任**: DevOps 團隊 + QA 團隊
- **參考文件**: 
  - [`docs/architecture/system_architecture.md`](../architecture/system_architecture.md)
  - [`docs/environment_configs/development_env_sot.md`](development_env_sot.md)
  - [`planning/project_development_dod_guide.md`](../../planning/project_development_dod_guide.md)

## 1. 環境概述

### 1.1 環境目的
- **主要用途**: 整合測試、用戶驗收測試 (UAT)、效能測試、安全測試
- **使用者**: QA 團隊、產品團隊、客戶代表、開發團隊
- **特性**: 接近生產環境配置、穩定的測試資料、完整的監控
- **資料管理**: 使用脫敏的生產資料副本和標準測試資料集

### 1.2 環境特點
- ✅ 接近生產環境的配置
- ✅ 完整的安全設置
- ✅ 詳細的監控和日誌
- ✅ 自動化部署和測試
- ✅ 資料備份和恢復機制

## 2. 作業系統與基礎環境

### 2.1 雲端基礎設施
- **雲端平台**: AWS / Azure / GCP (待確定)
- **作業系統**: Ubuntu 22.04 LTS
- **容器平台**: Docker 24.x + Kubernetes 1.28+
- **負載均衡**: Application Load Balancer (ALB)
- **CDN**: CloudFront / Azure CDN / Cloud CDN

### 2.2 網路架構
```yaml
network_architecture:
  vpc_cidr: "10.1.0.0/16"
  
  subnets:
    public:
      - "10.1.1.0/24"  # ALB, NAT Gateway
      - "10.1.2.0/24"  # ALB, NAT Gateway (Multi-AZ)
    
    private_app:
      - "10.1.10.0/24" # Application servers
      - "10.1.11.0/24" # Application servers (Multi-AZ)
    
    private_db:
      - "10.1.20.0/24" # Database servers
      - "10.1.21.0/24" # Database servers (Multi-AZ)

  security_groups:
    alb: "Allow 80,443 from 0.0.0.0/0"
    app: "Allow 3000-3010 from ALB SG"
    db: "Allow 5432,27017,6379 from App SG"
```

## 3. 核心技術棧版本

### 3.1 Node.js 環境
```yaml
runtime:
  nodejs: "18.19.0"  # 與開發環境保持一致
  npm: "10.2.3"
  typescript: "5.3.3"
  
package_manager:
  preferred: "npm"
  lock_file: "package-lock.json"  # 確保版本一致性
```

### 3.2 容器化配置
```yaml
container_runtime:
  docker: "24.0.7"
  kubernetes: "1.28.4"
  
container_registry:
  type: "AWS ECR / Azure ACR / GCR"
  image_scanning: true
  vulnerability_scanning: true
  
image_tags:
  format: "staging-{git_commit_sha}-{build_number}"
  retention: "30 days"
```

## 4. 資料庫配置

### 4.1 PostgreSQL (主資料庫)
```yaml
database:
  engine: "postgresql"
  version: "15.5"
  deployment: "RDS / Azure Database / Cloud SQL"
  
instance_config:
  instance_class: "db.t3.medium"  # 2 vCPU, 4GB RAM
  storage: "100GB GP3"
  storage_encrypted: true
  multi_az: true
  backup_retention: "7 days"
  
connection_config:
  host: "hwayo-staging-db.cluster-xxx.region.rds.amazonaws.com"
  port: 5432
  database: "hwayo_staging"
  username: "hwayo_staging_user"
  password: "${DB_PASSWORD}"  # 從 Secrets Manager 取得
  ssl_mode: "require"
  
connection_pool:
  min_connections: 5
  max_connections: 20
  idle_timeout: "300s"
  connection_timeout: "30s"
  
extensions:
  - "uuid-ossp"
  - "pg_trgm"
  - "btree_gin"
  - "pg_stat_statements"
```

### 4.2 MongoDB (審計日誌)
```yaml
mongodb:
  version: "6.0.12"
  deployment: "Atlas / Azure Cosmos DB / MongoDB Cloud"
  
cluster_config:
  tier: "M10"  # 2GB RAM, 10GB Storage
  region: "same as primary region"
  backup_enabled: true
  
connection_config:
  host: "hwayo-staging-audit.xxx.mongodb.net"
  port: 27017
  database: "hwayo_audit_staging"
  username: "audit_staging_user"
  password: "${MONGODB_PASSWORD}"
  ssl: true
  auth_source: "admin"
  
collections:
  - name: "audit_logs"
    indexes:
      - "timestamp"
      - "user_id"
      - "action_type"
  - name: "system_events"
    indexes:
      - "timestamp"
      - "event_type"
  - name: "user_activities"
    indexes:
      - "user_id"
      - "timestamp"
```

### 4.3 Redis (快取與訊息佇列)
```yaml
redis:
  version: "7.2.3"
  deployment: "ElastiCache / Azure Cache / Memorystore"
  
cluster_config:
  node_type: "cache.t3.micro"  # 1 vCPU, 0.5GB RAM
  num_nodes: 2
  multi_az: true
  
connection_config:
  host: "hwayo-staging-redis.xxx.cache.amazonaws.com"
  port: 6379
  password: "${REDIS_PASSWORD}"
  ssl: true
  
databases:
  cache: 0
  sessions: 1
  queue: 2
  
memory_policy: "allkeys-lru"
max_memory: "512mb"
eviction_policy: "volatile-lru"
```

## 5. 應用服務配置

### 5.1 Kubernetes 部署配置
```yaml
kubernetes:
  namespace: "hwayo-staging"
  
  deployments:
    web_app:
      replicas: 2
      image: "${ECR_REGISTRY}/hwayo-web:${IMAGE_TAG}"
      resources:
        requests:
          cpu: "100m"
          memory: "256Mi"
        limits:
          cpu: "500m"
          memory: "512Mi"
      
    api_gateway:
      replicas: 2
      image: "${ECR_REGISTRY}/hwayo-api:${IMAGE_TAG}"
      resources:
        requests:
          cpu: "200m"
          memory: "512Mi"
        limits:
          cpu: "1000m"
          memory: "1Gi"
      
    auth_service:
      replicas: 2
      image: "${ECR_REGISTRY}/hwayo-auth:${IMAGE_TAG}"
      resources:
        requests:
          cpu: "100m"
          memory: "256Mi"
        limits:
          cpu: "500m"
          memory: "512Mi"
      
    workflow_engine:
      replicas: 2
      image: "${ECR_REGISTRY}/hwayo-workflow:${IMAGE_TAG}"
      resources:
        requests:
          cpu: "200m"
          memory: "512Mi"
        limits:
          cpu: "1000m"
          memory: "1Gi"
      
    notification_service:
      replicas: 1
      image: "${ECR_REGISTRY}/hwayo-notification:${IMAGE_TAG}"
      resources:
        requests:
          cpu: "100m"
          memory: "256Mi"
        limits:
          cpu: "500m"
          memory: "512Mi"
```

### 5.2 服務配置
```yaml
services:
  web_app:
    type: "ClusterIP"
    port: 80
    target_port: 3000
    
  api_gateway:
    type: "ClusterIP"
    port: 80
    target_port: 3001
    
  auth_service:
    type: "ClusterIP"
    port: 80
    target_port: 3002
    
  workflow_engine:
    type: "ClusterIP"
    port: 80
    target_port: 3003
    
  notification_service:
    type: "ClusterIP"
    port: 80
    target_port: 3004
```

### 5.3 Ingress 配置
```yaml
ingress:
  class: "nginx"
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/force-ssl-redirect: "true"
    
  rules:
    - host: "staging.hwayo.com"
      paths:
        - path: "/"
          service: "web-app"
          port: 80
        - path: "/api"
          service: "api-gateway"
          port: 80
  
  tls:
    - hosts:
        - "staging.hwayo.com"
      secret_name: "hwayo-staging-tls"
```

## 6. 檔案儲存配置

### 6.1 雲端物件儲存
```yaml
object_storage:
  provider: "AWS S3 / Azure Blob / Google Cloud Storage"
  
  buckets:
    reports:
      name: "hwayo-reports-staging"
      versioning: true
      encryption: "AES256"
      lifecycle:
        - transition_to_ia: "30 days"
        - transition_to_glacier: "90 days"
        - expiration: "2555 days"  # 7 years for compliance
    
    attachments:
      name: "hwayo-attachments-staging"
      versioning: true
      encryption: "AES256"
      lifecycle:
        - transition_to_ia: "30 days"
        - expiration: "1095 days"  # 3 years
    
    templates:
      name: "hwayo-templates-staging"
      versioning: true
      encryption: "AES256"
      public_read: false

access_control:
  iam_roles:
    - "hwayo-staging-app-role"
  
  bucket_policies:
    - effect: "Allow"
      principal: "hwayo-staging-app-role"
      actions: ["s3:GetObject", "s3:PutObject", "s3:DeleteObject"]
      resources: ["arn:aws:s3:::hwayo-*-staging/*"]
```

### 6.2 CDN 配置
```yaml
cdn:
  provider: "CloudFront / Azure CDN / Cloud CDN"
  
  distributions:
    static_assets:
      origin: "hwayo-attachments-staging.s3.amazonaws.com"
      cache_behaviors:
        - path_pattern: "*.pdf"
          ttl: "86400"  # 1 day
        - path_pattern: "*.jpg,*.png"
          ttl: "604800"  # 7 days
      
    reports:
      origin: "hwayo-reports-staging.s3.amazonaws.com"
      cache_behaviors:
        - path_pattern: "*"
          ttl: "3600"  # 1 hour
      security:
        signed_urls: true
        ttl: "3600"
```

## 7. 網路與安全配置

### 7.1 負載均衡配置
```yaml
load_balancer:
  type: "Application Load Balancer"
  scheme: "internet-facing"
  
  listeners:
    - port: 80
      protocol: "HTTP"
      action: "redirect to HTTPS"
    
    - port: 443
      protocol: "HTTPS"
      ssl_certificate: "arn:aws:acm:region:account:certificate/xxx"
      target_group: "hwayo-staging-web"
  
  target_groups:
    hwayo_staging_web:
      port: 80
      protocol: "HTTP"
      health_check:
        path: "/health"
        interval: 30
        timeout: 5
        healthy_threshold: 2
        unhealthy_threshold: 3
```

### 7.2 SSL/TLS 配置
```yaml
ssl_tls:
  certificate_authority: "Let's Encrypt"
  certificate_manager: "AWS ACM / Azure Key Vault / Google Certificate Manager"
  
  certificates:
    - domain: "staging.hwayo.com"
      type: "wildcard"
      auto_renewal: true
      
  security_policy:
    min_tls_version: "TLSv1.2"
    cipher_suites: "ECDHE-RSA-AES128-GCM-SHA256,ECDHE-RSA-AES256-GCM-SHA384"
    hsts_enabled: true
    hsts_max_age: "31536000"
```

### 7.3 防火牆與安全群組
```yaml
security_groups:
  alb_sg:
    ingress:
      - port: 80
        protocol: "TCP"
        source: "0.0.0.0/0"
      - port: 443
        protocol: "TCP"
        source: "0.0.0.0/0"
    egress:
      - port: "all"
        protocol: "all"
        destination: "app_sg"
  
  app_sg:
    ingress:
      - port: 3000-3010
        protocol: "TCP"
        source: "alb_sg"
      - port: 22
        protocol: "TCP"
        source: "bastion_sg"
    egress:
      - port: "all"
        protocol: "all"
        destination: "0.0.0.0/0"
  
  db_sg:
    ingress:
      - port: 5432
        protocol: "TCP"
        source: "app_sg"
      - port: 27017
        protocol: "TCP"
        source: "app_sg"
      - port: 6379
        protocol: "TCP"
        source: "app_sg"
    egress: []
```

## 8. 環境變數與機密管理

### 8.1 機密管理服務
```yaml
secrets_management:
  provider: "AWS Secrets Manager / Azure Key Vault / Google Secret Manager"
  
  secrets:
    database:
      name: "hwayo/staging/database"
      keys:
        - "DB_PASSWORD"
        - "DB_CONNECTION_STRING"
    
    redis:
      name: "hwayo/staging/redis"
      keys:
        - "REDIS_PASSWORD"
        - "REDIS_CONNECTION_STRING"
    
    mongodb:
      name: "hwayo/staging/mongodb"
      keys:
        - "MONGODB_PASSWORD"
        - "MONGODB_CONNECTION_STRING"
    
    jwt:
      name: "hwayo/staging/jwt"
      keys:
        - "JWT_SECRET"
        - "JWT_REFRESH_SECRET"
    
    external_services:
      name: "hwayo/staging/external"
      keys:
        - "EMAIL_API_KEY"
        - "SMS_API_KEY"
        - "STORAGE_ACCESS_KEY"
        - "STORAGE_SECRET_KEY"

  rotation_policy:
    database_passwords: "90 days"
    api_keys: "180 days"
    jwt_secrets: "30 days"
```

### 8.2 環境變數配置
```yaml
environment_variables:
  # 基本配置
  NODE_ENV: "staging"
  LOG_LEVEL: "info"
  PORT: "3001"
  
  # 應用程式配置
  APP_NAME: "Hwayo Staging"
  APP_VERSION: "${BUILD_VERSION}"
  APP_URL: "https://staging.hwayo.com"
  API_URL: "https://staging.hwayo.com/api"
  
  # 資料庫配置 (從 Secrets Manager 取得)
  DB_HOST: "${DB_HOST}"
  DB_PORT: "5432"
  DB_NAME: "hwayo_staging"
  DB_USER: "hwayo_staging_user"
  DB_PASSWORD: "${secrets:hwayo/staging/database:DB_PASSWORD}"
  DB_SSL: "true"
  DB_POOL_MIN: "5"
  DB_POOL_MAX: "20"
  
  # Redis 配置
  REDIS_HOST: "${REDIS_HOST}"
  REDIS_PORT: "6379"
  REDIS_PASSWORD: "${secrets:hwayo/staging/redis:REDIS_PASSWORD}"
  REDIS_SSL: "true"
  
  # MongoDB 配置
  MONGODB_URL: "${secrets:hwayo/staging/mongodb:MONGODB_CONNECTION_STRING}"
  
  # JWT 配置
  JWT_SECRET: "${secrets:hwayo/staging/jwt:JWT_SECRET}"
  JWT_REFRESH_SECRET: "${secrets:hwayo/staging/jwt:JWT_REFRESH_SECRET}"
  JWT_EXPIRY: "1h"
  JWT_REFRESH_EXPIRY: "7d"
  
  # 檔案儲存配置
  STORAGE_TYPE: "s3"
  STORAGE_BUCKET_REPORTS: "hwayo-reports-staging"
  STORAGE_BUCKET_ATTACHMENTS: "hwayo-attachments-staging"
  STORAGE_BUCKET_TEMPLATES: "hwayo-templates-staging"
  STORAGE_REGION: "${AWS_REGION}"
  
  # 外部服務配置
  EMAIL_PROVIDER: "ses"
  EMAIL_FROM: "noreply@staging.hwayo.com"
  SMS_PROVIDER: "sns"
  
  # 監控配置
  ENABLE_METRICS: "true"
  METRICS_PORT: "9090"
  ENABLE_TRACING: "true"
  JAEGER_ENDPOINT: "${JAEGER_ENDPOINT}"
  
  # 安全配置
  CORS_ORIGINS: "https://staging.hwayo.com"
  RATE_LIMIT_ENABLED: "true"
  RATE_LIMIT_WINDOW: "15"
  RATE_LIMIT_MAX: "100"
  
  # 功能開關
  FEATURE_NEW_REPORT_ENGINE: "true"
  FEATURE_ADVANCED_ANALYTICS: "false"
  FEATURE_BETA_UI: "true"
```

## 9. 監控與日誌配置

### 9.1 應用程式監控
```yaml
monitoring:
  apm_tool: "New Relic / Datadog / Application Insights"
  
  metrics:
    collection_interval: "30s"
    retention: "30 days"
    
    custom_metrics:
      - "workflow_completion_time"
      - "report_generation_duration"
      - "user_login_success_rate"
      - "api_response_time_p95"
      - "database_connection_pool_usage"
  
  alerts:
    response_time:
      threshold: "2000ms"
      duration: "5m"
      severity: "warning"
    
    error_rate:
      threshold: "5%"
      duration: "2m"
      severity: "critical"
    
    cpu_usage:
      threshold: "80%"
      duration: "10m"
      severity: "warning"
    
    memory_usage:
      threshold: "85%"
      duration: "5m"
      severity: "critical"
```

### 9.2 日誌管理
```yaml
logging:
  centralized_logging: "ELK Stack / Azure Monitor / Cloud Logging"
  
  log_levels:
    application: "info"
    database: "warn"
    security: "info"
    performance: "debug"
  
  log_format: "json"
  
  log_destinations:
    - type: "stdout"
      format: "json"
    - type: "file"
      path: "/var/log/hwayo/app.log"
      rotation: "daily"
      retention: "30 days"
    - type: "elasticsearch"
      index: "hwayo-staging-logs"
      retention: "90 days"
  
  structured_logging:
    include_fields:
      - "timestamp"
      - "level"
      - "service"
      - "trace_id"
      - "user_id"
      - "request_id"
      - "message"
      - "metadata"
```

### 9.3 健康檢查
```yaml
health_checks:
  application:
    endpoint: "/health"
    interval: "30s"
    timeout: "5s"
    
  database:
    endpoint: "/health/db"
    interval: "60s"
    timeout: "10s"
    
  external_services:
    endpoint: "/health/external"
    interval: "120s"
    timeout: "15s"
  
  readiness_probe:
    endpoint: "/ready"
    initial_delay: "30s"
    interval: "10s"
    
  liveness_probe:
    endpoint: "/live"
    initial_delay: "60s"
    interval: "30s"
```

## 10. 備份與災難恢復

### 10.1 資料備份策略
```yaml
backup_strategy:
  postgresql:
    type: "automated_backup"
    frequency: "daily"
    retention: "7 days"
    point_in_time_recovery: "enabled"
    backup_window: "03:00-04:00 UTC"
    
  mongodb:
    type: "automated_backup"
    frequency: "daily"
    retention: "7 days"
    backup_window: "04:00-05:00 UTC"
    
  redis:
    type: "snapshot"
    frequency: "daily"
    retention: "3 days"
    
  file_storage:
    type: "versioning"
    retention: "30 days"
    cross_region_replication: "enabled"
```

### 10.2 災難恢復計劃
```yaml
disaster_recovery:
  rto: "4 hours"  # Recovery Time Objective
  rpo: "1 hour"   # Recovery Point Objective
  
  backup_regions:
    primary: "us-east-1"
    secondary: "us-west-2"
  
  recovery_procedures:
    database:
      - "Restore from automated backup"
      - "Apply point-in-time recovery if needed"
      - "Verify data integrity"
      
    application:
      - "Deploy latest stable image"
      - "Update DNS records"
      - "Verify service health"
      
    file_storage:
      - "Restore from versioned backups"
      - "Verify file accessibility"
```

## 11. 效能與擴展性

### 11.1 效能基準
```yaml
performance_targets:
  response_time:
    api_endpoints: "< 500ms (p95)"
    web_pages: "< 2s (p95)"
    report_generation: "< 30s"
    
  throughput:
    concurrent_users: "100"
    requests_per_second: "500"
    
  availability:
    uptime: "99.5%"
    planned_downtime: "< 4 hours/month"
```

### 11.2 自動擴展配置
```yaml
auto_scaling:
  horizontal_pod_autoscaler:
    min_replicas: 2
    max_replicas: 10
    target_cpu_utilization: 70
    target_memory_utilization: 80
    
  cluster_autoscaler:
    min_nodes: 2
    max_nodes: 6
    scale_down_delay: "10m"
    scale_up_delay: "3m"
```

## 12. 安全配置

### 12.1 身份驗證與授權
```yaml
authentication:
  method: "JWT"
  provider: "internal"
  
  jwt_config:
    algorithm: "RS256"
    issuer: "https://staging.hwayo.com"
    audience: "hwayo-staging"
    expiry: "1h"
    refresh_expiry: "7d"
    
  password_policy:
    min_length: 8
    require_uppercase: true
    require_lowercase: true
    require_numbers: true
    require_symbols: true
    max_age: "90 days"
    
authorization:
  model: "RBAC"
  
  roles:
    - name: "admin"
      permissions: ["*"]
    - name: "researcher"
      permissions: ["read:own_data", "write:own_data", "read:templates"]
    - name: "reviewer"
      permissions: ["read:assigned_data", "write:review", "read:reports"]
    - name: "customer"
      permissions: ["read:own_reports", "download:own_reports"]
```

### 12.2 資料加密
```yaml
encryption:
  data_at_rest:
    database: "AES-256"
    file_storage: "AES-256"
    backups: "AES-256"
    
  data_in_transit:
    api_communication: "TLS 1.2+"
    database_connections: "SSL/TLS"
    internal_services: "mTLS"
    
  key_management:
    provider: "AWS KMS / Azure Key Vault / Google KMS"
    key_rotation: "annual"
    
  sensitive_fields:
    - "user_passwords"
    - "customer_data"
    - "financial_information"
    - "personal_identifiers"
```

### 12.3 安全掃描與合規
```yaml
security_scanning:
  vulnerability_scanning:
    frequency: "weekly"
    tools: ["Snyk", "OWASP Dependency Check"]
    
  container_scanning:
    frequency: "on_build"
    tools: ["Trivy", "Clair"]
    
  code_scanning:
    frequency: "on_commit"
    tools: ["SonarQube", "CodeQL"]
    
compliance:
  standards: ["ISO 27001", "SOC 2 Type II"]
  audit_logging: "enabled"
  data_retention: "7 years"
  gdpr_compliance: "enabled"
```

## 13. CI/CD 配置

### 13.1 部署管道
```yaml
ci_cd_pipeline:
  trigger: "git_push_to_staging_branch"
  
  stages:
    build:
      - "Run unit tests"
      - "Run integration tests"
      - "Build Docker images"
      - "Security scanning"
      - "Push to container registry"
      
    deploy:
      - "Deploy to staging cluster"
      - "Run database migrations"
      - "Run smoke tests"
      - "Update monitoring dashboards"
      
    test:
      - "Run end-to-end tests"
      - "Run performance tests"
      - "Run security tests"
      - "Generate test reports"
      
    notify:
      - "Send deployment notifications"
      - "Update deployment status"

  rollback_strategy:
    automatic: "on_health_check_failure"
    manual: "available_via_cli"
    retention: "last_5_deployments"
```

### 13.2 部署策略
```yaml
deployment_strategy:
  type: "rolling_update"
  
  rolling_update:
    max_unavailable: "25%"
    max_surge: "25%"
    
  blue_green:
    enabled: false  # 可選的部署策略
    
  canary:
    enabled: false  # 可選的部署策略
    
  maintenance_window:
    preferred: "02:00-04:00 UTC"
    notification_advance: "24 hours"
```

## 14. 測試配置

### 14.1 測試資料管理
```yaml
test_data:
  source: "production_subset"
  anonymization: "enabled"
  
  datasets:
    users:
      count: 100
      anonymized_fields: ["email", "phone", "address"]
      
    experiments:
      count: 500
      date_range: "last_6_months"
      
    reports:
      count: 200
      status_distribution:
        draft: "30%"
        in_review: "20%"
        completed: "50%"
  
  refresh_frequency: "weekly"
  retention: "30 days"
```

### 14.2 測試自動化
```yaml
automated_testing:
  unit_tests:
    framework: "Jest"
    coverage_threshold: "80%"
    
  integration_tests:
    framework: "Supertest"
    database: "test_database"
    
  e2e_tests:
    framework: "Playwright"
    browsers: ["chromium", "firefox", "webkit"]
    
  performance_tests:
    tool: "k6"
    scenarios:
      - name: "load_test"
        users: 50
        duration: "10m"
      - name: "stress_test"
        users: 100
        duration: "5m"
        
  security_tests:
    tool: "OWASP ZAP"
    scan_types: ["baseline", "full"]
```

## 15. 故障排除與維護

### 15.1 常見問題處理
```yaml
troubleshooting:
  database_connection_issues:
    symptoms: ["Connection timeout", "Too many connections"]
    solutions:
      - "Check security group rules"
      - "Verify connection pool settings"
      - "Check database instance status"
      
  high_response_times:
    symptoms: ["API responses > 2s", "User complaints"]
    solutions:
      - "Check application metrics"
      - "Review database query performance"
      - "Verify cache hit rates"
      
  deployment_failures:
    symptoms: ["Pod crash loops", "Health check failures"]
    solutions:
      - "Check application logs"
      - "Verify environment variables"
      - "Check resource limits"
```

### 15.2 維護計劃
```yaml
maintenance_schedule:
  security_updates:
    frequency: "monthly"
    window: "first_sunday_02:00_utc"
    
  dependency_updates:
    frequency: "quarterly"
    testing_