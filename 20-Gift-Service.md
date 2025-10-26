# Gift Service - Gift Code Server

## Tổng quan

**Gift** là service quản lý mã quà tặng của hệ thống JWS2, chịu trách nhiệm tạo, quản lý, phân phối và theo dõi các mã quà tặng trong game, tích hợp với hệ thống thanh toán và phần thưởng.

## Thông tin cơ bản

- **Tên service**: gift
- **Port chính**: 2020 (HTTPS)
- **Server ID**: gift1
- **Protocol**: HTTPS, HTTP
- **Ngôn ngữ**: Go (Golang)
- **Kiến trúc**: Gift code management server với OSS integration

## Cấu trúc thư mục

```
share/x/gift/
├── cmds/                     # Command line interface
│   ├── allinone/            # All-in-one command
│   │   └── allinone.go      # All-in-one implementation
│   └── cmds.go              # Commands
├── conf/                    # Configuration files
│   ├── config.toml          # Main config
│   ├── logiclog.toml        # Logic log config
│   ├── metrics.toml         # Metrics config
│   ├── ca.srl               # CA serial
│   └── server.csr           # Server certificate request
├── config/                  # Config management
│   ├── config.go            # Config utilities
│   ├── const.go             # Constants
│   └── define.go            # Definitions
├── db/                      # Database layer
│   ├── mysql/               # MySQL implementation
│   │   ├── const.go         # MySQL constants
│   │   ├── create_table_operation.go # Create table operations
│   │   ├── create_table_statement.go # Create table statements
│   │   ├── mysql.go         # MySQL implementation
│   │   └── sql_statement.go # SQL statements
│   ├── oss/                 # OSS implementation
│   │   ├── oss_operation.go # OSS operations
│   │   └── oss.go           # OSS implementation
│   ├── operation.go         # Database operations
│   └── operation_assist.go  # Operation assistance
├── model/                   # Data models
│   ├── api/                 # API models
│   │   └── api.go           # API implementation
│   ├── common.go            # Common models
│   ├── define/              # Model definitions
│   │   ├── gift_code_info.go # Gift code info
│   │   └── item.go          # Item model
│   └── errs/                # Error models
│       └── errs_msg.go      # Error messages
├── modules/                 # Business modules
│   ├── common/              # Common module
│   │   ├── common_submodule.go # Common submodule
│   │   ├── handler/         # Common handlers
│   │   │   └── gen_gIft_code.go # Generate gift code
│   │   ├── handler.go       # Handler implementation
│   │   └── util/            # Utilities
│   │       └── util.go      # Utility implementation
│   └── giftcode/            # Gift code module
│       ├── gen.go           # Generated code
│       ├── giftcode_handler/ # Gift code handlers
│       │   ├── add_gift_code.go # Add gift code
│       │   ├── claim_gift_code.go # Claim gift code
│       │   ├── destroy_gift_code.go # Destroy gift code
│       │   ├── download_gift_code.go # Download gift code
│       │   ├── gen_gift_code.go # Generate gift code
│       │   ├── query_gen_info.go # Query generation info
│       │   ├── query_gift_code.go # Query gift code
│       │   ├── query_la.go  # Query LA
│       │   ├── update_gift_code.go # Update gift code
│       │   └── update_la.go # Update LA
│       ├── gm_tools.go      # GM tools
│       └── handle.go        # Handler implementation
├── server/                  # Server implementation
│   ├── module_reg.go        # Module registration
│   ├── server_module.go     # Server module
│   └── server.go            # Server implementation
├── services/                # Business services
│   ├── common/              # Common services
│   │   └── common.go        # Common service
│   ├── giftcode_service/    # Gift code service
│   │   └── giftcode_service.go # Gift code service implementation
│   └── services.go          # Services implementation
├── test/                    # Test files
│   └── tls/                 # TLS tests
│       ├── gmtools_2_gift_https/ # GM tools to gift HTTPS
│       ├── shard_2_gift_https/ # Shard to gift HTTPS
│       └── tls_unobstructed/ # TLS unobstructed
├── startgift.sh             # Start script
└── main.go                  # Main entry point
```

## Chức năng chính

### 1. Gift Code Management
- **Gift Code Generation**: Tạo mã quà tặng
- **Gift Code Validation**: Xác thực mã quà tặng
- **Gift Code Distribution**: Phân phối mã quà tặng
- **Gift Code Tracking**: Theo dõi mã quà tặng

### 2. Gift Code Operations
- **Add Gift Code**: Thêm mã quà tặng
- **Update Gift Code**: Cập nhật mã quà tặng
- **Destroy Gift Code**: Hủy mã quà tặng
- **Query Gift Code**: Truy vấn mã quà tặng

### 3. Gift Code Claiming
- **Claim Gift Code**: Nhận mã quà tặng
- **Claim Validation**: Xác thực nhận quà
- **Claim Tracking**: Theo dõi nhận quà
- **Claim History**: Lịch sử nhận quà

### 4. Gift Code Analytics
- **Usage Statistics**: Thống kê sử dụng
- **Generation Analytics**: Phân tích tạo mã
- **Claim Analytics**: Phân tích nhận quà
- **Performance Metrics**: Chỉ số hiệu suất

### 5. OSS Integration
- **Data Storage**: Lưu trữ dữ liệu
- **File Management**: Quản lý file
- **Backup & Recovery**: Sao lưu và khôi phục
- **Data Synchronization**: Đồng bộ dữ liệu

## Giao thức kết nối

### 1. Client ↔ Gift
- **Protocol**: HTTPS/HTTP
- **Port**: 2020
- **Purpose**: Gift code operations
- **Features**:
  - Gift code claiming
  - Gift code validation
  - Gift code querying
  - Gift code management

### 2. Gift ↔ MySQL
- **Protocol**: MySQL
- **Purpose**: Data storage
- **Features**:
  - Gift code storage
  - User data storage
  - Transaction logging
  - Analytics data

### 3. Gift ↔ OSS
- **Protocol**: OSS
- **Purpose**: File storage
- **Features**:
  - File upload
  - File download
  - File management
  - Data backup

## Cấu hình chính

### Development Config
```toml
[GiftCfg]
# MySQL Database Configuration
db_driver = "mysql"
gift_mysql_url = "root:123456@tcp(127.0.0.1:3306)/new_server"
db_max_open_conns = 100
db_max_idle_conns = 20
db_conn_max_life_time = 600

# OSS Configuration
oss_endpoint = "http://oss-cn-hangzhou.aliyuncs.com"
oss_data_bucket = "jws2-battle-data"
oss_cloud_root = "gift"
oss_accesskey = "YOUR_OSS_ACCESS_KEY"
oss_secretkey = "YOUR_OSS_SECRET_KEY"

# Server Configuration
code_project_num = 0
serverid = "gift1"
proj = "jws2"

# Service Configuration
httpport = ":2020"
server_crt_file = "conf/server.crt"
server_key_file = "conf/server.key"
ca_crt_file = "conf/ca.crt"
```

### Production Config
```toml
# Similar structure with production values
gift_mysql_url = "production_mysql_url"
oss_endpoint = "production_oss_endpoint"
oss_accesskey = "production_access_key"
oss_secretkey = "production_secret_key"
```

## Gift Code System

### 1. Gift Code Structure
```go
type GiftCodeInfo struct {
    ID          string
    Code        string
    ItemID      string
    ItemCount   int
    ExpireTime  time.Time
    MaxUses     int
    UsedCount   int
    Status      string
    CreatedAt   time.Time
    // ... other fields
}
```

### 2. Gift Code Generation
```go
type GiftCodeGenerator struct {
    ProjectNum  int
    ServerID    string
    CodeLength  int
    CodePrefix  string
    // ... other fields
}
```

### 3. Gift Code Validation
```go
type GiftCodeValidator struct {
    Code        string
    UserID      string
    ServerID    string
    Validation  bool
    // ... other fields
}
```

## Gift Code Operations

### 1. Generation Operations
- **Batch Generation**: Tạo hàng loạt
- **Custom Generation**: Tạo tùy chỉnh
- **Template Generation**: Tạo từ mẫu
- **Random Generation**: Tạo ngẫu nhiên

### 2. Management Operations
- **Code Activation**: Kích hoạt mã
- **Code Deactivation**: Vô hiệu hóa mã
- **Code Expiration**: Hết hạn mã
- **Code Cleanup**: Dọn dẹp mã

### 3. Distribution Operations
- **Direct Distribution**: Phân phối trực tiếp
- **Batch Distribution**: Phân phối hàng loạt
- **Scheduled Distribution**: Phân phối theo lịch
- **Conditional Distribution**: Phân phối có điều kiện

## OSS Integration

### 1. File Management
- **File Upload**: Tải lên file
- **File Download**: Tải xuống file
- **File Delete**: Xóa file
- **File List**: Danh sách file

### 2. Data Storage
- **Gift Code Data**: Dữ liệu mã quà tặng
- **User Data**: Dữ liệu người dùng
- **Transaction Data**: Dữ liệu giao dịch
- **Analytics Data**: Dữ liệu phân tích

### 3. Backup & Recovery
- **Data Backup**: Sao lưu dữ liệu
- **Data Recovery**: Khôi phục dữ liệu
- **Data Synchronization**: Đồng bộ dữ liệu
- **Data Validation**: Xác thực dữ liệu

## Performance & Optimization

### 1. Database Optimization
- **Connection Pooling**: Pool kết nối
- **Query Optimization**: Tối ưu query
- **Index Optimization**: Tối ưu index
- **Transaction Optimization**: Tối ưu transaction

### 2. OSS Optimization
- **Upload Optimization**: Tối ưu tải lên
- **Download Optimization**: Tối ưu tải xuống
- **Caching Strategy**: Chiến lược cache
- **Compression**: Nén dữ liệu

### 3. API Optimization
- **Response Caching**: Cache phản hồi
- **Request Batching**: Gộp request
- **Rate Limiting**: Giới hạn tốc độ
- **Load Balancing**: Cân bằng tải

## Monitoring & Logging

### Metrics
- **Gift Code Count**: Số lượng mã quà tặng
- **Claim Count**: Số lượng nhận quà
- **Generation Count**: Số lượng tạo mã
- **Success Rate**: Tỷ lệ thành công
- **Response Time**: Thời gian phản hồi
- **Error Rate**: Tỷ lệ lỗi

### Logging
- **Gift Code Log**: Log mã quà tặng
- **Claim Log**: Log nhận quà
- **Generation Log**: Log tạo mã
- **Error Log**: Log lỗi
- **Performance Log**: Log hiệu suất

## Error Handling

### Gift Code Errors
- **Invalid Code**: Mã không hợp lệ
- **Expired Code**: Mã đã hết hạn
- **Used Code**: Mã đã sử dụng
- **Max Uses Reached**: Đã đạt giới hạn sử dụng

### System Errors
- **Database Error**: Lỗi database
- **OSS Error**: Lỗi OSS
- **Network Error**: Lỗi mạng
- **Configuration Error**: Lỗi cấu hình

## Security Features

### 1. Gift Code Security
- **Code Encryption**: Mã hóa mã
- **Code Validation**: Xác thực mã
- **Code Expiration**: Hết hạn mã
- **Code Tracking**: Theo dõi mã

### 2. API Security
- **HTTPS Encryption**: Mã hóa HTTPS
- **Authentication**: Xác thực
- **Authorization**: Phân quyền
- **Rate Limiting**: Giới hạn tốc độ

### 3. Data Security
- **Data Encryption**: Mã hóa dữ liệu
- **Access Control**: Kiểm soát truy cập
- **Audit Logging**: Log audit
- **Data Backup**: Sao lưu dữ liệu

## Deployment

### Build
```bash
cd share/x/gift
go build -o gift
```

### Run
```bash
# Development
./gift allinone -c config.toml

# Production
./gift allinone -c config_g32.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o gift

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/gift .
CMD ["./gift", "allinone"]
```

## Troubleshooting

### Common Issues
1. **Database Connection Issues**: Kiểm tra MySQL configuration
2. **OSS Connection Issues**: Kiểm tra OSS configuration
3. **Gift Code Generation Issues**: Kiểm tra generation logic
4. **Performance Issues**: Kiểm tra optimization settings

### Debug Tools
- **Gift Code Inspector**: Kiểm tra mã quà tặng
- **Database Monitor**: Giám sát database
- **OSS Monitor**: Giám sát OSS
- **Performance Profiler**: Profile hiệu suất

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Gift Common**: Gift utilities
- **Proto**: Protocol definitions

### External Dependencies
- **MySQL**: Database
- **OSS**: Object storage
- **HTTPS**: Client communication
- **PlanX**: Platform framework

## Development

### Code Structure
- **Gift Code Management**: Quản lý mã quà tặng
- **Database Layer**: Lớp database
- **OSS Integration**: Tích hợp OSS
- **API Layer**: Lớp API

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Gift Code Tests**: Gift code testing
- **OSS Tests**: OSS testing

## Contact

- **Author**: ZhangZhen
- **Email**: zhangzhen@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service gift - server quản lý mã quà tặng của hệ thống JWS2.*

