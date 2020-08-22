# catatan-pentin-laravel

## Eloquent ORM
```php
$id = 1;
$user = User::find($id);
$user->name = "Muhammad Azamuddin";
$user->save();
```

```
> ## ORM memiliki kepanjangan Object Relational Mapping, dengan ORM kita bisa melakukan operasi
CRUD ke database tanpa harus menulis SQL Command. ORM inilah yang bertugas untuk menulis
SQL Command untuk kita. Laravel menggunakan ORM yang bernama Eloquent.
```
