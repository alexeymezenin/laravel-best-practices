![Laravel best practices](/images/logo-english.png?raw=true)

You might also want to check out the [real-world Laravel example application](https://github.com/alexeymezenin/laravel-realworld-example-app)

## <p dir="rtl">ترجمے:</p>

[Nederlands](https://github.com/Protoqol/Beste-Laravel-Praktijken) (by [Protoqol](https://github.com/Protoqol))

[한국어](https://github.com/xotrs/laravel-best-practices) (by [cherrypick](https://github.com/xotrs))

[日本語](japanese.md) (by [2bo](https://github.com/2bo))

[漢語](chinese.md) (by [xiaoyi](https://github.com/Shiloh520))

[ภาษาไทย](thai.md) (by [kongvut sangkla](https://github.com/kongvut))

[বাংলা](bangla.md) (by [Anowar Hossain](https://github.com/AnowarCST))

[فارسی](persian.md) (by [amirhossein baghaie](https://github.com/amirbagh75))

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

[Azərbaycanca](https://github.com/Maharramoff/laravel-best-practices-az) (by [Maharramoff](https://github.com/Maharramoff))

[العربية](arabic.md) (by [ahmedsaoud31](https://github.com/ahmedsaoud31))

[اردو](urdu.md) (by [RizwanAshraf1](https://github.com/RizwanAshraf1))
 
## <p dir="rtl">انڈیکس</p>

[<p dir="rtl">واحد ذمہ داری کا اصول</p>](#1)

[<p dir="rtl">بڑے models ، چھوٹے controllers!</p>](#2)

[<p dir="rtl">توثیق</p>](#3)

[<p dir="rtl">کاروباری منطق service class میں ہونی چاہیے۔</p>](#4)

[<p dir="rtl">اپنے آپ کو نہ دہرائیں (DRY)</p>](#5)
،
[<p dir="rtl">Query Builderاور raw SQL queries پر Eloquent استعمال کرنے کو  ترجیح دیں ۔ arrays پر collections کو ترجیح دیں۔</p>](#6)

[<p dir="rtl">بڑے پیمانے پر تفویض</p>](#7)

[<p dir="rtl">Blade templates میں queries نہ  چلایئں اور eager loading کا استعمال کریں (N + 1 مسئلہ)</p>](#8)

[<p dir="rtl">اپنے کوڈ پر تبصرہ کریں ، لیکن تبصرے پر وضاحتی method اور variables ناموں کو ترجیح دیں</p>](#9)

[<p dir="rtl">Blade templates میں JS اور CSS نہ ڈالیں اور PHP Classes میں کوئی HTML نہ ڈالیں۔</p>](#10)

[<p dir="rtl">کوڈ میں ٹیکسٹ کی بجائے config، لینگویج فائلز اور constants استعمال کریں۔</p>](#11)

[<p dir="rtl">Laravel کے معیاری ٹولز کا استعمال کریں جو کمیونٹی نے قبول کیے ہیں۔</p>](#12)

[<p dir="rtl">Laravel کےاپنے نام رکھنے کے  طریقوں پر عمل کریں .</p>](#13)

[<p dir="rtl">جہاں ممکن ہو مختصر اور زیادہ پڑھنے کے قابل syntax کا استعمال کریں۔</p>](#14)

[<p dir="rtl">نئی Class کے بجائے IoC کنٹینر یا facades استعمال کریں۔</p>](#15)

[<p dir="rtl">`.env` فائل سے غلطہ راست ڈیٹا حاصل نہ کریں۔</p>](#16)

[<p dir="rtl">تاریخوں کو معیاری شکل میں محفوظ کریں۔ ڈیٹ فارمیٹ میں ترمیم کرنے کے لیے accessors اور mutators کا استعمال کریں۔</p>](#17)

[<p dir="rtl">دوسرے اچھے طریقے۔</p>](#18)
### <p dir="rtl">1</p>
### **<p dir="rtl">واحد ذمہ داری کا اصول</p>**

<p dir="rtl">ایک class اور ایک method کی صرف ایک ذمہ داری ہونی چاہیے۔</p>

<p dir="rtl">❌ غلط طریقہ:</p>

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

<p dir="rtl">✔️ درست طریقہ:</p>

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

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)

### <p dir="rtl">2</p>
### **<p dir="rtl">بڑے models ، چھوٹے controllers!</p>**

<p dir="rtl">اگر آپ  Query Builder یا raw SQL queries استعمال کر رہے ہیں تو تمام DB سے متعلقہ منطق کو Eloquent models  یا Repository کی classes میں ڈالیں۔</p>
<p dir="rtl">❌ غلط طریقہ:</p>

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

<p dir="rtl">✔️ درست طریقہ:</p>

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

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">3</p>
### **<p dir="rtl">توثیق</p>**

<p dir="rtl">توثیق کو controllers سے Request classes میں منتقل کریں۔</p>
<p dir="rtl">❌ غلط طریقہ:</p>

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

<p dir="rtl">✔️ درست طریقہ:</p>

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

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">4</p>
### **<p dir="rtl">کاروباری منطق service class میں ہونی چاہیے۔</p>**

<p dir="rtl">ایک controller کی صرف ایک ذمہ داری ہونی چاہیے ، لہذا کاروباری منطق کو controller سے service classes  میں منتقل کریں۔</p>

<p dir="rtl">❌ غلط طریقہ:</p>

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

<p dir="rtl">✔️ درست طریقہ:</p>

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

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">5</p>
### **<p dir="rtl">اپنے آپ کو نہ دہرائیں (DRY)</p>**

<p dir="rtl">جب ممکن ہو تو کوڈ کو دوبارہ استعمال کریں۔ SRP آپ کو نقل سے بچنے میں مدد دے رہا ہے۔ نیز ، Blade templates کو دوبارہ استعمال کریں </p>

<p dir="rtl"> Eloquent scopesوغیرہ استعمال کریں۔</p>
 
<p dir="rtl">❌ غلط طریقہ:</p>

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

<p dir="rtl">✔️ درست طریقہ:</p>

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

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">6</p>
### **<p dir="rtl">Query Builderاور raw SQL queries پر Eloquentاستعمال کرنے کو  ترجیح دیں ۔ arrays پر collections کو ترجیح دیں۔</p>**

<p dir="rtl"> Eloquent آپ کو پڑھنے کے قابل اور دیکھ بھال کے قابل کوڈ لکھنے کی اجازت دیتا ہے۔ نیز ، Eloquent کے پاس بلٹ ان ٹولز ہیں جیسے soft deletes, events, scopes وغیرہ۔</p> 

<p dir="rtl">❌ غلط طریقہ:</p>

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

<p dir="rtl">✔️ درست طریقہ:</p>

```php
Article::has('user.profile')->verified()->latest()->get();
```

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">7</p>
### **<p dir="rtl">بڑے پیمانے پر تفویض</p>**

<p dir="rtl">❌ غلط طریقہ:</p>

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

<p dir="rtl">✔️ درست طریقہ:</p>

```php
$category->article()->create($request->validated());
```

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">8</p>
### **<p dir="rtl">Blade templates میں queries نہ  چلایئں اور eager loading کا استعمال کریں (N + 1 مسئلہ)</p>**

<p dir="rtl">❌ غلط طریقہ:</p>
<p dir="rtl">غلط (100 صارفین کے لیے ، 101 DB queries استعمال ہوں گی ):</p>

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

<p dir="rtl">✔️ درست طریقہ:</p>
<p dir="rtl">غلط (100 صارفین کے لیے ، 2 DB queries استعمال ہوں گی ):</p>

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">9</p>
### **<p dir="rtl">اپنے کوڈ پر تبصرہ کریں ، لیکن تبصرے پر وضاحتی method اور variables ناموں کو ترجیح دیں</p>**

<p dir="rtl">❌ غلط طریقہ:</p>

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

<p dir="rtl">بہتر طریقہ۔:</p>

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

<p dir="rtl">✔️ درست طریقہ:</p>

```php
if ($this->hasJoins())
```

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">10</p>
### **<p dir="rtl">Blade templates میں JS اور CSS نہ ڈالیں اور PHP Classes میں کوئی HTML نہ ڈالیں۔</p>**

<p dir="rtl">❌ غلط طریقہ:</p>

```php
let article = `{{ json_encode($article) }}`;
```

<p dir="rtl">✔️ بہتر طریقہ۔:</p>

```php
<input id="article" type="hidden" value='@json($article)'>

Or

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

کی Javascript فائل میں۔:

```javascript
let article = $('#article').val();
```

<p dir="rtl">ڈیٹا منتقل کرنے کے لیے خصوصی PHP to JS package  کا استعمال کرنا ہے۔</p>


[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">11</p>
### **<p dir="rtl">کوڈ میں ٹیکسٹ کی بجائے config، لینگویج فائلز اور constants استعمال کریں۔</p>**

<p dir="rtl">❌ غلط طریقہ:</p>

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

<p dir="rtl">✔️ درست طریقہ:</p>

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">12</p>
### **<p dir="rtl">Laravel کے معیاری ٹولز کا استعمال کریں جو کمیونٹی نے قبول کیے ہیں۔</p>**

<p dir="rtl">تھرڈ پارٹی پیکجز اور ٹولز استعمال کرنے کے بجائے بلٹ ان Laravel functionality اور کمیونٹی پیکجز استعمال کرنے کو ترجیح دیں۔ کوئی بھی ڈویلپر جو مستقبل میں آپ کی ایپ کے ساتھ کام کرے گا اسے نئے ٹولز سیکھنے کی ضرورت ہوگی۔ نیز ، جب آپ تھرڈ پارٹی پیکیج یا ٹول استعمال کر رہے ہیں تو Laravel کمیونٹی سے مدد حاصل کرنے کے امکانات نمایاں طور پر کم ہیں۔ اپنے کلائنٹ کو اس کی ادائیگی کرنے پر مجبور نہ کریں ۔
 </p>

کام | معیاری ٹولز  |  تھرڈ پارٹی ٹولز
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

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">13</p>
### **<p dir="rtl">Laravel کےاپنے نام رکھنے کے  طریقوں پر عمل کریں</p>**
<p dir="rtl">پیروی <a href="http://www.php-fig.org/psr/psr-2">PSR  معیارات </a></p>
 
 <p dir="rtl">نیز ، Laravel کمیونٹی کے ذریعہ قبول کردہ نام رکھنے کے طریقوں  پر عمل کریں:</p>

کیا  | کیسے | درست طریقہ | غلط طریقہ
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

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">14</p>
### **<p dir="rtl">جہاں ممکن ہو مختصر اور زیادہ پڑھنے کے قابل syntax کا استعمال کریں۔</p>**

<p dir="rtl">❌ غلط طریقہ:</p>

```php
$request->session()->get('cart');
$request->input('name');
```

<p dir="rtl">✔️ درست طریقہ:</p>

```php
session('cart');
$request->name;
```

<p dir="rtl">مزید مثالیں:</p>

عام syntax | چھوٹا syntax اور زیادہ پڑھنے کے قابل 
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

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">15</p>
### **<p dir="rtl">نئی Class کے بجائے IoC کنٹینر یا facades استعمال کریں۔</p>**

<p dir="rtl">new Class syntax کے درمیان سخت جوڑا coupling ہے اور testing کو پیچیدہ بناتا ہے۔ اس کے بجائے IoC container یا facades استعمال کریں۔</p>

<p dir="rtl">❌ غلط طریقہ:</p>

```php
$user = new User;
$user->create($request->validated());
```

<p dir="rtl">✔️ درست طریقہ:</p>

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">16</p>
### **<p dir="rtl">`.env` فائل سے غلطہ راست ڈیٹا حاصل نہ کریں۔</p>**

<p dir="rtl">config فائلوں کو ڈیٹا منتقل کریں اور پھر ایپلیکیشن میں ڈیٹا استعمال کرنے کے لیے `()config` ہیلپر فنکشن استعمال کریں۔</p>

<p dir="rtl">❌ غلط طریقہ:</p>

```php
$apiKey = env('API_KEY');
```

<p dir="rtl">✔️ درست طریقہ:</p>

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">17</p>
### **<p dir="rtl">تاریخوں کو معیاری شکل میں محفوظ کریں۔ ڈیٹ فارمیٹ میں ترمیم کرنے کے لیے accessors اور mutators کا استعمال کریں۔</p>**

<p dir="rtl">❌ غلط طریقہ:</p>

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

<p dir="rtl">✔️ درست طریقہ:</p>

```php
// Model
protected $dates = ['ordered_at', 'created_at', 'updated_at'];
public function getSomeDateAttribute($date)
{
    return $date->format('m-d');
}

// ملف العرض
{{ $object->ordered_at->toDateString() }}
{{ $object->ordered_at->some_date }}
```

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
### <p dir="rtl">18</p>
### **<p dir="rtl">دوسرے اچھے طریقے۔</p>**

<p dir="rtl">routes کی فائلوں میں کبھی بھی کوئی منطق نہ ڈالیں۔</p>

<p dir="rtl">Blade templates میں vanilla PHP کا استعمال کم سے کم کریں۔</p>

[<p dir="rtl">🔝 انڈیکس پر واپس جائیں</p>](#انڈیکس)
