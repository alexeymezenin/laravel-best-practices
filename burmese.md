![Laravel best practices](/images/logo-burmese.png?raw=true)

You might also want to check out the [real-world Laravel example application](https://github.com/alexeymezenin/laravel-realworld-example-app) and [Eloquent SQL reference](https://github.com/alexeymezenin/eloquent-sql-reference)

Translations:

[Nederlands](https://github.com/Protoqol/Beste-Laravel-Praktijken) (by [Protoqol](https://github.com/Protoqol))

[Indonesia](indonesia.md) (by [P0rguy](https://github.com/p0rguy), [Doni Ahmad](https://github.com/donyahmd))

[한국어](https://github.com/xotrs/laravel-best-practices) (by [cherrypick](https://github.com/xotrs))

[日本語](japanese.md) (by [2bo](https://github.com/2bo))

[简体中文](chinese.md) (by [xiaoyi](https://github.com/Shiloh520))

[繁體中文](traditional-chinese.md) (by [woeichern](https://github.com/woeichern))

[ภาษาไทย](thai.md) (by [kongvut sangkla](https://github.com/kongvut))

[বাংলা](bangla.md) (by [Anowar Hossain](https://github.com/AnowarCST))

[فارسی](persian.md) (by [amirhossein baghaie](https://github.com/ohmydevops))

[Português](https://github.com/jonaselan/laravel-best-practices) (by [jonaselan](https://github.com/jonaselan))

[Українська](ukrainian.md) (by [Tenevyk](https://github.com/tenevyk))

[Русский](russian.md)

[Tiếng Việt](https://chungnguyen.xyz/posts/code-laravel-lam-sao-cho-chuan) (by [Chung Nguyễn](https://github.com/nguyentranchung))

[Español](spanish.md) (by [César Escudero](https://github.com/cedaesca))

[Français](french.md) (by [Mikayil S.](https://github.com/mikayilsrt))

[Polski](polish.md) (by [Karol Pietruszka](https://github.com/pietrushek))

[Română](romanian.md) (by [als698](https://github.com/als698))

[Türkçe](turkish.md) (by [Burak](https://github.com/ikidnapmyself))

[Deutsch](german.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Italiana](italian.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Azərbaycanca](https://github.com/Maharramoff/laravel-best-practices-az) (by [Maharramoff](https://github.com/Maharramoff))

[العربية](arabic.md) (by [ahmedsaoud31](https://github.com/ahmedsaoud31))

[اردو](urdu.md) (by [RizwanAshraf1](https://github.com/RizwanAshraf1))

[မြန်မာဘာသာ](burmese.md) (by[Kaung Zay Yan](https://github.com/KaungZayY))

[![Laravel example app](/images/laravel-real-world-banner.png?raw=true)](https://github.com/alexeymezenin/laravel-realworld-example-app)

## Contents

[အလုပ်တစ်ခုပဲ တာဝန်ယူနိယာမ](#အလုပ်တစ်ခုပဲ-တာဝန်ယူနိယာမ)

[Method တစ်ခုက အလုပ်တစ်ခုပဲလုပ်သင့်ပါတယ်](#Method-တစ်ခုက-အလုပ်တစ်ခုပဲလုပ်သင့်ပါတယ်)

[Fat models, skinny controllers](#fat-models-skinny-controllers)

[Validation](#validation)

[Business logic တွေက Service class ထဲမှာပဲ ရှိသင့်တယ်](#business-logic-တွေက-Service-class-ထဲမှာပဲ-ရှိသင့်တယ်)

[ထပ်တစ်လဲလဲပြန်မရေးနဲ့ (DRY)](#ထပ်တစ်လဲလဲပြန်မရေးနဲ့-DRY)

[Query Builder နဲ့ raw SQL queries အစား Eloquent၊ arrays အစား collection ကိုပိုသုံးပါ](#query-builder-နဲ့-raw-sql-queries-အစား-eloquent-arrays-အစား-collection-ကိုပိုသုံးပါ)

[Mass assignment](#mass-assignment)

[Queries တွေကို Blade Templates တွေထဲမှာ Execute မလုပ်ပဲနဲ့ အဲတာအစား eager loading ကိုအသုံးပြုပါ။ (N + 1 problem)](#queries-တွေကို-blade-templates-တွေထဲမှာ-execute-မလုပ်ပဲနဲ့-အဲတာအစား-eager-loading-ကိုအသုံးပြုပါ-n--1-problem)

[Data-heavy tasks တွေအတွက် Chunk data ကိုသုံးပါ](#data-heavy-tasks-တွေအတွက်-chunk-data-ကိုသုံးပါ)

[Comment ရေးမဲ့ အစား method နဲ့ variable name တွေကို သေချာပေးခဲ့ပါ](#comment-ရေးမဲ့-အစား-method-နဲ့-variable-name-တွေကို-သေချာပေးခဲ့ပါ)

[JS နဲ့ CSS ကို blade templates ထဲကို မထည့်ပါနဲ့၊ PHP class တွေထဲမှာ HTML code တွေမထည့်ပါနဲ့](#js-နဲ့-css-ကို-blade-templates-ထဲကို-မထည့်ပါနဲ့-php-class-တွေထဲမှာ-html-code-တွေမထည့်ပါနဲ့)

[Code ထဲမှာ hard coded စာသားတွေ ထည့်မဲ့အစား config နဲ့ language files တွေကိုသုံးပါ](#code-ထဲမှာ-hard-coded-စာသားတွေ-ထည့်မဲ့အစား-config-နဲ့-language-files-တွေကိုသုံးပါ)

[Community က လက်ခံပြီး အသုံးပြုနေကျ standard laravel tools တွေကိုပဲသုံးပါ](#community-က-လက်ခံပြီး-အသုံးပြုနေကျ-standard-laravel-tools-တွေကိုပဲသုံးပါ)

[Laravel ရဲ့ အမည်ပေးပုံတွေကိုလိုက်နာပါ](#laravel-ရဲ့-အမည်ပေးပုံတွေကိုလိုက်နာပါ)

[Convention over configuration](#convention-over-configuration)

[တိုတိုနဲ့ ဖတ်ရလွယ်တဲ့ syntax ကိုတက်နိုင်သမျှသုံးပါ](#တိုတိုနဲ့-ဖတ်ရလွယ်တဲ့-syntax-ကိုတက်နိုင်သမျှသုံးပါ)

[new Class အစား loC / Service container တွေကိုသုံးပါ](#new-class-အစား-loC--Service-container-တွေကိုသုံးပါ)

[`.env` file ကနေ data ကိုတိုက်ရိုက်မယူပါနဲ့](#env-file-ကနေ-data-ကိုတိုက်ရိုက်မယူပါနဲ့)

[ရက်စွဲတွေကို standard format အတိုင်းသိမ်းပါ။ Date format တွေကို modify လုပ်ချင်ရင် accessors နဲ့ mutators ကိုသုံးပါ](#ရက်စွဲတွေကို-standard-format-အတိုင်းသိမ်းပါ-date-format-တွေကို-modify-လုပ်ချင်ရင်-accessors-နဲ့-mutators-ကိုသုံးပါ)

[DocBlocks တွေကိုမသုံးပါနဲ့](#docblocks-တွေကိုမသုံးပါနဲ့)

[တစ်ခြားအလေ့အကျင့်ကောင်းများ](#တစ်ခြားအလေ့အကျင့်ကောင်းများ)

### **အလုပ်တစ်ခုပဲ တာဝန်ယူနိယာမ**

Class တစ်ခုမှာ တာဝန်တစ်ခုပဲရှိသင့်ပါတယ်။

Bad:

```php
public function update(Request $request): string
{
    $validated = $request->validate([
        'title' => 'required|max:255',
        'events' => 'required|array:date,type'
    ]);

    foreach ($request->events as $event) {
        $date = $this->carbon->parse($event['date'])->toString();

        $this->logger->log('Update event ' . $date . ' :: ' . $);
    }

    $this->event->updateGeneralEvent($request->validated());

    return back();
}
```

Good:

```php
public function update(UpdateRequest $request): string
{
    $this->logService->logEvents($request->events);

    $this->event->updateGeneralEvent($request->validated());

    return back();
}
```

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **Method တစ်ခုက အလုပ်တစ်ခုပဲလုပ်သင့်ပါတယ်**

Function တစ်ခုက အလုပ်တစ်ခုကိုပဲ သေချာလုပ်သင့်ပါတယ်။

Bad:

```php
public function getFullNameAttribute(): string
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
public function getFullNameAttribute(): string
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

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **Fat models, skinny controllers**

DB logic တွေ အကုန်လုံးကို Eloquent Model ထဲကို ထည့်ပါ။

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

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **Validation**

Validation စစ်တာကို controller ထဲမှာ မစစ်ပဲ request class ထဲမှာစစ်ပါ။

Bad:

```php
public function store(Request $request)
{
    $request->validate([
        'title' => 'required|unique:posts|max:255',
        'body' => 'required',
        'publish_at' => 'nullable|date',
    ]);

    ...
}
```

Good:

```php
public function store(PostRequest $request)
{
    ...
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

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **Business logic တွေက Service class ထဲမှာပဲ ရှိသင့်တယ်**

Controller တစ်ခုက အလုပ်တစ်ခုပဲ လုပ်သင့်တယ်။ Business‌ logic တွေကို သပ်သပ် service class ထဲကိုရွှေ့ပါ။

Bad:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ...
}
```

Good:

```php
public function store(Request $request)
{
    $this->articleService->handleUploadedImage($request->file('image'));

    ...
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

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **ထပ်တစ်လဲလဲပြန်မရေးနဲ့ (DRY)**

Code ကိုတက်နိုင်သမျှ ထပ်တစ်လဲလဲပြန်မရေးပဲနဲ့ ပြန်သုံးပါ။ Blade templates၊ Eloquent scopes တွေကိုပြန်သုံးပါ။

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

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **Query Builder နဲ့ raw SQL queries အစား Eloquent၊ arrays အစား collection ကိုပိုသုံးပါ**

Eloquent က code ကို ဖတ်လို့လွယ် ပြုပြင်ဖို့လွယ်စေတယ်။ နောက်ပြီး eloquent မှာ သုံးလို့‌ကောင်းတဲ့ soft deletes, events, scopes စတဲ့ build-in tools တွေ ပါပါတယ်။ ဒီမှာဝင်ဖတ်ကြည့်လို့ရပါတယ် [Eloquent to SQL reference](https://github.com/alexeymezenin/eloquent-sql-reference)

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

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **Mass assignment**

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

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **Queries တွေကို Blade Templates တွေထဲမှာ Execute မလုပ်ပဲနဲ့ အဲတာအစား eager loading ကိုအသုံးပြုပါ။ (N + 1 problem)**

Bad (user အယောက် ၁၀၀ အတွက် Query ၁၀၁ ခု execute လုပ်ရမယ် ):

```blade
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Good (user အယောက် ၁၀၀ အတွက် Query ၂ ခု ပဲ execute ရမယ်):

```php
$users = User::with('profile')->get();

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **Data-heavy tasks တွေအတွက် Chunk data ကိုသုံးပါ**

Bad:

```php
$users = $this->get();

foreach ($users as $user) {
    ...
}
```

Good:

```php
$this->chunk(500, function ($users) {
    foreach ($users as $user) {
        ...
    }
});
```

[🔝 Back to contents](#contents)

### **Comment ရေးမဲ့ အစား method နဲ့ variable name တွေကို သေချာပေးခဲ့ပါ**

Bad:

```php
// Determine if there are any joins
if (count((array) $builder->getQuery()->joins) > 0)
```

Good:

```php
if ($this->hasJoins())
```

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **JS နဲ့ CSS ကို blade templates ထဲကို မထည့်ပါနဲ့၊ PHP class တွေထဲမှာ HTML code တွေမထည့်ပါနဲ့**

Bad:

```javascript
let article = `{{ json_encode($article) }}`;
```

Better:

```php
<input id="article" type="hidden" value='@json($article)'>

Or

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

In a Javascript file:

```javascript
let article = $('#article').val();
```

အကောင်းဆုံးကတော့ data transfer ဖို့အတွက် specialized PHP to JS Package တွေကိုသုံးပါ။

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **Code ထဲမှာ hard coded စာသားတွေ ထည့်မဲ့အစား config နဲ့ language files တွေကိုသုံးပါ**

Bad:

```php
public function isNormal(): bool
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

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **Community က လက်ခံပြီး အသုံးပြုနေကျ standard laravel tools တွေကိုပဲသုံးပါ**

3rd party packages နဲ့ tools တွေ သုံးမဲ့အစား build-in laravel functionality တွေနဲ့ community packages တွေကိုသာ ပိုသုံးသင့်ပါတယ်။ မဟုတ်ရင် နောက်ပိုင်းမှာ ကိုယ့် project ကို တစ်ခြား developer တွေ ဆက်ပြီး လုပ်တဲ့အခါမှာ tools အသစ်တွေကိုထပ်ပြီး လေ့လာနေရပါလိမ့်မယ်။ ဒါ့အပြင် တစ်ခြား third party package နဲ့ tool သုံးခဲ့ရင် အဲဒီ tools တွေနဲ့ ပက်သတ်ပြီး community ဆီကနေ အကူအညီရနိုင်ခြေလဲ သိသိသာသာလျော့သွားပါလိမ့်မယ်။ ကိုယ့် client ကို အဲတာအတွက် အပိုမကုန်ပါစေနဲ့။

Task | Standard tools | 3rd party tools
------------ | ------------- | -------------
Authorization | Policies | Entrust, Sentinel and other packages
Compiling assets | Laravel Mix, Vite | Grunt, Gulp, 3rd party packages
Development Environment | Laravel Sail, Homestead | Docker
Deployment | Laravel Forge | Deployer and other solutions
Unit testing | PHPUnit, Mockery | Phpspec, Pest
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

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **Laravel ရဲ့ အမည်ပေးပုံတွေကိုလိုက်နာပါ**

Follow [PSR standards](https://www.php-fig.org/psr/psr-12/).

နောက်ပြီး laravel community က လက်ခံထားတဲ့ အမည်ပေးပုံတွေကိုလိုက်နာပါ။

What | How | Good | Bad
------------ | ------------- | ------------- | -------------
Controller | singular | ArticleController | ~~ArticlesController~~
Route | plural | articles/1 | ~~article/1~~
Route name | snake_case with dot notation | users.show_active | ~~users.show-active, show-active-users~~
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
Trait [(PSR)](https://www.php-fig.org/bylaws/psr-naming-conventions/) | adjective | NotifiableTrait | ~~Notification~~
Enum | singular | UserType | ~~UserTypes~~, ~~UserTypeEnum~~
FormRequest | singular | UpdateUserRequest | ~~UpdateUserFormRequest~~, ~~UserFormRequest~~, ~~UserRequest~~
Seeder | singular | UserSeeder | ~~UsersSeeder~~

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **Convention over configuration**

တစ်ချို့ naming conventions တွေကိုလိုက်နာနေရင် တစ်ခြား configuration တွေလုပ်စရာမလိုတော့ဘူး။

Bad:

```php
// Table name 'Customer'
// Primary key 'customer_id'
class Customer extends Model
{
    const CREATED_AT = 'created_at';
    const UPDATED_AT = 'updated_at';

    protected $table = 'Customer';
    protected $primaryKey = 'customer_id';

    public function roles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class, 'role_customer', 'customer_id', 'role_id');
    }
}
```

Good:

```php
// Table name 'customers'
// Primary key 'id'
class Customer extends Model
{
    public function roles(): BelongsToMany
    {
        return $this->belongsToMany(Role::class);
    }
}
```

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **တိုတိုနဲ့ ဖတ်ရလွယ်တဲ့ syntax ကိုတက်နိုင်သမျှသုံးပါ**

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

နောက်ထပ် ဥပမာများ:

တွေ့မြင်နေကျ syntax | တိုတိုနဲ့ ဖတ်ရလွယ် syntax
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

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **new Class အစား loC / Service container တွေကိုသုံးပါ**
new Class syntax က class တွေအတွင်းမှာ tight coupling ဖြစ်စေတဲ့အပြင် testing လုပ်တဲ့အခါမှာ ပိုပြီး ရှုတ်ထွေးစေတယ်။ အဲ့အစား LoC container နဲ့ facades ကိုသုံးပါ။

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

...

$this->user->create($request->validated());
```

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **`.env` file ကနေ data ကိုတိုက်ရိုက်မယူပါနဲ့**

အဲလိုလုပ်မဲ့အစား application မှာသုံးရမဲ့ data ကို config files တွေဆီပို့ပြီးတော့ `config()` helper function ကိုသုံးပါ။

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

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **ရက်စွဲတွေကို standard format အတိုင်းသိမ်းပါ။ Date format တွေကို modify လုပ်ချင်ရင် accessors နဲ့ mutators ကိုသုံးပါ**

ရက်စွဲတစ်ခုကို စာသား(string) အနေနဲ့သိမ်းတာက object instance အနေနဲ့သိမ်းတာလောက် စိတ်မချရဘူး ၊ ဥပမာ Carbon-instance။ Class အချင်းချင်းကြား date string အနေနဲ့ ပေးတာထက် carbon objects အနေနဲ့‌ပေးတာကို ပိုအားပေးပါတယ်။ ဒေတာပြန်ပြတာကိုတော့ display layer(templates) မှာပဲ လုပ်သင့်ပါတယ်။

Bad:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

Good:

```php
// Model
protected $casts = [
    'ordered_at' => 'datetime',
];

// Blade view
{{ $object->ordered_at->toDateString() }}
{{ $object->ordered_at->format('m-d') }}
```

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **DocBlocks တွေကိုမသုံးပါနဲ့**

DocBlocks တွေက ဖတ်ရတာ ပိုခက်စေတယ်။ အဲ့အစား method name ကိုသေချာပေးတာ နဲ့ အသစ်ထွက် PHP feautre တွေဖြစ်တဲ့ return type hints တွေကိုသုံးပါ။

Bad:

```php
/**
 * The function checks if given string is a valid ASCII string
 *
 * @param string $string String we get from frontend which might contain
 *                       illegal characters. Returns True is the string
 *                       is valid.
 *
 * @return bool
 * @author  John Smith
 *
 * @license GPL
 */

public function checkString($string)
{
}
```

Good:

```php
public function isValidAsciiString(string $string): bool
{
}
```

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)

### **တစ်ခြားအလေ့အကျင့်ကောင်းများ**

Laravel၊ တစ်ခြား ဆင်တူတဲ့(RoR၊ Django)အစရှိတဲ့ frameworks တွေနဲ့စိမ်းတဲ့ patterns တွေ tools တွေကိုမသုံးပါနဲ့။ App တစ်ခုဆောက်ဖို့ကို Symfony (ဒါမှမဟုတ် Spring) ရဲ့ approach ကို သုံးရတာကြိုက်ရင် အဲဒီ framework ကိုပဲသုံးလိုက်သင့်ပါတယ်။ 

Route file တွေမှာ ဘာlogic မှမထည့်ပါနဲ့။

Vanilla PHP ကို Blade templates တွေမှာ နည်းနိုင်သမျှ နည်းသုံးပါ။

Testing အတွက် in-memory DB ကိုသုံးပါ။

Framework version update လုပ်တာ နဲ့ တစ်ခြား issues တွေမတက်‌အောင် framework ရဲ့ standard features တွေကိုပြင်မရေးပါနဲ့။

နောက်ထွက် PHP syntax တွေကိုတက်နိုင်သမျှ အသုံးပြုပါ ဒါပေမယ့် ဖတ်ရလွယ်အောင်ရေးဖို့လဲ မမေ့ပါနဲ့။

တကယ်သေချာမသိရင် View Composers နဲ့ တစ်ခြားဆင်တူတဲ့ tools တွေကိုမသုံးပါနဲ့။ များသောအားဖြင့် ပြဿနာကို ဖြေရှင်းဖို့ ပိုကောင်းတဲ့ နည်းတွေရှိပါတယ်။

[🔝Contents တွေဆီပြန်သွားမယ်](#contents)
