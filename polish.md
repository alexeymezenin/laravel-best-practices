![Laravel - najlepsze praktyki](/images/logo-polish.png?raw=true)

Tłumaczenia:

[Nederlands](https://github.com/Protoqol/Beste-Laravel-Praktijken) (by [Protoqol](https://github.com/Protoqol))

[Indonesia](indonesia.md) (by [P0rguy](https://github.com/p0rguy), [Doni Ahmad](https://github.com/donyahmd))

[한국어](https://github.com/xotrs/laravel-best-practices) (by [cherrypick](https://github.com/xotrs))

[日本語](japanese.md) (by [2bo](https://github.com/2bo))

[简体中文](chinese.md) (by [xiaoyi](https://github.com/Shiloh520))

[繁體中文](traditional-chinese.md) (by [woeichern](https://github.com/woeichern))

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

To nie jest Laravel-owa adaptacja zasad SOLID, wzorców itp. Znajdziesz tu najlepsze praktyki, które są zwykle ignorowane w realnych projektach Laravela.

## Spis treści

[Zasada pojedynczej odpowiedzialności](#zasada-pojedynczej-odpowiedzialności)

[Grube modele, chude kontrolery](#grube-modele-chude-kontrolery)

[Walidacja](#walidacja)

[Logika biznesowa powinna znajdować się w klasie Service](#logika-biznesowa-powinna-znajdować-się-w-klasie-service)

[Nie powtarzaj się (DRY)](#nie-powtarzaj-się-dry)

[Preferuj używanie modeli Eloquent-a ponad klasy Query Builder lub surowe zapytania SQL. Staraj się używać kolecji zamist tablic](#preferuj-używanie-modeli-eloquent-a-ponad-klasy-query-builder-lub-surowe-zapytania-sql-staraj-się-używać-kolecji-zamist-tablic)

[Masowe przypisywanie](#masowe-przypisywanie)

[Nie wykonuj zapytań w szablonach Blade oraz używaj eager loading-u (problem N + 1)](#nie-wykonuj-zapytań-w-szablonach-blade-oraz-używaj-eager-loading-u-problem-n--1)

[Komentuj swój kod, ale preferuj opisowe nazwy metod i zmiennych zamiast komentarzy](#komentuj-swój-kod-ale-preferuj-opisowe-nazwy-metod-i-zmiennych-zamiast-komentarzy)

[Nie umieszczaj kodu JS i CSS w szablonach Blade oraz nie osadzaj żadnego kodu HTML wewnątrz klas PHP.](#nie-umieszczaj-kodu-js-i-css-w-szablonach-blade-oraz-nie-osadzaj-żadnego-kodu-html-wewnątrz-klas-php)

[Używaj plików konfiguracyjnych oraz językowych, stałych zamiast tekstu w kodzie](#używaj-plików-konfiguracyjnych-oraz-językowych-stałych-zamiast-tekstu-w-kodzie)

[Używaj standardowych narzędzi Laravel-a zaakceptowanych przez społeczność](#używaj-standardowych-narzędzi-laravel-a-zaakceptowanych-przez-społeczność)

[Postępuj zgodnie z konwencją nazewniczą Laravel-a](#postępuj-zgodnie-z-konwencją-nazewniczą-laravel-a)

[W miarę możliwości używaj krótszej i bardziej czytelnej składni](#w-miarę-możliwości-używaj-krótszej-i-bardziej-czytelnej-składni)

[Użyj kontenera IoC lub fasad zamiast nowych klas](#użyj-kontenera-ioc-lub-fasad-zamiast-nowych-klas)

[Nie pobieraj wartości z pliku `.env` bezpośrednio](#nie-pobieraj-wartości-z-pliku-env-bezpośrednio)

[Przechowuj daty w standardowym formacie. Używaj akcesorów i mutatorów do modyfikacji formatów.](#przechowuj-daty-w-standardowym-formacie-używaj-akcesorów-i-mutatorów-do-modyfikacji-formatów)

[Inne dobre praktyki](#inne-dobre-praktyki)

### **Zasada pojedynczej odpowiedzialności**

Klasa i metoda powinny mieć tylko jedną odpowiedzialność.

Źle:

```php
public function getFullNameAttribute()
{
    if (auth()->user() && auth()->user()->hasRole('client') && auth()->user()->isVerified()) {
        return 'Pan ' . $this->first_name . ' ' . $this->middle_name . ' ' . $this->last_name;
    } else {
        return $this->first_name[0] . '. ' . $this->last_name;
    }
}
```

Dobrze:

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
    return 'Pan ' . $this->first_name . ' ' . $this->middle_name . ' ' . $this->last_name;
}

public function getFullNameShort()
{
    return $this->first_name[0] . '. ' . $this->last_name;
}
```

[🔝 Wróć do spisu treści](#spis-treści)

### **Grube modele, chude kontrolery**

Umieszczaj całą logikę związaną z DB w modelach Eloquent-a lub w klasach Repozytorium.

Źle:

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

Dobrze:

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

[🔝 Wróć do spisu treści](#spis-treści)

### **Walidacja**

Przenieś walidację z kontrolerów do klas Request.

Źle:

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

Dobrze:

```php
public function store(PostRequest $request)
{    
    ...
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

[🔝 Wróć do spisu treści](#spis-treści)

### **Logika biznesowa powinna znajdować się w klasie Service**

Kontroler musi mieć tylko jedną odpowiedzialność, więc przenieś logikę biznesową z kontrolerów do klas Service.

Źle:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

Dobrze:

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

[🔝 Wróć do spisu treści](#spis-treści)

### **Nie powtarzaj się (DRY)**

Używaj kod ponownie, kiedy tylko możesz. SRP pomoże Ci uniknąć duplikatów. Ponadto, ponownie używaj szablonów Blade, używaj scope-ów Eloquent-a itp.

Źle:

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

Dobrze:

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

[🔝 Wróć do spisu treści](#spis-treści)

### **Preferuj używanie modeli Eloquent-a ponad klasy Query Builder lub surowe zapytania SQL. Staraj się używać kolecji zamist tablic**

Eloquent pozwala na pisanie czytelnego i łatwego w utrzymaniu kodu. Ponadto, Eloquent ma wbudowane świetne narzędzia, takie jak miękkie usuwanie, zdarzenia, scope-y itp.

Źle:

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

Dobrze:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 Wróć do spisu treści](#spis-treści)

### **Masowe przypisywanie**

Źle:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Dodaj kategorię do artykułu
$article->category_id = $category->id;
$article->save();
```

Dobrze:

```php
$category->article()->create($request->validated());
```

[🔝 Wróć do spisu treści](#spis-treści)

### **Nie wykonuj zapytań w szablonach Blade oraz używaj eager loading-u (problem N + 1)**

Źle (dla 100 użytkowników zostanie wykonanych 101 zapytań do bazy danych):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Dobrze (dla 100 użytkowników zostaną wykonane tylko 2 zapytania do bazy danych):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 Wróć do spisu treści](#spis-treści)

### **Komentuj swój kod, ale preferuj opisowe nazwy metod i zmiennych zamiast komentarzy**

Źle:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

Lepiej:

```php
// Ustal czy istnieją jakieś join-y
if (count((array) $builder->getQuery()->joins) > 0)
```

Dobrze:

```php
if ($this->hasJoins())
```

[🔝 Wróć do spisu treści](#spis-treści)

### **Nie umieszczaj kodu JS i CSS w szablonach Blade oraz nie osadzaj żadnego kodu HTML wewnątrz klas PHP.**

Źle:

```php
let article = `{{ json_encode($article) }}`;
```

Lepiej:

```php
<input id="article" type="hidden" value='@json($article)'>

Lub

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

W pliku Javascript:

```javascript
let article = $('#article').val();
```

Najlepszym sposobem jest użycie specialnego obiektu do transferu danych pomiędzy PHP i JS.

[🔝 Wróć do spisu treści](#spis-treści)

### **Używaj plików konfiguracyjnych oraz językowych, stałych zamiast tekstu w kodzie**

Źle:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Twój artykuł został dodany!');
```

Dobrze:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 Wróć do spisu treści](#spis-treści)

### **Używaj standardowych narzędzi Laravel-a zaakceptowanych przez społeczność**

Preferuj używanie wbudowanych funkcjonalności Laravel-a i paczek społecznościowych zamiast używania paczek i narzędzi innych podmiotów.
Każdy programista, który będzie pracował z twoją aplikacją w przyszłości, będzie musiał nauczyć się nowych narzędzi.
Ponadto, szanse na uzyskanie pomocy od społeczności Laravel-a są znacznie mniejsze, gdy używasz paczek lub narzędzi innych podmiotów.
Nie każ swojemu klientowi za to płacić.

Zadanie | Standardowe narzędzia | Narzędzia innych podmiotów
------------ | ------------- | -------------
Autoryzacja | Laravel Policies | Entrust, Sentinel i inne paczki
Kompilowanie zasobów | Laravel Mix | Grunt, Gulp oraz inne
Środowisko pracy | Laravel Sail, Homestead | Docker
Wdrażanie | Laravel Forge | Deployer i inne rozwiązania
Testy jednostkowe | PHPUnit, Mockery | Phpspec
Testy przeglądarkowe | Laravel Dusk | Codeception
Baza danych | Eloquent | SQL, Doctrine
Szablony widoków | Blade | Twig
Praca z danymi | kolekcje Laravel-a | natywne tablice
Walidacja formularzy | klasy Request | inne paczki, walidacja w kontrolerze
Uwierzytelnianie | wbudowane | inne paczki, Twoje własne rozwiązanie
Uwierzytelnianie API | Laravel Passport, Laravel Sanctum | inne paczki JWT oraz paczki OAuth
Tworzenie API | wbudowane | Dingo API oraz podobne paczki
Praca ze strukturą bazy danych | wbudowane migracje | bezpośrednia praca ze strukturą
Lokalizacja | wbudowane | inne paczki
Interfejsy użytkownika w czasie rzeczywistym | Laravel Echo, Pusher | inne paczki oraz bezpośrednia praca z WebSockets
Generowanie danych testowych | klasy Seeder-ów, fabryki modeli, Faker | manualne tworzenie danych testowych
Planowanie zadań | Laravel Task Scheduler | skrypty oraz inne paczki
Baza danych | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝 Wróć do spisu treści](#spis-treści)

### **Postępuj zgodnie z konwencją nazewniczą Laravel-a**

 Stosuj [standardy PSR](http://www.php-fig.org/psr/psr-2/).
 
 Przestrzegaj również konwencji nazewniczych przyjętych przez społeczność Laravel-a

Zagadnienie | Konwencja | Dobrze | Źle
------------ | ------------- | ------------- | -------------
Kontrolery | liczba pojedyncza | ArticleController | ~~ArticlesController~~
Ścieżka URL | liczba mnoga | articles/1 | ~~article/1~~
Nazwana ścieżka URL | snake_case wraz z notacją kropkową | users.show_active | ~~users.show-active, show-active-users~~
Model | liczba pojedyncza | User | ~~Users~~
relacje hasOne lub belongsTo | liczba pojedyncza | articleComment | ~~articleComments, article_comment~~
Wszystkie pozostałe relacje | liczba mnoga | articleComments | ~~articleComment, article_comments~~
Table | liczba mnoga | article_comments | ~~article_comment, articleComments~~
Tabela przestawna (Pivot) | nazwy modeli w liczbie pojedynczej w kolejności alfabetycznej | article_user | ~~user_article, articles_users~~
Kolumna w tabeli | snake_case bez nazwy modelu | meta_title | ~~MetaTitle; article_meta_title~~
Właściwość modelu | snake_case | $model->created_at | ~~$model->createdAt~~
Klucz obcy | nazwa modelu w liczbie pojedynczej z przyrostkiem _id | article_id | ~~ArticleId, id_article, articles_id~~
Klucz podstawowy | - | id | ~~custom_id~~
Migracja | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
Metoda | camelCase | getAll | ~~get_all~~
Metoda w kontrolerze zasobu | [zobacz tabelę](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
Metoda w klasie testowania | camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
Zmienna | camelCase | $articlesWithAuthor | ~~$articles_with_author~~
Kolekcja | opisowo, liczba mnoga | $activeUsers = User::active()->get() | ~~$active, $data~~
Obiekt | opisowo, liczba pojedyncza | $activeUser = User::active()->first() | ~~$users, $obj~~
Indeks plików konfiguracyjnych i językowych | snake_case | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
Widok | kebab-case | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
Pliki konfiguracyjne | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Kontrakt (interfejs) | przymiotnik lub rzeczownik | AuthenticationInterface | ~~Authenticatable, IAuthentication~~
Cecha (trait) | przymiotnik | Notifiable | ~~NotificationTrait~~

[🔝 Wróć do spisu treści](#spis-treści)

### **W miarę możliwości używaj krótszej i bardziej czytelnej składni**

Źle:

```php
$request->session()->get('cart');
$request->input('name');
```

Dobrze:

```php
session('cart');
$request->name;
```

Więcej przykładów:

Powszechna składnia | Krótsza i bardziej czytelna składnia
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

[🔝 Wróć do spisu treści](#spis-treści)

### **Użyj kontenera IoC lub fasad zamiast nowych klas**

Składnia tworzenia nowych klas tworzy ścisłe sprzężenie pomiędzy nimi i komplikuje testowanie.
Zamiast tego używaj kontenera IoC lub fasad.

Źle:

```php
$user = new User;
$user->create($request->validated());
```

Dobrze:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 Wróć do spisu treści](#spis-treści)

### **Nie pobieraj wartości z pliku `.env` bezpośrednio**

Zamiast tego wstaw je do plików konfiguracyjnych, a następnie użyj funkcji pomocniczej `config()` aby użyć tych danych w aplikacji.

Źle:

```php
$apiKey = env('API_KEY');
```

Dobrze:

```php
// config/api.php
'key' => env('API_KEY'),

// Użyj danych
$apiKey = config('api.key');
```

[🔝 Wróć do spisu treści](#spis-treści)

### **Przechowuj daty w standardowym formacie. Używaj akcesorów i mutatorów do modyfikacji formatów.**

Źle:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

Dobrze:

```php
// Model
protected $dates = ['ordered_at', 'created_at', 'updated_at'];
public function getSomeDateAttribute($date)
{
    return $date->format('m-d');
}

// Widok
{{ $object->ordered_at->toDateString() }}
{{ $object->ordered_at->some_date }}
```

[🔝 Wróć do spisu treści](#spis-treści)

### **Inne dobre praktyki**

NNigdy nie umieszczaj żadnej logiki w plikach ścieżek URL (routes/*.php).

Zminimalizuj użycie natywnego kodu PHP w szablonach Blade.

[🔝 Wróć do spisu treści](#spis-treści)
