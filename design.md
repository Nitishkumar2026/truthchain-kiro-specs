# TruthChain - System Design Document

## 1. System Overview

TruthChain is a cloud-native platform that combines AI forensic analysis, blockchain verification, and explainable trust scoring to authenticate digital media. The system is designed for high-stakes institutional use in legal, electoral, and journalistic contexts.

### 1.1 Design Principles

- **Explainability First**: Every determination must be traceable and interpretable
- **Legal Grade**: All processes designed for admissibility in court
- **Tamper Evident**: Immutable audit trails using blockchain
- **Scalable**: Bharat-scale architecture supporting millions of verifications
- **Modular**: Independent services for flexibility and maintainability

## 2. System Architecture

### 2.1 High-Level Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         Users & Clients                          │
│  (Web UI, Mobile, API Clients, Institutional Integrations)      │
└────────────────────────────┬────────────────────────────────────┘
                             │
┌────────────────────────────┴────────────────────────────────────┐
│                     API Gateway & Load Balancer                  │
│              (Authentication, Rate Limiting, Routing)            │
└────────────────────────────┬────────────────────────────────────┘
                             │
        ┌────────────────────┼────────────────────┐
        │                    │                    │
┌───────▼────────┐  ┌────────▼────────┐  ┌───────▼────────┐
│  Web Frontend  │  │  API Services   │  │  Admin Portal  │
│   (React SPA)  │  │  (REST/GraphQL) │  │   (Management) │
└────────────────┘  └─────────┬───────┘  └────────────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                     │
┌───────▼────────┐  ┌─────────▼────────┐  ┌────────▼────────┐
│ Media Ingestion│  │  User Management │  │ Case Management │
│    Service     │  │     Service      │  │    Service      │
└───────┬────────┘  └──────────────────┘  └─────────────────┘
        │
        ├──────────────────┬──────────────────┬─────────────────┐
        │                  │                  │                 │
┌───────▼────────┐ ┌───────▼────────┐ ┌──────▼──────┐ ┌───────▼────────┐
│  Fingerprinting│ │  AI Forensics  │ │  Timeline   │ │   Blockchain   │
│     Engine     │ │     Engine     │ │Reconstruction│ │  Verification  │
└───────┬────────┘ └───────┬────────┘ └──────┬──────┘ └───────┬────────┘
        │                  │                  │                 │
        └──────────────────┴──────────────────┴─────────────────┘
                                    │
                    ┌───────────────┴───────────────┐
                    │                               │
            ┌───────▼────────┐          ┌──────────▼─────────┐
            │  Report Engine │          │  Notification      │
            │  (PDF/JSON)    │          │     Service        │
            └────────────────┘          └────────────────────┘
```


### 2.2 Component Architecture

#### 2.2.1 Frontend Layer

**Web Application (React + TypeScript)**
- Single Page Application with responsive design
- Material-UI component library for consistent UX
- Redux for state management
- Real-time updates via WebSocket
- Progressive Web App capabilities

**Key Features:**
- Drag-and-drop media upload
- Interactive trust score visualization
- Edit timeline viewer with zoom/pan
- Heat map overlay for manipulated regions
- Case management dashboard
- Multi-language support (i18n)

#### 2.2.2 API Gateway Layer

**Technology**: AWS API Gateway + AWS Application Load Balancer

**Responsibilities:**
- Request routing and load balancing
- Authentication and authorization (JWT tokens)
- Rate limiting and throttling
- API versioning
- Request/response transformation
- CORS handling
- DDoS protection (AWS Shield)

#### 2.2.3 Application Services Layer

**Media Ingestion Service**
- File upload handling (multipart, resumable)
- Format validation and sanitization
- Metadata extraction (ExifTool, MediaInfo)
- Cryptographic fingerprinting (SHA-256, perceptual hash)
- Storage orchestration (S3)
- Job queue management

**User Management Service**
- Authentication (OAuth 2.0, SAML)
- Role-based access control (RBAC)
- Organization and team management
- API key generation and management
- Audit logging

**Case Management Service**
- Case creation and organization
- Media file association
- Tagging and annotation
- Collaborative workflows
- Search and filtering
- Export functionality

**Report Generation Service**
- Template-based report generation
- PDF rendering (Puppeteer)
- JSON export
- Multi-language support
- Digital signature integration
- Certificate generation with QR codes

**Notification Service**
- Email notifications (AWS SES)
- Webhook delivery
- In-app notifications
- SMS alerts (AWS SNS)
- Event-driven architecture

#### 2.2.4 AI Forensics Engine

**Fingerprinting Module**
- Perceptual hashing (pHash, dHash)
- Camera sensor pattern noise (PRNU)
- JPEG compression analysis
- Color filter array (CFA) pattern detection
- Double JPEG detection

**Deepfake Detection Module**
- Facial reenactment detection (FaceForensics++)
- GAN fingerprint detection
- Temporal inconsistency analysis
- Audio-visual synchronization check
- Lip-sync verification
- Blink pattern analysis

**Manipulation Detection Module**
- Copy-move forgery detection
- Splicing detection
- Cloning detection
- Lighting inconsistency analysis
- Shadow and reflection analysis
- Noise pattern analysis
- Error Level Analysis (ELA)

**AI Model Stack:**
- PyTorch for deep learning models
- XceptionNet, EfficientNet for image classification
- 3D CNN for video analysis
- Transformer models for audio analysis
- Ensemble methods for improved accuracy

**Infrastructure:**
- GPU-accelerated inference (AWS EC2 P3/G4 instances)
- Model serving via TorchServe or TensorFlow Serving
- Model versioning and A/B testing
- Batch processing for efficiency
- Auto-scaling based on queue depth

#### 2.2.5 Timeline Reconstruction Engine

**Edit History Analysis**
- Compression cycle detection
- Re-encoding identification
- Software signature detection (Photoshop, GIMP, FFmpeg)
- Timestamp analysis
- Metadata timeline construction

**Visualization Engine**
- Chronological edit sequence
- Before/after comparison generation
- Region-based change highlighting
- Interactive timeline UI components

#### 2.2.6 Blockchain Verification Layer

**Technology**: Hyperledger Fabric (Permissioned Blockchain)

**Architecture:**
- Multi-organization consortium network
- Endorsement policies for verification records
- Private data collections for sensitive information
- Chaincode (smart contracts) for verification logic

**Stored Data:**
- Media fingerprints (hash only, not content)
- Verification results and trust scores
- Timestamps and chain of custody
- Attestations from authorized parties
- Certificate issuance records

**Integration:**
- REST API for blockchain operations
- Event listeners for real-time updates
- Certificate verification portal (public)

#### 2.2.7 Data Storage Layer

**Object Storage (AWS S3)**
- Original media files (encrypted)
- Processed artifacts
- Generated reports
- Tiered storage (S3 Standard, Glacier for archival)

**Relational Database (AWS RDS PostgreSQL)**
- User accounts and permissions
- Case metadata
- Verification results
- Audit logs
- Search indices

**Document Database (AWS DocumentDB/MongoDB)**
- Flexible metadata storage
- Analysis results (JSON)
- Timeline data structures

**Cache Layer (AWS ElastiCache Redis)**
- Session management
- API response caching
- Job queue (Bull/BullMQ)
- Real-time data

**Search Engine (AWS OpenSearch)**
- Full-text search
- Media fingerprint similarity search
- Advanced filtering and aggregations

## 3. Data Flow

### 3.1 Media Verification Flow

```
1. User uploads media file via Web UI or API
   ↓
2. API Gateway authenticates and routes request
   ↓
3. Media Ingestion Service:
   - Validates file format and size
   - Scans for malware
   - Extracts metadata
   - Generates cryptographic hash
   - Stores in S3
   - Creates verification job
   ↓
4. Job Queue dispatches to processing engines (parallel):
   ├─→ Fingerprinting Engine
   │   - Generates perceptual hashes
   │   - Analyzes compression artifacts
   │   - Detects camera signatures
   │
   ├─→ AI Forensics Engine
   │   - Runs deepfake detection models
   │   - Analyzes manipulation indicators
   │   - Calculates confidence scores
   │
   └─→ Timeline Reconstruction Engine
       - Identifies edit history
       - Detects software signatures
       - Reconstructs timeline
   ↓
5. Results aggregated and analyzed:
   - Calculate overall trust score
   - Generate explanations
   - Create visualizations
   ↓
6. Blockchain Verification:
   - Record verification results
   - Generate certificate
   - Create audit trail
   ↓
7. Report Generation:
   - Compile findings
   - Generate PDF/JSON report
   - Create visual assets
   ↓
8. Notification Service:
   - Notify user of completion
   - Trigger webhooks
   ↓
9. Results stored in database and returned to user
```

### 3.2 Certificate Verification Flow

```
1. User scans QR code or enters certificate ID
   ↓
2. Public verification portal queries blockchain
   ↓
3. Blockchain returns immutable verification record
   ↓
4. Portal displays:
   - Trust score
   - Verification timestamp
   - Certificate validity
   - Chain of custody
   - Public attestations
```

## 4. Technology Stack

### 4.1 Frontend

- **Framework**: React 18 with TypeScript
- **State Management**: Redux Toolkit
- **UI Library**: Material-UI (MUI)
- **Visualization**: D3.js, Chart.js, React-Vis
- **HTTP Client**: Axios
- **Real-time**: Socket.io-client
- **Internationalization**: react-i18next
- **Build Tool**: Vite
- **Testing**: Jest, React Testing Library, Cypress

### 4.2 Backend

- **Runtime**: Node.js 20 LTS
- **Framework**: Express.js / NestJS
- **Language**: TypeScript
- **API**: REST + GraphQL (Apollo Server)
- **Authentication**: Passport.js, JWT
- **Validation**: Joi, class-validator
- **ORM**: Prisma / TypeORM
- **Job Queue**: Bull / BullMQ with Redis
- **Testing**: Jest, Supertest

### 4.3 AI/ML Services

- **Framework**: Python 3.11, PyTorch 2.x
- **Model Serving**: TorchServe / FastAPI
- **Computer Vision**: OpenCV, PIL, scikit-image
- **Deep Learning**: torchvision, timm
- **Audio Processing**: librosa, pydub
- **Video Processing**: FFmpeg, PyAV
- **Forensics**: ExifTool, MediaInfo
- **Notebooks**: Jupyter for research

### 4.4 Blockchain

- **Platform**: Hyperledger Fabric 2.5
- **SDK**: Fabric SDK for Node.js
- **Chaincode**: Go or Node.js
- **Certificate Authority**: Fabric CA
- **Consensus**: Raft ordering service

### 4.5 Infrastructure (AWS)

**Compute:**
- EC2 instances (t3, c5, p3, g4)
- ECS/EKS for container orchestration
- Lambda for serverless functions
- Auto Scaling Groups

**Storage:**
- S3 for object storage
- EBS for block storage
- EFS for shared file systems

**Database:**
- RDS PostgreSQL (Multi-AZ)
- DocumentDB
- ElastiCache Redis
- OpenSearch

**Networking:**
- VPC with public/private subnets
- Application Load Balancer
- CloudFront CDN
- Route 53 DNS
- VPN/Direct Connect for enterprise

**Security:**
- IAM for access management
- KMS for encryption keys
- Secrets Manager
- WAF for application firewall
- Shield for DDoS protection
- GuardDuty for threat detection

**Monitoring:**
- CloudWatch for metrics and logs
- X-Ray for distributed tracing
- SNS for alerts
- CloudTrail for audit logs

**DevOps:**
- CodePipeline for CI/CD
- CodeBuild for builds
- CodeDeploy for deployments
- ECR for container registry
- CloudFormation / Terraform for IaC

### 4.6 Third-Party Services

- **Email**: AWS SES
- **SMS**: AWS SNS
- **CDN**: AWS CloudFront
- **Monitoring**: Datadog / New Relic
- **Error Tracking**: Sentry
- **Analytics**: Mixpanel / Amplitude
- **Documentation**: Swagger/OpenAPI

## 5. Security Architecture

### 5.1 Authentication & Authorization

- Multi-factor authentication (MFA) for privileged accounts
- OAuth 2.0 / SAML 2.0 for enterprise SSO
- JWT tokens with short expiration (15 minutes)
- Refresh token rotation
- Role-based access control (RBAC)
- Attribute-based access control (ABAC) for fine-grained permissions

### 5.2 Data Protection

**Encryption at Rest:**
- S3 server-side encryption (SSE-KMS)
- RDS encryption with AWS KMS
- EBS volume encryption
- Application-level encryption for sensitive fields

**Encryption in Transit:**
- TLS 1.3 for all communications
- Certificate pinning for mobile apps
- VPN for internal service communication

**Data Sanitization:**
- Input validation and sanitization
- File type verification (magic bytes)
- Malware scanning (ClamAV)
- Size and rate limits

### 5.3 Network Security

- VPC isolation with security groups
- Private subnets for backend services
- NAT Gateway for outbound traffic
- WAF rules for common attacks (SQL injection, XSS)
- DDoS protection with AWS Shield
- IP whitelisting for admin access

### 5.4 Application Security

- OWASP Top 10 mitigation
- Content Security Policy (CSP)
- CORS configuration
- Rate limiting per user/IP
- API key rotation
- Secure session management
- SQL injection prevention (parameterized queries)
- XSS prevention (output encoding)

### 5.5 Audit & Compliance

- Comprehensive audit logging
- Immutable log storage
- Regular security assessments
- Penetration testing (quarterly)
- Compliance certifications (ISO 27001, SOC 2)
- Data residency compliance (India)
- DPDP Act compliance

## 6. Scalability & Performance

### 6.1 Horizontal Scaling

- Stateless application services
- Auto-scaling groups based on CPU/memory/queue depth
- Load balancing across availability zones
- Database read replicas
- Sharding strategy for high-volume data

### 6.2 Caching Strategy

- CDN caching for static assets (CloudFront)
- API response caching (Redis)
- Database query caching
- Browser caching with appropriate headers
- Cache invalidation strategies

### 6.3 Asynchronous Processing

- Job queues for long-running tasks
- Worker pools for parallel processing
- Batch processing for efficiency
- Priority queues for urgent requests
- Dead letter queues for failed jobs

### 6.4 Performance Optimization

- Database indexing strategy
- Query optimization
- Connection pooling
- Lazy loading and pagination
- Image/video optimization
- Code splitting and bundling
- GPU acceleration for AI inference

### 6.5 Monitoring & Observability

**Metrics:**
- Request latency (p50, p95, p99)
- Error rates
- Throughput (requests/second)
- Resource utilization (CPU, memory, GPU)
- Queue depth and processing time

**Logging:**
- Structured logging (JSON)
- Centralized log aggregation
- Log levels (DEBUG, INFO, WARN, ERROR)
- Correlation IDs for request tracing

**Alerting:**
- Threshold-based alerts
- Anomaly detection
- On-call rotation
- Incident management workflow

## 7. Deployment Architecture

### 7.1 Environment Strategy

- **Development**: Single-instance, shared resources
- **Staging**: Production-like, isolated
- **Production**: Multi-AZ, high availability

### 7.2 CI/CD Pipeline

```
Code Commit → GitHub
    ↓
Automated Tests (Unit, Integration)
    ↓
Code Quality Checks (ESLint, SonarQube)
    ↓
Security Scanning (Snyk, Trivy)
    ↓
Build Docker Images
    ↓
Push to ECR
    ↓
Deploy to Staging (Auto)
    ↓
Integration Tests
    ↓
Manual Approval
    ↓
Deploy to Production (Blue-Green)
    ↓
Health Checks
    ↓
Rollback if needed
```

### 7.3 Disaster Recovery

- Multi-AZ deployment for high availability
- Automated backups (daily, retained 30 days)
- Point-in-time recovery for databases
- Cross-region replication for critical data
- Disaster recovery plan with RTO < 4 hours, RPO < 1 hour
- Regular DR drills (quarterly)

## 8. Data Model

### 8.1 Core Entities

**User**
- id, email, name, role, organization_id
- authentication_method, mfa_enabled
- created_at, last_login_at

**Organization**
- id, name, type (court, media, government, etc.)
- subscription_tier, quota_limits
- settings (JSON)

**MediaFile**
- id, filename, file_type, file_size
- upload_timestamp, uploader_id
- storage_path, fingerprint_sha256
- perceptual_hash, metadata (JSON)

**VerificationJob**
- id, media_file_id, status
- created_at, completed_at
- priority, retry_count

**VerificationResult**
- id, media_file_id, job_id
- trust_score, confidence_level
- manipulation_detected, deepfake_detected
- findings (JSON), explanations (JSON)
- timeline_data (JSON)

**BlockchainRecord**
- id, media_file_id, transaction_id
- blockchain_hash, certificate_id
- timestamp, attestations (JSON)

**Case**
- id, name, description, organization_id
- created_by, status, tags
- created_at, updated_at

**CaseMedia**
- case_id, media_file_id
- notes, annotations (JSON)

**AuditLog**
- id, user_id, action, resource_type, resource_id
- ip_address, user_agent, timestamp
- details (JSON)

### 8.2 Relationships

- User belongs to Organization
- MediaFile belongs to User (uploader)
- VerificationJob belongs to MediaFile
- VerificationResult belongs to MediaFile and Job
- BlockchainRecord belongs to MediaFile
- Case belongs to Organization
- CaseMedia links Case and MediaFile (many-to-many)

## 9. API Design

### 9.1 RESTful Endpoints

**Authentication**
- POST /api/v1/auth/login
- POST /api/v1/auth/logout
- POST /api/v1/auth/refresh
- POST /api/v1/auth/mfa/verify

**Media Upload**
- POST /api/v1/media/upload
- POST /api/v1/media/batch-upload
- GET /api/v1/media/:id
- DELETE /api/v1/media/:id

**Verification**
- POST /api/v1/verify
- GET /api/v1/verify/:jobId/status
- GET /api/v1/verify/:jobId/result
- GET /api/v1/media/:id/verification

**Reports**
- GET /api/v1/reports/:id/pdf
- GET /api/v1/reports/:id/json
- GET /api/v1/reports/:id/certificate

**Blockchain**
- GET /api/v1/blockchain/verify/:certificateId
- POST /api/v1/blockchain/attest/:mediaId

**Cases**
- POST /api/v1/cases
- GET /api/v1/cases/:id
- PUT /api/v1/cases/:id
- DELETE /api/v1/cases/:id
- POST /api/v1/cases/:id/media
- GET /api/v1/cases/:id/media

**Search**
- GET /api/v1/search/media?q=...
- GET /api/v1/search/cases?q=...

### 9.2 WebSocket Events

- verification.started
- verification.progress
- verification.completed
- verification.failed

### 9.3 Webhooks

- verification.completed
- certificate.issued
- case.updated

## 10. Multi-Language Support

### 10.1 Supported Languages

- English (en)
- Hindi (hi)
- Tamil (ta)
- Telugu (te)
- Bengali (bn)
- Marathi (mr)
- Gujarati (gu)

### 10.2 Implementation

- i18n library for frontend (react-i18next)
- Translation files (JSON) for each language
- Backend API returns localized messages
- Report templates in multiple languages
- RTL support for future languages
- Language detection from browser/user preference

## 11. Legal and Compliance Considerations

### 11.1 Evidence Handling

- Maintain original file integrity (no modifications)
- Cryptographic proof of authenticity
- Timestamping with trusted time sources
- Chain of custody documentation
- Digital signatures for reports

### 11.2 Data Retention

- Legal cases: 7 years minimum
- Non-legal content: User-configurable (default 1 year)
- Audit logs: 5 years
- Blockchain records: Permanent

### 11.3 Compliance Standards

- Digital Personal Data Protection Act (DPDP)
- Indian Evidence Act
- ISO 27001 (Information Security)
- SOC 2 Type II
- WCAG 2.1 Level AA (Accessibility)

## 12. Future Enhancements

### Phase 2 (6-12 months)
- Mobile applications (iOS, Android)
- Real-time video stream verification
- Advanced AI model marketplace
- Social media platform integrations

### Phase 3 (12-24 months)
- Cross-border verification network
- Automated misinformation campaign detection
- VR/AR content verification
- Federated learning for privacy-preserving model training

## 13. Success Metrics and KPIs

**Technical Metrics:**
- Uptime: 99.9%
- Average verification time: <1 minute
- API response time: <200ms (p95)
- Detection accuracy: >95%
- False positive rate: <5%

**Business Metrics:**
- Active institutional users: 100+ (Year 1)
- Verifications per day: 10,000+
- User satisfaction: 90%+
- Legal case usage: 50+ cases
- Revenue growth: 200% YoY

**Impact Metrics:**
- Misinformation prevented
- Legal cases supported
- Media credibility improved
- Public trust enhanced
