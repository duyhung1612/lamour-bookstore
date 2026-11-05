# 📚 L'Amour Bookstore - Hệ Thống Quản Lý & Bán Sách Toàn Diện

> **L'Amour Bookstore** là hệ thống quản lý nhà sách và thương mại điện tử đa nền tảng theo mô hình MVC, tích hợp quản lý kho, kệ sách thông minh, kế toán, bán hàng tại quầy (POS quét mã vạch Barcode), cổng thanh toán MoMo Sandbox và gửi email hóa đơn tự động.

---

## 🚀 1. Yêu Cầu Môi Trường (Prerequisites)

* **Node.js**: Phiên bản 18.x trở lên (Khuyên dùng Node 20 LTS).
* **NPM**: Đi kèm với Node.js.
* **CSDL MongoDB**:
  * **Cách A (MongoDB Local):** Cài đặt [MongoDB Community Server](https://www.mongodb.com/try/download/community) và chạy tại `mongodb://127.0.0.1:27017`.
  * **Cách B (MongoDB Cloud / Atlas):** Sử dụng cluster MongoDB Atlas (Miễn phí trên mLab/AWS/GCP).

---

## ⚙️ 2. Cài Đặt & Cấu Hình Dự Án

### Bước 1: Cài đặt các gói phụ thuộc (Dependencies)
Mở cửa sổ dòng lệnh (Terminal/PowerShell) tại thư mục dự án và chạy:
```bash
npm install
```

### Bước 2: Tạo file cấu hình môi trường `.env`
Tạo một file có tên `.env` ngay tại thư mục gốc của dự án (nếu chưa có), copy nội dung từ mẫu dưới đây và điền thông tin:

```env
# Cổng chạy ứng dụng
PORT=4000
NODE_ENV=development

# Khóa bảo mật JWT
JWT_SECRET=lamour_secret_key_2026_very_secure

# ==========================================================
# CẤU HÌNH CƠ SỞ DỮ LIỆU MONGODB
# ==========================================================
# LỰA CHỌN 1: Kết nối MongoDB Local trên máy
MONGO_URI=mongodb://127.0.0.1:27017/lamour_bookstore

# LỰA CHỌN 2: Kết nối MongoDB Cloud (Atlas)
# MONGO_URI=mongodb+srv://<username>:<password>@lamour-bookstore.7ewktff.mongodb.net/lamour_bookstore?retryWrites=true&w=majority&appName=lamour-bookstore

# ==========================================================
# CẤU HÌNH GỬI EMAIL (HÓA ĐƠN & THÔNG BÁO)
# ==========================================================
# Cách 1: Sử dụng Brevo API (Khuyên dùng khi chạy Online / Render)
BREVO_API_KEY=xkeysib-xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
BREVO_SENDER_EMAIL=your_email@gmail.com
BREVO_SENDER_NAME="L'Amour Bookstore"

# Cách 2: Sử dụng SMTP Gmail (Thích hợp chạy Local)
SMTP_HOST=smtp.gmail.com
SMTP_PORT=465
SMTP_SECURE=true
SMTP_USER=your_email@gmail.com
SMTP_PASS=your_16_character_app_password
EMAIL_FROM="L'Amour Bookstore <your_email@gmail.com>"

# ==========================================================
# CỔNG THANH TOÁN MOMO SANDBOX (TEST)
# ==========================================================
MOMO_PARTNER_CODE=MOMOBKUN20180529
MOMO_ACCESS_KEY=klm05TvNBzhg7h7j
MOMO_SECRET_KEY=at67qH6mk8w5Y1nAyMoYKMWACiEi2Aca
MOMO_ENDPOINT=https://test-payment.momo.vn/v2/gateway/api/create
MOMO_IPN_URL=http://localhost:4000/api/payments/momo/ipn
MOMO_REDIRECT_URL=http://localhost:4000/checkout/momo-return
```

> **Lưu ý:** File `.env` chứa mật khẩu bảo mật nên **đã được cấu hình trong `.gitignore`**, tuyệt đối không chia sẻ công khai lên mạng.

---

## ▶️ 3. Khởi Động Website

### Chế độ phát triển (Auto-reload khi sửa code):
```bash
npm run dev
```
*(Hoặc chạy lệnh `node --watch server.js`)*

### Chế độ chuẩn (Production):
```bash
npm start
```

### 🌐 Các đường dẫn truy cập:
* **Giao diện trang chủ (Dành cho độc giả):** [http://localhost:4000](http://localhost:4000)
* **Trang Đăng nhập hệ thống:** [http://localhost:4000/login](http://localhost:4000/login)
* **Cổng HTTPS Local (Quét mã vạch camera điện thoại):** [https://localhost:4443](https://localhost:4443) *(Tự sinh chứng chỉ SSL nội bộ)*

---

## 👥 4. Tài Khoản Đăng Nhập Mặc Định

Khi khởi động server lần đầu hoặc kết nối vào CSDL mới, hệ thống sẽ **tự động khởi tạo** các tài khoản mẫu sau với mật khẩu chung là `password123`:

| Vai trò | Email đăng nhập | Mật khẩu mặc định | Quyền hạn |
| :--- | :--- | :--- | :--- |
| **Quản trị viên (Admin)** | `admin@lamour.vn` | `password123` | Toàn quyền quản trị hệ thống, nhân sự, doanh thu |
| **Nhân viên Bán hàng (Sales)** | `sales@lamour.vn` | `password123` | Bán hàng POS tại quầy, xuất hóa đơn, quản lý ca |
| **Thủ kho (Stock)** | `kho@lamour.vn` | `password123` | Nhập/xuất kho, bố trí kệ sách, kiểm kê hàng hóa |
| **Kế toán (Accountant)** | `ketoan@lamour.vn` | `password123` | Thu/chi, công nợ nhà cung cấp, báo cáo tài chính |
| **Khách hàng dùng thử** | `customer@gmail.com` | `password123` | Đặt sách online, tra cứu đơn hàng, đánh giá |

---

## 🗄️ 5. Quản Lý & Đồng Bộ CSDL MongoDB

### A. Chuyển đổi giữa CSDL Local và CSDL Cloud (Atlas)
Chỉ cần mở file `.env` và đổi giá trị dòng `MONGO_URI`:
* Muốn dùng CSDL trên máy:
  ```env
  MONGO_URI=mongodb://127.0.0.1:27017/lamour_bookstore
  ```
* Muốn dùng CSDL Cloud (Atlas):
  ```env
  MONGO_URI=mongodb+srv://<username>:<password>@lamour-bookstore.7ewktff.mongodb.net/lamour_bookstore?retryWrites=true&w=majority&appName=lamour-bookstore
  ```

### B. Đồng bộ dữ liệu từ máy Local lên MongoDB Cloud (Atlas)
Nếu bạn đã tạo nhiều dữ liệu mẫu, sách mới hoặc đơn hàng trên MongoDB Local và muốn đẩy toàn bộ lên Cloud Atlas:
```bash
node scripts/migrate_local_to_atlas.js
```
*Script sẽ tự động quét tất cả các collection từ máy tính và đồng bộ lên MongoDB Atlas một cách an toàn.*

### C. Tối ưu tốc độ truy vấn (Tạo Database Indexes)
Để tăng tốc độ tìm kiếm sách, lọc hóa đơn và kiểm tra kho:
```bash
node scripts/create_db_indexes.js
```

### D. Kiểm tra kết nối dịch vụ Email:
```bash
npm run test-email
```

---

## 📁 6. Cấu Trúc Thư Mục Dự Án

```text
├── config/             # Cấu hình kết nối CSDL MongoDB & DNS fallback
├── controllers/        # Điều khiển logic nghiệp vụ (Auth, Books, POS, Orders, Kho, v.v.)
├── middleware/         # Middleware bảo mật JWT, xác thực phiên và phân quyền
├── models/             # Schema Mongoose (Sách, Kệ, Hóa đơn, Người dùng, Kho,...)
├── public/             # Tài nguyên tĩnh (CSS, JS Client, Hình ảnh bìa sách)
├── routes/             # Định tuyến API và đường dẫn trang web
├── scripts/            # Các công cụ hỗ trợ: Migration, Seed dữ liệu, Indexing, Backup
├── services/           # Dịch vụ ngoài: Email (Brevo/SMTP), MoMo Gateway, Backup Scheduler
├── utils/              # Các hàm bổ trợ xử lý số liệu, QR Code, Kệ sách
├── views/              # Giao diện giao tiếp người dùng (EJS Templates)
├── backups/            # Thư mục lưu trữ các bản sao lưu database (ZIP)
├── server.js           # Điểm khởi động chính của ứng dụng
└── package.json        # Danh sách thư viện và scripts vận hành
```

---

## 🛠️ 7. Xử Lý Các Sự Cố Thường Gặp (Troubleshooting)

1. **Lỗi kết nối MongoDB Atlas (`querySrv ENOTFOUND`):**
   * *Nguyên nhân:* DNS của nhà mạng chặn phân giải tên miền SRV của Atlas.
   * *Khắc phục:* Hệ thống đã tự động cài đặt DNS dự phòng `8.8.8.8` và `1.1.1.1` trong file `config/db.js`. Đảm bảo máy tính có kết nối mạng ổn định.

2. **Lỗi không mở được Camera khi quét mã Barcode POS:**
   * *Nguyên nhân:* Trình duyệt web trên điện thoại chỉ cho phép mở Camera trên giao diện có chứng chỉ bảo mật HTTPS hoặc `localhost`.
   * *Khắc phục:* Mở bằng đường dẫn `https://<IP-máy-tính>:4443` và chấp nhận chứng chỉ tự ký (Self-signed Certificate).

3. **Lỗi gửi email không thành công trên Hosting/Cloud:**
   * *Nguyên nhân:* Các dịch vụ Hosting miễn phí (như Render Free Tier) thường chặn các cổng SMTP 465/587.
   * *Khắc phục:* Sử dụng khóa API `BREVO_API_KEY` trong file `.env`, hệ thống sẽ tự động chuyển sang gọi HTTP API của Brevo thay vì SMTP.
