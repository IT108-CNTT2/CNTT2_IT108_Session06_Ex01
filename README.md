### 1\. Bản phân tích luồng IPO

* Input (Dữ liệu đầu vào):  
  * Thư mục Home hiện tại (`~`).  
  * Tệp mẫu `template.ps1` nằm ở thư mục công cộng (`Public`).  
  * Thông tin cấu hình mật khẩu cảm biến và các yêu cầu cấu trúc thư mục (`bin`, `config`, `data`).  
* Process (Xử lý logic trên PowerShell):  
  * *Bước 1:* Sử dụng lệnh tạo thư mục với cờ hỗ trợ tạo thư mục cha/con đồng thời (`New-Item` với tham số `-ItemType Directory`).  
  * *Bước 2:* Tạo tệp cấu hình ẩn bằng cách đặt dấu chấm đầu tên (`.env`), hiểu rõ cơ chế ẩn file của hệ thống.  
  * *Bước 3:* Sao chép tệp mẫu từ nguồn công cộng sang thư mục đích đồng thời đổi tên tệp trong một lệnh duy nhất (`Copy-Item`).  
  * *Bước 4:* Kiểm tra cấu trúc phân cấp toàn bộ thư mục bằng lệnh liệt kê đệ quy hoặc hiển thị chi tiết (`Get-ChildItem -Recurse`).  
  * *Xử lý ràng buộc (Lỗi quyền):* Sử dụng cơ chế khởi chạy lại PowerShell dưới quyền Administrator (`Start-Process powershell -Verb RunAs`) nếu gặp lỗi `Access to the path is denied`.  
* Output (Đầu ra):  
  * Cấu trúc thư mục hoàn chỉnh tại `~/smart-farm` gồm 3 thư mục con (`bin`, `config`, `data`).  
  * Tệp ẩn `.env` chứa mật khẩu được đặt đúng vị trí trong `config`.  
  * Tệp điều khiển `pump_control.ps1` nằm sẵn sàng trong `bin`.

### 2\. Nhật ký câu lệnh PowerShell

#### Bước 1: Tạo thư mục `smart-farm` chứa `bin`, `config`, `data` bằng 1 dòng lệnh

New-Item \-ItemType Directory \-Path "\$HOME/smart-farm/bin", "\$HOME/smart-farm/config", "\$HOME/smart-farm/data"

#### Bước 2: Tạo file ẩn `.env` trong `config` và giải thích

Set-Content \-Path "\$HOME/smart-farm/config/.env" \-Value "SENSOR\_PASSWORD=matkhaucambien123"  
\# Đặt thuộc tính ẩn (Hidden) cho file trong hệ thống Windows PowerShell  
(Get-Item "\$HOME/smart-farm/config/.env").Attributes \+= 'Hidden'

* *Giải thích tại sao `Get-ChildItem` thông thường không nhìn thấy file `.env`:* Trên hệ điều hành Windows, các tệp bắt đầu bằng dấu chấm (`.`) hoặc có thuộc tính `Hidden` được xem là tệp ẩn hệ thống. Lệnh `Get-ChildItem` (hoặc `ls`/`dir`) mặc định chỉ hiển thị các tệp hiển thị thông thường, trừ khi bạn chỉ định thêm tham số `-Force` (để hiện cả file ẩn/hệ thống).

#### Bước 3: Sao chép tệp mẫu `template.ps1` từ `Public` vào `bin` và đổi tên thành `pump_control.ps1`

Copy-Item \-Path "C:\\Users\\Public\\template.ps1" \-Destination "\$HOME/smart-farm/bin\\pump\_control.ps1"

*(Lưu ý xử lý ràng buộc quyền: Nếu gặp lỗi `Access to the path is denied`, bạn cần mở lại cửa sổ PowerShell bằng quyền quản trị viên bằng cách chạy lệnh: `Start-Process powershell -Verb RunAs`, sau đó thực hiện lại câu lệnh sao chép trên).*

#### Bước 4: Kiểm tra lại toàn bộ cấu trúc thư mục `smart-farm`

Get-ChildItem \-Path "\$HOME/smart-farm" \-Recurse \-Force  
