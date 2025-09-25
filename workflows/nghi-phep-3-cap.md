# Quy Trình Nghỉ Phép - 3 Cấp Phê Duyệt

## Mô Tả Tổng Quan
Quy trình cho phép nhân viên gửi yêu cầu nghỉ phép, được xét duyệt qua 3 cấp quản lý trước khi được phê duyệt hoặc từ chối.

## Cấu Trúc Phê Duyệt
```
Nhân viên → Quản lý trực tiếp (Cấp 1) → Trưởng phòng nhân sự (Cấp 2) → Giám đốc (Cấp 3)
```

## Chi Tiết Thiết Kế

### 1. Cấu Trúc Dữ Liệu (Smart Process)

#### Entity: Leave_Request
```json
{
  "id": "auto_increment",
  "employee_name": "Text (Required)",
  "employee_id": "User (Required)",
  "department": "List (Required)",
  "leave_type": "List (Required)",
  "start_date": "Date (Required)",
  "end_date": "Date (Required)",
  "total_days": "Calculated Field",
  "reason": "Textarea (Required)",
  "status": "List (Required)",
  "rejection_reason": "Textarea",
  "approver_level_1": "User",
  "approver_level_2": "User", 
  "approver_level_3": "User",
  "approved_date": "Date",
  "created_date": "Date (Auto)",
  "modified_date": "Date (Auto)"
}
```

#### Danh Sách Giá Trị (Lists)

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

**Department:**
- Phòng Nhân sự
- Phòng Tài chính
- Phòng Kỹ thuật
- Phòng Kinh doanh
- Phòng Hành chính

### 2. Workflow Logic

#### Trigger: Record Created
```yaml
Event: OnCreate
Action: 
  - Set status = "Chờ phê duyệt cấp 1"
  - Set approver_level_1 = GetDirectManager(employee_id)
  - Calculate total_days = end_date - start_date + 1
  - Send notification to approver_level_1
```

#### Bước 1: Phê Duyệt Cấp 1 (Quản lý trực tiếp)
```yaml
Condition: status == "Chờ phê duyệt cấp 1"
Actions:
  - Create task for approver_level_1
  - Send email notification
  - Set due date = 2 business days

Decision Points:
  - Approve: 
    - Set status = "Chờ phê duyệt cấp 2"
    - Set approver_level_2 = GetHRManager(department)
    - Send notification to approver_level_2
  - Reject:
    - Set status = "Từ chối"
    - Set rejection_reason = user_input
    - Send notification to employee
  - Request Info:
    - Send message to employee
    - Keep status unchanged
```

#### Bước 2: Phê Duyệt Cấp 2 (Trưởng phòng nhân sự)
```yaml
Condition: status == "Chờ phê duyệt cấp 2"
Validation:
  - Check remaining leave balance
  - Validate leave type eligibility
  - Check for conflicts

Actions:
  - Create task for approver_level_2
  - Send email notification
  - Set due date = 1 business day

Decision Points:
  - Approve:
    - Set status = "Chờ phê duyệt cấp 3"
    - Set approver_level_3 = GetCEO()
    - Send notification to approver_level_3
  - Reject:
    - Set status = "Từ chối"
    - Set rejection_reason = user_input
    - Send notification to employee
```

#### Bước 3: Phê Duyệt Cấp 3 (Giám đốc)
```yaml
Condition: status == "Chờ phê duyệt cấp 3"
Actions:
  - Create task for approver_level_3
  - Send email notification
  - Set due date = 1 business day

Decision Points:
  - Approve:
    - Set status = "Đã phê duyệt"
    - Set approved_date = current_date
    - Update employee leave balance
    - Send approval notification to employee
  - Reject:
    - Set status = "Từ chối"
    - Set rejection_reason = user_input
    - Send rejection notification to employee
```

### 3. Validation Rules

#### Kiểm Tra Số Ngày Nghỉ
```javascript
function validateLeaveDays(employeeId, leaveType, totalDays) {
    const remainingBalance = getRemainingLeaveBalance(employeeId, leaveType);
    if (totalDays > remainingBalance) {
        return {
            valid: false,
            message: `Số ngày nghỉ yêu cầu (${totalDays}) vượt quá số ngày còn lại (${remainingBalance})`
        };
    }
    return { valid: true };
}
```

#### Kiểm Tra Xung Đột Lịch
```javascript
function checkScheduleConflict(employeeId, startDate, endDate) {
    const existingLeaves = getEmployeeLeaves(employeeId, startDate, endDate);
    if (existingLeaves.length > 0) {
        return {
            valid: false,
            message: "Có xung đột với lịch nghỉ phép đã được phê duyệt"
        };
    }
    return { valid: true };
}
```

### 4. Email Templates

#### Template 1: Thông Báo Chờ Phê Duyệt Cấp 1
```
Subject: [Bitrix24] Yêu cầu nghỉ phép cần phê duyệt - {employee_name}

Chào {approver_name},

Nhân viên {employee_name} đã gửi yêu cầu nghỉ phép với thông tin sau:
- Loại nghỉ: {leave_type}
- Từ ngày: {start_date}
- Đến ngày: {end_date}
- Số ngày: {total_days}
- Lý do: {reason}

Vui lòng phê duyệt hoặc từ chối yêu cầu này trong vòng 2 ngày làm việc.

[Link phê duyệt]

Trân trọng,
Hệ thống Bitrix24
```

#### Template 2: Thông Báo Phê Duyệt Thành Công
```
Subject: [Bitrix24] Yêu cầu nghỉ phép đã được phê duyệt

Chào {employee_name},

Yêu cầu nghỉ phép của bạn đã được phê duyệt với thông tin:
- Loại nghỉ: {leave_type}
- Từ ngày: {start_date}
- Đến ngày: {end_date}
- Số ngày: {total_days}

Bạn có thể xem chi tiết tại: [Link]

Trân trọng,
Phòng Nhân sự
```

#### Template 3: Thông Báo Từ Chối
```
Subject: [Bitrix24] Yêu cầu nghỉ phép bị từ chối

Chào {employee_name},

Yêu cầu nghỉ phép của bạn đã bị từ chối.

Lý do: {rejection_reason}

Vui lòng liên hệ với quản lý trực tiếp để được hỗ trợ thêm.

Trân trọng,
Phòng Nhân sự
```

### 5. Dashboard và Báo Cáo

#### Dashboard Widgets
1. **Tổng quan yêu cầu nghỉ phép**
   - Tổng số yêu cầu trong tháng
   - Số yêu cầu chờ phê duyệt
   - Số yêu cầu đã phê duyệt
   - Số yêu cầu bị từ chối

2. **Thống kê theo phòng ban**
   - Biểu đồ yêu cầu nghỉ phép theo phòng ban
   - Top 5 phòng ban có nhiều yêu cầu nghỉ phép nhất

3. **Hiệu suất phê duyệt**
   - Thời gian trung bình phê duyệt
   - Số lượng yêu cầu phê duyệt trong ngày

#### Báo Cáo
1. **Báo cáo nghỉ phép hàng tháng**
2. **Báo cáo tổng hợp theo nhân viên**
3. **Báo cáo hiệu suất phê duyệt**

### 6. Cấu Hình Quyền Truy Cập

#### Roles và Permissions
```yaml
Employee:
  - Create leave request
  - View own requests
  - Cancel pending requests

Direct Manager:
  - Approve/Reject level 1
  - View team requests
  - Request additional info

HR Manager:
  - Approve/Reject level 2
  - View all requests
  - Manage leave balances
  - Generate reports

CEO:
  - Approve/Reject level 3
  - View all requests
  - Override decisions
  - System configuration

Admin:
  - Full access
  - Configure workflow
  - Manage templates
  - System maintenance
```

### 7. Hướng Dẫn Triển Khai

#### Bước 1: Chuẩn Bị Dữ Liệu
1. Import danh sách nhân viên
2. Thiết lập cấu trúc tổ chức
3. Cấu hình quyền truy cập

#### Bước 2: Tạo Smart Process
1. Tạo entity "Leave_Request"
2. Định nghĩa các trường dữ liệu
3. Thiết lập validation rules

#### Bước 3: Thiết Kế Workflow
1. Tạo automation rules
2. Cấu hình điều kiện
3. Thiết lập actions

#### Bước 4: Cấu Hình Thông Báo
1. Tạo email templates
2. Thiết lập notification rules
3. Test thông báo

#### Bước 5: Tạo Dashboard
1. Thiết kế widgets
2. Cấu hình báo cáo
3. Setup alerts

### 8. Testing Scenarios

#### Test Case 1: Quy Trình Phê Duyệt Thành Công
1. Nhân viên tạo yêu cầu nghỉ phép
2. Quản lý trực tiếp phê duyệt
3. Trưởng phòng nhân sự phê duyệt
4. Giám đốc phê duyệt
5. Kiểm tra thông báo cuối cùng

#### Test Case 2: Quy Trình Từ Chối
1. Nhân viên tạo yêu cầu nghỉ phép
2. Quản lý trực tiếp từ chối
3. Kiểm tra thông báo từ chối

#### Test Case 3: Validation Errors
1. Test yêu cầu vượt quá số ngày còn lại
2. Test yêu cầu có xung đột lịch
3. Test dữ liệu không hợp lệ

### 9. Maintenance và Support

#### Backup Strategy
- Backup workflow configuration daily
- Backup data weekly
- Test restore procedures monthly

#### Monitoring
- Monitor workflow performance
- Track error rates
- User feedback collection

#### Updates
- Regular workflow optimization
- Feature enhancements
- Security updates

## Kết Luận

Quy trình nghỉ phép 3 cấp phê duyệt được thiết kế để:
- Đảm bảo tính chính xác và minh bạch
- Tăng hiệu suất xử lý yêu cầu
- Cung cấp audit trail đầy đủ
- Hỗ trợ báo cáo và phân tích

Quy trình này có thể được tùy chỉnh theo nhu cầu cụ thể của từng tổ chức.
