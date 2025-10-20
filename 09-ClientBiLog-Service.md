# ClientBiLog Service - Client Business Intelligence Log Server

## Tổng quan

**ClientBiLog** là service thu thập và xử lý dữ liệu business intelligence từ client của hệ thống JWS2, chịu trách nhiệm nhận và xử lý các log từ client, tạo ra logic log để phân tích dữ liệu người dùng.

## Thông tin cơ bản

- **Tên service**: clientbilog
- **Port chính**: 8089 (HTTP)
- **Server ID**: clientbilog1
- **Protocol**: HTTP/HTTPS
- **Ngôn ngữ**: Go (Golang)
- **Kiến trúc**: Log collection server với IP geolocation

## Cấu trúc thư mục

```
clientbilog/
├── cmds/                     # Command line interface
│   ├── allinone/            # All-in-one command
│   │   └── allinone.go      # All-in-one implementation
│   └── cmds.go              # Commands
├── conf/                    # Configuration files
│   ├── accesslog.xml        # Access log configuration
│   ├── app.toml             # Main application config
│   ├── config_g32.toml      # Production config
│   ├── logiclog.toml        # Logic log config
│   ├── metrics.toml         # Metrics config
│   └── reload.toml          # Reload config
├── config/                  # Config management
│   └── config.go            # Config utilities
├── ipdata/                  # IP geolocation data
│   ├── IP2LOCATION-CONTINENT-ENGLISH.CSV # Continent data
│   ├── IP2LOCATION-LITE-DB1.IPV6.BIN     # IP database
│   └── ReadMe               # IP data readme
├── routers/                 # HTTP routers
│   ├── router_log_client_analysis.go # Client analysis router
│   ├── router_log_common.go # Common log router
│   ├── router_log_v2_test.go # Log v2 test
│   ├── router_log_v2.go     # Log v2 router
│   └── router_log.go        # Main log router
├── stress/                  # Stress testing
│   ├── bilog_v1.lua         # BiLog v1 test
│   ├── bilog_v2.lua         # BiLog v2 test
│   └── clientbilog.sh       # Stress test script
├── readme                   # Service readme
└── main.go                  # Main entry point
```

## Chức năng chính

### 1. Client Log Collection
- **Client Time Event**: Sự kiện thời gian client
- **Device Information**: Thông tin thiết bị
- **User Behavior**: Hành vi người dùng
- **Game Events**: Sự kiện game

### 2. Data Processing
- **Data Decoding**: Giải mã dữ liệu
- **Data Validation**: Xác thực dữ liệu
- **Data Transformation**: Chuyển đổi dữ liệu
- **Logic Log Generation**: Tạo logic log

### 3. IP Geolocation
- **IP Location**: Vị trí IP
- **Country Detection**: Phát hiện quốc gia
- **Region Detection**: Phát hiện vùng
- **Continent Detection**: Phát hiện lục địa

### 4. Rate Limiting
- **Request Limiting**: Giới hạn request
- **Rate Control**: Kiểm soát tốc độ
- **Burst Control**: Kiểm soát burst
- **IP Limiting**: Giới hạn IP

### 5. Data Analysis
- **Client Analysis**: Phân tích client
- **User Journey**: Hành trình người dùng
- **Performance Metrics**: Chỉ số hiệu suất
- **Business Intelligence**: Thông tin kinh doanh

## Giao thức kết nối

### 1. Client ↔ ClientBiLog
- **Protocol**: HTTP/HTTPS
- **Port**: 8089
- **Purpose**: Log collection
- **Endpoints**:
  - `/log/v1/clienttimeevent` - Client time event
  - `/log/v1/analysis` - Client analysis
  - `/log/v2/*` - Log v2 endpoints

### 2. ClientBiLog ↔ Logic Log System
- **Protocol**: Internal
- **Purpose**: Generate logic logs
- **Features**:
  - Data transformation
  - Log formatting
  - Log routing

### 3. ClientBiLog ↔ IP Geolocation
- **Protocol**: Internal
- **Purpose**: IP location lookup
- **Features**:
  - IP database lookup
  - Country detection
  - Region detection

## Cấu hình chính

### Development Config
```toml
[CommonConfig]
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

gid = 12
serverid = "clientbilog1"
httpport = ":8089"
internalip = "127.0.0.1"

[LimitConfig]
limit_url_regex = ".*"  # URLs to limit
rate_limit_valid = false  # Rate limiting switch
rate_limit_Average = 10000  # Average rate per second
rate_limit_Burst = 20000   # Maximum burst rate
```

### Production Config
```toml
# Similar structure with production values
httpport = ":8089"
rate_limit_valid = true  # Enable rate limiting in production
```

## API Endpoints

### 1. Client Time Event
- **POST** `/log/v1/clienttimeevent`
- **Purpose**: Collect client time events
- **Parameters**:
  - `gid`: Game ID
  - `ver`: Game version
  - `devid`: Device ID
  - `uid`: User ID
  - `acid`: Account ID
  - `devtyp`: Device type
  - `isemulator`: Is emulator
  - `channel`: Channel
  - `clienttime`: Client time
  - `step`: Step
  - `ip`: IP address
  - `os`: Operating system
  - `IDFA`: IDFA
  - `country`: Country
  - `region`: Region
  - `extra`: Extra data
  - `cpu`: CPU model
  - `ram`: RAM size
  - `graphics`: Graphics settings
  - `FirstGraphics`: First graphics
  - `isFirst`: Is first time
  - `downloadspeed`: Download speed
  - `levelid`: Level ID
  - `isitem`: Is item
  - `itemid`: Item ID
  - `itemevent`: Item event
  - `Language`: Language
  - `netstate`: Network state
  - `abtestmode`: AB test mode

### 2. Client Analysis
- **POST** `/log/v1/analysis`
- **Purpose**: Client analysis data
- **Parameters**: Analysis-specific parameters

### 3. Log V2 Endpoints
- **POST** `/log/v2/*`
- **Purpose**: Enhanced log collection
- **Features**: Improved data structure and processing

## Data Processing

### 1. Data Decoding
```go
// Decode base64 encoded parameters
gidB, _ := secure.DefaultEncode.Decode64FromNet(gid)
verB, _ := secure.DefaultEncode.Decode64FromNet(ver)
deviceIDB, _ := secure.DefaultEncode.Decode64FromNet(deviceID)
// ... more parameters
```

### 2. Data Validation
- **Type Conversion**: Convert string to appropriate types
- **Range Validation**: Validate numeric ranges
- **Format Validation**: Validate data formats
- **Required Field Check**: Check required fields

### 3. IP Geolocation
```go
// IP location lookup
location := ip2loc.GetLocation(ip)
country := location.Country
region := location.Region
```

### 4. Logic Log Generation
- **Data Transformation**: Transform client data to logic log format
- **Log Formatting**: Format logs according to standards
- **Log Routing**: Route logs to appropriate destinations

## Rate Limiting

### 1. Request Limiting
- **URL Regex**: Match URLs to limit
- **Rate Control**: Control request rate
- **Burst Control**: Control burst requests
- **IP Limiting**: Limit per IP

### 2. Rate Limiting Configuration
```toml
[LimitConfig]
limit_url_regex = ".*"  # All URLs
rate_limit_valid = false  # Disabled in development
rate_limit_Average = 10000  # 10k requests per second average
rate_limit_Burst = 20000   # 20k requests per second burst
```

## IP Geolocation

### 1. IP Database
- **IP2Location Database**: IPv6 database
- **Continent Data**: Continent mapping
- **Country Data**: Country detection
- **Region Data**: Region detection

### 2. Location Detection
- **IP Lookup**: Look up IP location
- **Country Detection**: Detect country
- **Region Detection**: Detect region
- **Continent Detection**: Detect continent

## Monitoring & Logging

### Metrics
- **Request Count**: Số lượng request
- **Request Rate**: Tốc độ request
- **Error Rate**: Tỷ lệ lỗi
- **Response Time**: Thời gian phản hồi
- **IP Location Hits**: Số lần tra cứu IP
- **Data Processing Time**: Thời gian xử lý dữ liệu

### Logging
- **Access Log**: Log truy cập
- **Logic Log**: Log logic
- **Error Log**: Log lỗi
- **Performance Log**: Log hiệu suất

## Error Handling

### Request Errors
- **Invalid Parameters**: Tham số không hợp lệ
- **Decoding Errors**: Lỗi giải mã
- **Validation Errors**: Lỗi xác thực
- **Rate Limit Exceeded**: Vượt quá giới hạn tốc độ

### Processing Errors
- **Data Transformation Errors**: Lỗi chuyển đổi dữ liệu
- **IP Lookup Errors**: Lỗi tra cứu IP
- **Log Generation Errors**: Lỗi tạo log
- **Database Errors**: Lỗi database

## Security Features

### 1. Data Security
- **Base64 Encoding**: Mã hóa base64
- **Data Validation**: Xác thực dữ liệu
- **Input Sanitization**: Làm sạch input
- **Rate Limiting**: Giới hạn tốc độ

### 2. Access Control
- **IP Whitelisting**: Danh sách trắng IP
- **Rate Limiting**: Giới hạn tốc độ
- **Request Validation**: Xác thực request
- **Header Validation**: Xác thực header

## Performance & Optimization

### 1. Request Processing
- **Async Processing**: Xử lý bất đồng bộ
- **Batch Processing**: Xử lý theo batch
- **Connection Pooling**: Pool kết nối
- **Memory Management**: Quản lý memory

### 2. IP Lookup Optimization
- **Database Caching**: Cache database
- **Lookup Optimization**: Tối ưu tra cứu
- **Memory Mapping**: Memory mapping
- **Index Optimization**: Tối ưu index

## Deployment

### Build
```bash
cd clientbilog
go build -o clientbilog
```

### Run
```bash
# Development
./clientbilog allinone -c app.toml

# Production
./clientbilog allinone -c config_g32.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o clientbilog

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/clientbilog .
COPY --from=builder /app/ipdata ./ipdata
CMD ["./clientbilog", "allinone"]
```

## Troubleshooting

### Common Issues
1. **High Request Rate**: Kiểm tra rate limiting
2. **IP Lookup Errors**: Kiểm tra IP database
3. **Data Decoding Errors**: Kiểm tra data format
4. **Memory Usage**: Kiểm tra memory usage

### Debug Tools
- **Request Monitor**: Giám sát request
- **IP Lookup Test**: Test tra cứu IP
- **Data Validation Test**: Test xác thực dữ liệu
- **Log Analysis**: Phân tích log

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Logic Log**: Logic log utilities
- **IP2Location**: IP geolocation library

### External Dependencies
- **etcd**: Service discovery
- **Gin**: HTTP framework
- **PlanX**: Platform framework

## Development

### Code Structure
- **Router Layer**: Lớp router
- **Data Processing**: Xử lý dữ liệu
- **IP Geolocation**: Định vị IP
- **Log Generation**: Tạo log

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Stress Tests**: Stress testing
- **Load Tests**: Load testing

## Contact

- **Author**: ZhangZhen
- **Email**: zhangzhen@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service clientbilog - server thu thập và xử lý dữ liệu business intelligence từ client của hệ thống JWS2.*
