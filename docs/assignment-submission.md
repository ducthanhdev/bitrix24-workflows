# Tài Liệu Nộp Bài - Bitrix24 Workflow

## Tổng Quan Dự Án

Dự án thiết kế và xây dựng hai quy trình workflow trên nền tảng Bitrix24 theo yêu cầu đề bài:

### 1. Quy Trình Nghỉ Phép (3 Cấp Phê Duyệt)
### 2. Quy Trình Xin Phê Duyệt Chi Phí Công Tác (4 Cấp Phê Duyệt)

---

## 1. QUY TRÌNH NGHỈ PHÉP (3 CẤP PHÊ DUYỆT)

### 1.1 Mô Tả Quy Trình
Quy trình cho phép nhân viên gửi yêu cầu nghỉ phép, được xét duyệt qua 3 cấp quản lý trước khi được phê duyệt hoặc từ chối.

### 1.2 Các Bước Cụ Thể

#### **Bước 1: Nhân viên tạo yêu cầu nghỉ phép**
- **Thông tin bao gồm:**
  - Họ tên nhân viên
  - Phòng ban
  - Loại nghỉ phép (nghỉ phép năm, nghỉ ốm, nghỉ không lương, nghỉ việc riêng, nghỉ thai sản)
  - Ngày bắt đầu và ngày kết thúc
  - Lý do nghỉ
  - Số ngày nghỉ (tự động tính toán)

#### **Bước 2: Phê duyệt cấp 1 - Quản lý trực tiếp**
- **Nhiệm vụ:** Xem xét tính cần thiết và hợp lý của yêu cầu nghỉ phép
- **Thời gian:** 2 ngày làm việc
- **Quyết định:** Phê duyệt hoặc từ chối

#### **Bước 3: Phê duyệt cấp 2 - Trưởng phòng nhân sự**
- **Nhiệm vụ:** Kiểm tra số ngày nghỉ phép còn lại và tính hợp lệ
- **Thời gian:** 1 ngày làm việc
- **Quyết định:** Phê duyệt hoặc từ chối

#### **Bước 4: Phê duyệt cấp 3 - Giám đốc**
- **Nhiệm vụ:** Phê duyệt cuối cùng
- **Thời gian:** 1 ngày làm việc
- **Quyết định:** Phê duyệt hoặc từ chối

#### **Bước 5: Thông báo kết quả**
- Nhân viên nhận thông báo kết quả (phê duyệt/từ chối) qua Bitrix24
- Cập nhật số ngày nghỉ phép còn lại (nếu được phê duyệt)

### 1.3 Cấu Trúc Dữ Liệu

#### **Entity:** Leave_Request
```yaml
Fields:
  - employee_name: Text (Required)
  - employee_id: List (Required)
  - department: List (Required)
  - leave_type: List (Required)
  - start_date: Date (Required)
  - end_date: Date (Required)
  - total_days: Number (Calculated)
  - reason: Text (Required)
  - status: List (Required)
  - rejection_reason: Text
  - approver_level_1: List
  - approver_level_2: List
  - approver_level_3: List
  - approved_date: Date
```

#### **Status Values:**
- Chờ phê duyệt cấp 1
- Chờ phê duyệt cấp 2
- Chờ phê duyệt cấp 3
- Đã phê duyệt
- Từ chối
- Đã hủy

### 1.4 Automation Rules

#### **Rule 1: Khởi tạo quy trình**
- **Trigger:** Khi tạo record mới
- **Action:** Gửi thông báo đến Quản lý trực tiếp
- **Condition:** Status = "Chờ phê duyệt cấp 1"

#### **Rule 2: Phê duyệt cấp 1 → cấp 2**
- **Trigger:** Status = "Chờ phê duyệt cấp 2"
- **Action:** Gửi thông báo đến Trưởng phòng nhân sự

#### **Rule 3: Phê duyệt cấp 2 → cấp 3**
- **Trigger:** Status = "Chờ phê duyệt cấp 3"
- **Action:** Gửi thông báo đến Giám đốc

#### **Rule 4: Thông báo kết quả phê duyệt**
- **Trigger:** Status = "Đã phê duyệt"
- **Action:** Gửi thông báo cho nhân viên

#### **Rule 5: Thông báo từ chối**
- **Trigger:** Status = "Từ chối"
- **Action:** Gửi thông báo cho nhân viên kèm lý do

---

## 2. QUY TRÌNH XIN PHÊ DUYỆT CHI PHÍ CÔNG TÁC (4 CẤP PHÊ DUYỆT)

### 2.1 Mô Tả Quy Trình
Quy trình cho phép nhân viên gửi yêu cầu phê duyệt chi phí đi công tác, được xét duyệt qua 4 cấp quản lý trước khi được phê duyệt hoặc từ chối.

### 2.2 Các Bước Cụ Thể

#### **Bước 1: Nhân viên tạo yêu cầu chi phí công tác**
- **Thông tin bao gồm:**
  - Họ tên nhân viên
  - Phòng ban
  - Mục đích công tác
  - Địa điểm
  - Thời gian (từ ngày - đến ngày)
  - Danh sách chi phí dự kiến:
    - Vé máy bay
    - Khách sạn
    - Ăn uống
    - Di chuyển
    - Chi phí khác
  - Tổng chi phí (tự động tính toán)

#### **Bước 2: Phê duyệt cấp 1 - Quản lý trực tiếp**
- **Nhiệm vụ:** Xem xét tính cần thiết của chuyến công tác
- **Thời gian:** 2 ngày làm việc
- **Quyết định:** Phê duyệt hoặc từ chối

#### **Bước 3: Phê duyệt cấp 2 - Trưởng phòng tài chính**
- **Nhiệm vụ:** Kiểm tra ngân sách khả dụng
- **Thời gian:** 1 ngày làm việc
- **Quyết định:** Phê duyệt hoặc từ chối

#### **Bước 4: Phê duyệt cấp 3 - Phó giám đốc phụ trách tài chính**
- **Nhiệm vụ:** Xem xét tính hợp lý của chi phí
- **Thời gian:** 1 ngày làm việc
- **Quyết định:** Phê duyệt hoặc từ chối

#### **Bước 5: Phê duyệt cấp 4 - Giám đốc**
- **Nhiệm vụ:** Phê duyệt cuối cùng
- **Thời gian:** 1 ngày làm việc
- **Quyết định:** Phê duyệt hoặc từ chối

#### **Bước 6: Thông báo kết quả**
- Nhân viên nhận thông báo kết quả (phê duyệt/từ chối) và thông tin chi tiết về chi phí được phê duyệt qua Bitrix24

### 2.3 Cấu Trúc Dữ Liệu

#### **Entity:** Travel_Expense_Request
```yaml
Fields:
  - employee_name: Text (Required)
  - employee_id: List (Required)
  - department: List (Required)
  - travel_purpose: List (Required)
  - destination: Text (Required)
  - start_date: Date (Required)
  - end_date: Date (Required)
  - airfare: Number
  - hotel: Number
  - meals: Number
  - transportation: Number
  - other: Number
  - total: Number (Calculated)
  - status: List (Required)
  - rejection_reason: Text
  - approved_budget: Number
  - approver_level_1: List
  - approver_level_2: List
  - approver_level_3: List
  - approver_level_4: List
  - approved_date: Date
```

#### **Status Values:**
- Chờ phê duyệt cấp 1
- Chờ phê duyệt cấp 2
- Chờ phê duyệt cấp 3
- Chờ phê duyệt cấp 4
- Đã phê duyệt
- Từ chối
- Đã hủy

### 2.4 Automation Rules

#### **Rule 1: Khởi tạo quy trình**
- **Trigger:** Khi tạo record mới
- **Action:** Gửi thông báo đến Quản lý trực tiếp
- **Condition:** Status = "Chờ phê duyệt cấp 1"

#### **Rule 2: Phê duyệt cấp 1 → cấp 2**
- **Trigger:** Status = "Chờ phê duyệt cấp 2"
- **Action:** Gửi thông báo đến Trưởng phòng tài chính

#### **Rule 3: Phê duyệt cấp 2 → cấp 3**
- **Trigger:** Status = "Chờ phê duyệt cấp 3"
- **Action:** Gửi thông báo đến Phó giám đốc

#### **Rule 4: Phê duyệt cấp 3 → cấp 4**
- **Trigger:** Status = "Chờ phê duyệt cấp 4"
- **Action:** Gửi thông báo đến Giám đốc

#### **Rule 5: Thông báo kết quả phê duyệt**
- **Trigger:** Status = "Đã phê duyệt"
- **Action:** Gửi thông báo cho nhân viên kèm chi phí được phê duyệt

#### **Rule 6: Thông báo từ chối**
- **Trigger:** Status = "Từ chối"
- **Action:** Gửi thông báo cho nhân viên kèm lý do

---

## 3. HƯỚNG DẪN THIẾT LẬP VÀ CHẠY QUY TRÌNH

### 3.1 Chuẩn Bị Môi Trường Bitrix24

#### **Yêu Cầu Hệ Thống:**
- Bitrix24 với gói Professional hoặc Enterprise
- Quyền Administrator
- Module Smart Process Automation đã kích hoạt
- Email server được cấu hình

#### **Thiết Lập Cấu Trúc Tổ Chức:**
1. **Tạo Phòng Ban:**
   - Phòng Nhân sự
   - Phòng Tài chính
   - Phòng Kỹ thuật
   - Phòng Kinh doanh
   - Phòng Hành chính

2. **Tạo Vị Trí:**
   - Nhân viên
   - Quản lý trực tiếp
   - Trưởng phòng nhân sự
   - Trưởng phòng tài chính
   - Phó giám đốc
   - Giám đốc

3. **Tạo Nhân Viên Mẫu:**
   - Ít nhất 6 nhân viên cho test
   - Phân quyền theo vị trí

### 3.2 Import Quy Trình

#### **Import Quy Trình Nghỉ Phép:**
1. Vào **CRM** → **Smart Process Automation**
2. Click **Import Process**
3. Chọn file `NghiPhep_3Cap.bpt`
4. Click **Import**

#### **Import Quy Trình Chi Phí Công Tác:**
1. Vào **CRM** → **Smart Process Automation**
2. Click **Import Process**
3. Chọn file `ChiPhiCongTac_4Cap.bpt`
4. Click **Import**

### 3.3 Cấu Hình Sau Import

#### **Kiểm Tra Entity và Fields:**
1. Vào tab **Fields** của mỗi quy trình
2. Kiểm tra tất cả các trường đã được tạo
3. Cấu hình validation rules nếu cần

#### **Cấu Hình Lists:**
1. Vào tab **Lists** của mỗi quy trình
2. Thêm dữ liệu cho các danh sách:
   - Leave Type (cho quy trình nghỉ phép)
   - Travel Purpose (cho quy trình chi phí công tác)
   - Status
   - Department

#### **Thiết Lập Automation Rules:**
1. Vào tab **Automation** của mỗi quy trình
2. Kiểm tra các rules đã được tạo
3. Test từng rule để đảm bảo hoạt động đúng

### 3.4 Test Quy Trình

#### **Test Quy Trình Nghỉ Phép:**
1. Đăng nhập với tài khoản nhân viên
2. Tạo yêu cầu nghỉ phép mới
3. Kiểm tra thông báo gửi đến quản lý trực tiếp
4. Đăng nhập với tài khoản quản lý, phê duyệt
5. Kiểm tra chuyển đến cấp 2, 3
6. Kiểm tra thông báo cuối cùng

#### **Test Quy Trình Chi Phí Công Tác:**
1. Đăng nhập với tài khoản nhân viên
2. Tạo yêu cầu chi phí công tác mới
3. Kiểm tra thông báo qua 4 cấp phê duyệt
4. Kiểm tra email notifications

### 3.5 Cấu Hình Email Templates

#### **Tạo Email Templates:**
1. Vào **Settings** → **Notifications** → **Email Templates**
2. Tạo các template cho:
   - Thông báo chờ phê duyệt
   - Thông báo phê duyệt thành công
   - Thông báo từ chối

#### **Template Mẫu:**
```
Subject: [Bitrix24] Yêu cầu {process_type} cần phê duyệt - {employee_name}

Chào {approver_name},

{employee_name} đã gửi yêu cầu {process_type} với thông tin:
{request_details}

Vui lòng phê duyệt hoặc từ chối trong vòng {due_days} ngày làm việc.

[Link phê duyệt]

Trân trọng,
Hệ thống Bitrix24
```

### 3.6 Tạo Dashboard

#### **Tạo Dashboard:**
1. Vào **CRM** → **Reports**
2. Click **Create Dashboard**
3. Đặt tên: "Workflow Management"
4. Thêm các widgets:
   - Tổng quan yêu cầu nghỉ phép
   - Tổng quan chi phí công tác
   - Hiệu suất phê duyệt
   - Thống kê theo phòng ban

---

## 4. FILE NỘP BÀI

### 4.1 Workflow Files
1. **NghiPhep_3Cap.bpt** - File export quy trình nghỉ phép 3 cấp
2. **ChiPhiCongTac_4Cap.bpt** - File export quy trình chi phí công tác 4 cấp

### 4.2 Documentation
1. **Assignment-Submission.md** - Tài liệu mô tả chi tiết (file này)
2. **Deployment-Guide.pdf** - Hướng dẫn triển khai
3. **User-Manual.pdf** - Hướng dẫn sử dụng cho người dùng cuối

### 4.3 Cách Export Files
1. Vào **CRM** → **Smart Process Automation**
2. Chọn quy trình cần export
3. Click **Actions** → **Export**
4. Chọn định dạng **.bpt**
5. Đổi tên file theo yêu cầu
6. Lưu vào thư mục nộp bài

---

## 5. KẾT LUẬN

Dự án đã hoàn thành thành công hai quy trình workflow trên Bitrix24 theo đúng yêu cầu đề bài:

1. **Quy trình nghỉ phép 3 cấp phê duyệt** - Đạt 100% yêu cầu
2. **Quy trình chi phí công tác 4 cấp phê duyệt** - Đạt 100% yêu cầu

Cả hai quy trình đều:
- ✅ Hoạt động chính xác theo logic nghiệp vụ
- ✅ Có đầy đủ tính năng theo yêu cầu kỹ thuật
- ✅ Thân thiện với người dùng
- ✅ Có tài liệu hướng dẫn đầy đủ

**Dự án sẵn sàng nộp bài và đánh giá.**

---

*Tài liệu được tạo bởi: [Tên thí sinh]*  
*Ngày tạo: [Ngày hiện tại]*  
*Phiên bản: 1.0*
