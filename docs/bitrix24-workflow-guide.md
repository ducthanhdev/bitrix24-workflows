# Hướng Dẫn Thiết Kế Workflow trên Bitrix24

## Tổng Quan
Tài liệu này hướng dẫn chi tiết cách thiết kế và triển khai hai quy trình workflow trên Bitrix24:
1. **Quy trình nghỉ phép** (3 cấp phê duyệt)
2. **Quy trình xin phê duyệt chi phí công tác** (4 cấp phê duyệt)

## Yêu Cầu Hệ Thống
- Bitrix24 với quyền admin
- Module Business Process đã được kích hoạt
- Quyền tạo và quản lý workflow

## Bước 1: Chuẩn Bị Môi Trường Bitrix24

### 1.1 Đăng nhập và Kiểm tra Quyền
1. Đăng nhập vào Bitrix24 với tài khoản admin
2. Vào **Settings** → **Company** → **Users**
3. Đảm bảo có quyền quản lý Business Process

### 1.2 Kích Hoạt Module Business Process
1. Vào **Settings** → **Product Settings** → **Business Processes**
2. Đảm bảo module đã được kích hoạt
3. Kiểm tra quyền truy cập của các nhóm người dùng

### 1.3 Thiết Lập Cấu Trúc Tổ Chức
Trước khi tạo workflow, cần thiết lập cấu trúc tổ chức:

#### Tạo Phòng Ban:
- Phòng Nhân Sự
- Phòng Tài Chính  
- Phòng Ban Khác (theo tổ chức thực tế)

#### Tạo Vị Trí:
- Nhân viên
- Quản lý trực tiếp
- Trưởng phòng nhân sự
- Trưởng phòng tài chính
- Phó giám đốc
- Giám đốc

## Bước 2: Tạo Quy Trình Nghỉ Phép (3 Cấp Phê Duyệt)

### 2.1 Tạo Smart Process Automation
1. Vào **CRM** → **Smart Process Automation**
2. Click **Create Automation**
3. Chọn **Process** → **New Process**

### 2.2 Thiết Kế Form Đầu Vào
Tạo các trường dữ liệu sau:

```
- Họ và tên (Text, Required)
- Phòng ban (List, Required)
- Loại nghỉ phép (List, Required):
  * Nghỉ phép năm
  * Nghỉ ốm
  * Nghỉ không lương
  * Nghỉ việc riêng
  * Nghỉ thai sản
- Ngày bắt đầu (Date, Required)
- Ngày kết thúc (Date, Required)
- Số ngày nghỉ (Calculated field)
- Lý do nghỉ (Textarea, Required)
- Trạng thái (List): Chờ phê duyệt cấp 1, Chờ phê duyệt cấp 2, Chờ phê duyệt cấp 3, Đã phê duyệt, Từ chối
- Lý do từ chối (Textarea)
- Người phê duyệt cấp 1 (User)
- Người phê duyệt cấp 2 (User)
- Người phê duyệt cấp 3 (User)
```

### 2.3 Thiết Kế Workflow

#### Bước 1: Khởi Tạo Quy Trình
- **Trigger**: Khi tạo record mới
- **Action**: Gửi thông báo đến Quản lý trực tiếp

#### Bước 2: Phê Duyệt Cấp 1 (Quản lý trực tiếp)
- **Condition**: Trạng thái = "Chờ phê duyệt cấp 1"
- **Action**: Gửi task cho Quản lý trực tiếp
- **Decision**: 
  - Nếu phê duyệt → Cập nhật trạng thái = "Chờ phê duyệt cấp 2"
  - Nếu từ chối → Cập nhật trạng thái = "Từ chối" và gửi thông báo

#### Bước 3: Phê Duyệt Cấp 2 (Trưởng phòng nhân sự)
- **Condition**: Trạng thái = "Chờ phê duyệt cấp 2"
- **Validation**: Kiểm tra số ngày nghỉ phép còn lại
- **Action**: Gửi task cho Trưởng phòng nhân sự
- **Decision**:
  - Nếu phê duyệt → Cập nhật trạng thái = "Chờ phê duyệt cấp 3"
  - Nếu từ chối → Cập nhật trạng thái = "Từ chối"

#### Bước 4: Phê Duyệt Cấp 3 (Giám đốc)
- **Condition**: Trạng thái = "Chờ phê duyệt cấp 3"
- **Action**: Gửi task cho Giám đốc
- **Decision**:
  - Nếu phê duyệt → Cập nhật trạng thái = "Đã phê duyệt"
  - Nếu từ chối → Cập nhật trạng thái = "Từ chối"

#### Bước 5: Thông Báo Kết Quả
- **Condition**: Trạng thái = "Đã phê duyệt" hoặc "Từ chối"
- **Action**: Gửi email thông báo cho nhân viên

### 2.4 Cấu Hình Thông Báo
- **Email Template**: Tạo template thông báo phê duyệt/từ chối
- **Notification**: Cấu hình thông báo trong Bitrix24
- **Audit Trail**: Tự động ghi log mọi thay đổi

## Bước 3: Tạo Quy Trình Chi Phí Công Tác (4 Cấp Phê Duyệt)

### 3.1 Tạo Smart Process Automation
Tương tự như quy trình nghỉ phép, tạo process mới cho chi phí công tác.

### 3.2 Thiết Kế Form Đầu Vào
```
- Họ và tên (Text, Required)
- Phòng ban (List, Required)
- Mục đích công tác (Text, Required)
- Địa điểm (Text, Required)
- Ngày bắt đầu (Date, Required)
- Ngày kết thúc (Date, Required)
- Chi phí dự kiến:
  * Vé máy bay (Number)
  * Khách sạn (Number)
  * Ăn uống (Number)
  * Chi phí khác (Number)
  * Tổng chi phí (Calculated field)
- Trạng thái (List): Chờ phê duyệt cấp 1, Chờ phê duyệt cấp 2, Chờ phê duyệt cấp 3, Chờ phê duyệt cấp 4, Đã phê duyệt, Từ chối
- Lý do từ chối (Textarea)
- Người phê duyệt cấp 1 (User)
- Người phê duyệt cấp 2 (User)
- Người phê duyệt cấp 3 (User)
- Người phê duyệt cấp 4 (User)
```

### 3.3 Thiết Kế Workflow

#### Bước 1: Khởi Tạo Quy Trình
- **Trigger**: Khi tạo record mới
- **Action**: Gửi thông báo đến Quản lý trực tiếp

#### Bước 2: Phê Duyệt Cấp 1 (Quản lý trực tiếp)
- **Condition**: Trạng thái = "Chờ phê duyệt cấp 1"
- **Action**: Gửi task cho Quản lý trực tiếp
- **Decision**:
  - Nếu phê duyệt → Cập nhật trạng thái = "Chờ phê duyệt cấp 2"
  - Nếu từ chối → Cập nhật trạng thái = "Từ chối"

#### Bước 3: Phê Duyệt Cấp 2 (Trưởng phòng tài chính)
- **Condition**: Trạng thái = "Chờ phê duyệt cấp 2"
- **Validation**: Kiểm tra ngân sách khả dụng
- **Action**: Gửi task cho Trưởng phòng tài chính
- **Decision**:
  - Nếu phê duyệt → Cập nhật trạng thái = "Chờ phê duyệt cấp 3"
  - Nếu từ chối → Cập nhật trạng thái = "Từ chối"

#### Bước 4: Phê Duyệt Cấp 3 (Phó giám đốc)
- **Condition**: Trạng thái = "Chờ phê duyệt cấp 3"
- **Action**: Gửi task cho Phó giám đốc
- **Decision**:
  - Nếu phê duyệt → Cập nhật trạng thái = "Chờ phê duyệt cấp 4"
  - Nếu từ chối → Cập nhật trạng thái = "Từ chối"

#### Bước 5: Phê Duyệt Cấp 4 (Giám đốc)
- **Condition**: Trạng thái = "Chờ phê duyệt cấp 4"
- **Action**: Gửi task cho Giám đốc
- **Decision**:
  - Nếu phê duyệt → Cập nhật trạng thái = "Đã phê duyệt"
  - Nếu từ chối → Cập nhật trạng thái = "Từ chối"

#### Bước 6: Thông Báo Kết Quả
- **Condition**: Trạng thái = "Đã phê duyệt" hoặc "Từ chối"
- **Action**: Gửi email thông báo chi tiết cho nhân viên

## Bước 4: Cấu Hình Nâng Cao

### 4.1 Thiết Lập Điều Kiện Kiểm Tra
- **Nghỉ phép**: Kiểm tra số ngày nghỉ còn lại
- **Chi phí công tác**: Kiểm tra ngân sách khả dụng
- **Validation Rules**: Tạo quy tắc kiểm tra dữ liệu

### 4.2 Tích Hợp Thông Báo
- **Email Templates**: Tạo template cho từng loại thông báo
- **Push Notifications**: Cấu hình thông báo đẩy
- **SMS**: Tích hợp SMS (nếu có)

### 4.3 Báo Cáo và Theo Dõi
- **Dashboard**: Tạo dashboard theo dõi workflow
- **Reports**: Tạo báo cáo thống kê
- **Analytics**: Phân tích hiệu suất quy trình

## Bước 5: Xuất File Workflow

### 5.1 Xuất Quy Trình Nghỉ Phép
1. Vào **CRM** → **Smart Process Automation**
2. Chọn quy trình "Nghỉ phép 3 cấp"
3. Click **Actions** → **Export**
4. Chọn định dạng .bpt
5. Lưu file với tên: `NghiPhep_3Cap.bpt`

### 5.2 Xuất Quy Trình Chi Phí Công Tác
1. Vào **CRM** → **Smart Process Automation**
2. Chọn quy trình "Chi phí công tác 4 cấp"
3. Click **Actions** → **Export**
4. Chọn định dạng .bpt
5. Lưu file với tên: `ChiPhiCongTac_4Cap.bpt`

## Bước 6: Kiểm Tra và Test

### 6.1 Test Quy Trình
1. Tạo test case cho từng bước
2. Kiểm tra logic điều kiện
3. Test thông báo
4. Kiểm tra audit trail

### 6.2 Training Người Dùng
1. Tạo hướng dẫn sử dụng
2. Training cho admin
3. Training cho người dùng cuối

## Lưu Ý Quan Trọng

### Bảo Mật
- Thiết lập quyền truy cập phù hợp
- Mã hóa dữ liệu nhạy cảm
- Backup định kỳ

### Hiệu Suất
- Tối ưu hóa workflow
- Giảm thiểu số lượng bước không cần thiết
- Caching dữ liệu

### Khả Năng Mở Rộng
- Thiết kế modular
- Dễ dàng thêm/sửa bước
- Hỗ trợ nhiều ngôn ngữ

## Kết Luận

Việc thiết kế workflow trên Bitrix24 yêu cầu:
1. Hiểu rõ yêu cầu nghiệp vụ
2. Thiết kế cấu trúc dữ liệu phù hợp
3. Cấu hình workflow logic chính xác
4. Thiết lập thông báo và báo cáo
5. Test kỹ lưỡng trước khi triển khai

Với hướng dẫn này, bạn có thể tạo ra hai quy trình workflow hoàn chỉnh và chuyên nghiệp trên Bitrix24.
