![Laravel best practices](/images/logo-traditional-chinese.png?raw=true)

本文件中列出的並不是 Laravel 版的 SOLID 原則、模式等。在本文件中，我們列出許多在實際 Laravel 專案中常常被忽略的一些最佳實踐。

## 內容

[單一職責原則](#單一職責原則)

[使 Controller 簡潔，Model 肥大](#使-controller-簡潔model-肥大)

[驗證](#驗證)

[商業邏輯應放置於 Service 類別內](#商業邏輯應放置於-service-類別內)

[DRY 原則 - 不要重覆自己](#dry-原則---不要重覆自己)

[優先使用 Eloquent 而不是 Query Builder 與原始 SQL 語句；優先使用 Collection 而不是陣列](#優先使用-eloquent-而不是-query-builder-與原始-sql-語句；優先使用-collection-而不是陣列)

[Mass Assignement - 大量賦值](#mass-assignement---大量賦值)

[不要在 Blade 樣板中執行查詢，並使用 Eager Loading (N + 1 問題)](#不要在-blade-樣板中執行查詢並使用-eager-loading-n--1-問題)

[在程式碼中加上註解，但比起註解應儘量使用描述性的方法與變數名稱](#在程式碼中加上註解但比起註解應儘量使用描述性的方法與變數名稱)

[不要將 JS 與 CSS 放到 Blade 樣板內，也不要把 HTML 放到 PHP 內](#不要將-js-與-css-放到-blade-樣板內也不要把-html-放到-php-內)

[使用設定檔與語系檔，並在程式碼中使用常數來代替文字](#使用設定檔與語系檔並在程式碼中使用常數來代替文字)

[使用社群認可的標準 Laravel 工具](#使用社群認可的標準-laravel-工具)

[遵循 Laravel 命名規範](#遵循-laravel-命名規範)

[盡可能使用簡短且可讀性更好的語法](#盡可能使用簡短且可讀性更好的語法)

[使用 IoC Container 或 Facade 而不是直接 new Class](#使用-ioc-container-或-facade-而不是直接-new-class)

[不要直接從 .env 檔案取得資料](#不要直接從-env-檔案取得資料)

[以標準格式來儲存日期時間，並以 Accesor 或 Mutator 來修改日期格式](#以標準格式來儲存日期時間並以-accesor-或-mutator-來修改日期格式)

[其他優良實踐](#其他優良實踐)

### **單一職責原則**

一個類別與方法應只有一個職責。

例如:

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

Good:

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

[🔝 回到目錄](#內容)

### **使 Controller 簡潔，Model 肥大**

如果使用 Query Builder 或原始 SQL 查詢，則請將所有 DB 關聯的邏輯放在 Eloquent Model 或 Repository 類別中。

Bad:

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

Good:

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

[🔝 回到目錄](#內容)

### **驗證**

將資料類別從 Controller 中移到 Request 類別內。

Bad:

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

Good:

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

[🔝 回到目錄](#內容)

### **商業邏輯應放置於 Service 類別內**

Controller 必須只能有單一職責，因此將商業邏輯移到 Service 類別內。

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

[🔝 回到目錄](#內容)

### **DRY 原則 - 不要重覆自己**

盡可能重複使用程式碼。通過 SRP (單一職責原則) 有助於避免重複。另外，請重複使用 Blade 樣板，並使用 Eloquent Scope 等。

Bad:

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

Good:

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

[🔝 回到目錄](#內容)

### **優先使用 Eloquent 而不是 Query Builder 與原始 SQL 語句；優先使用 Collection 而不是陣列**

使用 Eloquent 可以寫出有較高可讀性與可維護性的程式碼。另外，Eloquent 還內建了許多不錯的工具，如軟刪除、事件、Scope 等功能。

Bad:

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

Good:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 回到目錄](#內容)

### **Mass Assignement - 大量賦值**

Bad:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

Good:

```php
$category->article()->create($request->validated());
```

[🔝 回到目錄](#內容)

### **不要在 Blade 樣板中執行查詢，並使用 Eager Loading (N + 1 問題)**

例子 (若有 100 個使用者，則會執行 101 次 DB 查詢):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

更優的寫法 (若有 100 個使用者，則會執行 2 次 DB 查詢):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 回到目錄](#內容)

### **在程式碼中加上註解，但比起註解應儘量使用描述性的方法與變數名稱**

Bad:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

加上註釋:

```php
// 確定是否有任何 Join
if (count((array) $builder->getQuery()->joins) > 0)
```

Good:

```php
if ($this->hasJoins())
```

[🔝 回到目錄](#內容)

### **不要將 JS 與 CSS 放到 Blade 樣板內，也不要把 HTML 放到 PHP 內**

Bad:

```php
let article = `{{ json_encode($article) }}`;
```

Good:

```php
<input id="article" type="hidden" value='@json($article)'>

或

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

在 JavaScript 檔案中:

```javascript
let article = $('#article').val();
```

最好的方法是用專門的 PHP 或 JS 套件來傳遞資料。

[🔝 回到目錄](#內容)

### **使用設定檔與語系檔，並在程式碼中使用常數來代替文字**

Bad:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

Good:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 回到目錄](#內容)

### **使用社群認可的標準 Laravel 工具**


儘量使用內建的 Laravel 功能以及社群套件，而不是使用第三方套件與工具。若未來有哪位開發者接手你的專案，就必須要再學習新工具。另外，若使用第三方套件或工具，那麼從 Laravel 社群中取得協助的機會也會減少。請避免增加客戶的成本。

任務 | 標準工具 | 第三方工具
------------ | ------------- | -------------
權限控制 | Policies | Entrust, Sentinel 或其他套件
編譯資源 | Laravel Mix | Grunt, Gulp, 或其他第三方套件
開發環境 | Laravel Sail, Homestead | Docker
部署 | Laravel Forge | Deployer 或其他解決方案
單元測試 | PHPUnit, Mockery | Phpspec
瀏覽器測試 | Laravel Dusk | Codeception
DB | Eloquent | SQL, Doctrine
樣板 | Blade | Twig
資料操作 | Laravel Collection | 陣列
表單驗證 | Request 類別 | 第三方套件、在 Controller 中驗證
登入驗證 | 內建 | 其他第三方套件或自製解決方案
API 登入驗證 | Laravel Passport, Laravel Sanctum | 第三方 JWT 或 OAuth 套件
建立 API | 內建 | Dingo API 或類似套件
處理 DB 結構 | Migrations | 直接操作 DB 結構
本地化 | 內建 | 第三方套件
即時使用者界面 | Laravel Echo, Pusher | 第三方套件或直接使用 WebSocket
建立測試資料 | Seeder 類別, Model Factories, Faker | 手動建立測試資料
任務排程 | Laravel Task Scheduler | 腳本或第三方套件
資料庫 | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝 回到目錄](#內容)

### **遵循 Laravel 命名規範**

遵守 [PSR 標準 (英語)](http://www.php-fig.org/psr/psr-2/)。

另外，請遵守 Laravel 社群認可的命名規範:

東西 | 命名方式 | Good | Bad
------------ | ------------- | ------------- | -------------
Controller | 單數 | ArticleController | ~~ArticlesController~~
Route - 路由 | 複數 | articles/1 | ~~article/1~~
Named Route - 路由命名| 使用點標記的 snake_case | users.show_active | ~~users.show-active, show-active-users~~
Model | 單數 | User | ~~Users~~
hasOne 或 belongsTo 關聯 | 單數 | articleComment | ~~articleComments, article_comment~~
所有其他關聯 | 複數 | articleComments | ~~articleComment, article_comments~~
資料表 | 複數 | article_comments | ~~article_comment, articleComments~~
Pivot Table 透視表 | 以字母順序排列的單數 Model 名稱 | article_user | ~~user_article, articles_users~~
資料表欄位| 使用 snake_case，並且不包含 Model 名稱 | meta_title | ~~MetaTitle; article_meta_title~~
Model 屬性 | snake_case | $model->created_at | ~~$model->createdAt~~
Foreign Key - 外鍵 | 以單數 Model 名稱後方加上 _id | article_id | ~~ArticleId, id_article, articles_id~~
Primary Key - 主鍵 | - | id | ~~custom_id~~
Migration | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
方法 | camelCase | getAll | ~~get_all~~
Resource Controller 中的方法 | [table](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
測試類別中的方法| camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
變數 | camelCase | $articlesWithAuthor | ~~$articles_with_author~~
Collection | 描述性名稱、複數 | $activeUsers = User::active()->get() | ~~$active, $data~~
物件 | 秒屬性名稱、單數 | $activeUser = User::active()->first() | ~~$users, $obj~~
設定檔與語系檔的索引鍵 | snake_case | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
View | kebab-case | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
設定檔 | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Contract (界面) | 形容詞或名詞 | AuthenticationInterface | ~~Authenticatable, IAuthentication~~
Trait | 形容詞 | Notifiable | ~~NotificationTrait~~

[🔝 回到目錄](#內容)

### **盡可能使用簡短且可讀性更好的語法**

Bad:

```php
$request->session()->get('cart');
$request->input('name');
```

Good:

```php
session('cart');
$request->name;
```

更多範例:

一般語法 | Good
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

[🔝 回到目錄](#內容)

### **使用 IoC Container 或 Facade 而不是直接 new Class**

new Class 語法增加物件間的耦合度，且會讓測試更複雜。應使用 IoC Container 或 Facade 來代替。

Bad:

```php
$user = new User;
$user->create($request->validated());
```

Good:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 回到目錄](#內容)

### **不要直接從 `.env` 檔案取得資料**

請改而將資料傳至設定檔並使用 `config()` helper 函式來在應用程式中使用資料。

Bad:

```php
$apiKey = env('API_KEY');
```

Good:

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[🔝 回到目錄](#內容)

### **以標準格式來儲存日期時間，並以 Accesor 或 Mutator 來修改日期格式**

Bad:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

Good:

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

[🔝 回到目錄](#內容)

### **其他優良實踐**

絕對不要在路由檔案中撰寫任何邏輯。

在 Blade 樣板中避免使用原始 PHP。

[🔝 回到目錄](#內容)


