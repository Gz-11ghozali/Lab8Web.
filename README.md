# Lab8Web.
1. Persiapan
Text Editor: Gunakan text editor seperti VSCode.
Folder Projek: Buat folder baru bernama lab8_php_database di dalam htdocs (docroot web server).
MySQL Server: Jalankan server MySQL melalui XAMPP

![image](https://github.com/user-attachments/assets/5c6b2fcc-5a8e-4e00-ad2b-d3e546ae1c03)
Membuat Database
CREATE DATABASE latihan1;
Membuat Tabel
CREATE TABLE data_barang (
id_barang int(10) auto_increment Primary Key,
kategori varchar(30),
nama varchar(30),
gambar varchar(100),
harga_beli decimal(10,0),
harga_jual decimal(10,0),
stok
Menambahkan Data
INSERT INTO data_barang (kategori, nama, gambar, harga_beli, harga_jual, stok)
VALUES ('Elektronik', 'HP Samsung Android', 'hp_samsung.jpg', 2000000, 2400000, 5),
('Elektronik', 'HP Xiaomi Android', 'hp_xiaomi.jpg', 1000000, 1400000, 5),
('Elektronik', 'HP OPPO Android', 'hp_oppo.jpg', 1800000, 2300000, 5);

![Screenshot 2024-11-18 045857](https://github.com/user-attachments/assets/759c106d-4f97-4e86-a595-9661386d4061)
Membuat Program CRUD
Buat folder lab8_php_database pada root directory web server (d:\xampp\htdocs)

![Screenshot 2024-11-18 061834](https://github.com/user-attachments/assets/df547f4d-ba4d-445e-ba70-f248f537caf5)

Kemudian untuk mengakses direktory tersebut pada web server dengan mengakses URL:
http://localhost/lab8_php_database/

![Screenshot 2024-11-18 050059](https://github.com/user-attachments/assets/a214fcde-7e18-4799-b980-7998ca3e3ab2)

Membuat file koneksi database
Buat file baru dengan nama koneksi.php
<?php
$host = "localhost";
$user = "root";
$pass = "";
$db = "latihan1";

$conn = mysqli_connect($host, $user, $pass, $db);

if ($conn == false) {
    echo "Koneksi ke server gagal.";
    die();
} else {
    echo "Koneksi berhasil";
}
?>

![Screenshot 2024-11-18 053530](https://github.com/user-attachments/assets/fdd577e4-cb57-4927-b36c-a0e4b04f4559)

Membuat file index untuk menampilkan data (Read)
Buat file baru dengan nama index.php
<?php
include("koneksi.php");

// Query untuk menampilkan data
$sql = 'SELECT * FROM data_barang';
$result = mysqli_query($conn, $sql);

if (!$result) {
    die("Query gagal: " . mysqli_error($conn));
}
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Data Barang</title>
</head>
<body>
<div class="container">
    <h1>Data Barang</h1>
    <div class="main">
        <table border="1">
            <thead>
                <tr>
                    <th>Gambar</th>
                    <th>Nama Barang</th>
                    <th>Kategori</th>
                    <th>Harga Beli</th>
                    <th>Harga Jual</th>
                    <th>Stok</th>
                    <th>Aksi</th>
                </tr>
            </thead>
            <tbody>
                <?php if ($result && mysqli_num_rows($result) > 0): ?>
                    <?php while ($row = mysqli_fetch_array($result)): ?>
                        <tr>
                            <td>
                                <img src="gambar/<?= htmlspecialchars($row['gambar']); ?>" 
                                     alt="<?= htmlspecialchars($row['nama']); ?>" 
                                     style="width:100px; height:auto;">
                            </td>
                            <td><?= htmlspecialchars($row['nama']); ?></td>
                            <td><?= htmlspecialchars($row['kategori']); ?></td>
                            <td><?= htmlspecialchars($row['harga_beli']); ?></td>
                            <td><?= htmlspecialchars($row['harga_jual']); ?></td>
                            <td><?= htmlspecialchars($row['stok']); ?></td>
                            <td>
                                <a href="edit.php?id=<?= $row['id_barang']; ?>">Edit</a> |
                                <a href="delete.php?id=<?= $row['id_barang']; ?>" onclick="return confirm('Hapus data ini?');">Hapus</a>
                            </td>
                        </tr>
                    <?php endwhile; ?>
                <?php else: ?>
                    <tr>
                        <td colspan="7">Belum ada data</td>
                    </tr>
                <?php endif; ?>
            </tbody>
        </table>
    </div>
</div>
</body>
</html>

![Screenshot 2024-11-18 054354](https://github.com/user-attachments/assets/d679c904-6406-4a17-a483-d1c063f70303)

Menambah Data (Create)
Buat file baru dengan nama tambah.php
<?php
error_reporting(E_ALL);
include_once 'koneksi.php';
if (isset($_POST['submit']))
{
$nama = $_POST['nama'];
$kategori = $_POST['kategori'];
$harga_jual = $_POST['harga_jual'];
$harga_beli = $_POST['harga_beli'];
$stok = $_POST['stok'];
$file_gambar = $_FILES['file_gambar'];
$gambar = null;
if ($file_gambar['error'] == 0)
{
$filename = str_replace(' ', '_',$file_gambar['name']);
$destination = dirname(__FILE__) .'/gambar/' . $filename;
if(move_uploaded_file($file_gambar['tmp_name'], $destination))
{
$gambar = 'gambar/' . $filename;;
}
}
$sql = 'INSERT INTO data_barang (nama, kategori,harga_jual, harga_beli,
stok, gambar) ';
$sql .= "VALUE ('{$nama}', '{$kategori}','{$harga_jual}',
'{$harga_beli}', '{$stok}', '{$gambar}')";
$result = mysqli_query($conn, $sql);
header('location: index.php');
}
?>
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<link href="style.css" rel="stylesheet" type="text/css" />
<title>Tambah Barang</title>
</head>
<body>
<div class="container">
<h1>Tambah Barang</h1>
<div class="main">
<form method="post" action="tambah.php"
enctype="multipart/form-data">
<div class="input">
<label>Nama Barang</label>
<input type="text" name="nama" />
</div>
<div class="input">
<label>Kategori</label>
<select name="kategori">
<option value="Komputer">Komputer</option>
<option value="Elektronik">Elektronik</option>
<option value="Hand Phone">Hand Phone</option>
</select>
</div>
<div class="input">
<label>Harga Jual</label>
<input type="text" name="harga_jual" />
</div>
<div class="input">
<label>Harga Beli</label>
<input type="text" name="harga_beli" />
</div>
<div class="input">
<label>Stok</label>
<input type="text" name="stok" />
</div>
<div class="input">
<label>File Gambar</label>
<input type="file" name="file_gambar" />
</div>
<div class="submit">
<input type="submit" name="submit" value="Simpan" />
</div>
</form>
</div>
</div>
</body>
</html>

![Screenshot 2024-11-18 054946](https://github.com/user-attachments/assets/cb0f07e6-a083-40c5-9e26-6d6918a7ceb6)

Mengubah Data (Update)
Buat file baru dengan nama ubah.php
<?php
error_reporting(E_ALL);
include_once 'koneksi.php';

// Memeriksa apakah 'id' ada di URL (GET)
if (isset($_GET['id'])) {
    $id = $_GET['id'];

    // Debugging: Memeriksa apakah id diterima dengan benar
    // echo "ID diterima: " . $id;  // Uncomment untuk melihat output ID di browser

    // Query untuk mengambil data barang berdasarkan ID
    $sql = "SELECT * FROM data_barang WHERE id_barang = '{$id}'";
    $result = mysqli_query($conn, $sql);
    
    // Memeriksa apakah query berhasil dan data ditemukan
    if (!$result) {
        die('Error: Data tidak tersedia');
    }
    
    $data = mysqli_fetch_array($result);

    // Memeriksa apakah data ditemukan
    if (!$data) {
        die('Error: Barang dengan ID tersebut tidak ditemukan.');
    }

} else {
    // Menangani jika ID tidak ditemukan di URL
    die('Error: ID tidak ditemukan.');
}

// Menangani form submit untuk mengupdate data
if (isset($_POST['submit'])) {
    $id = $_POST['id'];
    $nama = $_POST['nama'];
    $kategori = $_POST['kategori'];
    $harga_jual = $_POST['harga_jual'];
    $harga_beli = $_POST['harga_beli'];
    $stok = $_POST['stok'];
    $file_gambar = $_FILES['file_gambar'];
    $gambar = null;

    // Menangani upload gambar jika ada
    if ($file_gambar['error'] == 0) {
        $filename = str_replace(' ', '_', $file_gambar['name']);
        $destination = dirname(__FILE__) . '/gambar/' . $filename;
        
        if (move_uploaded_file($file_gambar['tmp_name'], $destination)) {
            $gambar = 'gambar/' . $filename;
        }
    }

    // Query untuk memperbarui data barang
    $sql = 'UPDATE data_barang SET ';
    $sql .= "nama = '{$nama}', kategori = '{$kategori}', ";
    $sql .= "harga_jual = '{$harga_jual}', harga_beli = '{$harga_beli}', stok = '{$stok}' ";
    
    if (!empty($gambar)) {
        $sql .= ", gambar = '{$gambar}' ";
    }

    $sql .= "WHERE id_barang = '{$id}'";
    
    // Eksekusi query untuk memperbarui data
    $result = mysqli_query($conn, $sql);
    
    if ($result) {
        header('location: index.php');
        exit();
    } else {
        echo "Error: Gagal memperbarui data.";
    }
}

function is_select($var, $val) {
    if ($var == $val) return 'selected="selected"';
    return false;
}
?>

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <link href="style.css" rel="stylesheet" type="text/css" />
    <title>Ubah Barang</title>
</head>
<body>
<div class="container">
    <h1>Ubah Barang</h1>
    <div class="main">
        <form method="post" action="ubah.php" enctype="multipart/form-data">
            <div class="input">
                <label>Nama Barang</label>
                <input type="text" name="nama" value="<?php echo htmlspecialchars($data['nama']); ?>" />
            </div>
            <div class="input">
                <label>Kategori</label>
                <select name="kategori">
                    <option <?php echo is_select('Komputer', $data['kategori']); ?> value="Komputer">Komputer</option>
                    <option <?php echo is_select('Elektronik', $data['kategori']); ?> value="Elektronik">Elektronik</option>
                    <option <?php echo is_select('Hand Phone', $data['kategori']); ?> value="Hand Phone">Hand Phone</option>
                </select>
            </div>
            <div class="input">
                <label>Harga Jual</label>
                <input type="text" name="harga_jual" value="<?php echo htmlspecialchars($data['harga_jual']); ?>" />
            </div>
            <div class="input">
                <label>Harga Beli</label>
                <input type="text" name="harga_beli" value="<?php echo htmlspecialchars($data['harga_beli']); ?>" />
            </div>
            <div class="input">
                <label>Stok</label>
                <input type="text" name="stok" value="<?php echo htmlspecialchars($data['stok']); ?>" />
            </div>
            <div class="input">
                <label>File Gambar</label>
                <input type="file" name="file_gambar" />
            </div>
            <div class="submit">
                <input type="hidden" name="id" value="<?php echo $data['id_barang']; ?>" />
                <input type="submit" name="submit" value="Simpan" />
            </div>
        </form>
    </div>
</div>
</body>
</html>


![Screenshot 2024-11-18 055459](https://github.com/user-attachments/assets/d35b433c-0ef5-49cb-b192-814487bd3472)

Menghapus Data (Delete)
Buat file baru dengan nama hapus.php
<?php
include_once 'koneksi.php';
$id = $_GET['id'];
$sql = "DELETE FROM data_barang WHERE id_barang = '{$id}'";
$result = mysqli_query($conn, $sql);
header('location: index.php');
?>

![Screenshot 2024-11-18 055642](https://github.com/user-attachments/assets/2f818903-522b-44eb-9df2-ff154ae9c4ad)
