# POS - Smart Restaurant Management & Real-time QR Ordering System

**POS** không chỉ là một phần mềm quản lý bán hàng đơn thuần; đây là một hệ sinh thái vận hành nhà hàng toàn diện. Hệ thống kết nối trực tiếp Khách hàng - Nhân viên - Nhà bếp thông qua quy trình tự động hóa bằng mã QR và giao tiếp thời gian thực (Real-time), giúp tối ưu hóa tốc độ phục vụ và loại bỏ sai sót trong quy trình vận hành thủ công.

---

## 💎 Điểm nhấn Kỹ thuật Chuyên sâu (Technical Showcases)

Dự án này là minh chứng cho khả năng thiết kế hệ thống có tính nhất quán cao, xử lý luồng dữ liệu phức tạp và tối ưu hóa trải nghiệm người dùng cuối.

### 1. Kiến trúc Real-time Event-Driven (WebSockets)
*   **Giao tiếp đa chiều**: Sử dụng **Socket.IO** để thiết lập luồng thông tin tức thời giữa ba thực thể:
    *   **Khách hàng**: Nhận thông báo khi món ăn hoàn thành hoặc bàn được chuyển.
    *   **Nhà bếp**: Nhận đơn hàng mới ngay khi khách bấm đặt món mà không cần reload trang.
    *   **Nhân viên**: Theo dõi trạng thái tất cả các bàn và yêu cầu hỗ trợ/thanh toán từ khách hàng.
*   **Phân luồng Room/Namespace**: Triển khai cơ chế `join room` linh hoạt dựa trên `tableId` và `role`. Điều này đảm bảo tính riêng tư (khách bàn A không nhận đơn của bàn B) và tính tập trung (tất cả nhân viên nhận được thông báo chung).

### 2. Mô hình MVC & Hybrid Routing (SSR & API)
*   **Kiến trúc Modular MVC**: Codebase được tổ chức chặt chẽ với sự phân tách rõ ràng giữa Model (Query SQL thuần), Controller (Business Logic) và View (EJS Templates).
*   **Chiến lược Routing kép**:
    *   **SSR (Server-Side Rendering)**: Sử dụng EJS cho các giao diện quản trị (Admin/Staff/Kitchen) để tăng tốc độ render ban đầu và bảo mật logic phía server.
    *   **RESTful API**: Xây dựng hệ thống API (phiên bản v1/v2) phục vụ cho các tác vụ lấy dữ liệu động, cập nhật trạng thái mà không cần tải lại trang, giúp giao diện mượt mà như một Single Page Application (SPA).
*   **Version Control cho API**: Triển khai cấu trúc `/api/v1` và `/api/v2` để sẵn sàng cho việc mở rộng tính năng mà không làm ảnh hưởng đến các phiên bản hiện tại.

### 3. Quản lý Trạng thái & Tính nhất quán dữ liệu (State Management)
*   **Quy trình vòng đời đơn hàng**: Thiết kế một State Machine chặt chẽ cho món ăn: `Đã nhận` -> `Đang chế biến` -> `Hoàn thành`. 
*   **Ràng buộc cơ sở dữ liệu (Integrity)**: 
    *   Sử dụng **MySQL Transactions** (thông qua `mysql2/promise`) để đảm bảo khi thanh toán hóa đơn: Trạng thái bàn tự động chuyển về `Trống`, điểm tích lũy khách hàng được cập nhật, và đơn hàng hiện tại được lưu vào lịch sử hóa đơn một cách đồng bộ.
    *   Xử lý **Soft Delete** cho các thực thể quan trọng (Món ăn, Bàn, Khách hàng) để duy trì tính toàn vẹn của dữ liệu báo cáo lịch sử.

### 4. Hệ thống Định danh & Phân quyền (Security & Session)
*   **Session-based Authentication**: Sử dụng `express-session` kết hợp `session-file-store` để quản lý phiên làm việc bền vững. 
*   **Bảo mật mật khẩu**: Triển khai cơ chế băm mật khẩu bằng **bcrypt** với salt rounds cao, đảm bảo an toàn tuyệt đối cho tài khoản quản trị và nhân viên.
*   **Middleware Protection**: Xây dựng các lớp Middleware kiểm soát quyền truy cập theo vai trò (Role-based Access Control), ngăn chặn truy cập trái phép vào các khu vực nhạy cảm như Admin Dashboard hoặc Kitchen Monitor.

### 5. Tối ưu hóa UI/UX & Responsive Design
*   **Mobile-First cho Khách hàng**: Giao diện đặt món được tối ưu hoàn toàn cho thiết bị di động, tập trung vào thao tác chạm và quét QR.
*   **Component-based EJS**: Chia nhỏ các thành phần giao diện (Sidebar, Tab, Modal) thành các file `.ejs` riêng biệt, giúp tái sử dụng mã nguồn và dễ dàng bảo trì.
*   **Dynamic Assets Management**: Hệ thống quản lý hình ảnh món ăn linh hoạt, hỗ trợ hiển thị ảnh theo nhóm món (Category) giúp người dùng dễ dàng tìm kiếm.

---

## 🛠 Công nghệ Sử dụng (Detailed Tech Stack)

### 🟢 Backend (Node.js Ecosystem)
- **Language**: JavaScript (ES6+).
- **Framework**: Express.js - Lightweight và hiệu năng cao.
- **Database**: MySQL - Lưu trữ dữ liệu quan hệ chặt chẽ.
- **Real-time**: Socket.IO - Xử lý giao tiếp song công (Full-duplex).
- **Authentication**: Bcrypt, Express-Session.
- **Tools**: Multer (Upload ảnh), Dotenv (Quản lý biến môi trường), Nodemon.

### 🔵 Frontend (Web Technologies)
- **Template Engine**: EJS (Embedded JavaScript templates).
- **Core**: Vanilla JavaScript - Tối ưu hiệu năng, không phụ thuộc thư viện nặng.
- **Styling**: CSS3 (Custom Grid & Flexbox) - Xây dựng giao diện hiện đại, sạch sẽ.
- **Communication**: Axios & Native Fetch API.

### 🟡 Infrastructure & DevOps
- **Containerization**: Docker & Docker Compose - Đóng gói toàn bộ ứng dụng và database để triển khai chỉ với một lệnh.
- **Environment**: Quản lý cấu hình qua `.env` cho phép chuyển đổi dễ dàng giữa Development và Production.

---

## 📈 Những gì tôi đã làm được & Học được

### ✅ Kết quả đạt được
1.  **Xây dựng hoàn chỉnh hệ thống POS**: Từ khâu thiết kế Database (10+ bảng) đến triển khai giao diện người dùng cuối.
2.  **Xử lý bài toán Concurrency**: Giải quyết vấn đề nhiều khách hàng cùng đặt món tại một bàn hoặc nhiều nhân viên cùng cập nhật trạng thái đơn hàng thông qua cơ chế Locking và Socket events.
3.  **Hệ thống báo cáo**: Triển khai logic tính toán doanh thu, thống kê món ăn bán chạy và quản lý cấp bậc khách hàng (Đồng/Bạc/Vàng/Kim cương).

### 🧠 Bài học kinh nghiệm
*   **Tư duy Hệ thống**: Cách thiết kế luồng đi của dữ liệu sao cho tối ưu nhất, đặc biệt là trong các hệ thống đòi hỏi tính phản hồi nhanh như nhà hàng.
*   **Kỹ năng Debug Real-time**: Học cách xử lý các vấn đề về độ trễ mạng, mất kết nối socket và đồng bộ hóa trạng thái giữa Client-Server.
*   **Sạch hóa mã nguồn**: Áp dụng Design Patterns và Clean Architecture để biến một dự án lớn trở nên dễ đọc và dễ mở rộng.

---

© 2026 POS Project. Thiết kế và phát triển bởi **[Tên của bạn]**.
