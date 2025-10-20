# Battlex Service - Battle Server

## Tổng quan

**Battlex** là service xử lý logic chiến đấu real-time của hệ thống JWS2, chịu trách nhiệm quản lý các phòng battle, xử lý frame data, và đảm bảo đồng bộ hóa giữa các người chơi trong battle.

## Thông tin cơ bản

- **Tên service**: battlex
- **Port chính**: 8701 (KCP + TCP)
- **Protocol**: KCP (KCP over UDP), TCP
- **Frame Rate**: 20 FPS (50ms per frame)
- **Ngôn ngữ**: Go (Golang)
- **Kiến trúc**: Real-time battle server

## Cấu trúc thư mục

```
battlex/
├── battle/                    # Core battle logic
│   ├── battle_conn_agent.go   # Connection agent
│   ├── battle_check_helper.go # Battle check helper
│   ├── player.go             # Player management
│   ├── room.go               # Room management
│   ├── RoomFrameMgr.go       # Frame manager
│   ├── roommgr.go            # Room manager
│   └── server.go             # Battle server
├── battleforcheck/           # Battle replay data
├── battlenet/                # Network layer
│   ├── kcp.go               # KCP implementation
│   ├── net.go               # Network utilities
│   └── tcp.go               # TCP implementation
├── battleproto/              # Battle protocol
│   ├── cmd.go               # Command definitions
│   ├── cmdb2cconnectdone.go # Connect done command
│   ├── cmdb2cframe.go       # Frame command
│   ├── cmdb2cmsg.go         # Message command
│   ├── cmdc2bbattleend.go   # Battle end command
│   ├── cmdc2bdebug.go       # Debug command
│   ├── cmdc2bframe.go       # Client frame command
│   ├── cmdc2bhello.go       # Hello command
│   ├── cmdc2bready.go       # Ready command
│   ├── cmdpingpong.go       # Ping pong command
│   └── reg.go               # Protocol registration
├── cmds/                     # Command line interface
├── conf/                     # Configuration files
├── config/                   # Config management
└── core/                     # Core utilities
```

## Chức năng chính

### 1. Battle Room Management
- **Room Creation**: Tạo phòng battle mới
- **Player Management**: Quản lý người chơi trong phòng
- **Room State**: Quản lý trạng thái phòng battle
- **Room Cleanup**: Dọn dẹp phòng khi kết thúc

### 2. Real-time Frame Processing
- **Frame Rate**: 20 FPS (50ms per frame)
- **Frame Sync**: Đồng bộ hóa frame giữa các người chơi
- **Frame Validation**: Xác thực frame data
- **Frame Replay**: Lưu trữ frame để replay

### 3. Network Protocol
- **KCP Protocol**: KCP over UDP cho real-time
- **TCP Protocol**: TCP backup cho reliability
- **Message Types**: Các loại message battle
- **Command System**: Hệ thống command

### 4. Battle Types
- **Online Solo**: Solo battle
- **Team PVE**: Team player vs environment
- **Guild Boss**: Guild boss battle
- **World Boss**: World boss battle
- **Rebel Strike**: Rebel strike battle

## Giao thức kết nối

### 1. Client ↔ Battlex
- **Protocol**: KCP over UDP (primary), TCP (backup)
- **Port**: 8701
- **Features**:
  - Low latency UDP
  - Reliable delivery
  - Congestion control
  - Fast retransmission

### 2. Gamexshard ↔ Battlex
- **Protocol**: gRPC
- **Purpose**: Battle room management
- **Commands**:
  - Create battle room
  - Join battle room
  - Leave battle room
  - Battle result

### 3. BattlexAgent ↔ Battlex
- **Protocol**: gRPC
- **Purpose**: Battle agent communication
- **Port**: 9999 (AgentServerPort)

## Cấu hình chính

### Development Config
```toml
[BattleConfig]
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

gid = 12
serverid = "battlex1"
internalip = "127.0.0.1"
publicip = "185.238.251.18:8701"
listen = ":8701"

# Battle settings
frame2file = false
frame2filepath = "./battleforcheck/"
importantBattle2filepath = "./importantbattle/"

# Agent settings
AgentServerPort = "9999"
```

### Production Config
```toml
# Similar structure with production values
gid = 32
serverid = "battlex1"
publicip = "aws"  # Production environment
```

## Battle Protocol

### Message Types
```go
const (
    C2BHello        MsgTypID = iota + 1  // Client to Battle Hello
    C2BReady                            // Client Ready
    C2BFrame                            // Client Frame Data
    C2BBattleEnd                        // Battle End
    C2BDebug                           // Debug Command
    B2CConnectDone                     // Battle to Client Connect Done
    B2CFrame                           // Battle Frame Data
    B2CMsg                             // Battle Message
    PingPong                           // Ping Pong
    MsgTypCount
)
```

### Frame Data Structure
```go
type PlayerFrameData struct {
    PlayerId byte
    FrameId  uint32
    Data     []byte
}
```

### Room ID Generation
```go
// Conv = (RoomID * 100 + battleId) * 100 + PlayerID
// Max 100 players per room
func genRoomID() uint32 {
    curRoomId += 1
    if curRoomId > maxRoomID {
        curRoomId = 1
    }
    return curRoomId*battleconfig.RoomIDBegin + battleconfig.Cfg.ServerIndex
}
```

## Performance & Optimization

### Frame Rate Management
- **Target FPS**: 20 FPS
- **Frame Time**: 50ms per frame
- **Sync Delay**: 1ms
- **Warning Threshold**: 50ms

### Network Optimization
- **KCP Settings**:
  - Stream Mode: false
  - No Delay: 1, 20, 2, 1
  - MTU: 1400
  - ACK No Delay: false
  - Read Buffer: 16KB
  - Write Buffer: 16KB

### Memory Management
- **Connection Pooling**: Reuse connections
- **Buffer Management**: Efficient buffer handling
- **Garbage Collection**: Optimized GC

## Battle Replay System

### Replay Storage
- **Path**: `./battleforcheck/`
- **Format**: `.p1` files
- **Structure**: `GRoomId{roomId}.{gid}.{date}.{time}.{server}.p1`
- **Content**: Frame data for replay

### Important Battle Replay
- **Path**: `./importantbattle/`
- **Purpose**: Store important battles for analysis
- **Trigger**: Special battle types or conditions

## Monitoring & Logging

### Metrics
- **Battle Count**: Số lượng battle đang diễn ra
- **Player Count**: Số lượng người chơi online
- **Frame Rate**: Tốc độ xử lý frame
- **Network Latency**: Độ trễ mạng
- **Error Rate**: Tỷ lệ lỗi

### Logging
- **Battle Log**: Log chi tiết battle
- **Network Log**: Log network events
- **Error Log**: Log lỗi hệ thống
- **Performance Log**: Log hiệu suất

## Error Handling

### Connection Errors
- **Broken Pipe**: Handle gracefully
- **Timeout**: Retry mechanism
- **Invalid Data**: Validation and rejection

### Battle Errors
- **Room Full**: Reject new players
- **Invalid Frame**: Skip invalid frames
- **Player Disconnect**: Handle disconnection

## Security Features

### Data Validation
- **Frame Validation**: Validate frame data
- **Command Validation**: Validate commands
- **Player Validation**: Validate player data

### Anti-Cheat
- **Frame Check**: Check frame consistency
- **Speed Check**: Check movement speed
- **Action Check**: Check action validity

## Deployment

### Build
```bash
cd battlex
go build -o battlex
```

### Run
```bash
# Development
./battlex battle -c battle.toml

# Production
./battlex battle -c config_g32.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o battlex

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/battlex .
CMD ["./battlex", "battle"]
```

## Troubleshooting

### Common Issues
1. **High Latency**: Kiểm tra network configuration
2. **Frame Drops**: Kiểm tra server performance
3. **Connection Issues**: Kiểm tra KCP settings
4. **Memory Leaks**: Kiểm tra connection cleanup

### Debug Tools
- **Frame Replay**: Replay battle frames
- **Network Monitor**: Monitor network traffic
- **Performance Profiler**: Profile performance
- **Log Analysis**: Analyze logs

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Battle Common**: Battle utilities
- **Proto**: Protocol definitions

### External Dependencies
- **KCP**: KCP library
- **etcd**: Service discovery
- **gRPC**: Internal communication

## Development

### Code Structure
- **Battle Logic**: Core battle logic
- **Network Layer**: Network handling
- **Protocol Layer**: Message protocol
- **Room Management**: Room handling

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Performance Tests**: Performance testing
- **Load Tests**: Load testing

## Contact

- **Author**: ZhangZhen
- **Email**: zhangzhen@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service battlex - server xử lý battle real-time của hệ thống JWS2.*
