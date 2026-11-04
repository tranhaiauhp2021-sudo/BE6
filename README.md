# MyStore — ASP.NET MVC5 / Entity Framework (Customer Site, bài #13–#16)

> **Mã nguồn bổ sung (starter/overlay) cho project Visual Studio MVC5**, không phải một solution `.sln` có thể chạy độc lập. Vì đề bài chỉ kèm PowerPoint và file SQL, chưa có project của sinh viên hoặc file `.edmx` được Visual Studio sinh tự động, cần **ghép mã nguồn này vào project MVC5 của chính bạn** trước khi nộp GitHub.

## Phạm vi thực hiện

- **Metadata & Partial Class:** 6 class trùng 6 bảng trong `SQL/database2024_original.txt`: `User`, `Customer`, `Category`, `Product`, `Order`, `OrderDetail`.
- **Home:** banner, thanh tìm kiếm theo tên/mô tả/danh mục; 10 sản phẩm nổi bật theo số lượt xuất hiện trong đơn hàng; danh sách 20 sản phẩm ít lượt mua nhất, phân trang 6 sản phẩm/trang. Theo slide của giảng viên, phần này đặt tên **Sản phẩm mới** dù cách truy vấn là *ít lượt mua nhất*.
- **Product Detail:** ảnh, tên, mô tả, giá, số lượt mua, chọn số lượng, thêm vào giỏ, 8 sản phẩm cùng danh mục, 8 sản phẩm bán chạy trong danh mục, Top Deals phân trang 3 sản phẩm/trang.
- **Bài #16 — Giỏ hàng (cơ bản + nâng cao):** `CartItem.cs`, `Cart.cs`, `CartService.cs` dùng `Session`; `CartController` có Index/AddItem/UpdateQuantity/RemoveItem/ClearCart/UpdateSelection; `Views/Cart/Index.cshtml` hiển thị sản phẩm theo danh mục, ô chọn sản phẩm, hình ảnh, số lượng, giá và tổng tiền các mặt hàng được chọn; `Views/Cart/_PVSimilarProduct.cshtml` gợi ý sản phẩm tương tự, phân trang 6 sản phẩm/trang.
- **Bổ trợ:** menu danh mục, trang danh sách sản phẩm theo danh mục và layout giao diện khách hàng.

**Chưa thực hiện:** thanh toán/checkout, đăng ký và đăng nhập, lịch sử đơn hàng, phần CRUD Admin. Đây là các tính năng trong đề án tổng thể, chưa nằm trong phần thực hành #13–#16 này. Giỏ hàng là bản nâng cao dùng Session, chưa ghi thành Order trong database. Nút thanh toán được vô hiệu hóa có chủ đích để tránh liên kết đến trang chưa làm.

## Thiết lập trong Visual Studio (Windows)

1. Cài **Visual Studio 2022** với workload **ASP.NET and web development**, .NET Framework targeting pack phù hợp, và **SQL Server + SSMS**.
2. Tạo project **ASP.NET Web Application (.NET Framework) → MVC**, chọn .NET Framework 4.8 (hoặc phiên bản dự án đang dùng). Đặt tên project **`MyStore`** để khớp namespace trong các file. Nếu project của bạn có tên khác (ví dụ `MaSV_MyStore`) hãy dùng Find/Replace thay `MyStore.Models`, `MyStore.Controllers` và `MyStoreEntities` tương ứng.
3. Tạo database **MyStore** trong SQL Server. Mở file `SQL/database2024_original.txt` bằng SSMS, chọn database MyStore và chạy câu lệnh SQL (chưa có `CREATE DATABASE` trong file gốc).
4. Chuột phải thư mục `Models` → **Add → New Item → ADO.NET Entity Data Model** → **EF Designer from database** → tạo model từ 6 bảng trong MyStore. Đặt Entity Container là **`MyStoreEntities`** và namespace mô hình cùng với **`MyStore.Models`**. Hãy kiểm tra lớp được EF sinh có đúng `Category`, `Product`, `User`, `Customer`, `Order`, `OrderDetail`, các navigation property `Product.Category`, `Product.OrderDetails` và `DbSet Products/Categories`.
5. Cài NuGet **EntityFramework (EF6)** và **PagedList.Mvc** (kèm thư viện PagedList theo dependency) qua Manage NuGet Packages. Nếu có sẵn thì không cần cài lại.
6. Sao chép các thư mục `Controllers`, `Models/Metadata`, `Models/ViewModel`, `Views/Home`, `Views/Cart`, `Views/Shared` và `Content` vào project. Với `Controllers/HomeController.cs`, dùng bản này thay bản mặc định **sau khi đã lưu lại code muốn giữ**.
7. Đảm bảo namespace C# do `*.edmx` tự sinh và tên DbContext đúng. Trong `Web.config`, connection string `MyStoreEntities` sẽ do Entity Data Model wizard tạo — đừng đưa thông tin máy chủ/mật khẩu thật lên GitHub.
8. Khởi chạy project với IIS Express: kiểm tra Home (`/Home/Index`), search, phân trang, chi tiết (`/Home/ProductDetail/1`), thêm giỏ, cập nhật số lượng, xóa 1/xóa toàn bộ, ô chọn tính tiền, nhóm theo danh mục, gợi ý sản phẩm tương tự. Xem `docs/BAI16_GIO_HANG.md` để kiểm thử cụ thể.
9. Chỉ nộp repository khi trong đó đã có đủ file **`.sln`, `.csproj`, các class từ EF/EDMX hoặc cấu trúc sinh model hợp lệ, controllers, views, scripts và hướng dẫn database**.

## Các package và kỹ thuật

- C#, ASP.NET MVC5, Razor Views, Entity Framework 6 Database First, LINQ, SQL Server, `PagedList.Mvc`, `Session`, `ViewModel`, `PartialView`.
- Annotation: `MetadataType`, `DisplayName`, `Required`, `EmailAddress`, `Phone`, `Range`, `DisplayFormat`.
- `partial class` giữ các annotation tách khỏi file EF tạo tự động (tránh mất code khi update model từ database).

## Thư mục

```text
MyStore/                               (project do Visual Studio tạo)
├── MyStore.sln                        (có khi nằm ngoài thư mục project)
├── MyStore/MyStore.csproj
├── MyStore/Models/MyStore.edmx         (tạo từ Database First)
├── MyStore/Models/Metadata/           (copy từ starter)
├── MyStore/Models/ViewModel/          (copy từ starter)
├── MyStore/Controllers/               (copy từ starter)
├── MyStore/Views/Home/                (copy từ starter)
├── MyStore/Views/Cart/                (copy từ starter)
├── MyStore/Views/Shared/              (copy từ starter)
├── MyStore/Content/                   (copy từ starter)
├── SQL/database2024_original.txt
└── README.md
```

## Git / GitHub

Từ **thư mục gốc chứa file `.sln`** (mở terminal/Git Bash):

```bash
git init
git add .
git commit -m "Add session cart, cart view models and similar product partial"
git branch -M main
git remote add origin https://github.com/USERNAME/REPOSITORY.git
git push -u origin main
```

Thay `USERNAME/REPOSITORY` bằng repository bạn đã tạo trên GitHub. Nếu remote `origin` đã tồn tại thì không chạy `git remote add origin` lần nữa.

**Cực kỳ quan trọng:** Visual Studio / ASP.NET sẽ sinh `Web.config` chứa connection string. Chỉ push `Web.config` khi không có tên máy/mật khẩu thật hoặc dữ liệu bí mật. Nếu lỡ commit mật khẩu, cần thay mật khẩu và xóa thông tin đó khỏi lịch sử Git, không chỉ sửa file hiện tại.

## Lưu ý học thuật

- Thống kê "bán chạy" trong slide đang dựa vào `p.OrderDetails.Count()` (số **dòng** order detail) chứ chưa phải tổng `Quantity`. Code này bám đúng cách tính trong slide.
- SQL mẫu đang lưu `Password` dưới dạng `NCHAR(50)`; **không nên dùng mật khẩu thuần trong môi trường thực tế**. Chưa cài chức năng authentication trong starter.
- Chưa có solution thực tế của người học và chưa thể chạy `Build` trên môi trường Windows/Visual Studio của người học; bạn cần build và test trước khi nộp.
- **Quan trọng:** thay `CartController.cs` và `HomeController.cs` của starter cũ cùng lúc. `CartItemVM.cs` của bản trước đã được thay bằng `CartItem.cs` + `Cart.cs` + `CartService.cs`; không dùng song song hai kiểu giỏ hàng trong Session.
