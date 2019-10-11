![Laravel best practices](/images/logo-english.png?raw=true)

Traduzioni:

[Nederlands](https://github.com/Protoqol/Beste-Laravel-Praktijken) (by [Protoqol](https://github.com/Protoqol))

[한국어](https://github.com/xotrs/laravel-best-practices) (by [cherrypick](https://github.com/xotrs))

[日本語](japanese.md) (by [2bo](https://github.com/2bo))

[漢語](chinese.md) (by [xiaoyi](https://github.com/Shiloh520))

[ภาษาไทย](thai.md) (by [kongvut sangkla](https://github.com/kongvut))

[فارسی](persian.md) (by [amirhossein baghaie](https://github.com/amirbagh75))

[Português](https://github.com/jonaselan/laravel-best-practices) (by [jonaselan](https://github.com/jonaselan))

[Русский](russian.md)

[Tiếng Việt](https://chungnguyen.xyz/posts/code-laravel-lam-sao-cho-chuan) (by [Chung Nguyễn](https://github.com/nguyentranchung))

[Español](spanish.md) (by [César Escudero](https://github.com/cedaesca))

[Français](french.md) (by [Mikayil S.](https://github.com/mikayilsrt))

[Polski](https://github.com/maciejjeziorski/laravel-best-practices-pl) (by [Maciej Jeziorski](https://github.com/maciejjeziorski))

[Türkçe](turkish.md) (by [Burak](https://github.com/ikidnapmyself))

[Italiano](italian.md) (by [Claudio La Barbera](https://github.com/thebatclaudio))

Questo non è un adattamento su Laravel dei principi SOLID, pattern ecc. Qui troverai le best practices che sono solitamente ignorate nei progetti Laravel.

## Indice

[Principio di singola responsabilità](#principio-di-singola-responsabilità)

[Model grassi, controller magri](#model-grassi-controller-magri)

[Validazione](#validazione)

[Le logiche di business dovrebbero stare in una classe service](#le-logiche-di-business-dovrebbero-stare-in-una-classe-service)

[Don't repeat yourself (DRY)](#dont-repeat-yourself-dry)

[Prediligi Eloquent al Query Builder e alle query SQL grezze. Prediligi le collection agli array](#prediligi-eloquent-al-query-builder-e-alle-query-sql-grezze-prediligi-le-collection-agli-array)

[Assegnazione di massa](#assegnazione-di-massa)

[Non eseguire query nei template Blade e utilizza l'eager loading (N + 1 problem)](#non-eseguire-query-nei-template-blade-e-utilizza-l-eager-loading-n-1-problem)

[Commenta il tuo codice, ma prediligi nomi descrittivi per metodi e variabili ai commenti](#commenta-il-tuo-codice-ma-prediligi-nomi-descrittivi-per-metodi-e-variabili-ai-commenti)

[Non inserire JS e CSS nei template Blade e non inserire HTML nelle classi PHP](#non-inserire-js-e-css-nei-template-blade-e-non-inserire-html-nelle-classi-php)

[Usa i file config e delle lingue, costanti al posto del testo nel codice](#usa-i-file-config-e-delle-lingue-costanti-al-posto-del-testo-nel-codice)

[Usa gli strumenti standard per Laravel accettati dalla comunità](#usa-gli-strumenti-standard-per-laravel-accettati-dalla-comunità)

[Segui le convenzioni di naming di Laravel](#segui-le-convenzioni-di-naming-di-laravel)

[Usa una sintassi breve e più leggibile dove possibile](#usa-una-sintassi-breve-e-piu-leggibile-dove-possibile)

[Usa gli IoC container o i facades al posto di nuove classi](#usa-gli-ioc-container-o-i-facades-al-posto-di-nuove-classi)

[Non prendere dati dal file `.env` direttamente](#non-prendere-dati-dal-file-env-direttamente)

[Salva le date nel formato standard. Usa accessors e mutators per modificare i formati delle date](#salva-le-date-nel-formato-standard-usa-accessors-e-mutators-per-modificare-i-formati-delle-date)

[Altre buone abitudini](#alte-buone-abitudini)

### **Principio di singola responsabilità**

Una classe e un metodo devono avere una sola responsabilità.

Male:

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

Bene:

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

[🔝 Torna all'indice](#indice)

### **Model grassi, controller magri**

Metti tutta la logica relativa al DB nei model Eloquent o nelle classi Repository se stai usando i Query Builder o le query SQL.

Male:

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

Bene:

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

[🔝 Torna all'indice](#indice)

### **Validazione**

Sposta la validazione dai controller alle classi Request.

Male:

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

Bene:

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

[🔝 Torna all'indice](#indice)

### **Le logiche di business dovrebbero stare in una classe service**

Un controller dovrebbe avere una sola responsabilità, allora sposta le logiche di business dai controller alle classe service.

Male:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

Bene:

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

[🔝 Torna all'indice](#indice)

### **Don't repeat yourself (DRY)**

Riutilizza il codice quando puoi. Il principio di singola responsabilità ti aiuta ad evitare duplicazioni. Inoltre, riutilizza i template Blade, utilizza gli scope di Eloquent ecc.

Male:

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

Bene:

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

[🔝 Torna all'indice](#indice)

### **Prediligi Eloquent al Query Builder e alle query SQL grezze. Prediligi le collection agli array**

Eloquent ti permette di scrivere codice leggibile e manutenibile. Inoltre, Eloquent ha degli stumenti built-in come cancellazione logica, eventi, scopes ecc.

Male:

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

Bene:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 Torna all'indice](#indice)

### **Assegnazione di massa**

Male:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

Bene:

```php
$category->article()->create($request->validated());
```

[🔝 Torna all'indice](#indice)

### **Non eseguire query nei template Blade e utilizza l'eager loading (N + 1 problem)**

Male (per 100 utenti, verranno eseguite 101 query):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Bene (per 100 utenti, verranno eseguite 2 query):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 Torna all'indice](#indice)

### **Commenta il tuo codice, ma prediligi nomi descrittivi per metodi e variabili ai commenti**

Male:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

Meglio:

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

Bene:

```php
if ($this->hasJoins())
```

[🔝 Torna all'indice](#indice)

### **Non inserire JS e CSS nei template Blade e non inserire HTML nelle classi PHP**

Male:

```php
let article = `{{ json_encode($article) }}`;
```

Meglio:

```php
<input id="article" type="hidden" value='@json($article)'>

Oppure

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

In un file javascript:

```javascript
let article = $('#article').val();
```

Il modo migliore è utilizzare dei paccheddi dedicati PHP to JS package per trasferire dati.

[🔝 Torna all'indice](#indice)

### **Usa i file config e delle lingue, costanti al posto del testo nel codice**

Male:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

Bene:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 Torna all'indice](#indice)

### **Usa gli strumenti standard per Laravel accettati dalla comunità**

Cerca di utilizzare le funzionalità built-in di Laravel e i pacchetti della comunità invece di utilizzare pacchetti e strumenti di terze parti. Ogni sviluppatore che lavorerà con la tua applicazione in futuro avrà bisogno di imparare nuovi strumenti. Inoltre, le possibilità di ricevere aiuto dalla comunità di laravel è significativamente minore quando utilizzi strumenti o pacchetti di terze parti. Non far pagare il tuo cliente per questo.

Attività | Strumento standard | Strumento di terze parti
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

[🔝 Torna all'indice](#indice)

### **Segui le convenzioni di naming di Laravel**

 Segui gli [standard PSR](http://www.php-fig.org/psr/psr-2/).
 
 Inoltre, segui le convenzioni di naming accettate dalla comunità di Laravel:

Cosa | Come | Bene | Male
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
Contract (interface) | adjective or noun | Authenticatable | ~~AuthenticationInterface, IAuthentication~~
Trait | adjective | Notifiable | ~~NotificationTrait~~

[🔝 Torna all'indice](#indice)

### **Usa una sintassi breve e più leggibile dove possibile**

Male:

```php
$request->session()->get('cart');
$request->input('name');
```

Bene:

```php
session('cart');
$request->name;
```

Più esempi:

Sintassi comune | Sintassi più breve e leggibile
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

[🔝 Torna all'indice](#indice)

### **Usa gli IoC container o i facades al posto di nuove classi**

La sintassi `new Class` crea tight coupling tra le classi and complica le attività di test. Piuttosto usa gli IoC container o i facades.

Male:

```php
$user = new User;
$user->create($request->validated());
```

Bene:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 Torna all'indice](#indice)

### **Non prendere dati dal file `.env` direttamente**

Passa i dati dai file di config piuttosto e utilizza la funzione helper `config()` per utilizzare i dati nell'applicazione.

Male:

```php
$apiKey = env('API_KEY');
```

Bene:

```php
// config/api.php
'key' => env('API_KEY'),

// Utilizza i dati
$apiKey = config('api.key');
```

[🔝 Torna all'indice](#indice)

### **Salva le date nel formato standard. Usa accessors e mutators per modificare i formati delle date**

Male:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

Bene:

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

[🔝 Torna all'indice](#indice)

### **Altre buone abitudini**

Non inserire mai nessuna logica nei file di route.

Minimizza l'utilizzo di PHP puro nei template Blade.

[🔝 Torna all'indice](#indice)
