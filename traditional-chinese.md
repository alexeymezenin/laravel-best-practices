![Laravel best practices](/images/logo-chinese.png?raw=true)

多國語言列表:

[Nederlands](https://github.com/Protoqol/Beste-Laravel-Praktijken) (by [Protoqol](https://github.com/Protoqol))

[한국어](https://github.com/xotrs/laravel-best-practices) (by [cherrypick](https://github.com/xotrs))

[Українська](ukrainian.md) (by [Tenevyk](https://github.com/tenevyk))

[Русский](russian.md)

[فارسی](persian.md) (by [amirhossein baghaie](https://github.com/amirbagh75))

[Português](https://github.com/jonaselan/laravel-best-practices) (by [jonaselan](https://github.com/jonaselan))

[Tiếng Việt](https://chungnguyen.xyz/posts/code-laravel-lam-sao-cho-chuan) (by [Chung Nguyễn](https://github.com/nguyentranchung))

[Español](spanish.md) (by [César Escudero](https://github.com/cedaesca))

[Français](french.md) (by [Mikayil S.](https://github.com/mikayilsrt))

[Polski](https://github.com/maciejjeziorski/laravel-best-practices-pl) (by [Maciej Jeziorski](https://github.com/maciejjeziorski))

[Türkçe](turkish.md) (by [Burak](https://github.com/ikidnapmyself))

[Deutsch](german.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Italiana](italian.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[العربية](arabic.md) (by [ahmedsaoud31](https://github.com/ahmedsaoud31))

這並非laravel官方強制要求的規範，而是我們在日常開發過程中遇到的一些容易忽視的優秀實作方式。

## 內容

[單一職責原則](#單一職責原則)

[保持控制器的簡潔](#保持控制器的簡潔)

[使用自定義Request類別來進行驗證](#使用自定義Request類別來進行驗證)

[商業邏輯程式碼要放到服務層中](#商業邏輯程式碼要放到服務層中)

[DRY原則 不要重覆自己](#DRY原則-不要重覆自己)

[使用ORM而不是純sql語句，使用集合而不是陣列](#使用ORM而不是純sql語句使用集合而不是陣列)

[集中處理資料](#集中處理資料)

[不要在模板中查詢，盡量使用惰性加載](#不要在模板中查詢盡量使用惰性加載)

[註釋你的程式碼，但是更優雅的做法是使用描述性的語言來編寫你的程式碼](#註釋你的程式碼但是更優雅的做法是使用描述性的語言來編寫你的程式碼)

[不要把 JS 和 CSS 放到 Blade 模板中，也不要把任何 HTML 程式碼放到 PHP 程式碼裡](#不要把-JS-和-CSS-放到-Blade-模板中也不要把任何-HTML-程式碼放到-PHP-程式碼裡)

[在程式碼中使用配置、語言包和常量，而不是使用寫死的方式](#在程式碼中使用配置語言包和常量而不是使用寫死的方式)

[使用社群認可的標準Laravel工具](#使用社群認可的標準Laravel工具)

[遵循laravel命名規範](#遵循laravel命名規範)

[盡可能使用簡短且可讀性更好的語法](#盡可能使用簡短且可讀性更好的語法)

[使用IOC容器來創建實例 而不是直接new一個實例](#使用IOC容器來創建實例-而不是直接new一個實例)

[避免直接從 `.env` 文件裡獲取資料](#避免直接從-env-文件裡獲取資料)

[使用標準格式來儲存日期，用訪問器和修改器來修改日期格式](#使用標準格式來儲存日期用訪問器和修改器來修改日期格式)

[其他的好建議](#其他的一些好建議)

### **單一職責原則**

一個類別和一個方法應該只有一個責任。

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

更優的寫法:

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

[🔝 返回目錄](#內容)

### **保持控制器的簡潔**

如果您使用的是查詢生成器或原始SQL查詢，請將所有與資料庫相關的邏輯放入Eloquent模型或Repository類別中。

例如:

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

更優的寫法:

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

[🔝 返回目錄](#內容)

### **使用自定義Request類別來進行驗證**

把驗證規則放到 Request 類別中.

例子:

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

更優的寫法:

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

[🔝 返回目錄](#內容)

### **商業邏輯程式碼要放到服務層中**

控制器必須遵循單一職責原則，因此最好將商業邏輯程式碼從控制器移動到服務層中。

例子:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }

    ....
}
```

更優的寫法:

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

[🔝 返回目錄](#內容)

### **DRY原則 不要重覆自己**

盡可能重用程式碼，SRP可以幫助您避免重覆造輪子。 此外盡量重覆使用Blade模板，使用Eloquent的 scopes 方法來實作程式碼。

例子:

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

更優的寫法:

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

[🔝 返回目錄](#內容)

### **使用ORM而不是純sql語句，使用集合而不是陣列**

使用Eloquent可以幫您編寫可讀和可維護的程式碼。 此外Eloquent還有非常優雅的內建工具，如軟刪除，事件，範圍等。

例子:

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

更優的寫法:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 返回目錄](#內容)

### **集中處理資料**

例子:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

更優的寫法:

```php
$category->article()->create($request->validated());
```

[🔝 返回目錄](#內容)

### **不要在模板中查詢，盡量使用惰性加載**

例子 (對於100個用戶，將執行101次DB查詢):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

更優的寫法 (對於100個用戶，使用以下寫法只需執行2次DB查詢):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 返回目錄](#內容)

### **註釋你的程式碼，但是更優雅的做法是使用描述性的語言來編寫你的程式碼**

例子:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

加上註釋:

```php
// 確定是否有任何連接
if (count((array) $builder->getQuery()->joins) > 0)
```

更優的寫法:

```php
if ($this->hasJoins())
```

[🔝 返回目錄](#內容)

### **不要把 JS 和 CSS 放到 Blade 模板中，也不要把任何 HTML 程式碼放到 PHP 程式碼裡**

例子:

```php
let article = `{{ json_encode($article) }}`;
```

更好的寫法:

```php
<input id="article" type="hidden" value='@json($article)'>

Or

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

在Javascript文件中加上:

```javascript
let article = $('#article').val();
```

當然最好的辦法還是使用專業的PHP的JS包傳輸資料。

[🔝 返回目錄](#內容)

### **在程式碼中使用配置、語言包和常量，而不是使用寫死的方式**

例子:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

更優的寫法:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 返回目錄](#內容)

### **使用社群認可的標準Laravel工具**


強力推薦使用內建的Laravel功能和擴展包，而不是使用第三方的擴展包和工具。
如果你的項目被其他開發人員接手了，他們將不得不重新學習這些第三方工具的使用教程。
此外，當您使用第三方擴展包或工具時，你很難從Laravel社群獲得什麽幫助。 不要讓你的客戶為額外的問題付錢。

想要實作的功能 | 標準工具 | 第三方工具
------------ | ------------- | -------------
權限 | Policies | Entrust, Sentinel 或者其他擴展包
資源編譯工具| Laravel Mix | Grunt, Gulp, 或者其他第三方包
開發環境| Homestead | Docker
部署 | Laravel Forge | Deployer 或者其他解決方案
自動化測試 | PHPUnit, Mockery | Phpspec
頁面預覽測試 | Laravel Dusk | Codeception
DB操縱 | Eloquent | SQL, Doctrine
模板 | Blade | Twig
資料操縱 | Laravel集合 | 陣列
表單驗證| Request classes | 他第三方包,甚至在控制器中做驗證
權限 | Built-in | 他第三方包或者你自己解決
API身份驗證 | Laravel Passport | 第三方的JWT或者 OAuth 擴展包
創建 API | Built-in | Dingo API 或者類似的擴展包
創建資料庫結構 | Migrations | 直接用 DB 語句創建
本土化 | Built-in |第三方包
實時消息隊列 | Laravel Echo, Pusher | 使用第三方包或者直接使用WebSockets
創建測試資料| Seeder classes, Model Factories, Faker | 手動創建測試資料
任務調度| Laravel Task Scheduler | 腳本和第三方包
資料庫 | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝 返回目錄](#內容)

### **遵循laravel命名規範**

來源 [PSR standards](http://www.php-fig.org/psr/psr-2/).

另外，遵循Laravel社群認可的命名規範：

對象 | 規則 | 更優的寫法 | 應避免的寫法
------------ | ------------- | ------------- | -------------
控制器 | 單數 | ArticleController | ~~ArticlesController~~
路由 | 覆數 | articles/1 | ~~article/1~~
路由命名| 帶點符號的蛇形命名 | users.show_active | ~~users.show-active, show-active-users~~
模型 | 單數 | User | ~~Users~~
hasOne或belongsTo關系 | 單數 | articleComment | ~~articleComments, article_comment~~
所有其他關系 | 覆數 | articleComments | ~~articleComment, article_comments~~
表單 | 覆數 | article_comments | ~~article_comment, articleComments~~
透視表| 按字母順序排列模型 | article_user | ~~user_article, articles_users~~
資料表字段| 使用蛇形並且不要帶表名 | meta_title | ~~MetaTitle; article_meta_title~~
模型參數 | 蛇形命名 | $model->created_at | ~~$model->createdAt~~
外鍵 | 帶有_id後綴的單數模型名稱 | article_id | ~~ArticleId, id_article, articles_id~~
主鍵 | - | id | ~~custom_id~~
遷移 | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
方法 | 駝峰命名 | getAll | ~~get_all~~
資源控制器 | [table](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
測試類別| 駝峰命名 | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
變量 | 駝峰命名 | $articlesWithAuthor | ~~$articles_with_author~~
集合 | 描述性的, 覆數的 | $activeUsers = User::active()->get() | ~~$active, $data~~
對象 | 描述性的, 單數的 | $activeUser = User::active()->first() | ~~$users, $obj~~
配置和語言文件索引 | 蛇形命名 | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
視圖 | 短橫線命名 | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
配置 | 蛇形命名 | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
內容 (interface) | 形容詞或名詞 | Authenticatable | ~~AuthenticationInterface, IAuthentication~~
Trait | 使用形容詞 | Notifiable | ~~NotificationTrait~~

[🔝 返回目錄](#內容)

### **盡可能使用簡短且可讀性更好的語法**

例子:

```php
$request->session()->get('cart');
$request->input('name');
```

更優的寫法:

```php
session('cart');
$request->name;
```

更多示例:

常規寫法 | 更優雅的寫法
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

[🔝 返回目錄](#內容)

### **使用IOC容器來創建實例 而不是直接new一個實例**

創建新的類別會讓類別之間的更加耦合，使得測試越發複雜。請改用IoC容器或注入來實作。

例子:

```php
$user = new User;
$user->create($request->validated());
```

更優的寫法:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 返回目錄](#內容)

### **避免直接從 `.env` 文件裡獲取資料**

將資料傳遞給配置文件，然後使用`config（）`輔助函數來調用資料

例子:

```php
$apiKey = env('API_KEY');
```

更優的寫法:

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[🔝 返回目錄](#內容)

### **使用標準格式來儲存日期，用訪問器和修改器來修改日期格式**

例子:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

更優的寫法:

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

[🔝 返回目錄](#內容)

### **其他的一些好建議**

永遠不要在路由文件中放任何的邏輯程式碼。

盡量不要在Blade模板中寫原始 PHP 程式碼。

[🔝 返回目錄](#內容)


