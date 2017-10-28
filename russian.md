Хорошие практики при работе с фреймворком Laravel.

## Содержание

[Принцип единственной ответственности (Single responsibility principle)](#Принцип-единственной-ответственности-single-responsibility-principle)

[Тонкие контроллеры, толстые модели](#Тонкие-контроллеры-толстые-модели)

[Валидация](#Валидация)

[Бизнес логика в сервис-классах](#Бизнес-логика-в-сервис-классах)

[Не повторяйся (DRY)](#Не-повторяйся-dry)

[Предпочитайте Eloquent конструктору запросов (query builder) и сырым запросам в БД. Предпочитайте работу с коллекциями работе с массивами](#Предпочитайте-eloquent-конструктору-запросов-query-builder-и-сырым-запросам-в-БД-Предпочитайте-работу-с-коллекциями-работе-с-массивами)

[Используйте массовое заполнение (mass assignment)](#Используйте-массовое-заполнение-mass-assignment)

[Комментируйте код, предпочитайте читаемые имена методов комментариям](#Комментируйте-код-предпочитайте-читаемые-имена-методов-комментариям)

[Выносите JS и CSS из шаблонов Blade и HTML из PHP кода](#Выносите-js-и-css-из-шаблонов-blade-и-html-из-php-кода)

[Используйте инструменты и практики принятые сообществом](#Используйте-инструменты-и-практики-принятые-сообществом)

[Соблюдайте соглашения сообщества](#Соблюдайте-соглашения-сообщества)

[Конфиги, языковые файлы и константы вместо текста в коде](#Конфиги-языковые-файлы-и-константы-вместо-текста-в-коде)

[Короткий и читаемый синтаксис там, где это возможно](#Короткий-и-читаемый-синтаксис-там-где-это-возможно)

[Используйте IoC или фасады вместо new Class](#Используйте-ioc-или-фасады-вместо-new-class)

[Другие советы и практики](#Другие-советы-и-практики)

### **Принцип единственной ответственности (Single responsibility principle)**

Каждый класс и метод должны выполнять лишь одно действие.

Плохо:

```
public function getFullNameAttribute()
{
    if (auth()->user() && auth()->user()->hasRole('client') && auth()->user()->isVerified()) {
        return 'Mr. ' . $this->first_name . ' ' . $this->middle_name . ' ' $this->last_name;
    } else {
        return $this->first_name[0] . '. ' . $this->last_name;
    }
}
```

Хорошо:

```
public function getFullNameAttribute()
{
    return $this->isVerifiedClient() ? $this->getFullNameLong() : $this->getFullNameShort();
}

public function isVerfiedClient()
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

[⬆ Наверх](#Содержание)

### **Тонкие контроллеры, толстые модели**

По своей сути, это лишь один из частных случаев принципа единой ответственности. Выносите работу с данными в модели при работе с Eloquent или в репозитории при работе с Query Builder или "сырыми" SQL запросами.

Плохо:

```
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

Хорошо:

```
public function index()
{
    return view('index', ['clients' => $this->client->getWithNewOrders()]);
}

Class Client extends Model
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

[⬆ Наверх](#Содержание)

### **Валидация**

Следуя принципам тонкого контроллера и SRP, выносите валидацию из контроллера в Request классы.

Плохо:

```
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

Хорошо:

```
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

[⬆ Наверх](#Содержание)

### **Бизнес логика в сервис-классах**

Контроллер должен выполнять только свои прямые обязанности, поэтому выносите всю бизнес логику в отдельные классы и сервис классы.

Плохо:

```
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

Хорошо:

```
public function store(Request $request)
{
    $this->articleService->handleUploadedImage($request);

    ....
}

class ArticleService
{
    public function handleUploadedImage(Request $request)
    {
        if ($request->hasFile('image')) {
            $request->file('image')->move(public_path('images') . 'temp');
        }
    }
}
```

[⬆ Наверх](#Содержание)

### **Не повторяйся (DRY)**

Этот принцип призывает вас переиспользовать код везде, где это возможно. Если вы следуете принципу SRP, вы уже избегаете повторений, но Laravel позволяет вам также переиспользовать представления, части Eloquent запросов и т.д.

Плохо:

```
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

Хорошо:

```
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

[⬆ Наверх](#Содержание)

### **Предпочитайте Eloquent конструктору запросов (query builder) и сырым запросам в БД. Предпочитайте работу с коллекциями работе с массивами**

Eloquent позволяет писать максимально читаемый код, а изменять функционал приложения несоизмеримо легче. У Eloquent также есть ряд удобных и мощных инструментов.

Плохо:

```
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

Хорошо:

```
Article::has('user.profile')->verified()->latest()->get();
```

[⬆ Наверх](#Содержание)

### **Используйте массовое заполнение (mass assignment)**

Плохо:

```
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Привязать статью к категории.
$article->category_id = $category->id;
$article->save();
```

Хорошо:

```
$category->article()->create($request()->all());
```

[⬆ Наверх](#Содержание)

### **Комментируйте код, предпочитайте читаемые имена методов комментариям**

Плохо:

```
if (count((array) $builder->getQuery()->joins) > 0)
```

Лучше:

```
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

Хорошо:

```
if ($this->hasJoins())
```

[⬆ Наверх](#Содержание)

### **Выносите JS и CSS из шаблонов Blade и HTML из PHP кода**

Плохо:

```
let article = `{{ json_encode($article) }}`;
```

Лучше:

```
<input id="article" tyoe="hidden" value="{{ json_encode($article) }}">

....

let article = $('#article').val();
```

Еще лучше использовать специализированный пакет для передачи данных из бэкенда во фронтенд.

[⬆ Наверх](#Содержание)

### **Конфиги, языковые файлы и константы вместо текста в коде**

Непосредственно в коде не должно быть никакого текста.

Плохо:

```
public function isNormal ()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Ваша статья была успешно добавлена');
```

Хорошо:

```
public function isNormal ()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[⬆ Наверх](#Содержание)

### **Используйте инструменты и практики принятые сообществом**

Laravel имеет встроенные инструменты для решения часто встречаемых задач. Изучите документацию и используйте Request классы для валидации, политики для авторизации, Mix для работы с JS и CSS, Dusk и phpunit для тестов, Eloquent для работы с БД, Passport для аутентификации при работе с API, коллекции вместо массивов, миграции для создания таблиц, Seeder классы для создания тестовых данных и т.д. Предпочитайте использовать встроенный функционал использованию сторонних пакетов и инструментов.

Дело в том, что Laravel разработчику, пришедшему в проект после вас, придется изучать и работать с новым для него инструментом, со всеми вытекающими. Получить помощь от сообщества будет также гораздо труднее. Не заставляйте клиента или работодателя платить за ваши велосипеды.

[⬆ Наверх](#Содержание)

### **Соблюдайте соглашения сообщества**

 Следуйте [стандартам PSR](http://www.php-fig.org/psr/psr-2/) при написании кода. Соблюдайте cоглашения об именовании.

Что | Хорошо | Плохо
------------ | ------------- | -------------
Контроллер | ArticleController | ArticlesController
Модель | User | Users
Таблица | articles | article
Столбец в таблице | meta_title | MetaTitle; article_meta_title
Cвойство модели | metaTitle | meta_title
Миграция | 2017_01_01_000000_create_articles_table | 2017_01_01_000000_articles
Метод | getAll | get_all
Метод в контроллере ресурсов | store | saveArticle
Метод в тесте | testGuestCannotSeeArticle | test_guest_cannot_see_article
Переменные | $articlesWithAuthor | $articles_with_author
Переменные в конфиге, языковых файлах | articles_enabled | ArticlesEnabled; articles-enabled
Представления (view) | show_filtered.blade.php | showFiltered.blade.php; show-filtered.blade.php

[⬆ Наверх](#Содержание)

### **Короткий и читаемый синтаксис там, где это возможно**

Плохо:

```
$request->session()->get('cart');
$request->input('name');
```

Хорошо:

```
session('cart');
$request->name;
```

Еще примеры:

Часто используемый синтаксис | Более короткий и читаемый синтаксис
------------ | -------------
Session::get('cart') | session('cart')
$request->session()->get('cart') | session('cart')
Session::put('cart', $data) | session(['cart' => $data])
$request->input('name') | $request->name
Request::get('name') | request('name')
return Redirect::back() | return back()
return view('index')->with('title', $title)->with('client', $client) | return view('index', compact('title', 'client'))

[⬆ Наверх](#Содержание)

### **Используйте IoC или фасады вместо new Class**

Внедрение классов через синтаксис new Class создает сильное сопряжение между частями приложения и усложняет тестирование. Используйте контейнер или фасады.

Плохо:

```
$user = new User;
$user->create($request()->all());
```

Хорошо:

```
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request()->all());
```

[⬆ Наверх](#Содержание)

### **Другие советы и практики**

Не размещайте логику в маршрутах.

Не используйте сырой PHP в шаблонах Blade.

[⬆ Наверх](#Содержание)
