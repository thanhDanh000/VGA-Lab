# VGA LAB — AI PC Builder

Trang web review VGA NVIDIA/AMD, kết hợp chuẩn bị yêu cầu build PC để gửi sang Google Gemini.

## Dùng ngay (không cần tài khoản)

1. Mở `index.html` bằng trình duyệt, hoặc đặt lên GitHub Pages.
2. Lọc NVIDIA (GTX, RTX 20/30/40/50) hoặc AMD (RX 5000/6000/7000/8000/9000), chọn card, bấm **Build PC với AI**.
3. Nhập ngân sách, game, độ phân giải, FPS, linh kiện đã có. Prompt được tạo tự động.
4. Bấm **Sao chép & mở Gemini**. Khi Gemini mở ra, dán nội dung (`Ctrl+V`) và gửi.
5. Bấm **Lưu cấu hình** để lưu yêu cầu build. Xem lại ở mục **Build đã lưu**. Bấm **Reset phiên build** để đưa form về mặc định mà không xóa bản đã lưu.
6. Thêm VGA riêng bằng **Thêm mẫu VGA**, hoặc quản lý xóa tại **Build đã lưu**.

Dữ liệu ở **chế độ khách** chỉ nằm trong localStorage của trình duyệt/thiết bị đó; xóa dữ liệu trang web sẽ khiến các build và VGA tự thêm bị mất. Dữ liệu khách **không tự động chuyển** sang tài khoản.

**Lưu ý**: Google Gemini không hỗ trợ chính thức nhận prompt sẵn từ một URL khác. Website **không gửi prompt tự động**, không đọc kết quả trả lời, và không thể xóa lịch sử/cuộc trò chuyện bên Gemini. "Reset" chỉ làm mới bản nháp trong VGA LAB. Review trong bản này được lưu cục bộ theo trình duyệt, không phải hệ thống đánh giá công khai nhiều người.

## Bật tài khoản Google thật và đồng bộ qua Firebase

Để các tài khoản Google khác nhau tự thêm VGA và lưu build trên nhiều thiết bị, chủ website phải cấu hình Firebase **trước khi đăng web**:

1. Mở [Firebase Console](https://console.firebase.google.com/), tạo một dự án và đăng ký **Web App**.
2. Vào **Authentication → Sign-in method → Google** và bật đăng nhập Google.
3. Vào **Firestore Database** và tạo database. Trước khi website có người dùng, mở tab **Rules**, thay nội dung bằng file `firestore.rules` trong thư mục ZIP rồi bấm Publish. Rules chỉ cho tài khoản đọc/ghi trong `users/{uid}/builds` và `users/{uid}/gpus` của chính người đó.
4. Mở `index.html` bằng editor, tìm chính xác đoạn:
   ```js
   const FIREBASE_CONFIG = {apiKey:'',authDomain:'',projectId:'',appId:''};
   ```
   Thay giá trị bằng **Firebase configuration của Web App** do Firebase cung cấp (các trường tối thiểu: `apiKey`, `authDomain`, `projectId`, `appId`; các trường khác có thể giữ thêm).
5. Vào **Firebase Authentication → Settings → Authorized domains**, thêm tên miền website thực tế, ví dụ `ten-user.github.io` hoặc tên miền riêng. Với local development có thể thêm `localhost` nếu Firebase cho phép. Đăng nhập không hoạt động qua `file://`; hãy chạy bằng HTTPS (GitHub Pages) hoặc local server.
6. Mở web ở domain đã cho phép, bấm **Khách / Đăng nhập → Tiếp tục với Google**, chọn tài khoản. Mỗi uid có thư mục dữ liệu riêng trong Firestore.

**Bảo mật:** Firebase Web config không phải mật khẩu quản trị nhưng phải dùng Firestore Security Rules phù hợp. **Không** dùng service account key, private key hay Gemini API key trong mã HTML công khai. Không sửa rules thành `allow read, write: if true`.

## Đưa lên GitHub Pages

1. Tạo repository mới trên GitHub.
2. Upload `index.html` (và có thể cả `README.md`, `firestore.rules`) vào thư mục gốc của repository.
3. Vào **Settings → Pages → Deploy from a branch**, chọn nhánh `main`, thư mục `/ (root)`, Save.
4. Mở đường dẫn GitHub Pages được GitHub cung cấp. Với chức năng đăng nhập Google, nhớ thêm hostname GitHub Pages vào Firebase Authorized domains.

## Giới hạn dữ liệu demo

- Card và VRAM trong danh mục chỉ dùng để lọc và làm đầu vào tư vấn; không có giá bán hoặc FPS real-time.
- Dòng AMD RX 8000 được giữ làm bộ lọc theo yêu cầu nhưng không có mẫu Radeon RX 8000 desktop phổ thông chính thức trong dữ liệu mặc định.
- Gemini sẽ tự đề xuất cấu hình sau khi người dùng dán prompt và gửi. VGA LAB không tự gọi Gemini API và không ghi lại kết quả trả lời.
- Lưu cấu hình gồm lựa chọn của người dùng và bản prompt, **không** lưu đoạn chat bên Gemini.
- Trước khi dùng cho cộng đồng lớn, nên bổ sung chống spam, kiểm duyệt nội dung đánh giá, chỉ số hiệu năng/giá có nguồn xác minh và giới hạn dung lượng Firestore.

## Cấu trúc file

- `index.html` — toàn bộ giao diện + JavaScript, dùng độc lập.
- `firestore.rules` — quy tắc đọc/ghi Firestore theo UID, cần chủ trang publish bằng Firebase Console.
- `README.md` — tài liệu sử dụng và triển khai.
