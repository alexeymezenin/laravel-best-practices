![Laravel best practices](/images/logo-english.png?raw=true)

Şeýle hem, [Laravel mysal programmasyna](https://github.com/alexeymezenin/laravel-realworld-example-app) we [Eloquent ORM tarapyndan döredilen SQL soraglaryna](https://github.com/alexeymezenin/eloquent-sql-reference) göz aýlamak gyzykly bolup biler.

[![Laravel mysal programmasy](/images/laravel-real-world-banner.png?raw=true)](https://github.com/alexeymezenin/laravel-realworld-example-app)

## Mazmuny

[Ýeke-täk jogapkärçilik prinsipi (Single responsibility principle)](#ýeke-täk-jogapkärçilik-prinsipi-single-responsibility-principle)

[Usullar diňe bir zady etmeli](#usullar-diňe-bir-zady-etmeli)

[Ýarpaýy kontrolýorlar, ýagly modeller](#ýarpaýy-kontrolýorlar-ýagly-modeller)

[Vallidasiýa](#vallidasiýa)

[Işewürlik logikasy hyzmat-klasslarynda](#işewürlik-logikasy-hyzmat-klasslarynda)

[Täzeden gaýtalama (DRY)](#täzeden-gaýtalama-dry)

[Eloquent'yi soraglar konstruktory (query builder) we database'ye çygly soraglar bilen işlemekden has gowy saýlaň. Koleksiýalar bilen işlemekni massiwler bilen işlemekden has gowy saýlaň.](#eloquentyi-soraglar-konstruktory-query-builder-we-databaseye-çygly-soraglar-bilen-işlemekden-has-gowy-saýlaň-koleksiýalar-bilen-işlemekni-massiwler-bilen-işlemekden-has-gowy-saýlaň)

[Massa täzelenmesini ulanyň (mass assignment)](#massa-täzelenmesini-ulanyň-mass-assignment)

[Soraglary görkezmelerde ýerine ýetirmäň we sabyrsyz ýüklemäni ulanyň (mesele N + 1)](#soraglary-görkezmelerde-ýerine-ýetirmäň-we-sabyrsyz-ýüklemäni-ulanyň-mesele-n--1)

[Çok sanly maglumatlar bilen işleşende chunk usulyny ulanyň](#çok-sanly-maglumatlar-bilen-işleşende-chunk-usulyny-ulanyň)

[JS we CSS-i Blade şablonlaryndan çykaryp, HTML-i PHP kodundan aýyryň](#js-we-css-i-blade-şablonlaryndan-çykaryp-html-i-php-kodundan-aýyryň)

[Topar tarapyndan kabul edilen gurallary we amalyýetleri ulanyň](#topar-tarapyndan-kabul-edilen-gurallary-we-amalyýetleri-ulanyň)

[Jemgyýetiniň atlandyrma baradaky ylalaşyklaryna eýeriň.](#jemgyýetiniň-atlandyrma-baradaky-ylalaşyklaryna-eýeriň)

[new Class ulanmagyň ýerine IoC ýa-da fasadlary ulanyň.](#new-class-ulanmagyň-ýerine-ioc-ýa-da-fasadlary-ulanyň)

[.env faýlyndan maglumatlar bilen gönüden-göni işlemäň](#env-faýlyndan-maglumatlar-bilen-gönüden-göni-işlemäň)

### **Ýeke-täk jogapkärçilik prinsipi (Single responsibility principle)**

Her bir klass diňe bir borçly bolmaly.

Goýy:

```php
public function update(Request $request): string
{
    $validated = $request->validate([
        'title' => 'required|max:255',
        'events' => 'required|array:date,type'
    ]);

    foreach ($request->events as $event) {
        $date = $this->carbon->parse($event['date'])->toString();

        $this->logger->log('Update event ' . $date . ' :: ' . $);
    }

    $this->event->updateGeneralEvent($request->validated());

    return back();
}
```

Gowy:

```php
public function update(UpdateRequest $request): string
{
    $this->logService->logEvents($request->events);

    $this->event->updateGeneralEvent($request->validated());

    return back();
}
```

[🔝 Ýokary](#Mazmuny)


### **Usullar diňe bir zady etmeli**

Funksiýa diňe bir zady etmeli we ony gowy etmeli.

Goýy:

```php
public function getFullNameAttribute(): string
{
    if (auth()->user() && auth()->user()->hasRole('client') && auth()->user()->isVerified()) {
        return 'Mr. ' . $this->first_name . ' ' . $this->middle_name . ' ' . $this->last_name;
    } else {
        return $this->first_name[0] . '. ' . $this->last_name;
    }
}
```

Gowy:

```php
public function getFullNameAttribute(): string
{
    return $this->isVerifiedClient() ? $this->getFullNameLong() : $this->getFullNameShort();
}

public function isVerifiedClient(): bool
{
    return auth()->user() && auth()->user()->hasRole('client') && auth()->user()->isVerified();
}

public function getFullNameLong(): string
{
    return 'Mr. ' . $this->first_name . ' ' . $this->middle_name . ' ' . $this->last_name;
}

public function getFullNameShort(): string
{
    return $this->first_name[0] . '. ' . $this->last_name;
}
```

[🔝 Ýokary](#Mazmuny)

### **Ýarpaýy kontrolýorlar, ýagly modeller**

Maglumaty işlemek işini modellere çykaryň

Goýy:

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

Gowy:

```php
public function index()
{
    return view('index', ['clients' => $this->client->getWithNewOrders()]);
}

class Client extends Model
{
    public function getWithNewOrders(): Collection
    {
        return $this->verified()
            ->with(['orders' => function ($q) {
                $q->where('created_at', '>', Carbon::today()->subWeek());
            }])
            ->get();
    }
}
```

[🔝 Ýokary](#Mazmuny)

### **Vallidasiýa**

Ýarpaýy kontrolýor we SRP prinsiplerine laýyklykda, validasiýany kontrolýordan Request klasslaryna çykaryň.

Goýy:

```php
public function store(Request $request)
{
    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
        'publish_at' => 'nullable|date',
    ]);

    ...
}
```

Gowy:

```php
public function store(PostRequest $request)
{
    ...
}

class PostRequest extends Request
{
    public function rules(): array
    {
        return [
            'title' => 'required|unique:posts|max:255',
            'body' => 'required',
            'publish_at' => 'nullable|date',
        ];
    }
}
```

[🔝 Ýokary](#Mazmuny)

### **Işewürlik logikasy hyzmat-klasslarynda.**

Kontrolýor diňe özniň doly borçlaryny ýerine ýetirmeli, şonuň üçin ähli işewürlik logikasyny aýratyn klasslara we hyzmat-klasslaryna çykaryň.

Goýy:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ...
}
```

Gowy:

```php
public function store(Request $request)
{
    $this->articleService->handleUploadedImage($request->file('image'));

    ...
}

class ArticleService
{
    public function handleUploadedImage($image): void
    {
        if (!is_null($image)) {
            $image->move(public_path('images') . 'temp');
        }
    }
}
```

[🔝 Ýokary](#Mazmuny)

### **Täzeden gaýtalama (DRY)**

Bu prinsip size kody mümkin boldugyça her ýerde gaýtadan ulanmaga çagyryş edýär. Egerde siz SRP prinsipine eýerýän bolsaňyz, siz öňünden gaýtalanmalardan gaça durýarsyňyz, emma Laravel size şonuň ýaly hem görkezmeleri, Eloquent soraglarynyň böleklerini we ş.m. gaýtadan ulanmak mümkinçiligini berýär.

Goýy:

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

Gowy:

```php
public function scopeActive($q)
{
    return $q->where('verified', true)->whereNotNull('deleted_at');
}

public function getActive(): Collection
{
    return $this->active()->get();
}

public function getArticles(): Collection
{
    return $this->whereHas('user', function ($q) {
            $q->active();
        })->get();
}
```

[🔝 Ýokary](#Mazmuny)

### **Eloquent'yi soraglar konstruktory (query builder) we database'ye çygly soraglar bilen işlemekden has gowy saýlaň. Koleksiýalar bilen işlemekni massiwler bilen işlemekden has gowy saýlaň.**

Eloquent, mümkin boldugyça okalýan kody ýazmaga mümkinçilik berýär, programmanyň funksionallygyny üýtgetmek bolsa mukdarsyz has ýeňildir. Eloquent'de şeýle hem bir topar amatly we güýçli gurallar bar. Siziň üçin gyzykly bolup biler [Eloquent soraglaryny SQL'ye terjime etmek üçin gollanma.](https://github.com/alexeymezenin/eloquent-sql-reference)

Goýy:

```php
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

Gowy:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 Ýokary](#Mazmuny)

### **Massa täzelenmesini ulanyň (mass assignment)**

Goýy:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;

// Makalany kategoriýaga baglaň.
$article->category_id = $category->id;
$article->save();
```

Gowy:

```php
$category->article()->create($request->validated());
```

[🔝 Ýokary](#Mazmuny)

### **Soraglary görkezmelerde ýerine ýetirmäň we sabyrsyz ýüklemäni ulanyň (mesele N + 1)**

Goýy (100 ulanyjy üçin 101 sorag database'ye ýerine ýetiriler):

```blade
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Gowy (100 ulanyjy üçin 2 sorag database'ye ýerine ýetirilýär):

```php
$users = User::with('profile')->get();

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 Ýokary](#Mazmuny)

### **Çok sanly maglumatlar bilen işleşende chunk usulyny ulanyň**

Goýy:

```php
$users = $this->get();

foreach ($users as $user) {
    ...
}
```

Gowy:

```php
$this->chunk(500, function ($users) {
    foreach ($users as $user) {
        ...
    }
});
```

[🔝 Ýokary](#Mazmuny)

### **Okalýan atlary we metodlary, düşündirişlere (kommentariýalara) garanda has gowy saýlaň**

Goýy:

```php
// Determine if there are any joins
if (count((array) $builder->getQuery()->joins) > 0)
```

Gowy:

```php
if ($this->hasJoins())
```

[🔝 Ýokary](#Mazmuny)

### **JS we CSS-i Blade şablonlaryndan çykaryp, HTML-i PHP kodundan aýyryň.**

Goýy:

```javascript
let article = `{{ json_encode($article) }}`;
```

Gowy:

```php
<input id="article" type="hidden" value='@json($article)'>

Ýa-da

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

Javascript faýlynda:

```javascript
let article = $('#article').val();
```

Has gowusy, maglumatlary backend-den frontend'e geçirmek üçin ýöriteleşdirilen paket ulanyň.

[🔝 Ýokary](#Mazmuny)

### **Konfigurasiýalar, dil faýllary we sabitler kodyň içinde tekstiň ýerine ulanylmaly**

Kodda hiç bir tekst bolanok bolmaly.

Goýy:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Makalaňyz üstünlikli goşuldy');
```

Gowy:

```php
public function isNormal(): bool
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 Ýokary](#Mazmuny)

### **Topar tarapyndan kabul edilen gurallary we amalyýetleri ulanyň**

Laravel, giňden duş gelýän meseleleri çözmek üçin utgaşdyrylan gurallara eýe. Şol gurallary ulanmaga has köp üns beriň, daşarky paketler we gurallary ulanyp çözgüt tapmaga garanda. Laravel-de täze gelen bir geliştirici, size degişli bolan bir projede işlärkä, täze bir guralla işleşmegi öwrenmeli bolar, bu bolsa ähli netijelere getirip biler. Hem-de jemgyýetden kömek almak has kyn bolar. Müşderä ýa-da iş berijiňiz üçin öz "velosipedleriňizi" döretmäň.

Wazifa | Adaty gurallar | Adaty bolmadyk gurallar
------------ | ------------- | -------------
Autentifikasiýa | Syýasatlar | Entrust, Sentinel we beýleki paketler, öz çözgüt
Işlemek JS, CSS we ş.m. bilen | Laravel Mix, Vite | Grunt, Gulp, daşarky paketler
Önümçilik gurşawy | Laravel Sail, Homestead | Docker
Arza ýerleşdirmek | Laravel Forge | Deployer we köp sanly başga
Synaglar | Phpunit, Mockery | Phpspec, Pest
e2e synaglary | Laravel Dusk | Codeception
Işlemek Baza bilen | Eloquent | SQL, soraglar gurluşy, Doctrine
Şablonlar | Blade | Twig
Maglumatlar bilen işlemek | Laravel Koleksiýalary | Massiwler
Formanyň validasiýasy | Request klasslary | Daşarky paketler, kontrolýorda validasiýa
Autentifikasiýa | Iňňän funksionallyk | Daşarky paketler, öz çözgüt
API autentifikasiýasy | Laravel Passport, Laravel Sanctum | Daşarky paketler, JWT, OAuth ulanýanlar
API döretmek | Iňňän funksionallyk | Dingo API we beýleki paketler
Baza gurluşy bilen işlemek | Migrasiýalar | Baza bilen doğrudan işlemek
Lokalizasiýa | Iňňän funksionallyk | Daşarky paketler
Maglumat alyş-berişi reňkde ýerine ýetirmek | Laravel Echo, Pusher | Paketler we websoketler bilen doğrudan işlemek
Synag maglumatlaryny döretmek | Seeder klasslary, model fabrikalary, Faker | El bilen doldurmak we paketler
Wezipe planlamasy | Laravel wezipe planlagyjy | Skriptler we daşarky paketler
Baza | MySQL, PostgreSQL, SQLite, SQL Server | MongoDb

[🔝 Ýokary](#Mazmuny)

### **Jemgyýetiniň atlandyrma baradaky ylalaşyklaryna eýeriň.**

Eýeriň [PSR standartlaryna](https://www.php-fig.org/psr/psr-12/) kody ýazanyňyzda.

Şeýle hem, beýleki atlandyrma ylalaşyklaryna eýeriň:

Näme | Düzgün | Kabullanýan | Kabullanmaýan
------------ | ------------- | ------------- | -------------
Kontrolýor | Ýeke-täk | ArticleController | ~~ArticlesController~~
Marşrutlar | Köp | articles/1 | ~~article/1~~
Marşrut atlary | snake_case | users.show_active | ~~users.show-active, show-active-users~~
Model | Ýeke-täk | User | ~~Users~~
HasOne we belongsTo aragatnaşyklary | Ýeke-täk | articleComment | ~~articleComments, article_comment~~
Başga ähli aragatnaşyklary | Köp | articleComments | ~~articleComment, article_comments~~
Tablisa | Köp | article_comments | ~~article_comment, articleComments~~
Pivot tablisa | Modelleriň atlary alfabetik tertipde, ýeke-täk görnüşde | article_user | ~~user_article, articles_users~~
Tablisadaky sütün | snake_case modeliň ady bolmazdan | meta_title | ~~MetaTitle; article_meta_title~~
Modeliň häsiýeti | snake_case | $model->created_at | ~~$model->createdAt~~
Daşary açar| Modelleriň atlary Ýeke-täk we _id | article_id | ~~ArticleId, id_article, articles_id~~
Esasy açar | - | id | ~~custom_id~~
Migrasiýa | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
Metod | camelCase | getAll | ~~get_all~~
Resurs kontrolýoryndaky metod | [tablisa](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
Synagdaky metod | camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
Üýtgeýänler | camelCase | $articlesWithAuthor | ~~$articles_with_author~~
Koleksiýa | düşündiriji, Köp | $activeUsers = User::active()->get() | ~~$active, $data~~
Obýekt | düşündiriji, Ýeke-täk | $activeUser = User::active()->first() | ~~$users, $obj~~
Konfigurasiýa we dil faýllaryndaky indeksler | snake_case | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
Görkezme | kebab-case | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
Konfigurasiýa faýly | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Kontrakt (interfeýs) | Sıfat ýa-da naýar | AuthenticationInterface | ~~Authenticatable, IAuthentication~~
Trait | Sıfat | Notifiable | ~~NotificationTrait~~
Trait [(PSR)](https://www.php-fig.org/bylaws/psr-naming-conventions/) | adjective | NotifiableTrait | ~~Notification~~
Enum | Ýeke-täk | UserType | ~~UserTypes~~, ~~UserTypeEnum~~
FormRequest | singular | UpdateUserRequest | ~~UpdateUserFormRequest~~, ~~UserFormRequest~~, ~~UserRequest~~
Seeder | singular | UserSeeder | ~~UsersSeeder~~

[🔝 Ýokary](#Mazmuny)

### **Ylalaşyklaryň konfigurasiýadan üstünligine üns beriň**

Kabul edilen ylalaşyklara eýerýän bolsaňyz, koda goşmaça konfigurasiýa goşmaga zerurlyk ýok.

Goýy:

```php
// Tabliçanyň ady 'customers'
// Esasy açar 'id'
class Customer extends Model
{
    const CREATED_AT = 'created_at';
    const UPDATED_AT = 'updated_at';

    protected $table = 'Customer';
    protected $primaryKey = 'customer_id';

    public function roles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class, 'role_customer', 'customer_id', 'role_id');
    }
}
```

Gowy:

```php
// Tabliçanyň ady 'customers'
// Esasy açar 'id'
class Customer extends Model
{
    public function roles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class);
    }
}
```

[🔝 Ýokary](#Mazmuny)

### **Mümkin ýerlerde gysga we okalýan sintaksis ulanyň**

Goýy:

```php
$request->session()->get('cart');
$request->input('name');
```

Gowy:

```php
session('cart');
$request->name;
```

Ýene mysallar:

Köp ulanylýan sintaksis | Has gysga we has okalýan sintaksis
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

[🔝 Ýokary](#Mazmuny)

### **new Class ulanmagyň ýerine IoC ýa-da fasadlary ulanyň.**

new Class arkaly klasslary ornaşdyrmak programmanyň bölekleriniň arasynda berk baglanyşyk döredýär we synag işlerini kynlaşdyrýar.
Şonuň üçin konteýneri ýa-da fasadlary ulanyň.

Goýy:

```php
$user = new User;
$user->create($request->validated());
```

Gowy:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

...

$this->user->create($request->validated());
```

[🔝 Ýokary](#Mazmuny)

### **`.env` faýlyndan maglumatlar bilen gönüden-göni işlemäň**

`.env` faýlyndan maglumatlary konfigurasiýa faýlyna geçiriň we ol maglumatlary ulamak üçin programmada `config()` funksiýasyny ulanyň.

Goýy:

```php
$apiKey = env('API_KEY');
```

Gowy:

```php
// config/api.php
'key' => env('API_KEY'),

// Programmada maglumatlary ulanyň
$apiKey = config('api.key');
```

[🔝 Ýokary](#Mazmuny)

### **Sene-maglumaty standart formatda saklaň. Formaty üýtgetmek üçin okyjylar (reader) we öwrüjiler (mutator) ulanyň.**

Goýy:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

Gowy:

```php
// Model
protected $casts = [
    'ordered_at' => 'datetime',
];
// Okap bilýän (accessor)
public function getSomeDateAttribute($date)
{
    return $date->format('m-d');
}

// Şablon
{{ $object->ordered_at->toDateString() }}
{{ $object->ordered_at->some_date }}
```

[🔝 Ýokary](#Mazmuny)

### **DocBlock ulanmaň**

DocBlock'lar kodyň okalýanyny peseldýär.
Olaryň ýerine metodlar üçin gowy atlary we häzirki zaman PHP sintaksisini ulanyň, mysal üçin gaýdýan tiplere düşündiriş goşuň.

Goýy:

```php
/**
 * Funksiýa setirde ASCII düzümine girmeýän belgileriň barlygyny barlaýar.
 *
 * @param string $string Frontend-den alýan setirimiz bolup, 
 *                       şol setirde ASCII düzümine girmeýän belgiler bolup biler.
 *                       Funksiýa, eger setirde beýle belgiler ýok bolsa, True gaýtarýar.
 *
 * @return bool
 * @author
 *
 * @license GPL
 */

public function checkString($string)
{
}
```

Gowy:

```php
public function isValidAsciiString(string $string): bool
{
}
```

[🔝 Ýokary](#Mazmuny)

### **Beýleki maslahatlar we amalyýetler**

Laravel we oňa meňzeş freýmworklar (RoR, Django) üçin ýat bolan patterneri we gurallary ulanmaň. Symfony (ýa-da Spring we ş.m.) ýaly ýollary halasaňyz, şeýle freýmworklary web programmalary döretmek üçin ulanmak has akylly bolar.

Logikany marşrutlarda ýerleşdirmäň.

Blade şablonlarynda "çygyly" PHP ulanmazlyga çalyşyň.

Synag işlerinde ýatda ýerleşdirilen maglumat bazasyny (in-memory DB) ulanyň.

Freýmworkyň standart gurallaryny üýtgetmäň, sebäbi bu freýmworky täzeläniňizde kynçylyk döredip biler.

PHP-nyň häzirki zaman sintaksisini ulanyň, emma şonuň bilen birlikde kodyň okalýandygyna üns beriň — okalýan kod elmydama möhüm.

View Composer ýaly gurallary ulanyň, ýöne ýokary seresaplyk bilen. Köplenç ýagdaýda, meseläniň başga bir çözgüdi tapmak mümkinçiligi bolýar.

[🔝 Ýokary](#Mazmuny)
