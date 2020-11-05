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

## Pengenalan Model
Model bertugas untuk query ke database, insert data baru, update, atau hapus record di database. Semua itu
dilakukan dengan ORM (Object Relational Mapping) sehingga pada banyak kasus, kita tidak perlu
menuliskan kode SQL secara langsung, akan tetapi langsung menggunakan method bawaan dari ORM
seperti, find, findOrFail, create, update, dll. Dan ORM bawaan yang dipakai oleh Laravel adalah Eloquent.

### Membuat Model
```bash
php artisan make:model NamaModel
```

> Kesepakatan (Konvensi) dalam penulisan model adalah bentuk tunggal dan menggunakan
CamelCase dengan kapital di awal, misalnya Product, ProductItem, dll. Setiap model tersebut akan
merepresentasikan tabel dengan nama jamak. Dengan demikian model Product yang sudah kita
generate merepresentasikan tabel products pada database. Kesepakatan tersebut bisa kita ubah,
pembahasannya akan kita pelajari bersama di bab Model & Eloquent.

## Pengenalan Controller
Kemudian kita belajar bahwa selain anonymous function sebagai callback, pada aplikasi sesungguhnya kita
akan lebih sering memberikan Controller action sebagai callback route seperti ini

```php
Route::get('/products', 'ProductController@index');
```
Pada definisi route di atas, ProductController merupakan nama controller sedangkan index merupakan
action pada controller.

### Membuat Controller
```bash
php artisan make:controller ProductController
```
> Kesepakatan penamaan controller adalah dengan StudlyCaps dan memberikan akhiran Controller
pada nama filenya. Misalnya ProductController, UserController, ProductItemController
dll.

## Pengenalan View
View bertanggungjawab untuk memberikan tampilan ke user. Jika kita ingin meletakkan kode html, css dan
javascript di view lah tempatnya. Laravel membuat view lebih powerful dengan memanfaatkan templating
engine.

## Route Parameter
Ketika mengakses route tersebut, kita menginginkan
untuk melihat produk dengan ID 20. Nah ID tersebut kan berubah-ubah sesuai dari pengguna mau melihat
produk dengan ID berapa. Maka, ID tersebut bisa kita jadikan route param yang akan kita tangkap nilainya.

```php
Route::get("products/{id}", "ProductController@show");
```
Lalu pada ProductController action show kita dapat mengakses nilai $id yang diinput oleh user seperti
ini.
File ProductController.php
```php
public function show($id){
}
```
Urutan parameter menentukan cara aksesnya misal:
```php
Route::get("/product/{product_id}", "ProductController@show");
```
Maka, apakah di controller action kita perlu mengubah $id menjadi $product_id. Jawabanya tidak harus, yang
penting urutannya sama.Karena route di atas hanya memiliki 1 params, maka nama apapun di controller
action akan merujuk ke param tersebut.
contoh2:
```php
Route::get("users/{user_id}/comments/{comment_id}",
"UserController@showComment");
```
Route tersebut memiliki dua params yaitu {user_id} dan {comment_id}, maka kita bisa mengaksesnya di
UserController action showComment dalam argument. Argument pertama adalah untuk mendapatkan
nilai user_id dan argument kedua adalah untuk mendapatkan nilai comment_id, apapun namanya.
```php
public function showComment($argument1, $argument2){
/**
* $argument1 = nilai dari param {user_id}
* $argument2 = nilai dari param {comment_id}
* apapun nama yang kita berikan yaa
* yang berpengaruh adalah uruatannya.
*/
}
```
Akan tetapi agar kode kita lebih enak dibaca, sebaiknya kita samakan dengan nama route param yang kita
definisikan, yaitu $user_id dan $comment_id.

### Optional Route Parameter
untuk membuatnya menjadi opsional, artinya parameter tersebut boleh dikosongkan kita cukup
tambahkan ? di belakang definisi route parameter kita.
contoh:
```php
Route::get("/products/{product_id?}", "ProductController@show");
```
Nah, kini param {product_id} boleh dikosongkan sehingga jika kita mengakses http://toko-
online.test/products tetap akan memanggil controller bersangkutan, tanpa error.

## Mengizinkan lebih dari satu HTTP method
Misalnya kita ingin route products/43 bisa diakses dengan method PUT dan PATCH, maka kita bisa
menuliskan definisi routenya seperti ini:
```php
Route::match(["PUT", "PATCH"], "/products/{id}",
"ProductController@update");
```
Atau jika kita ingin mengizinkan semua HTTP method untuk sebuah route maka kita bisa menulisnya seperti
ini:
```php
Route::any("/products/{id}", "ProductController@action");
```

### Named Route
Kita juga bisa memberikan nama untuk route yang kita definisikan. Caranya adalah seperti ini

```php
Route::get("/products/list", "ProductController@list")->name("products");
```

Route yang memiliki
nama ini bisa kita gunakan dengan helper lainnya.
```php
$url = route("products");
```
atau redirect ke route
```php
redirect()->route("products");
// akan redirect ke http://toko-online.test/products/list
```

### Route Group
misalkan semua route memiliki "/products" di url.
Kita bisa membuat kelompok route (Route Group) seperti ini:
```php
Route::group(["prefix"=>"/products"], function(){
Route::get("/all", "ProductController@all");
Route::get("/bag", "ProductController@bag");
Route::get("/latest", "ProductController@latest");
Route::get("/discounts", "ProductController@discounts");
});
```

kita
bisa menerapkan policy melalui middleware tertentu ke dalam semua route di dalam sebuah route group.
Cukup dengan menambahkan key middleware ke dalam array config seperti ini
```php
Route::group(["prefix"=>"admin", "middleware"=>"mustAdmin"], function(){
Route::get("/dashboard", "DashboardController@index");
Route::get("/orders", "DashboardController@index");
// definisi route lainnya di sini
});
```
### Route View
Bisa juga kita melewati controller dari route, hal ini bisa kita lakukan jika kita ingin menampilkan view tanpa
data yang kompleks, atau view yang bersifat statis. Caranya adalah sepert ini:

```php
Route::view("/path", "nama.view");
```
Penjelasan kode: Dengan kode di atas kita bisa mengakses http://toko-online.test/path dan view
nama.view yang terletak pada resources/views/nama/view.blade.php akan ditampilkan tanpa
melalui controller.

## Controller

### Anatomi controller
Anatomi paling dasar agar kita bisa menggunakan controller
hanyalah action.
Berikut adalah anatomi controller kosong yang belum
bisa kita gunakan.
```php
<?php
namespace App\Http\Controllers;
use Illuminate\Http\Request;
class ItemController extends Controller
{

}
```
Kode diatas merupakan definisi class ItemController, karena controller tersebut tidak memiliki action
apapun maka controller tersebut belum bisa digunakan.

### Controller Resource
Pola paling dasar dari sebuah aplikasi adalah CRUD (Create, Read, Update, Delete). Yaitu pengguna aplikasi
dapat membuat data baru, membaca / melihat data, mengupdate data tertentu dan menghapusnya.
, sebuah controller harus memiliki beberapa action standard. Action
tersebut antara lain index, create, show, edit, update dan delete.
Istilah untuk menyebut kesemua
fungsionalitas tersebut adalah resource sehingga semua action tadi kita sebut sebagai actions
resource dan controller yang memiliki beberapa action tersebut kita sebut controller resource.

#### Index
Action index digunakan untuk menampilkan list atau daftar data dari sebuah controller.

#### create
Action create digunakan untuk menampilkan view yaitu berupa form untuk membuat / insert data baru.

#### store
Action store digunakan untuk menerima data yang diinput melalui form dari action create sebelumnya.
Tugas dari action store adalah untuk menerima data lalu menyimpannya ke tabel tertentu,

#### edit
Action edit seperti halnya action create digunakan untuk menampilkan form isian untuk data tertentu.
Bedanya form edit akan diisi dengan data tertentu bukan form kosong. Karena tujuan dari edit adalah untuk
melakukan edit data yang sebelumnya sudah ada.
Di action edit kita juga memerlukan route param yang berisi ID data tertentu yang akan diedit.

#### update
Action update serupa dengan action store yaitu berfungsi untuk menyimpan data tertentu ke database.
Sekali lagi, bedanya adalah update menerima data isian yang dikirim dari action edit bukan create.Action ini juga memerlukan route param yang berisi ID tertentu yang akan diupdate dengan data yang
diterima dari action edit

#### destroy

Oleh
karenanya action ini membutuhkan route param yang bernilai ID data tertentu yang akan didelete.	
Action destroy digunakan untuk menghapus data tertentu dari database berdasarkan ID tertentu. Oleh
karenanya action ini membutuhkan route param yang bernilai ID data tertentu yang akan didelete.

#### Membuat Controller Resource
Kita bisa membuat controller resource dengan command seperti pembuatan controller pada umumnya.

```command
php artisan make:controller CategoryController --resource
```

#### Membuat Route Resource
Setelah kita memiliki CategoryController di atas dengan action resource lalu bagaimana kita
menghubungkannya dengan route? apakah kita definisikan seperti sebelumnya yaitu seperti ini:

```command
Route::get("/categories", "CategoryController@index");
Route::get("/categories/create", "CategoryController@create");
Route::post("/categories", "CategoryController@store");
Route::get("/categories/{id}", "CategoryController@show");
Route::get("/categories/{id}/edit", "CategoryController@edit");
Route::put("/categories/{id}", "CategoryController@update");
Route::delete("/categories/{id}", "CategoryController@destroy");
```

## Membaca Input & Memberikan Response

### Input & Query String
Sebelumnya
kita belajar bahwa ada beberapa HTTP method yang bisa kita gunakan dalam request. Dan kita juga belajar
bagaimana POST method cenderung lebih aman ketimbang menggunakan GET method. Meskipun demikian
keduanya memiliki kegunanaannya masing-masing. Misalnya perhatikan URL google search, kamu akan
menemukan URL kurang lebih seperti ini
```
https://google.co.id?q=buku+laravel+lengkap+bagus
```
Request ke search engine tersebut menggunakan method GET, kenapa? yang pertama karena kita bisa
langsung mengetikannya di address bar browser kita. Kedua, karena data yang kita kirim yaitu keyword
dalam bentuk query string perhatikan bagian ?q=buku+laravel+lengkap+bagus itu merupakan
query string. Di contoh tersebut terdapat query string bernama q dan bernilai buku laravel
lengkap bagus. Inilah salah satu penggunaan GET method dan query string nya, dan ketika kita
mengubah langsung di browser keyword, maka tidak ada masalah.
Inilah kenapa pada data yang sensitif kita jangan menggunakan method GET, tapi gunakan
method POST yang lebih aman, karena data yang dikirim ke server tidak tampak di URL tetapi terbungkus
dalam payload. Nah, kita menyebutnya data tersebut dengan input.

1. query string
Data yang dikirim menggunakan method GET.
2. input
Data yang dikirim menggunakan method POST atau PUT atau PATCH. Kenapa ketiganya sekaligus? karena
sifat mereka sama dalam mengirimkan data.

### Membaca Input & Query String
Misalnya dalam ProductController action create kita ingin menangkap data yang diinput oleh user
untuk membuat produk baru. Kita gunakan bantuan $request->get("nama_input") seperti ini:
```php
public function create(Request $request){
$product_name = $request->get("product_name");
$product_stock = $request->get("stock");
$product_desc = $request->get("description");
$product_price = $request->get("price");
}
```
kode di atas kita menangkap input dari user
yaitu berupa product_name, stock, description dan price lalu kita simpan ke dalam variabel masing-
masing. Dengan begitu kita siap untuk menyimpan produk baru ke database dengan data yang baru saja kita
dapatkan.


Untuk menangkap query string kita juga menggunakan fungsi yang sama yaitu $request->get("nama_query_string"). Sehingga misalnya kita memiliki fitur search pada URL ini:
```bash
http://toko-online.test/search?product_name=Sepatu
```

Url tersebut kita konfigurasi ke route berikut"
```php
Route::get("search", "ProductController@search");
```

Maka di action search kita akan menangkap query string product_name seperti ini:
```php
public function search(Request $request){
	// Lalu kita akan mencari ke tabel product berdasarkan product.name ==
	$keyword = $request->get("product_name");
}

> TIPS! Selain menggunakan $request->get kita bisa menggunakan $request->input.

### Membaca semua input & query string
Seandainya kita memiliki 20 input yang harus ditangkap, tampaknya menangkap satu per satu terlalu melelahkan.
```php
$data = $request->all();
```

### Mengecualikan input & query string

dari seluruh input
ada input yang ingin kita kecualikan. Contohnya kita ingin mengecualikan input 
_token karena kita tidak butuh input tersebut untuk disimpan ke database.
Dan justru akan error jika tidak dikecualikan.

```php
$data = $request->except(["_token"]);
```
Kini $data berisi seluruh input kecuali input bernama _token. Dan kita bisa mengecualikan lebih dari satu field seperti ini : $request->except(["_token", "field_lain"]);

### Memberikan Response
Setelah kita menangkap input dari user pada controller maka dalam praktiknya kita akan memproses input
tersebut baik menginsert data baru atau mengupdate data yang sudah ada. Lalu setelah proses itu kita
memberikan response kembali ke user baik menginfokan bahwa insert berhasil, atau redirect ke halaman lain
jika error dan lain-lain.

### Response dasar
Cara paling dasar untuk memberikan reponse adalah dengan return dari controller action seperti ini, kita
gunakan CategoryController yang sudah kita buat ya.

```php
public function index(){
	return "DAFTAR CATEGORY";
}
```

### Response redirect ke halaman lain
Kita bisa melakukan redirect ke halaman lain dengan bantuan Facade \Redirect::to("path") seperti
ini:
```php
public function index(){
	return \Redirect::to("/categories/10/edit");
}
```

### Response redirect ke website lain
```php
public function index(){
	return \Redirect::away("https://google.co.id");
}
```

### Response view
Pada pratiknya kita akan meresponse dengan memberikan sebuah view untuk ditampilkan ke user aplikasi

```php
public function index(){
	return view("category/index"); 
}
```

## Database

### Migration
Migration berfungsi sebagai version control database kita. Dengan migration kita bisa membuat, mengubah
atau menghapus struktur tabel dan field database tanpa harus membuka aplikasi GUI database
management

File Migration ini memiliki 2 method yaitu up() dan down(). Method up() digunakan
untuk menuliskan perintah pembuatan atau pengubahan struktur database. Method down() digunakan untuk
menuliskan kode yang membatalkan apa yang telah dieksekusi di method up().

### Mengeksekusi Migration
```bash
php artisan migrate	
```
Penjelasan Kode: Dengan perintah di atas, maka file-file Migration yang berisi perintah pengubahan
database akan dijalankan, hanya kode di method up()yang dieksekusi. Untuk melihat perubahannya, kita
bisa melihat langsung tabel di database kita.

### Migration Rollback
Migration rollback digunakan untuk mengembalikan database kita ke versi sebelum menjalankan perintah
migrate. Misalnya kita banyak memiliki file migration, maka rollback akan mengembalikan database kita ke
versi terakhir sebelum perintah migrate terakhir kita jalankan.

### Schema Builder
Schema Builder merupakan facade yang bisa kita gunakan untuk mengubah struktur database kita.
kode $table->increments(“id”) dan $table->timestamps(), keduanya kita lihat pada
file Migration yang kita generate sebelumnya, dan berfungsi untuk mengubah struktur database melalui kode.
Dan kedua method itulah salah satu contoh method dari Schema Builder
Termasuk pula kode
Schema::create(“products”) yang berfungsi untuk membuat tabel baru dengan nama sesuai
parameter yang diberikan.

### Membuat Tabel
```php
Schema::create("products", function(Blueprint $table){
	// kode untuk mengubah kolom di sini
});
```
Penjelasan kode: Kode ini jika dijalankan oleh perintah php artisan migrate, akan membuat tabel baru
dengan nama products, kemudian callback pada argument kedua digunakan untuk selanjutnya menulis kode
untuk mengubah struktur tabel.

### Memilih Tabel
Selain membuat tabel baru, file Migration kita juga bisa melakukan perubahan struktur column dari suatu
tabel yang sudah ada.
```php
Schema::table("products", function(Blueprint $table){
// kode untuk mengubah kolom di sini
});
```
Penjelasan kode: Kode ini jika dijalankan tidak akan membuat tabel baru, tetapi memilih tabel products yang
sebelumnya sudah ada. Syaratnya adalah memang tabel yang kita pilih sudah ada di database kita.

### Mengecek apakah column / field sudah ada
Setelah menuliskan kode perintah membuat tabel, selanjutnya kita ingin menambah sebuah column dengan
nama description, akan tetapi asumsikan kita tidak tahu apakah tabel tersebut sebelumnya pernah dibuat dan
sudah memiliki column tersebut
```php
Schema::table("products", function(Blueprint $table){
$table->increments("id");
$descriptionSudahAda = $table->hasColumn("description")
if($descriptionSudahAda){
	} else {
// tabel belum memiliki column description
// buat column description dengan tipe string
$table->string("description");
}
$table->timestamps();
}));
```
Penjelasan kode: Perhatikan kode $table->hasColumn(“description”), kode ini akan menghasilkan Boolean,
yaitu true jika tabel products sudah memiliki column description dan akan menghasilkan false jika tabel
products belum memiliki column description.


###Memilih koneksi
```php
Schema::connection(‘pgsql’)->create('products', function (Blueprint
$table) {
$table->increments('id');
});
```

Penjelasan kode: Sebelum membuat tabel products, kita memilih koneksi yang akan digunakan yaitu pgsql,
koneksi ini harus tersedia pada konfigurasi config/database.php.
### Mengubah nama column
Untuk mengubah nama tabel kita gunakan method renameColumn().

```php
public function up(){
Schema::table("products", function(Blueprint $table){
$table->renameColumn("nama", "nama_baru");
});
}
```

### Menghapus column
Untuk mengubah column kita gunakan method dropColumn()

```php
public function up(){
Schema::table("products", function(Blueprint $table){
$table->dropColumn("nama_column");
});
}
```

### Menghapus tabel
```php
public function down()
{
// Drop tabel jika tabel products ditemukan
Schema::dropIfExists('products');
}
```

### Column Modifiers
Selain kita bisa menentukan tipe data dari column, dengan schema builder kita juga bisa mengubah /
menambah perilaku dari column tersebut. Misalnya, membuat column menjadi nullable, menambahkan
column setelah column tertentu. Kemampuan untuk melakukan hal itu disebut dengan modifier.

Misalkan kita mau
membuat column email, kita ingin meletakkannya setelah column password, dan mau kita beri komentar.

```php
public function up()
{
Schema::create(“users”, function(Blueprint $table){
$table->increments();
// begini cara pakai modifier
$table->string(“email”, 255)->after(“password”)-
>comment(“Email pengguna utama pengguna”);
})
}
```

### Mengubah Column Attribute
Untuk mengubah atribut kolom / field, terlebih dahulu kita membutuhkan package doctrine/dbal

```bash
composer require doctrine/dbal
```

Lalu kita bisa menggunakan sebuah method bernama change(). Contohnya adalah kita ingin mengubah
column "name" yang tadinya tidak menerima NULL / tidak boleh NULL ingin kita ubah agar menjadi nullable.

```php
Schema::table('users', function (Blueprint $table) {
$table->string('name', 50)->nullable()->change();
});
```

## Seeding
Apa itu seeding? Seeding jika diartikan secara harfiah bermakna memberi benih. Dalam konteks
pengembangan aplikasi, Seeding adalah memberikan data awal ke database.

### Membuat Seeder
Pertama, kita buat terlebih dahulu file seeder untuk tabel tertentu. Misalnya, kita akan menguji fitur product
list, itu berarti kita ingin menyiapkan data products terlebih dahulu ke database. Data ini hanyalah data
dummy.

```bash
php artisan make:seeder ProductTableSeeder
```

### Menjalankan Seeder

Setelah itu, kita akan eksekusi Seeder yang baru saja kita buat.
```bash
php artisan db:seed --class=ProductTableSeeder
```
Dengan perintah di atas kita menjalankan Seeding menggunakan class ProductTableSeeder. Setelah itu
data akan tersedia di tabel products.

### Menjalankan Multiple Seeder Class
Untuk melakukannya, pertama kita buka file DatabaseSeeder.php yang terletak di direktori
database/seeds. Lalu jalankan $this->call() dan isi parameter dengan class table yang kita ingin
eksekusi.

```php
<?php
use Illuminate\Database\Seeder;
class DatabaseSeeder extends Seeder
{
	/**
	* Seed the application's database.
	*
	* @return void
	*/
	public function run()
	{
		$this->call(UserTableSeeder::class);
		$this->call(ProductTableSeeder::class);
		$this->call(CategoryTableSeeder::class);
	}
}
```
Lalu tinggal jalankan perintah berikut:
```bash
php artisan db:seed
```

> Apabila ketika menjalankan perintah db:seed muncul error class doesn't exist. Jalankan
terlebih dahulu perintah composer dump-autoload lalu jalankan lagi perintah php artisan
db:seed

Selain menggunakan cara $this->call(UserTableSeeder::class), kita juga bisa menggunakan
sintak alternatif sepert ini

```bash
$this->call("UserTableSeeder");
```

### Latihan Praktik

1. Membuat migration untuk tabel categories dengan field-field berikut:
1. id: Integer {PK} {AutoIncrement}
2. name: String
3. created_at: Timestamps
4. updated_at: Timestamps
2. Membuat 10 untuk table categories di atas.
Langkah-langkahnya adalah sebagai berikut. Pertama kamu masuk ke workspace masing-masing dulu ya di
terminal (ingat caranya di bab instalasi dan konfigurasi).

### A. Membuat Tabel dan Strukturnya dengan Migration

1. Buat file migration untuk tabel categories

``` bash
php artisan make:migration create_categories_table
```
2. Buka file yang baru digenerate (nama file berakhiran create_categories_table.php) di direktori
database/migrations lalu pastikan kode nya menjadi seperti ini:
```php
<?php
use Illuminate\Support\Facades\Schema;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Database\Migrations\Migration;
class CreateCategoriesTable extends Migration
{
/**
* Run the migrations.
*
* @return void
*/
public function up()
{
Schema::create('categories', function (Blueprint $table) {
// buat column id -> integer, autoIncrement dan primary
$table->bigIncrements('id');
// buat column name -> string
$table->string("name");
// buat column created_at dan updated_at
$table->timestamps();
});
}
/**
* Reverse the migrations.
*
* @return void
*/
public function down()
{
// hapus jika tabel categories exist.
// untuk migrate:rollback
Schema::dropIfExists('categories');
}
}
```
3. Jalankan migration
```bash
php artisan migrate
```
B. Mengisi data awal dengan Seeder
1. Buat file seeder untuk tabel categories

```bash
php artisan make:seeder CategoryTableSeeder
```
2. Sebelum kita bisa insert data di Seeder kita perlu model category. Buat dulu Model Category,
pembahasan lebih lanjut akan kita pelajari di bab setelah ini.

```bash
php artisan make:model Category
```
3. Lalu buka file CategoryTableSeeder.php pada direktori database/seeds, dan isi kode berikut ini:
Perhatikan sebelum bisa menggunakan new Category kita menambahkan baris use App\Category; di
bagian atas file untuk mengimport model Category yang terletak di app/Category.php.

```php
<?php
use Illuminate\Database\Seeder;
use App\Category;
class CategoryTableSeeder extends Seeder
{
/**
* Run the database seeds.
*
* @return void
*/
public function run()
{
$kategori1 = new Category;
$kategori1->name = "Sepatu";
$kategori1->save();
}
}
```
Setelah itu eksekusi seed di atas dengan perintah ini"

```bash
php artisan db:seed --CategoryTableSeeder
```
## Model & Eloquent

```php
$kategori = new Category;
$kategori->name = "Sepatu";
$kategori->save();
```
Penjelasan Kode: Membuat model Category dengan perintah new Category disimpan sebagai variabel
$kategori lalu kita ubah field name menjadi "Sepatu" dan kita simpan ke database dengan perintah
$kategori->save()

### Model Attribute / properti
### Mengganti Tabel pada Model
Kita bisa melakukannya dengan menambahkan properti protected $table pada file model kita

```php
<?php
namespace App;
use Illuminate\Database\Eloquent\Model;
class Category extends Model
{
	protected $table = “custom_nama_table”;
}
```
Dengan definisi di atas, maka sekarang model Category merepresentasikan tabel custom_nama_table di
database.

### Menggunakan Koneksi Selain Default pada Model

Koneksi untuk sebuah model secara default menggunakan settingan global di file konfigurasi database kita.
Akan tetapi, misalnya untuk model tertentu kita ingin menggunakan koneksi selain default, maka kita gunakan
properti $connection pada model tersebut untuk menentukan koneksi mana yang ingin dipakai di model
yang bersangkutan.

```php
<?php
namespace App;
use Illuminate\Database\Eloquent\Model;
class Category extends Model
{
	protected $table = "custom_nama_table";
	protected $connection = “mongo_stage_1”;
}
```

### Mengubah Primary Key Model
```php
<?php
namespace App;
use Illuminate\Database\Eloquent\Model;
class Category extends Model
{
	protected $table = "custom_nama_tabel";
	protected $primaryKey = “identifier”;
}
```
Dengan kode di atas, maka sekarang model Product menggunakan field identifier pada tabel
custom_nama_tabel sebagai primaryKey.

### Mass-assignment
Mass-assignment merupakan cara untuk menginsert data
melalui model dengan sekali perintah.

contoh:
```php
$kategori = Category::create($request->all());
```

Perhatikan kita menginsert langsung ke tabel categories menggunakan model Category method create
dan mengisikan seluruh data yang kita ambil dari $request->all().
memang lebih cepat dengan mass-assignment, akan tetapi kita harus mempertimbangkan faktor
keamanan. Kenapa begitu? karena dengan mass-assignment kita membuka celah bagi user untuk mengakali
aplikasi kita.

### Mengizinkan Operasi Mass-assignment pada Properti Model
Properti $fillable kita gunakan untuk mengizinkan fields-fields apa saja yang diperbolehkan untuk operasi
mass assignment. Properti ini harus diassignkan dengan nilai array yang berisi nama field yang
diperbolehkan untuk mass assignment.

```php
<?php
namespace App;
use Illuminate\Database\Eloquent\Model;
class User extends Model
{
	protected $table = "custom_nama_table";
	protected $primaryKey = “identifier”;
	protected $fillable = ["name", "email", "address"];
}
```
Dengan kode di atas, kini fields name, email, dan address diperbolehkan untuk operasi mass-assignment,
sementara field role tidak diizinkan.

### Memproteksi Properti Model dari Operasi Mass-assignment
properti $guards memiliki fungsi sebaliknya dengan $fillable, yaitu untuk mendeklarasikan field apa saja yang
tidak diperbolehkan untuk mass-assignment.

```php
<?php
namespace App;
use Illuminate\Database\Eloquent\Model;
class User extends Model
{
	protected $table = "custom_nama_table";
	protected $primaryKey = "identifier";
	protected $guards = ["role"];
}
?>
```

Dengan kode di atas, maka kini kita mengizinkan semua field pada model User untuk operasi mass-
assignment, kecuali yang dilindungi (guarded) yaitu field role.

### Mencatat Kapan Data Model Dibuat dan Diupdate
Secara default model mengasumsikan tabel kita memiliki field created_at dan updated_at. Kedua field
tersebut merupakan penanda kapan suatu record tersebut dibuat dan kapan terakhir diupdate. Keduanya
akan otomatis diinsert atau diupdate ketika kita memanipulasi record menggunakan Eloquent.

Jika tabel kita tidak memiliki kedua field tersebut akan muncul error. Untuk menghindari error bila kita
memang tidak memerlukan field created_at dan updated_at.

```php
<?php
namespace App;
use Illuminate\Database\Eloquent\Model;
class Category extends Model
{
//
	protected $timestamps = false;
}
```

Selanjutnya, misalnya kita ingin menggunakan timestamps tapi menggunakan field lain kita bisa ubah dengan
const CREATED_AT dan UPDATED_AT seperti ini:

```php
<?php
namespace App;
use Illuminate\Database\Eloquent\Model;
class Category extends Model
{
	const CREATED_AT = "tanggal_dibuat";
	const UPDATED_AT = "tanggal_diupdate";
}
```

### Eloquent
Ketika kita membuat model di Laravel, sebetulnya kita membuat Eloquent Model. Atau dengan kata lain
semua model di Laravel merupakan turunan dari Eloquent model.

### Query Record
## Menampilkan seluruh record ##

Method all() digunakan untuk mengambil semua data model dari tabel database yang bersangkutan.
Misalnya model Product yang merepresentasikan tabel products, jika kita panggil method all seperti ini
Product:all(), maka kita mengambil seluruh row pada tabel products.

### Mencari record berdasarkan primaryKey
Method find() digunakan untuk mencari row berdasarkan primaryKey

```php
Product::find(1)
```
Penjelasan kode: Kode di atas mencari row di tabel products dengan id 1. Hasil dari method find() adalah
single record, yang berarti sebuah instance dari Model, bukan Array. Dengan begitu jangan coba-coba
melakukan perulangan (looping) ya karena akan error. 

### Mendapatkan record pertama saja
Selain method find(), kita juga bisa mencari single row dengan method first(), bedanya first tidak
menerima parameter apapun. Biasanya digunakan setelah kita melakukan filtering dengan method where()
seperti ini:

```php
Product::where("status", "active")->first();
```

Penjelasan kode: Kode di atas mencari row di tabel products dengan kriteria column status bernilai "active"
dan hanya ambil satu row pertama. Hasil dari first() juga merupakan instance dari model, bukan Array.


### findOrFail()
Method findOrFail() bekerja mirip dengan find(), bedanya saat tidak ada row yang ditemukan maka
akan otomatis melempar Error Model Not Found alias 404, bereda dengan find() yang
mengembalkan nilai NULL jika row tidak ditemukan dengan id sesuai parameter.

```php
Product::findOrFail(1);
```
Kode ini akan mencari row di tabel products dengan id 1, apabila tidak ada row yang
ditemukan dengan id 1 maka otomatis berikan error 404 ke user. Method ini kita gunakan jika kita tidak ingin
mengecek secara manual apakah row ditemukan atau tidak.

### Aggregates
Kode ini akan mencari row di tabel products dengan id 1, apabila tidak ada row yang
ditemukan dengan id 1 maka otomatis berikan error 404 ke user. Method ini kita gunakan jika kita tidak ingin
mengecek secara manual apakah row ditemukan atau tidak.

### count()
Menghitung jumlah row

### max()
Dapatkan nilai tertinggi dari kumpulan row berdasarkan field tertentu.
```php
Product::all()->max("price");
```
Penjelasan kode: Dapatkan nilai price tertinggi dari semua row di tabel products.

### min()
Dapatkan nilai terendah dari kumpulan row berdasarkan field tertentu.

```php
Product::all()->min("price");
```
Penjelasan kode: Dapatkan nilai price terendah dari semua row di tabel products.

### sum()
Jumlahkan nilai field tertentu

```php
Product::all()->sum("stock");
```
Penjelasan kode: Jumlahkan semua nilai stock dari tabel products, dengan begitu kita bisa mendapatkan
keseluruhan stock dari product yang ada di tabel.

### avg()
Dapatkan nilai rerata berdasarkan field tertentu pada sebuah tabel.

```php
Product::all()->avg("price");
```

Penjelasan kode: Dapatkan nilai rerata harga product dari seluruh data di tabel products.

### Insert Record
sekarang kita akan mulai belajar untuk memanipulasi data ke database. Operasi
pertama yang kita pelajari adalah insert record baru.
Ada dua cara untuk melakukan insert record ke dalam database yaitu dengan method save() dan
create()

### save()
Dengan method save() kita bisa menginsert record baru, caranya adalah kita buat model baru terlebih
dahulu baru kita panggil method save(). Contoh kodenya seperti ini

```php
$product_baru = new Product;
$product_baru->name = "Sepatu Coder";
$product_baru->description = "Deskripsi produk sepatu coder";
$product_baru->save();
```
Penjelasan kode: Untuk menggunakan method save() pertama kita buat terlebih dahulu model baru,
karena kita akan menginsert ke tabel products, maka kita buat model Product dengan new Product dan
kita simpan sebagai variabel $product_baru.
Setelah itu kita isi properti yang ada di model products, properti ini mewakili column yang ada di tabel
products, yaitu name dan description. Kita mengisinya dengan $product_baru->name = "Sepatu
Coder"; $product_baru->description = "Deskripsi sepatu coder";

### crate()
Cara lain untuk membuat record baru di database adalah dengan method create(). Cara penggunaannya
sedikit berbeda dengan save();

```php
$product_tas = Product::create([
	"name" => "Tas Selempang Army",
	"description" => "Deskripsi tas selempang army"
]);
```
Penjelasan kode: Insert ke tabel products dengan method create pada model Product, method create()
membutuhkan sebuah parameter bertipe array yang berisi data yang akan diinsert ke database.

> PENTING! Apa yang dilakukan oleh method create() adalah menggunakan cara mass-assignment,
method create bisa kita pakai, kita harus definisikan dulu properti apa saja dari model Product yang
boleh untuk operasi mass-assignment. Kita telah mempelajarinya pada sub bab mass-assignment.

### Update Record
### save() 
Method save() selain bisa kita gunakan untuk menginsert record baru ke database juga bisa kita gunakan
untuk mengupdate record tertentu. Ketika menyimpan kita membuat model baru dengan new NamaModel
misalnya new Product, sedangkan untuk mengupdate data, pertama kita query dulu record yang akan kita
update.

```php
// select dari tabel products dengan ID 20
$produk_untuk_diupdate = Product::findOrFail(20);
// ubah nilai description dari record yang kita dapatkan
$produk_untuk_diupdate->description = "Deskripsi kita ubah di sini";
// simpan perubahan ke database untuk product dengan ID 20 tadi
$product_untuk_diupdate->save();
```
Penjelasan kode: Mengupdate nilai column description untuk produk dengan ID 20 ke database.

### update()
Selain method save(), kita juga bisa mengupdate record dengan method update(); Method ini fungsinya
mirip dengan create() untuk membuat data. Perbedaannya tentu saja, method update untuk mengupdate
bukan membuat / menginsert record baru.

```php
App\Product::where("status", "active")->update(["status" => "inactive"]);
```
Penjelasan kode: Ubah status menjadi “inactive” dari semua data di tabel products yang saat ini memiliki
status bernilai “active”

Method update() sama seperti method create() membutuhkan sebuah parameter bertipe array yang
berisi data untuk diupdate ke database. Selain itu, untuk menggunakan method ini kita juga perlu
mendefinisikan field / column apa saja yang boleh untuk operasi mass-assignment.

### Delete Record
Ada dua cara untuk menghapus record di database kita ketika menggunakan Eloquent, yaitu dengan method
delete() dan destroy()

### Menghapus data model
Method ini kita panggil dari instance model tertentu. Misalnya pertama kita query terlebih dahulu sebuah
model berdasarkan primary key seperti ini

```php
$product = Product::findOrFail(21);
```
Kita cari product dengan id 21 dan simpan sebagai variabel $product. Lalu kita hapus model tersebut dengan
memanggil method delete seperti in

```php
$product->delete();
```
### Soft Deletes ###
Soft delete merupakan sebuah cara untuk menghapus sebuah record tanpa langsung menghapusnya dari
database. Dengan kata lain sebetulnya data yang disoft delete masih ada di record tabel, akan tetapi column
deleted_at di record tertentu akan diisi sebuah nilai yaitu timestamp kapan model tersebut dihapus.
Jika column deleted_at tidak null alias ada isiannya, maka model tersebut statusnya adalah sedang
dihapus.

Jika kita ingin menggunakan fitur soft deletes, pertama kita harus buat sebuah column di tabel kita bernama
deleted_at, jika kita menggunakan file Migration kita bisa membuat field tersebut dengan memanggil
method softDeletes()dari Schema Builder (lihat kembali bahasan Migration).
Kemudian kita harus menggunakan trait Illuminate\Database\Eloquent\SoftDeletes pada model
yang ingin kita aktifkan soft delete. Sehingga model kita akan tampak seperti ini

```php
<?php
namespace App;
use Illuminate\Database\Eloquent\Model;
// PERHATIKAN INI!
use Illuminate\Database\Eloquent\SoftDeletes;
class Category extends Model
{
  // DAN INI
  use SoftDeletes;
}
```
Jika tanpa menggunakan soft delete, ketika kita memanggil delete() atau destroy() maka record yang
kita pilih akan langsung dihapus dari tabel. Sedangkan jika menggunakan soft delete tidak seperti itu. Saat
kita mengaktifkan soft delete maka ingat, kita memiliki column deleted_at pada tabel, column ini defaultnya
bernilai NULL. Dan NULL berarti record tersebut dalam kondisi tidak dihapus, kemudian saat kita memanggil
method delete() atau destroy() maka record yang kita pilih memiliki nilai pada column deleted_at,
artinya column deleted_at tidak lagi bernilai NULL, sehingga kini record-record tersebut statusnya soft
deleted (dihapus), tetapi tetap tersimpan di tabel. Jika kita query menggunakan eloquent, record-record ini
tidak akan muncul karena dianggap telah dihapus.

### trashed()###
Method trashed() digunakan untuk mengecek apakah sebuah model dalam kondisi di tempat sampah
alias soft deleted. Seperti ini:
```php
$product = App\Product::findOrFail(23);
if($product->trashed()){
// ya product sedang berada di tempat sampah / soft deleted
}
```

Penjelasan kode: Melakukan pengecekkan apakah record di tabel products dengan ID 23 sedang di tong
sampah. Method trashed() mengembalikan nilai Boolean, true jika model tersebut soft deleted dan
false jika model tersebut dalam kondisi tidak disoft deleted.

### restore() ###
Method restore() kita gunakan untuk mengembalikan model yang sebelumnya berada di tong sampah /
soft deleted agar kembali aktif atau tidak soft deleted. Melanjutkan contoh kode sebelumnya, kita ingin
merestore product dengan id tertentu apabila statusnya sekarang soft deleted, maka kita lakukan seperti
ini:

```php
// cari produk di tabel products dengan ID == 23
// jika gagal lempar error 404
$product = App\Product::findOrFail(23);
// jika ketemu dan statusnya soft deleted (trashed)
// restore
if($product->trashed()){
	$product->restore();
}
```
Penjelasan kode: Dapatkan model products dengan id 23, kemudian cek menggunakan method trashed()
apakah sedang berada di tong sampah / soft deleted, jika iya maka restore model tersebut.

### withTrashed() ###
Kami tadi menjelaskan bahwa ketika kita melakukan query terhadap model yang mengaktifkan soft delete,
maka semua record yang berstatus soft deleted tidak ikut terquery. Tetapi terkadang kita ingin menquery
keduanya, baik soft deleted atau tidak. Maka, untuk mengikutsertakan hasil dari model yang berstatus soft
deleted kita gunakan method withTrashed() pada query kita, misalnya seperti ini:

```php
$product = App\Product::where("owner", 12)->withTrashed()->get();
```
Penjelasan kode: Dapatkan record dari tabel products yang nilai column ownernya adalah 12, ikut sertakan
juga record yang berstatus soft deleted.

### onlyTrashed() ###
onlyTrashed()
Berbeda dengan withTrashed(), onlyTrashed() justru mengembalikan record-record yang berstatus
soft deleted saja. Tanpa menyertakan record yang belum disoft delete.

```php
$product_trashed = App\Product::where("owner", 12)->onlyTrashed()->get();
```

### forceDelete() ###
forceDelete() kita gunakan untuk menghapus sebuah record secara permanen meskipun kita
menggunakan softDelete pada model yang terkait.

## Data Pagination ##
Dengan menggunakan Eloquent untuk memanipulasi data, kita jadi lebih mudah untuk melakukan paging.
Fitur pagination bawaan Laravel mendukung baik jika kita menggunakan Query Builder atau Eloquent. Ada
dua metode yang bisa kita gunakan untuk melakuka pagination di laravel yaitu simplePaginate() dan
paginate().


### simplePaginate() ###
Method simplePaginate() memiliki sebuah parameter opsional yaitu berapa item yang ingin kita
tampilkan per halamannya. Misalnya kita ingin menampilkan data dari tabel products per halaman 25 product,
maka kita gunakan simplePaginate(25).

```php
<?php
namespace App\Http\Controllers;
use Illuminate\Http\Request;
class ProductController extends Controller
{
public function index()
{<?php
namespace App\Http\Controllers;
use Illuminate\Http\Request;
class ProductController extends Controller
{
public function index()
{
return view("products.list",
["products"=>\App\Product::simplePaginate(25)]);
}
}
	return view("products.list", ["products"=>\App\Product::simplePaginate(25)]);
}
}
```

### paginate() ###
Method paginate() bekerja seperti halnya simplePaginate() yaitu memiliki sebuah parameter opsional
berupa jumlah item yang ingin kita tampilkan per halaman. Untuk menggunakannya cukup tuliskan
paginate(25) misalnya.

```php
<?php
namespace App\Http\Controllers;
use Illuminate\Http\Request;
class ProductController extends Controller
{
public function index()
{
return view("products.list",
["products"=>\App\Product::paginate(25)]);
}
}
```

Perhatikan: Method simplePaginate() dan paginate()apabila tidak diberikan parameter maka default
data per halaman adalah 15

### Perbedaan ###
Perbedaan pertama antara simplePaginate dengan paginate adalah, simplePaginate kita gunakan jika kita
hanya ingin menampilkan tombol “next” dan “prev” tanpa perlu menampilkan informasi pagination lainnya
seperti halaman yang aktif, jumlah halaman, halaman terakhir, dll. Menggunakan simplePaginate akan lebih
efisien untuk data yang besar.
Perbedaan kedua adalah, ketika kita memanggil simplePaginate() dan paginate() maka kita akan bisa
mengakses beberapa method berkaitan dengan pagination di view kita, namun ada 2 method yang tidak
tersedia di simplePaginate() tapi tersedia jika menggunakan paginate().

### Menampilkan link pagination di view ##
```php
<ul>
@foreach($products as $p)
<li>{{$p->name}}</li>
@endforeach
</ul>
{{$products->links()}}
```


