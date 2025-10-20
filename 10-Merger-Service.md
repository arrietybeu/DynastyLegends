# Merger Service - Server Merge Service

## Tổng quan

**Merger** là service xử lý hợp nhất server (server merge) của hệ thống JWS2, chịu trách nhiệm thực hiện quá trình hợp nhất dữ liệu giữa các server, quản lý tiến trình merge, và đảm bảo tính toàn vẹn dữ liệu trong quá trình hợp nhất.

## Thông tin cơ bản

- **Tên service**: merger
- **Port chính**: 7101 (gRPC)
- **Server ID**: merger1
- **Protocol**: gRPC
- **Ngôn ngữ**: Go (Golang)
- **Kiến trúc**: Distributed merge service với worker system

## Cấu trúc thư mục

```
merger/
├── cmds/                     # Command line interface
│   ├── allinone/            # All-in-one command
│   │   └── allinone.go      # All-in-one implementation
│   ├── cmds.go              # Commands
│   └── etcdreg.go           # ETCD registration
├── conf/                    # Configuration files
│   ├── config.toml          # Main config
│   └── config_g32.toml      # Production config
├── config/                  # Config management
│   └── config.go            # Config utilities
├── core/                    # Core merge logic
│   ├── callback.go          # Callback functions
│   ├── const.go             # Constants
│   ├── etcd.go              # ETCD integration
│   ├── logics.go            # Merge logics
│   ├── manager.go           # Merge manager
│   └── service.go           # Merge service
├── worker/                  # Worker system
│   ├── err.go               # Error handling
│   ├── option.go            # Worker options
│   ├── worker_test.go       # Worker tests
│   └── worker.go            # Worker implementation
├── packageInfo.go           # Package information
├── packageInfo.toml         # Package info config
├── README.md                # Service readme
├── startmerger.sh           # Start script
└── main.go                  # Main entry point
```

## Chức năng chính

### 1. Server Merge Management
- **Merge Planning**: Lập kế hoạch merge
- **Merge Execution**: Thực hiện merge
- **Merge Monitoring**: Giám sát merge
- **Merge Rollback**: Rollback merge

### 2. Data Merge Processing
- **Data Validation**: Xác thực dữ liệu
- **Data Transformation**: Chuyển đổi dữ liệu
- **Data Consolidation**: Hợp nhất dữ liệu
- **Data Integrity**: Đảm bảo tính toàn vẹn

### 3. Progress Tracking
- **Shard Progress**: Tiến trình shard
- **Module Progress**: Tiến trình module
- **Overall Progress**: Tiến trình tổng thể
- **Progress Reporting**: Báo cáo tiến trình

### 4. Worker System
- **Worker Management**: Quản lý worker
- **Task Distribution**: Phân phối task
- **Worker Monitoring**: Giám sát worker
- **Worker Recovery**: Khôi phục worker

### 5. Database Operations
- **Database Connection**: Kết nối database
- **Data Migration**: Di chuyển dữ liệu
- **Data Backup**: Sao lưu dữ liệu
- **Data Restore**: Khôi phục dữ liệu

## Giao thức kết nối

### 1. Merger ↔ Gamexshard
- **Protocol**: gRPC
- **Purpose**: Merge operations
- **Commands**:
  - Start merge
  - Query merge status
  - Rollback merge
  - Reset merge

### 2. Merger ↔ Database
- **Protocol**: Database connection
- **Purpose**: Data operations
- **Features**:
  - Data migration
  - Data validation
  - Data consolidation

### 3. Merger ↔ ETCD
- **Protocol**: etcd
- **Purpose**: Service discovery
- **Features**:
  - Service registration
  - Configuration management
  - State synchronization

## Cấu hình chính

### Development Config
```toml
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

gid = 12
serverid = "merger1"
internalip = "127.0.0.1"
```

### Production Config
```toml
# Similar structure with production values
gid = 32
serverid = "merger1"
internalip = "production_ip"
```

## Merge Process

### 1. Merge Planning
- **Target Server**: Server đích
- **Source Servers**: Server nguồn
- **Merge Strategy**: Chiến lược merge
- **Data Mapping**: Ánh xạ dữ liệu

### 2. Merge Execution
- **Data Validation**: Xác thực dữ liệu
- **Data Migration**: Di chuyển dữ liệu
- **Data Consolidation**: Hợp nhất dữ liệu
- **Data Verification**: Xác minh dữ liệu

### 3. Progress Tracking
- **Shard Progress**: Tiến trình từng shard
- **Module Progress**: Tiến trình từng module
- **Overall Progress**: Tiến trình tổng thể
- **Error Handling**: Xử lý lỗi

### 4. Merge Completion
- **Final Validation**: Xác thực cuối cùng
- **Data Cleanup**: Dọn dẹp dữ liệu
- **Service Update**: Cập nhật service
- **Notification**: Thông báo hoàn thành

## Manager System

### 1. Manager Structure
```go
type Manager struct {
    Progresses   map[int32]*ShardProgress       // [shardID]progress
    ShardPools   map[int32]redispool.IPool      // [shardID]pool
    ProfilePools map[int32]redispool.IPool      // [shardID]pool
    RenameDB     map[MShard]merge_db.ICMDLoader // [[shardID,MergeUID]]RenameDB
    mutex        sync.RWMutex                   // Mutex for thread safety
}
```

### 2. Shard Progress
```go
type ShardProgress struct {
    *pb.ShardProgress
}

type ModuleProgress struct {
    *pb.ModuleProgress
}
```

### 3. Progress Management
- **Progress Tracking**: Theo dõi tiến trình
- **Progress Update**: Cập nhật tiến trình
- **Progress Reporting**: Báo cáo tiến trình
- **Progress Recovery**: Khôi phục tiến trình

## Worker System

### 1. Worker Management
- **Worker Creation**: Tạo worker
- **Worker Assignment**: Gán task cho worker
- **Worker Monitoring**: Giám sát worker
- **Worker Recovery**: Khôi phục worker

### 2. Task Distribution
- **Task Queue**: Hàng đợi task
- **Task Assignment**: Gán task
- **Task Execution**: Thực hiện task
- **Task Completion**: Hoàn thành task

### 3. Error Handling
- **Error Detection**: Phát hiện lỗi
- **Error Recovery**: Khôi phục lỗi
- **Error Reporting**: Báo cáo lỗi
- **Error Rollback**: Rollback lỗi

## gRPC API

### 1. Start Merge
```bash
grpcurl -plaintext -d '{
  "target": {"gid": 32, "sid": 64, "mergeUID": 1888}, 
  "sources": [
    {"gid": 32, "sid": 64, "mergeUID": 1234}, 
    {"gid": 32, "sid": 65, "mergeUID": 1357}, 
    {"gid": 32, "sid": 66, "mergeUID": 2468}
  ]
}' localhost:7101 pb.Merger.start
```

### 2. Query Merge Status
```bash
grpcurl -plaintext -d '{"isAll": true}' localhost:7101 pb.Merger.query
```

### 3. Reset Merge
```bash
grpcurl -plaintext -d '{}' localhost:7101 pb.Merger.reset
```

### 4. Rollback Merge
```bash
grpcurl -plaintext -d '{
  "target": {"gid": 32, "sid": 64, "mergeUID": 1888}, 
  "sources": [
    {"gid": 32, "sid": 64, "mergeUID": 1234}, 
    {"gid": 32, "sid": 65, "mergeUID": 1357}, 
    {"gid": 32, "sid": 66, "mergeUID": 2468}
  ]
}' localhost:7101 pb.Merger.rollback
```

## Data Operations

### 1. Data Validation
- **Data Integrity Check**: Kiểm tra tính toàn vẹn
- **Data Consistency Check**: Kiểm tra tính nhất quán
- **Data Completeness Check**: Kiểm tra tính đầy đủ
- **Data Format Check**: Kiểm tra định dạng

### 2. Data Migration
- **Data Extraction**: Trích xuất dữ liệu
- **Data Transformation**: Chuyển đổi dữ liệu
- **Data Loading**: Tải dữ liệu
- **Data Verification**: Xác minh dữ liệu

### 3. Data Consolidation
- **Data Merging**: Hợp nhất dữ liệu
- **Data Deduplication**: Loại bỏ trùng lặp
- **Data Aggregation**: Tổng hợp dữ liệu
- **Data Normalization**: Chuẩn hóa dữ liệu

## Monitoring & Logging

### Metrics
- **Merge Progress**: Tiến trình merge
- **Data Volume**: Khối lượng dữ liệu
- **Processing Time**: Thời gian xử lý
- **Error Rate**: Tỷ lệ lỗi
- **Worker Status**: Trạng thái worker
- **Database Performance**: Hiệu suất database

### Logging
- **Merge Log**: Log merge
- **Progress Log**: Log tiến trình
- **Error Log**: Log lỗi
- **Performance Log**: Log hiệu suất

## Error Handling

### Merge Errors
- **Data Validation Error**: Lỗi xác thực dữ liệu
- **Data Migration Error**: Lỗi di chuyển dữ liệu
- **Data Consolidation Error**: Lỗi hợp nhất dữ liệu
- **Database Error**: Lỗi database

### System Errors
- **Worker Error**: Lỗi worker
- **Network Error**: Lỗi mạng
- **Service Error**: Lỗi service
- **Configuration Error**: Lỗi cấu hình

## Security Features

### 1. Data Security
- **Data Encryption**: Mã hóa dữ liệu
- **Data Backup**: Sao lưu dữ liệu
- **Data Validation**: Xác thực dữ liệu
- **Access Control**: Kiểm soát truy cập

### 2. Process Security
- **Process Validation**: Xác thực quy trình
- **Permission Check**: Kiểm tra quyền
- **Audit Logging**: Log audit
- **Rollback Capability**: Khả năng rollback

## Performance & Optimization

### 1. Merge Optimization
- **Parallel Processing**: Xử lý song song
- **Batch Processing**: Xử lý theo batch
- **Memory Management**: Quản lý memory
- **Database Optimization**: Tối ưu database

### 2. Worker Optimization
- **Worker Pool**: Pool worker
- **Task Distribution**: Phân phối task
- **Load Balancing**: Cân bằng tải
- **Resource Management**: Quản lý tài nguyên

## Deployment

### Build
```bash
cd merger
go build -o merger
```

### Run
```bash
# Development
./merger allinone -c config.toml

# Production
./merger allinone -c config_g32.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o merger

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/merger .
CMD ["./merger", "allinone"]
```

## Troubleshooting

### Common Issues
1. **Merge Timeout**: Kiểm tra database performance
2. **Data Validation Error**: Kiểm tra data format
3. **Worker Error**: Kiểm tra worker status
4. **Database Connection**: Kiểm tra database connection

### Debug Tools
- **Merge Monitor**: Giám sát merge
- **Worker Inspector**: Kiểm tra worker
- **Database Monitor**: Giám sát database
- **Log Analysis**: Phân tích log

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Merge Common**: Merge utilities
- **Proto**: Protocol definitions

### External Dependencies
- **etcd**: Service discovery
- **gRPC**: Internal communication
- **Redis**: Data storage
- **MySQL**: Database
- **PlanX**: Platform framework

## Development

### Code Structure
- **Merge Logic**: Logic merge
- **Worker System**: Hệ thống worker
- **Manager System**: Hệ thống manager
- **API Layer**: Lớp API

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Merge Tests**: Merge testing
- **Worker Tests**: Worker testing

## Contact

- **Author**: jiangyi
- **Email**: jiangyi@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service merger - server xử lý hợp nhất server của hệ thống JWS2.*
