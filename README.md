# Pratik-PDO
PDO ile yazılmış pratik ve kullanımı kolay bir veritabanı sınıfı

## Kullanımı
Veritabanındaki tablolarla çalışırken en çok yaptığımız işlemler CRUD (yani veri ekleme, okuma, güncelleme ve silme) işlemleridir. Bunu baz alarak yazılan bu sınıf sayesinde veri çekme/okuma, ekleme, silme, düzenleme işlemlerini kolayca yapabiliyoruz. Kendi projelerimde kullanmak için yazdığım bir sınıftır, paylaşmaktan mutluluk duyuyorum.

Veritabanından gelen veri; dizi (array) olarak değil, obje (object) olarak dönmektedir. Örneğin `$post['title']` yerine `$post->title` olarak kullanmanız gerekiyor. 

## Bağlantı Kurma ve Ayarlar
```php
$db = new Database('localhost', 'Veritabanı adı', 'Kullanıcı adı', 'Şifre');
```
Veritabanı bağlantısı sağladıktan sonra, ID'ye göre işlem yapacağımız bazı metodlar olacağı için tablomuzda kullandığımız asıl anahtar (primary key) adının `id` olması gerekiyor. Eğer farklı bir anahtar adı kullanıyorsanız `$db->setPrimaryKey('postId')` metodunu kullanabilirsiniz.

### Tekil Veri Çekme (getOne)
Bir satır veri çekmek için kullanılan method
```php
$post = Database::getOne('post', 'WHERE created = ?', array('2015-04-15 12:24:14'));
echo $post->title;
```

### Tekil Veri Çekme (execOne)
Bir satır veri çekmek için ya da sorgu çalıştırmak için kullanılır
```php
$post = Database::execOne('SELECT * FROM post WHERE created = ?', array('2015-04-15 12:24:14'));
echo $post->title;
```

### Tekil Veri Çekme (getId)
ID'ye göre tek satır veri çekmek için kullanılır
```php
$category = Database::getId('category', 5);
echo $category->name;
```

### Çoklu Veri Çekme (getAll)
Şart ve parametlere göre veritabanından çoklu veri çeker
```php
$posts = Database::getAll('post', 'WHERE draft = ?', array(0));
foreach ($posts as $post) {
  echo $post->title . '<br>';
}
```

### Çoklu Veri Çekme (execAll)
Şart ve parametlere göre veritabanından SQL kodları ile çoklu veri çeker
```php
$posts = Database::execAll('SELECT * FROM post WHERE draft = ?', array(0));
foreach ($posts as $post) {
  echo $post->title . '<br>';
}
```

### Veri Ekleme
Tabloya kolayca veri eklememizi sağlar
```php
$insert = Database::insert('post', array(
  'title' => $_POST['title'],
  'body' => $_POST['body'],
  'draft' => 0
));

echo $insert ? $insert . ' numaralı gönderi eklendi' : 'Gönderi eklenemedi';
```

### Veri Güncelleme
Eğer veritbanındaki belirli ID'ye ait satırı güncellemek istersek:
```php
$update = Database::update('post', 5, array(
  'title' => 'Yeni gönderi başlığı',
  'body' => 'Düzenlenen yeni gönderi içeriği',
  'draft' => 0
));

echo $update ? 'Gönderi başarıyla güncellendi' : 'Gönderi güncellenemedi';
```

Eğer ID olmadan farklı şart ve parametrelere göre güncellemek istersek şöyle kullanıyoruz:
```php
$update = Database::update('post', 0, array(
  'title' => 'Yeni gönderi başlığı',
  'body' => 'Düzenlenen yeni gönderi içeriği',
  'draft' => 0
), 'WHERE title = ?', array('Eski gönderi başlığı'));

echo $update ? 'Gönderi başarıyla güncellendi' : 'Gönderi güncellenemedi';
```

### Veri Silme
Güncelleme ile aynı şekilde sadece ID'ye göre silmek için
```php
$delete = Database::delete('post', 5);
echo $delete ? 'Gönderi başarıyla silindi' : 'Gönderi silinemedi';
```

ID olmadan, farklı şart ve parametrelerle silme işlemi yapmak istersek şu şekilde kullanıyoruz:
```php
$delete = Database::delete('post', 0, 'WHERE title = ?', array('Silinmelik gönderi'));
echo $delete ? 'Gönderi başarıyla silindi' : 'Gönderi silinemedi';
```

### Toplam Satır Sayısı Alma
```php
$count = Database::count('post');
echo $count ? 'Toplam ' . $count . ' gönderi mevcut' : 'Henüz hiç gönderi yok';
```
veya
```php
$count = Database::count('post', 'WHERE draft = ?', array(0));
echo $count ? 'Toplam ' . $count . ' yayımda olan gönderi mevcut' : 'Henüz hiç yayımlanmış gönderi yok';
```
veya SQL sorgusunu manuel yazmak gerekirse `execCount()` methodunu kullanabiliriz
```php
$count = Database::execCount('SELECT * FROM post WHERE draft = ?', array(1));
echo $count ? 'Toplam ' . $count . ' taslak olan gönderi mevcut' : 'Hiç taslak gönderi yok';
```
