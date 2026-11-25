# Project A — Bản vá liên động bơm

Bản vá FC SCL dành cho TIA Portal V20. Giữ OB100 và các bộ định thời. Chèn FC vào bản sao project để compile và thử với ngõ ra vật lý đã được cô lập. Việc xác nhận cực tính các tín hiệu tại lab là điều kiện để gán chân.

## Nối khối trong OB1

- Tạo FC từ External source `MixingPumpPermissive.scl`. Gọi sau khi tính xong trạng thái bước định lượng và hồi của vòng quét. Các ngõ Auto lấy từ Step_B201, Step_B202, Step_B203. Đầu vào ReturnRequest lấy từ Step_Rst.
- Dùng Key_Switch (%I11.2) để chọn chế độ. Gán ModeChanged bằng xung một vòng quét khi Key_Switch đổi ở bất kỳ chiều nào. Xóa nhánh Key_Switch bật trực tiếp van và bơm trong network cũ. ManualB201–203 nhận lệnh riêng đã được xác nhận. Nếu project chưa có lệnh chọn bồn bằng tay, nối FALSE cả ba khi chạy thử.
- Chuẩn hóa StopOK từ Stop_BTN (%I11.1) và B201Available–B204Available từ bốn LowLevel_B201–B204 (%I10.2, %I10.3, %I10.4, %I10.6). Available = TRUE chỉ khi mực nước đủ. Cực tính cần đối chiếu trực tiếp. Chuẩn hóa tương tự hai mức cao B204High và B201High.
- Gán ValveB201–203 theo thứ tự vào Val_2M3 (%Q4.2), Val_2M4 (%Q4.3), Val_2M5 (%Q4.4). Gán FeedPump vào Enable_Pump (%Q4.0), ReturnPump vào Pump_P202 (%Q4.1), AnalogOutput vào Pump_Speed (%QW0).
- Cho OB30 lưu kết quả quy đổi vào một tag INT nội bộ, đưa tag đó vào AnalogDemand. Chuyển chỗ ghi %QW0 sang ngõ ra AnalogOutput của FC. Kiểm tra Cross-references để mỗi ngõ ra vật lý chỉ còn một chỗ ghi.
- Sau FC, dùng AbortDose để xóa cả ba bit Step_B20x và lệnh tay lưu giữ. Dùng AbortReturn để xóa Step_Rst. Cho điều kiện xóa có ưu tiên cuối cùng trong vòng quét. Chặn lệnh START và RETURN đang giữ bằng cách chỉ nhận cạnh mới sau khi nút đã được nhả. Kiểm tra cả trường hợp khởi động lại với nút đang giữ và trường hợp gạt chế độ đồng thời với yêu cầu chạy.

## Các ca thử

| Tình huống | Kết quả mong đợi |
| --- | --- |
| Gạt Switch khi đang chờ | Van và bơm không nhận lệnh |
| Gạt Switch khi đang chạy | Dừng bơm và xóa bước đang chạy |
| Một bồn nguồn hợp lệ, đủ nước, có START | Chỉ một van nguồn và P-201 nhận lệnh |
| Bồn nguồn đang dùng xuống thấp | P-201 tắt, %QW0 về 0, bước định lượng bị xóa |
| B204 xuống thấp khi đang hồi | P-202 tắt và bước hồi bị xóa |
| Hai nguồn được yêu cầu hoặc định lượng và hồi đồng thời | Các lệnh bơm về 0 |
| STOP, sau đó nhả STOP với nút chạy còn giữ | Các ngõ ra về 0 và chỉ chạy lại sau lệnh mới |

Phiên bản SCL chưa được compile bằng TIA Portal. Việc đặt đúng cực tính tín hiệu, xác nhận lệnh HMI và kiểm tra liên động trên trạm cần làm trước khi nạp bản sửa.
