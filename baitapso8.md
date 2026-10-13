Phần 1: Phân tích lỗi

Chỉ ra các dòng lệnh gây ra lỗi mất dữ liệu lịch sử và giải thích nguyên nhân kỹ thuật:



Các dòng lệnh dùng lệnh Out-File mặc định trong script hiện tại:

"\[$(Get-Date)] --- Bat dau quet ---" | Out-File weekly\_errors.txt

Select-String -Path app.log -Pattern "error" | Out-File weekly\_errors.txt

"\[$(Get-Date)] --- Ket thuc quet ---" | Out-File weekly\_errors.txt





Nguyên nhân kỹ thuật: Mặc định trong PowerShell, lệnh Out-File thực hiện cơ chế ghi đè (Overwrite). Mỗi khi câu lệnh tiếp theo được thực thi trong script, nó sẽ xóa sạch toàn bộ nội dung cũ đang có trong tệp weekly\_errors.txt rồi mới ghi nội dung mới vào. Vì vậy, ngay cả trong cùng một lần chạy, dòng kết thúc đã ghi đè lên dòng kết quả trước đó, và qua mỗi giờ chạy tiếp theo, toàn bộ lịch sử tích lũy từ các giờ trước bị xóa sạch hoàn toàn.Mô tả chính xác điều gì còn sót lại trong file weekly\_errors.txt sau mỗi lần script chạy:Sau khi script chạy xong, trong file weekly\_errors.txt chỉ còn lại duy nhất dòng kết thúc (\[<thời\_gian>] --- Ket thuc quet ---) của lần chạy ở giờ đó, còn toàn bộ nhật ký lỗi trích xuất từ app.log và dòng bắt đầu đều đã bị ghi đè và mất hoàn toàn.Phần 2: Sửa lỗi và kiểm thử1. Phiên bản script đã sửa đúng quy trình cộng dồn (-Append):PowerShell# Script: hourly\_log.ps1 (Đã sửa lỗi bằng tham số -Append để cộng dồn nhật ký)

\# Chạy mỗi giờ, cộng dồn lỗi vào weekly\_errors.txt



"\[$(Get-Date)] --- Bat dau quet ---" | Out-File weekly\_errors.txt -Append

Select-String -Path app.log -Pattern "error" | Out-File weekly\_errors.txt -Append

"\[$(Get-Date)] --- Ket thuc quet ---" | Out-File weekly\_errors.txt -Append

2\. Bảng so sánh giữa Out-File (ghi đè) và Out-File -Append / Add-Content (ghi nối):Đặc điểmOut-File (Mặc định)Out-File -Append / Add-ContentCơ chế tác độngGhi đè (Overwrite)Ghi nối tiếp (Append)Trạng thái tệp cũXóa sạch toàn bộ nội dung cũ trước khi ghi mớiGiữ nguyên vẹn nội dung cũ và nối thêm vào cuối tệpTrường hợp sử dụngKhi tạo mới báo cáo hoặc làm sạch file log mỗi phiên chạyKhi tích lũy nhật ký dài hạn, cộng dồn dữ liệu theo thời gian

