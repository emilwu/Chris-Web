# Hwayo 生產環境配置 SOT

## 文件資訊
- **文件名稱**: 生產環境配置唯一真實來源 (SOT)
- **環境類型**: Production Environment
- **建立日期**: 2025/05/31
- **階段**: 子任務 5.3 - 定義詳細環境配置並確立 SOT
- **狀態**: 已完成
- **維護責任**: DevOps 團隊 + SRE 團隊
- **參考文件**: 
  - [`docs/architecture/system_architecture.md`](../architecture/system_architecture.md)
  - [`docs/environment_configs/staging_env_sot.md`](staging_env_sot.md)
  - [`planning/project_development_dod_guide.md`](../../planning/project_development_dod_guide.md)

## 1. 環境概述

### 1.1 環境目的
- **主要用途**: 正式生產服務、客戶服務、業務營運
- **使用者**: 實驗室人員、客戶、管理人員
- **特性**: 高可用性、高效能、高安全性、完整合規
- **資料管理**: 真實業務資料、嚴格的資料保護和備份

### 1.2 環境特點
- ✅ 99.9% 可用性保證
- ✅ 企業級安全防護
- ✅ 24/7 監控和告警
- ✅ 自動化故障恢復
- ✅ 完整的合規和審計
- ✅ 多區域災難恢復

## 2. 作業系統與基礎設施

### 2.1 雲端基礎設施
- **雲端平台**: AWS (主要) / Multi-Cloud 策略
- **作業系統**: Ubuntu 22.04 LTS (強化版)
- **容器平台**: Amazon EKS 1.28+ (Kubernetes)
- **服務網格**: Istio 1.19+ (進階流量管理)
- **負載均衡**: Application Load Balancer + Global Load Balancer

### 2.2 多區域架構
```yaml
multi_region_architecture:
  primary_region: "ap-northeast-1"  # Tokyo
  secondary_region: "ap-southeast-1"  # Singapore
  dr_region: "us-west-2"  # Oregon (災難恢復)
  
  traffic_distribution:
    primary: "80%"
    secondary: "20%"
    
  failover_strategy:
    automatic: true
    rto: "15 minutes"
    rpo: "5 minutes"
```

### 2.3 網路架構
```yaml
network_architecture:
  primary_vpc:
    cidr: "10.0.0.0/16"
    region: "ap-northeast-1"
    
    subnets:
      public:
        - "10.0.1.0/24"  # ALB, NAT Gateway (AZ-1a)
        - "10.0.2.0/24"  # ALB, NAT Gateway (AZ-1c)
        - "10.0.3.0/24"  # ALB, NAT Gateway (AZ-1d)
      
      private_app:
        - "10.0.10.0/24" # Application servers (AZ-1a)
        - "10.0.11.0/24" # Application servers (AZ-1c)
        - "10.0.12.0/24" # Application servers (AZ-1d)
      
      private_db:
        - "10.0.20.0/24" # Database servers (AZ-1a)
        - "10.0.21.0/24" # Database servers (AZ-1c)
        - "10.0.22.0/24" # Database servers (AZ-1d)
      
      private_mgmt:
        - "10.0.30.0/24" # Management, monitoring (AZ-1a)
        - "10.0.31.0/24" # Management, monitoring (AZ-1c)

  secondary_vpc:
    cidr: "10.1.0.0/16"
    region: "ap-southeast-1"
    # 類似的子網路配置

  connectivity:
    vpc_peering: "enabled"
    transit_gateway: "enabled"
    direct_connect: "planned"
    
  security:
    waf: "enabled"
    ddos_protection: "enabled"
    network_acls: "restrictive"
```

## 3. 核心技術棧版本

### 3.1 Node.js 環境
```yaml
runtime:
  nodejs: "18.19.0"  # LTS 版本，定期更新
  npm: "10.2.3"
  typescript: "5.3.3"
  
package_management:
  lock_file_enforcement: true
  vulnerability_scanning: true
  license_compliance: true
  
security_hardening:
  node_options: "--max-old-space-size=2048 --enable-source-maps"
  process_isolation: true
  privilege_escalation: false
```

### 3.2 容器化與編排
```yaml
container_platform:
  kubernetes: "1.28.4"
  eks_version: "1.28"
  
  cluster_config:
    node_groups:
      system:
        instance_type: "m5.large"
        min_size: 3
        max_size: 6
        desired_size: 3
        
      application:
        instance_type: "m5.xlarge"
        min_size: 6
        max_size: 20
        desired_size: 8
        
      compute_intensive:
        instance_type: "c5.2xlarge"
        min_size: 2
        max_size: 10
        desired_size: 3
        
  service_mesh:
    istio_version: "1.19.3"
    features:
      - "traffic_management"
      - "security_policies"
      - "observability"
      - "circuit_breaker"
      
container_registry:
  primary: "Amazon ECR"
  scanning: "enhanced"
  encryption: "enabled"
  
  image_policies:
    vulnerability_threshold: "high"
    base_image_updates: "automatic"
    retention: "90 days"
```

## 4. 資料庫配置

### 4.1 PostgreSQL (主資料庫)
```yaml
database:
  engine: "Amazon RDS PostgreSQL"
  version: "15.5"
  
  primary_instance:
    instance_class: "db.r6g.2xlarge"  # 8 vCPU, 64GB RAM
    storage: "1000GB GP3"
    iops: "12000"
    storage_encrypted: true
    kms_key: "alias/hwayo-prod-db"
    
  read_replicas:
    count: 3
    instance_class: "db.r6g.xlarge"  # 4 vCPU, 32GB RAM
    regions: ["ap-northeast-1", "ap-southeast-1"]
    
  backup_config:
    automated_backup: true
    backup_retention: "30 days"
    backup_window: "03:00-04:00 UTC"
    copy_tags_to_snapshot: true
    
  maintenance:
    maintenance_window: "sun:04:00-sun:05:00"
    auto_minor_version_upgrade: false
    
connection_config:
  host: "hwayo-prod-cluster.cluster-xxx.ap-northeast-1.rds.amazonaws.com"
  port: 5432
  database: "hwayo_production"
  username: "hwayo_prod_user"
  password: "${secrets:hwayo/prod/database:DB_PASSWORD}"
  ssl_mode: "require"
  ssl_cert_verification: true
  
connection_pool:
  min_connections: 10
  max_connections: 100
  idle_timeout: "600s"
  connection_timeout: "30s"
  statement_timeout: "30s"
  
performance_insights:
  enabled: true
  retention: "7 days"
  
monitoring:
  enhanced_monitoring: true
  monitoring_interval: 60
  
extensions:
  - "uuid-ossp"
  - "pg_trgm"
  - "btree_gin"
  - "pg_stat_statements"
  - "pg_audit"
```

### 4.2 MongoDB (審計日誌)
```yaml
mongodb:
  service: "MongoDB Atlas"
  version: "6.0.12"
  
  cluster_config:
    tier: "M30"  # 8GB RAM, 40GB Storage
    region: "AP-NORTHEAST-1"
    cluster_type: "REPLICASET"
    num_shards: 1
    replication_factor: 3
    
  backup_config:
    cloud_backup: true
    snapshot_interval: "6 hours"
    retention: "30 days"
    point_in_time_recovery: true
    
  security:
    network_access:
      - ip: "10.0.0.0/16"  # VPC CIDR
      - ip: "10.1.0.0/16"  # Secondary VPC CIDR
    database_access:
      username: "hwayo_audit_prod"
      password: "${secrets:hwayo/prod/mongodb:MONGODB_PASSWORD}"
      roles: ["readWrite"]
      
connection_config:
  connection_string: "${secrets:hwayo/prod/mongodb:MONGODB_CONNECTION_STRING}"
  ssl: true
  auth_source: "admin"
  read_preference: "secondaryPreferred"
  
collections:
  audit_logs:
    indexes:
      - fields: ["timestamp", "user_id"]
        options: {"background": true}
      - fields: ["action_type", "timestamp"]
        options: {"background": true}
      - fields: ["resource_id", "timestamp"]
        options: {"background": true}
    sharding_key: "timestamp"
    
  system_events:
    indexes:
      - fields: ["timestamp", "event_type"]
        options: {"background": true}
      - fields: ["severity", "timestamp"]
        options: {"background": true}
    ttl_index:
      field: "timestamp"
      expire_after: "2555 days"  # 7 years
      
  user_activities:
    indexes:
      - fields: ["user_id", "timestamp"]
        options: {"background": true}
      - fields: ["session_id", "timestamp"]
        options: {"background": true}
```

### 4.3 Redis (快取與訊息佇列)
```yaml
redis:
  service: "Amazon ElastiCache"
  version: "7.2.3"
  
  cluster_config:
    mode: "cluster"
    node_type: "cache.r6g.large"  # 2 vCPU, 13.07GB RAM
    num_cache_nodes: 6
    num_node_groups: 3
    replicas_per_node_group: 1
    
  security:
    auth_token: "${secrets:hwayo/prod/redis:REDIS_AUTH_TOKEN}"
    encryption_at_rest: true
    encryption_in_transit: true
    
  backup_config:
    snapshot_retention_limit: 7
    snapshot_window: "05:00-06:00"
    
connection_config:
  configuration_endpoint: "hwayo-prod-redis.xxx.cache.amazonaws.com:6379"
  ssl: true
  auth_token: "${secrets:hwayo/prod/redis:REDIS_AUTH_TOKEN}"
  
databases:
  cache: 0
  sessions: 1
  queue: 2
  rate_limiting: 3
  
memory_management:
  max_memory_policy: "allkeys-lru"
  reserved_memory: "25%"
  
monitoring:
  cloudwatch_metrics: true
  slow_log: true
  slow_log_threshold: "10000"  # 10ms
```

## 5. 應用服務配置

### 5.1 Kubernetes 部署配置
```yaml
kubernetes:
  namespace: "hwayo-production"
  
  resource_quotas:
    requests.cpu: "20"
    requests.memory: "40Gi"
    limits.cpu: "40"
    limits.memory: "80Gi"
    persistentvolumeclaims: "10"
    
  network_policies:
    default_deny: true
    allow_ingress: "from_istio_gateway"
    allow_egress: "to_databases_and_external"
    
  deployments:
    web_app:
      replicas: 4
      image: "${ECR_REGISTRY}/hwayo-web:${IMAGE_TAG}"
      strategy:
        type: "RollingUpdate"
        rolling_update:
          max_unavailable: "25%"
          max_surge: "25%"
      resources:
        requests:
          cpu: "200m"
          memory: "512Mi"
        limits:
          cpu: "1000m"
          memory: "1Gi"
      security_context:
        run_as_non_root: true
        run_as_user: 1000
        read_only_root_filesystem: true
        
    api_gateway:
      replicas: 6
      image: "${ECR_REGISTRY}/hwayo-api:${IMAGE_TAG}"
      strategy:
        type: "RollingUpdate"
        rolling_update:
          max_unavailable: "16%"
          max_surge: "25%"
      resources:
        requests:
          cpu: "500m"
          memory: "1Gi"
        limits:
          cpu: "2000m"
          memory: "2Gi"
      hpa:
        min_replicas: 6
        max_replicas: 20
        target_cpu_utilization: 70
        target_memory_utilization: 80
        
    auth_service:
      replicas: 4
      image: "${ECR_REGISTRY}/hwayo-auth:${IMAGE_TAG}"
      resources:
        requests:
          cpu: "200m"
          memory: "512Mi"
        limits:
          cpu: "1000m"
          memory: "1Gi"
      hpa:
        min_replicas: 4
        max_replicas: 12
        target_cpu_utilization: 70
        
    workflow_engine:
      replicas: 4
      image: "${ECR_REGISTRY}/hwayo-workflow:${IMAGE_TAG}"
      resources:
        requests:
          cpu: "500m"
          memory: "1Gi"
        limits:
          cpu: "2000m"
          memory: "2Gi"
      hpa:
        min_replicas: 4
        max_replicas: 16
        target_cpu_utilization: 70
        
    notification_service:
      replicas: 3
      image: "${ECR_REGISTRY}/hwayo-notification:${IMAGE_TAG}"
      resources:
        requests:
          cpu: "200m"
          memory: "512Mi"
        limits:
          cpu: "1000m"
          memory: "1Gi"
      hpa:
        min_replicas: 3
        max_replicas: 10
        target_cpu_utilization: 70
        
    report_generator:
      replicas: 3
      image: "${ECR_REGISTRY}/hwayo-report:${IMAGE_TAG}"
      resources:
        requests:
          cpu: "1000m"
          memory: "2Gi"
        limits:
          cpu: "4000m"
          memory: "4Gi"
      hpa:
        min_replicas: 3
        max_replicas: 12
        target_cpu_utilization: 60
```

### 5.2 Istio 服務網格配置
```yaml
istio_config:
  gateway:
    name: "hwayo-gateway"
    hosts: ["hwayo.com", "www.hwayo.com"]
    tls:
      mode: "SIMPLE"
      credential_name: "hwayo-tls-secret"
      
  virtual_services:
    web_app:
      hosts: ["hwayo.com"]
      http:
        - match:
            - uri:
                prefix: "/api"
          route:
            - destination:
                host: "api-gateway"
                port: 80
        - match:
            - uri:
                prefix: "/"
          route:
            - destination:
                host: "web-app"
                port: 80
                
  destination_rules:
    api_gateway:
      traffic_policy:
        load_balancer:
          simple: "LEAST_CONN"
        circuit_breaker:
          consecutive_errors: 5
          interval: "30s"
          base_ejection_time: "30s"
          max_ejection_percent: 50
          
  security_policies:
    authentication:
      policy: "STRICT"
      mtls_mode: "STRICT"
      
    authorization:
      rules:
        - from:
            - source:
                principals: ["cluster.local/ns/istio-system/sa/istio-ingressgateway-service-account"]
          to:
            - operation:
                methods: ["GET", "POST", "PUT", "DELETE"]
```

## 6. 檔案儲存與 CDN

### 6.1 Amazon S3 配置
```yaml
s3_storage:
  buckets:
    reports:
      name: "hwayo-reports-production"
      region: "ap-northeast-1"
      versioning: true
      encryption:
        type: "aws:kms"
        kms_key: "alias/hwayo-prod-s3"
      lifecycle:
        - id: "reports_lifecycle"
          status: "Enabled"
          transitions:
            - days: 30
              storage_class: "STANDARD_IA"
            - days: 90
              storage_class: "GLACIER"
            - days: 2555  # 7 years
              storage_class: "DEEP_ARCHIVE"
      cross_region_replication:
        destination: "hwayo-reports-production-replica"
        region: "ap-southeast-1"
        
    attachments:
      name: "hwayo-attachments-production"
      region: "ap-northeast-1"
      versioning: true
      encryption:
        type: "aws:kms"
        kms_key: "alias/hwayo-prod-s3"
      lifecycle:
        - id: "attachments_lifecycle"
          status: "Enabled"
          transitions:
            - days: 30
              storage_class: "STANDARD_IA"
            - days: 365
              storage_class: "GLACIER"
            - days: 1095  # 3 years
              storage_class: "DEEP_ARCHIVE"
              
    templates:
      name: "hwayo-templates-production"
      region: "ap-northeast-1"
      versioning: true
      encryption:
        type: "aws:kms"
        kms_key: "alias/hwayo-prod-s3"
      public_access_block: true
      
  access_control:
    iam_roles:
      - "hwayo-prod-app-role"
      - "hwayo-prod-backup-role"
      
    bucket_policies:
      - sid: "AllowAppAccess"
        effect: "Allow"
        principal:
          aws: "arn:aws:iam::ACCOUNT:role/hwayo-prod-app-role"
        actions:
          - "s3:GetObject"
          - "s3:PutObject"
          - "s3:DeleteObject"
        resources:
          - "arn:aws:s3:::hwayo-*-production/*"
        condition:
          ip_address:
            aws:source_ip: ["10.0.0.0/16", "10.1.0.0/16"]
```

### 6.2 CloudFront CDN 配置
```yaml
cloudfront:
  distributions:
    static_assets:
      origin:
        domain: "hwayo-attachments-production.s3.ap-northeast-1.amazonaws.com"
        origin_access_control: "enabled"
        
      cache_behaviors:
        default:
          path_pattern: "*"
          target_origin: "s3-origin"
          viewer_protocol_policy: "redirect-to-https"
          allowed_methods: ["GET", "HEAD", "OPTIONS"]
          cached_methods: ["GET", "HEAD"]
          ttl:
            default: 86400  # 1 day
            max: 31536000   # 1 year
            min: 0
          compress: true
          
        images:
          path_pattern: "*.jpg,*.png,*.gif,*.webp"
          ttl:
            default: 604800  # 7 days
            max: 31536000    # 1 year
            
        pdfs:
          path_pattern: "*.pdf"
          ttl:
            default: 3600    # 1 hour
            max: 86400       # 1 day
            
      security:
        waf_web_acl: "hwayo-prod-waf"
        ssl_certificate: "arn:aws:acm:us-east-1:ACCOUNT:certificate/xxx"
        minimum_protocol_version: "TLSv1.2_2021"
        
      geo_restrictions:
        restriction_type: "whitelist"
        locations: ["JP", "TW", "SG", "US", "EU"]
        
    reports_cdn:
      origin:
        domain: "hwayo-reports-production.s3.ap-northeast-1.amazonaws.com"
        origin_access_control: "enabled"
        
      cache_behaviors:
        default:
          path_pattern: "*"
          viewer_protocol_policy: "https-only"
          allowed_methods: ["GET", "HEAD"]
          ttl:
            default: 3600    # 1 hour
            max: 86400       # 1 day
            min: 0
            
      security:
        signed_urls: true
        signed_url_ttl: 3600  # 1 hour
        trusted_signers: ["self"]
        
      logging:
        enabled: true
        bucket: "hwayo-cloudfront-logs-production"
        prefix: "reports/"
```

## 7. 安全配置

### 7.1 網路安全
```yaml
network_security:
  waf:
    web_acl: "hwayo-prod-waf"
    rules:
      - name: "AWSManagedRulesCommonRuleSet"
        priority: 1
        action: "BLOCK"
        
      - name: "AWSManagedRulesKnownBadInputsRuleSet"
        priority: 2
        action: "BLOCK"
        
      - name: "AWSManagedRulesSQLiRuleSet"
        priority: 3
        action: "BLOCK"
        
      - name: "RateLimitRule"
        priority: 4
        action: "BLOCK"
        rate_limit: 2000  # requests per 5 minutes
        
      - name: "GeoBlockRule"
        priority: 5
        action: "BLOCK"
        geo_match:
          country_codes: ["CN", "RU", "KP"]  # 根據需求調整
          
  ddos_protection:
    shield_advanced: true
    response_team: true
    
  security_groups:
    alb_sg:
      name: "hwayo-prod-alb-sg"
      ingress:
        - port: 80
          protocol: "TCP"
          source: "0.0.0.0/0"
          description: "HTTP from internet"
        - port: 443
          protocol: "TCP"
          source: "0.0.0.0/0"
          description: "HTTPS from internet"
      egress:
        - port: "all"
          protocol: "all"
          destination: "app_sg"
          
    app_sg:
      name: "hwayo-prod-app-sg"
      ingress:
        - port: 3000-3010
          protocol: "TCP"
          source: "alb_sg"
          description: "App ports from ALB"
        - port: 22
          protocol: "TCP"
          source: "bastion_sg"
          description: "SSH from bastion"
      egress:
        - port: 443
          protocol: "TCP"
          destination: "0.0.0.0/0"
          description: "HTTPS to internet"
        - port: 5432
          protocol: "TCP"
          destination: "db_sg"
          description: "PostgreSQL"
        - port: 27017
          protocol: "TCP"
          destination: "db_sg"
          description: "MongoDB"
        - port: 6379
          protocol: "TCP"
          destination: "db_sg"
          description: "Redis"
          
    db_sg:
      name: "hwayo-prod-db-sg"
      ingress:
        - port: 5432
          protocol: "TCP"
          source: "app_sg"
          description: "PostgreSQL from app"
        - port: 27017
          protocol: "TCP"
          source: "app_sg"
          description: "MongoDB from app"
        - port: 6379
          protocol: "TCP"
          source: "app_sg"
          description: "Redis from app"
      egress: []
```

### 7.2 身份驗證與授權
```yaml
authentication:
  method: "JWT + OAuth2"
  
  jwt_config:
    algorithm: "RS256"
    issuer: "https://hwayo.com"
    audience: "hwayo-production"
    access_token_expiry: "15m"
    refresh_token_expiry: "7d"
    key_rotation: "monthly"
    
  oauth2_providers:
    google:
      enabled: true
      client_id: "${secrets:hwayo/prod/oauth:GOOGLE_CLIENT_ID}"
      client_secret: "${secrets:hwayo/prod/oauth:GOOGLE_CLIENT_SECRET}"
      
    microsoft:
      enabled: true
      client_id: "${secrets:hwayo/prod/oauth:MICROSOFT_CLIENT_ID}"
      client_secret: "${secrets:hwayo/prod/oauth:MICROSOFT_CLIENT_SECRET}"
      
  password_policy:
    min_length: 12
    require_uppercase: true
    require_lowercase: true
    require_numbers: true
    require_symbols: true
    max_age: "90 days"
    history: 12
    lockout_threshold: 5
    lockout_duration: "30 minutes"
    
  mfa:
    enabled: true
    methods: ["totp", "sms", "email"]
    backup_codes: true
    
authorization:
  model: "RBAC + ABAC"
  
  roles:
    super_admin:
      permissions: ["*"]
      mfa_required: true
      
    lab_admin:
      permissions:
        - "manage:lab_settings"
        - "manage:users"
        - "read:all_reports"
        - "manage:templates"
        
    senior_researcher:
      permissions:
        - "read:all_experiments"
        - "write:own_experiments"
        - "review:assigned_experiments"
        - "generate:reports"
        
    researcher:
      permissions:
        - "read:own_experiments"
        - "write:own_experiments"
        - "read:templates"
        
    reviewer:
      permissions:
        - "read:assigned_experiments"
        - "write:reviews"
        - "approve:reports"
        
    customer:
      permissions:
        - "read:own_reports"
        - "download:own_reports"
        - "read:own_experiments_status"
        
  attribute_policies:
    data_classification:
      - if: "data.classification == 'confidential'"
        then: "require:clearance_level >= 3"
      - if: "data.classification == 'restricted'"
        then: "require:clearance_level >= 2"
        
    time_based:
      - if: "time.hour < 6 OR time.hour > 22"
        then: "require:emergency_access_approval"
        
    location_based:
      - if: "request.ip NOT IN allowed_ip_ranges"
        then: "require:additional_verification"
```

### 7.3 資料加密與金鑰管理
```yaml
encryption:
  data_at_rest:
    databases:
      postgresql: "AES-256 (AWS KMS)"
      mongodb: "AES-256 (MongoDB Atlas)"
      redis: "AES-256 (AWS KMS)"
      
    file_storage:
      s3: "AES-256 (AWS KMS)"
      ebs: "AES-256 (AWS KMS)"
      
    backups:
      database_backups: "AES-256 (AWS KMS)"
      file_backups: "AES-256 (AWS KMS)"
      
  data_in_transit:
    external_api: "TLS 1.3"
    internal_services: "mTLS (Istio)"
    database_connections: "SSL/TLS"
    
  application_level:
    sensitive_fields:
      - field: "user_passwords"
        algorithm: "bcrypt"
        rounds: 12
      - field: "customer_personal_data"
        algorithm: "AES-256-GCM"
        key_source: "aws_kms"
      - field: "financial_information"
        algorithm: "AES-256-GCM"
        key_source: "aws_kms"
      - field: "experiment_data"
        algorithm: "AES-256-GCM"
        key_source: "aws_kms"
        
key_management:
  provider: "AWS KMS"
  
  keys:
    database_encryption:
      alias: "alias/hwayo-prod-db"
      key_spec: "SYMMETRIC_DEFAULT"
      key_usage: "ENCRYPT_DECRYPT"
      rotation: "annual"
      
    s3_encryption:
      alias: "alias/hwayo-prod-s3"
      key_spec: "SYMMETRIC_DEFAULT"
      key_usage: "ENCRYPT_DECRYPT"
      rotation: "annual"
      
    application_encryption:
      alias: "alias/hwayo-prod-app"
      key_spec: "SYMMETRIC_DEFAULT"
      key_usage: "ENCRYPT_DECRYPT"
      rotation: "quarterly"
      
    jwt_signing:
      alias: "alias/hwayo-prod-jwt"
      key_spec: "RSA_2048"
      key_usage: "SIGN_VERIFY"
      rotation: "monthly"
      
  access_policies:
    database_key:
      principals: ["arn:aws:iam::ACCOUNT:role/hwayo-prod-db-role"]
      actions: ["kms:Decrypt", "kms:GenerateDataKey"]
      
    application_key:
      principals: ["arn:aws:iam::ACCOUNT:role/hwayo-prod-app-role"]
      actions: ["kms:Decrypt", "kms:Encrypt", "kms:GenerateDataKey"]
```

## 8. 監控與可觀測性

### 8.1 應用程式效能監控 (APM)
```yaml
apm:
  provider: "New Relic / Datadog"
  
  instrumentation:
    nodejs: "automatic"
    custom_metrics: true
    distributed_tracing: