# Scene Service - Scene Server

## Tổng quan

**Scene** là service xử lý logic scene và di chuyển của hệ thống JWS2, chịu trách nhiệm quản lý các scene trong game, xử lý di chuyển của người chơi, quản lý NPC, và các hoạt động trong scene.

## Thông tin cơ bản

- **Tên service**: scene
- **Port chính**: 10003 (gRPC)
- **Shard ID**: 120001, 120002 (có thể có nhiều shard)
- **Protocol**: gRPC, HTTP
- **Ngôn ngữ**: Go (Golang)
- **Kiến trúc**: Scene-based server với screen system

## Cấu trúc thư mục

```
scene/
├── cmds/                     # Command line interface
│   ├── cmds.go              # Main commands
│   ├── common.go            # Common utilities
│   └── scene/               # Scene commands
│       └── scene.go         # Scene command implementation
├── conf/                    # Configuration files
│   ├── scene.toml          # Main config
│   ├── config_g32.toml     # Production config
│   ├── scene_120001.toml   # Shard 120001 config
│   ├── scene_120002.toml   # Shard 120002 config
│   └── metrics.toml        # Metrics config
├── config/                  # Config management
│   └── config.go           # Config utilities
├── logic/                   # Scene logic
│   ├── gen_reg_func.go     # Generated register functions
│   ├── gen_scene.go        # Generated scene code
│   ├── init.go             # Initialization
│   └── handlers/           # Message handlers
│       ├── C2SRoleMove.go  # Player movement
│       ├── C2SRoleStop.go  # Player stop
│       ├── C2SRoleCheck.go # Player check
│       ├── G2SEnterScene.go # Enter scene
│       ├── G2SExitScene.go # Exit scene
│       ├── G2SGotoScene.go # Goto scene
│       ├── G2SCollect.go   # Collection
│       ├── G2SRobCart.go   # Rob cart
│       └── [30+ more handlers]
├── scenes/                  # Scene implementation
│   ├── scene.go            # Main scene logic
│   ├── scene_role.go       # Role management
│   ├── scene_npc.go        # NPC management
│   ├── scene_object.go     # Object management
│   ├── scene_pet.go        # Pet management
│   ├── scene_cart.go       # Cart management
│   ├── scene_collection.go # Collection system
│   ├── scene_quiz.go       # Quiz system
│   ├── guild_party.go      # Guild party
│   ├── rebel_strike.go     # Rebel strike
│   └── pool.go             # Object pooling
├── plugin/                  # Plugin system
│   └── plugin.go           # Plugin interface
├── plugin_agent/           # Plugin agent
│   └── agent_tamplate/     # Agent template
└── main.go                 # Main entry point
```

## Chức năng chính

### 1. Scene Management
- **Scene Creation**: Tạo scene mới
- **Scene Destruction**: Hủy scene khi không cần
- **Scene Navigation**: Điều hướng giữa các scene
- **Scene State**: Quản lý trạng thái scene

### 2. Player Movement
- **Role Movement**: Xử lý di chuyển người chơi
- **Position Tracking**: Theo dõi vị trí
- **Movement Validation**: Xác thực di chuyển
- **Screen System**: Hệ thống 9-screen navigation

### 3. NPC Management
- **NPC Spawning**: Tạo NPC
- **NPC AI**: Logic AI cho NPC
- **NPC Interaction**: Tương tác với NPC
- **NPC State**: Quản lý trạng thái NPC

### 4. Object Management
- **Collection Points**: Điểm thu thập
- **Carts**: Xe hàng
- **Pets**: Thú cưng
- **Guild Party Boxes**: Hộp guild party

### 5. Special Systems
- **Scene Quiz**: Hệ thống câu hỏi trong scene
- **Guild Party**: Party guild
- **Rebel Strike**: Tấn công nổi dậy
- **Escort Tasks**: Nhiệm vụ hộ tống

## Giao thức kết nối

### 1. Gamexshard ↔ Scene
- **Protocol**: gRPC
- **Purpose**: Scene management
- **Commands**:
  - Enter scene
  - Exit scene
  - Goto scene
  - Role change info
  - Get position
  - Set position

### 2. Client ↔ Scene (thông qua Gamexshard)
- **Protocol**: gRPC (indirect)
- **Purpose**: Player actions
- **Commands**:
  - Role move
  - Role stop
  - Role check
  - Collection
  - Cart operations

### 3. Cross Server ↔ Scene
- **Protocol**: gRPC
- **Purpose**: Cross-server scene operations
- **Commands**:
  - Cross scene operations
  - Cross server events

## Cấu hình chính

### Development Config
```toml
[SceneConfig]
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

shardid = 120001  # Must match gamex
gid = 12
internalip = "127.0.0.1"

# Rate limiting
limit_valid = true
ratelimit_url_info = "Scene/MessageC2SRoleMove,1,30;Scene/MessageC2SRoleStop,1,10;Scene/MessageC2SRoleCheck,1,3;Scene/MessageG2SEnterScene,1,1;Scene/MessageG2SExitScene,1,1;Scene/MessageG2SGotoScene,1,1;Scene/MessageG2SRoleChangeInfo,1,5;Scene/MessageG2SGetPosition,1,2"
```

### Production Config
```toml
# Similar structure with production values
shardid = 120001
gid = 32
internalip = "production_ip"
```

## Screen System

### Screen Grid
- **Screen Size**: 30x30 units
- **Navigation**: 9-screen system
- **Optimization**: Only load visible screens
- **Memory Management**: Lazy loading/unloading

### Screen Constants
```go
const (
    SCREEN_WIDTH  = 30
    SCREEN_HIGHTH = 30
)
```

## Scene Types

### 1. Normal Scenes
- **Main City**: Thành phố chính
- **Dungeon**: Hầm ngục
- **Wild**: Hoang dã
- **Guild**: Guild scenes

### 2. Special Scenes
- **Quiz Scenes**: Scene câu hỏi (602001, 602011)
- **Cross Server**: Scene liên server
- **Guild Party**: Scene party guild
- **Rebel Strike**: Scene tấn công nổi dậy

### 3. Scene Validation
```go
var (
    MoveValidatorMap map[int32]struct{} // Scenes requiring movement validation
    SceneQuizMap     map[int32]struct{} // Quiz scenes
)
```

## Message Handlers

### Client to Scene (C2S)
- **C2SRoleMove**: Di chuyển người chơi
- **C2SRoleStop**: Dừng di chuyển
- **C2SRoleCheck**: Kiểm tra người chơi
- **C2SGetCartPos**: Lấy vị trí xe
- **C2SStartPickUpBox**: Bắt đầu nhặt hộp
- **C2STeamFollowNpc**: Team theo NPC

### Game to Scene (G2S)
- **G2SEnterScene**: Vào scene
- **G2SExitScene**: Ra khỏi scene
- **G2SGotoScene**: Chuyển scene
- **G2SRoleChangeInfo**: Thay đổi thông tin role
- **G2SGetPosition**: Lấy vị trí
- **G2SSetPosition**: Đặt vị trí
- **G2SCollect**: Thu thập
- **G2SRobCart**: Cướp xe
- **G2SNpcEnterScene**: NPC vào scene
- **G2SNpcLeaveScene**: NPC rời scene

### Cross to Scene (CR2S)
- **CR2SSceneQuizGetRoldCircleInfo**: Thông tin circle quiz
- **CR2SSceneQuizQuizEnd**: Kết thúc quiz

## Scene Objects

### 1. Role (Player)
- **Position**: Vị trí trong scene
- **State**: Trạng thái (moving, idle, etc.)
- **Screen**: Screen hiện tại
- **Keep Alive**: Cơ chế keep alive

### 2. NPC
- **Spawn**: Tạo NPC
- **AI**: Logic AI
- **Interaction**: Tương tác
- **State**: Trạng thái NPC

### 3. Collection Points
- **Spawn**: Tạo điểm thu thập
- **Usage**: Sử dụng điểm
- **Cooldown**: Thời gian chờ
- **Rewards**: Phần thưởng

### 4. Carts
- **Position**: Vị trí xe
- **Owner**: Chủ sở hữu
- **State**: Trạng thái xe
- **Robbery**: Cướp xe

## Performance & Optimization

### Object Pooling
- **Role Pool**: Pool cho role objects
- **NPC Pool**: Pool cho NPC objects
- **Screen Pool**: Pool cho screen objects
- **Memory Efficiency**: Tái sử dụng objects

### Screen Management
- **Lazy Loading**: Chỉ load screen cần thiết
- **Unloading**: Unload screen không dùng
- **Memory Cleanup**: Dọn dẹp memory

### Rate Limiting
- **Movement**: 30 moves per second
- **Stop**: 10 stops per second
- **Check**: 3 checks per second
- **Scene Operations**: 1 operation per second

## Special Features

### 1. Scene Quiz System
- **Quiz Scenes**: 602001, 602011
- **Circle Occupation**: Chiếm vòng tròn
- **Time Management**: Quản lý thời gian
- **Rewards**: Phần thưởng

### 2. Guild Party System
- **Party Boxes**: Hộp party
- **Generation**: Tạo hộp
- **Destruction**: Hủy hộp
- **Rewards**: Phần thưởng

### 3. Rebel Strike System
- **Global Management**: Quản lý toàn cục
- **Battle Results**: Kết quả battle
- **Cheat Commands**: Lệnh cheat
- **Robbery**: Cướp bóc

### 4. Escort System
- **Task Management**: Quản lý nhiệm vụ
- **Map Loading**: Load bản đồ
- **Position Tracking**: Theo dõi vị trí
- **Removal**: Xóa nhiệm vụ

## Monitoring & Logging

### Metrics
- **Scene Count**: Số lượng scene
- **Player Count**: Số lượng người chơi
- **NPC Count**: Số lượng NPC
- **Movement Rate**: Tốc độ di chuyển
- **Error Rate**: Tỷ lệ lỗi

### Logging
- **Scene Log**: Log chi tiết scene
- **Movement Log**: Log di chuyển
- **Error Log**: Log lỗi hệ thống
- **Performance Log**: Log hiệu suất

## Error Handling

### Movement Errors
- **Invalid Position**: Vị trí không hợp lệ
- **Speed Check**: Kiểm tra tốc độ
- **Collision**: Va chạm
- **Boundary**: Biên giới

### Scene Errors
- **Scene Not Found**: Không tìm thấy scene
- **Permission Denied**: Không có quyền
- **Capacity Full**: Scene đầy
- **Invalid State**: Trạng thái không hợp lệ

## Security Features

### Movement Validation
- **Speed Validation**: Kiểm tra tốc độ
- **Position Validation**: Kiểm tra vị trí
- **Collision Detection**: Phát hiện va chạm
- **Boundary Check**: Kiểm tra biên giới

### Rate Limiting
- **Per Player**: Giới hạn per player
- **Per Scene**: Giới hạn per scene
- **Per Action**: Giới hạn per action
- **Dynamic Adjustment**: Điều chỉnh động

## Deployment

### Build
```bash
cd scene
go build -o scene
```

### Run
```bash
# Development
./scene scene -c scene.toml

# Production
./scene scene -c config_g32.toml

# Specific shard
./scene scene -c scene_120001.toml
```

### Multiple Shards
```bash
# Shard 120001
./scene scene -c scene_120001.toml

# Shard 120002
./scene scene -c scene_120002.toml
```

## Troubleshooting

### Common Issues
1. **High Memory Usage**: Kiểm tra screen management
2. **Movement Lag**: Kiểm tra rate limiting
3. **Scene Not Loading**: Kiểm tra configuration
4. **NPC Not Spawning**: Kiểm tra NPC logic

### Debug Tools
- **Scene Inspector**: Kiểm tra scene
- **Movement Tracker**: Theo dõi di chuyển
- **Performance Profiler**: Profile hiệu suất
- **Log Analysis**: Phân tích log

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Scene Common**: Scene utilities
- **Proto**: Protocol definitions

### External Dependencies
- **etcd**: Service discovery
- **gRPC**: Internal communication
- **PlanX**: Platform framework

## Development

### Code Structure
- **Scene Logic**: Core scene logic
- **Message Handlers**: Message processing
- **Object Management**: Object handling
- **Screen System**: Screen management

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

*Tài liệu này mô tả chi tiết service scene - server xử lý scene và di chuyển của hệ thống JWS2.*
