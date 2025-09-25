# Hướng Dẫn Nhanh - Triển Khai Workflow Bitrix24

## Tóm Tắt Nhanh
Đây là hướng dẫn tóm tắt để triển khai nhanh hai quy trình workflow trên Bitrix24.

## Bước 1: Chuẩn Bị Bitrix24 (15 phút)

### 1.1 Kiểm Tra Quyền
- Đăng nhập với tài khoản Administrator
- Vào **Settings** → **Company** → **Users**
- Đảm bảo có quyền quản lý Business Process

### 1.2 Kích Hoạt Module
- Vào **Settings** → **Product Settings** → **Business Processes**
- Đảm bảo module đã được kích hoạtimage.png

### 1.3 Tạo Cấu Trúc Tổ Chức
```
Phòng Ban:
- Phòng Nhân sự
- Phòng Tài chính
- Phòng Kỹ thuật
- Phòng Kinh doanh
- Phòng Hành chính

Vị Trí:
- Nhân viên
- Quản lý trực tiếp
- Trưởng phòng nhân sự
- Trưởng phòng tài chính
- Phó giám đốc
- Giám đốc

Nhân Viên Mẫu (ít nhất 6 người):
- 1 Nhân viên
- 1 Quản lý trực tiếp
- 1 Trưởng phòng nhân sự
- 1 Trưởng phòng tài chính
- 1 Phó giám đốc
- 1 Giám đốc
```

## Bước 2: Tạo Quy Trình Nghỉ Phép (30 phút)

### 2.1 Tạo Smart Process
1. Vào **CRM** → **Smart Process Automation**
2. Click **Create Automation** → **Process** → **New Process**
3. Đặt tên: "Leave Request 3 Levels"

### 2.2 Tạo Entity
```yaml
Entity Name: Leave_Request
Fields:
  - employee_name: Text (Required)
  - employee_id: User (Required)
  - department: List (Required)
  - leave_type: List (Required)
  - start_date: Date (Required)
  - end_date: Date (Required)
  - total_days: Calculated Field
  - reason: Textarea (Required)
  - status: List (Required)
  - rejection_reason: Textarea
  - approver_level_1: User
  - approver_level_2: User
  - approver_level_3: User
  - approved_date: Date
```

### 2.3 Cấu Hình Lists
**Leave Type:**
- Nghỉ phép năm
- Nghỉ ốm
- Nghỉ không lương
- Nghỉ việc riêng
- Nghỉ thai sản
- Nghỉ khác

**Status:**
- Chờ phê duyệt cấp 1
- Chờ phê duyệt cấp 2
- Chờ phê duyệt cấp 3
- Đã phê duyệt
- Từ chối
- Đã hủy

### 2.4 Tạo Automation Rules
1. **Rule 1**: Record Created
   - Set status = "Chờ phê duyệt cấp 1"
   - Set approver_level_1 = GetDirectManager(employee_id)
   - Calculate total_days = end_date - start_date + 1
   - Send notification

2. **Rule 2**: Status = "Chờ phê duyệt cấp 2"
   - Set approver_level_2 = GetHRManager(department)
   - Create task for approver_level_2
   - Send notification

3. **Rule 3**: Status = "Chờ phê duyệt cấp 3"
   - Set approver_level_3 = GetCEO()
   - Create task for approver_level_3
   - Send notification

4. **Rule 4**: Status = "Đã phê duyệt" OR "Từ chối"
   - Send notification to employee

## Bước 3: Tạo Quy Trình Chi Phí Công Tác (45 phút)

### 3.1 Tạo Smart Process
1. Vào **CRM** → **Smart Process Automation**
2. Click **Create Automation** → **Process** → **New Process**
3. Đặt tên: "Travel Expense Request 4 Levels"

### 3.2 Tạo Entity
```yaml
Entity Name: Travel_Expense_Request
Fields:
  - employee_name: Text (Required)
  - employee_id: User (Required)
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
  - total: Calculated Field
  - status: List (Required)
  - rejection_reason: Textarea
  - approved_budget: Number
  - approver_level_1: User
  - approver_level_2: User
  - approver_level_3: User
  - approver_level_4: User
```

### 3.3 Cấu Hình Lists
**Travel Purpose:**
- Họp khách hàng
- Tham gia hội nghị
- Khảo sát thị trường
- Đào tạo nhân viên
- Nghiên cứu công nghệ
- Khác

**Status:**
- Chờ phê duyệt cấp 1
- Chờ phê duyệt cấp 2
- Chờ phê duyệt cấp 3
- Chờ phê duyệt cấp 4
- Đã phê duyệt
- Từ chối
- Đã hủy

### 3.4 Tạo Automation Rules
1. **Rule 1**: Record Created
   - Set status = "Chờ phê duyệt cấp 1"
   - Set approver_level_1 = GetDirectManager(employee_id)
   - Calculate total = airfare + hotel + meals + transportation + other
   - Send notification

2. **Rule 2**: Status = "Chờ phê duyệt cấp 2"
   - Set approver_level_2 = GetFinanceManager()
   - Create task for approver_level_2
   - Send notification

3. **Rule 3**: Status = "Chờ phê duyệt cấp 3"
   - Set approver_level_3 = GetDeputyDirector()
   - Create task for approver_level_3
   - Send notification

4. **Rule 4**: Status = "Chờ phê duyệt cấp 4"
   - Set approver_level_4 = GetCEO()
   - Create task for approver_level_4
   - Send notification

5. **Rule 5**: Status = "Đã phê duyệt" OR "Từ chối"
   - Send notification to employee

## Bước 4: Cấu Hình Email Templates (20 phút)

### 4.1 Tạo Templates
1. Vào **Settings** → **Notifications** → **Email Templates**
2. Tạo 3 templates chính:

**Template 1: Thông Báo Chờ Phê Duyệt**
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

**Template 2: Thông Báo Phê Duyệt**
```
Subject: [Bitrix24] Yêu cầu {process_type} đã được phê duyệt

Chào {employee_name},

Yêu cầu {process_type} của bạn đã được phê duyệt:
{approved_details}

Bạn có thể xem chi tiết tại: [Link]

Trân trọng,
{department_name}
```

**Template 3: Thông Báo Từ Chối**
```
Subject: [Bitrix24] Yêu cầu {process_type} bị từ chối

Chào {employee_name},

Yêu cầu {process_type} của bạn đã bị từ chối.

Lý do: {rejection_reason}

Vui lòng liên hệ với quản lý trực tiếp để được hỗ trợ.

Trân trọng,
{department_name}
```

## Bước 5: Test Quy Trình (30 phút)

### 5.1 Test Quy Trình Nghỉ Phép
1. Đăng nhập với tài khoản nhân viên
2. Tạo yêu cầu nghỉ phép
3. Kiểm tra thông báo gửi đến quản lý
4. Đăng nhập với tài khoản quản lý, phê duyệt
5. Kiểm tra chuyển đến cấp 2, 3
6. Kiểm tra thông báo cuối cùng

### 5.2 Test Quy Trình Chi Phí Công Tác
1. Đăng nhập với tài khoản nhân viên
2. Tạo yêu cầu chi phí công tác
3. Kiểm tra thông báo qua 4 cấp phê duyệt
4. Kiểm tra email notifications

## Bước 6: Xuất File (10 phút)

### 6.1 Xuất Quy Trình Nghỉ Phép
1. Vào **CRM** → **Smart Process Automation**
2. Click vào quy trình "Leave Request 3 Levels"
3. Click **Actions** → **Export**
4. Chọn định dạng **.bpt**
5. Đổi tên file thành: `NghiPhep_3Cap.bpt`

### 6.2 Xuất Quy Trình Chi Phí Công Tác
1. Vào **CRM** → **Smart Process Automation**
2. Click vào quy trình "Travel Expense Request 4 Levels"
3. Click **Actions** → **Export**
4. Chọn định dạng **.bpt**
5. Đổi tên file thành: `ChiPhiCongTac_4Cap.bpt`

## Bước 7: Tạo Dashboard (15 phút)

### 7.1 Tạo Dashboard
1. Vào **CRM** → **Reports**
2. Click **Create Dashboard**
3. Đặt tên: "Workflow Management"

### 7.2 Thêm Widgets
1. **Tổng quan yêu cầu nghỉ phép**
2. **Tổng quan chi phí công tác**
3. **Hiệu suất phê duyệt**
4. **Thống kê theo phòng ban**

## Checklist Hoàn Thành

### Quy Trình Nghỉ Phép
- [ ] Smart Process đã tạo
- [ ] Entity và fields đã cấu hình
- [ ] Lists đã thiết lập
- [ ] Automation rules đã tạo
- [ ] Email templates đã cấu hình
- [ ] Test thành công
- [ ] File .bpt đã export

### Quy Trình Chi Phí Công Tác
- [ ] Smart Process đã tạo
- [ ] Entity và fields đã cấu hình
- [ ] Lists đã thiết lập
- [ ] Automation rules đã tạo
- [ ] Email templates đã cấu hình
- [ ] Test thành công
- [ ] File .bpt đã export

### Tổng Quan
- [ ] Cấu trúc tổ chức đã tạo
- [ ] Quyền truy cập đã cấu hình
- [ ] Dashboard đã tạo
- [ ] Documentation đã hoàn thành
- [ ] File nộp bài đã sẵn sàng

## File Nộp Bài Cuối Cùng

```
Submission_Folder/
├── NghiPhep_3Cap.bpt
├── ChiPhiCongTac_4Cap.bpt
├── Deployment-Guide.pdf
├── User-Manual.pdf
└── README.txt
```

## Lưu Ý Quan Trọng

1. **Test kỹ lưỡng** trước khi export
2. **Backup dữ liệu** trước khi thay đổi
3. **Kiểm tra email** notifications
4. **Verify permissions** cho tất cả users
5. **Document** tất cả thay đổi

## Thời Gian Ước Tính

- **Chuẩn bị môi trường**: 15 phút
- **Tạo quy trình nghỉ phép**: 30 phút
- **Tạo quy trình chi phí công tác**: 45 phút
- **Cấu hình email**: 20 phút
- **Testing**: 30 phút
- **Xuất file**: 10 phút
- **Tạo dashboard**: 15 phút

**Tổng thời gian**: ~2.5 giờ

## Support

Nếu gặp vấn đề, tham khảo:
- [Hướng dẫn chi tiết](bitrix24-workflow-guide.md)
- [Hướng dẫn triển khai](deployment-guide.md)
- [Thiết kế quy trình nghỉ phép](../workflows/nghi-phep-3-cap.md)
- [Thiết kế quy trình chi phí công tác](../workflows/chi-phi-cong-tac-4-cap.md)

---

**Chúc bạn thành công!** 🚀
