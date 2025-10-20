# Sensitiveword Service - Sensitive Word Filter Server

## Tổng quan

**Sensitiveword** là service lọc từ nhạy cảm của hệ thống JWS2, chịu trách nhiệm kiểm tra và thay thế các từ nhạy cảm trong nội dung người dùng, đảm bảo nội dung phù hợp và tuân thủ các quy định cộng đồng.

## Thông tin cơ bản

- **Tên service**: sensitiveword
- **Port chính**: gRPC (internal)
- **Server ID**: SensitiveWord1
- **Protocol**: gRPC
- **Ngôn ngữ**: Go (Golang)
- **Kiến trúc**: Sensitive word filter server với batch processing

## Cấu trúc thư mục

```
sensitiveword/
├── cmds/                     # Command line interface
│   ├── allinone/            # All-in-one command
│   │   └── allinone.go      # All-in-one implementation
│   ├── cmds.go              # Commands
│   └── common.go            # Common utilities
├── conf/                    # Configuration files
│   ├── config.toml          # Main config
│   ├── config_g32.toml      # Production config
│   ├── metrics.toml         # Metrics config
│   └── tracing.toml         # Tracing config
├── config/                  # Config management
│   └── config.go            # Config utilities
├── logics/                  # Business logic
│   ├── query_msg.go         # Query message handling
│   └── reg.go               # Registration
├── hotchangelevel.sh        # Hot change level script
├── pprof                    # Profiling
├── startSensitiveWord.sh    # Start script
├── startSensitiveWord2.sh   # Start script 2
└── main.go                  # Main entry point
```

## Chức năng chính

### 1. Sensitive Word Detection
- **Word Checking**: Kiểm tra từ nhạy cảm
- **Symbol Checking**: Kiểm tra ký tự đặc biệt
- **Content Validation**: Xác thực nội dung
- **Batch Processing**: Xử lý hàng loạt

### 2. Content Filtering
- **Text Filtering**: Lọc văn bản
- **Word Replacement**: Thay thế từ
- **Content Sanitization**: Làm sạch nội dung
- **Safe Content**: Nội dung an toàn

### 3. Word Management
- **Sensitive Word Database**: Cơ sở dữ liệu từ nhạy cảm
- **Word Categories**: Phân loại từ
- **Word Updates**: Cập nhật từ
- **Word Statistics**: Thống kê từ

### 4. Content Processing
- **Real-time Processing**: Xử lý real-time
- **Batch Processing**: Xử lý hàng loạt
- **Content Analysis**: Phân tích nội dung
- **Result Caching**: Cache kết quả

### 5. Quality Assurance
- **Content Quality**: Chất lượng nội dung
- **Compliance Check**: Kiểm tra tuân thủ
- **Policy Enforcement**: Thực thi chính sách
- **Audit Logging**: Log audit

## Giao thức kết nối

### 1. Gamexshard ↔ Sensitiveword
- **Protocol**: gRPC
- **Purpose**: Content filtering
- **Commands**:
  - Check sensitive words
  - Replace sensitive words
  - Validate content
  - Get word statistics

### 2. Other Services ↔ Sensitiveword
- **Protocol**: gRPC
- **Purpose**: Content validation
- **Features**:
  - Real-time filtering
  - Batch processing
  - Content validation
  - Word replacement

### 3. Sensitiveword ↔ Word Database
- **Protocol**: Internal
- **Purpose**: Word management
- **Features**:
  - Word lookup
  - Word updates
  - Category management
  - Statistics collection

## Cấu hình chính

### Development Config
```toml
etcd_endpoint = ["http://127.0.0.1:2379/"]
etcd_devops = "root/devops"
etcd_server = "root/server"

gid = 12
serverid = "SensitiveWord1"
internalip = "127.0.0.1"
```

### Production Config
```toml
# Similar structure with production values
gid = 32
serverid = "SensitiveWord1"
internalip = "production_ip"
```

## Sensitive Word Processing

### 1. Word Detection
```go
type QueryMsg struct {
    *protogen.SensitiveWordMsg
    Id int32
}

func (req *QueryMsg) Handle(span opentracing.Span) proto.Message {
    ret := &protogen.SensitiveWordResp{}
    
    if req.GetType() == protogen.TSensitiveWord_TCheck {
        if req.GetCheckSymbol() {
            ret.HasSensitive = name.CheckSymbol(req.Content)
        }
        if !ret.HasSensitive {
            ret.HasSensitive = name.GetValidateWord().HasSensitive(req.Content)
        }
    } else if req.GetType() == protogen.TSensitiveWord_TReplace {
        ret.Content = name.GetValidateWord().Replace(req.Content)
    }
    
    return ret
}
```

### 2. Content Types
- **Text Content**: Nội dung văn bản
- **Chat Messages**: Tin nhắn chat
- **Player Names**: Tên người chơi
- **Guild Names**: Tên guild
- **Comments**: Bình luận

### 3. Processing Modes
- **Check Mode**: Chế độ kiểm tra
- **Replace Mode**: Chế độ thay thế
- **Validate Mode**: Chế độ xác thực
- **Filter Mode**: Chế độ lọc

## Word Database

### 1. Sensitive Word Categories
- **Profanity**: Từ tục tĩu
- **Hate Speech**: Ngôn từ thù hận
- **Spam**: Spam
- **Inappropriate**: Không phù hợp
- **Political**: Chính trị
- **Religious**: Tôn giáo

### 2. Word Management
- **Word Addition**: Thêm từ
- **Word Removal**: Xóa từ
- **Word Update**: Cập nhật từ
- **Category Management**: Quản lý danh mục

### 3. Word Statistics
- **Detection Count**: Số lần phát hiện
- **Replacement Count**: Số lần thay thế
- **Category Statistics**: Thống kê danh mục
- **Usage Patterns**: Mẫu sử dụng

## Performance & Optimization

### 1. Processing Optimization
- **Batch Processing**: Xử lý hàng loạt
- **Parallel Processing**: Xử lý song song
- **Memory Management**: Quản lý memory
- **Cache Optimization**: Tối ưu cache

### 2. Word Lookup Optimization
- **Trie Data Structure**: Cấu trúc dữ liệu Trie
- **Hash Tables**: Bảng băm
- **Index Optimization**: Tối ưu index
- **Memory Efficiency**: Hiệu quả memory

### 3. Content Processing
- **Stream Processing**: Xử lý stream
- **Buffer Management**: Quản lý buffer
- **Memory Pooling**: Pool memory
- **Garbage Collection**: Thu gom rác

## Monitoring & Logging

### Metrics
- **Request Count**: Số lượng request
- **Detection Count**: Số lần phát hiện
- **Replacement Count**: Số lần thay thế
- **Processing Time**: Thời gian xử lý
- **Error Rate**: Tỷ lệ lỗi
- **Cache Hit Rate**: Tỷ lệ hit cache

### Logging
- **Content Log**: Log nội dung
- **Detection Log**: Log phát hiện
- **Replacement Log**: Log thay thế
- **Error Log**: Log lỗi
- **Performance Log**: Log hiệu suất

## Error Handling

### Content Errors
- **Invalid Content**: Nội dung không hợp lệ
- **Encoding Error**: Lỗi mã hóa
- **Processing Error**: Lỗi xử lý
- **Validation Error**: Lỗi xác thực

### System Errors
- **Database Error**: Lỗi database
- **Memory Error**: Lỗi memory
- **Network Error**: Lỗi mạng
- **Service Error**: Lỗi service

## Security Features

### 1. Content Security
- **Input Validation**: Xác thực input
- **Content Sanitization**: Làm sạch nội dung
- **Encoding Security**: Bảo mật mã hóa
- **Injection Prevention**: Ngăn chặn injection

### 2. Data Protection
- **Data Encryption**: Mã hóa dữ liệu
- **Access Control**: Kiểm soát truy cập
- **Audit Logging**: Log audit
- **Privacy Protection**: Bảo vệ quyền riêng tư

## Deployment

### Build
```bash
cd sensitiveword
go build -o sensitiveword
```

### Run
```bash
# Development
./sensitiveword allinone -c config.toml

# Production
./sensitiveword allinone -c config_g32.toml
```

### Docker
```dockerfile
FROM golang:1.19-alpine AS builder
WORKDIR /app
COPY . .
RUN go build -o sensitiveword

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/sensitiveword .
CMD ["./sensitiveword", "allinone"]
```

## Troubleshooting

### Common Issues
1. **High Processing Time**: Kiểm tra word database size
2. **Memory Usage**: Kiểm tra memory management
3. **False Positives**: Kiểm tra word database
4. **Performance Issues**: Kiểm tra optimization

### Debug Tools
- **Content Inspector**: Kiểm tra nội dung
- **Word Database Monitor**: Giám sát database
- **Performance Profiler**: Profile hiệu suất
- **Log Analysis**: Phân tích log

## Dependencies

### Internal Dependencies
- **Common**: Shared libraries
- **Name Validation**: Name validation utilities
- **Proto**: Protocol definitions

### External Dependencies
- **etcd**: Service discovery
- **gRPC**: Internal communication
- **PlanX**: Platform framework

## Development

### Code Structure
- **Word Processing**: Xử lý từ
- **Content Filtering**: Lọc nội dung
- **Message Handling**: Xử lý message
- **Validation Logic**: Logic xác thực

### Testing
- **Unit Tests**: Unit testing
- **Integration Tests**: Integration testing
- **Content Tests**: Content testing
- **Performance Tests**: Performance testing

## Contact

- **Author**: anonymous
- **Email**: anonymous@taiyouxi.cn
- **Company**: Ticore Game Company

---

*Tài liệu này mô tả chi tiết service sensitiveword - server lọc từ nhạy cảm của hệ thống JWS2.*
