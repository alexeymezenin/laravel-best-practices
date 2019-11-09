![Laravel best practices](/images/logo-english.png?raw=true)

Translations:

[Nederlands](https://github.com/Protoqol/Beste-Laravel-Praktijken) (by [Protoqol](https://github.com/Protoqol))

[한국어](https://github.com/xotrs/laravel-best-practices) (by [cherrypick](https://github.com/xotrs))

[日本語](japanese.md) (by [2bo](https://github.com/2bo))

[漢語](chinese.md) (by [xiaoyi](https://github.com/Shiloh520))

[ภาษาไทย](thai.md) (by [kongvut sangkla](https://github.com/kongvut))

[فارسی](persian.md) (by [amirhossein baghaie](https://github.com/amirbagh75))

[Português](https://github.com/jonaselan/laravel-best-practices) (by [jonaselan](https://github.com/jonaselan))

[Українська](ukrainian.md) (by [Tenevyk](https://github.com/tenevyk))

[Русский](russian.md)

[Tiếng Việt](https://chungnguyen.xyz/posts/code-laravel-lam-sao-cho-chuan) (by [Chung Nguyễn](https://github.com/nguyentranchung))

[Español](spanish.md) (by [César Escudero](https://github.com/cedaesca))

[Français](french.md) (by [Mikayil S.](https://github.com/mikayilsrt))

[Polski](https://github.com/maciejjeziorski/laravel-best-practices-pl) (by [Maciej Jeziorski](https://github.com/maciejjeziorski))

[Türkçe](turkish.md) (by [Burak](https://github.com/ikidnapmyself))

[Deutsche](german.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Italiana](italian.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

Es handelt sich nicht um eine Laravel-Anpassung von SOLID-Prinzipien, Mustern usw. Hier finden Sie die Best Practices, die in echten Laravel-Projekten normalerweise ignoriert werden.

## Contents

[Prinzip der Einzelverantwortung](#single-responsibility-principle)

[Fette Models, dünne Controller](#fat-models-skinny-controllers)

[Validierung](#validation)

[Geschäftslogik sollte in der Serviceklasse sein](#business-logic-should-be-in-service-class)

[Wiederhole dich nicht (DRY)](#dont-repeat-yourself-dry)

[Ziehen Sie es vor, Eloquent anstelle von Query Builder und unformatierten SQL-Abfragen zu verwenden. Ziehen Sie Sammlungen Arrays vor](#prefer-to-use-eloquent-over-using-query-builder-and-raw-sql-queries-prefer-collections-over-arrays)

[Massenzuordnung](#mass-assignment)

[Führen Sie keine Abfragen in Blade-Vorlagen aus und verwenden Sie das eifrige Laden (N + 1-Problem).](#do-not-execute-queries-in-blade-templates-and-use-eager-loading-n--1-problem)

[Kommentieren Sie Ihren Code, bevorzugen Sie jedoch beschreibende Methoden- und Variablennamen gegenüber Kommentaren](#comment-your-code-but-prefer-descriptive-method-and-variable-names-over-comments)

[Setzen Sie JS und CSS nicht in Blade-Vorlagen und setzen Sie kein HTML in PHP-Klassen](#do-not-put-js-and-css-in-blade-templates-and-do-not-put-any-html-in-php-classes)

[Verwenden Sie Konfigurations- und Sprachdateien, Konstanten anstelle von Text im Code
](#use-config-and-language-files-constants-instead-of-text-in-the-code)

[Verwenden Sie Standard-Laravel-Tools, die von der Community akzeptiert werden
](#use-standard-laravel-tools-accepted-by-community)

[Befolgen Sie die Namenskonventionen von Laravel](#follow-laravel-naming-conventions)

[Verwenden Sie nach Möglichkeit eine kürzere und besser lesbare Syntax](#use-shorter-and-more-readable-syntax-where-possible)

[Verwenden Sie IoC-Container oder -Fassaden anstelle der neuen Klasse](#use-ioc-container-or-facades-instead-of-new-class)

[Rufen Sie keine Daten direkt aus der ENV-Datei ab](#do-not-get-data-from-the-env-file-directly)

[Speichern Sie Daten im Standardformat. Verwenden Sie Accessoren und Mutatoren, um das Datumsformat zu ändern](#store-dates-in-the-standard-format-use-accessors-and-mutators-to-modify-date-format)

[Andere gute Praktiken](#other-good-practices)

### **Prinzip der Einzelverantwortung**

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

[🔝Zurück zum Inhalt](#contents)

### **Fette Models, dünne Controller**

Fügen Sie die gesamte DB-bezogene Logik in Eloquent-Modelle oder in Repository-Klassen ein, wenn Sie Query Builder oder Raw SQL-Abfragen verwenden.

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

[🔝Zurück zum Inhalt](#contents)

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

[🔝Zurück zum Inhalt](#contents)

### **Geschäftslogik sollte in der Serviceklasse sein**

Ein Controller muss nur eine Verantwortung haben. Verschieben Sie die Geschäftslogik von Controllern in Serviceklassen.

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

[🔝Zurück zum Inhalt](#contents)

### **Wiederhole dich nicht (DRY)**

Code wiederverwenden, wenn Sie können. SRP hilft Ihnen, Doppelarbeit zu vermeiden. Verwenden Sie auch Blade-Vorlagen, eloquente Bereiche usw.

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

[🔝Zurück zum Inhalt](#contents)

### **Verwenden Sie lieber Eloquent als Query Builder und SQL-Rohdatenabfragen. Ziehen Sie Sammlungen Arrays vor**

Mit Eloquent können Sie lesbaren und wartbaren Code schreiben. Außerdem verfügt Eloquent über großartige integrierte Tools wie Soft Deletes, Events, Scopes usw.

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

[🔝🔝Zurück zum Inhalt](#contents)

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

[🔝🔝Zurück zum Inhalt](#contents)

### **Führen Sie keine Abfragen in Blade-Vorlagen aus und verwenden Sie das eifrige Laden (N + 1-Problem).**

Schlecht (for 100 users, 101 DB queries will be executed):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Gut (for 100 users, 2 DB queries will be executed):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝🔝Zurück zum Inhalt](#contents)

### **Kommentieren Sie Ihren Code, bevorzugen Sie jedoch beschreibende Methoden- und Variablennamen gegenüber Kommentaren**

Schlecht:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

Better:

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

Gut:

```php
if ($this->hasJoins())
```

[🔝🔝Zurück zum Inhalt](#contents)

### **Setzen Sie JS und CSS nicht in Blade-Vorlagen und setzen Sie kein HTML in PHP-Klassen**

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

In einer Javascript-Datei:

```javascript
let article = $('#article').val();
```

Am besten verwenden Sie ein spezielles PHP-zu-JS-Paket, um die Daten zu übertragen.

[🔝🔝Zurück zum Inhalt](#contents)

### **Verwenden Sie Konfigurations- und Sprachdateien, Konstanten anstelle von Text im Code**

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

[🔝Zurück zum Inhalt](#contents)

### **Verwenden Sie Standard-Laravel-Tools, die von der Community akzeptiert werden**

Verwenden Sie vorzugsweise integrierte Laravel-Funktionen und Community-Pakete, anstatt Pakete und Tools von Drittanbietern zu verwenden. Jeder Entwickler, der in Zukunft mit Ihrer App arbeitet, muss neue Tools erlernen. Außerdem sind die Chancen, Hilfe von der Laravel-Community zu erhalten, erheblich geringer, wenn Sie ein Paket oder Tool eines Drittanbieters verwenden. Lassen Sie Ihren Kunden nicht dafür bezahlen.

Aufgabe | Standardwerkzeuge | Tools von Drittanbietern
------------ | ------------- | -------------
Genehmigung | Richtlinien | Entrust, Sentinel und andere Pakete
Assets zusammenstellen Laravel Mix | Grunzen, Schlucken, 3rd-Party-Pakete
Entwicklungsumgebung | Gehöft | Docker
Bereitstellung | Laravel Forge | Deployer und andere Lösungen
Einzelprüfung | PHPUnit, Spott | Phpspec
Browsertests | Laravel Dämmerung | Codezeption
DB | Eloquent | SQL, Lehre
Vorlagen | Klinge | Zweig
Mit Daten arbeiten | Laravel Sammlungen | Arrays
Formularvalidierung | Klassen anfordern | Pakete von Drittanbietern, Validierung im Controller
Authentifizierung | Eingebaut | Pakete von Drittanbietern, Ihre eigene Lösung
API-Authentifizierung | Laravel Passport | JWT- und OAuth-Pakete von Drittanbietern
API erstellen | Eingebaut | Dingo API und ähnliche Pakete
Mit DB-Struktur arbeiten | Migrationen | Direkt mit der DB-Struktur arbeiten
Lokalisierung | Eingebaut | Pakete von Drittanbietern
Echtzeit-Benutzeroberflächen | Laravel Echo, Drücker | Pakete von Drittanbietern und direktes Arbeiten mit WebSockets
Testdaten generieren | Sämaschinenklassen, Modellfabriken, Faker | Testdaten manuell erstellen
Aufgabenplanung | Laravel Task Scheduler | Skripte und Pakete von Drittanbietern
DB | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝Zurück zum Inhalt](#contents)

### **Befolgen Sie die Namenskonventionen von Laravel**

 Folgen [PSR standards](http://www.php-fig.org/psr/psr-2/).
 
 Befolgen Sie außerdem die von der Laravel-Community akzeptierten Namenskonventionen:

Was | Wie | Gut | Schlecht
------------ | ------------- | ------------- | -------------
Controller | Singular | ArticleController | ~~ArticlesController~~
Route | plural | Artikel / 1 |~~Artikel/1~~
Benannte Route | snake_case mit Punktnotation | users.show_active |~~users.show-active, show-active-users~~
Modell | Singular | Benutzer |~~Benutzer~~
hasOne oder Zugehörigkeit zu einer Beziehung Singular | articleComment |~~articleComments, article_comment~~
Alle anderen Beziehungen plural | articleComments | ~~articleComment, article_comments~~
Tisch | plural | article_comments |~~article_comment, articleComments~~
Schwenktisch | Singuläre Modellnamen in alphabetischer Reihenfolge article_user |~~user_article, articles_users~~
Tabellenspalte | snake_case ohne Modellname | meta_title |~~MetTitle? article_title_title~~
Modelleigenschaft | snake_case | $ model-> created_at | ~~$ model-> createdAt~~
Fremdschlüssel | singulärer Modellname mit Suffix _id | article_id | ~~ArticleId, id_article, articles_id~~
Primärschlüssel | - | id | ~~custom_id~~
Migration | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
Methode | camelCase | getAll | ~~Nimm alle~~
Methode im Ressourcencontroller | [table](https://laravel.com/docs/master/controllers#resource-controllers) | Geschäft | ~~saveArticle~~
Methode in der Testklasse camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
Variable | camelCase | $ articlesWithAuthor | ~~$ articles_with_author~~
Sammlung | beschreibend, Plural | $ activeUsers = User :: active () -> get () | ~~$ active, $ data~~
Objekt | beschreibend, einzigartig $ activeUser = User :: active ()->first() | ~~$users, $obj~~
Konfigurations- und Sprachdateien index | snake_case | articles_enabled | ~~ArticlesEnabled; Artikel-fähig~~
Ansicht | Döner-Etui | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
Config | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Vertrag (Schnittstelle) | Adjektiv oder Substantiv Authentifizierbar | ~~AuthenticationInterface, IAuthentication~~
Merkmal | Adjektiv | Meldepflichtig | ~~NotificationTrait~~

[🔝🔝Zurück zum Inhalt](#contents)

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

Gemeinsame Syntax | Kürzere und lesbarere Syntax
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

[🔝🔝Zurück zum Inhalt](#contents)

### **Verwenden Sie IoC-Container oder -Fassaden anstelle der neuen Klasse**

Die neue Klassensyntax sorgt für eine enge Kopplung zwischen den Klassen und erschwert das Testen. Verwenden Sie stattdessen einen IoC-Container oder Fassaden.

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

[🔝🔝Zurück zum Inhalt](#contents)

### **Rufen Sie keine Daten direkt aus der ENV-Datei ab**

Übergeben Sie die Daten stattdessen an Konfigurationsdateien und verwenden Sie dann die Hilfefunktion `config ()`, um die Daten in einer Anwendung zu verwenden.

Schlecht:

```php
$apiKey = env('API_KEY');
```

Gut:

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[🔝Zurück zum Inhalt](#contents)

### **Speichern Sie Daten im Standardformat. Verwenden Sie Accessoren und Mutatoren, um das Datumsformat zu ändern**

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

[🔝Zurück zum Inhalt](#contents)

### **Andere gute Praktiken**

Fügen Sie niemals Logik in Routendateien ein.

Minimieren Sie die Verwendung von Vanille-PHP in Blade-Vorlagen.

[🔝Zurück zum Inhalt](#contents)
