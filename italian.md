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

Non è un adattamento Laravel di principi, schemi, ecc. SOLID. Qui troverai le migliori pratiche che di solito vengono ignorate nei progetti Laravel nella vita reale.

## Contenuto

[Principio della sola responsabilità](#single-responsibility-principle)

[Modelli grassi, controller skinny](#fat-models-skinny-controllers)

[Validazione](#validation)

[La logica aziendale dovrebbe essere nella classe di servizio](#business-logic-should-be-in-service-class)

[Non ripeterti (SECCO)](#dont-repeat-yourself-dry)

[Preferisco usare Eloquent rispetto a Query Builder e query SQL non elaborate. Preferisce raccolte su array](#prefer-to-use-eloquent-over-using-query-builder-and-raw-sql-queries-prefer-collections-over-arrays)

[Assegnazione di massa](#mass-assignment)

[Non eseguire query nei modelli Blade e utilizzare il caricamento desideroso (problema N + 1)](#do-not-execute-queries-in-blade-templates-and-use-eager-loading-n--1-problem)

[Commenta il tuo codice, ma preferisci il metodo descrittivo e i nomi delle variabili rispetto ai commenti](#comment-your-code-but-prefer-descriptive-method-and-variable-names-over-comments)

[Non inserire JS e CSS nei modelli Blade e non inserire HTML nelle classi PHP](#do-not-put-js-and-css-in-blade-templates-and-do-not-put-any-html-in-php-classes)

[Usa file di configurazione e lingua, costanti anziché testo nel codice](#use-config-and-language-files-constants-instead-of-text-in-the-code)

[Utilizzare gli strumenti standard Laravel accettati dalla community](#use-standard-laravel-tools-accepted-by-community)

[Segui le convenzioni di denominazione di Laravel](#follow-laravel-naming-conventions)

[Utilizzare la sintassi più breve e più leggibile ove possibile](#use-shorter-and-more-readable-syntax-where-possible)

[Utilizzare il contenitore o le facciate IoC anziché la nuova classe](#use-ioc-container-or-facades-instead-of-new-class)

[Non ottiene direttamente i dati dal file `.env`](#do-not-get-data-from-the-env-file-directly)

[Memorizza le date nel formato standard. Utilizzare accessori e mutatori per modificare il formato della data](#store-dates-in-the-standard-format-use-accessors-and-mutators-to-modify-date-format)

[Altre buone pratiche](#other-good-practices)

### **Principio della sola responsabilità**

Una classe e un metodo dovrebbero avere una sola responsabilità.

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

Buono:

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

[🔝Torna ai contenuti](#contents)

### **Fat models, skinny controllers**

Put all DB related logic into Eloquent models or into Repository classes if you're using Query Builder or raw SQL queries.

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

Buono:

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

[Torna ai contenuti](#contents)

### **Validazione**

Sposta la convalida dai controller alle classi di richiesta.

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

Buono:

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

[Torna ai contenuti](#contents)

### **La logica aziendale dovrebbe essere nella classe di servizio**

Un controller deve avere una sola responsabilità, quindi sposta la logica aziendale dai controller alle classi di servizio.

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

Buono:

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

[Torna ai contenuti](#contents)

### **Non ripeterti (SECCO)**

Riutilizzare il codice quando è possibile. SRP ti aiuta a evitare la duplicazione. Inoltre, riutilizza i modelli di blade, usa gli ambiti eloquenti ecc.

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

Buono:

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

[Torna ai contenuti](#contents)

### **Preferisco usare Eloquent rispetto a Query Builder e query SQL non elaborate. Preferisce raccolte su array**

Eloquent ti consente di scrivere codice leggibile e gestibile. Inoltre, Eloquent ha ottimi strumenti integrati come eliminazioni soft, eventi, ambiti ecc.

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

Buono:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[Torna ai contenuti](#contents)

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

Buono:

```php
$category->article()->create($request->validated());
```

[Torna ai contenuti](#contents)

### **Non eseguire query nei modelli Blade e utilizzare il caricamento desideroso (problema N + 1)**

Male (fo 100 utenti, verranno eseguite 101 query DB):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Buono (per 100 utenti, verranno eseguite 2 query DB):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[Torna ai contenuti](#contents)

### **Commenta il tuo codice, ma preferisci il metodo descrittivo e i nomi delle variabili rispetto ai commenti**

Male:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

Meglio:

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

Buono:

```php
if ($this->hasJoins())
```

[Torna ai contenuti](#contents)

### **Non inserire JS e CSS nei modelli Blade e non inserire HTML nelle classi PHP**

Male:

```php
let article = `{{ json_encode($article) }}`;
```

Meglio:

```php
<input id="article" type="hidden" value='@json($article)'>

Or

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

In un file Javascript:

```javascript
let article = $('#article').val();
```

Il modo migliore è utilizzare il pacchetto PHP-JS specializzato per trasferire i dati.

[Torna ai contenuti](#contents)

### **Usa file di configurazione e lingua, costanti anziché testo nel codice**

Male:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

Buono:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[Torna ai contenuti](#contents)

### **Utilizzare gli strumenti standard Laravel accettati dalla community**

Preferisci utilizzare la funzionalità Laravel integrata e i pacchetti della community anziché utilizzare pacchetti e strumenti di terze parti. Qualsiasi sviluppatore che lavorerà con la tua app in futuro dovrà imparare nuovi strumenti. Inoltre, le possibilità di ottenere aiuto dalla comunità Laravel sono significativamente inferiori quando si utilizza un pacchetto o uno strumento di terze parti. Non far pagare il tuo cliente per quello.

Compito | Strumenti standard | Strumenti di terze parti
------------ | ------------- | -------------
Autorizzazione | Politiche | Affida, Sentinel e altri pacchetti
Compiling assets | Laravel Mix | Grunt, Gulp, 3rd party packages
Ambiente di sviluppo | Fattoria | docker
Distribuzione | Laravel Forge | Deployer e altre soluzioni
Test unitari | PHPUnit, Mockery | Phpspec
Test del browser | Laravel Dusk | Codeception
DB | Eloquente | SQL, Doctrine
Modelli | Lama | Ramoscello
Lavorare con i dati | Collezioni Laravel | Array
Convalida del modulo | Richiedi classi | Pacchetti di terze parti, convalida nel controller
Autenticazione | Incorporato | Pacchetti di terze parti, la tua soluzione
Autenticazione API | Passaporto Laravel | Pacchetti JWT e OAuth di terze parti
Creazione dell'API | Incorporato | API Dingo e pacchetti simili
Lavorare con la struttura DB | Migrazioni | Lavorare direttamente con la struttura DB
Localizzazione | Incorporato | Pacchetti di terze parti
Interfacce utente in tempo reale | Laravel Echo, Pusher | Pacchetti di terze parti e funzionamento diretto con WebSocket
Generazione di dati di test | Classi di seminatrice, Fabbriche modello, Faker | Creazione manuale dei dati di test
Pianificazione delle attività | Utilità di pianificazione Laravel | Script e pacchetti di terze parti
DB | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[Torna ai contenuti](#contents)

### **Segui le convenzioni di denominazione di Laravel**

 Seguire [Standard PSR](http://www.php-fig.org/psr/psr-2/).
 
 Inoltre, segui le convenzioni di denominazione accettate dalla comunità Laravel:

Cosa | Come | Buono | Male
------------ | ------------- | ------------- | -------------
Controller | singolare | Controllo articolo |~~ArticlesController~~
Rotta | plurale | articoli / 1 | ~~article/1~~
Percorso denominato | snake_case con notazione punto | users.show_active | ~~users.show-active, show-active-users~~
Modello | singolare | Utente | ~~Users~~
hasOne o appartiene alla relazione | singolare | articleComment |~~articleComments, article_comment~~
Tutte le altre relazioni | plurale | articleComments | ~~articleComment, article_comments~~
Tabella | plurale | commenti_articolo | ~~article_comment, articleComments~~
Tabella pivot | nomi di modelli singolari in ordine alfabetico | user_user | ~~user_article, articles_users~~
Colonna della tabella | snake_case senza nome modello | meta_title |~~Meta Title; articolo meta_title~~
Proprietà del modello | snake_case | $ model-> Created_at |~~$model->createdAt~~
Foreign key | singular model name with _id suffix | article_id | ~~ArticleId, id_article, articles_id~~
Chiave primaria | - | id |~~custom_id~~
Migrazione | - | 2017_01_01_000000_create_articles_table |~~2017_01_01_000000_articles~~
Metodo | camelCase | getAll | ~~get_all~~
Metodo nel controller delle risorse | [tavolo](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
Metodo nella classe di prova | camelCase | testGuestCannotSeeArticle |~~test_guest_cannot_see_article~~
Variabile | camelCase | $ articoliWithAuthor |~~$articles_with_author~~
Collezione | descrittivo, plurale | $ activeUsers = Utente :: active () -> get () | ~~$active, $data~~
Oggetto | descrittivo, singolare | $ activeUser = User :: active () -> first () | ~~$users, $obj~~
Indice file di configurazione e lingua | snake_case | articoli abilitati |~~ArticlesEnabled; articles-enabled~~
Visualizza | astuccio per kebab | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
Config | snake_case | google_calendar.php |~~googleCalendar.php, google-calendar.php~~
Contratto (interfaccia) | aggettivo o sostantivo | Autenticabile | ~~AuthenticationInterface, IAuthentication~~
Tratto | aggettivo | Notificabile | ~~NotificationTrait~~

[Torna ai contenuti](#contents)

### **Utilizzare la sintassi più breve e più leggibile ove possibile**

Male:

```php
$request->session()->get('cart');
$request->input('name');
```

Buono:

```php
session('cart');
$request->name;
```

Più esempi:

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

[Torna ai contenuti](#contents)

### **Utilizzare il contenitore o le facciate IoC anziché la nuova classe**

la nuova sintassi della classe crea un accoppiamento stretto tra le classi e complica i test. Utilizzare invece il contenitore o le facciate IoC.

Male:

```php
$user = new User;
$user->create($request->validated());
```

Buono:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[Torna ai contenuti](#contents)

### **Non ottiene direttamente i dati dal file `.env`**

Passa invece i dati ai file di configurazione e quindi usa la funzione di supporto `config ()` per usare i dati in un'applicazione.

Male:

```php
$apiKey = env('API_KEY');
```

Buono:

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[Torna ai contenuti](#contents)

### **Memorizza le date nel formato standard. Utilizzare accessori e mutatori per modificare il formato della data**

Male:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

Buono:

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

[Torna ai contenuti](#contents)

### **Altre buone pratiche**

Non inserire mai alcuna logica nei file di route.

Ridurre al minimo l'utilizzo di PHP vaniglia nei modelli Blade.

[Torna ai contenuti](#contents)
