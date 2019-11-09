![Laravel best practices](/images/logo-english.png?raw=true)

অনুবাদঃ

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

[বাংলা](bangla.md) (by [Anowar Hossain](https://github.com/AnowarCST))


এটা লারাভেল এর সাথে SOLID Principles বা Patterns সংযোজন নয়। এখানে আপনি সেরা অনুশীলন গুলা পাবেন যা বাস্তব জীবনে লারাভেল প্রজেক্টে সাধারণত অবহেলা করা হয়। 

## সূচীপত্র

[সিঙ্গেল রেস্পন্সিবিলিটি প্রিন্সিপল বা একক দায়িত্ব নীতি](#সিঙ্গেল-রেস্পন্সিবিলিটি-প্রিন্সিপল-বা-একক-দায়িত্ব-নীতি)

[ফ্যাট মডেলস, স্কীনি কন্ট্রোলারস](#ফ্যাট-মডেলস-স্কীনি-কন্ট্রোলারস)

[ভ্যালিডেশন](#ভ্যালিডেশন)

[বিসনেস লজিক সমূহ সার্ভিস ক্লাসে থাকা প্রয়োজন](#বিসনেস-লজিক-সমূহ-সার্ভিস-ক্লাসে-থাকা-প্রয়োজন)

[একি জিনিস বার বার করবেন না। (DRY)](#একি-জিনিস-বার-বার-করবেন-না-dry)

[Query Builder এবং Raw SQL Query লেখার পরিবর্তে Eloquent কে গুরুত্ব দিন। Array থেকে Collection ব্যবহার কে গুরুত্ব দিন](#query-builder-এবং-raw-sql-query-লেখার-পরিবর্তে-eloquent-কে-গুরুত্ব-দিন-array-থেকে-collection-ব্যবহার-কে-গুরুত্ব-দিন)

[সমানে এসাইন করা](#সমানে-এসাইন-করা)

[ব্লেড-টেমপ্লেটে কোয়েরী লিখবেন-না এবং একবারে লোড করুন। (N + 1 সমস্যা)](#ব্লেড-টেমপ্লেটে-কোয়েরী-লিখবেন-না-এবং-একবারে-লোড-করুন-n--1-সমস্যা)

[কোড মন্তব্য লিখতে সমস্যা নাই, কিন্তু মেথডের নামকরণ এবং ভেরিয়েবলের নামকরণ মন্তব্য থেকে বেশি গুরুত্বপুর্ণ](#কোড-মন্তব্য-লিখতে-সমস্যা-নাই-কিন্তু-মেথডের-নামকরণ-এবং-ভেরিয়েবলের-নামকরণ-মন্তব্য-থেকে-বেশি-গুরুত্বপুর্ণ)

[ব্লেড টেমপ্লেটের মধ্যে JS এবং CSS সরাসরি ইঞ্জেক্ট করবেন না এবং PHP Class এ HTML লিখবেন না](#ব্লেড-টেমপ্লেটের-মধ্যে-js-এবং-css-সরাসরি-ইঞ্জেক্ট-করবেন-না-এবং-php-class-এ-html-লিখবেন-না)

[সরাসরি লেখা থেকে কনফিগারেশন, ল্যাঙ্গুয়েজ এবং কনস্টান্ট ফাইল ব্যাবহার করুন](#সরাসরি-লেখা-থেকে-কনফিগারেশন-ল্যাঙ্গুয়েজ-এবং-কনস্টান্ট-ফাইল-ব্যাবহার-করুন)

[কমিউনিটিতে প্রচলিত মানসম্মত লারাভেলের টুলস গুলো ব্যাবহার করুন](#কমিউনিটিতে-প্রচলিত-মানসম্মত-লারাভেলের-টুলস-গুলো-ব্যাবহার-করুন)

[লারাভেল নেমিং কনভেনশন অনুসরণ করুন](#লারাভেল-নেমিং-কনভেনশন-অনুসরণ-করুন)

[যত সম্ভব সংক্ষিপ্ত ও সহজে পড়া যায় এমন সিনট্যাক্স লিখবেন](#যত-সম্ভব-সংক্ষিপ্ত-ও-সহজে-পড়া-যায়-এমন-সিনট্যাক্স-লিখবেন)

[নতুন ক্লাসের পরিবর্তে IoC কন্টেইনার বা facades ব্যবহার করুন](#নতুন-ক্লাসের-পরিবর্তে-ioc-কন্টেইনার-বা-facades-ব্যবহার-করুন)

[`.env` ফাইলের ডাটা সরাসরি নিবেন না](#env-ফাইলের-ডাটা-সরাসরি-নিবেন-না)

[তারিখ গুলো স্ট্যান্ডার্ড ফরম্যাট এ রাখবেন। তারিখের ফরম্যাট পরিবর্তনের জন্য accessors এবং mutators ব্যবহার করুন](#তারিখ-গুলো-স্ট্যান্ডার্ড-ফরম্যাট-এ-রাখবেন-তারিখের-ফরম্যাট-পরিবর্তনের-জন্য-accessors-এবং-mutators-ব্যবহার-করুন)

[অন্যান্য ভাল অনুশীলন](#অন্যান্য-ভাল-অনুশীলন)

### **সিঙ্গেল রেস্পন্সিবিলিটি প্রিন্সিপল বা একক দায়িত্ব নীতি**

একটা ক্লাস এবং একটা মেথডের একটা করে কাজ/দায়িত্ব হওয়া উচিৎ।

খারাপঃ

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

ভালঃ

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

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **ফ্যাট মডেলস, স্কীনি কন্ট্রোলারস**

সবগুলো ডাটাবেস লজিক Eloquent মডেলে অথবা Repository ক্লাসে থাকা উচিৎ, আপনি যদি Query Builder অথবা raw SQL queries ব্যাবহার করেন।

খারাপঃ

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

ভালঃ

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

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **ভ্যালিডেশন**

ভ্যালিডেশন কোড গুলো Controller থেকে Request class এ সরিয়ে ফেলুন।

খারাপঃ

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

ভালঃ

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

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **বিসনেস লজিক সমূহ সার্ভিস ক্লাসে থাকা প্রয়োজন**

একটা কন্ট্রোলারের একটাই কাজ হওয়া উচিৎ। তাই বিজনেস লজিক গুলো কন্ট্রোলার থেকে সার্ভিস ক্লাসে সরিয়ে ফেলুন।

খারাপঃ

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

ভালঃ

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

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **একি জিনিস বার বার করবেন না। (DRY)**

কোডের পুনঃব্যবহার নিশ্চিত করুন। বার বার লেখার থেকে SRP আপনাকে সাহায্য করবে। সাথে Blade টেম্পলেট, Eloquent স্কোপ ইত্যাদির পুনঃব্যবহার করুন।

খারাপঃ

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

ভালঃ

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

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **Query Builder এবং Raw SQL Query লেখার পরিবর্তে Eloquent কে গুরুত্ব দিন। Array থেকে Collection ব্যবহার কে গুরুত্ব দিন**

Eloquent আপনাকে পাঠযোগ্য এবং রক্ষণাবেক্ষণযোগ্য কোড করতে সাহায্য করবে। এছাড়াও, Eloquent এর বেশ কিছু বিল্ট-ইন টুলস আছে যেমনঃ soft deletes, events, scopes ইত্যাদি।

খারাপঃ

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

ভালঃ

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **সমানে এসাইন করা**

খারাপঃ

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

ভালঃ

```php
$category->article()->create($request->validated());
```

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **ব্লেড-টেমপ্লেটে কোয়েরী লিখবেন-না এবং একবারে লোড করুন। (N + 1 সমস্যা)**

খারাপ (১০০ জন ইউজারের জন্য, ১০১ টা DB queries এক্সিকিউট হবে):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

ভালো (১০০ জন ইউজারের জন্য, ২ টা DB queries এক্সিকিউট হবে)-ঃ 

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **কোড মন্তব্য লিখতে সমস্যা নাই, কিন্তু মেথডের নামকরণ এবং ভেরিয়েবলের নামকরণ মন্তব্য থেকে বেশি গুরুত্বপুর্ণ**

খারাপঃ

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

তুলনামূলক ভালঃ

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

ভালঃ

```php
if ($this->hasJoins())
```

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **ব্লেড টেমপ্লেটের মধ্যে JS এবং CSS সরাসরি ইঞ্জেক্ট করবেন না এবং PHP Class এ HTML লিখবেন না**

খারাপঃ

```php
let article = `{{ json_encode($article) }}`;
```

তুলনামূলক ভালঃ

```php
<input id="article" type="hidden" value='@json($article)'>

Or

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

Javascript ফাইলের এর মধ্যেঃ

```javascript
let article = $('#article').val();
```

সবচেয়ে ভালো হয় আলাদা ভাবে PHP থেকে JS প্যাকেজে ডাটা ট্র্যান্সফার করা।

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **সরাসরি লেখা থেকে কনফিগারেশন, ল্যাঙ্গুয়েজ এবং কনস্টান্ট ফাইল ব্যাবহার করুন**

খারাপঃ

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

ভালঃ

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **কমিউনিটিতে প্রচলিত মানসম্মত লারাভেলের টুলস গুলো ব্যাবহার করুন**

অন্যকোন থার্ডপার্টি packages বা tools ব্যাবহার করা থেকে লারাভেলের নিজস্ব ফাংশনালিটি এবং কমিউনিটি প্যাকেজ ব্যাবহার করা ভাল। ভবিষ্যতে কোন ডেভেলপার আপনার প্রজেক্টে কাজ করতে গেলে তাকে টুলস গুলো শিখে নেয়া লাগবে। এছাড়াও, থার্ডপার্টি packages বা tools ব্যাবহার করলে লারাভেল কমিউনিটি থেকে সাপোর্ট পাওয়ার সম্ভাবনা কম। আপনার ক্লায়েন্ট কে সেটার জন্য অর্থ খরচ করাবেন না।

কাজ | স্ট্যান্ডার্ড টুলস | থার্ডপার্টি টুলস
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

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **লারাভেল নেমিং কনভেনশন অনুসরণ করুন**

 [PSR standards](http://www.php-fig.org/psr/psr-2/) অনুসরণ করুন।
 
 
 এছাড়াও, লারাভেল কমিউনিটি কর্তিক স্বীকৃত নেমিং কনভেনশন (নামকরণ) ফলো করা যায়ঃ

কি | কিভাবে | ভাল | খারাপ
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
Contract (interface) | adjective or noun | Authenticatable | ~~AuthenticationInterface, IAuthentication~~
Trait | adjective | Notifiable | ~~NotificationTrait~~

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **যত সম্ভব সংক্ষিপ্ত ও সহজে পড়া যায় এমন সিনট্যাক্স লিখবেন**

খারাপঃ

```php
$request->session()->get('cart');
$request->input('name');
```

ভালঃ

```php
session('cart');
$request->name;
```

আরো উদাহরণঃ

সাধারণ সিনটেক্স | সংক্ষিপ্ত এবং আরো সহজে পাঠযোগ্য সিনটেক্স
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

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **নতুন ক্লাসের পরিবর্তে IoC কন্টেইনার বা facades ব্যবহার করুন**

নতুন ক্লাসের সিনট্যাক্স ক্লাস গুলোকে টাইট কাপলিং করে এবং টেস্টিং জটিল করে। এর থেকে ভালো IoC container অথবা facades ব্যাবহার করা।

খারাপঃ

```php
$user = new User;
$user->create($request->validated());
```

ভালঃ

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **`.env` ফাইলের ডাটা সরাসরি নিবেন না**

বরং ডাটা গুলোকে কনফিগ ফাইলের মধ্যে রাখুন এবং `config()` হেল্পার ফাংশন ব্যাবহার করে আপনার এপ্লিকেশনে ব্যাবহার করুন।

খারাপঃ

```php
$apiKey = env('API_KEY');
```

ভালঃ

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **তারিখ গুলো স্ট্যান্ডার্ড ফরম্যাট এ রাখবেন। তারিখের ফরম্যাট পরিবর্তনের জন্য accessors এবং mutators ব্যবহার করুন**

খারাপঃ

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

ভালঃ

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

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)

### **অন্যান্য ভাল অনুশীলন**

routes ফাইলের মধ্যে কখনো লজিক রাখবেন না।

Blade টেম্পলেটের মধ্যে ভ্যানিলা PHP এর ব্যাবহার কমায় ফেলেন।

[🔝 সূচীপত্রে ফিরে যান](#সূচীপত্র)
