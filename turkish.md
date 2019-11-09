![Laravel best practices](/images/logo-turkish.png?raw=true)

Çeviriler:

[Nederlands](https://github.com/Protoqol/Beste-Laravel-Praktijken) (by [Protoqol](https://github.com/Protoqol))

[한국어](https://github.com/xotrs/laravel-best-practices) (by [cherrypick](https://github.com/xotrs))

[Українська](ukrainian.md) (by [Tenevyk](https://github.com/tenevyk))

[Русский](russian.md)

[日本語](japanese.md) (by [2bo](https://github.com/2bo))

[漢語](chinese.md) (by [xiaoyi](https://github.com/Shiloh520))

[فارسی](persian.md) (by [amirhossein baghaie](https://github.com/amirbagh75))

[Português](https://github.com/jonaselan/laravel-best-practices) (by [jonaselan](https://github.com/jonaselan))

[Tiếng Việt](https://chungnguyen.xyz/posts/code-laravel-lam-sao-cho-chuan) (by [Chung Nguyễn](https://github.com/nguyentranchung))

[Español](spanish.md) (by [César Escudero](https://github.com/cedaesca))

[Français](french.md) (by [Mikayil S.](https://github.com/mikayilsrt))

[Polski](https://github.com/maciejjeziorski/laravel-best-practices-pl) (by [Maciej Jeziorski](https://github.com/maciejjeziorski))

[Türkçe](turkish.md) (by [Burak](https://github.com/ikidnapmyself))

[Deutsche](german.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Italiana](italian.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

Bu metin Laravel için SOLID prensipleri, patternler vb. şeylerin uygulaması değildir. Burada, Laravel projelerinde 
geliştiriciler tarafından dikkate alınmayan iyi ve kötü pratiklerin karşılaştırmalarını bulacaksınız.

**Çevirmenin Notu #1: Wikipedia'da Türkçe başlığı olmaması nedeniyle SOLID prensipleri hakkında açıklayıcı bir [Ekşi Sözlük entrysi](https://eksisozluk.com/entry/50438875)*  

## İçerik

[Single responsibility principle (Tek sorumluluk prensibi)](#single-responsibility-principle-tek-sorumluluk-prensibi)

[Büyük modeller, çirkin controllerlar](#büyük-modeller-çirkin-controllerlar)

[Validation (Veri Doğrulama)](#veri-doğrulama-validasyon)

[Business logic servis class'ında bulunmalıdır](#business-logic-servis-classında-bulunmalıdır)

[Kendini tekrar etme (DRY: Don't repeat yourself)](#kendini-tekrar-etme-dry-dont-repeat-yourself)

[Query Builder ve düz queryler kullanmak yerine Eloquent, array kullanmak yerine Collection kullanın](#query-builder-ve-düz-queryler-kullanmak-yerine-eloquent-array-kullanmak-yerine-collection-kullanın)

[Mass assignment (Toplu atama)](#mass-assignment-toplu-atama)

[Blade templatelerinde asla query çalıştırmayın, eager loading kullanın (N + 1 problemi)](#blade-templatelerinde-asla-query-çalıştırmayın-eager-loading-kullanın-n--1-problemi)

[Koda yorum yazın ancak öncelikli olarak anlamlı method ve değişken isimleri seçin](#koda-yorum-yazın-ancak-öncelikli-olarak-anlamlı-method-ve-değişken-isimleri-seçin)

[Blade içinde JS ve CSS kullanmayın ve PHP classlarına HTML yazmayın](#blade-içinde-js-ve-css-kullanmayın-ve-php-classlarına-html-yazmayın)

[Config ve language dosyalarını kullanın, kod içinde ise metin kullanmak yerine constant kullanın](#config-ve-language-dosyalarını-kullanın-kod-içinde-ise-metin-kullanmak-yerine-constant-kullanın)

[Laravel topluluğu tarafından kabul edilen standart araçları kullanın](#laravel-topluluğu-tarafından-kabul-edilen-standart-araçları-kullanın)

[Laravel'de isimlendirme](#laravelde-isimlendirme)

[Mümkün olduğunca daha kısa ve okunabilir syntax kullanın](#mümkün-olduğunca-daha-kısa-ve-okunabilir-syntax-kullanın)

[new Class kullanımı yerine IoC container ya da facade kullanın](#new-class-kullanımı-yerine-ioc-container-ya-da-facade-kullanın)

[`.env` dosyasından doğrudan veri çekmeyin](#env-dosyasından-doğrudan-veri-çekmeyin)

[Tarihleri standart formatta kaydedin. Tarihleri formatlamak için accessor ve mutator kullanın](#tarihleri-standart-formatta-kaydedin-tarihleri-formatlamak-için-accessor-ve-mutator-kullanın)

[Diğer iyi pratikler](#diğer-iyi-pratikler)

### **Single responsibility principle (Tek sorumluluk prensibi)**

Bir class ya da method'un tek bir görevi ve amacı olmalıdır.

Kötü:

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

İyi:

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

[🔝 Başa dön](#içerik)

### **Büyük modeller, çirkin controllerlar**

Bütün database ile ilişkili Query Builder ve raw SQL işlemlerini Eloquent modelinde ya da Repository class'ında tanımlayarak kullanın. 


Kötü:

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

İyi:

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

[🔝 Başa dön](#içerik)

### **Veri Doğrulama, Validasyon**

Validation işlemlerini controller içinde değil, Request classları içinde yapın.

Kötü:

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

İyi:

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

[🔝 Başa dön](#içerik)

### **Business logic servis class'ında bulunmalıdır**

Bir controller sadece bir görevden sorumlu olmalıdır. Bu nedenle business logic, controllerlar yerine servis classında tanımlanmalıdır.

Kötü:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

İyi:

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

[🔝 Başa dön](#içerik)

### **Kendini tekrar etme (DRY: Don't repeat yourself)**

Kullanabildiğiniz sürece kodu tekrar kullanın. SRP (single responsibility principle - tek sorumluluk ilkesi) size kod 
tekrarını azaltmanıza da katkı sunar. Blade templatelerini, Eloqunt scopelarını tekrar kullanın.

Kötü:

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

İyi:

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

[🔝 Başa dön](#içerik)

### **Query Builder ve düz queryler kullanmak yerine Eloquent, array kullanmak yerine Collection kullanın**

Eloquent okunabilir ve sürdürülebilir kod yazmanıza izin verir. Ayrıca, Eloquent güzel dahili araçlara sahiptir. Örnek olarak;
soft delete, event ve scope özellikleri verilebilir.

Kötü:

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

İyi:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 Başa dön](#içerik)

### **Mass assignment (Toplu atama)**

Kötü:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

İyi:

```php
$category->article()->create($request->validated());
```

[🔝 Başa dön](#içerik)

### **Blade templatelerinde asla query çalıştırmayın, eager loading kullanın (N + 1 problemi)**

Kötü (100 kullanıcı için, 101 DB tane query çalıştırılacak):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

İyi (100 kullanıcı için, 2 DB tane query çalıştırılacak):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 Başa dön](#içerik)

### **Koda yorum yazın ancak öncelikli olarak anlamlı method ve değişken isimleri seçin**

Kötü:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

Görece İyi:

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

İyi:

```php
if ($this->hasJoins())
```

[🔝 Başa dön](#içerik)

### **Blade içinde JS ve CSS kullanmayın ve PHP classlarına HTML yazmayın**

Kötü:

```php
let article = `{{ json_encode($article) }}`;
```

Daha İyi:

```php
<input id="article" type="hidden" value='@json($article)'>

Ya da

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

Javascript dosyasında:

```javascript
let article = $('#article').val();
```

Data transferi için en iyi yol amaca özel programlanmış PHP'den JS'ye veri aktaran paketleri kullanmaktır.

[🔝 Başa dön](#içerik)

### **Config ve language dosyalarını kullanın, kod içinde ise metin kullanmak yerine constant kullanın**

Kötü:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Makaleniz eklendi!');
```

İyi:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 Başa dön](#içerik)

### **Laravel topluluğu tarafından kabul edilen standart araçları kullanın**

Geniş topluluk desteği olmayan paketler yerine Laravel'e dahili gelen ya da topluluk tarafından kabul edilmiş araç ve paketleri kullanın.
Bu şekilde koda dahil olan herkesin rahatlıkla projeye katkı sunmasını sağlayabilirsiniz. Ayrıca topluluk desteği düşük olan
paketleri ya da araçları kullandığınızda yardım alabilme ihtimaliniz önemli derecede azalmaktadır. Bu paketleri tekrar
hazırlamak yerine, tercih edilen paketleri kullanın ve bu maliyetlerden kaçının.

*Çevirmen Notu #2: Tercihen en çok forklanmış, en çok yıldızlanmış, en çok takip edilen repositorylerden faydalanabilirsiniz. Koda yapılan son commit tarihi ve issue kısmında yanıtlanan soru ve cevap dökümleri paketin güncelliği ve ne kadar güncel kalacağı ile ilgili size bilgi sunar.*

Yapılacak | Standart araç | 3rd party araçlar
------------ | ------------- | -------------
Authorization (Yetkilendirme) | Policies | Entrust, Sentinel vb.
Compiling assets (CSS ve JS Derleme) | Laravel Mix | Grunt, Gulp, 3rd party paketler
Geliştirme Ortamı | Homestead | Docker
Deployment | Laravel Forge | Deployer and other solutions
Unit testing | PHPUnit, Mockery | Phpspec
Browser testing | Laravel Dusk | Codeception
DB | Eloquent | SQL, Doctrine
Template | Blade | Twig
Veri işleme | Laravel collections | Arrays
Form doğrulama | Request classları | 3rd party paketler, controllerda doğrulama
Authentication (Doğrulama) | Dahili | 3rd party paketler ya da kendi çözümünüz
API authentication (Doğrulama) | Laravel Passport | 3rd party JWT and OAuth packetleri
API Oluşturma | Dahili | Dingo API vb.
DB Yapısı | Migrations | Doğrudan DB yönetimi
Lokalizasyon (Yerelleştirme) | Dahili | 3rd party paketler
Gerçek zamanlı kullanıcı etkileşimi | Laravel Echo, Pusher | Doğrudan WebSocket kullanan 3rd party paketler
Test verisi oluşturmak | Seeder classları, Model Factoryleri, Faker | Oluşturup manuel test etmek
Görev Zamanlama | Laravel Task Scheduler | Scriptler ve 3rd party paketler
DB | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝 Başa dön](#içerik)

### **Laravel'de isimlendirme**

 [PSR standards](http://www.php-fig.org/psr/psr-2/) takip edin.
 
 Ayrıca, topluluk tarafından kabul gören isimlendirmeler:

Ne | Nasıl | İyi | Kötü
------------ | ------------- | ------------- | -------------
Controller | tekil | ArticleController | ~~ArticlesController~~
Route | çoğul | articles/1 | ~~article/1~~
Named route | snake_case ve dot notation (nokta kullanımı) | users.show_active | ~~users.show-active, show-active-users~~
Model | tekil | User | ~~Users~~
hasOne or belongsTo relationship | tekil | articleComment | ~~articleComments, article_comment~~
All other relationships | çoğul | articleComments | ~~articleComment, article_comments~~
Table | çoğul | article_comments | ~~article_comment, articleComments~~
Pivot table | tekil model isimleri alfabetik sırada | article_user | ~~user_article, articles_users~~
Table column | snake_case ve model adı olmadan | meta_title | ~~MetaTitle; article_meta_title~~
Model property | snake_case | $model->created_at | ~~$model->createdAt~~
Foreign key | tekil model adı ve _id suffix'i (soneki) | article_id | ~~ArticleId, id_article, articles_id~~
Primary key | - | id | ~~custom_id~~
Migration | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
Method | camelCase | getAll | ~~get_all~~
Method in resource controller | [table](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
Method in test class | camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
Variable | camelCase | $articlesWithAuthor | ~~$articles_with_author~~
Collection | tanımlayıcı, çoğul | $activeUsers = User::active()->get() | ~~$active, $data~~
Object | tanımlayıcı, tekil | $activeUser = User::active()->first() | ~~$users, $obj~~
Config and language files index | snake_case | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
View | kebab-case | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
Config | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Contract (interface) | sıfat ya da isim | Authenticatable | ~~AuthenticationInterface, IAuthentication~~
Trait | sıfat | Notifiable | ~~NotificationTrait~~

[🔝 Başa dön](#içerik)

### **Mümkün olduğunca daha kısa ve okunabilir syntax kullanın**

Kötü:

```php
$request->session()->get('cart');
$request->input('name');
```

İyi:

```php
session('cart');
$request->name;
```

Daha çok örnek:

Ortak syntax | Kısa ve daha okunabilir syntax
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

[🔝 Başa dön](#içerik)

### **new Class kullanımı yerine IoC container ya da facade kullanın**

new Class kullanımı classlar arası bağlantıları doğrudan kurar ve test sürecini karmaşıklaştırır. IoC container ya da facade kullanın.

Kötü:

```php
$user = new User;
$user->create($request->validated());
```

İyi:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 Başa dön](#içerik)

### **`.env` dosyasından doğrudan veri çekmeyin**

Veriyi config dosyasında çağırın ve `config()` helper fonksiyonunu kullanarak uygulama içinde erişin.

Kötü:

```php
$apiKey = env('API_KEY');
```

İyi:

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[🔝 Başa dön](#içerik)

### **Tarihleri standart formatta kaydedin. Tarihleri formatlamak için accessor ve mutator kullanın**

Kötü:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

İyi:

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

[🔝 Başa dön](#içerik)

### **Diğer iyi pratikler**

Route dosyalarına asla logic yazmayın.

Blade template dosyalarında vanilya PHP (düz PHP) kullanmayın.

[🔝 Başa dön](#içerik)
