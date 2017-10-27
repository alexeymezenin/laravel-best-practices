## Содержание

[Принцип единственной ответственности (Single responsibility principle)](#Принцип-единственной-ответственности-single-responsibility-principle)

[Тонкие контроллеры, толстые модели](#Тонкие-контроллеры-толстые-модели)

[Валидация](#Валидация)

[Бизнес логика в сервис-классах](#Бизнес-логика-в-сервис-классах)

[Предпочитайте Eloquent конструктору запросов (query builder) и сырым запросам в БД. Предпочитайте работу с коллекциями работе с массивами](#Предпочитайте-eloquent-конструктору-запросов-query-builder-и-сырым-запросам-в-БД-Предпочитайте-работу-с-коллекциями-работе-с-массивами)

[Используйте массовое заполнение (mass assignment)](#Используйте-массовое-заполнение-mass-assignment)

[Комментируйте код, предпочитайте читаемые имена методов комментариям](#Комментируйте-код-предпочитайте-читаемые-имена-методов-комментариям)

[Выносите JS и CSS из шаблонов Blade и HTML из PHP кода](#Выносите-js-и-css-из-шаблонов-blade-и-html-из-php-кода)

[Конфиги, языковые файлы и константы вместо текста в коде](#Конфиги-языковые-файлы-и-константы-вместо-текста-в-коде)

[Короткий и читаемый синтаксис там, где это возможно](#Короткий-и-читаемый-синтаксис-там-где-это-возможно)

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

Это лишь один из частных случаев принципа единой ответственности. Выносите работу с данными в классы-модели при работе с Eloquent или в классы-репозитории при работе с Query Builder и "сырыми" SQL запросами.

Плохо:

```
public gunction index()
{
    $clients = Client::verified()
        ->with(['orders' => function ($q) {
            $q->where('created_at', '<', Carbon::today()->subWeek());
        }])
        ->get();

    return view('index', ['clients' => $clients]);
}
```

Хорошо:
```
public gunction index()
{
    return view('index', ['clients' => $this->client->getWithNewOrders()]);
}

Class Client extends Model
{
    public function getWithNewOrders()
    {
        return $this->verified()
            ->with(['orders' => function ($q) {
                $q->where('created_at', '<', Carbon::today()->subWeek());
            }])
            ->get();
    }
}
```

[⬆ Наверх](#Содержание)

### **Валидация**

Следуя принципу тонкого контроллера, выносите валидацию из контроллера в Request классы.

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

Контроллер - это связующее звено приложения и он не должен делать ничего кроме этой единственной задачи. Выносите всю бизнес логику в отдельные классы и сервис-классы.

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

### **Никакой логики в маршрутах**


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

return back()->with('message', 'Статья была успешно добавлена');
```

Хорошо:

```
public function isNormal ()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

### **Короткий и читаемый синтаксис там, где это возможно**

Плохо:

```
Session::get('cart');
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
$request->session()-get('cart') | session('cart')
Session::put('cart', $data) | session(['cart' => $data])
$request->input('name') | $request->name
Request::get('name') | request('name')
return Redirect::back() | return back()
return view('index')->with('title', $title)->with('client', $client) | return view('index', compact('title', 'client'))

[⬆ Наверх](#Содержание)
