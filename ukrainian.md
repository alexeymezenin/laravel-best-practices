![Найкращі практики Laravel](/images/logo-ukrainian.png?raw=true)

Це не адаптація Laravel під принципи SOLID, схем тощо. Тут ви знайдете найкращі практики, які зазвичай ігнорують в справжніх Laravel проєктах. Також, рекомендую ознайомитися з [хорошими практиками в контексті PHP](https://github.com/jupeter/clean-code-php).

[Back to English version](README.md)

## Зміст

[Принцип єдиної відповідальності (Single responsibility principle)](#Принцип-єдиної-відповідальності-single-responsibility-principle)

[Товсті моделі, тонкі контролери](#Товсті-моделі-тонкі-контролери)

[Перевірка даних](#Перевірка-даних)

[Бізнес-логіка лише в сервісних класах](#Бізнес-логіка-лише-в-сервісних-класах)

[Не повторюйтеся (DRY)](#Не-повторюйтеся-dry)

[Віддавайте перевагу Eloquent понад використанням Query Builder та сирих SQL запитів. Перевага у колекцій, а не масивів](#Віддавайте-перевагу-eloquent-понад-використанням-query-builder-та-сирих-SQL-запитів-Перевага-у-колекцій-а-не-масивів)

[Масове призначення](#Масове-призначення)

[Ніяких запитів у шаблонах Blade та використовуйте жадібне завантаження (проблема N + 1)](#Ніяких-запитів-у-шаблонах-blade-та-використовуйте-жадібне-завантаження-проблема-n--1)

[Коментуйте свій код, але описові назви методів та змінних краще](#Коментуйте-свій-код-але-описові-назви-методів-та-змінних-краще)

[Жодних JS та CSS у шаблонах Blade та HTML у PHP класах](#Жодних-js-та-css-у-шаблонах-blade-та-html-у-php-класах)

[Конфігурації, мовні файли та константи замість тексту в коді](#Конфігурації-мовні-файли-та-константи-замість-тексту-в-коді)

[Використовуйте стандартні інструменти Laravel, що прийняті спільнотою](#Використовуйте-стандартні-інструменти-laravel-що-прийняті-спільнотою)

[Дотримуйтеся домовленостей Laravel з найменування](#Дотримуйтеся-домовленостей-laravel-з-найменування)

[Використовуйте, де можливо, короткий та читабельний синтаксис](#Використовуйте-де-можливо-короткий-та-читабельний-синтаксис)

[Використовуйте контейнер IoC або фасади замість new Class](#Використовуйте-контейнер-ioc-або-фасади-замість-new-class)

[Не отримуйте дані безпосередньо з файлу `.env`](#Не-отримуйте-дані-безпосередньо-з-файлу-env)

[Зберігайте дати в стандартному форматі. Використовуйте методи доступу та зміни даних для зміни формату](#Зберігайте-дати-в-стандартному-форматі-Використовуйте-методи-доступу-та-зміни-даних-для-зміни-формату)

[Інші хороші практики](#Інші-хороші-практики)

### **Принцип єдиної відповідальності (Single responsibility principle)**

Клас та метод повинні мати лише одну відповідальність.

Погано:

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

Добре:

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

[🔝 Назад до змісту](#зміст)

### **Товсті моделі, тонкі контролери**

За своєю суттю це лише один з прикладів принципа єдиної відповідальності. Працюйте з даними в моделі при роботі з Eloquent або в репозиторії при роботі з Query Builder або "сирими" SQL запитами.

Погано:

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

Добре:

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

[🔝 Назад до змісту](#Зміст)

### **Перевірка даних**

Відповідно принципам тонкого контролера та SRP, пишіть [перевірку даних (валідацію)](https://laravel.com/docs/5.8/validation) у Request класах, а не контролерах.

Погано:

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

Добре:

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

[🔝 Назад до змісту](#Зміст)

### **Бізнес-логіка лише в сервісних класах**

Контролер має виконувати свої прямі обов’язки, тож перемістіть бізнес-логіку з контролерів до [сервісних класів](https://laravel.com/docs/5.8/providers).

Bad:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

Good:

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

[🔝 Назад до змісту](#Зміст)

### **Не повторюйтеся (DRY)**

Повторно використовуйте код де можете. SRP вже допомагатиме вам уникати задвоєнь, але Laravel дозволяє також повторно використовувати [шаблони Blade](https://laravel.com/docs/5.8/blade), [області дії Eloquent](https://laravel.com/docs/5.8/eloquent#query-scopes) тощо.

Погано:

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

Добре:

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

[🔝 Назад до змісту](#Зміст)

### **Віддавайте перевагу Eloquent понад використанням Query Builder та сирих SQL запитів. Перевага у колекцій, а не масивів**

Eloquent дозволяє вам писати читабельний та підтримний код. Також, Eloquent має чудові вбудовані інструменти, як-от: [м’які видалення](https://laravel.com/docs/5.8/eloquent#soft-deleting), [події](https://laravel.com/docs/5.8/eloquent#events), [області дії](https://laravel.com/docs/5.8/eloquent#query-scopes) тощо.

Погано:

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

Добре:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 Назад до змісту](#Зміст)

### **Масове призначення**

[Масове призначення](https://laravel.com/docs/5.8/eloquent#mass-assignment)

Погано:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Додати категорію до статті
$article->category_id = $category->id;
$article->save();
```

Добре:

```php
$category->article()->create($request->validated());
```

[🔝 Назад до змісту](#Зміст)

### **Ніяких запитів у шаблонах Blade та використовуйте жадібне завантаження (проблема N + 1)**

Погано (на 100 користувачів 101 запит у БД (базу даних)):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Добре (на 100 користувачів лише 2 запити у БД):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 Назад до змісту](#Зміст)

### **Коментуйте свій код, але описові назви методів та змінних краще**

Погано:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

Нормально:

```php
// Визначає наявність join-ів.
if (count((array) $builder->getQuery()->joins) > 0)
```

Добре:

```php
if ($this->hasJoins())
```

[🔝 Назад до змісту](#Зміст)

### **Жодних JS та CSS у шаблонах Blade та HTML у PHP класах**

Погано:

```php
let article = `{{ json_encode($article) }}`;
```

Краще:

```php
<input id="article" type="hidden" value='@json($article)'>

Або

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

У файлі Javascript:

```javascript
let article = $('#article').val();
```

Найкращий варіант — використовувати спеціалізований пакунок для передачі даних з PHP до JS.

[🔝 Назад до змісту](#Зміст)

### **Конфігурації, мовні файли та константи замість тексту в коді**

[Про це в документації Laravel](https://laravel.com/docs/5.8/localization)

Погано:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Вашу статтю було додано!');
```

Добре:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 Назад до змісту](#Зміст)

### **Використовуйте стандартні інструменти Laravel, що прийняті спільнотою**

Віддавайте перевагу вбудованому функціоналу Laravel та пакункам від спільноти використанню сторонніх пакунків та інструментів. Будь-якому розробнику, що працюватиме з вашим застосунком у майбутньому, знадобиться вивчати нові інструменти. Окрім того, в разі використання сторонніх пакунків чи інструментів шанси отримати допомогу від спільноти Laravel відчутно менші. Не змушуйте свого клієнта платити за це.

Завдання | Стандартні інструменти | Сторонні інструменти
------------ | ------------- | -------------
Авторизація | Policies | Entrust, Sentinel and other packages
Компіляція засобів | Laravel Mix | Grunt, Gulp, 3rd party packages
Середовище розробки | Homestead | Docker
Розгортання застосунків | Laravel Forge | Deployer and other solutions
Unit тестування | PHPUnit, Mockery | Phpspec
Тестування браузера | Laravel Dusk | Codeception
База даних | Eloquent | SQL, Doctrine
Шаблони | Blade | Twig
Робота з даними | Laravel collections | Arrays
Перевірка даних форми | Request classes | 3rd party packages, validation in controller
Автентифікація | Built-in | 3rd party packages, your own solution
API автентифікація | Laravel Passport | 3rd party JWT and OAuth packages
Створення API | Built-in | Dingo API and similar packages
Робота зі структурою БД | Migrations | Working with DB structure directly
Локалізація | Built-in | 3rd party packages
Користувацькі інтерфейси в реальному часі | Laravel Echo, Pusher | 3rd party packages and working with WebSockets directly
Генерування тестових даних | Seeder classes, Model Factories, Faker | Creating testing data manually
Планування завдань | Laravel Task Scheduler | Scripts and 3rd party packages
База даних | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝 Назад до змісту](#Зміст)

### **Дотримуйтеся домовленостей Laravel з найменування**

 Дотримуйтеся [стандартів PSR](http://www.php-fig.org/psr/psr-2/).
 
 Також, дотримуйтеся домовленостей з найменування прийнятих спільнотою Laravel:

Що | Написання | Добре | Погано
------------ | ------------- | ------------- | -------------
Контролер | однина | ArticleController | ~~ArticlesController~~
Маршрути | множина | articles/1 | ~~article/1~~
Назви маршрутів | snake_case з позначенням крапкою | users.show_active | ~~users.show-active, show-active-users~~
Модель | однина | User | ~~Users~~
Зв’язки hasOne або belongsTo | однина | articleComment | ~~articleComments, article_comment~~
Решта зв’язків | множина | articleComments | ~~articleComment, article_comments~~
Таблиця | множина | article_comments | ~~article_comment, articleComments~~
Зведена таблиця | ім’я моделі в однині в алфавітному порядку | article_user | ~~user_article, articles_users~~
Стовпчик таблиці | snake_case без імені моделі | meta_title | ~~MetaTitle; article_meta_title~~
Властивість моделі | snake_case | $model->created_at | ~~$model->createdAt~~
Зовнішній ключ | ім’я моделі в однині з суфіксом _id | article_id | ~~ArticleId, id_article, articles_id~~
Первинний ключ | - | id | ~~custom_id~~
Міграція | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
Метод | camelCase | getAll | ~~get_all~~
Метод у ресурсному контролері| [таблиця](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
Метод у тестовому класі | camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
Змінна | camelCase | $articlesWithAuthor | ~~$articles_with_author~~
Зібрання | описове, множина | $activeUsers = User::active()->get() | ~~$active, $data~~
Об’єкт | описове, однина | $activeUser = User::active()->first() | ~~$users, $obj~~
Індекси в конфігураційних та мовних файлах | snake_case | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
Вигляд | kebab-case | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
Конфігурація | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Домовленість (інтерфейс) | прикметник або іменник | Authenticatable | ~~AuthenticationInterface, IAuthentication~~
Trait | прикметник | Notifiable | ~~NotificationTrait~~

[🔝 Назад до змісту](#Зміст)

### **Використовуйте, де можливо, короткий та читабельний синтаксис**

Погано:

```php
$request->session()->get('cart');
$request->input('name');
```

Добре:

```php
session('cart');
$request->name;
```

Більше прикладів:

Початковий синтаксис | Короткий й читабельний синтаксик
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

[🔝 Назад до змісту](#Зміст)

### **Використовуйте контейнер IoC або фасади замість new Class**

Синтаксис new Class створює міцні з’єднання між класами та ускладнює тестування. Використовуйте натомість контейнер IoC або фасади.

Погано:

```php
$user = new User;
$user->create($request->validated());
```

Добре:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 Назад до змісту](#Зміст)

### **Не отримуйте дані безпосередньо з файлу .env**

Натомість, передавайте дані до конфігураційних файлів та потім використовуйте допоміжну функцію `config()` для використання даних в застосунку.

Погано:

```php
$apiKey = env('API_KEY');
```

Добре:

```php
// config/api.php
'key' => env('API_KEY'),

// Використайте дані
$apiKey = config('api.key');
```

[🔝 Назад до змісту](#Зміст)

### **Зберігайте дати в стандартному форматі. Використовуйте методи доступу та зміни даних для зміни формату**

Погано:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

Добре:

```php
// Модель
protected $dates = ['ordered_at', 'created_at', 'updated_at'];
public function getSomeDateAttribute($date)
{
    return $date->format('m-d');
}

// Вигляд
{{ $object->ordered_at->toDateString() }}
{{ $object->ordered_at->some_date }}
```

[🔝 Назад до змісту](#Зміст)

### **Інші хороші практики**

Жодної логіки в маршрутах.

Зменшіть використання чистого PHP у шаблонах Blade.

[🔝 Назад до змісту](#Зміст)
