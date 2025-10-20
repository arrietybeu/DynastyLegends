# Gamexshard Service - Game Server Chính

## Tổng quan

**Gamexshard** là service game server chính của hệ thống JWS2, chịu trách nhiệm xử lý toàn bộ logic game, quản lý người chơi, và điều phối các hoạt động game. Đây là trái tim của hệ thống game server.

## Thông tin cơ bản

- **Tên service**: gamexshard
- **Port chính**: 8080 (HTTP), 10002 (gRPC)
- **Database**: MySQL, MongoDB, Redis
- **Ngôn ngữ**: Go (Golang)
- **Kiến trúc**: Microservice với module system

## Cấu trúc thư mục

```
gamexshard/
├── account/                 # Quản lý tài khoản người chơi
├── api/                    # API endpoints và handlers
├── battleforcheck/         # Kiểm tra battle
├── clodudb/               # Cloud database
├── cmds/                  # Command line interface
├── conf/                  # Configuration files
├── core/                  # Core game logic
│   ├── account/           # Account management
│   ├── battle/            # Battle system
│   ├── dbpool/            # Database connection pool
│   ├── game_clouddb/      # Cloud database
│   ├── message/           # Message handling
│   └── scene/             # Scene management
├── debug/                 # Debug tools
├── forgm/                 # GM tools
├── logics/                # Game logic handlers
│   ├── cachex_handlers/   # Cache service handlers
│   ├── cross_handlers/    # Cross-server handlers
│   ├── friend_handlers/   # Friend system handlers
│   ├── gamex_handlers/    # Game handlers
│   ├── pay_handlers/      # Payment handlers
│   └── handlers/          # Main game handlers (1143 files)
├── modules/               # Game modules (100+ modules)
└── pre_modules/           # Pre-startup modules
```

## Chức năng chính

### 1. Quản lý người chơi (Player Management)
- **Account Manager**: Quản lý tài khoản người chơi
- **Player State**: Trạng thái người chơi (online/offline)
- **Session Management**: Quản lý phiên đăng nhập
- **Data Persistence**: Lưu trữ dữ liệu người chơi

### 2. Game Logic System
- **Request Processing**: Xử lý request từ client
- **Message Handling**: Xử lý tin nhắn game
- **Event System**: Hệ thống sự kiện game
- **State Management**: Quản lý trạng thái game

### 3. Module System (100+ Modules)
- **Activity Modules**: Các hoạt động game
- **Battle Modules**: Hệ thống chiến đấu
- **Guild System**: Hệ thống guild
- **Ranking System**: Hệ thống xếp hạng
- **Auction House**: Nhà đấu giá
- **Mail System**: Hệ thống thư
- **Friend System**: Hệ thống bạn bè

### 4. Cross-Server Communication
- **Cross Handlers**: Xử lý giao tiếp liên server
- **Cross Cache**: Cache liên server
- **Cross Rank**: Xếp hạng liên server

## Giao thức kết nối

### 1. Client ↔ Gamexshard
- **Protocol**: gRPC stream
- **Port**: 10002 (internal)
- **Gateway**: Thông qua Gatex (port 8667)
- **Authentication**: JWT token validation

### 2. Internal Services
- **Gatex**: gRPC stream cho client connection
- **Scene**: gRPC cho scene management
- **Battlex**: gRPC cho battle system
- **Cachex**: gRPC cho cache operations
- **Crossx**: gRPC cho cross-server communication

### 3. External Services
- **Auth**: HTTP cho authentication
- **Payment**: HTTP cho payment processing
- **GM**: HTTP cho GM operations
- **Chat**: HTTP cho chat system

## Cấu hình chính

### Development Config
```toml
# etcd
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

# shard
gid = 12
shardid = 120001
internalip = "127.0.0.1"

# Database
mongo_pay_db_name = "PayDB"
DBSync = true
BatchSaveDB = true

# NATS
nats_url = ["nats://127.0.0.1:4222"]

# Chat
chat_server_url = "http://185.238.251.18:9999"
chat_app_key = "uufijdsfiid"
chat_server_secret = "rifgjo493fkdsfdskgvf"
```

### Production Config
```toml
# shard
gid = 32
shardid = 64

# Rate limiting
limit_valid = true
ratelimit_url_info = "PlayLevel/DeclareLvlEnmyLootRequest,1,1;PlayerAttr/GetInfoRequest,1,10"
```

## Database Schema

### MySQL Tables
- **Account Data**: Thông tin tài khoản người chơi
- **Player Data**: Dữ liệu người chơi
- **Game Data**: Dữ liệu game
- **Transaction Data**: Dữ liệu giao dịch

### MongoDB Collections
- **PayDB**: Dữ liệu thanh toán
- **Player Collections**: Dữ liệu người chơi chi tiết
- **Game Collections**: Dữ liệu game chi tiết

### Redis Keys
- **Session Data**: Dữ liệu phiên đăng nhập
- **Cache Data**: Dữ liệu cache
- **Real-time Data**: Dữ liệu real-time

## API Endpoints

### Game APIs
- **Player APIs**: Quản lý người chơi
- **Battle APIs**: Hệ thống chiến đấu
- **Guild APIs**: Hệ thống guild
- **Rank APIs**: Hệ thống xếp hạng
- **Mail APIs**: Hệ thống thư
- **Friend APIs**: Hệ thống bạn bè

### Internal APIs
- **Cache APIs**: Cache operations
- **Cross APIs**: Cross-server operations
- **Payment APIs**: Payment processing
- **GM APIs**: GM operations

## Module System

### Core Modules
1. **Account Module**: Quản lý tài khoản
2. **Player Module**: Quản lý người chơi
3. **Battle Module**: Hệ thống chiến đấu
4. **Guild Module**: Hệ thống guild
5. **Rank Module**: Hệ thống xếp hạng

### Activity Modules
1. **Activity Cross Group**: Hoạt động liên server
2. **Activity Hero Awaken**: Hoạt động thức tỉnh hero
3. **Activity Red Envelope**: Hoạt động bao lì xì
4. **Activity Treasure Bowl**: Hoạt động bát kho báu
5. **Activity Super Gold**: Hoạt động vàng siêu cấp

### System Modules
1. **Auction House**: Nhà đấu giá
2. **Mail System**: Hệ thống thư
3. **Friend System**: Hệ thống bạn bè
4. **Chat System**: Hệ thống chat
5. **Silence System**: Hệ thống cấm chat

## Performance & Monitoring

### Metrics
- **CCU (Concurrent Users)**: Số người chơi đồng thời
- **Request Rate**: Tốc độ xử lý request
- **Response Time**: Thời gian phản hồi
- **Error Rate**: Tỷ lệ lỗi
- **Memory Usage**: Sử dụng bộ nhớ
- **CPU Usage**: Sử dụng CPU

### Logging
- **Logic Log**: Log logic game
- **User Info Log**: Log thông tin người chơi
- **Packet Log**: Log gói tin
- **Error Log**: Log lỗi
- **Performance Log**: Log hiệu suất

### Monitoring Tools
- **Prometheus**: Metrics collection
- **FeiShu Bot**: Alert notifications
- **Holmes**: Memory leak detection
- **PProf**: Performance profiling

## Security Features

### Authentication
- **JWT Token**: Xác thực người chơi
- **Session Management**: Quản lý phiên đăng nhập
- **Token Validation**: Xác thực token

### Rate Limiting
- **Request Rate Limiting**: Giới hạn tốc độ request
- **API Rate Limiting**: Giới hạn tốc độ API
- **User Rate Limiting**: Giới hạn tốc độ người dùng

### Data Protection
- **Data Encryption**: Mã hóa dữ liệu
- **Secure Communication**: Giao tiếp bảo mật
- **Input Validation**: Xác thực đầu vào

## Deployment

### Build
```bash
cd gamexshard
go build -o gamexshard
```

### Run
```bash
# Development
./gamexshard game -c config.toml

# Production
./gamexshard game -c config_g32.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o gamexshard

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/gamexshard .
CMD ["./gamexshard", "game"]
```

## Troubleshooting

### Common Issues
1. **Service không khởi động**: Kiểm tra port conflicts
2. **Database connection**: Kiểm tra database credentials
3. **etcd connection**: Kiểm tra etcd service
4. **Memory issues**: Kiểm tra resource usage

### Debug Tools
- **PProf**: Performance profiling
- **Goroutine**: Goroutine analysis
- **Holmes**: Memory leak detection
- **Logic Log**: Game logic debugging

### Health Checks
- **Startup Check**: Kiểm tra khởi động
- **Health Endpoint**: Endpoint kiểm tra sức khỏe
- **Metrics Endpoint**: Endpoint metrics

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Gamedata**: Game data
- **Proto**: Protocol definitions

### External Dependencies
- **MySQL**: Database
- **MongoDB**: NoSQL database
- **Redis**: Cache
- **NATS**: Message queue
- **etcd**: Service discovery

## Development

### Code Structure
- **Handlers**: Request handlers
- **Modules**: Game modules
- **API**: API endpoints
- **Core**: Core game logic

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Performance Tests**: Performance testing

### Hot Reload
- **Hot Patch**: Hot reload code
- **Config Reload**: Reload configuration
- **Data Reload**: Reload game data

## Contact

- **Author**: ZhangZhen
- **Email**: zhangzhen@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service gamexshard - trái tim của hệ thống game server JWS2.*
