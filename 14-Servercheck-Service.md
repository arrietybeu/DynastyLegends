# Servercheck Service - Server Health Check Service

## Tổng quan

**Servercheck** là service kiểm tra sức khỏe server của hệ thống JWS2, chịu trách nhiệm giám sát và kiểm tra trạng thái của tất cả các service trong hệ thống, đảm bảo tính khả dụng và hiệu suất của toàn bộ hệ thống.

## Thông tin cơ bản

- **Tên service**: servercheck
- **Port chính**: gRPC (internal)
- **Server ID**: ServerCheck1
- **Protocol**: gRPC, HTTP
- **Ngôn ngữ**: Go (Golang)
- **Kiến trúc**: Health check service với FeiShu integration

## Cấu trúc thư mục

```
servercheck/
├── checker/                 # Checker implementation
│   ├── battlex/            # Battlex checker
│   │   ├── check.go        # Check implementation
│   │   ├── check_test.go   # Check tests
│   │   ├── mgr.go          # Manager
│   │   └── tcp.go          # TCP checker
│   ├── chatx/              # Chatx checker
│   │   ├── check.go        # Check implementation
│   │   ├── check_test.go   # Check tests
│   │   ├── README.md       # Chatx checker readme
│   │   └── tcp.go          # TCP checker
│   ├── checker.go          # Main checker
│   ├── checker_api.go      # Checker API
│   ├── checker_robot.go    # Checker robot
│   ├── checker_srv.go      # Checker service
│   ├── checker_test.go     # Checker tests
│   ├── condition.go        # Condition handling
│   ├── const.go            # Constants
│   ├── event_auth.go       # Auth events
│   ├── event_battlex.go    # Battlex events
│   ├── event_clientbi.go   # ClientBI events
│   ├── event_cometx.go     # Cometx events
│   ├── event_crossx.go     # Crossx events
│   ├── event_gamex.go      # Gamex events
│   ├── event_gate.go       # Gate events
│   ├── event_notice.go     # Notice events
│   ├── event_payx.go       # Payx events
│   ├── interface.go        # Interface definitions
│   └── util/               # Utilities
│       └── event.go        # Event utilities
├── cmds/                   # Command line interface
│   ├── allinone/           # All-in-one command
│   │   └── allinone.go     # All-in-one implementation
│   └── cmds.go             # Commands
├── conf/                   # Configuration files
│   ├── config.toml         # Main config
│   └── metrics.toml        # Metrics config
├── config/                 # Config management
│   └── config.go           # Config utilities
├── core/                   # Core functionality
│   └── battle/             # Battle core
│       └── battle.go       # Battle implementation
├── modules/                # Check modules
│   ├── auth/               # Auth module
│   │   ├── auth.go         # Auth implementation
│   │   ├── checker.go      # Auth checker
│   │   ├── const.go        # Auth constants
│   │   └── gen.go          # Generated code
│   ├── battlex/            # Battlex module
│   │   ├── battlex.go      # Battlex implementation
│   │   ├── checker.go      # Battlex checker
│   │   ├── const.go        # Battlex constants
│   │   └── gen.go          # Generated code
│   ├── clientbilog/        # ClientBiLog module
│   │   ├── checker.go      # ClientBiLog checker
│   │   ├── clientbilog.go  # ClientBiLog implementation
│   │   ├── const.go        # ClientBiLog constants
│   │   └── gen.go          # Generated code
│   ├── cometx/             # Cometx module
│   │   ├── checker.go      # Cometx checker
│   │   ├── cometx.go       # Cometx implementation
│   │   ├── const.go        # Cometx constants
│   │   └── gen.go          # Generated code
│   ├── common/             # Common module
│   │   ├── callback.go     # Callback functions
│   │   ├── checker.go      # Common checker
│   │   ├── const.go        # Common constants
│   │   └── module.go       # Module implementation
│   ├── cross/              # Cross module
│   │   ├── checker.go      # Cross checker
│   │   ├── const.go        # Cross constants
│   │   ├── crossx.go       # Crossx implementation
│   │   └── gen.go          # Generated code
│   ├── gamex/              # Gamex module
│   │   ├── checker.go      # Gamex checker
│   │   ├── const.go        # Gamex constants
│   │   ├── gamex.go        # Gamex implementation
│   │   └── gen.go          # Generated code
│   ├── gatex/              # Gatex module
│   │   ├── checker.go      # Gatex checker
│   │   ├── const.go        # Gatex constants
│   │   ├── gatex.go        # Gatex implementation
│   │   └── gen.go          # Generated code
│   ├── module_mng.go       # Module manager
│   ├── notice/             # Notice module
│   │   ├── checker.go      # Notice checker
│   │   ├── const.go        # Notice constants
│   │   ├── gen.go          # Generated code
│   │   └── notice.go       # Notice implementation
│   ├── payx/               # Payx module
│   │   ├── checker.go      # Payx checker
│   │   ├── const.go        # Payx constants
│   │   ├── gen.go          # Generated code
│   │   └── notice.go       # Payx notice
│   └── reg.go              # Module registration
├── packageInfo.go          # Package information
├── packageInfo.toml        # Package info config
├── startservercheck.sh     # Start script
└── main.go                 # Main entry point
```

## Chức năng chính

### 1. Server Health Monitoring
- **Service Discovery**: Tìm kiếm service
- **Health Check**: Kiểm tra sức khỏe
- **Status Monitoring**: Giám sát trạng thái
- **Performance Monitoring**: Giám sát hiệu suất

### 2. Multi-Service Checking
- **Gamexshard Check**: Kiểm tra game server
- **Battlex Check**: Kiểm tra battle server
- **Scene Check**: Kiểm tra scene server
- **Crossx Check**: Kiểm tra cross server
- **Cachex Check**: Kiểm tra cache server
- **Payx Check**: Kiểm tra payment server
- **Gmserver Check**: Kiểm tra GM server
- **Auth Check**: Kiểm tra auth server
- **Gatex Check**: Kiểm tra gateway
- **Cometx Check**: Kiểm tra chat server
- **ClientBiLog Check**: Kiểm tra BI log server

### 3. Alert System
- **FeiShu Integration**: Tích hợp FeiShu
- **Alert Notifications**: Thông báo cảnh báo
- **Status Reports**: Báo cáo trạng thái
- **Error Notifications**: Thông báo lỗi

### 4. Metrics Collection
- **Performance Metrics**: Chỉ số hiệu suất
- **Health Metrics**: Chỉ số sức khỏe
- **Error Metrics**: Chỉ số lỗi
- **Response Time Metrics**: Chỉ số thời gian phản hồi

### 5. Automated Testing
- **TCP Connection Test**: Test kết nối TCP
- **HTTP Health Check**: Kiểm tra sức khỏe HTTP
- **gRPC Health Check**: Kiểm tra sức khỏe gRPC
- **Service Functionality Test**: Test chức năng service

## Giao thức kết nối

### 1. Servercheck ↔ All Services
- **Protocol**: gRPC, HTTP, TCP
- **Purpose**: Health checking
- **Features**:
  - Service discovery
  - Health monitoring
  - Performance testing
  - Error detection

### 2. Servercheck ↔ FeiShu
- **Protocol**: HTTP/HTTPS
- **Purpose**: Alert notifications
- **Features**:
  - Alert sending
  - Status reporting
  - Error notifications
  - Health updates

### 3. Servercheck ↔ ETCD
- **Protocol**: etcd
- **Purpose**: Service discovery
- **Features**:
  - Service registration
  - Service discovery
  - Configuration management
  - Health status updates

## Cấu hình chính

### Development Config
```toml
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

gid = 12
serverid = "ServerCheck1"
internalip = "127.0.0.1"

# FeiShu Integration
FeiShuServerCheckTestUrl = "https://open.feishu.cn/open-apis/bot/v2/hook/477285eb-6a35-4470-9cc2-ce0e80e079c0"
FeiShuServerCheckUrl = "https://open.feishu.cn/open-apis/bot/v2/hook/f7d0b6d5-542d-4ef5-b065-02a7db692905"
FeiShuServerCheckCNUrl = "https://open.feishu.cn/open-apis/bot/v2/hook/1f1ecb6b-df29-4708-bbe0-48d04fe2bdf9"
FeiShuEnable = true
FeiShuSecretKey = "5XmM1vTD7YNUI3wg7Tdouc"
```

### Production Config
```toml
# Similar structure with production values
FeiShuServerCheckUrl = "production_feishu_url"
FeiShuServerCheckCNUrl = "production_cn_feishu_url"
```

## Checker Architecture

### 1. Checker Structure
```go
type Checker struct {
    UID               string
    SrvType           string
    MetricsPrefix     string
    eventLoop         map[string]CheckFunc
    quit              chan struct{}
    Rander            *rand.Rand
    CheckerMetricsApi
}
```

### 2. Check Function
```go
type CheckFunc func() (error, *protogen.ServerCheckResult)
```

### 3. Server Condition
```go
type ServerCondition struct {
    UID           string
    MetricsPrefix string
    SrvType       string
}
```

## Health Check Types

### 1. TCP Connection Check
- **Port Connectivity**: Kết nối port
- **Connection Timeout**: Timeout kết nối
- **Connection Stability**: Độ ổn định kết nối
- **Network Latency**: Độ trễ mạng

### 2. HTTP Health Check
- **HTTP Status**: Trạng thái HTTP
- **Response Time**: Thời gian phản hồi
- **Content Validation**: Xác thực nội dung
- **Error Rate**: Tỷ lệ lỗi

### 3. gRPC Health Check
- **Service Availability**: Khả dụng service
- **Method Response**: Phản hồi method
- **Error Handling**: Xử lý lỗi
- **Performance Metrics**: Chỉ số hiệu suất

### 4. Service Functionality Check
- **Core Functions**: Chức năng cốt lõi
- **Data Integrity**: Tính toàn vẹn dữ liệu
- **Business Logic**: Logic nghiệp vụ
- **Integration Test**: Test tích hợp

## Alert System

### 1. FeiShu Integration
- **Webhook URLs**: URL webhook
- **Message Formatting**: Định dạng tin nhắn
- **Alert Levels**: Mức độ cảnh báo
- **Notification Channels**: Kênh thông báo

### 2. Alert Types
- **Service Down**: Service sập
- **High Error Rate**: Tỷ lệ lỗi cao
- **Performance Degradation**: Suy giảm hiệu suất
- **Connection Issues**: Vấn đề kết nối

### 3. Alert Configuration
- **Alert Thresholds**: Ngưỡng cảnh báo
- **Alert Frequency**: Tần suất cảnh báo
- **Alert Escalation**: Tăng cấp cảnh báo
- **Alert Suppression**: Ngăn chặn cảnh báo

## Performance & Optimization

### 1. Check Optimization
- **Parallel Checking**: Kiểm tra song song
- **Check Scheduling**: Lập lịch kiểm tra
- **Resource Management**: Quản lý tài nguyên
- **Memory Optimization**: Tối ưu memory

### 2. Metrics Optimization
- **Metrics Collection**: Thu thập chỉ số
- **Metrics Storage**: Lưu trữ chỉ số
- **Metrics Analysis**: Phân tích chỉ số
- **Metrics Reporting**: Báo cáo chỉ số

### 3. Alert Optimization
- **Alert Filtering**: Lọc cảnh báo
- **Alert Aggregation**: Tổng hợp cảnh báo
- **Alert Deduplication**: Loại bỏ trùng lặp
- **Alert Prioritization**: Ưu tiên cảnh báo

## Monitoring & Logging

### Metrics
- **Check Count**: Số lượng kiểm tra
- **Success Rate**: Tỷ lệ thành công
- **Error Rate**: Tỷ lệ lỗi
- **Response Time**: Thời gian phản hồi
- **Service Availability**: Khả dụng service
- **Alert Count**: Số lượng cảnh báo

### Logging
- **Check Log**: Log kiểm tra
- **Alert Log**: Log cảnh báo
- **Error Log**: Log lỗi
- **Performance Log**: Log hiệu suất
- **Health Log**: Log sức khỏe

## Error Handling

### Check Errors
- **Connection Timeout**: Timeout kết nối
- **Service Unavailable**: Service không khả dụng
- **Invalid Response**: Phản hồi không hợp lệ
- **Network Error**: Lỗi mạng

### System Errors
- **Configuration Error**: Lỗi cấu hình
- **Alert Failure**: Lỗi cảnh báo
- **Metrics Error**: Lỗi chỉ số
- **Service Discovery Error**: Lỗi tìm kiếm service

## Security Features

### 1. Check Security
- **Authentication**: Xác thực
- **Authorization**: Phân quyền
- **Data Validation**: Xác thực dữ liệu
- **Secure Communication**: Giao tiếp bảo mật

### 2. Alert Security
- **Webhook Security**: Bảo mật webhook
- **Message Encryption**: Mã hóa tin nhắn
- **Access Control**: Kiểm soát truy cập
- **Audit Logging**: Log audit

## Deployment

### Build
```bash
cd servercheck
go build -o servercheck
```

### Run
```bash
# Development
./servercheck allinone -c config.toml

# Production
./servercheck allinone -c config_g32.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o servercheck

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/servercheck .
CMD ["./servercheck", "allinone"]
```

## Troubleshooting

### Common Issues
1. **False Alerts**: Kiểm tra alert thresholds
2. **Check Timeout**: Kiểm tra timeout settings
3. **Service Discovery Issues**: Kiểm tra etcd connection
4. **Alert Delivery Failure**: Kiểm tra FeiShu configuration

### Debug Tools
- **Health Monitor**: Giám sát sức khỏe
- **Alert Inspector**: Kiểm tra cảnh báo
- **Metrics Dashboard**: Dashboard chỉ số
- **Log Analysis**: Phân tích log

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Server Check Common**: Server check utilities
- **Proto**: Protocol definitions

### External Dependencies
- **etcd**: Service discovery
- **FeiShu**: Alert system
- **gRPC**: Internal communication
- **PlanX**: Platform framework

## Development

### Code Structure
- **Checker Logic**: Logic kiểm tra
- **Module System**: Hệ thống module
- **Alert System**: Hệ thống cảnh báo
- **Metrics System**: Hệ thống chỉ số

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Health Check Tests**: Health check testing
- **Alert Tests**: Alert testing

## Contact

- **Author**: anonymous
- **Email**: anonymous@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service servercheck - server kiểm tra sức khỏe server của hệ thống JWS2.*
