# AURA - High-End Perfume E-commerce & AI-Consultant Platform

**AURA** không chỉ là một trang web bán nước hoa thông thường; đây là một hệ thống thương mại điện tử hoàn chỉnh, được thiết kế với kiến trúc bền vững, tập trung vào tối ưu hóa trải nghiệm người dùng cao cấp và tích hợp trí tuệ nhân tạo (AI) vào quy trình tư vấn khách hàng.

---

## 💎 Điểm nhấn Kỹ thuật Chuyên sâu (Advanced Technical Showcases)

Phần này được thiết kế để "trình diễn" khả năng thiết kế hệ thống và giải quyết các bài toán kỹ thuật phức tạp của bạn trong mắt nhà tuyển dụng.

### 1. Hệ thống Tư vấn AI (RAG - Retrieval-Augmented Generation)
*   **Kiến trúc RAG hiện đại**: Xây dựng chatbot tư vấn chuyên sâu sử dụng **Spring AI**. Hệ thống không chỉ trả lời theo kịch bản mà thực sự "hiểu" kho hàng thông qua cơ chế truy vấn vector (Semantic Search).
*   **Quy trình ETL & Ingestion**: 
    *   Tự xây dựng logic gom dữ liệu (Data Aggregator) từ nhiều thực thể phức tạp (Perfume, Note, Volume, Brand) thành một chuỗi văn bản ngữ cảnh giàu thông tin (Rich Context).
    *   Sử dụng **Ollama (mxbai-embed-large)** chạy local để thực hiện embedding, giúp tối ưu chi phí (0 đồng phí embedding) và bảo mật tuyệt đối dữ liệu doanh nghiệp.
*   **Real-time Vector Sync (Event-Driven)**: 
    *   Triển khai kiến trúc hướng sự kiện sử dụng `ApplicationEventPublisher`.
    *   Sử dụng `@TransactionalEventListener(phase = TransactionPhase.AFTER_COMMIT)` để đảm bảo dữ liệu chỉ được đồng bộ sang **Qdrant Vector Database** sau khi giao dịch MySQL thành công, giữ cho dữ liệu giữa 2 database luôn nhất quán.
*   **Prompt Engineering & AI Persona**: Thiết kế System Prompt chi tiết để định hình tính cách chuyên gia tư vấn cao cấp, hỗ trợ định dạng Markdown, tạo link sản phẩm động trực tiếp trong hội thoại.

### 2. Hệ thống Thanh toán VNPay (Security & Consistency)
*   **Ký số & Bảo mật**: Triển khai chuẩn **VNPay v2.1.0** với thuật toán băm **HMAC-SHA512**. Xử lý nghiêm ngặt việc sắp xếp tham số (Alphabetical Sort) và URL Encoding theo chuẩn `US_ASCII` để loại bỏ hoàn toàn lỗi lệch chữ ký ("Invalid Signature").
*   **Cơ chế Webhook IPN (Instant Payment Notification)**: 
    *   Thiết kế endpoint IPN bảo mật server-to-server để cập nhật trạng thái đơn hàng.
    *   Triển khai quy trình kiểm tra 4 lớp: (1) Xác thực chữ ký, (2) Đối chiếu số tiền (VND/USD exchange), (3) Kiểm tra sự tồn tại đơn hàng, (4) Kiểm tra trạng thái hiện tại (Idempotency) để tránh cập nhật trùng lặp.
*   **Xử lý Giao dịch phức tạp**: Tích hợp tính năng **Repay** (Tạo lại link thanh toán) cho phép người dùng thanh toán lại các đơn hàng bị lỗi hoặc hết hạn session mà không phải trải qua quy trình đặt hàng lại từ đầu.

### 3. Kiến trúc Backend Modular Monolith
*   **Domain-Driven Design (DDD) Influence**: Codebase được chia thành các module độc lập (Auth, Perfume, Invoice, Assistant...). Mỗi module sở hữu đầy đủ các lớp từ Controller, Service, Mapper cho đến DTO, giúp giảm thiểu sự phụ thuộc chéo (Tight Coupling).
*   **Persistence Layer Optimization**:
    *   **N+1 Query Resolution**: Sử dụng `@EntityGraph` và `@BatchSize` của Hibernate để tải dữ liệu liên quan (như ảnh, tầng hương, dung tích) một cách hiệu quả, giảm số lượng truy vấn từ N xuống chỉ còn 1-2 query.
    *   **Formula Mapping**: Sử dụng `@Formula` của Hibernate để tính toán giá thấp nhất (minPrice) trực tiếp bằng SQL ngay khi query, giúp tính năng sắp xếp theo giá (Sort by Price) hoạt động cực nhanh trên hàng ngàn sản phẩm.
    *   **Dynamic Filtering**: Triển khai **JPA Specification** để xây dựng bộ lọc sản phẩm linh hoạt (theo tên, hãng, giới tính, khoảng giá) với các câu lệnh SQL động.

### 4. Hệ thống Định danh & Bảo mật (Security Excellence)
*   **Stateless JWT Authentication**: Xây dựng bộ lọc `JwtAuthenticationFilter` tùy chỉnh. Điểm đặc biệt: Triển khai logic cho phép các request có token hết hạn/lỗi vẫn truy cập được vào các API công khai (Public Endpoints) mà không bị chặn (ADR-001), giúp tối ưu SEO và UX.
*   **Google OAuth2 & Account Linking**: 
    *   Tích hợp đăng nhập mạng xã hội qua **OAuth2 Client**.
    *   Xử lý logic **Account Linking**: Tự động liên kết tài khoản Google với tài khoản đăng ký bằng email truyền thống nếu trùng khớp, tránh tạo tài khoản rác cho người dùng.
    *   Sử dụng **HttpOnly Cookie** để truyền tải JWT an toàn sau khi Login thành công qua OAuth2.

### 5. Frontend Architecture & UX (React 18 & Vite)
*   **Feature-Based Structure**: Tổ chức code theo tính năng (Features), mỗi tính năng có các components, hooks và types riêng, giúp dự án dễ dàng mở rộng và scale lên quy mô lớn.
*   **State Management & Persistence**: 
    *   Tự xây dựng các **Custom Hooks** (`useConsultation`, `useModal`, `useToggle`) để quản lý logic phức tạp một cách tái sử dụng.
    *   Triển khai **Cart Persistence** thủ công qua LocalStorage, đồng bộ hóa trạng thái giỏ hàng với kho hàng backend trong quá trình checkout.
*   **Luxury Design System**: Sử dụng **Tailwind CSS** để xây dựng giao diện theo phong cách tối giản nhưng sang trọng (Typography Noto Serif, bảng màu Neutral, hiệu ứng Glassmorphism).

### 6. Hệ thống Thông báo Email tự động (Automated Mail Service)
*   **Giải pháp**: Triển khai dịch vụ thông báo tự động, gửi email xác nhận đơn hàng và hóa đơn điện tử ngay sau khi khách hàng hoàn tất thanh toán thành công.
*   **Kỹ thuật**: Sử dụng **Spring Boot Starter Mail** kết hợp với **Thymeleaf Template** để thiết kế các mẫu email (HTML Email) chuyên nghiệp, hỗ trợ cá nhân hóa dữ liệu khách hàng và thông tin đơn hàng.
*   **Achievement**: Áp dụng cơ chế xử lý bất đồng bộ (**Asynchronous Support**) giúp việc gửi email không ảnh hưởng đến thời gian phản hồi của luồng thanh toán chính, tối ưu hóa hiệu năng hệ thống.

---

## 🛠 Công nghệ Sử dụng (Detailed Tech Stack)

### 🟢 Backend (Java Ecosystem)
- **Language**: Java 21 (LTS) - Tận dụng các tính năng mới như Record, Pattern Matching.
- **Persistence**: Spring Data JPA, Hibernate, MySQL 8.1
- **Framework**: Spring Boot 3.4.0.
- **Security**: Spring Security 6 (OAuth2, JWT, Password Encoding).
- **AI Integration**: Spring AI (Vector Store, Chat Client, Advisors API), GroqAPI, Ollama.
- **Messaging**: Spring Boot Starter Mail, Thymeleaf (Email Templating).
- **Database**: MySQL 8.1 (Relational), Qdrant (Vector DB).
- **Mapping**: MapStruct (High performance object mapping).
- **Documentation**: Swagger/OpenAPI, Mermaid.js, ADR Markdown files.

### 🔵 Frontend (Modern Web)
- **Framework**: React 18 (Functional Components, Hooks).
- **Build Tool**: Vite (Fastest build tool).
- **Routing**: React Router v6 (Nested Routes, Protected Routes).
- **Styling**: Tailwind CSS, PostCSS.
- **API Communication**: Axios (Interceptors for Bearer Tokens).
- **Content**: React Markdown (Dành cho phản hồi từ AI).

---

## 🛠 Công nghệ Sử dụng (Tech Stack)
### Backend
*   **Core**: Java 21, Spring Boot 3.4
*   **Persistence**: Spring Data JPA, Hibernate, MySQL 8.1
*   **Security**: Spring Security, JWT, OAuth2 (Google)
*   **AI**: Spring AI, Groq API, Qdrant (Vector DB), Ollama
*   **Notification**: Spring Boot Starter Mail, Thymeleaf
*   **Integration**: VNPay API, Cloudinary (Image Storage)
*   **Testing**: JUnit 5, Mockito, AssertJ
### Frontend
*   **Core**: React 18, React Router v6
*   **Styling**: Tailwind CSS (Luxury design system)
*   **Networking**: Axios (với interceptors xử lý Token)
*   **Tooling**: Vite, Vitest (Testing), Prettier/ESLint
### Infrastructure
*   **Containerization**: Docker, Docker Compose
*   **DevOps**: GitHub-focused workflow với các tập hướng dẫn AI (Copilot Instructions) tối ưu cho đội ngũ.

---

## 🏗 Engineering Principles (Quy chuẩn Kỹ thuật)

1.  **Clean Code**: Tuân thủ nguyên tắc SOLID, DRY và KISS.
2.  **API Standards**: Sử dụng chuẩn RESTful, trả về dữ liệu theo cấu trúc Envelope (`ApiResponse`) đồng nhất cho toàn hệ thống.
3.  **Global Error Handling**: Xử lý ngoại lệ tập trung, trả về mã lỗi và thông báo có ý nghĩa cho phía client.
4.  **Database Migration**: Quản lý schema database chặt chẽ, khởi tạo môi trường nhanh chóng qua Docker Compose.
5.  **Testing**: Triển khai Unit Test cho các lớp Logic và Mapper quan trọng (JUnit 5, Mockito, AssertJ).

---

## 🚀 Hướng dẫn Cài đặt & Triển khai

### Yêu cầu hệ thống
- **Docker & Docker Compose** (Khuyến nghị)
- **Java 21 JDK**
- **Node.js 18+** & **npm**

### Các bước thực hiện
1.  **Clone dự án**: `git clone [URL]`
2.  **Setup Environment**: Copy `.env.example` thành `.env` trong thư mục `/backend` và điền các thông tin (DB, Groq API Key, VNPay Key).
3.  **Khởi động Infrastructure**: `docker compose up -d` (Khởi chạy MySQL, Qdrant).
4.  **Chạy Backend**: `mvn spring-boot:run` trong thư mục `/backend`.
5.  **Chạy Frontend**: `npm install` và `npm run dev` trong thư mục `/frontend`.

---

© 2026 AURA Gallery Project. Toàn bộ kiến trúc và mã nguồn được thiết kế bởi **[Tên của bạn]**.
