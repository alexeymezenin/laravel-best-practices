![Laravel best practices](/images/logo-english.png?raw=true)

Translations:

[Nederlands](https://github.com/Protoqol/Beste-Laravel-Praktijken) (by [Protoqol](https://github.com/Protoqol))

[한국어](https://github.com/xotrs/laravel-best-practices) (by [cherrypick](https://github.com/xotrs))

[日本語](japanese.md) (by [2bo](https://github.com/2bo))

[漢語](chinese.md) (by [xiaoyi](https://github.com/Shiloh520))

[ภาษาไทย](thai.md) (by [kongvut sangkla](https://github.com/kongvut))

[فارسی](persian.md) (by [amirhossein baghaie](https://github.com/amirbagh75))

[Português](https://github.com/jonaselan/laravel-best-practices) (by [jonaselan](https://github.com/jonaselan))

[Русский](russian.md)

[Tiếng Việt](https://chungnguyen.xyz/posts/code-laravel-lam-sao-cho-chuan) (by [Chung Nguyễn](https://github.com/nguyentranchung))

[Español](spanish.md) (by [César Escudero](https://github.com/cedaesca))

[Français](french.md) (by [Mikayil S.](https://github.com/mikayilsrt))

[Polski](https://github.com/maciejjeziorski/laravel-best-practices-pl) (by [Maciej Jeziorski](https://github.com/maciejjeziorski))

[Türkçe](turkish.md) (by [Burak](https://github.com/ikidnapmyself))

[Deutsche](german.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Italiana](italian.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

এটা লারাভেল এর সাথে SOLID Principles বা Patterns সংযোজন নয়। এখানে আপনি সেরা অনুশীলন গুলা পাবেন যা সত্যিকার লারাভেল প্রজেক্টে সাধারণত অবহেলা করা হয়। 

## Contents

[সিঙ্গেল রেস্পন্সিবিলিটি প্রিন্সিপল বা একক দায়িত্ব নীতি](#single-responsibility-principle)

[ফ্যাট মডেলস, স্কীননি কন্ট্রোলার](#fat-models-skinny-controllers)

[ভ্যালিডেশন](#validation)

[বিসনেস লজিক সমূহ সার্ভিস ক্লাসে থাকা প্রয়োজন](#business-logic-should-be-in-service-class)

[একি জিনিস বার বার করবেন না। (DRY)](#dont-repeat-yourself-dry)

[Query Builder এবং Raw SQL Query লেখার পরিবর্তে Eloquent কে গুরুত্ব দিন। Array থেকে Collection ব্যবহার কে গুরুত্ব দিন](#prefer-to-use-eloquent-over-using-query-builder-and-raw-sql-queries-prefer-collections-over-arrays)

[এলোপাতাড়ি কাজ](#mass-assignment)

[ব্লেড-টেমপ্লেটে কোয়েরী লিখবেন-না এবং একবারে লোড করুন। (N + 1 সমস্যা)](#do-not-execute-queries-in-blade-templates-and-use-eager-loading-n--1-problem)

[কোড মন্তব্য লিখতে সমস্যা নাই, কিন্তু মেথডের নামকরণ এবং ভেরিয়েবলের নামকরণ মন্তব্য থেকে বেশি গুরুত্বপুর্ণ](#comment-your-code-but-prefer-descriptive-method-and-variable-names-over-comments)

[ব্লেড টেমপ্লেটের মধ্যে JS এবং CSS সরাসরি ইঞ্জেক্ট করবেন না এবং PHP Class এ HTML লিখবেন না](#do-not-put-js-and-css-in-blade-templates-and-do-not-put-any-html-in-php-classes)

[সরাসরি রচনা লেখা থেকে কনফিগারেশন, ল্যাঙ্গুয়েজ এবং কনস্টান্ট গুলো আলাদা ফাইলে রাখুন](#use-config-and-language-files-constants-instead-of-text-in-the-code)

[কমিউনিটিতে প্রচলিত মানসম্মত লারাভেলের টুলস গুলো ব্যাবহার করুন](#use-standard-laravel-tools-accepted-by-community)

[লারাভেল নেমিং কনভেনশন অনুসরণ করুন](#follow-laravel-naming-conventions)

[যত সম্ভব সংক্ষিপ্ত ও সহজে পড়া যায় এমন সিনট্যাক্স লিখবেন](#use-shorter-and-more-readable-syntax-where-possible)

[নতুন ক্লাসের পরিবর্তে IoC কন্টেইনার বা facades ব্যবহার করুন](#use-ioc-container-or-facades-instead-of-new-class)

[`.env` ফাইলের ডাটা সরাসরি নিবেন না](#do-not-get-data-from-the-env-file-directly)

[তারিখ গুলো স্ট্যান্ডার্ড ফরম্যাট এ রাখবেন। তারিখের ফরম্যাট পরিবর্তনের জন্য accessors এবং mutators ব্যবহার করুন](#store-dates-in-the-standard-format-use-accessors-and-mutators-to-modify-date-format)

[অন্যান্য ভাল অনুশীলন](#other-good-practices)

### **সিঙ্গেল রেস্পন্সিবিলিটি প্রিন্সিপল বা একক দায়িত্ব নীতি**

A class and a method should have only one responsibility.

খারাপ:

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

ভালো:

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

[🔝 Back to contents](#contents)

### **ফ্যাট মডেলস, স্কীননি কন্ট্রোলার**

Put all DB related logic into Eloquent models or into Repository classes if you're using Query Builder or raw SQL queries.

খারাপ:

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

ভালো:

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

[🔝 Back to contents](#contents)

### **ভ্যালিডেশন**

Move validation from controllers to Request classes.

খারাপ:

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

ভালো:

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

[🔝 Back to contents](#contents)

### **বিসনেস লজিক সমূহ সার্ভিস ক্লাসে থাকা প্রয়োজন**

A controller must have only one responsibility, so move business logic from controllers to service classes.

খারাপ:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

ভালো:

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

[🔝 Back to contents](#contents)

### **একি জিনিস বার বার করবেন না। (DRY)**

Reuse code when you can. SRP is helping you to avoid duplication. Also, reuse Blade templates, use Eloquent scopes etc.

খারাপ:

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

ভালো:

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

[🔝 Back to contents](#contents)

### **Query Builder এবং Raw SQL Query লেখার পরিবর্তে Eloquent কে গুরুত্ব দিন। Array থেকে Collection ব্যবহার কে গুরুত্ব দিন**

Eloquent allows you to write readable and maintainable code. Also, Eloquent has great built-in tools like soft deletes, events, scopes etc.

খারাপ:

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

ভালো:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 Back to contents](#contents)

### **এলোপাতাড়ি কাজ**

খারাপ:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

ভালো:

```php
$category->article()->create($request->validated());
```

[🔝 Back to contents](#contents)

### **ব্লেড-টেমপ্লেটে কোয়েরী লিখবেন-না এবং একবারে লোড করুন। (N + 1 সমস্যা)**

Bad (for 100 users, 101 DB queries will be executed):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Good (for 100 users, 2 DB queries will be executed):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 Back to contents](#contents)

### **কোড মন্তব্য লিখতে সমস্যা নাই, কিন্তু মেথডের নামকরণ এবং ভেরিয়েবলের নামকরণ মন্তব্য থেকে বেশি গুরুত্বপুর্ণ**

খারাপ:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

Better:

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

ভালো:

```php
if ($this->hasJoins())
```

[🔝 Back to contents](#contents)

### **ব্লেড টেমপ্লেটের মধ্যে JS এবং CSS সরাসরি ইঞ্জেক্ট করবেন না এবং PHP Class এ HTML লিখবেন না**

খারাপ:

```php
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

The best way is to use specialized PHP to JS package to transfer the data.

[🔝 Back to contents](#contents)

### **সরাসরি রচনা লেখা থেকে কনফিগারেশন, ল্যাঙ্গুয়েজ এবং কনস্টান্ট গুলো আলাদা ফাইলে রাখুন**

খারাপ:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

ভালো:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 Back to contents](#contents)

### **কমিউনিটিতে প্রচলিত মানসম্মত লারাভেলের টুলস গুলো ব্যাবহার করুন**

Prefer to use built-in Laravel functionality and community packages instead of using 3rd party packages and tools. Any developer who will work with your app in the future will need to learn new tools. Also, chances to get help from the Laravel community are significantly lower when you're using a 3rd party package or tool. Do not make your client pay for that.

Task | Standard tools | 3rd party tools
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

[🔝 Back to contents](#contents)

### **যত সম্ভব সংক্ষিপ্ত ও সহজে পড়া যায় এমন সিনট্যাক্স লিখবেন**

 Follow [PSR standards](http://www.php-fig.org/psr/psr-2/).
 
 Also, follow naming conventions accepted by Laravel community:

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
Contract (interface) | adjective or noun | Authenticatable | ~~AuthenticationInterface, IAuthentication~~
Trait | adjective | Notifiable | ~~NotificationTrait~~

[🔝 Back to contents](#contents)

### **যত সম্ভব সংক্ষিপ্ত ও সহজে পড়া যায় এমন সিনট্যাক্স লিখবেন**

খারাপ:

```php
$request->session()->get('cart');
$request->input('name');
```

ভালো:

```php
session('cart');
$request->name;
```

More examples:

Common syntax | Shorter and more readable syntax
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

[🔝 Back to contents](#contents)

### **নতুন ক্লাসের পরিবর্তে IoC কন্টেইনার বা facades ব্যবহার করুন**

new Class syntax creates tight coupling between classes and complicates testing. Use IoC container or facades instead.

খারাপ:

```php
$user = new User;
$user->create($request->validated());
```

ভালো:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 Back to contents](#contents)

### **`.env` ফাইলের ডাটা সরাসরি নিবেন না**

Pass the data to config files instead and then use the `config()` helper function to use the data in an application.

খারাপ:

```php
$apiKey = env('API_KEY');
```

ভালো:

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[🔝 Back to contents](#contents)

### **তারিখ গুলো স্ট্যান্ডার্ড ফরম্যাট এ রাখবেন। তারিখের ফরম্যাট পরিবর্তনের জন্য accessors এবং mutators ব্যবহার করুন**

খারাপ:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

ভালো:

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

[🔝 Back to contents](#contents)

### **অন্যান্য ভাল অনুশীলন**

Never put any logic in routes files.

Minimize usage of vanilla PHP in Blade templates.

[🔝 Back to contents](#contents)
