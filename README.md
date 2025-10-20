# JWS2 Game Server - Tài liệu chi tiết

## Tổng quan

Đây là bộ tài liệu chi tiết về hệ thống backend game server JWS2, bao gồm phân tích sâu về từng service, kiến trúc tổng thể, và hướng dẫn triển khai.

## Cấu trúc tài liệu

### 1. [00-Project-Overview.md](./00-Project-Overview.md)
**Tổng quan dự án**
- Kiến trúc tổng thể hệ thống
- Công nghệ sử dụng
- Cấu trúc dự án
- Giao thức giao tiếp
- Hướng dẫn triển khai

### 2. [01-Gamexshard-Service.md](./01-Gamexshard-Service.md)
**Game Server Chính**
- Service game server chính (port 8080)
- 100+ modules trong hệ thống
- 1143 handler files
- Core system với account, battle, scene
- API system với nhiều endpoints

### 3. [02-Battlex-Service.md](./02-Battlex-Service.md)
**Battle Server**
- Server xử lý battle real-time (port 8701)
- KCP protocol cho low latency
- 20 FPS frame processing
- Battle room management
- Frame synchronization

### 4. [03-Scene-Service.md](./03-Scene-Service.md)
**Scene Server**
- Server xử lý scene và di chuyển (port 10003)
- Screen system (30x30 units)
- 9-screen navigation
- NPC management
- Object management

### 5. [04-Crossx-Service.md](./04-Crossx-Service.md)
**Cross Server**
- Server giao tiếp liên server (port 10004)
- Leader-Member cluster architecture
- 100+ modules cho cross server
- Country management
- Cross scene system

### 6. [05-Cachex-Service.md](./05-Cachex-Service.md)
**Cache Server**
- Server quản lý cache (port 10005)
- Player cache, Guild cache, Send cache
- Multiple cache strategies
- Performance optimization
- Memory management

### 7. [06-Payx-Service.md](./06-Payx-Service.md)
**Payment Server**
- Server xử lý thanh toán (port 2019)
- Multiple payment channels
- SDK integration (NT, Hero, 3rd party)
- Security features
- MongoDB integration

### 8. [07-Gmserver-Service.md](./07-Gmserver-Service.md)
**Game Management Server**
- Server quản lý game và GM tools (port 8808)
- Player management
- Guild management
- Payment management
- Game management

### 9. [08-BattlexAgent-Service.md](./08-BattlexAgent-Service.md)
**Battle Agent Server**
- Server agent xử lý battle (port 8705)
- Proxy connection management
- Load balancing
- Service discovery
- Connection pooling

### 10. [09-ClientBiLog-Service.md](./09-ClientBiLog-Service.md)
**Client Business Intelligence Log Server**
- Server thu thập dữ liệu BI từ client (port 8089)
- Client log collection
- IP geolocation
- Data processing
- Rate limiting

### 11. [10-Merger-Service.md](./10-Merger-Service.md)
**Server Merge Service**
- Server xử lý hợp nhất server (port 7101)
- Distributed merge processing
- Worker system
- Progress tracking
- Data consolidation

### 12. [11-Friendx-Service.md](./11-Friendx-Service.md)
**Friend Management Server**
- Server quản lý bạn bè (gRPC internal)
- Friend operations
- Blacklist management
- Rival system
- LRU cache system

### 13. [12-Pingserver-Service.md](./12-Pingserver-Service.md)
**Ping Test Server**
- Server kiểm tra ping (port 8601)
- KCP protocol testing
- Network performance measurement
- Connection quality testing
- Service discovery

### 14. [13-Sensitiveword-Service.md](./13-Sensitiveword-Service.md)
**Sensitive Word Filter Server**
- Server lọc từ nhạy cảm (gRPC internal)
- Content filtering
- Word detection
- Content sanitization
- Batch processing

### 15. [14-Servercheck-Service.md](./14-Servercheck-Service.md)
**Server Health Check Service**
- Server kiểm tra sức khỏe server (gRPC internal)
- Multi-service monitoring
- Health checking
- Alert system
- FeiShu integration

### 16. [15-Stressx-Service.md](./15-Stressx-Service.md)
**Stress Testing Server**
- Server kiểm tra tải (gRPC internal)
- Robot simulation
- Load testing
- Performance testing
- Action state management

## Kiến trúc tổng thể

### Microservices Architecture
```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Client        │    │   Client        │    │   Client        │
│   (Mobile)      │    │   (Web)         │    │   (Desktop)     │
└─────────┬───────┘    └─────────┬───────┘    └─────────┬───────┘
          │                      │                      │
          └──────────────────────┼──────────────────────┘
                                 │
                    ┌─────────────┴─────────────┐
                    │        Gatex              │
                    │     (Gateway)             │
                    │     Port: 8667            │
                    └─────────────┬─────────────┘
                                 │
                    ┌─────────────┴─────────────┐
                    │      Gamexshard           │
                    │    (Game Server)          │
                    │     Port: 8080            │
                    └─────────────┬─────────────┘
                                 │
        ┌────────────────────────┼────────────────────────┐
        │                       │                        │
┌───────┴────────┐    ┌─────────┴─────────┐    ┌─────────┴─────────┐
│    Battlex     │    │      Scene        │    │      Crossx       │
│ (Battle Server)│    │  (Scene Server)   │    │ (Cross Server)    │
│   Port: 8701   │    │   Port: 10003     │    │   Port: 10004     │
└─────────┬──────┘    └───────────────────┘    └───────────────────┘
          │
┌─────────┴────────┐
│  BattlexAgent    │
│ (Battle Agent)   │
│   Port: 8705     │
└──────────────────┘

        ┌─────────────────────────────────────────────────────────┐
        │                                                         │
┌───────┴────────┐    ┌─────────┴─────────┐    ┌─────────┴─────────┐
│    Cachex      │    │       Payx        │    │     Gmserver      │
│ (Cache Server) │    │ (Payment Server)  │    │  (GM Server)      │
│   Port: 10005  │    │   Port: 2019      │    │   Port: 8808      │
└────────────────┘    └───────────────────┘    └───────────────────┘

┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   ClientBiLog   │    │     Merger      │    │    Friendx      │
│ (BI Log Server) │    │ (Merge Server)  │    │ (Friend Server) │
│   Port: 8089    │    │   Port: 7101    │    │   gRPC Internal │
└─────────────────┘    └─────────────────┘    └─────────────────┘

┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Pingserver    │    │  Sensitiveword  │    │   Servercheck   │
│ (Ping Server)   │    │ (Filter Server) │    │ (Health Check)  │
│   Port: 8601    │    │  gRPC Internal  │    │  gRPC Internal  │
└─────────────────┘    └─────────────────┘    └─────────────────┘

┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│     Stressx     │    │   Other Services│    │   Future Services│
│ (Stress Testing)│    │   (Auth, etc.)  │    │   (New Features)│
│  gRPC Internal  │    │                 │    │                 │
└─────────────────┘    └─────────────────┘    └─────────────────┘
```

### Giao thức giao tiếp

#### 1. Client ↔ Server
- **TCP/TLS**: Gatex (port 8667) - Chính
- **WebSocket**: Cometx (port 10001) - Chat
- **HTTP/HTTPS**: Các API endpoints
- **HTTP**: ClientBiLog (port 8089) - BI Log collection

#### 2. Inter-Service Communication
- **gRPC**: Giao tiếp giữa các service
- **NATS**: Message queue
- **etcd**: Service discovery
- **Proxy**: BattlexAgent (port 8705) - Battle proxy
- **Merge**: Merger (port 7101) - Server merge operations

#### 3. Database
- **MySQL**: Game data chính
- **MongoDB**: NoSQL data
- **Redis**: Cache và session
- **Pika**: Profile storage

## Công nghệ sử dụng

### Backend
- **Go (Golang)**: Ngôn ngữ chính
- **gRPC**: Giao tiếp nội bộ
- **HTTP/HTTPS**: API endpoints
- **WebSocket**: Real-time communication
- **TCP/TLS**: Client communication

### Database
- **MySQL**: Relational database
- **MongoDB**: Document database
- **Redis**: In-memory cache
- **Pika**: Redis-compatible storage

### Infrastructure
- **etcd**: Service discovery
- **NATS**: Message queue
- **Docker**: Containerization
- **Kubernetes**: Orchestration

### Monitoring
- **Prometheus**: Metrics collection
- **FeiShu Bot**: Alerting
- **Logging**: Comprehensive logging

## Hướng dẫn sử dụng

### 1. Đọc tài liệu theo thứ tự
1. Bắt đầu với [00-Project-Overview.md](./00-Project-Overview.md) để hiểu tổng quan
2. Đọc từng service README (16 services) để hiểu chi tiết
3. Tham khảo cấu hình và deployment

### 2. Triển khai hệ thống
1. Cài đặt dependencies (Go, MySQL, MongoDB, Redis, etcd)
2. Cấu hình từng service theo file config
3. Build và chạy các service
4. Kiểm tra kết nối và monitoring

### 3. Phát triển
1. Hiểu kiến trúc microservices
2. Tham khảo code structure của từng service
3. Sử dụng common libraries
4. Tuân thủ coding standards

## Liên hệ

- **Company**: Ticore Game Company
- **Project**: JWS2 Game Server
- **Documentation**: Chi tiết và cập nhật

---

*Bộ tài liệu này cung cấp cái nhìn toàn diện về hệ thống JWS2 Game Server, từ kiến trúc tổng thể đến chi tiết từng service.*
