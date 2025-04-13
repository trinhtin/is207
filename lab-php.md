# LAB01: LÀM QUEN VỚi PHP VÀ Xử LÝ BIỂU MẪU

---

### 1. MỤc tiêu
- Thực hành nhúng PHP vào tập tin HTML.
- Sử dụng biến, hằng, lệnh echo, hàm date().
- Xử lý biểu mẫu với GET và POST.

---

### 2. Thực hành

#### BT1: Hello PHP
Tạo file `hello.php`:
```php
<?php
  $name = "Sinh viên";
  echo "<h2>Chào mừng bạn, $name!</h2>";
  echo "Hôm nay là: " . date("d/m/Y - H:i:s");
?>
```

**Yêu cầu:**
- Thay đổi giá trị biến `$name`.
- In ra dòng thông báo ngày giờ hiện tại.

---

#### BT2: Xử lý form GET
Tạo file `form_get.php`:
```html
<form action="handle_get.php" method="GET">
  Nhập tên: <input type="text" name="name">
  <input type="submit" value="Gửi">
</form>
```

Tạo file `handle_get.php`:
```php
<?php
  if (isset($_GET['name'])) {
    $name = $_GET['name'];
    echo "Chào bạn, $name!";
  } else {
    echo "Vui lòng nhập tên.";
  }
?>
```

**Yêu cầu mở rộng:**
- Thêm input radio giới tính (nam/nữ).
- Hiển thị lời chào đủ: "Chào bạn [tên], giới tính: [nam/nữ]".

---

#### BT3: Xử lý form POST
Tạo file `form_post.php`:
```html
<form action="handle_post.php" method="POST">
  Nhập email: <input type="email" name="email">
  <input type="submit" value="Gửi">
</form>
```

Tạo file `handle_post.php`:
```php
<?php
  if (isset($_POST['email'])) {
    $email = $_POST['email'];
    echo "Email bạn nhập: $email";
  } else {
    echo "Chưa nhập email.";
  }
?>
```

---

### 3. Yêu cầu nộp bài
- 4 file: `hello.php`, `form_get.php`, `handle_get.php`, `form_post.php`, `handle_post.php`
- Chụp màn hình kết quả và nộp qua Moodle hoặc Google Drive.


-------------------------------------------------------------------------------------------------------------
# LAB02: MẢNG VÀ ĐIỀU KIỆN

---

### 1. Mục tiêu
- Sử dụng lệnh rẻ nhánh (if/else, switch).
- Khai báo mảng và duyệt mảng.
- Xử lý form có nhiều đối tượng lựa chọn.

---

### 2. Thực hành

#### Bài 1: Lời chào theo thời gian
Tạo file `greeting.php`:
```php
<?php
  $h = date("H");
  if ($h < 12) {
    echo "Chào buổi sáng!";
  } elseif ($h < 18) {
    echo "Chào buổi chiều!";
  } else {
    echo "Chào buổi tôi!";
  }
?>
```

#### Bài 2: Khảo sát sở thích
Tạo file `survey.php`:
```html
<form action="handle_survey.php" method="POST">
  <p>Chọn các môn bạn yêu thích:</p>
  <input type="checkbox" name="subjects[]" value="Toán">Toán<br>
  <input type="checkbox" name="subjects[]" value="Văn">Văn<br>
  <input type="checkbox" name="subjects[]" value="Anh">Anh<br>
  <input type="checkbox" name="subjects[]" value="Tin">Tin<br>
  <input type="submit" value="Gửi">
</form>
```

Tạo file `handle_survey.php`:
```php
<?php
  if (!empty($_POST['subjects'])) {
    $subs = $_POST['subjects'];
    echo "Bạn đã chọn: <ul>";
    foreach ($subs as $s) {
      echo "<li>$s</li>";
    }
    echo "</ul>";
  } else {
    echo "Bạn chưa chọn môn nào.";
  }
?>
```

#### Bài 3: Đánh giá sao dịch vụ
Tạo file `rate.php`:
```html
<form action="handle_rate.php" method="POST">
  <p>Chất lượng dịch vụ:</p>
  <input type="radio" name="rate" value="1">1 sao<br>
  <input type="radio" name="rate" value="2">2 sao<br>
  <input type="radio" name="rate" value="3">3 sao<br>
  <input type="radio" name="rate" value="4">4 sao<br>
  <input type="radio" name="rate" value="5">5 sao<br>
  <input type="submit" value="Đánh giá">
</form>
```

Tạo file `handle_rate.php`:
```php
<?php
  if (isset($_POST['rate'])) {
    $rate = $_POST['rate'];
    echo "Bạn đã đánh giá: $rate sao";
  } else {
    echo "Vui lòng chọn mức đánh giá.";
  }
?>
```

---

### 3. Yêu cầu nộp bài
- Tổng các file: `greeting.php`, `survey.php`, `handle_survey.php`, `rate.php`, `handle_rate.php`
- Chụp màn hình kết quả và nộp qua Moodle hoặc Google Drive.

-------------------------------------------------------------------------------------------------------------
# LAB03: KẾT NỐI CSDL VÀ HIỂN THỊ DANH SÁCH SẢN PHẨM

---

### 1. Mục tiêu
- Kết nối đến cơ sở dữ liệu MySQL.
- Hiển thị danh sách sản phẩm trong trang `managepost.html`.

---

### 2. Chuẩn bị

#### Cơ sở dữ liệu: `dealcongnghe`
Bạn import file `dealcongnghe.sql` vào phpMyAdmin để tạo cấu trúc và dữ liệu ban đầu.

**Bảng chính: `products`** (giả sử có các cột: `id`, `name`, `price`, `category`, `image`, `link`)

---

### 3. Thực hành

#### Bước 1: Tạo file `connect.php`
```php
<?php
$host = "localhost";
$user = "root";
$pass = "";
$dbname = "dealcongnghe";

$conn = new mysqli($host, $user, $pass, $dbname);
if ($conn->connect_error) {
    die("Kết nối thất bại: " . $conn->connect_error);
}
?>
```

#### Bước 2: Tạo file `managepost.php`
Hiển thị danh sách sản phẩm:
```php
<?php
include "connect.php";
$sql = "SELECT * FROM products";
$result = $conn->query($sql);
?>

<h2>Quản lý tin đăng</h2>
<table border="1" class="table table-bordered">
<tr>
  <th>ID</th>
  <th>Tên SP</th>
  <th>Giá</th>
  <th>Loại</th>
  <th>Link SP</th>
  <th>Thao tác</th>
</tr>
<?php while($row = $result->fetch_assoc()) { ?>
<tr>
  <td><?= $row['id'] ?></td>
  <td><?= $row['name'] ?></td>
  <td><?= $row['price'] ?></td>
  <td><?= $row['category'] ?></td>
  <td><a href="<?= $row['link'] ?>" target="_blank">Xem</a></td>
  <td>
    <a href="editpost.php?id=<?= $row['id'] ?>">Sửa</a> |
    <a href="deletepost.php?id=<?= $row['id'] ?>" onclick="return confirm('Chắc chắn xóa?')">Xóa</a>
  </td>
</tr>
<?php } ?>
</table>
```

**Ghi chú:** Đổi `managepost.html` thành `managepost.php` trong thanh menu.

---

### 4. Yêu cầu nộp bài
- File: `connect.php`, `managepost.php`
- 1 ảnh chụp màn hình giao diện hiển thị dữ liệu từ DB.

-------------------------------------------------------------------------------------------------------------
# LAB04: THÊM - Sửa - XÓA (CRUD) SẢN PHẨM

---

### 1. Mục tiêu
- Thêm sản phẩm mới vào CSDL.
- Chỉnh sửa sản phẩm.
- Xóa sản phẩm khỏi CSDL.

---

### 2. Thực hành

#### A. Thêm sản phẩm - `createpost.php`
Chuyển form trong `createpost.html` sang file PHP và thêm xử lý:

```php
<?php
include "connect.php";
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $name = $_POST['name'];
    $price = $_POST['price'];
    $category = $_POST['category'];
    $image = $_POST['image'];
    $link = $_POST['link'];

    $sql = "INSERT INTO products(name, price, category, image, link)
            VALUES ('$name', '$price', '$category', '$image', '$link')";

    if ($conn->query($sql) === TRUE) {
        echo "<script>alert('Thêm thành công'); window.location='managepost.php';</script>";
    } else {
        echo "Lỗi: " . $conn->error;
    }
}
?>
```

**Chỉnh sửa `createpost.html`:**
- Đổi tên file thành `createpost.php`.
- Thêm thuộc tính `method="POST"` và `action=""` vào form.
- Thêm `name="name"`, `name="price"`, `name="category"`, `name="image"`, `name="link"` cho các input tương ứng.

---

#### B. Chỉnh sửa - `editpost.php`
```php
<?php
include "connect.php";
$id = $_GET['id'];
$sql = "SELECT * FROM products WHERE id=$id";
$result = $conn->query($sql);
$row = $result->fetch_assoc();

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $name = $_POST['name'];
    $price = $_POST['price'];
    $category = $_POST['category'];
    $image = $_POST['image'];
    $link = $_POST['link'];

    $sql = "UPDATE products SET name='$name', price='$price', category='$category', image='$image', link='$link' WHERE id=$id";
    if ($conn->query($sql) === TRUE) {
        echo "<script>alert('Cập nhật OK'); window.location='managepost.php';</script>";
    }
}
?>
```

**Chỉnh sửa `editpost.html`:**
- Đổi thành `editpost.php`.
- Gán `value="<?= $row['field'] ?>"` vào từng input.
- Thêm thẻ `form` với `method="POST"` và không có action hoặc `action=""`.

---

#### C. Quản lý sản phẩm - `managepost.php`
```php
<?php
include "connect.php";
$sql = "SELECT * FROM products";
$result = $conn->query($sql);
?>
<h2>Quản lý tin đăng</h2>
<table border="1">
<tr>
  <th>ID</th><th>Tên SP</th><th>Giá</th><th>Loại</th><th>Link</th><th>Thao tác</th>
</tr>
<?php while($row = $result->fetch_assoc()) { ?>
<tr>
  <td><?= $row['id'] ?></td>
  <td><?= $row['name'] ?></td>
  <td><?= $row['price'] ?></td>
  <td><?= $row['category'] ?></td>
  <td><a href="<?= $row['link'] ?>" target="_blank">Xem</a></td>
  <td>
    <a href="editpost.php?id=<?= $row['id'] ?>">Sửa</a> |
    <a href="deletepost.php?id=<?= $row['id'] ?>" onclick="return confirm('Chắc chắn xóa?')">Xóa</a>
  </td>
</tr>
<?php } ?>
</table>
```

**Chỉnh sửa `managepost.html`:**
- Đổi tên thành `managepost.php`.
- Nhúng PHP và thay phần thân bằng bảng như trên.

---

#### D. Xóa sản phẩm - `deletepost.php`
```php
<?php
include "connect.php";
$id = $_GET['id'];
$sql = "DELETE FROM products WHERE id=$id";
if ($conn->query($sql) === TRUE) {
  header("Location: managepost.php");
}
?>
```

---

#### E. Tìm kiếm sản phẩm - `searchpost.php`
Tùy chỉnh đoạn xử lý JavaScript để sau này có thể thay bằng PHP truy vấn DB theo tên sản phẩm (nếu nâng cấp).

---

### 3. Yêu cầu nộp bài
- File: `createpost.php`, `editpost.php`, `deletepost.php`, `managepost.php`, các input có đúng `name`.
- Test CRUD OK và chụp màn hình minh họa.

