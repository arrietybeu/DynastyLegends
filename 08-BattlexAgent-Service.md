# BattlexAgent Service - Battle Agent Server

## Tổng quan

**BattlexAgent** là service agent xử lý battle của hệ thống JWS2, chịu trách nhiệm kết nối và quản lý các battle server, xử lý proxy connection, và đảm bảo kết nối ổn định giữa client và battle server.

## Thông tin cơ bản

- **Tên service**: battlexagent
- **Port chính**: 8705 (KCP + TCP)
- **Server ID**: battlexagent1
- **Protocol**: KCP, TCP
- **Ngôn ngữ**: Go (Golang)
- **Kiến trúc**: Agent server với proxy connection

## Cấu trúc thư mục

```
battlexagent/
├── battleagent/              # Battle agent core
│   ├── agent_conn_battle.go  # Agent connection battle
│   ├── agnet_mgr.go          # Agent manager
│   ├── client.go             # Client implementation
│   ├── proxy.go              # Proxy implementation
│   └── service.go            # Service implementation
├── cmds/                     # Command line interface
│   ├── battlexagent/         # Battle agent commands
│   │   └── battleagent.go    # Battle agent command implementation
│   └── cmds.go               # Commands
├── conf/                     # Configuration files
│   ├── battleagent.toml      # Main config
│   ├── config_g32.toml       # Production config
│   ├── metrics.toml          # Metrics config
│   └── tracing.toml          # Tracing config
├── config/                   # Config management
│   └── config.go             # Config utilities
├── health                    # Health check
├── hotchangelevel.sh         # Hot change level script
├── linux_build.sh            # Linux build script
├── main.go                   # Main entry point
├── nohup.out                 # No hang up output
├── pprof                     # Profiling
├── startbattlexagent.sh      # Start script
└── startbattlexagent2.sh     # Start script 2
```

## Chức năng chính

### 1. Battle Agent Management
- **Agent Connection**: Kết nối agent
- **Agent Manager**: Quản lý agent
- **Service Discovery**: Tìm kiếm service
- **Connection Pooling**: Pool kết nối

### 2. Proxy Connection
- **Proxy Implementation**: Triển khai proxy
- **Connection Routing**: Định tuyến kết nối
- **Load Balancing**: Cân bằng tải
- **Failover**: Chuyển đổi dự phòng

### 3. Client Management
- **Client Connection**: Kết nối client
- **Client Event Loop**: Vòng lặp sự kiện client
- **Client State**: Trạng thái client
- **Client Health**: Sức khỏe client

### 4. Service Management
- **Service Registration**: Đăng ký service
- **Service Discovery**: Tìm kiếm service
- **Service Health**: Sức khỏe service
- **Service Load Balancing**: Cân bằng tải service

### 5. Network Management
- **KCP Protocol**: Giao thức KCP
- **TCP Protocol**: Giao thức TCP
- **Connection Management**: Quản lý kết nối
- **Network Monitoring**: Giám sát mạng

## Giao thức kết nối

### 1. BattlexAgent ↔ Battlex
- **Protocol**: KCP, TCP
- **Port**: 8705
- **Purpose**: Battle server connection
- **Features**:
  - Low latency KCP
  - Reliable TCP backup
  - Connection pooling
  - Load balancing

### 2. Client ↔ BattlexAgent
- **Protocol**: KCP, TCP
- **Purpose**: Client proxy connection
- **Features**:
  - Proxy routing
  - Connection management
  - Load balancing
  - Failover

### 3. BattlexAgent ↔ Service Discovery
- **Protocol**: etcd
- **Purpose**: Service discovery
- **Features**:
  - Service registration
  - Service discovery
  - Health checking
  - Load balancing

## Cấu hình chính

### Development Config
```toml
[BattleConfig]
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

gid = 12
serverid = "battlexagent1"
internalip = "127.0.0.1"

publicip = "127.0.0.1:8705"
listen = ":8705"
is_kcp = true
CloudGAAddress = ""  # Load balancer address

frame2file = false  # For testing
region = "A"
domainname = "185.238.251.18"

net_lock_thread_num = 2
```

### Production Config
```toml
# Similar structure with production values
publicip = "aws"  # Production environment
is_kcp = true
CloudGAAddress = "production_lb_address"
```

## Agent Architecture

### 1. Client Event Loop
```go
type clientEventLoop struct {
    common.PoolHandle
    common.KeepAliveCheck
    servers  sync.Map     // Server connections
    clientId int          // Client ID
    client   *gnet.Client // Client connection
    mgr      *agentManager
}
```

### 2. Agent Manager
- **Service Management**: Quản lý service
- **Connection Management**: Quản lý kết nối
- **Load Balancing**: Cân bằng tải
- **Health Checking**: Kiểm tra sức khỏe

### 3. Proxy Implementation
- **Connection Proxy**: Proxy kết nối
- **Message Routing**: Định tuyến message
- **Load Balancing**: Cân bằng tải
- **Failover**: Chuyển đổi dự phòng

## Network Protocol

### 1. KCP Protocol
- **Low Latency**: Độ trễ thấp
- **Reliable**: Đáng tin cậy
- **Congestion Control**: Kiểm soát tắc nghẽn
- **Fast Retransmission**: Truyền lại nhanh

### 2. TCP Protocol
- **Reliable**: Đáng tin cậy
- **Ordered**: Có thứ tự
- **Error Checking**: Kiểm tra lỗi
- **Flow Control**: Kiểm soát luồng

### 3. Connection Management
- **Connection Pooling**: Pool kết nối
- **Connection Reuse**: Tái sử dụng kết nối
- **Connection Health**: Sức khỏe kết nối
- **Connection Cleanup**: Dọn dẹp kết nối

## Performance & Optimization

### 1. Connection Pooling
- **Pool Size**: Kích thước pool
- **Pool Management**: Quản lý pool
- **Pool Health**: Sức khỏe pool
- **Pool Optimization**: Tối ưu pool

### 2. Load Balancing
- **Round Robin**: Vòng tròn
- **Least Connections**: Ít kết nối nhất
- **Weighted**: Có trọng số
- **Health-based**: Dựa trên sức khỏe

### 3. Network Optimization
- **KCP Settings**: Cài đặt KCP
- **TCP Settings**: Cài đặt TCP
- **Buffer Management**: Quản lý buffer
- **Memory Management**: Quản lý memory

## Monitoring & Logging

### Metrics
- **Connection Count**: Số lượng kết nối
- **Active Connections**: Kết nối hoạt động
- **Connection Pool Size**: Kích thước pool
- **Load Balance Distribution**: Phân phối cân bằng tải
- **Error Rate**: Tỷ lệ lỗi
- **Response Time**: Thời gian phản hồi

### Logging
- **Connection Log**: Log kết nối
- **Proxy Log**: Log proxy
- **Error Log**: Log lỗi
- **Performance Log**: Log hiệu suất

## Error Handling

### Connection Errors
- **Connection Timeout**: Timeout kết nối
- **Connection Refused**: Từ chối kết nối
- **Network Error**: Lỗi mạng
- **Service Unavailable**: Service không khả dụng

### Proxy Errors
- **Proxy Timeout**: Timeout proxy
- **Proxy Error**: Lỗi proxy
- **Routing Error**: Lỗi định tuyến
- **Load Balance Error**: Lỗi cân bằng tải

## Security Features

### 1. Connection Security
- **Connection Validation**: Xác thực kết nối
- **IP Whitelisting**: Danh sách trắng IP
- **Rate Limiting**: Giới hạn tốc độ
- **Connection Encryption**: Mã hóa kết nối

### 2. Proxy Security
- **Proxy Authentication**: Xác thực proxy
- **Message Validation**: Xác thực message
- **Access Control**: Kiểm soát truy cập
- **Audit Logging**: Log audit

## Deployment

### Build
```bash
cd battlexagent
go build -o battlexagent
```

### Run
```bash
# Development
./battlexagent battleagent -c battleagent.toml

# Production
./battlexagent battleagent -c config_g32.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o battlexagent

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/battlexagent .
CMD ["./battlexagent", "battleagent"]
```

## Troubleshooting

### Common Issues
1. **Connection Timeout**: Kiểm tra network và service
2. **Load Balance Issues**: Kiểm tra load balancer
3. **Proxy Errors**: Kiểm tra proxy configuration
4. **Service Discovery**: Kiểm tra etcd connection

### Debug Tools
- **Connection Monitor**: Giám sát kết nối
- **Proxy Inspector**: Kiểm tra proxy
- **Load Balance Monitor**: Giám sát cân bằng tải
- **Log Analysis**: Phân tích log

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Battle Common**: Battle utilities
- **Proto**: Protocol definitions

### External Dependencies
- **etcd**: Service discovery
- **gnet**: Network library
- **gRPC**: Internal communication
- **PlanX**: Platform framework

## Development

### Code Structure
- **Agent Logic**: Logic agent
- **Proxy Implementation**: Triển khai proxy
- **Connection Management**: Quản lý kết nối
- **Service Management**: Quản lý service

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Load Tests**: Load testing
- **Connection Tests**: Connection testing

## Contact

- **Author**: zhangzhen
- **Email**: zhangzhen@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service battlexagent - server agent xử lý battle của hệ thống JWS2.*
