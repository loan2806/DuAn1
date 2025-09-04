# 📦 Database Design cho Hệ thống Bán Hàng

## 1. Bảng `User`
| Trường       | Kiểu dữ liệu | Mô tả |
|--------------|--------------|-------|
| <span style="color:red">User_id</span>      | PK           | ID người dùng |
| Name         | VARCHAR      | Họ tên |
| Email        | VARCHAR      | Email |
| Password     | VARCHAR      | Mật khẩu |
| Phone        | VARCHAR      | SĐT |
| Address      | TEXT         | Địa chỉ mặc định |
| Role         | INT          | 0 - admin, 1 - nhân viên, 2 - thành viên |
| Created_at   | DATETIME     | Thời gian tạo |

---

## 2. Bảng `Role`
| Trường   | Kiểu dữ liệu | Mô tả |
|----------|--------------|-------|
| <span style="color:red">Role_id</span>  | PK           | ID phân quyền |
| <span style="color:blue">User_id</span>  | FK           | Tham chiếu `User` |

---

## 3. Bảng `Address_history`
| Trường       | Kiểu dữ liệu | Mô tả |
|--------------|--------------|-------|
| <span style="color:red">Id</span>           | PK           | ID địa chỉ |
| <span style="color:blue">User_id</span>      | FK           | Người dùng |
| Address_name | TEXT         | Địa chỉ |
| Active       | BOOLEAN      | Địa chỉ hiện tại |

---

## 4. Bảng `Categories`
| Trường        | Kiểu dữ liệu | Mô tả |
|---------------|--------------|-------|
| <span style="color:red">Category_id</span>   | PK           | ID danh mục |
| Category_name | VARCHAR      | Tên danh mục |
| Describe      | TEXT         | Mô tả |
| Created_at    | DATETIME     | Ngày tạo |
| Update_by       | INT          | Người cập nhật |
| Update_at       | DATETIME     | Thời gian cập nhật |


---

## 5. Bảng `Products`
| Trường        | Kiểu dữ liệu | Mô tả |
|---------------|--------------|-------|
| <span style="color:red">Product_id</span>    | PK           | ID sản phẩm |
| Product_name  | VARCHAR      | Tên sản phẩm |
| Describe      | TEXT         | Chất liệu / mô tả |
| Product_img   | VARCHAR      | Ảnh đại diện |
| Created_at    | DATETIME     | Ngày tạo |
| <span style="color:blue">Category_id</span>   | FK           | Tham chiếu `Categories` |
| Update_by       | INT          | Người cập nhật |
| Update_at       | DATETIME     | Thời gian cập nhật |


---

## 6. Bảng `Product_valibale`
| Trường     | Kiểu dữ liệu | Mô tả |
|------------|--------------|-------|
| <span style="color:red">Id</span>         | PK           | ID biến thể |
| <span style="color:blue">Product_id</span> | FK           | Sản phẩm |
| Size       | VARCHAR      | Kích thước |
| Price      | DECIMAL      | Giá |
| Quantity   | INT          | Số lượng |
| Update_by       | INT          | Người cập nhật |


---

## 7. Bảng `Product_images`
| Trường     | Kiểu dữ liệu | Mô tả |
|------------|--------------|-------|
| <span style="color:red">Image_id</span>   | PK           | ID ảnh |
| <span style="color:blue">Product_id</span> | FK           | Sản phẩm |
| Image_main | VARCHAR      | Link ảnh |
| Update_by       | INT          | Người cập nhật |


---

## 8. Bảng `Cart`
| Trường   | Kiểu dữ liệu | Mô tả |
|----------|--------------|-------|
| <span style="color:red">Cart_id</span>  | PK           | ID giỏ hàng |
| <span style="color:blue">User_id</span>  | FK           | Người dùng |

---

## 9. Bảng `Cart_items`
| Trường     | Kiểu dữ liệu | Mô tả |
|------------|--------------|-------|
| <span style="color:red">Id</span>         | PK           | ID chi tiết giỏ |
| <span style="color:blue">Cart_id</span>    | FK           | Giỏ hàng |
| <span style="color:blue">Product_id</span> | FK           | Sản phẩm |
| Update_at       | DATETIME     | Thời gian cập nhật |


---

## 10. Bảng `Orders`
| Trường          | Kiểu dữ liệu | Mô tả |
|-----------------|--------------|-------|
| <span style="color:red">Order_id</span>        | PK           | ID đơn hàng |
| <span style="color:blue">User_id</span>         | FK           | Người đặt |
| Order_date      | DATETIME     | Ngày đặt |
| Total           | DECIMAL      | Tổng tiền |
| Address         | TEXT         | Địa chỉ nhận |
| Phone           | VARCHAR      | SĐT |
| Note            | TEXT         | Ghi chú |
| Status          | INT          | Trạng thái |
| Payment_method  | INT          | 0 - CK, 1 - TM |
| Transaction     | INT          | 0 - Chưa TT, 1 - Đã TT, 2 - Hoàn tiền, 3 - Thất bại |
| Update_by       | INT          | Người cập nhật |
| Update_at       | DATETIME     | Thời gian cập nhật |

---

## 11. Bảng `Status`
| Trường     | Kiểu dữ liệu | Mô tả |
|------------|--------------|-------|
| <span style="color:red">Status_id</span>  | PK           | ID trạng thái |
| <span style="color:blue">Order_id</span>   | FK           | Đơn hàng |

---

## 12. Bảng `Pay_money` (Phương thức thanh toán)
| Trường   | Kiểu dữ liệu | Mô tả |
|----------|--------------|-------|
| <span style="color:red">Pay_id</span>   | PK           | ID phương thức |
| Role     | INT          | 0 - CK, 1 - TM |
| <span style="color:blue">Order_id</span> | FK           | Đơn hàng |

---

## 13. Bảng `Transaction`
| Trường         | Kiểu dữ liệu | Mô tả |
|----------------|--------------|-------|
| <span style="color:red">Transaction_id</span> | PK           | ID giao dịch |
| <span style="color:blue">Pay_id</span>         | FK           | Phương thức thanh toán |

---

## 14. Bảng `Order_details`
| Trường          | Kiểu dữ liệu | Mô tả |
|-----------------|--------------|-------|
| <span style="color:red">Order_detail_id</span> | PK           | ID chi tiết |
| <span style="color:blue">Order_id</span>        | FK           | Đơn hàng |
| <span style="color:blue">Product_id</span>      | FK           | Sản phẩm |
| Product_name    | VARCHAR      | Tên sản phẩm |
| Size            | VARCHAR      | Kích thước |
| Price           | DECIMAL      | Giá |
| Quantity        | INT          | Số lượng |

---

# 🔗 Quan hệ chính
- **1 User – n Orders**
- **1 User – n Reviews**
- **1 Category – n Products**
- **1 Product – n Variants (Product_valibale)**
- **1 Product – n Images**
- **1 Product – n Reviews**
- **1 Product – n Order_details**
- **1 Cart – n Cart_items**
