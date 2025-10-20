# Pingserver Service - Ping Test Server

## Tổng quan

**Pingserver** là service kiểm tra ping và kết nối mạng của hệ thống JWS2, chịu trách nhiệm xử lý các yêu cầu ping từ client, đo lường độ trễ mạng, và kiểm tra chất lượng kết nối KCP.

## Thông tin cơ bản

- **Tên service**: pingserver
- **Port chính**: 8601 (KCP)
- **Server ID**: pingServer1
- **Protocol**: KCP
- **Ngôn ngữ**: Go (Golang)
- **Kiến trúc**: Ping test server với KCP protocol

## Cấu trúc thư mục

```
pingserver/
├── cmds/                     # Command line interface
│   ├── cmds.go              # Commands
│   └── kcpping/             # KCP ping commands
│       └── kcpping.go       # KCP ping implementation
├── conf/                    # Configuration files
│   ├── pingserver.toml      # Main config
│   ├── pingserver2.toml     # Alternative config
│   ├── metrics.toml         # Metrics config
│   └── tracing.toml         # Tracing config
├── config/                  # Config management
│   └── config.go            # Config utilities
├── kcpService/              # KCP service implementation
│   ├── connmgr.go           # Connection manager
│   ├── player.go            # Player management
│   └── server.go            # Server implementation
├── health                   # Health check
├── linux_build.sh           # Linux build script
├── pprof                    # Profiling
├── startPingServer.sh       # Start script
└── main.go                  # Main entry point
```

## Chức năng chính

### 1. Ping Testing
- **KCP Ping**: Ping qua KCP protocol
- **Latency Measurement**: Đo lường độ trễ
- **Connection Quality**: Chất lượng kết nối
- **Network Performance**: Hiệu suất mạng

### 2. KCP Connection Management
- **KCP Listener**: Lắng nghe KCP
- **Connection Acceptance**: Chấp nhận kết nối
- **Connection Configuration**: Cấu hình kết nối
- **Connection Cleanup**: Dọn dẹp kết nối

### 3. Player Management
- **Player Creation**: Tạo người chơi
- **Player Connection**: Kết nối người chơi
- **Player Session**: Phiên người chơi
- **Player Cleanup**: Dọn dẹp người chơi

### 4. Network Monitoring
- **Connection Monitoring**: Giám sát kết nối
- **Performance Metrics**: Chỉ số hiệu suất
- **Error Tracking**: Theo dõi lỗi
- **Quality Assessment**: Đánh giá chất lượng

### 5. Service Discovery
- **ETCD Registration**: Đăng ký ETCD
- **Service Health**: Sức khỏe service
- **Load Balancing**: Cân bằng tải
- **Service Discovery**: Tìm kiếm service

## Giao thức kết nối

### 1. Client ↔ Pingserver
- **Protocol**: KCP
- **Port**: 8601
- **Purpose**: Ping testing
- **Features**:
  - Low latency KCP
  - Connection quality testing
  - Network performance measurement
  - Real-time monitoring

### 2. Pingserver ↔ ETCD
- **Protocol**: etcd
- **Purpose**: Service discovery
- **Features**:
  - Service registration
  - Health checking
  - Configuration management
  - Service discovery

### 3. Pingserver ↔ Other Services
- **Protocol**: gRPC
- **Purpose**: Service communication
- **Features**:
  - Service coordination
  - Data sharing
  - Event notification

## Cấu hình chính

### Development Config
```toml
[PingServerConfig]
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

gid = 12
serverid = "pingServer1"
internalip = "127.0.0.1"

publicip = "127.0.0.1:8601"
listen = ":8601"
CloudGAAddress = ""  # Load balancer address

region = "A"
domainname = "10.0.2.92"
isAutoPutEtcd = true  # Auto register to etcd

# Oracle cloud service IP
oracle_pri_ip = "127.0.0.1"
oracle_pub_ip = "127.0.0.1"
```

### Production Config
```toml
# Similar structure with production values
publicip = "aws"  # Production environment
domainname = "production_domain"
oracle_pri_ip = "production_private_ip"
oracle_pub_ip = "production_public_ip"
```

## KCP Protocol Configuration

### 1. KCP Settings
```go
const (
    NetTimeOut = time.Second * 2  // Network timeout
    Mtu        = 1000            // MTU size
)

// KCP Configuration
conn.SetStreamMode(false)
conn.SetNoDelay(1, 20, 2, 1)
conn.SetMtu(Mtu)
conn.SetACKNoDelay(false)
conn.SetReadBuffer(1024 * 16)
conn.SetWriteBuffer(1024 * 16)
conn.SetReadDeadline(time.Now().Add(NetTimeOut))
conn.SetWriteDeadline(time.Now().Add(NetTimeOut))
```

### 2. Connection Management
- **Connection Pooling**: Pool kết nối
- **Connection Reuse**: Tái sử dụng kết nối
- **Connection Health**: Sức khỏe kết nối
- **Connection Cleanup**: Dọn dẹp kết nối

### 3. Player Management
- **Player ID Generation**: Tạo ID người chơi
- **Player Session**: Phiên người chơi
- **Player Cleanup**: Dọn dẹp người chơi
- **Player Monitoring**: Giám sát người chơi

## Ping Service Architecture

### 1. Ping Service Structure
```go
type PingService struct {
    kcpListen *kcp.Listener
    wait      *util.WaitGroupWrapper
    closed    int32  // Close flag
}
```

### 2. Player Structure
```go
type Player struct {
    PlayerId byte
    conn     battlenet.IBattleNet
    // ... other fields
}
```

### 3. Connection Management
- **Accept Connections**: Chấp nhận kết nối
- **Handle Connections**: Xử lý kết nối
- **Close Connections**: Đóng kết nối
- **Monitor Connections**: Giám sát kết nối

## Performance & Optimization

### 1. KCP Optimization
- **Stream Mode**: Chế độ stream
- **No Delay**: Không delay
- **MTU Size**: Kích thước MTU
- **Buffer Size**: Kích thước buffer

### 2. Connection Optimization
- **Connection Pooling**: Pool kết nối
- **Connection Reuse**: Tái sử dụng kết nối
- **Connection Health**: Sức khỏe kết nối
- **Connection Cleanup**: Dọn dẹp kết nối

### 3. Memory Management
- **Memory Pooling**: Pool memory
- **Garbage Collection**: Thu gom rác
- **Memory Monitoring**: Giám sát memory
- **Memory Optimization**: Tối ưu memory

## Monitoring & Logging

### Metrics
- **Ping Count**: Số lượng ping
- **Ping Latency**: Độ trễ ping
- **Connection Count**: Số lượng kết nối
- **Error Rate**: Tỷ lệ lỗi
- **Response Time**: Thời gian phản hồi
- **Network Quality**: Chất lượng mạng

### Logging
- **Ping Log**: Log ping
- **Connection Log**: Log kết nối
- **Error Log**: Log lỗi
- **Performance Log**: Log hiệu suất

## Error Handling

### Connection Errors
- **Connection Timeout**: Timeout kết nối
- **Connection Refused**: Từ chối kết nối
- **Network Error**: Lỗi mạng
- **KCP Error**: Lỗi KCP

### Ping Errors
- **Ping Timeout**: Timeout ping
- **Ping Failed**: Ping thất bại
- **Invalid Ping**: Ping không hợp lệ
- **Network Unreachable**: Mạng không thể truy cập

## Security Features

### 1. Connection Security
- **Connection Validation**: Xác thực kết nối
- **IP Whitelisting**: Danh sách trắng IP
- **Rate Limiting**: Giới hạn tốc độ
- **Connection Encryption**: Mã hóa kết nối

### 2. Ping Security
- **Ping Validation**: Xác thực ping
- **Spam Protection**: Bảo vệ spam
- **Rate Limiting**: Giới hạn tốc độ
- **Access Control**: Kiểm soát truy cập

## Deployment

### Build
```bash
cd pingserver
go build -o pingserver
```

### Run
```bash
# Development
./pingserver kcpping -c pingserver.toml

# Production
./pingserver kcpping -c pingserver2.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o pingserver

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/pingserver .
CMD ["./pingserver", "kcpping"]
```

## Troubleshooting

### Common Issues
1. **High Ping Latency**: Kiểm tra network configuration
2. **Connection Timeout**: Kiểm tra KCP settings
3. **Service Discovery Issues**: Kiểm tra etcd connection
4. **Memory Usage**: Kiểm tra memory management

### Debug Tools
- **Ping Monitor**: Giám sát ping
- **Connection Inspector**: Kiểm tra kết nối
- **Network Monitor**: Giám sát mạng
- **Log Analysis**: Phân tích log

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Battle Common**: Battle utilities
- **Proto**: Protocol definitions

### External Dependencies
- **KCP**: KCP library
- **etcd**: Service discovery
- **gRPC**: Internal communication
- **PlanX**: Platform framework

## Development

### Code Structure
- **KCP Service**: KCP service logic
- **Player Management**: Quản lý người chơi
- **Connection Management**: Quản lý kết nối
- **Ping Logic**: Logic ping

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Ping Tests**: Ping testing
- **Network Tests**: Network testing

## Contact

- **Author**: zhangzhen
- **Email**: zhangzhen@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service pingserver - server kiểm tra ping và kết nối mạng của hệ thống JWS2.*
