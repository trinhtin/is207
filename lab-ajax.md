# **BT01: TÌM KIẾM SẢN PHẨM BẰNG AJAX**

---

### **1. Mục tiêu**

* Hiểu cơ bản về cách hoạt động của AJAX.
* Tạo tính năng tìm kiếm sản phẩm theo tên mà không cần tải lại trang.
* Sử dụng JavaScript (Fetch API hoặc XMLHttpRequest) hoặc jQuery AJAX để gửi yêu cầu đến PHP và nhận dữ liệu JSON.

---

### **2. Chuẩn bị**

* Các file từ lab trước (`connect.php`, `products` table).
* Tạo file mới: `searchajax.php` (để xử lý yêu cầu AJAX).
* Tạo file `searchproduct.php` chứa giao diện và mã JavaScript.

---

### **3. Thực hành**

#### A. Tạo giao diện tìm kiếm: `searchproduct.php`

```php
<?php include "connect.php"; ?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>Tìm kiếm sản phẩm bằng AJAX</title>
    <style>
        input { margin-bottom: 10px; padding: 5px; }
        table { width: 100%; border-collapse: collapse; }
        th, td { padding: 8px; border: 1px solid #ccc; }
    </style>
</head>
<body>

<h2>Tìm kiếm sản phẩm</h2>
<input type="text" id="search" placeholder="Nhập tên sản phẩm...">

<table>
    <thead>
        <tr>
            <th>ID</th><th>Tên SP</th><th>Giá</th><th>Loại</th><th>Link</th>
        </tr>
    </thead>
    <tbody id="result">
        <!-- Kết quả tìm kiếm sẽ hiện tại đây -->
    </tbody>
</table>

<script>
document.getElementById('search').addEventListener('keyup', function () {
    let keyword = this.value;

    fetch('searchajax.php?keyword=' + encodeURIComponent(keyword))
        .then(response => response.json())
        .then(data => {
            let result = document.getElementById('result');
            result.innerHTML = '';
            data.forEach(row => {
                result.innerHTML += `
                <tr>
                    <td>${row.id}</td>
                    <td>${row.name}</td>
                    <td>${row.price}</td>
                    <td>${row.category}</td>
                    <td><a href="${row.link}" target="_blank">Xem</a></td>
                </tr>`;
            });
        });
});
</script>

</body>
</html>
```

---

#### B. Xử lý AJAX và trả về dữ liệu JSON: `searchajax.php`

## 🔌 `connect.php`

```php
<?php
$host = 'localhost';
$user = 'root';
$pass = '';
$dbname = 'dealcongnghe';
$conn = new mysqli($host, $user, $pass, $dbname);
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
?>
```

---
## 🔌 `searchajax.php`

```php
<?php
include "connect.php";
header('Content-Type: application/json');

$keyword = isset($_GET['keyword']) ? $_GET['keyword'] : '';

$sql = "SELECT * FROM products WHERE name LIKE ?";
$stmt = $conn->prepare($sql);
$like = "%$keyword%";
$stmt->bind_param("s", $like);
$stmt->execute();

$result = $stmt->get_result();
$data = [];

while ($row = $result->fetch_assoc()) {
    $data[] = $row;
}

echo json_encode($data);
?>
```

---

### **4. Gợi ý mở rộng**

* Cho phép tìm kiếm theo cả `category` hoặc `price range`.
* Sử dụng jQuery để viết AJAX ngắn gọn hơn.
* Highlight phần `keyword` trùng khớp trong kết quả.

---

### **5. Yêu cầu nộp bài**

* Các file: `searchproduct.php`, `searchajax.php`, `connect.php`.
* 1 ảnh chụp màn hình khi tìm kiếm và kết quả được hiển thị.
* Đảm bảo có ít nhất 5 sản phẩm mẫu trong CSDL để test.

---

### **Đáp án mẫu (giả lập)**

**Ví dụ khi người dùng nhập "tai nghe":**

```json
[
  {
    "id": "2",
    "name": "Tai nghe Bluetooth Sony",
    "price": "1200000",
    "category": "Phụ kiện",
    "image": "sony.jpg",
    "link": "https://example.com/tai-nghe-sony"
  },
  {
    "id": "5",
    "name": "Tai nghe AirPods",
    "price": "3000000",
    "category": "Apple",
    "image": "airpods.jpg",
    "link": "https://example.com/airpods"
  }
]
```


## Bổ sung thêm 3 cách để giải BT01 sử dụng XMLHttpRequest, Fetch API và jQuery AJAX:

---

## ✅ Yêu cầu chung

### 1. CSDL mẫu: `products`

| id | name           | price | category   | link                                       |
| -- | -------------- | ----- | ---------- | ------------------------------------------ |
| 1  | iPhone 15      | 1000  | Smartphone | [https://apple.com](https://apple.com)     |
| 2  | Samsung Galaxy | 900   | Smartphone | [https://samsung.com](https://samsung.com) |

---

### 2. Server – `ajax_search.php` (Dùng cho cả 3 phiên bản)

```php
<?php
header('Content-Type: application/json');
include 'connect.php';

$keyword = $_POST['keyword'] ?? '';
$sql = "SELECT * FROM products WHERE name LIKE ?";
$stmt = $conn->prepare($sql);
$param = "%$keyword%";
$stmt->bind_param("s", $param);
$stmt->execute();
$result = $stmt->get_result();

$products = [];
while ($row = $result->fetch_assoc()) {
    $products[] = $row;
}

echo json_encode($products);
?>
```

---

## 🧪 PHIÊN BẢN 1: XMLHttpRequest

```html
<!-- search_xhr.html -->
<!DOCTYPE html>
<html>
<head>
  <title>Tìm kiếm sản phẩm - XMLHttpRequest</title>
</head>
<body>
  <h2>AJAX XMLHttpRequest</h2>
  <input type="text" id="search" placeholder="Nhập tên sản phẩm..." />
  <div id="result"></div>

<script>
document.getElementById('search').addEventListener('keyup', function() {
  const keyword = this.value;
  const xhr = new XMLHttpRequest();
  xhr.open('POST', 'ajax_search.php', true);
  xhr.setRequestHeader('Content-type', 'application/x-www-form-urlencoded');
  xhr.onload = function() {
    if (xhr.status === 200) {
      const data = JSON.parse(xhr.responseText);
      renderResult(data);
    }
  };
  xhr.send('keyword=' + encodeURIComponent(keyword));
});

function renderResult(data) {
  let html = '<ul>';
  data.forEach(p => {
    html += `<li><b>${p.name}</b> - ${p.price}đ - ${p.category} - <a href="${p.link}" target="_blank">Xem</a></li>`;
  });
  html += '</ul>';
  document.getElementById('result').innerHTML = html;
}
</script>
</body>
</html>
```

---

## 🧪 PHIÊN BẢN 2: fetch API

```html
<!-- search_fetch.html -->
<!DOCTYPE html>
<html>
<head>
  <title>Tìm kiếm sản phẩm - fetch API</title>
</head>
<body>
  <h2>AJAX fetch API</h2>
  <input type="text" id="search" placeholder="Nhập tên sản phẩm..." />
  <div id="result"></div>

<script>
document.getElementById('search').addEventListener('keyup', function() {
  const keyword = this.value;
  fetch('ajax_search.php', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/x-www-form-urlencoded',
    },
    body: 'keyword=' + encodeURIComponent(keyword),
  })
  .then(response => response.json())
  .then(data => renderResult(data));
});

function renderResult(data) {
  let html = '<ul>';
  data.forEach(p => {
    html += `<li><b>${p.name}</b> - ${p.price}đ - ${p.category} - <a href="${p.link}" target="_blank">Xem</a></li>`;
  });
  html += '</ul>';
  document.getElementById('result').innerHTML = html;
}
</script>
</body>
</html>
```

---

## 🧪 PHIÊN BẢN 3: jQuery AJAX

```html
<!-- search_jquery.html -->
<!DOCTYPE html>
<html>
<head>
  <title>Tìm kiếm sản phẩm - jQuery AJAX</title>
  <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
</head>
<body>
  <h2>AJAX jQuery</h2>
  <input type="text" id="search" placeholder="Nhập tên sản phẩm..." />
  <div id="result"></div>

<script>
$('#search').on('keyup', function() {
  const keyword = $(this).val();
  $.ajax({
    url: 'ajax_search.php',
    type: 'POST',
    data: { keyword },
    dataType: 'json',
    success: function(data) {
      renderResult(data);
    }
  });
});

function renderResult(data) {
  let html = '<ul>';
  data.forEach(p => {
    html += `<li><b>${p.name}</b> - ${p.price}đ - ${p.category} - <a href="${p.link}" target="_blank">Xem</a></li>`;
  });
  html += '</ul>';
  $('#result').html(html);
}
</script>
</body>
</html>
```

---

## 📦 Cấu trúc nộp bài gợi ý

```
/Lab05_AJAX
│
├── ajax_search.php
├── connect.php
├── search_xhr.html
├── search_fetch.html
└── search_jquery.html
```

---

# **BT02: QUẢN LÝ SẢN PHẨM, THÊM SỬA XÓA SẢN PHẨM BẰNG AJAX**
---
## 🗂️ Cấu trúc thư mục

```
/Lab05_Product_Management
│
├── db.php                 # Kết nối CSDL
├── api/
│   ├── get_products.php  # Lấy danh sách
│   ├── add_product.php   # Thêm
│   ├── update_product.php# Sửa
│   └── delete_product.php# Xóa
│
├── manage_xhr.html       # Giao diện dùng XMLHttpRequest
├── manage_fetch.html     # Giao diện dùng Fetch API
└── manage_jquery.html    # Giao diện dùng jQuery
```

---

## 🔌 `db.php`

```php
<?php
$host = 'localhost';
$user = 'root';
$pass = '';
$dbname = 'dealcongnghe';
$conn = new mysqli($host, $user, $pass, $dbname);
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}
?>
```

---

## 📥 `api/get_products.php`

```php
<?php
include '../db.php';
header('Content-Type: application/json');

$res = $conn->query("SELECT * FROM products");
$data = [];
while ($row = $res->fetch_assoc()) {
    $data[] = $row;
}
echo json_encode($data);
?>
```

---

## ➕ `api/add_product.php`

```php
<?php
include '../db.php';
$name = $_POST['name'];
$price = $_POST['price'];
$category = $_POST['category'];
$link = $_POST['link'];

$sql = "INSERT INTO products (name, price, category, link) VALUES (?, ?, ?, ?)";
$stmt = $conn->prepare($sql);
$stmt->bind_param("siss", $name, $price, $category, $link);
$stmt->execute();

echo json_encode(['status' => 'success']);
?>
```

---

## 🖊️ `api/update_product.php`

```php
<?php
include '../db.php';
$id = $_POST['id'];
$name = $_POST['name'];
$price = $_POST['price'];
$category = $_POST['category'];
$link = $_POST['link'];

$sql = "UPDATE products SET name=?, price=?, category=?, link=? WHERE id=?";
$stmt = $conn->prepare($sql);
$stmt->bind_param("sissi", $name, $price, $category, $link, $id);
$stmt->execute();

echo json_encode(['status' => 'updated']);
?>
```

---

## ❌ `api/delete_product.php`

```php
<?php
include '../db.php';
$id = $_POST['id'];

$sql = "DELETE FROM products WHERE id=?";
$stmt = $conn->prepare($sql);
$stmt->bind_param("i", $id);
$stmt->execute();

echo json_encode(['status' => 'deleted']);
?>
```

---

## 🖥️ Ví dụ Giao diện `manage_fetch.html` (Fetch API)

```html
<!DOCTYPE html>
<html>
<head>
  <title>Quản lý sản phẩm - Fetch API</title>
</head>
<body>
  <h2>Thêm sản phẩm</h2>
  <input id="name" placeholder="Tên" />
  <input id="price" placeholder="Giá" type="number"/>
  <input id="category" placeholder="Danh mục" />
  <input id="link" placeholder="Link" />
  <button onclick="addProduct()">Thêm</button>

  <h2>Danh sách sản phẩm</h2>
  <div id="list"></div>

<script>
function loadProducts() {
  fetch('api/get_products.php')
    .then(res => res.json())
    .then(data => {
      let html = '<ul>';
      data.forEach(p => {
        html += `<li>
          <b>${p.name}</b> - ${p.price}đ - ${p.category}
          [<a href="${p.link}" target="_blank">Xem</a>]
          <button onclick="editProduct(${p.id}, '${p.name}', ${p.price}, '${p.category}', '${p.link}')">Sửa</button>
          <button onclick="deleteProduct(${p.id})">Xóa</button>
        </li>`;
      });
      html += '</ul>';
      document.getElementById('list').innerHTML = html;
    });
}

function addProduct() {
  const data = new URLSearchParams();
  data.append('name', document.getElementById('name').value);
  data.append('price', document.getElementById('price').value);
  data.append('category', document.getElementById('category').value);
  data.append('link', document.getElementById('link').value);

  fetch('api/add_product.php', {
    method: 'POST',
    body: data
  }).then(() => loadProducts());
}

function editProduct(id, name, price, category, link) {
  const newName = prompt("Tên mới:", name);
  const newPrice = prompt("Giá mới:", price);
  const newCat = prompt("Danh mục mới:", category);
  const newLink = prompt("Link mới:", link);

  const data = new URLSearchParams();
  data.append('id', id);
  data.append('name', newName);
  data.append('price', newPrice);
  data.append('category', newCat);
  data.append('link', newLink);

  fetch('api/update_product.php', {
    method: 'POST',
    body: data
  }).then(() => loadProducts());
}

function deleteProduct(id) {
  const data = new URLSearchParams();
  data.append('id', id);

  fetch('api/delete_product.php', {
    method: 'POST',
    body: data
  }).then(() => loadProducts());
}

loadProducts();
</script>
</body>
</html>
```

---
Dưới đây là **3 phiên bản đầy đủ**: XMLHttpRequest, Fetch API, và jQuery AJAX — thể hiện thao tác với Web Server trả về dữ liệu JSON và client dùng JS để render dữ liệu sản phẩm:

---

### ✅ **1. XMLHttpRequest (manage\_xhr.html)**

```html
<!DOCTYPE html>
<html>
<head><title>Quản lý sản phẩm - XMLHttpRequest</title></head>
<body>
  <h2>Thêm sản phẩm (XHR)</h2>
  <input id="name" placeholder="Tên"/>
  <input id="price" placeholder="Giá" type="number"/>
  <input id="category" placeholder="Danh mục"/>
  <input id="link" placeholder="Link"/>
  <button onclick="addProduct()">Thêm</button>

  <h2>Danh sách sản phẩm</h2>
  <div id="list"></div>

<script>
function loadProducts() {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", "api/get_products.php", true);
  xhr.onload = function () {
    const data = JSON.parse(xhr.responseText);
    renderProducts(data);
  };
  xhr.send();
}

function addProduct() {
  const xhr = new XMLHttpRequest();
  xhr.open("POST", "api/add_product.php", true);
  xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
  const body = `name=${name.value}&price=${price.value}&category=${category.value}&link=${link.value}`;
  xhr.onload = () => loadProducts();
  xhr.send(body);
}

function deleteProduct(id) {
  const xhr = new XMLHttpRequest();
  xhr.open("POST", "api/delete_product.php", true);
  xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
  xhr.onload = () => loadProducts();
  xhr.send("id=" + id);
}

function editProduct(id, name, price, category, link) {
  const newName = prompt("Tên mới:", name);
  const newPrice = prompt("Giá mới:", price);
  const newCat = prompt("Danh mục mới:", category);
  const newLink = prompt("Link mới:", link);

  const xhr = new XMLHttpRequest();
  xhr.open("POST", "api/update_product.php", true);
  xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
  const body = `id=${id}&name=${newName}&price=${newPrice}&category=${newCat}&link=${newLink}`;
  xhr.onload = () => loadProducts();
  xhr.send(body);
}

function renderProducts(data) {
  let html = '<ul>';
  data.forEach(p => {
    html += `<li>${p.name} - ${p.price}đ - ${p.category}
    [<a href="${p.link}" target="_blank">Xem</a>]
    <button onclick="editProduct(${p.id}, '${p.name}', ${p.price}, '${p.category}', '${p.link}')">Sửa</button>
    <button onclick="deleteProduct(${p.id})">Xóa</button></li>`;
  });
  html += '</ul>';
  document.getElementById("list").innerHTML = html;
}

loadProducts();
</script>
</body>
</html>
```

---

### ✅ **2. Fetch API (manage\_fetch.html)**

(Đã gửi ở trên, giữ nguyên như vậy)

---

### ✅ **3. jQuery AJAX (manage\_jquery.html)**

```html
<!DOCTYPE html>
<html>
<head>
  <title>Quản lý sản phẩm - jQuery AJAX</title>
  <script src="https://code.jquery.com/jquery-3.7.1.min.js"></script>
</head>
<body>
  <h2>Thêm sản phẩm (jQuery)</h2>
  <input id="name" placeholder="Tên"/>
  <input id="price" placeholder="Giá" type="number"/>
  <input id="category" placeholder="Danh mục"/>
  <input id="link" placeholder="Link"/>
  <button onclick="addProduct()">Thêm</button>

  <h2>Danh sách sản phẩm</h2>
  <div id="list"></div>

<script>
function loadProducts() {
  $.get("api/get_products.php", function(data) {
    renderProducts(data);
  }, "json");
}

function addProduct() {
  $.post("api/add_product.php", {
    name: $("#name").val(),
    price: $("#price").val(),
    category: $("#category").val(),
    link: $("#link").val()
  }, function() {
    loadProducts();
  }, "json");
}

function deleteProduct(id) {
  $.post("api/delete_product.php", { id }, function() {
    loadProducts();
  }, "json");
}

function editProduct(id, name, price, category, link) {
  const newName = prompt("Tên mới:", name);
  const newPrice = prompt("Giá mới:", price);
  const newCat = prompt("Danh mục mới:", category);
  const newLink = prompt("Link mới:", link);
  $.post("api/update_product.php", {
    id, name: newName, price: newPrice, category: newCat, link: newLink
  }, function() {
    loadProducts();
  }, "json");
}

function renderProducts(data) {
  let html = "<ul>";
  $.each(data, function(i, p) {
    html += `<li>${p.name} - ${p.price}đ - ${p.category}
    [<a href="${p.link}" target="_blank">Xem</a>]
    <button onclick="editProduct(${p.id}, '${p.name}', ${p.price}, '${p.category}', '${p.link}')">Sửa</button>
    <button onclick="deleteProduct(${p.id})">Xóa</button></li>`;
  });
  html += "</ul>";
  $("#list").html(html);
}

loadProducts();
</script>
</body>
</html>
```

---

## 🧪 Kiểm thử

* Dùng trình duyệt mở 1 trong 3 file HTML tương ứng (`manage_xhr.html`, `manage_fetch.html`, `manage_jquery.html`)
* Thêm sản phẩm → Server trả về JSON (`status: success`)
* Client gọi lại `get_products.php`, nhận về danh sách sản phẩm JSON → Render HTML qua JS.

---

# **BT03: LOAD DỮ LIỆU CHO COMBOBOX BẰNG AJAX**
---

## ✅ **Yêu cầu cập nhật giao diện**

### 🎯 Thay đổi:

```html
<!-- Cũ -->
<input id="category" placeholder="Danh mục"/>

<!-- Mới -->
<select id="category"></select>
```

---

## ✅ **Gợi ý cấu trúc JSON trả về từ Server cho danh mục**

File: `api/get_categories.php`

```php
<?php
echo json_encode([
  ['id' => 1, 'name' => 'Điện thoại'],
  ['id' => 2, 'name' => 'Laptop'],
  ['id' => 3, 'name' => 'Phụ kiện']
]);
?>
```

---

## ✅ **Cập nhật script để load Combo Box**

### 📌 XMLHttpRequest

```js
function loadCategories() {
  const xhr = new XMLHttpRequest();
  xhr.open("GET", "api/get_categories.php", true);
  xhr.onload = function () {
    const categories = JSON.parse(xhr.responseText);
    const select = document.getElementById("category");
    select.innerHTML = categories.map(c =>
      `<option value="${c.name}">${c.name}</option>`
    ).join('');
  };
  xhr.send();
}
```

### 📌 Fetch API

```js
function loadCategories() {
  fetch("api/get_categories.php")
    .then(res => res.json())
    .then(categories => {
      const select = document.getElementById("category");
      select.innerHTML = categories.map(c =>
        `<option value="${c.name}">${c.name}</option>`
      ).join('');
    });
}
```

### 📌 jQuery AJAX

```js
function loadCategories() {
  $.get("api/get_categories.php", function(data) {
    let html = "";
    $.each(data, function(i, c) {
      html += `<option value="${c.name}">${c.name}</option>`;
    });
    $("#category").html(html);
  }, "json");
}
```

> 📌 Gọi `loadCategories()` trong `window.onload` hoặc khi DOM ready.
