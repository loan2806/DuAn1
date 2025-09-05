# Sơ đồ Cơ sở Dữ liệu cho Hệ thống Thương mại Điện tử

Tài liệu này phác thảo sơ đồ cơ sở dữ liệu hoàn chỉnh cho hệ thống thương mại điện tử dựa trên các bảng đã cung cấp. Tôi đã ghi lại tất cả các bảng hiện có như mô tả, bổ sung thêm bảng mới để làm cho hệ thống toàn diện hơn (bao gồm bảng lịch sử trạng thái đơn hàng như yêu cầu trước, và các bảng khác như đánh giá, danh sách yêu thích và giảm giá). Tôi đã loại bỏ bảng **Product_Categories** theo yêu cầu, và thay vào đó, thêm trường **Category_id** vào bảng **Products** để xử lý mối quan hệ 1-N giữa Categories và Products (một danh mục có nhiều sản phẩm, mỗi sản phẩm thuộc một danh mục duy nhất). Tôi sẽ giải thích chi tiết từng bảng, mô tả mối quan hệ giữa các bảng, và phác thảo các luồng chức năng chính của hệ thống.

Sơ đồ giả định sử dụng cơ sở dữ liệu quan hệ (ví dụ: MySQL hoặc PostgreSQL). Khóa chính được ký hiệu là **PK**, khóa ngoại là **FK**, và gợi ý kiểu dữ liệu để rõ ràng.

## 1. Các Bảng

### Các Bảng Hiện Có (Ghi Lại Như Đã Cung Cấp)
Đây là các bảng từ mô tả của bạn, với một số sửa lỗi nhỏ về chính tả (ví dụ: "valibale" thành "variants", "addreId" thành "Address_id", "Oder_date" thành "Order_date") để rõ ràng, nhưng giữ nguyên ý nghĩa gốc.

1. **User** (Người dùng)
   - **User_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho người dùng.
   - **Name** (VARCHAR(255)): Tên đầy đủ của người dùng.
   - **Email** (VARCHAR(255), UNIQUE): Địa chỉ email của người dùng dùng để đăng nhập và liên lạc.
   - **Password** (VARCHAR(255)): Mật khẩu đã mã hóa để xác thực.
   - **Phone** (VARCHAR(20)): Số điện thoại của người dùng.
   - **Role_id** (FK, INT): Tham chiếu đến vai trò của người dùng (ví dụ: admin, khách hàng).
   - **Created_at** (TIMESTAMP): Thời gian tạo tài khoản người dùng.
   - **Mô tả**: Bảng này lưu trữ thông tin người dùng để xác thực, vai trò và hồ sơ cơ bản. Đây là bảng trung tâm cho quản lý người dùng.

2. **Role** (Vai trò)
   - **Role_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho vai trò.
   - **Role_name** (VARCHAR(50)): Tên vai trò (ví dụ: "Admin", "Customer", "Seller").
   - **Mô tả**: Định nghĩa các vai trò người dùng để kiểm soát quyền truy cập (ví dụ: admin có thể quản lý sản phẩm, khách hàng có thể đặt hàng).

3. **Address_history** (Lịch sử địa chỉ)
   - **Address_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho địa chỉ (sửa từ "addreId").
   - **User_id** (FK, INT): Tham chiếu đến người dùng sở hữu địa chỉ này.
   - **Address_name** (VARCHAR(255)): Chuỗi địa chỉ đầy đủ (ví dụ: đường phố, thành phố, mã bưu điện).
   - **Active** (BOOLEAN): Cho biết địa chỉ này có đang hoạt động không (true/false).
   - **Mô tả**: Lưu trữ lịch sử và địa chỉ hiện tại của người dùng, cho phép nhiều địa chỉ cho mục đích vận chuyển.

4. **Categories** (Danh mục)
   - **Category_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho danh mục.
   - **Category_name** (VARCHAR(100)): Tên danh mục (ví dụ: "Electronics", "Clothing").
   - **Describe** (TEXT): Mô tả danh mục.
   - **Created_at** (TIMESTAMP): Thời gian tạo danh mục.
   - **Mô tả**: Tổ chức sản phẩm thành các danh mục để dễ dàng duyệt và tìm kiếm.

5. **Products** (Sản phẩm)
   - **Product_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho sản phẩm.
   - **Product_name** (VARCHAR(255)): Tên sản phẩm.
   - **Describe** (TEXT): Mô tả sản phẩm, bao gồm chất liệu.
   - **Product_img** (VARCHAR(255)): Đường dẫn hoặc URL đến hình ảnh sản phẩm chính.
   - **Created_at** (TIMESTAMP): Thời gian thêm sản phẩm.
   - **Category_id** (FK, INT): Tham chiếu đến danh mục mà sản phẩm thuộc về (bổ sung để thay thế bảng nối, giả định mỗi sản phẩm thuộc một danh mục duy nhất).
   - **Mô tả**: Bảng cốt lõi cho danh mục sản phẩm, lưu trữ thông tin cơ bản về sản phẩm. Mối quan hệ với danh mục nay là 1-N qua trường Category_id.

6. **Product_variants** (Biến thể sản phẩm, sửa từ "Product_valibale")
   - **Id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho biến thể.
   - **Product_id** (FK, INT): Tham chiếu đến sản phẩm cơ bản.
   - **Size** (VARCHAR(50)): Kích cỡ biến thể (ví dụ: "Small", "Medium").
   - **Price** (DECIMAL(10,2)): Giá cho biến thể này.
   - **Quantity** (INT): Số lượng tồn kho khả dụng.
   - **Mô tả**: Xử lý các biến thể sản phẩm (ví dụ: kích cỡ/màu sắc khác nhau với giá và tồn kho riêng).

7. **Product_images** (Hình ảnh sản phẩm)
   - **Image_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho hình ảnh.
   - **Product_id** (FK, INT): Tham chiếu đến sản phẩm.
   - **Image_main** (VARCHAR(255)): Đường dẫn hoặc URL đến hình ảnh; có thể chỉ định nếu là hình ảnh chính.
   - **Mô tả**: Lưu trữ nhiều hình ảnh cho mỗi sản phẩm để xem chi tiết.

8. **Cart** (Giỏ hàng)
   - **Cart_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho giỏ hàng.
   - **User_id** (FK, INT): Tham chiếu đến người dùng sở hữu giỏ hàng.
   - **Mô tả**: Đại diện cho giỏ hàng của người dùng, thường một giỏ mỗi người dùng.

9. **Cart_items** (Mục trong giỏ hàng)
   - **Id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho mục giỏ hàng.
   - **Cart_id** (FK, INT): Tham chiếu đến giỏ hàng.
   - **Product_id** (FK, INT): Tham chiếu đến sản phẩm (hoặc biến thể nếu mở rộng).
   - **Quantity** (INT): Số lượng mục trong giỏ.
   - **Mô tả**: Chi tiết các mục được thêm vào giỏ hàng, cho phép nhiều sản phẩm mỗi giỏ.

10. **Orders** (Đơn hàng)
    - **Order_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho đơn hàng.
    - **User_id** (FK, INT): Tham chiếu đến người dùng đặt hàng.
    - **Order_date** (TIMESTAMP): Ngày và giờ đặt hàng (sửa từ "Oder_date").
    - **Total** (DECIMAL(10,2)): Tổng giá trị đơn hàng.
    - **Address** (VARCHAR(255)): Địa chỉ vận chuyển cho đơn hàng.
    - **Phone** (VARCHAR(20)): Số điện thoại liên lạc cho đơn hàng.
    - **Note** (TEXT): Ghi chú của khách hàng.
    - **Status** (FK, INT): Tham chiếu đến trạng thái đơn hàng (có thể là Status_id).
    - **Pay_id** (FK, INT): Tham chiếu đến phương thức thanh toán.
    - **Transaction_id** (FK, INT): Tham chiếu đến trạng thái giao dịch.
    - **Update_by** (INT): Người dùng hoặc admin cập nhật cuối cùng.
    - **Update_at** (TIMESTAMP): Thời gian cập nhật trạng thái đơn hàng.
    - **Mô tả**: Quản lý đơn hàng của khách hàng, bao gồm trạng thái và chi tiết thanh toán.

11. **Status** (Trạng thái)
    - **Status_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho trạng thái.
    - **Status_name** (VARCHAR(50)): Tên trạng thái (ví dụ: "Pending", "Shipped", "Delivered").
    - **Mô tả**: Định nghĩa các trạng thái cho đơn hàng và có thể cho chi tiết đơn hàng.

12. **Pay_money** (Phương thức thanh toán, giữ nguyên tên nhưng giải thích rõ)
    - **Pay_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho phương thức thanh toán.
    - **Pay_name** (VARCHAR(100)): Tên phương thức (ví dụ: "Credit Card", "Cash on Delivery").
    - **Mô tả**: Liệt kê các lựa chọn thanh toán khả dụng.

13. **Transaction** (Trạng thái giao dịch)
    - **Transaction_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho trạng thái giao dịch.
    - **Transaction_name** (VARCHAR(100)): Tên trạng thái giao dịch (ví dụ: "Paid", "Failed").
    - **Mô tả**: Theo dõi trạng thái của các giao dịch thanh toán.

14. **Order_details** (Chi tiết đơn hàng)
    - **Order_detail_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho dòng chi tiết.
    - **Order_id** (FK, INT): Tham chiếu đến đơn hàng.
    - **Product_id** (FK, INT): Tham chiếu đến sản phẩm.
    - **Product_name** (VARCHAR(255)): Bản sao tên sản phẩm tại thời điểm đặt hàng.
    - **Size** (VARCHAR(50)): Bản sao kích cỡ/biến thể.
    - **Price** (DECIMAL(10,2)): Bản sao giá tại thời điểm đặt hàng.
    - **Quantity** (INT): Số lượng đặt hàng.
    - **Status_id** (FK, INT): Tham chiếu đến trạng thái cho mục này (ví dụ: nếu hoàn thành một phần).
    - **Mô tả**: Phân tích đơn hàng thành các mục riêng lẻ để theo dõi chính xác.

### Các Bảng Mới (Bổ Sung Để Hoàn Thiện)
Dựa trên các mối quan hệ đã đề cập (ví dụ: user-review, product-review), tôi đã bổ sung các bảng sau để lấp đầy khoảng trống trong hệ thống thương mại điện tử tiêu chuẩn. Bảng **Product_Categories** đã được loại bỏ, và mối quan hệ danh mục-sản phẩm được xử lý qua trường Category_id trong **Products**.

1. **Reviews** (Đánh giá)
   - **Review_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho đánh giá.
   - **User_id** (FK, INT): Tham chiếu đến người dùng viết đánh giá.
   - **Product_id** (FK, INT): Tham chiếu đến sản phẩm được đánh giá.
   - **Rating** (INT): Điểm đánh giá (ví dụ: 1-5).
   - **Comment** (TEXT): Nội dung đánh giá.
   - **Created_at** (TIMESTAMP): Thời gian gửi đánh giá.
   - **Mô tả**: Lưu trữ đánh giá và điểm số từ khách hàng cho sản phẩm, giúp tạo vòng phản hồi.

2. **Wishlist** (Danh sách yêu thích)
   - **Wishlist_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất.
   - **User_id** (FK, INT): Tham chiếu đến người dùng.
   - **Product_id** (FK, INT): Tham chiếu đến sản phẩm yêu thích.
   - **Added_at** (TIMESTAMP): Thời gian thêm vào.
   - **Mô tả**: Cho phép người dùng lưu sản phẩm để mua sau.

3. **Discounts** (Giảm giá)
   - **Discount_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất.
   - **Product_id** (FK, INT, NULLABLE): Tham chiếu đến sản phẩm (nếu dành riêng cho sản phẩm).
   - **Category_id** (FK, INT, NULLABLE): Tham chiếu đến danh mục (nếu áp dụng cho danh mục).
   - **Discount_percentage** (DECIMAL(5,2)): Phần trăm giảm giá.
   - **Start_date** (DATE): Ngày bắt đầu giảm giá.
   - **End_date** (DATE): Ngày kết thúc giảm giá.
   - **Mô tả**: Quản lý giảm giá và khuyến mãi để ảnh hưởng động đến giá cả.

4. **Order_status_history** (Lịch sử trạng thái đơn hàng)
   - **History_id** (PK, INT, AUTO_INCREMENT): Mã định danh duy nhất cho bản ghi lịch sử.
   - **Order_id** (FK, INT): Tham chiếu đến đơn hàng.
   - **Status_id** (FK, INT): Tham chiếu đến trạng thái mới (từ bảng Status).
   - **Changed_at** (TIMESTAMP): Thời gian thay đổi trạng thái.
   - **Changed_by** (FK, INT): Tham chiếu đến người dùng hoặc admin thực hiện thay đổi (từ bảng User).
   - **Note** (TEXT, NULLABLE): Ghi chú về lý do thay đổi (tùy chọn).
   - **Mô tả**: Bảng này theo dõi toàn bộ lịch sử thay đổi trạng thái của mỗi đơn hàng, giúp kiểm tra và báo cáo (ví dụ: từ "Pending" sang "Shipped" lúc nào, do ai). Điều này hữu ích cho việc kiểm toán và giải quyết tranh chấp.

## 2. Mối Quan Hệ Giữa Các Bảng
Mối quan hệ dựa trên thông tin đã cung cấp và mở rộng logic. Độ phức tạp: 1-1 (một-một), 1-N (một-nhiều), N-M (nhiều-nhiều). Bảng Product_Categories đã bị loại bỏ, nên mối quan hệ Categories - Products nay là 1-N qua trường Category_id trong Products.

- **User - Role**: 1-N (Một vai trò có nhiều người dùng; mỗi người dùng có một vai trò). FK: User.Role_id → Role.Role_id.
- **User - Address_history**: 1-N (Một người dùng có nhiều địa chỉ). FK: Address_history.User_id → User.User_id.
- **User - Cart**: 1-1 (Một người dùng có một giỏ hàng). FK: Cart.User_id → User.User_id.
- **User - Orders**: 1-N (Một người dùng có nhiều đơn hàng). FK: Orders.User_id → User.User_id.
- **User - Reviews**: 1-N (Một người dùng có nhiều đánh giá). FK: Reviews.User_id → User.User_id.
- **User - Wishlist**: 1-N (Một người dùng có nhiều mục yêu thích). FK: Wishlist.User_id → User.User_id.
- **Categories - Products**: 1-N (Một danh mục có nhiều sản phẩm; mỗi sản phẩm thuộc một danh mục). FK: Products.Category_id → Categories.Category_id.
- **Products - Product_variants**: 1-N (Một sản phẩm có nhiều biến thể). FK: Product_variants.Product_id → Products.Product_id.
- **Products - Product_images**: 1-N (Một sản phẩm có nhiều hình ảnh). FK: Product_images.Product_id → Products.Product_id.
- **Products - Reviews**: 1-N (Một sản phẩm có nhiều đánh giá). FK: Reviews.Product_id → Products.Product_id.
- **Products - Cart_items**: 1-N (Một sản phẩm trong nhiều mục giỏ hàng). FK: Cart_items.Product_id → Products.Product_id (hoặc biến thể nếu mở rộng).
- **Products - Order_details**: 1-N (Một sản phẩm trong nhiều chi tiết đơn hàng). FK: Order_details.Product_id → Products.Product_id.
- **Products - Wishlist**: 1-N (Một sản phẩm trong nhiều danh sách yêu thích). FK: Wishlist.Product_id → Products.Product_id.
- **Products - Discounts**: 1-N (Một sản phẩm có nhiều giảm giá). FK: Discounts.Product_id → Products.Product_id.
- **Categories - Discounts**: 1-N (Một danh mục có nhiều giảm giá). FK: Discounts.Category_id → Categories.Category_id.
- **Cart - Cart_items**: 1-N (Một giỏ hàng có nhiều mục). FK: Cart_items.Cart_id → Cart.Cart_id.
- **Orders - Order_details**: 1-N (Một đơn hàng có nhiều chi tiết). FK: Order_details.Order_id → Orders.Order_id.
- **Orders - Status**: 1-N (Một trạng thái cho nhiều đơn hàng). FK: Orders.Status → Status.Status_id.
- **Orders - Payment_methods** (Pay_money): 1-N (Một phương thức cho nhiều đơn hàng). FK: Orders.Pay_id → Pay_money.Pay_id.
- **Orders - Transaction**: 1-N (Một trạng thái giao dịch cho nhiều đơn hàng). FK: Orders.Transaction_id → Transaction.Transaction_id.
- **Order_details - Status**: 1-N (Một trạng thái cho nhiều chi tiết). FK: Order_details.Status_id → Status.Status_id.
- **Orders - Order_status_history**: 1-N (Một đơn hàng có nhiều bản ghi lịch sử). FK: Order_status_history.Order_id → Orders.Order_id.
- **Order_status_history - Status**: 1-N (Một trạng thái trong nhiều bản ghi lịch sử). FK: Order_status_history.Status_id → Status.Status_id.
- **Order_status_history - User**: 1-N (Một người dùng thay đổi nhiều lịch sử). FK: Order_status_history.Changed_by → User.User_id.

## 3. Các Luồng Chức Năng
Dưới đây là các luồng chức năng chính (luồng đi) cho các tính năng phổ biến trong hệ thống thương mại điện tử. Mỗi luồng mô tả quy trình từng bước, liên quan đến các bảng và mối quan hệ liên quan. Việc loại bỏ Product_Categories không ảnh hưởng lớn đến luồng, nhưng trong duyệt sản phẩm, nay sử dụng JOIN trực tiếp qua Category_id.

### Đăng Ký và Đăng Nhập Người Dùng
1. Người dùng gửi tên, email, mật khẩu, số điện thoại.
2. Hệ thống mã hóa mật khẩu và chèn vào bảng **User** với Role_id mặc định (ví dụ: "Customer").
3. Tạo giỏ hàng mới cho người dùng (chèn vào **Cart** với User_id).
4. Tùy chọn, thêm địa chỉ mặc định vào **Address_history**.
5. Để đăng nhập: Truy vấn **User** theo email, xác thực mật khẩu đã mã hóa.

### Duyệt và Tìm Kiếm Sản Phẩm
1. Người dùng truy vấn danh mục: SELECT từ **Categories**.
2. Hiển thị sản phẩm: JOIN **Products** với **Categories** (qua Category_id), **Product_variants** (giá/tồn kho), **Product_images** (hình ảnh), và tổng hợp từ **Reviews** (điểm trung bình).
3. Áp dụng bộ lọc (ví dụ: theo danh mục, giá): Sử dụng WHERE trên các bảng nối, ví dụ WHERE Products.Category_id = cụ thể.
4. Nếu có giảm giá: JOIN **Discounts** và áp dụng giá nếu ngày hiện tại nằm giữa Start_date và End_date.

### Thêm Vào Giỏ Hàng
1. Người dùng chọn sản phẩm/biến thể và số lượng.
2. Kiểm tra tồn kho trong **Product_variants**.
3. Chèn/Cập nhật **Cart_items** (liên kết với **Cart** của người dùng qua User_id).
4. Cập nhật tổng giỏ hàng (tính từ Price * Quantity của các mục).

### Thanh Toán và Đặt Đơn Hàng
1. Người dùng chọn địa chỉ từ **Address_history** (các địa chỉ active) hoặc thêm mới.
2. Chọn phương thức thanh toán từ **Pay_money**.
3. Tính tổng (từ mục giỏ hàng, áp dụng giảm giá).
4. Chèn vào **Orders** (User_id, Order_date=NOW(), Total, Address, Phone, Note, Status=ban đầu (ví dụ: "Pending"), Pay_id, Transaction_id="Pending").
5. Với mỗi mục giỏ: Chèn vào **Order_details** (bản sao Product_name, Size, Price, Quantity; Status_id=ban đầu).
6. Cập nhật số lượng **Product_variants** (trừ số lượng đặt hàng).
7. Xóa **Cart_items** của người dùng.
8. Xử lý thanh toán: Cập nhật Transaction_id nếu thành công.
9. Chèn bản ghi đầu tiên vào **Order_status_history** với Status_id ban đầu, Changed_at=NOW(), Changed_by=User_id.

### Quản Lý Đơn Hàng (Admin/Người Dùng)
1. Người dùng xem đơn hàng: SELECT từ **Orders** JOIN **Order_details** WHERE User_id = người dùng hiện tại.
2. Admin cập nhật trạng thái: UPDATE **Orders** SET Status=mới, Update_by=admin_id, Update_at=NOW().
3. Đồng thời, chèn bản ghi mới vào **Order_status_history** (Order_id, Status_id=mới, Changed_at=NOW(), Changed_by=admin_id, Note nếu có).
4. Nếu trạng thái thay đổi (ví dụ: "Shipped"), cập nhật **Order_details** Status_id tương ứng.
5. Xem lịch sử: SELECT từ **Order_status_history** JOIN **Status** WHERE Order_id = cụ thể, sắp xếp theo Changed_at.

### Viết Đánh Giá
1. Sau khi giao hàng (kiểm tra **Orders.Status** = "Delivered").
2. Người dùng gửi điểm số/bình luận cho sản phẩm trong **Order_details**.
3. Chèn vào **Reviews** (User_id, Product_id, Rating, Comment, Created_at=NOW()).

### Thêm Vào Danh Sách Yêu Thích
1. Người dùng chọn sản phẩm.
2. Chèn vào **Wishlist** (User_id, Product_id, Added_at=NOW()).
3. Xem danh sách: SELECT sản phẩm JOIN **Wishlist** WHERE User_id = hiện tại.

### Quản Lý Sản Phẩm (Admin)
1. Thêm danh mục: INSERT vào **Categories**.
2. Thêm sản phẩm: INSERT vào **Products** (với Category_id), sau đó biến thể vào **Product_variants**, hình ảnh vào **Product_images**.
3. Thêm giảm giá: INSERT vào **Discounts** liên kết với sản phẩm hoặc danh mục.

Sơ đồ và luồng này cung cấp nền tảng vững chắc. Để triển khai, hãy đảm bảo chỉ mục đúng trên các FK và sử dụng giao dịch cho các hoạt động nguyên tử (ví dụ: đặt hàng). Nếu cần, mở rộng với trigger hoặc view.