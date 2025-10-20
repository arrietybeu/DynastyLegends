# JWS2 Game Server - Hệ thống Backend Game Server

## Tổng quan dự án

Đây là một hệ thống backend game server phức tạp được xây dựng bằng Go, hỗ trợ game mobile với kiến trúc microservices. Dự án bao gồm nhiều service độc lập hoạt động cùng nhau để tạo ra một hệ thống game hoàn chỉnh.

## Kiến trúc tổng thể

### Công nghệ sử dụng
- **Ngôn ngữ**: Go (Golang)
- **Database**: MySQL, MongoDB, Redis
- **Message Queue**: NATS
- **Service Discovery**: etcd
- **Protocol**: TCP/TLS, gRPC, HTTP/HTTPS, WebSocket
- **Monitoring**: Prometheus, FeiShu Bot
- **Build System**: Shell scripts, Makefile

### Cấu trúc dự án

```
jws2/
├── battlex/                    # Battle Server - Xử lý logic chiến đấu
├── battlexagent/              # Battle Agent - Agent xử lý battle
├── cachex/                    # Cache Server - Quản lý cache
├── clientbilog/               # Client BiLog - Thu thập dữ liệu từ client
├── common/                    # Common Library - Thư viện chung
├── crossx/                    # Cross Server - Server liên server
├── data/                      # Game Data - Dữ liệu game
├── friendx/                   # Friend Server - Hệ thống bạn bè
├── gamedata/                  # Game Data - Dữ liệu game chi tiết
├── gamexshard/                # Game Shard - Server game chính
├── gmserver/                  # GM Server - Server quản trị
├── merger/                    # Merger Server - Hợp server
├── payx/                      # Payment Server - Xử lý thanh toán
├── pingserver/                # Ping Server - Kiểm tra ping
├── scene/                     # Scene Server - Xử lý scene game
├── sensitiveword/             # Sensitive Word - Lọc từ nhạy cảm
├── servercheck/               # Server Check - Kiểm tra server
├── share/                     # Shared Library - Thư viện dùng chung
├── stressx/                   # Stress Test - Test tải
└── build scripts              # Scripts build và deploy
```

## Chi tiết các thành phần

### 1. Gamexshard - Game Server Chính
**Chức năng**: Server game chính xử lý logic game
- **Port**: 8080 (HTTP), 10002 (gRPC)
- **Database**: MySQL, MongoDB
- **Chức năng chính**:
  - Xử lý logic game chính
  - Quản lý người chơi
  - Xử lý giao dịch
  - Quản lý guild, rank, mail
  - Xử lý các sự kiện game

**Cấu hình chính**:
```toml
gid = 12                    # Game ID
shardid = 120001           # Shard ID
etcd_endpoint = ["http://127.0.0.1:2379/"]
mongo_pay_db_name = "PayDB"
nats_url = ["nats://127.0.0.1:4222"]
```

### 2. Battlex - Battle Server
**Chức năng**: Xử lý logic chiến đấu real-time
- **Port**: 8701
- **Chức năng chính**:
  - Xử lý battle logic
  - Quản lý frame rate (20 FPS)
  - Lưu trữ battle replay
  - Xử lý battle check

**Cấu hình chính**:
```toml
BattleFrameRate = 20
BattleFrameTime = 50ms
frame2file = false
importantBattle2filepath = "./importantbattle/"
```

### 3. Scene - Scene Server
**Chức năng**: Xử lý scene game, di chuyển nhân vật
- **Port**: 10003
- **Chức năng chính**:
  - Xử lý di chuyển nhân vật
  - Quản lý scene
  - Xử lý collision detection
  - Rate limiting cho các action

**Cấu hình chính**:
```toml
shardid = 120001
limit_valid = true
ratelimit_url_info = "Scene/MessageC2SRoleMove,1,30;Scene/MessageC2SRoleStop,1,10"
```

### 4. Crossx - Cross Server
**Chức năng**: Xử lý giao tiếp liên server
- **Port**: 10004
- **Chức năng chính**:
  - Cross-server communication
  - Cross-server events
  - Cross-server ranking
  - Cross-server battles

**Cấu hình chính**:
```toml
gid = 12
serverid = 0  # 0 = leader
cross_member_num = 2
CrossDbAddr = "redis@127.0.0.1:6379"
```

### 5. Cachex - Cache Server
**Chức năng**: Quản lý cache cho toàn hệ thống
- **Port**: 10005
- **Chức năng chính**:
  - Redis cache management
  - Player data caching
  - Session management
  - Cache invalidation

### 6. Payx - Payment Server
**Chức năng**: Xử lý thanh toán
- **Port**: 2019
- **Chức năng chính**:
  - Payment processing
  - Order management
  - Payment verification
  - Refund handling

**Cấu hình chính**:
```toml
pay_channel = "NT"  # NT: 中台, HERO: 英雄
mongo_pay_db_name = "PayDB"
```

### 7. Gmserver - GM Server
**Chức năng**: Server quản trị game
- **Port**: 8808
- **Chức năng chính**:
  - GM tools
  - Player management
  - Server monitoring
  - Data analysis

### 8. Auth - Authentication Server
**Chức năng**: Xử lý xác thực
- **Port**: 8081
- **Chức năng chính**:
  - User authentication
  - Login/logout
  - Token management
  - SDK integration

### 9. Gatex - Gateway Server
**Chức năng**: Gateway chính cho client
- **Port**: 8667
- **Chức năng chính**:
  - Client connection management
  - Load balancing
  - SSL termination
  - Connection pooling

### 10. Chat System
**Cometx (Port: 10001)**: WebSocket server cho chat
**Logicx (Port: 10002)**: Logic server cho chat
**Jobx**: Job server cho chat

**Chức năng chính**:
- Real-time chat
- Private messages
- Room chat
- Message filtering (YiDun)
- Translation support

### 11. Friendx - Friend Server
**Chức năng**: Hệ thống bạn bè
- **Port**: 10006
- **Chức năng chính**:
  - Friend management
  - Friend requests
  - Friend activities
  - Social features

### 12. Merger - Server Merger
**Chức năng**: Hợp server
- **Port**: 7101
- **Chức năng chính**:
  - Server merging
  - Data migration
  - Conflict resolution
  - Rollback support

## Cơ sở dữ liệu

### MySQL
- **Gamexshard**: Dữ liệu game chính
- **Gmserver**: Dữ liệu GM tools
- **Payx**: Dữ liệu thanh toán

### MongoDB
- **PayDB**: Dữ liệu thanh toán
- **SilenceDB**: Dữ liệu cấm chat
- **AuthDB**: Dữ liệu xác thực

### Redis
- **Cache**: Cache dữ liệu
- **Session**: Quản lý session
- **Cross**: Dữ liệu cross-server

## Cấu hình môi trường

### Development
```bash
# Khởi động tất cả services
sh g32_servermanager.sh build

# Khởi động từng service riêng lẻ
cd gamexshard && sh startgamex.sh build
cd battlex && sh startbattlex.sh build
cd scene && sh startscene.sh build
```

### Production
```bash
# Build tất cả services
sh serverbuild.sh

# Khởi động với cấu hình production
sh g32_servermanager.sh run
```

## Monitoring và Logging

### FeiShu Bot Integration
- Server status monitoring
- Error notifications
- Performance alerts
- Custom notifications

### Logging System
- **Logic Log**: Game logic logs
- **Packet Log**: Network packet logs
- **Error Log**: Error tracking
- **Performance Log**: Performance metrics

## Bảo mật

### Authentication
- JWT tokens
- SDK integration (NT, Hero)
- Device verification
- IP whitelisting

### Data Protection
- SSL/TLS encryption
- Data encryption at rest
- Secure communication between services
- Rate limiting

## Deployment

### Docker Support
- Containerized services
- Docker Compose configuration
- Health checks
- Auto-restart policies

### Load Balancing
- ELB integration
- Service discovery via etcd
- Health check endpoints
- Failover support

## Development Tools

### Build Scripts
- `serverbuild.sh`: Build tất cả services
- `g32_servermanager.sh`: Quản lý services
- `start_service.sh`: Khởi động service

### Testing
- `stressx/`: Stress testing tools
- Unit tests
- Integration tests
- Performance tests

## Giao thức giao tiếp Client-Server

### 1. TCP/TLS Connection (Chính)
**Client ↔ Gatex (Gateway)**
- **Protocol**: TCP với TLS encryption
- **Port**: 8667 (Gatex)
- **Handshake**: Custom handshake protocol
- **Features**:
  - TLS encryption cho bảo mật
  - Custom packet protocol với length + CRC
  - Heartbeat mechanism
  - Gzip compression
  - Connection pooling

**Cấu hình TCP**:
```toml
# Gatex TCP Server
listen = "185.238.251.18:8667"
maxconn = 3
game_conn_num = 10

# TLS Configuration
[GateConfig.SslCfg]
cert = "conf/server.crt"
key = "conf/server.key"
ca = "conf/ca.crt"
```

### 2. WebSocket Connection (Chat)
**Client ↔ Cometx (Chat Server)**
- **Protocol**: WebSocket over TCP
- **Port**: 10001 (Cometx)
- **Features**:
  - Real-time chat
  - Private messages
  - Room chat
  - Message filtering

**Cấu hình WebSocket**:
```toml
# Cometx TCP Server
listen = ":10001"
[TCP]
Sndbuf = 4096
Rcvbuf = 4096
KeepAlive = false
DeadLine = 60
```

### 3. HTTP/HTTPS APIs
**Client ↔ Auth, Payment, GM Services**
- **Auth**: `http://127.0.0.1:8081` (HTTP)
- **Payment**: `http://127.0.0.1:2019` (HTTP)
- **GM**: `http://127.0.0.1:8808` (HTTP)
- **Notice**: `http://127.0.0.1:8084` (HTTP)

### 4. gRPC (Internal Services)
**Server-to-Server Communication**
- **Gatex ↔ Gamexshard**: gRPC stream
- **Cometx ↔ Logicx**: gRPC
- **Crossx**: gRPC cho cross-server
- **Cachex**: gRPC cho cache operations

## Packet Protocol Design

### Client Packet Structure
```
+----------+----------+----------+----------+
| Length   | MessageID| RawData  | CRC      |
| (4 bytes)| (4 bytes)| (bytes)  | (4 bytes)|
+----------+----------+----------+----------+
```

### Features
- **Length**: Packet size validation
- **CRC**: Data integrity check
- **Compression**: Gzip support
- **Encryption**: TLS layer
- **Heartbeat**: Keep-alive mechanism
- **Endian**: Little Endian (C# SDK compatibility)

### Handshake Protocol
1. **Client connects** via TCP/TLS
2. **Server sends** handshake parameters
3. **Client responds** with authentication token
4. **Server validates** và establishes session
5. **Heartbeat** mechanism starts

## API Documentation

### REST APIs
- **Auth**: `/api/auth/*`
- **Game**: `/api/game/*`
- **Payment**: `/api/payment/*`
- **GM**: `/api/gm/*`

### gRPC APIs
- **Battle**: Battle service
- **Scene**: Scene service
- **Cross**: Cross service
- **Cache**: Cache service

## Troubleshooting

### Common Issues
1. **Service không khởi động**: Kiểm tra port conflicts
2. **Database connection**: Kiểm tra database credentials
3. **etcd connection**: Kiểm tra etcd service
4. **Memory issues**: Kiểm tra resource usage

### Debug Tools
- `pprof`: Performance profiling
- `goroutine`: Goroutine analysis
- `holmes`: Memory leak detection
- `nohup.out`: Service logs

## Contributing

### Code Style
- Go standard formatting
- Comment documentation
- Error handling
- Logging standards

### Testing
- Unit tests required
- Integration tests
- Performance benchmarks
- Code coverage

## License

Proprietary - Ticore Game Company

## Contact

- **Author**: ZhangZhen, YangZhenYu, RenYiDa
- **Email**: zhangzhen@taiyouxi.cn, yangzhenyu@taiyouxi.cn, renyida@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này được tạo tự động dựa trên phân tích mã nguồn dự án JWS2 Game Server.*
