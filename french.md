![Laravel best practices](/images/logo-french.png?raw=true)

Traductions:

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

Ce n'est pas une adaptation Laravel des principes SOLID, des modèles, etc. Vous trouverez ici les meilleures pratiques qui sont généralement ignorées dans les projets réels de Laravel.

## Contenu

[Principe de responsabilité unique](#principe-de-responsabilité-unique)

[Modèles Fat, contrôleurs maigres](#modèles-Fat-contrôleurs-maigres)

[Validation](#validation)

[La logique métier doit être en classe de service](#la-logique-métier-doit-être-en-classe-de-service)

[Ne te répète pas (DRY)](#ne-te-répète-pas-dry)

[Préférez utiliser Eloquent à l’utilisation de Query Builder et de requêtes SQL brutes. Préférez les collections aux tableaux](#préférez-utiliser-eloquent-à-l-utilisation-de-Query-Builder-et-de-requêtes-SQL-brutes-Préférez-les-collections-aux-tableaux)

[Affectation en masse](#affectation-de-masse)

[N'exécutez pas de requêtes dans les modèles de blade et utilisez un chargement rapide (N + 1 problème)](#n-exécutez-pas-de-requêtes-dans-les-modèles-de-blade-et-utilisez-un-chargement-rapide-n--1-problem)

[Commentez votre code, mais préférez la méthode descriptive et les noms de variables aux commentaires](#commentez-votre-code-mais-préférez-la-méthode-descriptive-et-les-noms-de-variables-aux-commentaires)

[Ne mettez pas JS et CSS dans les templates Blade et ne mettez pas de HTML dans les classes PHP](#ne-mettez-pas-JS-et-CSS-dans-les-templates-Blade-et-ne-mettez-pas-de-HTML-dans-les-classes-PHP)

[Utilisez des fichiers de configuration et de langue, des constantes au lieu du texte dans le code](#utilisez-des-fichiers-de-configuration-et-de-langue-des-constantes-au-lieu-du-texte-dans-le-code)

[Utiliser les outils standard de Laravel acceptés par la communauté](#utiliser-les-outils-standard-de-Laravel-acceptés-par-la-communauté)

[Suivre les conventions de nommage de Laravel](#suivre-les-conventions-de-nommage-de-Laravel)

[Utilisez une syntaxe plus courte et plus lisible dans la mesure du possible](#utilisez-une-syntaxe-plus-courte-et-plus-lisible-dans-la-mesure-du-possible)

[Utilisez un conteneur IoC ou des façades au lieu de la nouvelle classe](#utilisez-un-conteneur-IoC-ou-des-façades-au-lieu-de-la-nouvelle-classe)

[Ne pas obtenir les données du fichier `.env` directement](#ne-pas-obtenir-les-données-du-fichier-env-directement)

[Stocker les dates au format standard. Utiliser des accesseurs et des mutateurs pour modifier le format de date](#stocker-les-dates-au-format-standard-Utiliser-des-accesseurs-et-des-mutateurs-pour-modifier-le-format-de-date)

[Autres bonnes pratiques](#autres-bonnes-pratiques)

### **Principe de responsabilité unique**

Une classe et une méthode ne devraient avoir qu'une seule responsabilité.

Mal:

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

Bien:

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

[🔝 Retour au contenu](#contents)

### **Gros modèles, contrôleurs maigres**

Placez toute la logique liée à la base de données dans les modèles Eloquent ou dans les classes du Repository si vous utilisez le générateur de requêtes ou des requêtes SQL brutes.

Mal:

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

Bien:

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

[🔝 Retour au contenu](#contents)

### **Validation**

Déplacez la validation des contrôleurs vers les classes Request.

Mal:

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

Bien:

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

[🔝 Retour au contenu](#contents)

### **La logique métier doit être dans une classe de service**

Un contrôleur ne doit avoir qu'une seule responsabilité. Par conséquent, déplacez la logique métier des contrôleurs vers les classes de service.

Mal:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

Bien:

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

[🔝 Retour au contenu](#contents)

### **Ne te répète pas (DRY)**

Réutilisez le code quand vous le pouvez. SRP vous aide à éviter les doubles emplois. Réutilisez également les modèles Blade, utilisez les Eloquent scopes, etc.

Mal:

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

Bien:

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

[🔝 Retour au contenu](#contents)

### **Préférez utiliser Eloquent à l’utilisation de Query Builder et de requêtes SQL brutes. Préférez les collections aux tableaux**

Eloquent vous permet d’écrire du code lisible et maintenable. Eloquent dispose également d'excellents outils intégrés tels que les suppressions, les événements, les scopes, etc.

Mal:

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

Bien:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 Retour au contenu](#contents)

### **Affectation en masse**

Mal:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

Bien:

```php
$category->article()->create($request->validated());
```

[🔝 Retour au contenu](#contents)

### **N'exécutez pas de requêtes dans les modèles Blade et utilisez un chargement rapide (eager loading) (problème N + 1)**

Mal (Pour 100 utilisateurs, 101 requêtes DB seront exécutées):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Bien (pour 100 utilisateurs, 2 requêtes de base de données seront exécutées):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 Retour au contenu](#contents)

### **Commentez votre code, mais préférez une méthode descriptive et des noms de variables aux commentaires**

Mal:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

Meilleure:

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

Bien:

```php
if ($this->hasJoins())
```

[🔝 Retour au contenu](#contents)

### **Ne mettez pas JS et CSS dans les templates Blade et ne mettez pas de HTML dans les classes PHP**

Mal:

```php
let article = `{{ json_encode($article) }}`;
```

Meilleure:

```php
<input id="article" type="hidden" value='@json($article)'>

Or

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

Dans un fichier Javascript:

```javascript
let article = $('#article').val();
```

Le meilleur moyen consiste à utiliser un package PHP vers JS spécialisé pour transférer les données.

[🔝 Retour au contenu](#contents)

### **Utilisez des fichiers de configuration et de langue, des constantes au lieu du texte dans le code**

Mal:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

Bien:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 Retour au contenu](#contents)

### **Utiliser les outils standard de Laravel acceptés par la communauté**

Préférez utiliser les fonctionnalités intégrées de Laravel et les packages de communauté au lieu d'utiliser des packages et des outils tiers. Tout développeur qui travaillera avec votre application à l'avenir devra apprendre de nouveaux outils. En outre, les chances d'obtenir de l'aide de la communauté Laravel sont considérablement réduites lorsque vous utilisez un package ou un outil tiers. Ne faites pas payer votre client pour cela.

Tâche | Outils standard | Outils tiers
------------ | ------------- | -------------
Autorisation | Policies | Entrust, Sentinel et d'autres packages
Compiler des assets | Laravel Mix | Grunt, Gulp, packages tiers
Environnement de développement | Homestead | Docker
Déploiement | Laravel Forge | Deployer et d'autre solutions
Tests unitaires | PHPUnit, Mockery | Phpspec
Test du navigateur | Laravel Dusk | Codeception
DB | Eloquent | SQL, Doctrine
Templates | Blade | Twig
Travailler avec des données | Laravel collections | Arrays
Validation du formulaire | Request classes | 3rd party packages, validation dans le contrôleur
Authentification | Built-in | 3rd party packages, votre propre solution
API D'authentification | Laravel Passport | 3rd party JWT et OAuth packages
Création d'API | Built-in | Dingo API and similar packages
Travailler avec une structure de base de données | Migrations | Travailler directement avec la structure de la base de données
Localisation | Built-in | 3rd party packages
Interfaces utilisateur en temps réel | Laravel Echo, Pusher | Packages tiers et utilisation directe de WebSockets
Générer des données de test | Seeder classes, Model Factories, Faker | Création manuelle de données de test
Planification des tâches | Laravel Task Scheduler | Scripts et packages tiers
DB | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝 Retour au contenu](#contents)

### **Suivre les conventions de nommage de Laravel**

 Suivre [Normes PSR](http://www.php-fig.org/psr/psr-2/).

 Suivez également les conventions de nommage acceptées par la communauté Laravel:

Quoi | Comment | Bien | Mal
------------ | ------------- | ------------- | -------------
Controller | singulier | ArticleController | ~~ArticlesController~~
Route | pluriel | articles/1 | ~~article/1~~
Route nommée | snake_case avec notation par points | users.show_active | ~~users.show-active, show-active-users~~
Model | singulier | User | ~~Users~~
Relations hasOne or belongsTo | singulier | articleComment | ~~articleComments, article_comment~~
Toutes les autres relations | pluriel | articleComments | ~~articleComment, article_comments~~
Table | plurielle | article_comments | ~~article_comment, articleComments~~
Table pivot | noms des modèles au singulier dans l'ordre alphabétique | article_user | ~~user_article, articles_users~~
Colonne de table | snake_case sans nom de modèle | meta_title | ~~MetaTitle; article_meta_title~~
Attribut du Model | snake_case | $model->created_at | ~~$model->createdAt~~
Foreign key | Nom du modèle au singulier avec _id comme suffix | article_id | ~~ArticleId, id_article, articles_id~~
Primary key | - | id | ~~custom_id~~
Migration | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
Méthode | camelCase | getAll | ~~get_all~~
Méthodes dans le controlleur de ressource | [table](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
Méthode dans une classe de test | camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
Variable | camelCase | $articlesWithAuthor | ~~$articles_with_author~~
Collection | descriptif, pluriel | $activeUsers = User::active()->get() | ~~$active, $data~~
Object | descriptif, singulier | $activeUser = User::active()->first() | ~~$users, $obj~~
Index de fichier de config et de langage | snake_case | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
Vue | kebab-case | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
Config | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Contract (interface) | adjectif ou nom | Authenticatable | ~~AuthenticationInterface, IAuthentication~~
Trait | adjectif | Notifiable | ~~NotificationTrait~~

[🔝 Retour au contenu](#contents)

### **Utilisez une syntaxe plus courte et plus lisible dans la mesure du possible**

Mal:

```php
$request->session()->get('cart');
$request->input('name');
```

Bien:

```php
session('cart');
$request->name;
```

Plus d'exemples:

Syntaxe commune | Syntaxe plus courte et plus lisible
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

[🔝 Retour au contenu](#contents)

### **Utilisez un conteneur IoC ou des façades au lieu de la nouvelle classe**

La nouvelle syntaxe de classe crée un couplage étroit entre les classes et complique les tests. Utilisez plutôt le conteneur IoC ou les façades.

Mal:

```php
$user = new User;
$user->create($request->validated());
```

Bien:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 Retour au contenu](#contents)

### **Ne pas obtenir directement les données du fichier `.env`**

Passez les données aux fichiers de configuration à la place, puis utilisez la fonction d'assistance `config ()` pour utiliser les données dans une application.

Mal:

```php
$apiKey = env('API_KEY');
```

Bien:

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[🔝 Retour au contenu](#contents)

### **Stocker les dates au format standard. Utiliser des accesseurs et des mutateurs pour modifier le format de date**

Mal:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

Bien:

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

[🔝 Retour au contenu](#contents)

### **D'autres bonnes pratiques**

Ne mettez jamais aucune logique dans les fichiers de routes.

Minimisez l'utilisation de PHP vanilla dans les modèles de blade.

[🔝 Retour au contenu](#contents)
