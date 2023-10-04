![Practici recomandate Laravel](/images/logo-english.png?raw=true)

Te poate interesa si [exemplul real de aplicație Laravel](https://github.com/alexeymezenin/laravel-realworld-example-app)

Traduceri:

[Nederlands](https://github.com/Protoqol/Beste-Laravel-Praktijken) (by [Protoqol](https://github.com/Protoqol))

[한국어](https://github.com/xotrs/laravel-best-practices) (by [cherrypick](https://github.com/xotrs))

[日本語](japanese.md) (by [2bo](https://github.com/2bo))

[漢語](chinese.md) (by [xiaoyi](https://github.com/Shiloh520))

[ภาษาไทย](thai.md) (by [kongvut sangkla](https://github.com/kongvut))

[فارسی](persian.md) (by [amirhossein baghaie](https://github.com/ohmydevops))

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

[العربية](arabic.md) (by [ahmedsaoud31](https://github.com/ahmedsaoud31))

[اردو](urdu.md) (by [RizwanAshraf1](https://github.com/RizwanAshraf1))

[Română](romanian.md) (by [als698](https://github.com/als698))


[![Exemplu aplicație Laravel](/images/laravel-real-world-banner.png?raw=true)](https://github.com/alexeymezenin/laravel-realworld-example-app)

## Cuprins

[Principiul responsabilității unice](#single-responsibility-principle)

[Modele voluminoase, controllere scurte](#fat-models-skinny-controllers)

[Validare](#validation)

[Păstrează functiile logice in clase de serviciu](#business-logic-should-be-in-service-class)

[Evita duplicarea codului (DRY)](#dont-repeat-yourself-dry)

[Preferă utilizarea Eloquent în locul Query Builder și a interogărilor SQL brute. Preferă colecțiile în locul matricilor](#prefer-to-use-eloquent-over-using-query-builder-and-raw-sql-queries-prefer-collections-over-arrays)

[Atribuire in masă](#mass-assignment)

[Evită executarea interogărilor în template-urile Blade și utilizează încărcarea eager (problema N + 1)](#do-not-execute-queries-in-blade-templates-and-use-eager-loading-n--1-problem)

[Fragmentează datele pentru sarcini care necesită multe date](#chunk-data-for-data-heavy-tasks)

[Preferă denumiri descriptive pentru metode și variabile în loc de comentarii](#comment-your-code-but-prefer-descriptive-method-and-variable-names-over-comments)

[Nu include cod JS și CSS în șabloanele Blade și nu include HTML în clasele PHP](#do-not-put-js-and-css-in-blade-templates-and-do-not-put-any-html-in-php-classes)

[Utilizează fișierele de configurație și de limbă, folosește constante în loc de text în cod](#use-config-and-language-files-constants-instead-of-text-in-the-code)

[Utilizează instrumentele standard Laravel acceptate de comunitate](#use-standard-laravel-tools-accepted-by-community)

[Respectă convențiile de denumire Laravel](#follow-laravel-naming-conventions)

[Folosește sintaxa mai scurtă și mai ușor de citit, acolo unde este posibil](#use-shorter-and-more-readable-syntax-where-possible)

[Folosește Containerul IoC / Service în loc de new Class](#use-ioc-container-or-facades-instead-of-new-class)

[Evită să stochezi date direct în fișierele `.env`](#do-not-get-data-from-the-env-file-directly)

[Stochează datele în format standard. Folosește accesorii și mutatori pentru a modifica formatul datelor](#store-dates-in-the-standard-format-use-accessors-and-mutators-to-modify-date-format)

[Alte bune practici](#other-good-practices)

### **Principiul responsabilității unice**

O clasă și o metodă ar trebui să aibă doar o singură responsabilitate.

Greșit:

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

Corect:

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

[🔝 Înapoi la cuprins](#contents)

### **Modele voluminoase, controllere scurte**

Mută toată logica legată de baza de date în modelele Eloquent.

Greșit:

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

Corect:

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

[🔝 Înapoi la cuprins](#contents)

### **Validare**

Mută validarea din controllere în clase de tip Request.

Greșit:

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

Corect:

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

[🔝 Înapoi la cuprins](#contents)

### **Păstrează functiile logice in clase de serviciu**

Un controller trebuie să aibă doar o singură responsabilitate, așa că mută logica aplicației din controllere în clase de serviciu.

Greșit:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ...
}
```

Corect:

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

[🔝 Înapoi la cuprins](#contents)

### **Evita duplicarea codului (DRY)**

Reutilizează codul atunci când poți. SRP te ajută să eviți duplicarea. De asemenea, reutilizează template-urile Blade, utilizează Eloquent scopes, etc.

Greșit:

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

Corect:

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

[🔝 Înapoi la cuprins](#contents)

### **Preferă utilizarea Eloquent în locul Query Builder și a interogărilor SQL brute. Preferă colecțiile în locul matricilor**

Eloquent îți permite să scrii cod ușor de citit și întreținut. De asemenea, Eloquent dispune de unelte încorporate excelente, cum ar fi ștergerile soft, evenimentele, domeniile etc.

Greșit:

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

Corect:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 Înapoi la cuprins](#contents)

### **Atribuire în masă**

Greșit:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;

// Add category to article
$article->category_id = $category->id;
$article->save();
```

Corect:

```php
$category->article()->create($request->validated());
```

[🔝 Înapoi la cuprins](#contents)

### **Evită executarea interogărilor în template-urile Blade și utilizează încărcarea eager (problema N + 1)**

Greșit (pentru 100 de utilizatori, vor fi executate 101 interogări în baza de date):

```blade
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Corect (pentru 100 de utilizatori, vor fi executate doar 2 interogări în baza de date):

```php
$users = User::with('profile')->get();

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 Înapoi la cuprins](#contents)

### **Fragmentează datele pentru sarcini care necesită multe date**

Greșit:

```php
$users = $this->get();

foreach ($users as $user) {
    ...
}
```

Corect:

```php
$this->chunk(500, function ($users) {
    foreach ($users as $user) {
        ...
    }
});
```

[🔝 Înapoi la cuprins](#contents)

### **Preferă denumiri descriptive pentru metode și variabile în loc de comentarii**

Greșit:

```php
// Determină dacă există vreun join
if (count((array) $builder->getQuery()->joins) > 0)
```

Corect:

```php
if ($this->hasJoins())
```

[🔝 Înapoi la cuprins](#contents)

### **Nu include cod JS și CSS în șabloanele Blade și nu include HTML în clasele PHP**

Greșit:

```javascript
let article = `{{ json_encode($article) }}`;
```

Corect:

```php
<input id="article" type="hidden" value='@json($article)'>

Alternativ:

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

Într-un fișier Javascript:

```javascript
let article = $('#article').val();
```

Cea mai bună metodă este de a utiliza un pachet specializat PHP către JS pentru a transfera datele.

[🔝 Înapoi la cuprins](#contents)

### **Utilizează fișierele de configurație și de limbă, folosește constante în loc de text în cod**

Greșit:

```php
public function isNormal(): bool
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

Corect:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 Înapoi la cuprins](#contents)

### **Utilizează instrumentele standard Laravel acceptate de comunitate**

Preferă să utilizezi funcționalitățile integrate ale Laravel și pachetele comunității în locul folosirii pachetelor și instrumentelor terțe. Orice dezvoltator care va lucra cu aplicația ta în viitor va trebui să învețe noi instrumente. De asemenea, șansele de a primi ajutor din partea comunității Laravel sunt semnificativ mai mici atunci când folosești un pachet sau un instrument terț. Nu face clientul să plătească pentru asta.

Task | Instrumente standard | Instrumente terțe
------------ | ------------- | -------------
Autorizare | Politici | Entrust, Sentinel și alte pachete
Compilarea asset-urilor | Laravel Mix, Vite | Grunt, Gulp, pachete terțe
Mediu de dezvoltare | Laravel Sail, Homestead | Docker
Implementare | Laravel Forge | Deployer și alte soluții
Testare unitară | PHPUnit, Mockery | Phpspec, Pest
Testare în browser | Laravel Dusk | Codeception
DB | Eloquent | SQL, Doctrine
Șabloane | Blade | Twig
Lucrul cu date | Laravel collections | Matrice
Validarea formularului | Clasele Request | Pachete terțe, validarea în controlor
Autentificare | Încorporată | Pachete terțe, soluția proprie
Autentificare API | Laravel Passport, Laravel Sanctum | Pachete terțe JWT și OAuth
Crearea API-ului | Încorporată | Dingo API și pachete similare
Lucrul cu structura DB-ului | Migrații | Lucrul direct cu structura DB-ului
Localizare | Încorporată | Pachete terțe
Interfețe în timp real pentru utilizatori | Laravel Echo, Pusher | Pachete terțe și lucrul direct cu WebSockets
Generarea datelor de testare | Clase Seeder, Fabrici de modele, Faker | Crearea manuală a datelor de testare
Programarea sarcinilor | Planificator de sarcini Laravel | Scripturi și pachete terțe
DB | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝 Înapoi la cuprins](#contents)

### **Respectă convențiile de denumire Laravel**

Respectă standardele [PSR](https://www.php-fig.org/psr/psr-12/).

De asemenea, respectă convențiile de denumire acceptate de comunitatea Laravel:

Ce | Cum | Corect | Greșit
------------ | ------------- | ------------- | -------------
Controller | singular | ArticleController | ~~ArticlesController~~
Route | plural | articles/1 | ~~article/1~~
Nume rută | snake_case cu notație punctată | users.show_active | ~~users.show-active, show-active-users~~
Model | singular | User | ~~Users~~
Relație hasOne sau belongsTo | singular | articleComment | ~~articleComments, article_comment~~
Toate celelalte relații | plural | articleComments | ~~articleComment, article_comments~~
Tabel | plural | article_comments | ~~article_comment, articleComments~~
Tabel pivot | nume model singular în ordine alfabetică | article_user | ~~user_article, articles_users~~
Coloană tabel | snake_case fără numele modelului | meta_title | ~~MetaTitle; article_meta_title~~
Proprietate model | snake_case | $model->created_at | ~~$model->createdAt~~
Cheie străină | nume model singular cu sufixul _id | article_id | ~~ArticleId, id_article, articles_id~~
Cheie primară | - | id | ~~custom_id~~
Migrare | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
Metodă | camelCase | getAll | ~~get_all~~
Metodă în controller-ul de resurse | [tabel](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
Metodă în clasă de testare | camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
Variabilă | camelCase | $articlesWithAuthor | ~~$articles_with_author~~
Colecție | descripțivă, plural | $activeUsers = User::active()->get() | ~~$active, $data~~
Obiect | descripțiv, singular | $activeUser = User::active()->first() | ~~$users, $obj~~
Fișiere de configurație și limbă - index | snake_case | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
Vizualizare | kebab-case | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
Configurație | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Contract (interfață) | adjectiv sau substantiv | AuthenticationInterface | ~~Authenticatable, IAuthentication~~
Trait | adjectiv | Notifiable | ~~NotificationTrait~~
Trait [(PSR)](https://www.php-fig.org/bylaws/psr-naming-conventions/) | adjectiv | NotifiableTrait | ~~Notification~~
Enum | singular | UserType | ~~UserTypes~~, ~~UserTypeEnum~~
FormRequest | singular | UpdateUserRequest | ~~UpdateUserFormRequest~~, ~~UserFormRequest~~, ~~UserRequest~~
Seeder | singular | UserSeeder | ~~UsersSeeder~~


[🔝 Înapoi la cuprins](#contents)

### **Folosește sintaxa mai scurtă și mai ușor de citit, acolo unde este posibil**

Greșit:

```php
$request->session()->get('cart');
$request->input('name');
```

Corect:

```php
session('cart');
$request->name;
```

Mai multe exemple:

Sintaxă comună | Sintaxă mai scurtă și mai ușor de citit
------------ | -------------
`Session::get('cart')` | `session('cart')`
`$request->session()->get('cart')` | `session('cart')`
`Session::put('cart', $data)` | `session(['cart' => $data])`
`$request->input('name'), Request::get('name')` | `$request->name, request('name')`
`return Redirect::back()` | `return back()`
`is_null($object->relation) ? null : $object->relation->id` | `optional($object->relation)->id` (în PHP 8: `$object->relation?->id`)
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

[🔝 Înapoi la cuprins](#contents)

### **Folosește Containerul IoC / Service în loc de new Class**

Sintaxa new Class creează un cuplaj strâns între clase și complică testarea. Folosește Containerul IoC / Service pentru a rezolva dependințele.

Greșit:

```php
$user = new User;
$user->create($request->validated());
```

Corect:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

...

$this->user->create($request->validated());
```

Sau mai bine, utilizează injecția de dependențe:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

public function store(Request $request)
{
    $this->user->create($request->all());
}
```

[🔝 Înapoi la cuprins](#contents)

### **Evită să stochezi date direct în fișierele `.env`**

În loc să preiei datele direct din fișierul `.env`, este mai indicat să le treci în fișierele de configurare și să utilizezi funcția ajutătoare `config()` pentru a accesa aceste date în aplicație.

Greșit:

```php
$apiKey = env('API_KEY');
```

Corect:

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[🔝 Înapoi la cuprins](#contents)

### **Stochează datele în format standard. Folosește accesorii și mutatori pentru a modifica formatul datelor**

Un șir de caractere ca dată este mai puțin fiabil decât o instanță de obiect, cum ar fi o instanță Carbon. Se recomandă trecerea obiectelor Carbon între clase în locul șirurilor de caractere. Redarea ar trebui să se facă în stratul de prezentare (template-uri):

Greșit:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

Corect:

```php
// Model
protected $casts = [
    'ordered_at' => 'datetime',
];

// Blade view
{{ $object->ordered_at->toDateString() }}
{{ $object->ordered_at->format('m-d') }}
```

[🔝 Înapoi la cuprins](#contents)

### **Alte bune practici**

Evită utilizarea de modele și instrumente străine pentru Laravel și framework-urile similare (cum ar fi RoR, Django). Dacă îți place abordarea Symfony (sau Spring) pentru construirea aplicațiilor, este o idee bună să folosești aceste framework-uri în schimb.

Nu pune niciodată logica în fișierele de rute.

Minimizează utilizarea PHP vanilla în template-urile Blade.

Folosește o bază de date în memorie pentru teste.

Evită suprascrierea funcționalităților standard ale framework-ului pentru a evita probleme legate de actualizarea versiunii framework-ului și multe alte probleme.

Folosește sintaxa PHP modernă acolo unde este posibil, dar nu uita de lizibilitate.

Evită utilizarea View Composers și a altor instrumente similare, cu excepția cazului în care cunoști cu adevărat ce faci. În majoritatea cazurilor, există o modalitate mai bună de rezolvare a problemei.

[🔝 Înapoi la cuprins](#contents)
