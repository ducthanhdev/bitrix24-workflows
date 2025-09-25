# Hướng Dẫn Triển Khai Workflow Bitrix24

## Tài Liệu Nộp Bài

### Danh Sách File Nộp Bài
1. `NghiPhep_3Cap.bpt` - File export quy trình nghỉ phép 3 cấp
2. `ChiPhiCongTac_4Cap.bpt` - File export quy trình chi phí công tác 4 cấp
3. `Deployment-Guide.pdf` - Tài liệu hướng dẫn triển khai
4. `User-Manual.pdf` - Hướng dẫn sử dụng cho người dùng cuối

## Hướng Dẫn Triển Khai Chi Tiết

### Bước 1: Chuẩn Bị Môi Trường Bitrix24

#### 1.1 Yêu Cầu Hệ Thống
- Bitrix24 với gói Professional hoặc Enterprise
- Quyền Administrator
- Module Business Process đã kích hoạt
- Email server được cấu hình

#### 1.2 Thiết Lập Cấu Trúc Tổ Chức

##### Tạo Phòng Ban
1. Vào **Settings** → **Company** → **Company Structure**
2. Click **Add Department**
3. Tạo các phòng ban:
   ```
   - Phòng Nhân sự
   - Phòng Tài chính
   - Phòng Kỹ thuật
   - Phòng Kinh doanh
   - Phòng Hành chính
   ```

##### Tạo Vị Trí
1. Vào **Settings** → **Company** → **Positions**
2. Click **Add Position**
3. Tạo các vị trí:
   ```
   - Nhân viên
   - Quản lý trực tiếp
   - Trưởng phòng nhân sự
   - Trưởng phòng tài chính
   - Phó giám đốc
   - Giám đốc
   ```

##### Tạo Nhân Viên Mẫu
1. Vào **Settings** → **Company** → **Users**
2. Click **Add User**
3. Tạo ít nhất 6 nhân viên cho test:
   ```
   - 1 Nhân viên (test user)
   - 1 Quản lý trực tiếp
   - 1 Trưởng phòng nhân sự
   - 1 Trưởng phòng tài chính
   - 1 Phó giám đốc
   - 1 Giám đốc
   ```

### Bước 2: Import Quy Trình Nghỉ Phép

#### 2.1 Import File Workflow
1. Vào **CRM** → **Smart Process Automation**
2. Click **Import Process**
3. Chọn file `NghiPhep_3Cap.bpt`
4. Click **Import**

#### 2.2 Cấu Hình Quy Trình
1. Sau khi import, click vào quy trình vừa tạo
2. Vào tab **Settings**
3. Cấu hình các thông số:

##### Cấu Hình Entity
```yaml
Entity Name: Leave_Request
Entity Code: leave_request
Table Name: c_leave_request
```

##### Cấu Hình Fields
1. Vào tab **Fields**
2. Kiểm tra và cấu hình các trường:
   ```
   - employee_name: Text (Required)
   - employee_id: User (Required)
   - department: List (Required)
   - leave_type: List (Required)
   - start_date: Date (Required)
   - end_date: Date (Required)
   - total_days: Calculated
   - reason: Textarea (Required)
   - status: List (Required)
   - rejection_reason: Textarea
   ```

##### Cấu Hình Lists
1. Vào tab **Lists**
2. Cấu hình các danh sách:

**Leave Type:**
```
- Nghỉ phép năm
- Nghỉ ốm
- Nghỉ không lương
- Nghỉ việc riêng
- Nghỉ thai sản
- Nghỉ khác
```

**Status:**
```
- Chờ phê duyệt cấp 1
- Chờ phê duyệt cấp 2
- Chờ phê duyệt cấp 3
- Đã phê duyệt
- Từ chối
- Đã hủy
```

**Department:**
```
- Phòng Nhân sự
- Phòng Tài chính
- Phòng Kỹ thuật
- Phòng Kinh doanh
- Phòng Hành chính
```

#### 2.3 Cấu Hình Automation Rules
1. Vào tab **Automation**
2. Cấu hình các rule:

##### Rule 1: Khởi Tạo Quy Trình
```yaml
Trigger: Record Created
Actions:
  - Set field "status" = "Chờ phê duyệt cấp 1"
  - Set field "approver_level_1" = GetDirectManager(employee_id)
  - Calculate "total_days" = end_date - start_date + 1
  - Send notification to approver_level_1
```

##### Rule 2: Phê Duyệt Cấp 1
```yaml
Trigger: Field "status" changed to "Chờ phê duyệt cấp 1"
Actions:
  - Create task for approver_level_1
  - Send email notification
  - Set due date = 2 business days
```

##### Rule 3: Chuyển Cấp 2
```yaml
Trigger: Field "status" changed to "Chờ phê duyệt cấp 2"
Actions:
  - Set approver_level_2 = GetHRManager(department)
  - Create task for approver_level_2
  - Send notification
```

##### Rule 4: Chuyển Cấp 3
```yaml
Trigger: Field "status" changed to "Chờ phê duyệt cấp 3"
Actions:
  - Set approver_level_3 = GetCEO()
  - Create task for approver_level_3
  - Send notification
```

##### Rule 5: Thông Báo Kết Quả
```yaml
Trigger: Field "status" changed to "Đã phê duyệt" OR "Từ chối"
Actions:
  - Send email notification to employee
  - Update leave balance (if approved)
```

### Bước 3: Import Quy Trình Chi Phí Công Tác

#### 3.1 Import File Workflow
1. Vào **CRM** → **Smart Process Automation**
2. Click **Import Process**
3. Chọn file `ChiPhiCongTac_4Cap.bpt`
4. Click **Import**

#### 3.2 Cấu Hình Quy Trình
Tương tự như quy trình nghỉ phép, cấu hình các thông số:

##### Cấu Hình Entity
```yaml
Entity Name: Travel_Expense_Request
Entity Code: travel_expense_request
Table Name: c_travel_expense_request
```

##### Cấu Hình Fields
```
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
- total: Calculated
- status: List (Required)
- rejection_reason: Textarea
- approved_budget: Number
```

##### Cấu Hình Automation Rules
1. **Rule 1**: Khởi tạo quy trình
2. **Rule 2**: Phê duyệt cấp 1 (Quản lý trực tiếp)
3. **Rule 3**: Phê duyệt cấp 2 (Trưởng phòng tài chính)
4. **Rule 4**: Phê duyệt cấp 3 (Phó giám đốc)
5. **Rule 5**: Phê duyệt cấp 4 (Giám đốc)
6. **Rule 6**: Thông báo kết quả

### Bước 4: Cấu Hình Email Templates

#### 4.1 Tạo Email Templates
1. Vào **Settings** → **Notifications**
2. Click **Email Templates**
3. Tạo các template:

##### Template 1: Thông Báo Chờ Phê Duyệt
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

##### Template 2: Thông Báo Phê Duyệt Thành Công
```
Subject: [Bitrix24] Yêu cầu {process_type} đã được phê duyệt

Chào {employee_name},

Yêu cầu {process_type} của bạn đã được phê duyệt:
{approved_details}

Bạn có thể xem chi tiết tại: [Link]

Trân trọng,
{department_name}
```

##### Template 3: Thông Báo Từ Chối
```
Subject: [Bitrix24] Yêu cầu {process_type} bị từ chối

Chào {employee_name},

Yêu cầu {process_type} của bạn đã bị từ chối.

Lý do: {rejection_reason}

Vui lòng liên hệ với quản lý trực tiếp để được hỗ trợ.

Trân trọng,
{department_name}
```

#### 4.2 Cấu Hình Notification Rules
1. Vào **Settings** → **Notifications** → **Rules**
2. Tạo rules cho từng loại thông báo
3. Liên kết với email templates
4. Test thông báo

### Bước 5: Tạo Dashboard và Báo Cáo

#### 5.1 Tạo Dashboard
1. Vào **CRM** → **Reports**
2. Click **Create Dashboard**
3. Tạo dashboard "Workflow Management"

##### Widgets cho Dashboard
1. **Tổng quan yêu cầu nghỉ phép**
2. **Tổng quan chi phí công tác**
3. **Hiệu suất phê duyệt**
4. **Thống kê theo phòng ban**

#### 5.2 Tạo Reports
1. **Báo cáo nghỉ phép hàng tháng**
2. **Báo cáo chi phí công tác**
3. **Báo cáo hiệu suất workflow**

### Bước 6: Cấu Hình Quyền Truy Cập

#### 6.1 Tạo Roles
1. Vào **Settings** → **Access Rights**
2. Tạo các roles:
   ```
   - Employee
   - Direct Manager
   - HR Manager
   - Finance Manager
   - Deputy Director
   - CEO
   - Admin
   ```

#### 6.2 Phân Quyền
1. Cấu hình quyền cho từng role
2. Test quyền truy cập
3. Đảm bảo bảo mật

### Bước 7: Testing và Validation

#### 7.1 Test Cases
1. **Test Case 1**: Quy trình phê duyệt thành công
2. **Test Case 2**: Quy trình từ chối
3. **Test Case 3**: Validation errors
4. **Test Case 4**: Email notifications
5. **Test Case 5**: Dashboard và reports

#### 7.2 User Acceptance Testing
1. Test với người dùng thực tế
2. Thu thập feedback
3. Điều chỉnh nếu cần

### Bước 8: Training và Documentation

#### 8.1 Tạo User Manual
1. Hướng dẫn tạo yêu cầu
2. Hướng dẫn phê duyệt
3. Hướng dẫn xem báo cáo
4. FAQ

#### 8.2 Training
1. Training cho admin
2. Training cho người dùng cuối
3. Training cho quản lý

### Bước 9: Go-Live và Support

#### 9.1 Go-Live
1. Backup dữ liệu
2. Deploy production
3. Monitor system
4. Support users

#### 9.2 Post-Go-Live
1. Monitor performance
2. Collect feedback
3. Optimize workflow
4. Regular maintenance

## Troubleshooting

### Lỗi Thường Gặp

#### 1. Import Failed
**Nguyên nhân**: File format không đúng hoặc thiếu quyền
**Giải pháp**:
- Kiểm tra định dạng file (.bpt)
- Đảm bảo có quyền admin
- Kiểm tra log system

#### 2. Email Not Sending
**Nguyên nhân**: Email server chưa cấu hình
**Giải pháp**:
- Cấu hình SMTP server
- Test email connection
- Kiểm tra spam folder

#### 3. Workflow Not Triggering
**Nguyên nhân**: Automation rules chưa được kích hoạt
**Giải pháp**:
- Kiểm tra automation rules
- Đảm bảo triggers đúng
- Test manual trigger

#### 4. Permission Denied
**Nguyên nhân**: Quyền truy cập chưa được cấu hình
**Giải pháp**:
- Kiểm tra access rights
- Cấu hình roles
- Test với user khác

## Maintenance

### Backup Strategy
- Daily backup của workflow configuration
- Weekly backup của data
- Monthly backup của system

### Monitoring
- Monitor workflow performance
- Track error rates
- User activity monitoring

### Updates
- Regular security updates
- Feature enhancements
- Bug fixes

## Support và Contact

### Technical Support
- Email: support@company.com
- Phone: +84-xxx-xxx-xxx
- Working hours: 9:00-17:00 (GMT+7)

### Documentation
- User Manual: [Link]
- API Documentation: [Link]
- Video Tutorials: [Link]

## Kết Luận

Với hướng dẫn này, bạn có thể triển khai thành công hai quy trình workflow trên Bitrix24. Đảm bảo test kỹ lưỡng trước khi go-live và có kế hoạch support cho người dùng.
