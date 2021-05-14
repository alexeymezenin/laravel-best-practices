![Laravel best practices](/images/logo-german.png?raw=true)

Translations:

[Nederlands](https://github.com/Protoqol/Beste-Laravel-Praktijken) (by [Protoqol](https://github.com/Protoqol))

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

[Polski](polish.md) (by [Karol Pietruszka](https://github.com/pietrushek))

[Türkçe](turkish.md) (by [Burak](https://github.com/ikidnapmyself))

[Deutsch](german.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Italiana](italian.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Azərbaycanca](https://github.com/Maharramoff/laravel-best-practices-az) (by [Maharramoff](https://github.com/Maharramoff))

[العربية](arabic.md) (by [ahmedsaoud31](https://github.com/ahmedsaoud31))

Es handelt sich hierbei nicht um eine Laravel-Adaption von SOLID-Prinzipien, Mustern usw. Hier finden Sie Best Practices, die in echten Laravel-Projekten normalerweise ignoriert werden.

## Inhaltsverzeichnis

[Single-Responsibility-Prinzip](#single-responsibility-prinzip)

[Fette Models, schlanke Controller](#fette-models-schlanke-controller)

[Validierung](#validierung)

[Geschäftslogik sollte in einer Serviceklasse sein](#geschäftslogik-sollte-in-einer-serviceklasse-sein)

[Wiederholen Sie sich nicht / Don't repeat yourself (DRY)](#wiederholen-sie-sich-nicht--dont-repeat-yourself-dry)

[Verwenden Sie Eloquent anstelle des Query Builders und rohen SQL-Abfragen. Bevorzugen Sie Collections gegenüber Arrays](#verwenden-sie-lieber-eloquent-anstelle-des-query-builders-und-rohen-sql-abfragen-bevorzugen-sie-collections-gegenüber-arrays)

[Massenzuordnung](#massenzuordnung)

[Führen Sie keine Abfragen in Blade-Templates aus und verwenden Sie eager loading (N + 1-Problem)](#führen-sie-keine-abfragen-in-blade-templates-aus-und-verwenden-sie-eager-loading-n--1-problem)

[Kommentieren Sie Ihren Code, aber bevorzugen Sie beschreibende Methoden- und Variablennamen gegenüber Kommentaren](#kommentieren-sie-ihren-code-aber-bevorzugen-sie-beschreibende-methoden--und-variablennamen-gegenüber-kommentaren)

[Schreiben Sie kein JS und CSS in Blade-Templates und schreiben Sie kein HTML in PHP-Klassen](#schreiben-sie-kein-js-und-css-in-blade-templates-und-schreiben-sie-kein-html-in-php-klassen)

[Verwenden Sie Konfigurations- und Sprachdateien und Konstanten anstelle von Text im Code](#verwenden-sie-konfigurations--und-sprachdateien-und-konstanten-anstelle-von-text-im-code)

[Verwenden Sie Standard-Laravel-Tools, die von der Community akzeptiert werden](#verwenden-sie-standard-laravel-tools-die-von-der-community-akzeptiert-werden)

[Befolgen Sie die Namenskonventionen von Laravel](#befolgen-sie-die-namenskonventionen-von-laravel)

[Verwenden Sie nach Möglichkeit eine kürzere und besser lesbare Syntax](#verwenden-sie-nach-möglichkeit-eine-kürzere-und-besser-lesbare-syntax)

[Verwenden Sie IoC-Container oder Facades, statt neue Klassen zu instanziieren](#verwenden-sie-ioc-container-oder-facades-statt-neue-klassen-zu-instanziieren)

[Rufen Sie Daten nicht direkt aus der `.env`-Datei ab](#rufen-sie-daten-nicht-direkt-aus-der-env-datei-ab)

[Speichern Sie Datumsangaben im Standardformat. Verwenden Sie Accessoren und Mutatoren, um das Datumsformat zu ändern](#speichern-sie-datumsangaben-im-standardformat-verwenden-sie-accessoren-und-mutatoren-um-das-datumsformat-zu-ändern)

[Andere gute Praktiken](#andere-gute-praktiken)

### **Single-Responsibility-Prinzip**

Eine Klasse und eine Methode sollten nur eine Verantwortung haben.

Schlecht:

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

Gut:

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

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Fette Models, schlanke Controller**

Fügen Sie die gesamte DB-bezogene Logik in Eloquent-Models oder in Repository-Klassen ein, wenn Sie Query Builder oder SQL-Rohabfragen verwenden.

Schlecht:

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

Gut:

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

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Validierung**

Verschieben Sie die Validierung von Controllern in Request-Klassen.

Schlecht:

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

Gut:

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

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Geschäftslogik sollte in einer Serviceklasse sein**

Ein Controller darf nur eine Verantwortung haben, also verschieben Sie die Geschäftslogik von Controllern in Serviceklassen.

Schlecht:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

Gut:

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

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Wiederholen Sie sich nicht / Don't repeat yourself (DRY)**

Verwenden Sie Code wieder, wenn Sie können. Das Single-Responsibility-Prinzip (SRP) hilft Doppelarbeit zu vermeiden. Verwenden Sie auch Blade Templates, Eloquent Scopes etc. wieder.

Schlecht:

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

Gut:

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

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Verwenden Sie lieber Eloquent anstelle des Query Builders und rohen SQL-Abfragen. Bevorzugen Sie Collections gegenüber Arrays**

Eloquent ermöglicht es, lesbaren und wartbaren Code schreiben. Außerdem verfügt Eloquent über großartige integrierte Tools wie Soft Deletes, Events, Scopes usw.

Schlecht:

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

Gut:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Massenzuordnung**

Schlecht:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

Gut:

```php
$category->article()->create($request->validated());
```

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Führen Sie keine Abfragen in Blade-Templates aus und verwenden Sie eager loading (N + 1-Problem)**

Schlecht (für 100 Benutzer werden 101 Datenbankabfragen ausgeführt):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Gut (für 100 Benutzer werden 2 Datenbankabfragen ausgeführt):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Kommentieren Sie Ihren Code, aber bevorzugen Sie beschreibende Methoden- und Variablennamen gegenüber Kommentaren**

Schlecht:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

Besser:

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

Gut:

```php
if ($this->hasJoins())
```

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Schreiben Sie kein JS und CSS in Blade-Templates und schreiben Sie kein HTML in PHP-Klassen**

Schlecht:

```php
let article = `{{ json_encode($article) }}`;
```

Besser:

```php
<input id="article" type="hidden" value='@json($article)'>

Oder

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

In einer JavaScript-Datei:

```javascript
let article = $('#article').val();
```

Am besten verwenden Sie ein spezielles PHP-zu-JS-Paket, um die Daten zu übertragen.

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Verwenden Sie Konfigurations- und Sprachdateien und Konstanten anstelle von Text im Code**

Schlecht:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

Gut:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Verwenden Sie Standard-Laravel-Tools, die von der Community akzeptiert werden**

Verwenden Sie vorzugsweise integrierte Laravel-Funktionen und Community-Pakete, anstatt Pakete und Tools von Drittanbietern zu verwenden. Ansonsten muss jeder Entwickler, der in Zukunft an Ihrer App arbeitet, neue Tools erlernen. Außerdem sind die Chancen, Hilfe von der Laravel-Community zu erhalten, erheblich geringer, wenn Sie ein Paket oder Tool eines Drittanbieters verwenden. Lassen Sie Ihren Kunden nicht dafür bezahlen.

Aufgabe | Standardwerkzeuge | Tools von Drittanbietern
------------ | ------------- | -------------
Autorisierung | Policies | Entrust, Sentinel und andere Pakete
Assets kompilieren | Laravel Mix | Grunt, Gulp, 3rd-Party-Pakete
Entwicklungsumgebung | Laravel Sail, Homestead | Docker
Bereitstellung | Laravel Forge | Deployer und andere Lösungen
Unit Tests | PHPUnit, Mockery | Phpspec
Browsertests | Laravel Dusk | Codeception
DB | Eloquent | SQL, Doctrine
Templates | Blade | Twig
Mit Daten arbeiten | Laravel Collections | Arrays
Formularvalidierung | Request-Klassen | Pakete von Drittanbietern, Validierung im Controller
Authentifizierung | Integriert | Pakete von Drittanbietern, Ihre eigene Lösung
API-Authentifizierung | Laravel Passport, Laravel Sanctum | JWT- und OAuth-Pakete von Drittanbietern
API erstellen | Integriert | Dingo API und ähnliche Pakete
Mit DB-Struktur arbeiten | Migrationen | Direkt mit der DB-Struktur arbeiten
Lokalisierung | Integriert | Pakete von Drittanbietern
Echtzeit-Benutzeroberflächen | Laravel Echo, Pusher | Pakete von Drittanbietern und direktes Arbeiten mit WebSockets
Testdaten generieren | Seeder-Klassen, Model Factories, Faker | Testdaten manuell erstellen
Aufgabenplanung | Laravel Task Scheduler | Skripte und Pakete von Drittanbietern
DB | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Befolgen Sie die Namenskonventionen von Laravel**

Folgen Sie den [PSR standards](http://www.php-fig.org/psr/psr-2/).

Befolgen Sie außerdem die von der Laravel-Community akzeptierten Namenskonventionen:

Was | Wie | Gut | Schlecht
------------ | ------------- | ------------- | -------------
Controller | singular | ArticleController | ~~ArticlesController~~
Route | plural | articles/1 | ~~article/1~~
Benannte Route | snake_case mit Punktnotation | users.show_active |~~users.show-active, show-active-users~~
Model | singular | User |~~Users~~
hasOne oder belongsTo Beziehung | singular | articleComment | ~~articleComments, article_comment~~
Alle anderen Beziehungen | plural | articleComments | ~~articleComment, article_comments~~
Tabelle | Plural | article_comments |~~article_comment, articleComments~~
Pivot-Tabelle | singuläre Modellnamen in alphabetischer Reihenfolge | article_user | ~~user_article, articles_users~~
Tabellenspalte | snake_case ohne Modellname | meta_title | ~~MetaTitle; article_title_title~~
Model-Eigenschaft | snake_case | $model->created_at | ~~$model->createdAt~~
Fremdschlüssel | singulärer Modellname mit Suffix _id | article_id | ~~ArticleId, id_article, articles_id~~
Primärschlüssel | - | id | ~~custom_id~~
Migration | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
Methode | camelCase | getAll | ~~get_all~~
Methode im Ressourcencontroller | [Tabelle](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
Methode in einer Testklasse | camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
Variable | camelCase | $articlesWithAuthor | ~~$articles_with_author~~
Collection | beschreibend, plural | $activeUsers = User::active()->get() | ~~$active, $data~~
Objekt | beschreibend, singular | $activeUser = User::active()->first() | ~~$users, $obj~~
Konfigurations- und Sprachdateien index | snake_case | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
View | kebab-case | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
Config | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Vertrag (Interface) | Adjektiv oder Substantiv | AuthenticationInterface | ~~Authenticatable, IAuthentication~~
Trait | Adjektiv | Notifiable | ~~NotificationTrait~~

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Verwenden Sie nach Möglichkeit eine kürzere und besser lesbare Syntax**

Schlecht:

```php
$request->session()->get('cart');
$request->input('name');
```

Gut:

```php
session('cart');
$request->name;
```

Mehr Beispiele:

Gängige Syntax | Kürzere und lesbarere Syntax
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

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Verwenden Sie IoC-Container oder Facades, statt neue Klassen zu instanziieren**

Die Syntax `new Class` erzeugt eine enge Kopplung zwischen Klassen und erschwert dadurch das Testen. Verwenden Sie stattdessen IoC-Container oder Facades.

Schlecht:

```php
$user = new User;
$user->create($request->validated());
```

Gut:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Rufen Sie Daten nicht direkt aus der `.env`-Datei ab**

Übergeben Sie die Daten stattdessen an eine Konfigurationsdatei und verwenden Sie dann die Hilfsfunktion `config()`, um die Daten in ihrer Anwendung zu verwenden.

Schlecht:

```php
$apiKey = env('API_KEY');
```

Gut:

```php
// config/api.php
'key' => env('API_KEY'),

// Verwendung der Daten
$apiKey = config('api.key');
```

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Speichern Sie Datumsangaben im Standardformat. Verwenden Sie Accessoren und Mutatoren, um das Datumsformat zu ändern**

Schlecht:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

Gut:

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

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)

### **Andere gute Praktiken**

Fügen Sie niemals Logik in Routes Dateien ein.

Minimieren Sie die Verwendung von vanilla PHP in Blade-Templates.

[🔝 Zurück zum Inhaltsverzeichnis](#inhaltsverzeichnis)
