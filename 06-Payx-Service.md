# Payx Service - Payment Server

## Tổng quan

**Payx** là service xử lý thanh toán của hệ thống JWS2, chịu trách nhiệm xử lý các giao dịch thanh toán, quản lý payment channels, và tích hợp với các SDK thanh toán bên thứ ba.

## Thông tin cơ bản

- **Tên service**: payx
- **Port chính**: 2019 (HTTP)
- **Server ID**: payx1
- **Protocol**: HTTP/HTTPS, gRPC
- **Ngôn ngữ**: Go (Golang)
- **Database**: MongoDB (PayDB)
- **Kiến trúc**: Payment server với SDK integration

## Cấu trúc thư mục

```
payx/
├── cmds/                     # Command line interface
│   ├── cmds.go              # Commands
│   ├── common.go            # Common utilities
│   └── pay/                 # Pay commands
│       └── pay.go           # Pay command implementation
├── conf/                    # Configuration files
│   ├── config.toml          # Main config
│   ├── logiclog.toml        # Logic log config
│   └── metrics.toml         # Metrics config
├── config/                  # Config management
│   ├── config.go            # Config utilities
│   └── const.go             # Constants
├── db/                      # Database layer
│   └── mongo/               # MongoDB implementation
│       ├── mongo.go         # MongoDB connection
│       ├── mongo_init.go    # MongoDB initialization
│       ├── mongo_operation.go # MongoDB operations
│       └── mongo_recharge_data.go # Recharge data
├── logic/                   # Business logic
│   ├── common.go            # Common logic
│   ├── handler.go           # Request handlers
│   ├── hero_pay.go          # Hero payment logic
│   ├── nt_pay.go            # NT payment logic
│   ├── nt_pay_3rd.go        # NT 3rd party payment
│   └── nt_pay_3rd_test.go   # NT 3rd party test
├── model/                   # Data models
│   ├── api/                 # API models
│   │   └── api.go           # API definitions
│   └── define/              # Data definitions
│       ├── hero_pay_data.go # Hero payment data
│       └── pay.go           # Payment data
├── services/                # Service layer
│   ├── common/              # Common services
│   │   └── common.go        # Common service
│   ├── pay_service/         # Payment service
│   │   └── pay_service.go   # Payment service implementation
│   └── services.go          # Services registration
├── test/                    # Test files
│   ├── mongo/               # MongoDB tests
│   │   ├── find_and_modify/ # Find and modify test
│   │   ├── mongodb/         # MongoDB test
│   │   └── repeated_save/   # Repeated save test
│   └── sdk_simu/            # SDK simulation
│       └── post_can_reach/  # Post reach test
├── util/                    # Utilities
│   ├── common_util.go       # Common utilities
│   └── hero_util.go         # Hero utilities
└── main.go                  # Main entry point
```

## Chức năng chính

### 1. Payment Processing
- **Payment Validation**: Xác thực thanh toán
- **Payment Processing**: Xử lý thanh toán
- **Payment Confirmation**: Xác nhận thanh toán
- **Payment Refund**: Hoàn tiền

### 2. SDK Integration
- **NT Payment**: Tích hợp NT payment
- **Hero Payment**: Tích hợp Hero payment
- **3rd Party Payment**: Thanh toán bên thứ ba
- **Multi-Channel**: Đa kênh thanh toán

### 3. Payment Channels
- **Domestic**: Thanh toán trong nước
- **Overseas**: Thanh toán quốc tế
- **Security Version**: Phiên bản bảo mật
- **Japan Version**: Phiên bản Nhật Bản
- **Vietnam Version**: Phiên bản Việt Nam

### 4. Data Management
- **Payment Data**: Dữ liệu thanh toán
- **Recharge Data**: Dữ liệu nạp tiền
- **Transaction History**: Lịch sử giao dịch
- **Payment Statistics**: Thống kê thanh toán

### 5. Security & Validation
- **Signature Validation**: Xác thực chữ ký
- **Data Encryption**: Mã hóa dữ liệu
- **Fraud Detection**: Phát hiện gian lận
- **Audit Logging**: Log audit

## Giao thức kết nối

### 1. Client ↔ Payx
- **Protocol**: HTTP/HTTPS
- **Port**: 2019
- **Purpose**: Payment processing
- **Endpoints**:
  - Payment initiation
  - Payment confirmation
  - Payment status
  - Payment refund

### 2. Gamexshard ↔ Payx
- **Protocol**: gRPC
- **Purpose**: Payment integration
- **Commands**:
  - Payment request
  - Payment confirmation
  - Payment status
  - Payment callback

### 3. Payx ↔ 3rd Party SDKs
- **Protocol**: HTTP/HTTPS
- **Purpose**: External payment processing
- **SDKs**:
  - NT Payment SDK
  - Hero Payment SDK
  - Other 3rd party SDKs

## Cấu hình chính

### Development Config
```toml
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

publicip = "127.0.0.1"
httpport = ":2019"
internalip = "127.0.0.1"

serverid = "payx1"
gid = 12

mongo_pay_db_name = "PayDB"
pay_channel = "NT"  # NT: 中台, HERO: 英雄

[SdkPayConfig]
# 国内测试
[SdkPayConfig.1001]
appId = 1001
secretkey = "eb6cf221fc3a527672c4b44f8a6fc774"

# 国内正式
[SdkPayConfig.1002]
appId = 1002
secretkey = "eb6cf221fc3a527672c4b44f8a6fc774"

# 海外版本
[SdkPayConfig.1007]
appId = 1007
secretkey = "a97eccf1a8f513e54442ea276fc687ad"

# 资安版本
[SdkPayConfig.1009]
appId = 1009
secretkey = "4b6d0d3f9bbd9697c2ad69ac46b0370c"

# 日本版本
[SdkPayConfig.1011]
appId = 1011
secretkey = "8151eeb18d81dffff7e8e9d76fce8f37"

# 越南版本
[SdkPayConfig.1016]
appId = 1016
secretkey = "4fdd5421ced3203b8f96040db56b653c"
```

## Payment Channels

### 1. NT Payment (中台)
- **Channel**: NT
- **App ID**: 1001, 1002
- **Purpose**: Domestic payment
- **Features**: Centralized payment processing

### 2. Hero Payment (英雄)
- **Channel**: HERO
- **Purpose**: Hero-specific payment
- **Features**: Hero game integration

### 3. Overseas Payment
- **App ID**: 1007
- **Purpose**: International payment
- **Features**: Multi-currency support

### 4. Security Version
- **App ID**: 1009
- **Purpose**: Security-focused payment
- **Features**: Enhanced security

### 5. Japan Version
- **App ID**: 1011
- **Purpose**: Japan market
- **Features**: Japan-specific features

### 6. Vietnam Version
- **App ID**: 1016
- **Purpose**: Vietnam market
- **Features**: Vietnam-specific features

## Database Schema

### MongoDB Collections
- **PayDB**: Main payment database
- **RechargeData**: Recharge transaction data
- **PaymentLog**: Payment transaction logs
- **RefundData**: Refund transaction data

### Data Models
```go
type PaymentData struct {
    AppId      int    `json:"appId"`
    OrderId    string `json:"orderId"`
    Amount     int    `json:"amount"`
    Currency   string `json:"currency"`
    Status     string `json:"status"`
    Timestamp  int64  `json:"timestamp"`
    Signature  string `json:"signature"`
}
```

## API Endpoints

### 1. Payment Initiation
- **POST** `/payment/initiate`
- **Purpose**: Start payment process
- **Parameters**: Order details, amount, currency
- **Response**: Payment URL, order ID

### 2. Payment Confirmation
- **POST** `/payment/confirm`
- **Purpose**: Confirm payment
- **Parameters**: Order ID, payment result
- **Response**: Confirmation status

### 3. Payment Status
- **GET** `/payment/status/{orderId}`
- **Purpose**: Check payment status
- **Parameters**: Order ID
- **Response**: Payment status

### 4. Payment Refund
- **POST** `/payment/refund`
- **Purpose**: Process refund
- **Parameters**: Order ID, refund amount
- **Response**: Refund status

## Security Features

### 1. Signature Validation
- **HMAC-SHA256**: Signature algorithm
- **Secret Key**: Per-app secret key
- **Timestamp Validation**: Time-based validation
- **Nonce Validation**: Replay attack prevention

### 2. Data Encryption
- **HTTPS**: Transport encryption
- **Data Encryption**: Sensitive data encryption
- **Key Management**: Secure key management
- **Certificate Management**: SSL certificate management

### 3. Fraud Detection
- **Amount Validation**: Validate payment amounts
- **Frequency Check**: Check payment frequency
- **IP Validation**: Validate client IP
- **Device Validation**: Validate client device

### 4. Audit Logging
- **Payment Logs**: Log all payments
- **Access Logs**: Log all access
- **Error Logs**: Log all errors
- **Security Logs**: Log security events

## Performance & Optimization

### 1. Database Optimization
- **Connection Pooling**: MongoDB connection pooling
- **Index Optimization**: Database index optimization
- **Query Optimization**: Query performance optimization
- **Caching**: Data caching

### 2. API Optimization
- **Response Caching**: Cache API responses
- **Request Batching**: Batch API requests
- **Async Processing**: Asynchronous processing
- **Load Balancing**: Load balancing

### 3. Payment Processing
- **Queue Processing**: Payment queue processing
- **Retry Logic**: Retry failed payments
- **Timeout Handling**: Handle timeouts
- **Error Recovery**: Error recovery

## Monitoring & Logging

### Metrics
- **Payment Count**: Số lượng thanh toán
- **Payment Amount**: Tổng số tiền thanh toán
- **Success Rate**: Tỷ lệ thành công
- **Error Rate**: Tỷ lệ lỗi
- **Response Time**: Thời gian phản hồi
- **Throughput**: Thông lượng

### Logging
- **Payment Log**: Log thanh toán
- **Error Log**: Log lỗi
- **Security Log**: Log bảo mật
- **Performance Log**: Log hiệu suất

## Error Handling

### Payment Errors
- **Invalid Amount**: Số tiền không hợp lệ
- **Invalid Currency**: Tiền tệ không hợp lệ
- **Signature Mismatch**: Chữ ký không khớp
- **Order Not Found**: Không tìm thấy đơn hàng

### System Errors
- **Database Error**: Lỗi database
- **Network Error**: Lỗi mạng
- **SDK Error**: Lỗi SDK
- **Timeout Error**: Lỗi timeout

## Deployment

### Build
```bash
cd payx
go build -o payx
```

### Run
```bash
# Development
./payx pay -c config.toml

# Production
./payx pay -c config_g32.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o payx

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/payx .
CMD ["./payx", "pay"]
```

## Troubleshooting

### Common Issues
1. **Payment Timeout**: Kiểm tra network và SDK
2. **Signature Error**: Kiểm tra secret key
3. **Database Error**: Kiểm tra MongoDB connection
4. **SDK Error**: Kiểm tra SDK configuration

### Debug Tools
- **Payment Inspector**: Kiểm tra thanh toán
- **Log Analysis**: Phân tích log
- **Performance Monitor**: Giám sát hiệu suất
- **Security Audit**: Audit bảo mật

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Payment Common**: Payment utilities
- **Proto**: Protocol definitions

### External Dependencies
- **MongoDB**: Payment database
- **etcd**: Service discovery
- **gRPC**: Internal communication
- **PlanX**: Platform framework

## Development

### Code Structure
- **Payment Logic**: Logic thanh toán
- **SDK Integration**: Tích hợp SDK
- **Database Layer**: Lớp database
- **API Layer**: Lớp API

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Payment Tests**: Payment testing
- **Security Tests**: Security testing

## Contact

- **Author**: ZhangZhen
- **Email**: zhangzhen@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service payx - server xử lý thanh toán của hệ thống JWS2.*
