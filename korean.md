![Laravel 모범 사례](/images/logo-korean.png?raw=true)

You might also want to check out the [real-world Laravel example application](https://github.com/alexeymezenin/laravel-realworld-example-app)

번역:

[Nederlands](https://github.com/Protoqol/Beste-Laravel-Praktijken) (by [Protoqol](https://github.com/Protoqol))

[한국어](korean.md) (by [dumbbelloper](https://github.com/dumbbelloper))

[Українська](ukrainian.md) (by [Tenevyk](https://github.com/tenevyk))

[Русский](russian.md)

[فارسی](persian.md) (by [amirhossein baghaie](https://github.com/ohmydevops))

[Português](https://github.com/jonaselan/laravel-best-practices) (by [jonaselan](https://github.com/jonaselan))

[Tiếng Việt](https://chungnguyen.xyz/posts/code-laravel-lam-sao-cho-chuan) (by [Chung Nguyễn](https://github.com/nguyentranchung))

[Español](spanish.md) (by [César Escudero](https://github.com/cedaesca))

[Français](french.md) (by [Mikayil S.](https://github.com/mikayilsrt))

[Polski](polish.md) (by [Karol Pietruszka](https://github.com/pietrushek))

[Türkçe](turkish.md) (by [Burak](https://github.com/ikidnapmyself))

[Deutsch](german.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Italiana](italian.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[العربية](arabic.md) (by [ahmedsaoud31](https://github.com/ahmedsaoud31))

[![Laravel example app](/images/laravel-real-world-banner.png?raw=true)](https://github.com/alexeymezenin/laravel-realworld-example-app)

## Contents
[단일 책임 원칙](#단일-책임-원칙)

[모델은 무겁게, 컨트롤러는 가볍게](#모델은-무겁게-컨트롤러는-가볍게)

[Validation-유효성 검사](#validation-유효성-검사)

[비즈니스 로직은 서비스 클래스에 있어야 합니다.](#비즈니스-로직은-서비스-클래스에-있어야-합니다)

[중복 배제(Don't repeat yourself)](#중복-배제dont-repeat-yourself)

[Query Builder, raw SQL 쿼리보다 Eloquent를 사용하는 것이 좋습니다.](#query-builder-raw-sql-쿼리보다-eloquent를-사용하는-것이-좋습니다)

[Mass assignment-대량 할당](#mass-assignment-대량-할당)

[블레이드 템플릿에서 쿼리를 실행하지 않습니다. 그리고 즉시 로딩을 사용합니다.(N + 1 문제)](#블레이드-템플릿에서-쿼리를-실행하지-않습니다-그리고-즉시-로딩을-사용합니다n--1-문제)

[방대한 데이터를 작업할 때 나눠서 처리합니다.](#방대한-데이터를-작업할-때-나눠서-처리합니다)

[코드에 주석을 작성합니다. 하지만 주석보다 의미있는 메서드 이름과 변수 이름을 사용하는 것이 더 좋습니다.](#코드에-주석을-작성합니다-하지만-주석보다-의미있는-메서드-이름과-변수-이름을-사용하는-것이-더-좋습니다)

[블레이드 템플릿에 JS와 CSS를 작성하지 않고 PHP 클래스에 HTML을 작성하지 않습니다.](#블레이드-템플릿에-js와-css를-작성하지-않고-php-클래스에-html을-작성하지-않습니다)

[코드에 텍스트로 작성하지 않고, 설정 파일, 언어 파일, 상수 등을 사용합니다.](#코드에-텍스트로-작성하지-않고-설정-파일-언어-파일-상수-등을-사용합니다)

[라라벨 커뮤니티에서 수용하는 표준 라라벨 도구를 사용합니다.](#라라벨-커뮤니티에서-수용하는-표준-라라벨-도구를-사용합니다)

[라라벨 네이밍 규칙을 따릅니다.](#라라벨-네이밍-규칙을-따릅니다)

[될 수 있으면 짧고 읽기 쉬운 문법을 사용합니다.](#될-수-있으면-짧고-읽기-쉬운-문법을-사용합니다)

[new Class 대신 IoC 컨테이너 또는 파사드를 사용합니다.](#new-class-대신-ioc-컨테이너-또는-파사드를-사용합니다)

[.env 파일에서 직접 데이터를 가져오지 않습니다.](#env-파일에서-직접-데이터를-가져오지-않습니다)

[날짜를 표준 형식으로 저장합니다. accessors(get), mutators(set)을 사용해 날짜 형식을 수정합니다.](#날짜를-표준-형식으로-저장합니다-accessorsget-mutatorsset을-사용해-날짜-형식을-수정합니다)

[또 다른 좋은 사례](#또-다른-좋은-사례)

### **단일 책임 원칙**

클래스와 메서드는 하나의 책임만 있어야 합니다.

나쁜 예:

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

좋은 예:

```php
public function getFullNameAttribute(): bool
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

[🔝 목차로 돌아가기](#contents)

### **모델은 무겁게, 컨트롤러는 가볍게**

DB와 관련된 로직은 Eloquent 모델이나 Repository 클래스에 작성되어야 합니다.

나쁜 예:

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

좋은 예:

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

[🔝 목차로 돌아가기](#contents)

### **Validation-유효성 검사**

유효성 검사 로직을 컨트롤러에서 Request 클래스로 옮깁니다.

나쁜 예:

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

좋은 예:

```php
public function store(PostRequest $request)
{    
    ....
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

[🔝 목차로 돌아가기](#contents)

### **비즈니스 로직은 서비스 클래스에 있어야 합니다.**

컨트롤러는 하나의 책임만 가지기 때문에 비즈니스 로직은 서비스 클래스에 있어야 합니다.


나쁜 예:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

좋은 예:

```php
public function store(Request $request)
{
    $this->articleService->handleUploadedImage($request->file('image'));

    ....
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

[🔝 목차로 돌아가기](#contents)

### **중복 배제(Don't repeat yourself)**

코드를 재사용합니다. 단일 책임 원칙뿐만 아니라 블레이드 템플릿, Eloquent 스코프 등은 코드의 중복을 피할 수 있도록 도와줍니다.

나쁜 예:

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

좋은 예:

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

[🔝 목차로 돌아가기](#contents)

### **Query Builder, raw SQL 쿼리보다 Eloquent를 사용하는 것이 좋습니다.**

Eloquent를 사용하면 읽기 쉽고 유지 보수할 수 있는 코드를 작성할 수 있습니다. Eloquent는 소프트 삭제, 이벤트, 스코프 등 좋은 기능이 있습니다.

나쁜 예:

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

좋은 예:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 목차로 돌아가기](#contents)

### **Mass assignment-대량 할당**

나쁜 예:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

좋은 예:

```php
$category->article()->create($request->validated());
```

[🔝 목차로 돌아가기](#contents)

### **블레이드 템플릿에서 쿼리를 실행하지 않습니다. 그리고 즉시 로딩을 사용합니다.(N + 1 문제)**

나쁜예 (유저 전체를 가져오는 쿼리(1번) + 해당 유저의 프로필을 가져오는 쿼리(100번) = 101번 실행):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

좋은 예 (유저 전체를 가져오는 쿼리(1번) + 해당 유저의 프로필을 가져오는 쿼리(1번) = 2번 실행):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 목차로 돌아가기](#contents)

### **대량의 데이터를 작업할 때 나눠서 처리합니다.**

나쁜예 ():

```php
$users = $this->get();

foreach ($users as $user) {
    ...
}
```

좋은예:

```php
$this->chunk(500, function ($users) {
    foreach ($users as $user) {
        ...
    }
});
```

[🔝 Back to contents](#contents)


### **코드에 주석을 작성합니다. 하지만 주석보다 의미있는 메서드 이름과 변수 이름을 사용하는 것이 더 좋습니다.**

Bad:

```php
// Determine if there are any joins
if (count((array) $builder->getQuery()->joins) > 0)
```

Good:

```php
if ($this->hasJoins())
```

[🔝 목차로 돌아가기](#contents)

### **블레이드 템플릿에 JS와 CSS를 작성하지 않고 PHP 클래스에 HTML을 작성하지 않습니다.**

나쁜 예:

```php
let article = `{{ json_encode($article) }}`;
```

조금 더 나은 예:

```php
<input id="article" type="hidden" value='@json($article)'>

Or

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

자바스크립트 파일:

```php
let article = $('#article').val();
```

The best way is to use specialized PHP to JS package to transfer the data.

[🔝 목차로 돌아가기](#contents)

### **코드에 텍스트로 작성하지 않고, 설정 파일, 언어 파일, 상수 등을 사용합니다.**

나쁜 예:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

좋은 예:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 목차로 돌아가기](#contents)

### **라라벨 커뮤니티에서 수용하는 표준 라라벨 도구를 사용합니다.**

써드파티 패키지 및 도구 대신 내장되어있는 라라벨 기능과 커뮤니티 패키지를 사용합니다. 프로젝트에 참여하게 되는 개발자는 새로운 도구에 대해 학습을 해야합니다. 또한 써드파티 패키지나 도구를 사용할 때 라라벨 커뮤니티의 도움을 받을 수 있는 기회가 줄어듭니다.

Task | Standard tools | 3rd party tools
------------ | ------------- | -------------
Authorization | Policies | Entrust, Sentinel and other packages
Compiling assets | Laravel Mix | Grunt, Gulp, 3rd party packages
Development Environment | Laravel Sail, Homestead | Docker
Deployment | Laravel Forge | Deployer and other solutions
Unit testing | PHPUnit, Mockery | Phpspec
Browser testing | Laravel Dusk | Codeception
DB | Eloquent | SQL, Doctrine
Templates | Blade | Twig
Working with data | Laravel collections | Arrays
Form validation | Request classes | 3rd party packages, validation in controller
Authentication | Built-in | 3rd party packages, your own solution
API authentication | Laravel Passport, Laravel Sanctum | 3rd party JWT and OAuth packages
Creating API | Built-in | Dingo API and similar packages
Working with DB structure | Migrations | Working with DB structure directly
Localization | Built-in | 3rd party packages
Realtime user interfaces | Laravel Echo, Pusher | 3rd party packages and working with WebSockets directly
Generating testing data | Seeder classes, Model Factories, Faker | Creating testing data manually
Task scheduling | Laravel Task Scheduler | Scripts and 3rd party packages
DB | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝 목차로 돌아가기](#contents)

### **라라벨 네이밍 규칙을 따릅니다.**

[PSR 표준](http://www.php-fig.org/psr/psr-2/)을 따릅니다.

또한 라라벨 커뮤니티에서 수용하고 있는 네이밍 규칙을 따릅니다:

What | How | Good | Bad
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
Contract (interface) | adjective or noun | AuthenticationInterface | ~~Authenticatable, IAuthentication~~
Trait | adjective | Notifiable | ~~NotificationTrait~~

[🔝 목차로 돌아가기](#contents)

### **될 수 있으면 짧고 읽기 쉬운 문법을 사용합니다.**

나쁜 예:

```php
$request->session()->get('cart');
$request->input('name');
```

좋은 예:

```php
session('cart');
$request->name;
```

더 많은 예시:

Common syntax | Shorter and more readable syntax
------------ | -------------
`Session::get('cart')` | `session('cart')`
`$request->session()->get('cart')` | `session('cart')`
`Session::put('cart', $data)` | `session(['cart' => $data])`
`$request->input('name'), Request::get('name')` | `$request->name, request('name')`
`return Redirect::back()` | `return back()`
`is_null($object->relation) ? null : $object->relation->id` | `optional($object->relation)->id` (in PHP 8: `$object->relation?->id`)
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

[🔝 목차로 돌아가기](#contents)

### **new Class 대신 IoC 컨테이너 또는 파사드를 사용합니다.**

new Class 문법은 클래스 간의 결합도를 높이고 테스트를 복잡하게 만듭니다. new Class 문법 대신에 IoC 컨테이너 또는 파사드를 사용합니다.

나쁜 예:

```php
$user = new User;
$user->create($request->validated());
```


좋은 예:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 목차로 돌아가기](#contents)

### **`.env` 파일에서 직접 데이터를 가져오지 않습니다.**

데이터를 설정 파일에 전달한 다음 `config()` helper 함수를 통해 애플리케이션에서 데이터를 사용합니다.

나쁜 예:

```php
$apiKey = env('API_KEY');
```

좋은 예:

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[🔝 목차로 돌아가기](#contents)

### **날짜를 표준 형식으로 저장합니다. accessors(get), mutators(set)을 사용해 날짜 형식을 수정합니다.**

나쁜 예:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

좋은 예:

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

[🔝 목차로 돌아가기](#contents)

### **또 다른 좋은 사례**

Laravel 및 유사한 프레임워크(예: RoR, Django)에 익숙하지 않은 패턴 및 도구를 사용하지 마십시오. 앱 빌드를 위한 Symfony(또는 Spring) 접근 방식이 마음에 든다면 이러한 프레임워크를 대신 사용하는 것이 좋습니다.

routes 파일에 로직을 넣지 마십시오.

블레이드 템플릿에서 바닐라 PHP 사용을 최소화합니다.

테스트를 위해 인메모리 DB를 사용합니다.

프레임워크 버전 업데이트 및 기타 여러 문제와 관련된 문제를 피하기 위해 표준 프레임워크 기능을 재정의하지 마십시오.

가능하면 최신 PHP 구문을 사용하되 가독성을 잊지 마십시오.

무엇을 하고 있는지 잘 모르는 경우 View Composers 및 이와 유사한 도구를 사용하지 마십시오. 대부분의 경우 문제를 해결하는 더 좋은 방법이 있습니다.

[🔝 목차로 돌아가기](#contents)
