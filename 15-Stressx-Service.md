# Stressx Service - Stress Testing Server

## Tổng quan

**Stressx** là service kiểm tra tải và stress testing của hệ thống JWS2, chịu trách nhiệm tạo ra các robot người chơi giả lập để kiểm tra hiệu suất, độ ổn định và khả năng chịu tải của toàn bộ hệ thống game.

## Thông tin cơ bản

- **Tên service**: stressx
- **Port chính**: gRPC (internal)
- **Server ID**: stress1
- **Protocol**: gRPC, TCP, KCP
- **Ngôn ngữ**: Go (Golang)
- **Kiến trúc**: Stress testing server với robot simulation

## Cấu trúc thư mục

```
stressx/
├── cmds/                     # Command line interface
│   └── stress/              # Stress command
│       └── stress.go        # Stress implementation
├── conf/                    # Configuration files
│   ├── config.toml          # Main config
│   ├── metrics.toml         # Metrics config
│   └── tracing.toml         # Tracing config
├── config/                  # Config management
│   ├── cloud_db.go          # Cloud database config
│   └── config.go            # Config utilities
├── forgm/                   # For GM functionality
│   ├── create_ctx.go        # Create context
│   ├── create_robot.go      # Create robot
│   ├── create_test/         # Create test
│   │   ├── create_test.go   # Create test implementation
│   │   └── player_archive.json # Player archive data
│   ├── forgm.go             # For GM main
│   ├── get_actions.go       # Get actions
│   ├── patch_create_account.go # Patch create account
│   ├── query_running_robot.go # Query running robot
│   └── stop_robot_action.go # Stop robot action
├── robot/                   # Robot implementation
│   ├── actions_watch.go     # Actions watch
│   ├── actionstate/         # Action state management
│   │   ├── 1v1.go           # 1v1 battle
│   │   ├── action_tree.go   # Action tree
│   │   ├── battlecheck.go   # Battle check
│   │   ├── chat/            # Chat actions
│   │   │   ├── chat_pb_hitstory.go # Chat history
│   │   │   ├── chat_pb_joinroom.go # Join room
│   │   │   ├── chat_pb_leaveroom.go # Leave room
│   │   │   ├── chat_pb_speak.go # Speak
│   │   │   ├── chat_pb_translate.go # Translate
│   │   │   ├── chat.go      # Chat implementation
│   │   │   └── common.go    # Chat common
│   │   ├── chat_gamex.go    # Chat gamex
│   │   ├── cheat.go         # Cheat actions
│   │   ├── common/          # Common actions
│   │   │   ├── action_state_sub_enter.go # Sub enter
│   │   │   ├── action_state_sub_param.go # Sub param
│   │   │   ├── major_task.go # Major task
│   │   │   ├── robot_common.go # Robot common
│   │   │   ├── sub_action.go # Sub action
│   │   │   └── sub_state.go # Sub state
│   │   ├── common.go        # Common implementation
│   │   ├── ConveyIdle.go    # Convey idle
│   │   ├── cross_cache.go   # Cross cache
│   │   ├── crossx.go        # Crossx actions
│   │   ├── custom.go        # Custom actions
│   │   ├── dig_treasure.go  # Dig treasure
│   │   ├── dragon_artery.go # Dragon artery
│   │   ├── dynamic_scene.go # Dynamic scene
│   │   ├── escort.go        # Escort actions
│   │   ├── fair_tournament.go # Fair tournament
│   │   ├── fightthrone.go   # Fight throne
│   │   ├── friend/          # Friend actions
│   │   │   ├── behavior_register.go # Behavior register
│   │   │   ├── behavior_req.go # Behavior request
│   │   │   ├── behavior_resp.go # Behavior response
│   │   │   ├── friend.go    # Friend implementation
│   │   │   ├── select_pool.go # Select pool
│   │   │   └── support.go   # Support
│   │   ├── get_cache.go     # Get cache
│   │   ├── gold_hero_boss.go # Gold hero boss
│   │   ├── guild/           # Guild actions
│   │   │   ├── common.go    # Guild common
│   │   │   ├── country_pvp.go # Country PvP
│   │   │   ├── country.go   # Country actions
│   │   │   ├── countryNightBoss.go # Country night boss
│   │   │   ├── guild_boss.go # Guild boss
│   │   │   ├── guild_join_rand.go # Guild join random
│   │   │   ├── guild_join.go # Guild join
│   │   │   ├── guild_leave.go # Guild leave
│   │   │   ├── guild_war.go # Guild war
│   │   │   ├── guild.go     # Guild implementation
│   │   │   ├── gvg.go       # GvG
│   │   │   ├── mine.go      # Mine actions
│   │   │   ├── power.go     # Power actions
│   │   │   ├── red_envelope_rain.go # Red envelope rain
│   │   │   └── siege_city.go # Siege city
│   │   ├── hero_match_3v3.go # Hero match 3v3
│   │   ├── hero_match.go    # Hero match
│   │   ├── hero_stress.go   # Hero stress
│   │   ├── idle.go          # Idle actions
│   │   ├── load_save.go     # Load save
│   │   ├── login.go         # Login actions
│   │   ├── major_task.go    # Major task
│   │   ├── multiplayer_dungeon.go # Multiplayer dungeon
│   │   ├── nats_echo.go     # NATS echo
│   │   ├── official_privilege.go # Official privilege
│   │   ├── pet_breed.go     # Pet breed
│   │   ├── pet_capture.go   # Pet capture
│   │   ├── rebelstrike.go   # Rebel strike
│   │   ├── rogue.go         # Rogue actions
│   │   ├── scene_quiz.go    # Scene quiz
│   │   ├── storm.go         # Storm actions
│   │   ├── sync_four_country.go # Sync four country
│   │   ├── team_cmd.go      # Team command
│   │   ├── team_fair.go     # Team fair
│   │   ├── team_resp.go     # Team response
│   │   ├── team.go          # Team actions
│   │   ├── teammatch.go     # Team match
│   │   ├── three_teamspk.go # Three teams PK
│   │   ├── trigger.go       # Trigger actions
│   │   ├── update_rank.go   # Update rank
│   │   ├── wait.go          # Wait actions
│   │   ├── watch_battle.go  # Watch battle
│   │   ├── world_auction_house.go # World auction house
│   │   └── worldboss.go     # World boss
│   ├── core/                # Core functionality
│   │   ├── auth/            # Authentication
│   │   │   ├── auth.go      # Auth implementation
│   │   │   └── auth_test.go # Auth tests
│   │   ├── game/            # Game core
│   │   │   ├── connect.go   # Connect implementation
│   │   │   ├── connect_chat.go # Connect chat
│   │   │   ├── const.go     # Game constants
│   │   │   ├── send.go      # Send implementation
│   │   │   └── send_test.go # Send tests
│   │   ├── gate/            # Gateway
│   │   │   └── gate.go      # Gate implementation
│   │   └── robot/           # Robot core
│   │       ├── action.go    # Action implementation
│   │       ├── action_resp_gen.go # Action response generator
│   │       ├── action_tree.go # Action tree
│   │       ├── action_tree_test.go # Action tree tests
│   │       ├── chat.go      # Chat implementation
│   │       ├── nats.go      # NATS implementation
│   │       ├── player_change.go # Player change
│   │       ├── pool.go      # Pool implementation
│   │       ├── receive_clientmessage.go # Receive client message
│   │       ├── robot.go     # Robot implementation
│   │       ├── robot_interface.go # Robot interface
│   │       ├── robot_test.go # Robot tests
│   │       └── state.go     # State management
│   ├── kcp/                 # KCP implementation
│   │   ├── kcpmanger.go     # KCP manager
│   │   └── kcprobot.go      # KCP robot
│   ├── regstate.go          # Register state
│   ├── robot_mgr.go         # Robot manager
│   └── util/                # Utilities
│       ├── archive.go       # Archive utilities
│       ├── GenerateIdUtil.go # Generate ID utility
│       ├── net_util.go      # Network utilities
│       ├── passthrough.go   # Passthrough utilities
│       └── random.go        # Random utilities
├── hotchangelevel.sh        # Hot change level script
├── pprof                    # Profiling
├── startstressx.sh          # Start script
└── main.go                  # Main entry point
```

## Chức năng chính

### 1. Robot Simulation
- **Robot Creation**: Tạo robot người chơi
- **Robot Management**: Quản lý robot
- **Robot Actions**: Hành động robot
- **Robot State**: Trạng thái robot

### 2. Stress Testing
- **Load Testing**: Kiểm tra tải
- **Performance Testing**: Kiểm tra hiệu suất
- **Concurrent Testing**: Kiểm tra đồng thời
- **Endurance Testing**: Kiểm tra độ bền

### 3. Game Simulation
- **Login Simulation**: Mô phỏng đăng nhập
- **Gameplay Simulation**: Mô phỏng gameplay
- **Battle Simulation**: Mô phỏng battle
- **Social Simulation**: Mô phỏng xã hội

### 4. Action State Management
- **Action Tree**: Cây hành động
- **State Machine**: Máy trạng thái
- **Action Queue**: Hàng đợi hành động
- **Action Execution**: Thực thi hành động

### 5. Multi-Protocol Support
- **TCP Protocol**: Giao thức TCP
- **KCP Protocol**: Giao thức KCP
- **gRPC Protocol**: Giao thức gRPC
- **WebSocket Protocol**: Giao thức WebSocket

## Giao thức kết nối

### 1. Stressx ↔ Gamexshard
- **Protocol**: gRPC, TCP
- **Purpose**: Game simulation
- **Features**:
  - Robot login
  - Game actions
  - Battle simulation
  - Social interactions

### 2. Stressx ↔ Battlex
- **Protocol**: KCP, TCP
- **Purpose**: Battle simulation
- **Features**:
  - Battle connection
  - Battle actions
  - Battle state management
  - Battle result processing

### 3. Stressx ↔ Other Services
- **Protocol**: gRPC, HTTP
- **Purpose**: Service testing
- **Features**:
  - Service discovery
  - Health checking
  - Performance monitoring
  - Load testing

## Cấu hình chính

### Development Config
```toml
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

gid = 12
serverid = "stress1"
internalip = "127.0.0.1"
```

### Production Config
```toml
# Similar structure with production values
gid = 32
serverid = "stress1"
internalip = "production_ip"
```

## Robot System

### 1. Robot Creation
```go
func createRobot() *Robot {
    robot := &Robot{
        ID:       generateID(),
        State:    RobotStateIdle,
        Actions:  make([]Action, 0),
        Conn:     nil,
        Manager:  robotManager,
    }
    return robot
}
```

### 2. Action State Machine
```go
type ActionState struct {
    CurrentState string
    Actions      []Action
    StateData    map[string]interface{}
    Transitions  map[string]string
}
```

### 3. Robot Actions
- **Login Actions**: Hành động đăng nhập
- **Game Actions**: Hành động game
- **Battle Actions**: Hành động battle
- **Social Actions**: Hành động xã hội
- **Guild Actions**: Hành động guild
- **Chat Actions**: Hành động chat

## Stress Testing Features

### 1. Load Testing
- **Concurrent Users**: Người dùng đồng thời
- **Request Rate**: Tốc độ request
- **Response Time**: Thời gian phản hồi
- **Throughput**: Thông lượng

### 2. Performance Testing
- **CPU Usage**: Sử dụng CPU
- **Memory Usage**: Sử dụng memory
- **Network Usage**: Sử dụng mạng
- **Database Performance**: Hiệu suất database

### 3. Endurance Testing
- **Long Running**: Chạy dài hạn
- **Memory Leak Detection**: Phát hiện rò rỉ memory
- **Resource Monitoring**: Giám sát tài nguyên
- **Stability Testing**: Kiểm tra độ ổn định

## Action State Management

### 1. Action Types
- **Login Actions**: Hành động đăng nhập
- **Gameplay Actions**: Hành động gameplay
- **Battle Actions**: Hành động battle
- **Social Actions**: Hành động xã hội
- **Guild Actions**: Hành động guild
- **Chat Actions**: Hành động chat

### 2. State Transitions
- **Idle State**: Trạng thái nghỉ
- **Active State**: Trạng thái hoạt động
- **Battle State**: Trạng thái battle
- **Social State**: Trạng thái xã hội
- **Error State**: Trạng thái lỗi

### 3. Action Execution
- **Sequential Execution**: Thực thi tuần tự
- **Parallel Execution**: Thực thi song song
- **Conditional Execution**: Thực thi có điều kiện
- **Error Handling**: Xử lý lỗi

## Performance & Optimization

### 1. Robot Optimization
- **Robot Pooling**: Pool robot
- **Action Batching**: Gộp hành động
- **Memory Management**: Quản lý memory
- **Connection Pooling**: Pool kết nối

### 2. Network Optimization
- **Connection Reuse**: Tái sử dụng kết nối
- **Message Batching**: Gộp message
- **Compression**: Nén dữ liệu
- **Protocol Optimization**: Tối ưu giao thức

### 3. Resource Management
- **CPU Management**: Quản lý CPU
- **Memory Management**: Quản lý memory
- **Network Management**: Quản lý mạng
- **Database Management**: Quản lý database

## Monitoring & Logging

### Metrics
- **Robot Count**: Số lượng robot
- **Action Count**: Số lượng hành động
- **Success Rate**: Tỷ lệ thành công
- **Error Rate**: Tỷ lệ lỗi
- **Response Time**: Thời gian phản hồi
- **Throughput**: Thông lượng

### Logging
- **Robot Log**: Log robot
- **Action Log**: Log hành động
- **Error Log**: Log lỗi
- **Performance Log**: Log hiệu suất
- **Stress Log**: Log stress test

## Error Handling

### Robot Errors
- **Connection Error**: Lỗi kết nối
- **Authentication Error**: Lỗi xác thực
- **Action Error**: Lỗi hành động
- **State Error**: Lỗi trạng thái

### System Errors
- **Resource Error**: Lỗi tài nguyên
- **Network Error**: Lỗi mạng
- **Database Error**: Lỗi database
- **Configuration Error**: Lỗi cấu hình

## Security Features

### 1. Robot Security
- **Authentication**: Xác thực robot
- **Authorization**: Phân quyền robot
- **Data Validation**: Xác thực dữ liệu
- **Action Validation**: Xác thực hành động

### 2. System Security
- **Access Control**: Kiểm soát truy cập
- **Data Protection**: Bảo vệ dữ liệu
- **Audit Logging**: Log audit
- **Security Monitoring**: Giám sát bảo mật

## Deployment

### Build
```bash
cd stressx
go build -o stressx
```

### Run
```bash
# Development
./stressx stress -c config.toml

# Production
./stressx stress -c config_g32.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o stressx

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/stressx .
CMD ["./stressx", "stress"]
```

## Troubleshooting

### Common Issues
1. **High Memory Usage**: Kiểm tra robot count
2. **Connection Timeout**: Kiểm tra network settings
3. **Action Failure**: Kiểm tra action logic
4. **Performance Issues**: Kiểm tra resource usage

### Debug Tools
- **Robot Monitor**: Giám sát robot
- **Action Inspector**: Kiểm tra hành động
- **Performance Profiler**: Profile hiệu suất
- **Log Analysis**: Phân tích log

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Robot Common**: Robot utilities
- **Proto**: Protocol definitions

### External Dependencies
- **etcd**: Service discovery
- **NATS**: Message queue
- **gRPC**: Internal communication
- **PlanX**: Platform framework

## Development

### Code Structure
- **Robot System**: Hệ thống robot
- **Action System**: Hệ thống hành động
- **State Management**: Quản lý trạng thái
- **Network Layer**: Lớp mạng

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Stress Tests**: Stress testing
- **Performance Tests**: Performance testing

## Contact

- **Author**: LiuTing
- **Email**: liuting@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service stressx - server kiểm tra tải và stress testing của hệ thống JWS2.*
