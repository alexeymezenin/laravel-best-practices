![روش های روش قابل قبول توسعه پروژه های مبتنی بر فریم ورک لاراول](/images/logo-persian.png?raw=true)

<div dir="rtl">

ترجمه ها:

[한국어](https://github.com/xotrs/laravel-best-practices) (by [cherrypick](https://github.com/xotrs))

[Українська](ukrainian.md) (by [Tenevyk](https://github.com/tenevyk))

[روسی](russian.md)

[فارسی](persian.md)

[پرتقالی](https://github.com/jonaselan/laravel-best-practices) (by [jonaselan](https://github.com/jonaselan))

[Tiếng Việt](https://chungnguyen.xyz/posts/code-laravel-lam-sao-cho-chuan) (by [Chung Nguyễn](https://github.com/nguyentranchung))

[Türkçe](turkish.md) (by [Burak](https://github.com/ikidnapmyself))

[Deutsche](german.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Italiana](italian.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

این مستندات درباره سازگاری لاراول با اصول SOLID یا Design Pattern ها و ... نیست. اینجا شما روش های اصولی توسعه پروژه های مبتنی بر لاراول رو پیدا میکنید که معمولا داخل پروژه ها در نظر گرفته نمیشوند.

## فهرست مطالب

- [اصل تک وظیفه ای بودن](#اصل-تک-وظیفه-ای-بودن)

- [مدل های بزرگ،‌ کنترلرهای کوچک!](#مدل-های-بزرگ-کنترلرهای-کوچک)

- [اعتبارسنجی](#اعتبارسنجی)

- [منطق برنامه باید در service class باشد.](#منطق-برنامه-باید-در-service-class-باشد)

- [اصل DRY یا خودت را تکرار نکن!](#اصل-dry-یا-خودت-را-تکرار-نکن)

- [به جای استفاده از Query Builder و raw SQL queries از Eloquent ORM استفاده کنید. همچنین به جای استفاده از Arrays از Collections استفاده کنید.](#به-جای-استفاده-از-query-builder-و-raw-sql-queries-از-eloquent-orm-استفاده-کنید-همچنین-به-جای-استفاده-از-arrays-از-collections-استفاده-کنید)

- [ایجاد یک مدل](#ایجاد-یک-مدل)

- [به جای نوشتن query ها در blade از eager loading استفاده کنید. (مسئله N+1)](#به-جای-نوشتن-query-ها-در-blade-از-eager-loading-استفاده-کنید-مسئله-n1)

- [کامنت گذاری بکنید، ولی اسامی متدها یا متغیرها را توصیفی و معنادار در نظر بگیرید. ](#کامنت-گذاری-بکنید-ولی-اسامی-متدها-یا-متغیرها-را-توصیفی-و-معنادار-در-نظر-بگیرید)

- [در تمپلیت های Blade از js و css استفاده نکنید و هیچگونه کد HTML ای را در class های PHP استفاده نکنید.](#در-تمپلیت-های-blade-از-js-و-css-استفاده-نکنید-و-هیچگونه-کد-html-ای-را-در-class-های-php-استفاده-نکنید)

- [به جای استفاده مستقیم از متن ها در کد، از فایل های config و languages استفاده کنید!](#به-جای-استفاده-مستقیم-از-متن-ها-در-کد-از-فایل-های-config-و-langugeus-استفاده-کنید)

- [از ابزارهای استاندارد لاراول که مورد تایید جامعه کاربری آن میباشد، استفاده کنید.](#از-ابزارهای-استاندارد-لاراول-که-مورد-تایید-جامعه-کاربری-آن-میباشد-استفاده-کنید)

- [از قرارداد های لاراول برای نامگذاری ها استفاده کنید.](#از-قرارداد-های-لاراول-برای-نامگذاری-ها-استفاده-کنید)

- [تا حد ممکن در کدتان، از Syntax های معنادار و کوتاه استفاده کنید.](#تا-حد-ممکن-در-کدتان-از-syntax-های-معنادار-و-کوتاه-استفاده-کنید)

- [به جای ایجاد یک object با new، از IoC container و facades استفاده کنید.](#به-جای-ایجاد-یک-object-با-new-از-ioc-container-و-facades-استفاده-کنید)

- [از فایل .env هیچ وقت مستقیم داده ای دریافت نکنید.](#از-فایل-env-هیچ-وقت-مستقیم-داده-ای-دریافت-نکنید)

- [تاریخ و زمان را در قالب استاندارد ذخیره کنید. از Accessors & Mutators ها برای دستکاری در نمایش تاریخ و زمان استفاده کنید.](#تاریخ-و-زمان-را-در-قالب-استاندارد-ذخیره-کنید-از-accessors--mutators-ها-برای-دستکاری-در-نمایش-تاریخ-و-زمان-استفاده-کنید)

- [دیگر روش ها](#دیگر-قواعد-توسعه-روش-قابل-قبول-بدون-فهرست)
</div>

<div dir="rtl">

### **اصل تک وظیفه ای بودن**

هر class و هر methode باید یک وظیفه داشته باشد.

❌ روش اشتباه:

</div>

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
<div dir="rtl">

✔️ روش قابل قبول:

</div>

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
<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **مدل های بزرگ،‌ کنترلرهای کوچک!**

اگر از Query Builder یا raw SQL queries استفاده میکنید، تمام منطق پایگاه داده را در model ها یا Repository classes قرار بدهید.

❌ روش اشتباه:

</div>

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
<div dir="rtl">

✔️ روش قابل قبول:

</div>

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
<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **اعتبارسنجی**

اعتبارسنجی ها را در Request classes انجام دهید نه در controllers.

❌ روش اشتباه:

</div>

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
<div dir="rtl">

✔️ روش قابل قبول:

</div>

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
<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **منطق برنامه باید در service class باشد**

هر کنترلر باید یک وظیفه داشته باشد، بنابراین منطق برنامه را در service classes بنویسید.

❌ روش اشتباه:

</div>

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```
<div dir="rtl">

✔️ روش قابل قبول:

</div>

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
<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **اصل DRY یا خودت را تکرار نکن!**

تا حد ممکن از کد ها بازاستفاده کنید. تک وظیفه ای شدن به شما کمک میکند تا کار تکراری نکنید. همچنین در Blade template حتما این اصل را رعایت کنید و در model ها از Eloquent scopes استفاده کنید و ... .

❌ روش اشتباه:

</div>

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
<div dir="rtl">

✔️ روش قابل قبول:

</div>

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
<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **به جای استفاده از Query Builder و raw SQL queries از Eloquent ORM استفاده کنید. همچنین به جای استفاده از Arrays از Collections استفاده کنید.**

Eloquent به شما این قابلیت را میدهد که کدهای خوانا و قابل توسعه بنویسید. همچنین دارای ویژگی های داخلی کاربردی مثل soft deletes یا events  یا scopes و .. میباشد.

❌ روش اشتباه:

</div>

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
<div dir="rtl">

✔️ روش قابل قبول:

</div>

```php
Article::has('user.profile')->verified()->latest()->get();
```
<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **ایجاد یک مدل**

[منظور نویسنده از این بخش این میباشد که برای راحتی کار و کوتاه تر شدن کد، در html طوری مقادیر name هر input یا ... را نامگذاری کنید که با column های جدول مربوطه یکسان باشد تا laravel آن ها رو با یک کامند خیلی سریع مپ و ذخیره کند.]

❌ روش اشتباه:

</div>

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```
<div dir="rtl">

✔️ روش قابل قبول:

</div>

```php
$category->article()->create($request->all());
```
<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **به جای نوشتن query ها در blade از eager loading استفاده کنید. (مسئله N+1)**

❌ روش اشتباه (برای ۱۰۰ کاربر، ما ۱۰۱ کوئری را اجرا میکنیم!):

</div>

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```
<div dir="rtl">

✔️ روش قابل قبول (برای ۱۰۰ کاربر، ما فقط ۲ کوئری اجرا کردیم!):

</div>

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```
<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **کامنت گذاری بکنید، ولی اسامی متدها یا متغیرها را توصیفی و معنادار در نظر بگیرید.**

❌ روش اشتباه:

</div>

```php
if (count((array) $builder->getQuery()->joins) > 0)
```
<div dir="rtl">

❗️ قابل قبول:

</div>

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```
<div dir="rtl">

✔️ روش قابل قبول:

</div>

```php
if ($this->hasJoins())
```

<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **در تمپلیت های Blade از js و css استفاده نکنید و هیچگونه کد HTML ای را در class های PHP استفاده نکنید.**

❌ روش اشتباه:

</div>

```php
let article = `{{ json_encode($article) }}`;
```
<div dir="rtl">

❗️ قابل قبول:

</div>

```php
<input id="article" type="hidden" value='@json($article)'>

Or

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```
<div dir="rtl">

در فایل JavaScript:

</div>

```javascript
let article = $('#article').val();
```
<div dir="rtl">

بهترین راه استفاده از پکیج مخصوص انتقال داده از php به js میباشد.

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **به جای استفاده مستقیم از متن ها در کد، از فایل های config و langugeus استفاده کنید!**

❌ روش اشتباه:

</div>

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'مقاله شما ایجاد گردید!');
```
<div dir="rtl">

✔️ روش قابل قبول:

</div>

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```
<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **از ابزارهای استاندارد لاراول که مورد تایید جامعه کاربری آن میباشد، استفاده کنید.**

به جای استفاده از ابزارها و پکیج های غیررسمی لاراول از ابزارها و قابلیت های داخلی و رسمی لاراول استفاده کنید. هر توسعه دهنده [لاراولی] ای که در آینده بخواهد با کدهای شما کار کند، باید ابزارهای جدید یاد بگیرد. همچنین اگر شما از ابزارهای غیررسمی استفاده کنید، شانس دریافت کمک از جامعه کاربری لاراول به طرز قابل توجهی  کمتر میشود. این هزینه را به گردن مشتریان خود نیندازید! [منظور نویسنده این هست که تو کارهای بزرگ و مهمتون از ابزارهای جدید و پراکنده استفاده نکنید. همیشه سعی کنید از ابزارهای داخلی و یا پکیج های رسمی لاراول استفاده کنید مگر این که چاره دیگری نباشد! شما با پراکنده کردن ابزارها هزینه فنی/مالی توسعه محصول را برای آینده زیادتر میکنید!]

</div>

نیاز | ابزارهای رسمی لاراول | ابزارهای غیررسمی لاراول
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

<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **از قرارداد های لاراول برای نامگذاری ها استفاده کنید**

از استاندارد های php که به [PSR](http://www.php-fig.org/psr/psr-2/) معروف است استفاده کنید.
 
همچنین روش های نامگذاری مورد قبول جامعه کاربری لاراول:

</div>

بخش مربوطه | قاعده اسم گذاری | ✔️ روش قابل قبول | ❌ روش اشتباه
------------ | ------------- | ------------- | -------------
Controller | اسامی مفرد | ArticleController | ~~ArticlesController~~
Route | اسامی جمع | articles/1 | ~~article/1~~
Named route | روش snake_case همراه با نقاط اتصال | users.show_active | ~~users.show-active, show-active-users~~
Model | اسامی مفرد | User | ~~Users~~
hasOne or belongsTo relationship | اسامی مفرد | articleComment | ~~articleComments, article_comment~~
All other relationships | اسامی جمع | articleComments | ~~articleComment, article_comments~~
Table | اسامی جمع | article_comments | ~~article_comment, articleComments~~
Pivot table | نام مدل ها با اسامی مفرد و ترتیب الفبایی | article_user | ~~user_article, articles_users~~
Table column | روش snake_case بدون اسم مدل| meta_title | ~~MetaTitle; article_meta_title~~
Model property | روش snake_case | $model->created_at | ~~$model->createdAt~~
Foreign key | اسامی مفرد model name with _id suffix | article_id | ~~ArticleId, id_article, articles_id~~
Primary key | - | id | ~~custom_id~~
Migration | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
Method | روش camelCase | getAll | ~~get_all~~
Method in resource controller | [table](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
Method in test class | روش camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
Variable | روش camelCase | $articlesWithAuthor | ~~$articles_with_author~~
Collection | توصیفی و اسامی جمع | $activeUsers = User::active()->get() | ~~$active, $data~~
Object | توصیفی و اسامی مفرد | $activeUser = User::active()->first() | ~~$users, $obj~~
Config and language files index | snake_case | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
View | kebab-case | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
Config | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Contract (interface) | صفت یا اسم | Authenticatable | ~~AuthenticationInterface, IAuthentication~~
Trait | صفت | Notifiable | ~~NotificationTrait~~

<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **تا حد ممکن در کدتان، از Syntax های معنادار و کوتاه استفاده کنید**

❌ روش اشتباه:

</div>

```php
$request->session()->get('cart');
$request->input('name');
```
<div dir="rtl">

✔️ روش قابل قبول:

</div>

```php
session('cart');
$request->name;
```
<div dir="rtl">

مثال های بیشتر:

</div>

سینتکس متداول | سینتکس کوتاه‌تر و خواناتر
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

<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **به جای ایجاد یک object با new، از IoC container و facades استفاده کنید.**

ایجاد یک object جدید با کلمه new یک اتصال کامل بین class ها و تست های پیچیده ایجاد میکند! بهتر است از IoC container یا facades استفاده کنید. [این بخش از مطلب تا جایی که من متوجه شدم مربوط به مباحث توسعه تست محور میباشد که من آشنایی زیادی ندارم ولی بعد از مطالعه و تکمیل اطلاعاتم این بخش را با توضیح تکمیلی، کامل خواهم کرد.]

❌ روش اشتباه:

</div

```php
$user = new User;
$user->create($request->all());
```
<div dir="rtl">

✔️ روش قابل قبول:

</div>

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->all());
```

<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **از فایل .env هیچ وقت مستقیم داده ای دریافت نکنید.**

اطلاعات موجود در .env را در صورت نیاز به استفاده، در فایل های config لود کنید و سپس با استفاده از helper function فایل های کانفیگ یعنی config() با آن در نرم افزار خود کار کنید.

❌ روش اشتباه:
</div>

```php
$apiKey = env('API_KEY');
```
<div dir="rtl">

✔️ روش قابل قبول:

</div>

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```
<div dir="rtl">

[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **تاریخ و زمان را در قالب استاندارد ذخیره کنید. از Accessors & Mutators ها برای دستکاری در نمایش تاریخ و زمان استفاده کنید.**

❌ روش اشتباه:

</div>

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```
<div dir="rtl">

✔️ روش قابل قبول:

</div>

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

<div dir="rtl">


[🔝 بازگشت به فهرست](#فهرست-مطالب)

### **دیگر قواعد توسعه روش قابل قبول (بدون فهرست)**

- در فایل های route خود هیچوقت منطق برنامه را قرار ندهید.

- تا حد ممکن از vanilla PHP در فایل های blade استفاده نکنید.

[🔝 بازگشت به فهرست](#فهرست-مطالب)

</div>
