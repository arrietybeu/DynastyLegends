# Gmserver Service - Game Management Server

## Tổng quan

**Gmserver** là service quản lý game và công cụ GM của hệ thống JWS2, chịu trách nhiệm cung cấp các công cụ quản trị game, quản lý người chơi, xử lý thanh toán, và các tính năng quản lý khác.

## Thông tin cơ bản

- **Tên service**: gmserver
- **Port chính**: 8808 (HTTP)
- **Server ID**: gmserver1
- **Protocol**: HTTP/HTTPS, gRPC
- **Ngôn ngữ**: Go (Golang)
- **Database**: MySQL, MongoDB, Redis, Pika
- **Kiến trúc**: Web-based GM tools với API

## Cấu trúc thư mục

```
gmserver/
├── cmds/                     # Command line interface
│   ├── allinone/            # All-in-one command
│   │   └── allinone.go      # All-in-one implementation
│   ├── cmds.go              # Commands
│   ├── gmtool_discovery.go  # GM tool discovery
│   ├── merger_discovery.go  # Merger discovery
│   ├── payx_discovery.go    # Payx discovery
│   ├── reg_gmserver.go      # GM server registration
│   ├── reg_key_cache.go     # Key cache registration
│   ├── reg_merge.go         # Merge registration
│   └── stressdiscovery.go   # Stress discovery
├── common/                  # Common utilities
│   ├── rules/               # Data rules
│   │   ├── data_common_convert_rule.go # Data conversion rules
│   │   ├── data_desc.go     # Data description
│   │   ├── data_format.go   # Data format
│   │   ├── display_conf.go  # Display configuration
│   │   ├── func_enum.go     # Function enumeration
│   │   ├── game_function_enum.go # Game function enum
│   │   ├── item.go          # Item rules
│   │   └── other.go         # Other rules
│   └── shards/              # Shard management
│       ├── etcd_key.go      # ETCD key management
│       ├── merge.go         # Merge logic
│       └── shards_info.go   # Shard information
├── conf/                    # Configuration files
│   ├── config.toml          # Main config
│   ├── config_g32.toml      # Production config
│   └── metrics.toml         # Metrics config
├── config/                  # Config management
│   └── config.go            # Config utilities
├── db/                      # Database layer
│   ├── mongo/               # MongoDB implementation
│   ├── mysql/               # MySQL implementation
│   ├── pika/                # Pika implementation
│   │   └── profile.go       # Profile management
│   └── redis/               # Redis implementation
├── desc/                    # Descriptions
│   ├── error.go             # Error descriptions
│   └── info.go              # Info descriptions
├── gameapi/                 # Game API
│   ├── api.go               # Main API
│   ├── RedisConnApi.go      # Redis connection API
│   └── db/                  # Database API
│       └── [20+ database API files]
├── logic/                   # Business logic
│   ├── ban/                 # Ban management
│   ├── battle_info_switch/  # Battle info switch
│   ├── cdKey/               # CD key management
│   ├── channel/             # Channel management
│   ├── chat_reward/         # Chat reward
│   ├── command.go           # Command handling
│   ├── common/              # Common logic
│   ├── complex_type_data/   # Complex type data
│   ├── country/             # Country management
│   ├── data_number_audit/   # Data number audit
│   ├── debug_clean/         # Debug clean
│   ├── del_account/         # Delete account
│   ├── draw_card_probability/ # Draw card probability
│   ├── engineer_switch/     # Engineer switch
│   ├── game_switch/         # Game switch
│   ├── game_version/        # Game version
│   ├── gid_switch/          # GID switch
│   ├── globalexceptionhandler.go # Global exception handler
│   ├── gm_common_command.go # GM common commands
│   ├── go_watch/            # Go watch
│   ├── guild/               # Guild management
│   ├── handler_raw.go       # Raw handler
│   ├── handler_v2.go        # Handler v2
│   ├── handler.go           # Main handler
│   ├── hero_match_1v1/      # Hero match 1v1
│   ├── hero_match_3v3/      # Hero match 3v3
│   ├── hotdata/             # Hot data
│   ├── hotpatch/            # Hot patch
│   ├── mail/                # Mail system
│   ├── market_switch/       # Market switch
│   ├── merge/               # Merge system
│   ├── paging/              # Paging
│   ├── pay_opt/             # Payment operations
│   ├── player/              # Player management
│   ├── profile/             # Profile management
│   ├── public_notice/       # Public notice
│   ├── questionnaire/       # Questionnaire
│   ├── rank/                # Ranking
│   ├── reg_nats_handler.go  # NATS handler registration
│   ├── resultcode.go        # Result codes
│   ├── robot/               # Robot management
│   ├── rollnotice/          # Roll notice
│   ├── sentiment/           # Sentiment analysis
│   ├── servergroup/         # Server group
│   ├── shard/               # Shard management
│   ├── silence_sys/         # Silence system
│   ├── thinkingdata_push/   # Thinking data push
│   ├── tools/               # Tools
│   ├── version_update/      # Version update
│   ├── war_zone_util/       # War zone utilities
│   ├── warzone/             # Warzone
│   ├── web_activity/        # Web activity
│   ├── white_list/          # White list
│   └── xscene/              # Cross scene
├── model/                   # Data models
│   ├── auth/                # Authentication models
│   ├── channel/             # Channel models
│   ├── clientHotFix/        # Client hot fix models
│   ├── command/             # Command models
│   ├── errs/                # Error models
│   ├── for_gms/             # GM models
│   ├── func_config/         # Function config models
│   ├── gm_sercer_test/      # GM server test
│   ├── mail/                # Mail models
│   ├── market_switch/       # Market switch models
│   ├── oss/                 # OSS models
│   ├── profile/             # Profile models
│   ├── rank/                # Rank models
│   ├── shard_list/          # Shard list models
│   ├── shard_state/         # Shard state models
│   ├── silence_sys/         # Silence system models
│   ├── sync_info/           # Sync info models
│   ├── tools/               # Tools models
│   └── version_update/      # Version update models
├── sql/                     # SQL scripts
│   ├── sql_alter_MP0010.sql # SQL alter MP0010
│   ├── sql_alter_MP0050_PC.sql # SQL alter MP0050 PC
│   └── sql_alter_MP0190_Mac.sql # SQL alter MP0190 Mac
├── util/                    # Utilities
│   ├── archive.go           # Archive utilities
│   ├── del_db_run_away/     # Delete DB run away
│   ├── entity.go            # Entity utilities
│   ├── error.go             # Error utilities
│   ├── etcd.go              # ETCD utilities
│   ├── merge_test.go        # Merge test
│   ├── merge.go             # Merge utilities
│   ├── RedisConn.go         # Redis connection
│   ├── response_vo.go       # Response VO
│   ├── server_state_k8s.go  # Server state K8s
│   ├── server_state.go      # Server state
│   ├── supervisord.go       # Supervisord
│   ├── util_test.go         # Util test
│   └── util.go              # Main utilities
└── main.go                  # Main entry point
```

## Chức năng chính

### 1. Player Management
- **Player Information**: Thông tin người chơi
- **Player Ban**: Cấm người chơi
- **Player Data**: Dữ liệu người chơi
- **Player Statistics**: Thống kê người chơi
- **Account Deletion**: Xóa tài khoản

### 2. Guild Management
- **Guild Information**: Thông tin guild
- **Guild Members**: Thành viên guild
- **Guild Operations**: Thao tác guild
- **Guild Statistics**: Thống kê guild

### 3. Payment Management
- **Payment Operations**: Thao tác thanh toán
- **Payment Statistics**: Thống kê thanh toán
- **Payment Refund**: Hoàn tiền
- **Payment Audit**: Audit thanh toán

### 4. Game Management
- **Game Switch**: Bật/tắt tính năng game
- **Game Version**: Quản lý phiên bản
- **Hot Data**: Dữ liệu nóng
- **Hot Patch**: Bản vá nóng

### 5. Server Management
- **Server Group**: Quản lý nhóm server
- **Shard Management**: Quản lý shard
- **Server State**: Trạng thái server
- **Server Merge**: Hợp nhất server

### 6. Communication Management
- **Mail System**: Hệ thống mail
- **Public Notice**: Thông báo công khai
- **Roll Notice**: Thông báo cuộn
- **Chat Reward**: Phần thưởng chat

### 7. Data Management
- **Data Audit**: Audit dữ liệu
- **Data Clean**: Làm sạch dữ liệu
- **Data Export**: Xuất dữ liệu
- **Data Import**: Nhập dữ liệu

## Giao thức kết nối

### 1. GM Tools ↔ Gmserver
- **Protocol**: HTTP/HTTPS
- **Port**: 8808
- **Purpose**: GM tool operations
- **Endpoints**:
  - Player management
  - Guild management
  - Payment management
  - Game management

### 2. Gamexshard ↔ Gmserver
- **Protocol**: gRPC
- **Purpose**: Game operations
- **Commands**:
  - Player operations
  - Guild operations
  - Game operations
  - Data operations

### 3. Payx ↔ Gmserver
- **Protocol**: gRPC
- **Purpose**: Payment operations
- **Commands**:
  - Payment processing
  - Payment audit
  - Payment statistics

### 4. Other Services ↔ Gmserver
- **Protocol**: gRPC
- **Purpose**: Service management
- **Commands**:
  - Service operations
  - Service monitoring
  - Service configuration

## Cấu hình chính

### Development Config
```toml
[CommonCfg]
etcd_endpoint = ["http://127.0.0.1:2379/"]
ServerEtcdRoot = "root/server"
DevopsEtcdRoot = "root/devops"
Gid = 12
Gids = [12,32]
internalip = "127.0.0.1"
gmId = "gmserver1"
GameMachineEtcdRoot = "/skydns/net/taiyouxi/gamex"

mongo_pay_db_name = "PayDB"
mongo_silence_db_name = "SilenceDB"
gid_type = 2

# MySQL Database
db_driver = "mysql"
gm_server_mysql_url = "root:123456@tcp(127.0.0.1:3306)/jws2_gmtools"
db_max_open_conns = 100
db_max_idle_conns = 20
db_conn_max_life_time = 600

# FeiShu Integration
FeiShuServerCheckTestUrl = "https://open.feishu.cn/open-apis/bot/v2/hook/7e245e57-077f-4715-b13d-6ee4a0512b0d"
FeiShuServerCheckUrl = ""
FeiShuServerCheckCNUrl = ""
FeiShuEnable = true
FeiShuSecretKey = "5XmM1vTD7YNUI3wg7Tdouc"

# SDK Configuration
[SdkNtConfig] # 国内
appId = "1002"
secretKey = "eb6cf221fc3a527672c4b44f8a6fc774"

[SdkNtHMTConfig] # 港澳台东南亚
appId = "1009"
secretKey = "4b6d0d3f9bbd9697c2ad69ac46b0370c"

[SdkNtJPConfig] # 日本
appId = "1011"
secretKey = "8151eeb18d81dffff7e8e9d76fce8f37"

[SdkNtVNConfig] # 越南
appId = "1016"
secretKey = "4fdd5421ced3203b8f96040db56b653c"

# YiDun Configuration
[YiDun]
Enable = true
BusinessId = "224af423e0a84d37ee459cc1bfdfac89"
SecretId = "ef6a1dd6d17379d48d2908e6f093260b"
SecretKey = "81f815ec14c789f1571b0a4f3ca5f5e5"
Version = "v5.1"
Url = "http://as.dun.163.com/v5/text/check"

# Client Hot Fix
[ClientHotFix]
CloudDriver = ""
ClientCdnBucket = "{{etcdvalue.ClientCdnBucket}}"
ClientCdnRegion = "{{etcdvalue.ClientCdnRegion}}"
ClientCdnDataPath = "{{etcdvalue.ClientCdnDataPath}}"
ClientCdnAccessKey = "{{etcdvalue.ClientCdnAccessKey}}"
ClientCdnSecretKey = "{{etcdvalue.ClientCdnSecretKey}}"
ClientCdnDataPathLittlePkg = "{{etcdvalue.ClientCdnDataPathLittlePkg}}"
```

## Database Integration

### 1. MySQL Database
- **Database**: jws2_gmtools
- **Purpose**: GM tools data
- **Tables**: Player data, guild data, payment data
- **Connection Pool**: 100 max connections

### 2. MongoDB
- **PayDB**: Payment database
- **SilenceDB**: Silence system database
- **Purpose**: NoSQL data storage

### 3. Redis
- **Purpose**: Caching and session storage
- **Features**: High-performance caching

### 4. Pika
- **Purpose**: Profile management
- **Features**: Redis-compatible storage

## API Endpoints

### 1. Player Management
- **GET** `/api/player/info/{playerId}` - Get player info
- **POST** `/api/player/ban` - Ban player
- **POST** `/api/player/unban` - Unban player
- **DELETE** `/api/player/delete` - Delete player
- **GET** `/api/player/statistics` - Get player statistics

### 2. Guild Management
- **GET** `/api/guild/info/{guildId}` - Get guild info
- **GET** `/api/guild/members/{guildId}` - Get guild members
- **POST** `/api/guild/operation` - Guild operation
- **GET** `/api/guild/statistics` - Get guild statistics

### 3. Payment Management
- **GET** `/api/payment/statistics` - Get payment statistics
- **POST** `/api/payment/refund` - Process refund
- **GET** `/api/payment/audit` - Get payment audit

### 4. Game Management
- **POST** `/api/game/switch` - Toggle game feature
- **GET** `/api/game/version` - Get game version
- **POST** `/api/game/hotdata` - Update hot data
- **POST** `/api/game/hotpatch` - Apply hot patch

### 5. Server Management
- **GET** `/api/server/group` - Get server group
- **GET** `/api/server/shard` - Get shard info
- **GET** `/api/server/state` - Get server state
- **POST** `/api/server/merge` - Merge servers

## Security Features

### 1. Authentication
- **GM Authentication**: Xác thực GM
- **Role-based Access**: Phân quyền theo vai trò
- **Session Management**: Quản lý phiên
- **Token Validation**: Xác thực token

### 2. Data Security
- **Data Encryption**: Mã hóa dữ liệu
- **Access Control**: Kiểm soát truy cập
- **Audit Logging**: Log audit
- **Data Validation**: Xác thực dữ liệu

### 3. API Security
- **Rate Limiting**: Giới hạn tốc độ
- **Input Validation**: Xác thực input
- **SQL Injection Prevention**: Ngăn chặn SQL injection
- **XSS Prevention**: Ngăn chặn XSS

## Monitoring & Logging

### Metrics
- **GM Operations**: Số lượng thao tác GM
- **Player Operations**: Số lượng thao tác người chơi
- **Payment Operations**: Số lượng thao tác thanh toán
- **Server Operations**: Số lượng thao tác server
- **Error Rate**: Tỷ lệ lỗi
- **Response Time**: Thời gian phản hồi

### Logging
- **GM Log**: Log thao tác GM
- **Player Log**: Log thao tác người chơi
- **Payment Log**: Log thanh toán
- **Error Log**: Log lỗi
- **Security Log**: Log bảo mật

## Deployment

### Build
```bash
cd gmserver
go build -o gmserver
```

### Run
```bash
# Development
./gmserver allinone -c config.toml

# Production
./gmserver allinone -c config_g32.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o gmserver

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/gmserver .
CMD ["./gmserver", "allinone"]
```

## Troubleshooting

### Common Issues
1. **Database Connection**: Kiểm tra kết nối database
2. **GM Authentication**: Kiểm tra xác thực GM
3. **API Response**: Kiểm tra phản hồi API
4. **Data Consistency**: Kiểm tra tính nhất quán dữ liệu

### Debug Tools
- **GM Inspector**: Kiểm tra GM tools
- **Database Monitor**: Giám sát database
- **API Monitor**: Giám sát API
- **Log Analysis**: Phân tích log

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **GM Common**: GM utilities
- **Proto**: Protocol definitions

### External Dependencies
- **MySQL**: GM tools database
- **MongoDB**: NoSQL database
- **Redis**: Caching
- **Pika**: Profile storage
- **etcd**: Service discovery
- **gRPC**: Internal communication
- **PlanX**: Platform framework

## Development

### Code Structure
- **GM Logic**: Logic GM tools
- **API Layer**: Lớp API
- **Database Layer**: Lớp database
- **Security Layer**: Lớp bảo mật

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **GM Tests**: GM tool testing
- **Security Tests**: Security testing

## Contact

- **Author**: renyida
- **Email**: renyida@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service gmserver - server quản lý game và công cụ GM của hệ thống JWS2.*
