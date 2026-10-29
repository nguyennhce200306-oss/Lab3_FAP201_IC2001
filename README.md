# Lab3_FAP201 - Clocked Sequential Logic & Edge Detection

## Thông tin chung
* **Môn học:** FAP201 - FPGA Programming
* **Bài thực hành:** Lab03 - Clocked Sequential Logic & Edge Detection
* **Mục tiêu:** Thiết kế, kiểm chứng và hiện thực mạch tuần tự (Sequential Circuit) sử dụng xung nhịp clock để bắt cạnh xuống (Falling Edge) của nút nhấn và đảo trạng thái (Toggle) đèn LED[cite: 23].

## Môi trường và Phần cứng
* **Công cụ phát triển:** Gowin EDA
* **Bo mạch (Board):** Xiaomeige ACG525
* **Chip FPGA (Target Device):** GW5A-LV25UG324C2/I1
* **Xung nhịp hệ thống (Clock):** 50 MHz (Chu kỳ 20 ns)[cite: 22]
* **Ngôn ngữ:** VHDL (IEEE 1164 STD_LOGIC)[cite: 23]

## Nguyên lý hoạt động & Bắt cạnh Tín hiệu (Edge Detection)
* **Mạch tuần tự (Sequential Circuit):** Khác với mạch tổ hợp ở Lab01/Lab02, thiết kế này sử dụng thanh ghi Flip-Flop và đồng bộ hóa hoạt động theo cạnh lên của xung nhịp `i_Clk` (`rising_edge(i_Clk)`)[cite: 23].
* **Mạch phát hiện cạnh xuống (Falling Edge Detector):**
  * Tín hiệu `r_Switch` đóng vai trò là một thanh ghi lưu lại trạng thái của `i_Switch` ở chu kỳ clock trước đó (`r_Switch <= i_Switch;`)[cite: 23].
  * Điều kiện `if i_Switch = '0' and r_Switch = '1'` chỉ thỏa mãn đúng trong 1 chu kỳ clock tại thời điểm tín hiệu chuyển từ mức `'1'` xuống mức `'0'` (tương ứng với thời điểm bắt cạnh xuống)[cite: 23].
* **Lật trạng thái LED (Toggle):** Khi phát hiện đúng cạnh xuống, thanh ghi `r_LED` sẽ lật trạng thái (`r_LED <= not r_LED;`), giúp đảo trạng thái bật/tắt của LED ngõ ra[cite: 23].

## Cấu hình Chân & Ràng buộc Thời gian (Constraints & Timing)

### 1. Ánh xạ chân vật lý (`Lab3.cst`)
Tín hiệu được cấu hình trong file `Lab3.cst`[cite: 21]:

| Tín hiệu VHDL | Chân FPGA | Linh kiện phần cứng | Chuẩn I/O | Kéo trở (Pull) |
| :--- | :---: | :--- | :--- | :--- |
| `i_Clk` | `T9` | System Clock (50 MHz) | `LVCMOS33`[cite: 21] | `NONE`[cite: 21] |
| `i_Switch` | `B16` | Switch / Nút nhấn ngõ vào | `LVCMOS33`[cite: 21] | `NONE`[cite: 21] |
| `o_LED` | `D14` | Output LED | `LVCMOS33`[cite: 21] | `NONE`[cite: 21] |

### 2. Ràng buộc thời gian (`Lab3.sdc`)
File `Lab3.sdc` khai báo xung nhịp `i_Clk` với chu kỳ **20 ns** (tương ứng **50 MHz**), dạng sóng vuông cân bằng 50% (`waveform {0 10}`) để công cụ Gowin EDA thực hiện phân tích định thì STA (Static Timing Analysis)[cite: 22]:
```tcl
create_clock -name i_Clk -period 20 -waveform {0 10} [get_ports {i_Clk}]
```[cite: 22]

## Cấu trúc thư mục dự án (Project Structure)
* `src/Lab3.vhd`: Mã nguồn VHDL mô tả mạch tuần tự bắt cạnh và lật LED[cite: 23].
* `constraints/Lab3.cst`: File cấu hình gán chân vật lý cho FPGA[cite: 21].
* `constraints/Lab3.sdc`: File định nghĩa ràng buộc thời gian xung nhịp[cite: 22].
* `sim/lab03_tb.vhd`: Testbench cấp xung nhịp 50MHz và giả lập tín hiệu bấm nút.
* `waveform/`: Ảnh chụp sóng mô phỏng thể hiện thời điểm bắt cạnh và lật trạng thái `r_LED`.
* `reports/`: Báo cáo Synthesis, P&R và Timing Analysis (STA).
* `evidence/`: Log Programmer và minh chứng thực nghiệm trên bo mạch.
* `README.md`: Document tài liệu dự án.

## Hướng dẫn Build & Kiểm chứng (Execution & Verification)
1. **Mô phỏng (Simulation):**
   * Tạo xung clock 50MHz trong Testbench[cite: 22].
   * Giả lập tín hiệu `i_Switch` chuyển từ `'1'` xuống `'0'` và quan sát ngõ ra `o_LED` lật trạng thái đúng tại cạnh lên của clock[cite: 23].
2. **Tổng hợp & Phân tích Thời gian (Synthesis & STA):**
   * Mở dự án trong Gowin EDA, nạp cả file `.cst` và file `.sdc`[cite: 21, 22].
   * Thực hiện **Synthesis** và **Place & Route**. Kiểm tra báo cáo Timing đảm bảo thiết kế đáp ứng thời gian Setup/Hold time[cite: 22].
3. **Nạp phần cứng (Programming):**
   * Kết nối bo mạch Xiaomeige ACG525 và dùng Gowin Programmer nạp file bitstream (`.fs`)[cite: 21].
   * Thao tác nhấn nút ngõ vào, mỗi lần tác động nhả/bấm nút sẽ làm đèn LED0 lật trạng thái giữa BẬT và TẮT[cite: 21, 23].
