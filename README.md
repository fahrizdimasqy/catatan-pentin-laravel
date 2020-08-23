# catatan-pentin-laravel

## Eloquent ORM
```php
$id = 1;
$user = User::find($id);
$user->name = "Muhammad Azamuddin";
$user->save();
```

> ## ORM memiliki kepanjangan Object Relational Mapping, dengan ORM kita bisa melakukan operasi
CRUD ke database tanpa harus menulis SQL Command. ORM inilah yang bertugas untuk menulis
SQL Command untuk kita. Laravel menggunakan ORM yang bernama Eloquent. 

## Konsep MVC
1. Model
Merupakan komponen dalam aplikasi kita yang bertanggungjawab mengelola akses langsung dengan
sumber data dan logika pengelolaan data tersebut

2. View
Merupakan komponen dalam aplikasi kita yang bertanggungjawab untuk membuat tampilan / interface
untuk pengguna. Sumber data didapat dari model yang didapatkan melalui controller. Tidak berinteraksi
langsung dengan database. View juga menangkap interaksi dari pengguna yang akan diteruskan ke
aplikasi.

3. Controller
Merupakan komponen dalam aplikasi kita yang bertanggungjawab untuk menerima input dan memberikan
output, atau dalam dunia web kita lebih mengenal dengan istilah request dan response.

## Routing
### Menggunakan Anonymous function
```php
Route::get('/hello', function () {
    return 'Hellow World!';
});
```
### Menggunakan action dari controller
Sehingga misalnya kita memiliki WelcomeController dengan method beriSalam, kita bisa menulis ulang
Route kita sebagai berikut:
```php
//aturan
Route::get("/hello", "NamaController@namaMethod");
//contoh
Route::get("/hello", "WelcomeController@beriSalam");
```

## Pengenalan method
Model bertugas untuk query ke database, insert data baru, update, atau hapus record di database. Semua itu
dilakukan dengan ORM (Object Relational Mapping) sehingga pada banyak kasus, kita tidak perlu
menuliskan kode SQL secara langsung, akan tetapi langsung menggunakan method bawaan dari ORM
seperti, find, findOrFail, create, update, dll. Dan ORM bawaan yang dipakai oleh Laravel adalah Eloquent.

### Membuat Model
```php artisan make:model NamaModel```

> Kesepakatan (Konvensi) dalam penulisan model adalah bentuk tunggal dan menggunakan
CamelCase dengan kapital di awal, misalnya Product, ProductItem, dll. Setiap model tersebut akan
merepresentasikan tabel dengan nama jamak. Dengan demikian model Product yang sudah kita
generate merepresentasikan tabel products pada database. Kesepakatan tersebut bisa kita ubah,
pembahasannya akan kita pelajari bersama di bab Model & Eloquent.
