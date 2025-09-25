# Hướng Dẫn Export Workflow Bitrix24

## Tổng Quan
Hướng dẫn chi tiết để export hai quy trình workflow từ Bitrix24 ra file .bpt để nộp bài thi.

## Chuẩn Bị Trước Khi Export

### 1. Kiểm Tra Quy Trình Hoàn Chỉnh
Trước khi export, đảm bảo các quy trình đã hoàn thiện:

#### Quy Trình Nghỉ Phép 3 Cấp:
- [ ] Smart Process đã tạo với tên "Leave Request 3 Levels"
- [ ] Entity "Leave_Request" với đầy đủ fields
- [ ] Lists (Leave Type, Status, Department) đã cấu hình
- [ ] Automation rules cho 3 cấp phê duyệt
- [ ] Email templates đã thiết lập
- [ ] Test thành công với dữ liệu mẫu

#### Quy Trình Chi Phí Công Tác 4 Cấp:
- [ ] Smart Process đã tạo với tên "Travel Expense Request 4 Levels"
- [ ] Entity "Travel_Expense_Request" với đầy đủ fields
- [ ] Lists (Travel Purpose, Status, Department) đã cấu hình
- [ ] Automation rules cho 4 cấp phê duyệt
- [ ] Email templates đã thiết lập
- [ ] Test thành công với dữ liệu mẫu

### 2. Backup Dữ Liệu
- [ ] Backup toàn bộ cấu hình workflow
- [ ] Export dữ liệu test (nếu cần)
- [ ] Chụp màn hình các cấu hình quan trọng

## Bước 1: Export Quy Trình Nghỉ Phép

### 1.1 Truy Cập Smart Process
1. Đăng nhập Bitrix24 với quyền Administrator
2. Vào **CRM** → **Smart Process Automation**
3. Tìm và click vào quy trình "Leave Request 3 Levels"

### 1.2 Export Workflow
1. Trong giao diện quy trình, click **Actions** (hoặc menu 3 chấm)
2. Chọn **Export** từ danh sách
3. Chọn định dạng **.bpt** (Bitrix Process Template)
4. Đợi quá trình export hoàn tất

### 1.3 Lưu File
1. File sẽ được tải xuống với tên mặc định
2. Đổi tên file thành: `NghiPhep_3Cap.bpt`
3. Lưu vào thư mục nộp bài

## Bước 2: Export Quy Trình Chi Phí Công Tác

### 2.1 Truy Cập Smart Process
1. Trong **CRM** → **Smart Process Automation**
2. Tìm và click vào quy trình "Travel Expense Request 4 Levels"

### 2.2 Export Workflow
1. Click **Actions** → **Export**
2. Chọn định dạng **.bpt**
3. Đợi quá trình export hoàn tất

### 2.3 Lưu File
1. Đổi tên file thành: `ChiPhiCongTac_4Cap.bpt`
2. Lưu vào thư mục nộp bài

## Bước 3: Tạo Tài Liệu Mô Tả

### 3.1 Tạo File Deployment Guide
Tạo file `Deployment-Guide.pdf` với nội dung:

```markdown
# Hướng Dẫn Triển Khai Workflow Bitrix24

## Quy Trình 1: Nghỉ Phép 3 Cấp Phê Duyệt

### Cài Đặt
1. Import file NghiPhep_3Cap.bpt vào Bitrix24
2. Cấu hình cấu trúc tổ chức
3. Thiết lập quyền truy cập
4. Test quy trình

### Sử Dụng
1. Nhân viên tạo yêu cầu nghỉ phép
2. Quản lý trực tiếp phê duyệt cấp 1
3. Trưởng phòng nhân sự phê duyệt cấp 2
4. Giám đốc phê duyệt cấp 3
5. Nhân viên nhận thông báo kết quả

## Quy Trình 2: Chi Phí Công Tác 4 Cấp Phê Duyệt

### Cài Đặt
1. Import file ChiPhiCongTac_4Cap.bpt vào Bitrix24
2. Cấu hình ngân sách phòng ban
3. Thiết lập quyền truy cập
4. Test quy trình

### Sử Dụng
1. Nhân viên tạo yêu cầu chi phí công tác
2. Quản lý trực tiếp phê duyệt cấp 1
3. Trưởng phòng tài chính phê duyệt cấp 2
4. Phó giám đốc phê duyệt cấp 3
5. Giám đốc phê duyệt cấp 4
6. Nhân viên nhận thông báo và giấy ủy quyền

## Lưu Ý Quan Trọng
- Đảm bảo có đủ quyền Administrator để import
- Test kỹ lưỡng trước khi đưa vào sử dụng
- Backup dữ liệu trước khi thay đổi
```

### 3.2 Tạo File User Manual
Tạo file `User-Manual.pdf` với hướng dẫn sử dụng chi tiết cho người dùng cuối.

### 3.3 Tạo File README
Tạo file `README.txt`:

```text
BITRIX24 WORKFLOW SUBMISSION
=============================

Files Included:
- NghiPhep_3Cap.bpt: Quy trình nghỉ phép 3 cấp phê duyệt
- ChiPhiCongTac_4Cap.bpt: Quy trình chi phí công tác 4 cấp phê duyệt
- Deployment-Guide.pdf: Hướng dẫn triển khai
- User-Manual.pdf: Hướng dẫn sử dụng

System Requirements:
- Bitrix24 Professional plan or higher
- Administrator access rights
- Modern web browser

Installation:
1. Import .bpt files into Bitrix24
2. Follow Deployment-Guide.pdf for setup
3. Test with sample data

Support:
- Refer to User-Manual.pdf for usage
- Contact: [Your Contact Information]

Created by: [Your Name]
Date: [Current Date]
Version: 1.0
```

## Bước 4: Kiểm Tra File Export

### 4.1 Kiểm Tra File .bpt
- [ ] File NghiPhep_3Cap.bpt có kích thước > 0 KB
- [ ] File ChiPhiCongTac_4Cap.bpt có kích thước > 0 KB
- [ ] Cả hai file có thể mở được bằng text editor

### 4.2 Kiểm Tra Nội Dung
- [ ] File chứa đầy đủ cấu hình workflow
- [ ] Không có lỗi syntax trong file
- [ ] Tất cả automation rules được export

### 4.3 Test Import (Tùy Chọn)
Nếu có môi trường test:
- [ ] Import file .bpt vào Bitrix24 test
- [ ] Kiểm tra quy trình hoạt động đúng
- [ ] Test với dữ liệu mẫu

## Bước 5: Chuẩn Bị Nộp Bài

### 5.1 Tạo Thư Mục Nộp Bài
```
Submission_Folder/
├── NghiPhep_3Cap.bpt
├── ChiPhiCongTac_4Cap.bpt
├── Deployment-Guide.pdf
├── User-Manual.pdf
└── README.txt
```

### 5.2 Nén File
1. Tạo file ZIP chứa tất cả files
2. Đặt tên: `Bitrix24_Workflow_Submission_[YourName].zip`
3. Kiểm tra file ZIP có thể giải nén được

### 5.3 Kiểm Tra Cuối Cùng
- [ ] Tất cả files cần thiết đã có
- [ ] File names đúng theo yêu cầu
- [ ] Nội dung files đầy đủ và chính xác
- [ ] File ZIP không bị lỗi

## Lưu Ý Quan Trọng

### Trước Khi Export
1. **Test kỹ lưỡng**: Đảm bảo quy trình hoạt động đúng
2. **Backup dữ liệu**: Lưu backup trước khi export
3. **Kiểm tra quyền**: Đảm bảo có quyền export
4. **Dọn dẹp**: Xóa dữ liệu test không cần thiết

### Trong Khi Export
1. **Không gián đoạn**: Không đóng browser trong quá trình export
2. **Kiên nhẫn**: Quá trình export có thể mất vài phút
3. **Kiểm tra file**: Đảm bảo file được tải xuống thành công

### Sau Khi Export
1. **Kiểm tra file**: Mở file để đảm bảo không bị lỗi
2. **Đặt tên đúng**: Đổi tên file theo yêu cầu
3. **Lưu trữ an toàn**: Backup file ở nhiều nơi

## Troubleshooting

### Lỗi Export
- **Không có quyền**: Liên hệ Administrator
- **File quá lớn**: Tối ưu hóa quy trình
- **Lỗi network**: Thử lại khi mạng ổn định

### Lỗi Import
- **File bị hỏng**: Export lại từ Bitrix24
- **Không tương thích**: Kiểm tra version Bitrix24
- **Thiếu quyền**: Đảm bảo có quyền Administrator

## Liên Hệ Hỗ Trợ

Nếu gặp vấn đề trong quá trình export:
1. Kiểm tra hướng dẫn này
2. Tham khảo tài liệu Bitrix24
3. Liên hệ support Bitrix24
4. Hỏi giảng viên hoặc bạn học

---

**Chúc bạn thành công trong việc export và nộp bài!** 🚀