
![Laravel best practices](/images/logo-english.png?raw=true)

Terjemahan :


[Nederlands](https://github.com/Protoqol/Beste-Laravel-Praktijken) (by [Protoqol](https://github.com/Protoqol))

[Indonesia](indonesia.md) (by [P0rguy](https://github.com/p0rguy))

[한국어](https://github.com/xotrs/laravel-best-practices) (by [cherrypick](https://github.com/xotrs))

[日本語](japanese.md) (by [2bo](https://github.com/2bo))

[漢語](chinese.md) (by [xiaoyi](https://github.com/Shiloh520))

[中文維基百科](traditional-chinese.md) (by [woeichern](https://github.com/woeichern))

[ภาษาไทย](thai.md) (by [kongvut sangkla](https://github.com/kongvut))

[বাংলা](bangla.md) (by [Anowar Hossain](https://github.com/AnowarCST))

[فارسی](persian.md) (by [amirhossein baghaie](https://github.com/amirbagh75))

[Português](https://github.com/jonaselan/laravel-best-practices) (by [jonaselan](https://github.com/jonaselan))

[Українська](ukrainian.md) (by [Tenevyk](https://github.com/tenevyk))

[Русский](russian.md)

[Tiếng Việt](https://chungnguyen.xyz/posts/code-laravel-lam-sao-cho-chuan) (by [Chung Nguyễn](https://github.com/nguyentranchung))

[Español](spanish.md) (by [César Escudero](https://github.com/cedaesca))

[Français](french.md) (by [Mikayil S.](https://github.com/mikayilsrt))

[Polski](https://github.com/maciejjeziorski/laravel-best-practices-pl) (by [Maciej Jeziorski](https://github.com/maciejjeziorski))

[Türkçe](turkish.md) (by [Burak](https://github.com/ikidnapmyself))

[Deutsch](german.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Italiana](italian.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Azərbaycanca](https://github.com/Maharramoff/laravel-best-practices-az) (by [Maharramoff](https://github.com/Maharramoff))

[العربية](arabic.md) (by [ahmedsaoud31](https://github.com/ahmedsaoud31))

Ini bukan adaptasi Laravel dari prinsip SOLID, pola dll. Di sini Anda akan menemukan praktik terbaik yang biasanya diabaikan dalam proyek Laravel kehidupan nyata.

## Contents

[Prinsip tanggung jawab tunggal](#prinsip-tanggung-jawab-tunggal)

[Model gemuk, Controller Kecil](#model-gemuk-controller-kecil)

[Validasi](#validasi)

[Logika bisnis harus dalam kelas layanan](#logika-bisnis-harus-dalam-kelas-layanan)

[Jangan ulangi diri sendiri (DRY)](#jangan-ulangi-diri-sendiri-dry)

[Lebih suka menggunakan Eloquent daripada menggunakan Query Builder dan query SQL mentah. Lebih suka koleksi daripada array](#lebih-suka-menggunakan-eloquent-daripada-menggunakan-query-builder-dan-query-sql-mentah-lebih-suka-koleksi-daripada-array)

[Tugas massal](#tugas-massal)

[Jangan mengeksekusi kueri dalam templat Blade dan menggunakan eager loading (masalah N + 1)](#jangan-mengeksekusi-kueri-dalam-templat-blade-dan-menggunakan-eager-loading-masalah-n1)

[Komentari kode Anda, tetapi lebih suka metode deskriptif dan nama variabel daripada komentar](#komentari-kode-anda-terapi-lebih-suka-metode-deskriptif-dan-nama-variable-daripada-komentar)

[Jangan letakkan JS dan CSS di templat Blade dan jangan letakkan HTML apa pun di kelas PHP](#jangan-letakkan-js-dan-css-di-templat-blade-dan-jangan-letakkan-html-apa-pun-di-kelas-php)

[Gunakan file config dan bahasa, konstanta alih-alih teks dalam kode](#gunakan-file-config-dan-bahasa-konstanta-alih-alih-teks-dalam-kode)

[Gunakan alat Laravel standar yang diterima oleh komunitas](#gunakan-alat-laravel-standar-yang-diterima-oleh-komunitas)

[Ikuti konvensi penamaan Laravel](#ikuti-konvensi-penamaan-laravel)

[Gunakan sintaks yang lebih pendek dan lebih mudah dibaca jika memungkinkan](#gunakan-sintaks-yang-lebih-pendek-dan-lebih-mudah-dibaca-jika-memungkinkan)

[Gunakan wadah atau fasad IoC sebagai ganti Kelas baru](#gunakan-wadah-atau-fasad-ioc-sebagai-ganti-kelas-baru)

[Jangan mendapatkan data dari file `.env` secara langsung](#jangan-mendapatkan-data-dari-file-env-secara-langsung)

[Simpan tanggal dalam format standar. Gunakan pengakses dan mutator untuk mengubah format tanggal](#simpan-tanggal-dalam-format-standar-gunakan-pengakses-dan-mutator-untuk-mengubah-format-tanggal)

[Praktik baik lainnya](#praktik-baik-lainnya)

### **Prinsip tanggung jawab tunggal**

Kelas dan metode seharusnya hanya memiliki satu tanggung jawab

Kurang Bagus:

```php
public function getFullNameAttribute()
{
    if (auth()->user() && auth()->user()->hasRole('client') && auth()->user()->isVerified()) {
        return 'Mr. ' . $this->first_name . ' ' . $this->middle_name . ' ' . $this->last_name;
    } else {
        return $this->first_name[0] . '. ' . $this->last_name;
    }
}
```

Bagus:

```php
public function getFullNameAttribute()
{
    return $this->isVerifiedClient() ? $this->getFullNameLong() : $this->getFullNameShort();
}

public function isVerifiedClient()
{
    return auth()->user() && auth()->user()->hasRole('client') && auth()->user()->isVerified();
}

public function getFullNameLong()
{
    return 'Mr. ' . $this->first_name . ' ' . $this->middle_name . ' ' . $this->last_name;
}

public function getFullNameShort()
{
    return $this->first_name[0] . '. ' . $this->last_name;
}
```

[🔝 Kembali ke Contents](#contents)

### **Model gemuk, Controller Kecil**

Masukkan semua logika terkait DB ke model Eloquent atau ke dalam kelas Repositori jika anda menggunakan Query Builder atau kueri SQL mentah.

Kurang Bagus:

```php
public function index()
{
    $clients = Client::verified()
        ->with(['orders' => function ($q) {
            $q->where('created_at', '>', Carbon::today()->subWeek());
        }])
        ->get();

    return view('index', ['clients' => $clients]);
}
```

Bagus:

```php
public function index()
{
    return view('index', ['clients' => $this->client->getWithNewOrders()]);
}

class Client extends Model
{
    public function getWithNewOrders()
    {
        return $this->verified()
            ->with(['orders' => function ($q) {
                $q->where('created_at', '>', Carbon::today()->subWeek());
            }])
            ->get();
    }
}
```

[🔝 Kemabli ke Contents](#contents)

### **Validasi**

Pindahkan validasi dari Controller ke class Request

Kurang Bagus:

```php
public function store(Request $request)
{
    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
        'publish_at' => 'nullable|date',
    ]);

    ....
}
```

Bagus:

```php
public function store(PostRequest $request)
{    
    ....
}

class PostRequest extends Request
{
    public function rules()
    {
        return [
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
            'publish_at' => 'nullable|date',
        ];
    }
}
```

[🔝 Kembali ke Contents](#contents)

### **Logika bisnis harus dalam kelas layanan**

Controller harus hanya memiliki satu tanggung jawab, jadi pindahkan logika bisnis dari Controller ke kelas Service

Kurang Bagus:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

Bagus:

```php
public function store(Request $request)
{
    $this->articleService->handleUploadedImage($request->file('image'));

    ....
}

class ArticleService
{
    public function handleUploadedImage($image)
    {
        if (!is_null($image)) {
            $image->move(public_path('images') . 'temp');
        }
    }
}
```

[🔝 Kemabli ke Contents](#contents)

### **Jangan ulangi diri sendiri (DRY)**

Gunakan kembali kode ketika anda bisa. SRP membantu anda menghindari duplikasi. juga gunakan kembali template blade gunakan lingkup Eloquent dll.

Kurang Bagus:

```php
public function getActive()
{
    return $this->where('verified', 1)->whereNotNull('deleted_at')->get();
}

public function getArticles()
{
    return $this->whereHas('user', function ($q) {
            $q->where('verified', 1)->whereNotNull('deleted_at');
        })->get();
}
```

Bagus:

```php
public function scopeActive($q)
{
    return $q->where('verified', 1)->whereNotNull('deleted_at');
}

public function getActive()
{
    return $this->active()->get();
}

public function getArticles()
{
    return $this->whereHas('user', function ($q) {
            $q->active();
        })->get();
}
```

[🔝 Kembali ke Contents](#contents)

### **Lebih suka menggunakan Eloquent daripada menggunakan Query Builder dan query SQL mentah. Lebih suka koleksi daripada array**

Eloquent memungkinkan anda menulis kode bisa dibaca dan dipelihara. juga Eloquent memilki alat bawaan yang hebat seperti penghapus lunak, acara, cakupan, dll

Kurang Bagus:

```sql
SELECT *
FROM `articles`
WHERE EXISTS (SELECT *
              FROM `users`
              WHERE `articles`.`user_id` = `users`.`id`
              AND EXISTS (SELECT *
                          FROM `profiles`
                          WHERE `profiles`.`user_id` = `users`.`id`) 
              AND `users`.`deleted_at` IS NULL)
AND `verified` = '1'
AND `active` = '1'
ORDER BY `created_at` DESC
```

Bagus:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 Kembali ke Contents](#contents)

### **Tugas massal**

Kurang Bagus:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

Bagus:

```php
$category->article()->create($request->validated());
```

[🔝 Kembali ke Contents](#contents)

### **Jangan mengeksekusi kueri dalam templat Blade dan menggunakan eager loading (masalah N + 1))**

Buruk ( untuk 100 pengguna, 101 permintaan DB akan dieksekusi):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Bagus (untuk 100 pengguna, 2 permintaan DB akan dieksekusi):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 Kembali ke Contents](#contents)

### **Komentari kode Anda, tetapi lebih suka metode deskriptif dan nama variabel daripada komentar**

Kurang Bagus:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

Lebih Baik:

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

Bagus:

```php
if ($this->hasJoins())
```

[🔝 Kembali ke Contents](#contents)

### **Jangan letakkan JS dan CSS di templat Blade dan jangan letakkan HTML apa pun di kelas PHP**

Kurang Bagus:

```php
let article = `{{ json_encode($article) }}`;
```

Lebih Baik:

```php
<input id="article" type="hidden" value='@json($article)'>

Atau

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

Dalam file javascript:

```javascript
let article = $('#article').val();
```

Cara terbaik adalah dengan menggunakan paket PHP ke JS khusus untuk mentransfer data.

[🔝 Kembali ke Contents](#contents)

### **Gunakan file config dan bahasa, konstanta alih-alih teks dalam kode**

Kurang Bagus:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

Bagus:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 Kembali ke Contents](#contents)

### **Gunakan alat Laravel standar yang diterima oleh komunitas**

Lebih suka menggunakan fungsionalitas bawaan dan paket komunitas daripada menggunakan paket dan alat pihak ke-3. Pengembang apa pun yang akan bekerja dengan aplikasi Anda di masa mendatang perlu mempelajari alat baru. Juga, peluang untuk mendapatkan bantuan dari komunitas Laravel jauh lebih rendah saat Anda menggunakan paket atau alat pihak ke-3. Jangan membuat klien Anda membayar untuk itu.

Task | Standard tools | 3rd party tools
------------ | ------------- | -------------
Authorization | Policies | Entrust, Sentinel and other packages
Compiling assets | Laravel Mix | Grunt, Gulp, 3rd party packages
Development Environment | Homestead | Docker
Deployment | Laravel Forge | Deployer and other solutions
Unit testing | PHPUnit, Mockery | Phpspec
Browser testing | Laravel Dusk | Codeception
DB | Eloquent | SQL, Doctrine
Templates | Blade | Twig
Working with data | Laravel collections | Arrays
Form validation | Request classes | 3rd party packages, validation in controller
Authentication | Built-in | 3rd party packages, your own solution
API authentication | Laravel Passport | 3rd party JWT and OAuth packages
Creating API | Built-in | Dingo API and similar packages
Working with DB structure | Migrations | Working with DB structure directly
Localization | Built-in | 3rd party packages
Realtime user interfaces | Laravel Echo, Pusher | 3rd party packages and working with WebSockets directly
Generating testing data | Seeder classes, Model Factories, Faker | Creating testing data manually
Task scheduling | Laravel Task Scheduler | Scripts and 3rd party packages
DB | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝 Kembali ke Contents](#contents)

### **Ikuti konvensi penamaan Laravel**

 Ikuti [PSR standards](http://www.php-fig.org/psr/psr-2/).
 
Juga, ikuti konvensi penamaan yang diterima oleh komunitas Laravel:

What | How | Good | Bad
------------ | ------------- | ------------- | -------------
Controller | singular | ArticleController | ~~ArticlesController~~
Route | plural | articles/1 | ~~article/1~~
Named route | snake_case with dot notation | users.show_active | ~~users.show-active, show-active-users~~
Model | singular | User | ~~Users~~
hasOne or belongsTo relationship | singular | articleComment | ~~articleComments, article_comment~~
All other relationships | plural | articleComments | ~~articleComment, article_comments~~
Table | plural | article_comments | ~~article_comment, articleComments~~
Pivot table | singular model names in alphabetical order | article_user | ~~user_article, articles_users~~
Table column | snake_case without model name | meta_title | ~~MetaTitle; article_meta_title~~
Model property | snake_case | $model->created_at | ~~$model->createdAt~~
Foreign key | singular model name with _id suffix | article_id | ~~ArticleId, id_article, articles_id~~
Primary key | - | id | ~~custom_id~~
Migration | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
Method | camelCase | getAll | ~~get_all~~
Method in resource controller | [table](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
Method in test class | camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
Variable | camelCase | $articlesWithAuthor | ~~$articles_with_author~~
Collection | descriptive, plural | $activeUsers = User::active()->get() | ~~$active, $data~~
Object | descriptive, singular | $activeUser = User::active()->first() | ~~$users, $obj~~
Config and language files index | snake_case | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
View | kebab-case | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
Config | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Contract (interface) | adjective or noun | AuthenticationInterface | ~~Authenticatable, IAuthentication~~
Trait | adjective | Notifiable | ~~NotificationTrait~~

[🔝 Kembali ke Contents](#contents)

### **Gunakan sintaks yang lebih pendek dan lebih mudah dibaca jika memungkinkan**

Kurang Bagus:

```php
$request->session()->get('cart');
$request->input('name');
```

Bagus:

```php
session('cart');
$request->name;
```

Contoh:

Common syntax | Shorter and more readable syntax
------------ | -------------
`Session::get('cart')` | `session('cart')`
`$request->session()->get('cart')` | `session('cart')`
`Session::put('cart', $data)` | `session(['cart' => $data])`
`$request->input('name'), Request::get('name')` | `$request->name, request('name')`
`return Redirect::back()` | `return back()`
`is_null($object->relation) ? null : $object->relation->id` | `optional($object->relation)->id`
`return view('index')->with('title', $title)->with('client', $client)` | `return view('index', compact('title', 'client'))`
`$request->has('value') ? $request->value : 'default';` | `$request->get('value', 'default')`
`Carbon::now(), Carbon::today()` | `now(), today()`
`App::make('Class')` | `app('Class')`
`->where('column', '=', 1)` | `->where('column', 1)`
`->orderBy('created_at', 'desc')` | `->latest()`
`->orderBy('age', 'desc')` | `->latest('age')`
`->orderBy('created_at', 'asc')` | `->oldest()`
`->select('id', 'name')->get()` | `->get(['id', 'name'])`
`->first()->name` | `->value('name')`

[🔝 Kembali ke Contents](#contents)

### **Gunakan wadah atau fasad IoC sebagai ganti Kelas baru**

Sintaks Kelas baru menciptakan kopling ketat antara kelas dan mempersulit pengujian. Gunakan wadah atau fasad IoC sebagai gantinya.

Kurang Bagus:

```php
$user = new User;
$user->create($request->validated());
```

Bagus:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 Kembali ke Contents](#contents)

### **Jangan mendapatkan data dari file `.env` secara langsung**

Alihkan data ke file konfigurasi sebagai gantinya dan kemudian gunakan fungsi pembantu `config ()` untuk menggunakan data dalam aplikasi.

Kurang Bagus:

```php
$apiKey = env('API_KEY');
```

Bagus:

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[🔝 Kembali ke Contents](#contents)

### **Simpan tanggal dalam format standar. Gunakan pengakses dan mutator untuk mengubah format tanggal**

Kurang Bagus:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

Bagus:

```php
// Model
protected $dates = ['ordered_at', 'created_at', 'updated_at'];
public function getSomeDateAttribute($date)
{
    return $date->format('m-d');
}

// View
{{ $object->ordered_at->toDateString() }}
{{ $object->ordered_at->some_date }}
```

[🔝 Kembali ke Contents](#contents)

### **Praktik baik lainnya**

Jangan pernah menaruh logika apa pun di file rute.

Minimalkan penggunaan vanilla PHP di templat Blade.

[🔝 Kemabali ke Contents](#contents)
