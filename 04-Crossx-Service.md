# Crossx Service - Cross Server

## Tổng quan

**Crossx** là service xử lý giao tiếp liên server của hệ thống JWS2, chịu trách nhiệm quản lý các hoạt động cross-server, battle check, country management, và các tính năng liên server khác.

## Thông tin cơ bản

- **Tên service**: crossx
- **Port chính**: 10004 (gRPC)
- **Server ID**: 0 (Leader), 1,2... (Members)
- **Protocol**: gRPC, HTTP
- **Ngôn ngữ**: Go (Golang)
- **Kiến trúc**: Leader-Member cluster với Redis

## Cấu trúc thư mục

```
crossx/
├── cmds/                     # Command line interface
├── conf/                     # Configuration files
│   ├── config_leader.toml   # Leader config
│   ├── config_member.toml   # Member config
│   └── config_g32.toml      # Production config
├── config/                   # Config management
├── core/                     # Core utilities
│   ├── cross_clouddb/       # Cross cloud database
│   └── dbpool/              # Database pool
├── crossapi/                 # Cross API
├── leader/                   # Leader server
│   ├── leader.go            # Leader main logic
│   ├── logic/               # Leader logic
│   │   ├── battlecheck_distributor/ # Battle check distributor
│   │   ├── country/         # Country management
│   │   ├── crossswitch/     # Cross switch
│   │   ├── debug/           # Debug tools
│   │   ├── servergroup/     # Server group management
│   │   └── xscene/          # Cross scene
│   ├── modules/             # Leader modules
│   │   ├── activity_group_control/ # Activity control
│   │   ├── battlecheck_distributor/ # Battle check
│   │   ├── check/           # Check system
│   │   ├── country/         # Country system
│   │   ├── csg/             # CSG system
│   │   ├── servergroup/     # Server group
│   │   └── xscene/          # Cross scene
│   └── switch.go            # Switch logic
├── member/                   # Member server
│   ├── member.go            # Member main logic
│   ├── api/                 # Member API
│   │   ├── activity.go      # Activity API
│   │   ├── chat_sender/     # Chat sender
│   │   ├── cross_leader.go  # Cross leader API
│   │   ├── cross_spec_server.go # Special server API
│   │   ├── gamex_cache/     # Gamex cache
│   │   ├── gamex.go         # Gamex API
│   │   ├── player_sender/   # Player sender
│   │   ├── servergroup.go   # Server group API
│   │   └── switch.go        # Switch API
│   ├── core/                # Member core
│   │   └── battle/          # Battle core
│   ├── logic/               # Member logic
│   │   ├── crossswitch/     # Cross switch
│   │   ├── servergroup/     # Server group
│   │   └── stress/          # Stress testing
│   └── modules/             # Member modules (100+ modules)
│       ├── activity_cross_anniversary_prepare/ # Anniversary
│       ├── activity_cross_super_gold/ # Super gold
│       ├── activity_lucky_draw/ # Lucky draw
│       ├── auction/         # Auction house
│       ├── battleroom/      # Battle room
│       ├── boss_challenge/  # Boss challenge
│       ├── command_the_world/ # Command world
│       ├── country/         # Country system
│       ├── country_night_boss/ # Night boss
│       ├── country_pvp/     # Country PvP
│       ├── cross_common_rank/ # Common rank
│       ├── custom_room/     # Custom room
│       ├── dragon_artery/   # Dragon artery
│       ├── ecosphere/       # Ecosphere
│       ├── escort/          # Escort
│       ├── fair_solo_rank/  # Fair solo rank
│       ├── fair_tournament/ # Fair tournament
│       ├── fair_tvt_rank/   # Fair TVT rank
│       ├── fight_bp/        # Fight BP
│       ├── fightthrone/     # Fight throne
│       ├── guild_boss_rank/ # Guild boss rank
│       ├── guild_war/       # Guild war
│       ├── gvg_fight/       # GvG fight
│       ├── hero_match/      # Hero match
│       ├── match_pve/       # Match PvE
│       ├── match1v1/        # 1v1 match
│       ├── match3v3/        # 3v3 match
│       ├── mine/            # Mine
│       ├── pet/             # Pet
│       ├── rank/            # Ranking
│       ├── scene_quiz/      # Scene quiz
│       ├── siege_city/      # Siege city
│       ├── slg_gve_rank/    # SLG GvE rank
│       ├── subject_month_activity/ # Monthly activity
│       ├── sync_four_country/ # Four country sync
│       ├── sync_four_country_rank/ # Four country rank
│       ├── team/            # Team
│       ├── team_fair_allserver_rank/ # All server rank
│       ├── team_fair_championship/ # Championship
│       ├── team_fair_warzone_mgr/ # Warzone manager
│       ├── team_war/        # Team war
│       ├── three_teams_pk/  # Three teams PK
│       ├── thumbs_up_panel/ # Thumbs up panel
│       ├── trigger_war_zone/ # Trigger war zone
│       ├── util/            # Utilities
│       ├── voice_chat/      # Voice chat
│       ├── wanted_task/     # Wanted task
│       ├── war_zone_auction/ # War zone auction
│       ├── warzone/         # Warzone
│       └── worldboss/       # World boss
├── plugin/                   # Plugin system
├── plugin_agent/            # Plugin agent
├── test/                    # Test files
└── main.go                  # Main entry point
```

## Chức năng chính

### 1. Cross Server Communication
- **Server Group Management**: Quản lý nhóm server
- **Cross Server Events**: Sự kiện liên server
- **Data Synchronization**: Đồng bộ dữ liệu
- **Message Routing**: Định tuyến message

### 2. Battle Check System
- **Battle Validation**: Xác thực battle
- **Battle Distribution**: Phân phối battle
- **Battle Results**: Kết quả battle
- **Battle Replay**: Replay battle

### 3. Country System
- **Country Management**: Quản lý quốc gia
- **Country PvP**: PvP giữa các quốc gia
- **Country Night Boss**: Boss đêm quốc gia
- **Country Wars**: Chiến tranh quốc gia

### 4. Cross Scene System
- **Cross Scene Management**: Quản lý scene liên server
- **Scene Matching**: Ghép scene
- **Scene Synchronization**: Đồng bộ scene
- **Scene Events**: Sự kiện scene

### 5. Activity System
- **Cross Activities**: Hoạt động liên server
- **Activity Control**: Điều khiển hoạt động
- **Activity Rewards**: Phần thưởng hoạt động
- **Activity Ranking**: Xếp hạng hoạt động

## Giao thức kết nối

### 1. Leader ↔ Members
- **Protocol**: gRPC
- **Purpose**: Leader-Member communication
- **Commands**:
  - Server group management
  - Activity control
  - Battle check distribution
  - Country management

### 2. Crossx ↔ Gamexshard
- **Protocol**: gRPC
- **Purpose**: Cross server operations
- **Commands**:
  - Cross server events
  - Battle check
  - Country operations
  - Activity operations

### 3. Crossx ↔ Battlex
- **Protocol**: gRPC
- **Purpose**: Battle operations
- **Commands**:
  - Battle room management
  - Battle validation
  - Battle results

### 4. Crossx ↔ Scene
- **Protocol**: gRPC
- **Purpose**: Scene operations
- **Commands**:
  - Cross scene management
  - Scene matching
  - Scene synchronization

## Cấu hình chính

### Leader Config
```toml
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

gid = 12
serverid = 0  # 0 for leader
cross_member_num = 2  # Number of member servers
internalip = "127.0.0.1"

# Cross database
CrossDbAddr = "redis@127.0.0.1:6379"
CrossDbPwd = ""
CrossDbNum = 1
```

### Member Config
```toml
# Similar structure with member-specific values
serverid = 1  # Member server ID
# Other configurations...
```

## Module System

### Leader Modules
1. **Activity Group Control**: Điều khiển nhóm hoạt động
2. **Battle Check Distributor**: Phân phối kiểm tra battle
3. **Check System**: Hệ thống kiểm tra
4. **Country System**: Hệ thống quốc gia
5. **CSG System**: Hệ thống CSG
6. **Server Group**: Quản lý nhóm server
7. **XScene**: Scene liên server

### Member Modules (100+ modules)
1. **Activity Modules**: Các module hoạt động
2. **Battle Modules**: Các module battle
3. **Country Modules**: Các module quốc gia
4. **Match Modules**: Các module ghép đôi
5. **Rank Modules**: Các module xếp hạng
6. **Team Modules**: Các module team
7. **War Modules**: Các module chiến tranh

## Cross Server Features

### 1. Server Group Management
- **Group Creation**: Tạo nhóm server
- **Group Management**: Quản lý nhóm
- **Group Conflict**: Xử lý xung đột
- **Group Warzone**: Vùng chiến tranh

### 2. Battle Check System
- **Battle Validation**: Xác thực battle
- **Battle Distribution**: Phân phối battle
- **Battle Results**: Kết quả battle
- **Battle Replay**: Replay battle

### 3. Country System
- **Country Management**: Quản lý quốc gia
- **Country PvP**: PvP giữa các quốc gia
- **Country Night Boss**: Boss đêm quốc gia
- **Country Wars**: Chiến tranh quốc gia

### 4. Cross Scene System
- **Scene Matching**: Ghép scene
- **Scene Synchronization**: Đồng bộ scene
- **Scene Events**: Sự kiện scene
- **Scene Management**: Quản lý scene

### 5. Activity System
- **Cross Activities**: Hoạt động liên server
- **Activity Control**: Điều khiển hoạt động
- **Activity Rewards**: Phần thưởng
- **Activity Ranking**: Xếp hạng

## Database & Storage

### Redis Database
- **Cross Database**: Redis cho cross server data
- **Configuration**: `redis@127.0.0.1:6379`
- **Database Number**: 1
- **Password**: Empty (development)

### Data Types
- **Server Group Data**: Dữ liệu nhóm server
- **Country Data**: Dữ liệu quốc gia
- **Activity Data**: Dữ liệu hoạt động
- **Battle Data**: Dữ liệu battle
- **Rank Data**: Dữ liệu xếp hạng

## Performance & Optimization

### Leader-Member Architecture
- **Leader**: Quản lý toàn cục
- **Members**: Xử lý cụ thể
- **Load Balancing**: Cân bằng tải
- **Failover**: Chuyển đổi dự phòng

### Module System
- **Modular Design**: Thiết kế module
- **Lazy Loading**: Tải lazy
- **Memory Management**: Quản lý memory
- **Performance Monitoring**: Giám sát hiệu suất

### Caching
- **Redis Caching**: Cache Redis
- **Memory Caching**: Cache memory
- **Cache Invalidation**: Vô hiệu hóa cache
- **Cache Warming**: Làm nóng cache

## Monitoring & Logging

### Metrics
- **Server Count**: Số lượng server
- **Module Count**: Số lượng module
- **Activity Count**: Số lượng hoạt động
- **Battle Count**: Số lượng battle
- **Error Rate**: Tỷ lệ lỗi

### Logging
- **Cross Log**: Log cross server
- **Module Log**: Log module
- **Error Log**: Log lỗi
- **Performance Log**: Log hiệu suất

## Error Handling

### Connection Errors
- **Leader Disconnect**: Mất kết nối leader
- **Member Disconnect**: Mất kết nối member
- **Database Error**: Lỗi database
- **Network Error**: Lỗi mạng

### Module Errors
- **Module Load Error**: Lỗi load module
- **Module Runtime Error**: Lỗi runtime module
- **Module Conflict**: Xung đột module
- **Module Timeout**: Timeout module

## Security Features

### Data Validation
- **Input Validation**: Xác thực input
- **Data Integrity**: Toàn vẹn dữ liệu
- **Permission Check**: Kiểm tra quyền
- **Access Control**: Kiểm soát truy cập

### Cross Server Security
- **Server Authentication**: Xác thực server
- **Message Encryption**: Mã hóa message
- **Data Encryption**: Mã hóa dữ liệu
- **Audit Logging**: Log audit

## Deployment

### Build
```bash
cd crossx
go build -o crossx
```

### Run Leader
```bash
# Development
./crossx cross -c config_leader.toml

# Production
./crossx cross -c config_g32.toml
```

### Run Member
```bash
# Member 1
./crossx cross -c config_member1.toml

# Member 2
./crossx cross -c config_member2.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o crossx

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/crossx .
CMD ["./crossx", "cross"]
```

## Troubleshooting

### Common Issues
1. **Leader Election**: Vấn đề bầu leader
2. **Member Disconnect**: Mất kết nối member
3. **Module Load Error**: Lỗi load module
4. **Database Connection**: Lỗi kết nối database

### Debug Tools
- **Cross Inspector**: Kiểm tra cross server
- **Module Monitor**: Giám sát module
- **Performance Profiler**: Profile hiệu suất
- **Log Analysis**: Phân tích log

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Cross Common**: Cross utilities
- **Proto**: Protocol definitions

### External Dependencies
- **etcd**: Service discovery
- **Redis**: Cross database
- **gRPC**: Internal communication
- **PlanX**: Platform framework

## Development

### Code Structure
- **Leader Logic**: Logic leader
- **Member Logic**: Logic member
- **Module System**: Hệ thống module
- **API Layer**: Lớp API

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Cross Server Tests**: Cross server testing
- **Load Tests**: Load testing

## Contact

- **Author**: yangzhenyu
- **Email**: yangzhenyu@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service crossx - server xử lý giao tiếp liên server của hệ thống JWS2.*
