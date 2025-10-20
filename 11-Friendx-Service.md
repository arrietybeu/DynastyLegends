# Friendx Service - Friend Management Server

## Tổng quan

**Friendx** là service quản lý bạn bè của hệ thống JWS2, chịu trách nhiệm xử lý các chức năng liên quan đến bạn bè, danh bạ, đối thủ, và các tương tác xã hội trong game.

## Thông tin cơ bản

- **Tên service**: friendx
- **Port chính**: gRPC (internal)
- **Server ID**: friendx1
- **Protocol**: gRPC, NATS
- **Ngôn ngữ**: Go (Golang)
- **Database**: Redis
- **Kiến trúc**: Friend management server với LRU cache

## Cấu trúc thư mục

```
friendx/
├── api/                      # API layer
│   ├── gamex_push.go        # Gamex push API
│   └── player_lru_manager.go # Player LRU manager
├── cmds/                     # Command line interface
│   ├── cmds.go              # Commands
│   ├── common.go            # Common utilities
│   └── friend/               # Friend commands
│       └── friend.go        # Friend command implementation
├── conf/                    # Configuration files
│   ├── config.toml          # Main config
│   ├── config_g32.toml      # Production config
│   ├── logiclog.toml        # Logic log config
│   └── metrics.toml         # Metrics config
├── config/                  # Config management
│   ├── config.go            # Config utilities
│   └── const.go             # Constants
├── db/                      # Database layer
│   └── redis/               # Redis implementation
│       └── dbpool.go        # Database pool
├── model/                   # Data models
│   ├── friend/              # Friend models
│   │   └── player_friend_info.go # Player friend info
│   └── lru/                 # LRU models
│       ├── lru_safe.go      # Safe LRU implementation
│       └── lru_unsafe.go    # Unsafe LRU implementation
├── modules/                 # Friend modules
│   ├── friend/              # Friend module
│   │   ├── clean_players.go # Clean players
│   │   ├── gen.go           # Generated code
│   │   ├── nats_handlers.go # NATS handlers
│   │   ├── player_lru_mgr.go # Player LRU manager
│   │   └── submodule/       # Submodules
│   │       ├── acceptor/    # Acceptor submodule
│   │       │   ├── add_black.go # Add blacklist
│   │       │   ├── add_contact.go # Add contact
│   │       │   ├── add_single_contact.go # Add single contact
│   │       │   ├── add_update_rival.go # Add/update rival
│   │       │   ├── agree_all_friend_application.go # Agree all friend applications
│   │       │   ├── agree_friend_application.go # Agree friend application
│   │       │   ├── cheat_handle.go # Cheat handling
│   │       │   ├── define.go # Definitions
│   │       │   ├── define_test.go # Definition tests
│   │       │   ├── refuse_all_friend_application.go # Refuse all friend applications
│   │       │   ├── refuse_friend_application.go # Refuse friend application
│   │       │   ├── remove_black.go # Remove from blacklist
│   │       │   ├── remove_friend.go # Remove friend
│   │       │   ├── remove_rival.go # Remove rival
│   │       │   ├── send_friend_application.go # Send friend application
│   │       │   ├── sync_l2_cache.go # Sync L2 cache
│   │       │   └── update_rival_hatreds.go # Update rival hatreds
│   │       ├── sub/         # Sub module
│   │       │   └── const.go # Constants
│   │       ├── submodule_db.go # Submodule database
│   │       ├── submodule_del.go # Submodule delete
│   │       └── submodule_pool.go # Submodule pool
│   ├── modules_gen.go       # Generated modules
│   └── modules_mgr.go       # Module manager
├── util/                    # Utilities
│   ├── convert.go           # Convert utilities
│   └── convert_test.go      # Convert tests
└── main.go                  # Main entry point
```

## Chức năng chính

### 1. Friend Management
- **Add Friend**: Thêm bạn bè
- **Remove Friend**: Xóa bạn bè
- **Friend List**: Danh sách bạn bè
- **Friend Status**: Trạng thái bạn bè

### 2. Friend Applications
- **Send Application**: Gửi lời mời kết bạn
- **Agree Application**: Đồng ý lời mời
- **Refuse Application**: Từ chối lời mời
- **Application List**: Danh sách lời mời

### 3. Blacklist Management
- **Add to Blacklist**: Thêm vào danh sách đen
- **Remove from Blacklist**: Xóa khỏi danh sách đen
- **Blacklist List**: Danh sách đen
- **Blacklist Check**: Kiểm tra danh sách đen

### 4. Rival Management
- **Add Rival**: Thêm đối thủ
- **Remove Rival**: Xóa đối thủ
- **Update Rival**: Cập nhật đối thủ
- **Rival Hatreds**: Mức độ thù hận

### 5. Contact Management
- **Add Contact**: Thêm liên hệ
- **Remove Contact**: Xóa liên hệ
- **Contact List**: Danh sách liên hệ
- **Contact Update**: Cập nhật liên hệ

### 6. Cache Management
- **LRU Cache**: Cache LRU
- **Player Cache**: Cache người chơi
- **Cache Sync**: Đồng bộ cache
- **Cache Cleanup**: Dọn dẹp cache

## Giao thức kết nối

### 1. Gamexshard ↔ Friendx
- **Protocol**: gRPC
- **Purpose**: Friend operations
- **Commands**:
  - Add friend
  - Remove friend
  - Get friend list
  - Send friend application
  - Agree/refuse application

### 2. Friendx ↔ Redis
- **Protocol**: Redis protocol
- **Purpose**: Data storage
- **Features**:
  - Friend data storage
  - Cache management
  - Session storage

### 3. Friendx ↔ NATS
- **Protocol**: NATS
- **Purpose**: Message queue
- **Features**:
  - Friend notifications
  - Status updates
  - Event handling

## Cấu hình chính

### Development Config
```toml
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

internalip = "127.0.0.1"
serverid = "friendx1"
gid = 12
```

### Production Config
```toml
# Similar structure with production values
gid = 32
serverid = "friendx1"
internalip = "production_ip"
```

## Friend System Features

### 1. Friend Operations
- **Add Friend**: Thêm bạn bè
- **Remove Friend**: Xóa bạn bè
- **Friend List**: Danh sách bạn bè
- **Friend Status**: Trạng thái bạn bè
- **Friend Search**: Tìm kiếm bạn bè

### 2. Application System
- **Send Application**: Gửi lời mời
- **Agree Application**: Đồng ý lời mời
- **Refuse Application**: Từ chối lời mời
- **Application History**: Lịch sử lời mời
- **Bulk Operations**: Thao tác hàng loạt

### 3. Blacklist System
- **Add to Blacklist**: Thêm vào danh sách đen
- **Remove from Blacklist**: Xóa khỏi danh sách đen
- **Blacklist Check**: Kiểm tra danh sách đen
- **Blacklist Management**: Quản lý danh sách đen

### 4. Rival System
- **Add Rival**: Thêm đối thủ
- **Remove Rival**: Xóa đối thủ
- **Update Rival**: Cập nhật đối thủ
- **Rival Hatreds**: Mức độ thù hận
- **Rival Tracking**: Theo dõi đối thủ

## LRU Cache System

### 1. Player LRU Manager
- **Player Caching**: Cache người chơi
- **LRU Eviction**: Loại bỏ LRU
- **Cache Size**: Kích thước cache
- **Cache Performance**: Hiệu suất cache

### 2. Cache Operations
- **Cache Get**: Lấy từ cache
- **Cache Set**: Đặt vào cache
- **Cache Delete**: Xóa khỏi cache
- **Cache Update**: Cập nhật cache

### 3. Cache Synchronization
- **L2 Cache Sync**: Đồng bộ cache L2
- **Cache Invalidation**: Vô hiệu hóa cache
- **Cache Warming**: Làm nóng cache
- **Cache Cleanup**: Dọn dẹp cache

## Data Models

### 1. Player Friend Info
```go
type PlayerFriendInfo struct {
    PlayerID    string
    FriendList  []string
    BlackList   []string
    RivalList   []string
    ContactList []string
    Applications []FriendApplication
}
```

### 2. Friend Application
```go
type FriendApplication struct {
    FromPlayerID string
    ToPlayerID   string
    Message      string
    Timestamp    int64
    Status       string
}
```

### 3. LRU Cache
```go
type LRUCache struct {
    capacity int
    cache    map[string]*Node
    head     *Node
    tail     *Node
}
```

## Performance & Optimization

### 1. Cache Optimization
- **LRU Strategy**: Chiến lược LRU
- **Memory Management**: Quản lý memory
- **Cache Hit Rate**: Tỷ lệ hit cache
- **Cache Performance**: Hiệu suất cache

### 2. Database Optimization
- **Redis Optimization**: Tối ưu Redis
- **Connection Pooling**: Pool kết nối
- **Query Optimization**: Tối ưu query
- **Data Compression**: Nén dữ liệu

### 3. Network Optimization
- **gRPC Optimization**: Tối ưu gRPC
- **NATS Optimization**: Tối ưu NATS
- **Message Batching**: Gộp message
- **Compression**: Nén dữ liệu

## Monitoring & Logging

### Metrics
- **Friend Count**: Số lượng bạn bè
- **Application Count**: Số lượng lời mời
- **Cache Hit Rate**: Tỷ lệ hit cache
- **Response Time**: Thời gian phản hồi
- **Error Rate**: Tỷ lệ lỗi
- **Memory Usage**: Sử dụng memory

### Logging
- **Friend Log**: Log bạn bè
- **Application Log**: Log lời mời
- **Cache Log**: Log cache
- **Error Log**: Log lỗi
- **Performance Log**: Log hiệu suất

## Error Handling

### Friend Errors
- **Friend Not Found**: Không tìm thấy bạn bè
- **Already Friends**: Đã là bạn bè
- **Friend Limit Reached**: Đã đạt giới hạn bạn bè
- **Invalid Friend ID**: ID bạn bè không hợp lệ

### Application Errors
- **Application Not Found**: Không tìm thấy lời mời
- **Already Applied**: Đã gửi lời mời
- **Application Expired**: Lời mời đã hết hạn
- **Invalid Application**: Lời mời không hợp lệ

## Security Features

### 1. Data Security
- **Data Validation**: Xác thực dữ liệu
- **Input Sanitization**: Làm sạch input
- **Access Control**: Kiểm soát truy cập
- **Data Encryption**: Mã hóa dữ liệu

### 2. Friend Security
- **Friend Verification**: Xác thực bạn bè
- **Spam Protection**: Bảo vệ spam
- **Rate Limiting**: Giới hạn tốc độ
- **Blacklist Protection**: Bảo vệ danh sách đen

## Deployment

### Build
```bash
cd friendx
go build -o friendx
```

### Run
```bash
# Development
./friendx friend -c config.toml

# Production
./friendx friend -c config_g32.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o friendx

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/friendx .
CMD ["./friendx", "friend"]
```

## Troubleshooting

### Common Issues
1. **High Memory Usage**: Kiểm tra cache size
2. **Slow Response**: Kiểm tra Redis performance
3. **Cache Miss**: Kiểm tra cache strategy
4. **Friend Sync Issues**: Kiểm tra NATS connection

### Debug Tools
- **Friend Inspector**: Kiểm tra bạn bè
- **Cache Monitor**: Giám sát cache
- **Redis Monitor**: Giám sát Redis
- **Log Analysis**: Phân tích log

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Friend Common**: Friend utilities
- **Proto**: Protocol definitions

### External Dependencies
- **Redis**: Data storage
- **etcd**: Service discovery
- **NATS**: Message queue
- **gRPC**: Internal communication
- **PlanX**: Platform framework

## Development

### Code Structure
- **Friend Logic**: Logic bạn bè
- **Cache System**: Hệ thống cache
- **API Layer**: Lớp API
- **Data Layer**: Lớp dữ liệu

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Cache Tests**: Cache testing
- **Friend Tests**: Friend testing

## Contact

- **Author**: ZhangZhen
- **Email**: zhangzhen@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service friendx - server quản lý bạn bè của hệ thống JWS2.*
