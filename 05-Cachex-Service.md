# Cachex Service - Cache Server

## Tổng quan

**Cachex** là service quản lý cache của hệ thống JWS2, chịu trách nhiệm lưu trữ và quản lý dữ liệu cache cho các service khác, đảm bảo hiệu suất cao và giảm tải cho database.

## Thông tin cơ bản

- **Tên service**: cachex
- **Port chính**: 10005 (gRPC)
- **Server ID**: Cachex1
- **Protocol**: gRPC, HTTP
- **Ngôn ngữ**: Go (Golang)
- **Kiến trúc**: Cache server với module system

## Cấu trúc thư mục

```
cachex/
├── api/                      # API layer
│   └── api.go               # Main API
├── cmds/                    # Command line interface
│   ├── allinone/            # All-in-one command
│   │   └── allinone.go      # All-in-one implementation
│   └── cmds.go              # Commands
├── conf/                    # Configuration files
│   ├── config.toml          # Main config
│   ├── config_g32.toml      # Production config
│   ├── metrics.toml         # Metrics config
│   └── tracing.toml         # Tracing config
├── config/                  # Config management
│   └── config.go            # Config utilities
├── logics/                  # Business logic
│   ├── batch_msg.go         # Batch message handling
│   ├── msg.go               # Message handling
│   └── reg.go               # Registration
├── modules/                 # Cache modules
│   ├── guild/               # Guild cache
│   │   ├── api.go           # Guild API
│   │   ├── const.go         # Guild constants
│   │   ├── gen.go           # Generated code
│   │   └── guild.go         # Guild logic
│   ├── player/              # Player cache
│   │   ├── api.go           # Player API
│   │   ├── cache.go         # Player cache logic
│   │   ├── const.go         # Player constants
│   │   ├── gen.go           # Generated code
│   │   └── player.go        # Player logic
│   ├── send/                # Send cache
│   │   ├── api.go           # Send API
│   │   ├── const.go         # Send constants
│   │   ├── gen.go           # Generated code
│   │   ├── send.go          # Send logic
│   │   └── sender.go        # Sender logic
│   ├── module_mng.go        # Module management
│   └── reg.go               # Module registration
├── packageInfo.go           # Package information
├── packageInfo.toml         # Package info config
└── main.go                  # Main entry point
```

## Chức năng chính

### 1. Cache Management
- **Data Caching**: Lưu trữ dữ liệu cache
- **Cache Invalidation**: Vô hiệu hóa cache
- **Cache Warming**: Làm nóng cache
- **Cache Statistics**: Thống kê cache

### 2. Player Cache
- **Player Data**: Dữ liệu người chơi
- **Player State**: Trạng thái người chơi
- **Player Session**: Phiên người chơi
- **Player Statistics**: Thống kê người chơi

### 3. Guild Cache
- **Guild Data**: Dữ liệu guild
- **Guild Members**: Thành viên guild
- **Guild State**: Trạng thái guild
- **Guild Statistics**: Thống kê guild

### 4. Send Cache
- **Message Caching**: Cache tin nhắn
- **Send Queue**: Hàng đợi gửi
- **Send Statistics**: Thống kê gửi
- **Send Optimization**: Tối ưu gửi

### 5. Batch Processing
- **Batch Messages**: Xử lý tin nhắn theo batch
- **Batch Operations**: Thao tác theo batch
- **Batch Statistics**: Thống kê batch
- **Batch Optimization**: Tối ưu batch

## Giao thức kết nối

### 1. Gamexshard ↔ Cachex
- **Protocol**: gRPC
- **Purpose**: Player data caching
- **Commands**:
  - Get player data
  - Set player data
  - Update player data
  - Delete player data

### 2. Crossx ↔ Cachex
- **Protocol**: gRPC
- **Purpose**: Cross server caching
- **Commands**:
  - Cross server data
  - Cross server statistics
  - Cross server events

### 3. Scene ↔ Cachex
- **Protocol**: gRPC
- **Purpose**: Scene data caching
- **Commands**:
  - Scene data
  - Scene statistics
  - Scene events

### 4. Battlex ↔ Cachex
- **Protocol**: gRPC
- **Purpose**: Battle data caching
- **Commands**:
  - Battle data
  - Battle statistics
  - Battle events

## Cấu hình chính

### Development Config
```toml
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

gid = 12
serverid = "Cachex1"
internalip = "127.0.0.1"
```

### Production Config
```toml
# Similar structure with production values
gid = 32
serverid = "Cachex1"
internalip = "production_ip"
```

## Module System

### 1. Player Module
- **Player Data Cache**: Cache dữ liệu người chơi
- **Player State Management**: Quản lý trạng thái
- **Player Session**: Phiên người chơi
- **Player Statistics**: Thống kê

### 2. Guild Module
- **Guild Data Cache**: Cache dữ liệu guild
- **Guild Member Management**: Quản lý thành viên
- **Guild State**: Trạng thái guild
- **Guild Statistics**: Thống kê guild

### 3. Send Module
- **Message Cache**: Cache tin nhắn
- **Send Queue Management**: Quản lý hàng đợi
- **Send Optimization**: Tối ưu gửi
- **Send Statistics**: Thống kê gửi

## Cache Strategies

### 1. Write-Through Cache
- **Immediate Write**: Ghi ngay lập tức
- **Data Consistency**: Đảm bảo tính nhất quán
- **Write Performance**: Hiệu suất ghi
- **Data Safety**: An toàn dữ liệu

### 2. Write-Behind Cache
- **Delayed Write**: Ghi chậm
- **Write Performance**: Hiệu suất ghi cao
- **Data Risk**: Rủi ro mất dữ liệu
- **Batch Writing**: Ghi theo batch

### 3. Read-Through Cache
- **Cache Miss**: Load từ database
- **Cache Hit**: Trả về từ cache
- **Load Performance**: Hiệu suất load
- **Data Freshness**: Độ tươi mới dữ liệu

### 4. Refresh-Ahead Cache
- **Proactive Refresh**: Làm mới chủ động
- **Cache Hit Rate**: Tỷ lệ hit cao
- **Resource Usage**: Sử dụng tài nguyên
- **Predictive Loading**: Load dự đoán

## Performance & Optimization

### Memory Management
- **Memory Pooling**: Pool memory
- **Garbage Collection**: Thu gom rác
- **Memory Monitoring**: Giám sát memory
- **Memory Optimization**: Tối ưu memory

### Cache Optimization
- **LRU Eviction**: Loại bỏ LRU
- **TTL Management**: Quản lý TTL
- **Cache Warming**: Làm nóng cache
- **Cache Preloading**: Preload cache

### Batch Processing
- **Batch Size**: Kích thước batch
- **Batch Frequency**: Tần suất batch
- **Batch Optimization**: Tối ưu batch
- **Batch Monitoring**: Giám sát batch

## Monitoring & Logging

### Metrics
- **Cache Hit Rate**: Tỷ lệ hit cache
- **Cache Miss Rate**: Tỷ lệ miss cache
- **Cache Size**: Kích thước cache
- **Memory Usage**: Sử dụng memory
- **Request Rate**: Tốc độ request
- **Response Time**: Thời gian phản hồi

### Logging
- **Cache Log**: Log cache operations
- **Performance Log**: Log hiệu suất
- **Error Log**: Log lỗi
- **Statistics Log**: Log thống kê

## Error Handling

### Cache Errors
- **Cache Miss**: Không tìm thấy cache
- **Cache Corruption**: Cache bị hỏng
- **Cache Overflow**: Cache tràn
- **Cache Timeout**: Cache timeout

### Data Errors
- **Data Inconsistency**: Dữ liệu không nhất quán
- **Data Corruption**: Dữ liệu bị hỏng
- **Data Loss**: Mất dữ liệu
- **Data Validation**: Xác thực dữ liệu

## Security Features

### Data Security
- **Data Encryption**: Mã hóa dữ liệu
- **Access Control**: Kiểm soát truy cập
- **Data Validation**: Xác thực dữ liệu
- **Audit Logging**: Log audit

### Cache Security
- **Cache Isolation**: Cô lập cache
- **Cache Validation**: Xác thực cache
- **Cache Sanitization**: Làm sạch cache
- **Cache Monitoring**: Giám sát cache

## Deployment

### Build
```bash
cd cachex
go build -o cachex
```

### Run
```bash
# Development
./cachex allinone -c config.toml

# Production
./cachex allinone -c config_g32.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o cachex

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/cachex .
CMD ["./cachex", "allinone"]
```

## Troubleshooting

### Common Issues
1. **High Memory Usage**: Kiểm tra cache size
2. **Low Hit Rate**: Kiểm tra cache strategy
3. **Slow Response**: Kiểm tra performance
4. **Data Inconsistency**: Kiểm tra cache logic

### Debug Tools
- **Cache Inspector**: Kiểm tra cache
- **Memory Profiler**: Profile memory
- **Performance Monitor**: Giám sát hiệu suất
- **Log Analysis**: Phân tích log

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Cache Common**: Cache utilities
- **Proto**: Protocol definitions

### External Dependencies
- **etcd**: Service discovery
- **gRPC**: Internal communication
- **PlanX**: Platform framework

## Development

### Code Structure
- **Cache Logic**: Logic cache
- **Module System**: Hệ thống module
- **API Layer**: Lớp API
- **Batch Processing**: Xử lý batch

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Performance Tests**: Performance testing
- **Cache Tests**: Cache testing

## Contact

- **Author**: anonymous
- **Email**: anonymous@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service cachex - server quản lý cache của hệ thống JWS2.*
