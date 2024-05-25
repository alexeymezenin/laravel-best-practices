![د لاراول د ښه کوډ لیکلو نمونې او مثالونه](/images/logo-english.png?raw=true)

You might also want to check out the [real-world Laravel example application](https://github.com/alexeymezenin/laravel-realworld-example-app) and [Eloquent SQL reference](https://github.com/alexeymezenin/eloquent-sql-reference)

ژباړې:

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

[![Laravel example app](/images/laravel-real-world-banner.png?raw=true)](https://github.com/alexeymezenin/laravel-realworld-example-app)

## د منځپانګو یا مطالبو نوملړ

[د یوه مسؤلیت اصل](#د-یوه-مسؤلیت-اصل)

[میتودونه باید یوازې یو کار ترسره کړي](#میتودونه-باید-یوازې-یو-کار-ترسره-کړي)

[لوی ماډلونه، کوچني کنټرولرونه!](#لوی-ماډلونه-کوچني-کنټرولرونه)

[ډېټا تصدیق یا اعتبار](#ډېټا-تصدیق-یا-اعتبار)

[د پروګرام منطق باید په service class کې وي.](#د-پروګرام-منطق-باید-په-service-class-کې-وي)

[د DRY اصل یا خپل ځان مه تکراروه!](#د-dry-اصل-یا-خپل-ځان-مه-تکراروه)

[د Query Builder او raw SQL queries پر ځای باید د Eloquent ORM څخه کار واخیستل شي. او همچنان د Arrays پر ځای د Collections څخه کار واخیستل شي.](#د-query-builder-او-raw-sql-queries-پر-ځای-باید-د-eloquent-orm-څخه-کار-واخیستل-شي-او-همچنان-د-arrays-پر-ځای-د-collections-څخه-کار-واخیستل-شي)

[(Mass assignment) ډله ایزه دنده](#mass-assignment-ډله-ایزه-دنده)


[د دې پر ځای چې query په blade کې ولیکئ د eager loading څخه کار واخلئ. (N+1 مسئله)](#د-دې-پر-ځای-چې-query-په-blade-کې-ولیکئ-د-eager-loading-څخه-کار-واخلئ-n1-مسئله)

[د ډېرې ډېټا لپاره د ډېټا چنک (data chunk) نه استفاده وکړئ](#د-ډېرې-ډېټا-لپاره-د-ډېټا-چنک-data-chunk-نه-استفاده-وکړئ)


[تبصرې وکړئ (Comments)، مګر د متودونو یا متغیرونو نومونه توضیحي او معنی لرونکي په پام کې ونیسئ.](#تبصرې-وکړئ-comments-مګر-د-متودونو-یا-متغیرونو-نومونه-توضیحي-او-معنی-لرونکي-په-پام-کې-و-نیسئ)

[په Blade ټیمپلیټونو کې له js او css څخه کار مه اخلئ او هېڅ HTML کوډ په PHP class کې مه کاروئ.](#په-blade-ټیمپلیټونو-کې-له-js-او-css-څخه-کار-مه-اخلئ-او-هېڅ-html-کوډ-په-php-class-کې-مه-کاروئ)

[پر ځای د مستقیم متنونو څخه په کوډ کې، د config او languages فایلونو څخه کار واخلئ!](#پر-ځای-د-مستقیم-متنونو-څخه-په-کوډ-کې-د-config-او-languages-فایلونو-څخه-کار-واخلئ)

[د لاراول د معیاري وسایلو څخه چې د لاراول ټولنې یا کمیونیټي لخوا تایید شوي دي کار واخلئ](#د-لاراول-د-معیاري-وسایلو-څخه-چې-د-لاراول-ټولنې-یا-کمیونیټي-لخوا-تایید-شوي-دي-کار-واخلئ)

[د لاراول د نومونو له اصولو څخه کار واخلئ.](#د-لاراول-د-نومونو-له-اصولو-څخه-کار-واخلئ)

[کنوانسیون د تنظیماتو په پرتله غوره دی](#کنوانسیون-د-تنظیماتو-په-پرتله-غوره-دی)

[تر حده پورې په خپل کوډ کې، د معنی لرونکي او لنډ Syntax څخه کار واخلئ.](#تر-حده-پورې-په-خپل-کوډ-کې-د-معنی-لرونکي-او-لنډ-syntax-څخه-کار-واخلئ)

[د object د جوړولو په وخت کې د new کیورد پر ځای IoC container او facades څخه کار واخلئ](#د-object-د-جوړولو-په-وخت-کې-د-new-کیورد-پر-ځای-ioc-container-او-facades-څخه-کار-واخلئ)

[له .env فایل څخه هېڅ وخت مستقیم ډېټا مه ترلاسه کوئ.](#له-env-فایل-څخه-هېڅ-وخت-مستقیم-ډېټا-مه-ترلاسه-کوئ)

[تاریخ او وخت په معیاري بڼه کې ذخیره کړئ. د تاریخ او وخت د ښودلو لپاره له Accessors & Mutators څخه کار واخلئ.](#تاریخ-او-وخت-په-معیاري-بڼه-کې-ذخیره-کړئ-د-تاریخ-او-وخت-د-ښودلو-لپاره-له-accessors--mutators-څخه-کار-واخلئ)


[ډاټ بلاک مه استفاده کوئ](#ډاټ-بلاک-مه-استفاده-کوئ)

[نورې ښې طریقې](#نورې-ښې-طریقې)


### **د یوه مسؤلیت اصل**

یو کلس باید یوه وظیفه ولري.

بد کوډ:

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

ښه کوډ:

```php
public function update(UpdateRequest $request): string
{
    $this->logService->logEvents($request->events);

    $this->event->updateGeneralEvent($request->validated());

    return back();
}
```

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **میتودونه باید یوازې یو کار ترسره کړي**

یو فنکشن باید یوازې یو کار ترسره کړي او باید په ښه شکل یې ترسره کړي.

بد کوډ:

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

ښه کوډ:

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

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **لوی ماډلونه، کوچني کنټرولرونه!**


د ډېټابیس مربوط شیان په Eloquent models کې ولیکئ.

بد کوډ:

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

ښه کوډ:

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

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **ډېټا تصدیق یا اعتبار**

د ډېټا تصدیق یا ولیدیشن د کنټرولرونو پر ځای په Request classess کې ولیکئ.

بد کوډ:

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

ښه کوډ:

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

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **د پروګرام منطق باید په service class کې وي**

یو کنټرولر باید یوازې یو مسؤلیت ولري، نو د کوډ منطق په د کنټرولرونو پر ځای باید په service classes کې ولیکئ.

بد کوډ:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ...
}
```

ښه کوډ:

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

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **د DRY اصل یا خپل ځان مه تکراروه**

کوډ یو وار ولیکئ او ډېر ځایه یې استعمال کړی د کوډ د تکرار څخه ډډه وکړئ.
همچنان Blade templates کوډ‌ هم مه تکراروئ، د Eloquent scope څخه استفاده وکړئ.

بد کوډ:

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

ښه کوډ:

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

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)


### **د Query Builder او raw SQL queries پر ځای باید د Eloquent ORM څخه کار واخیستل شي. او همچنان د Arrays پر ځای د Collections څخه کار واخیستل شي**


د Eloquent په واسېه تاسې کولی شئ چې کوډ ویونکي او -هغه چا ته چې وروسته په کوډ کې تغییرات راولی- ته آسانه کوډ ولیکئ.
همچنان Eloquent مخکې جوړ شوي شیان لري لکه soft deletes, events, scopes وغیره
[Eloquent SQL ته مرجع](https://github.com/alexeymezenin/eloquent-sql-reference).

بد کوډ:

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

ښه کوډ:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **(Mass assignment) ډله ایزه دنده**

بد کوډ:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;

// Add category to article
$article->category_id = $category->id;
$article->save();
```

ښه کوډ:

```php
$category->article()->create($request->validated());
```

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **د دې پر ځای چې query په blade کې ولیکئ د eager loading څخه کار واخلئ. (N+1 مسئله)**


بد کوډ (د ۱۰۰ کارنو لپاره ۱۰۱ کیوریانې رن کوي):

```blade
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

ښه کوډ (for 100 users, 2 DB queries will be executed):
ښه کوډ (د ۱۰۰ کارنو لپاره ۲ کیوریانې رن کوي):

```php
$users = User::with('profile')->get();

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **د ډېرې ډېټا لپاره د ډېټا چنک (data chunk) نه استفاده وکړئ**

بد کوډ:

```php
$users = $this->get();

foreach ($users as $user) {
    ...
}
```

ښه کوډ:

```php
$this->chunk(500, function ($users) {
    foreach ($users as $user) {
        ...
    }
});
```

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **تبصرې وکړئ comments مګر د متودونو یا متغیرونو نومونه توضیحي او معنی لرونکي په پام کې و نیسئ**


بد کوډ:

```php
// Determine if there are any joins
if (count((array) $builder->getQuery()->joins) > 0)
```

ښه کوډ:

```php
if ($this->hasJoins())
```

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **په Blade ټیمپلیټونو کې له js او css څخه کار مه اخلئ او هېڅ HTML کوډ په PHP class کې مه کاروئ**


بد کوډ:

```javascript
let article = `{{ json_encode($article) }}`;
```

بهتر کوډ:

```php
<input id="article" type="hidden" value='@json($article)'>

یا

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

په یو جاواسکریپت فایل کې

```javascript
let article = $('#article').val();
```

بهتره داده چې د PHP to JS .پکیج څخه د ډیټا د لېږلو په خاطر استفاده وشي

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **پر ځای د مستقیم متنونو څخه په کوډ کې، د config او languages فایلونو څخه کار واخلئ!**

بد کوډ:

```php
public function isNormal(): bool
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

ښه کوډ:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **د لاراول د معیاري وسایلو څخه چې د لاراول ټولنې یا کمیونیټي لخوا تایید شوي دي کار واخلئ**

د دې پر ځای چې دریمګړي پکیجونو او ټولس نه استفاده وکړئ دا به ښه وي چې د لاراول او د لاراول ټولنې له خوا  چې کوم پکیجونه او ټولس جوړ شوي، نه استفاده وشي.
هر هغه ډېوېلوپر چې په راتلونکي کې ستاسو له اپلکیشن څخه استفاده کوی، باید نوي ټولس زده کړي.
او که چېرې تاسې دا پکیجونه او ټولس چې رسمي د لاراول ټولنې څخه نه وي، استفاده کوئ. که چېرې کومې ستونزې سره مخامخ کېږئ نه حلول به یې ګران کار وي.  

وظیفه | معیاري وسایل | (غیر معیاری) 3 ګړي وسایل
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

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **د لاراول د نومونو له اصولو څخه کار واخلئ**

دا تعقیب کړئ [PSR standards](https://www.php-fig.org/psr/psr-12/).

همچنان هغه اصول چې د لاراول د ټولنې لخوا منل شوي دي، تعقیب کړئ

څه | څنګه | ښه کوډ | بد کوډ
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

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **کنوانسیون د تنظیماتو په پرتله غوره دی**


که تاسو دې اصولو ته پاملرنه وکړئ نو اضافي کانفیګ او کوډونو ته به اړتیا و نه لرئ

بد کوډ:

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

ښه کوډ:

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

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **تر حده پورې په خپل کوډ کې، د معنی لرونکي او لنډ Syntax څخه کار واخلئ**

بد کوډ:

```php
$request->session()->get('cart');
$request->input('name');
```

ښه کوډ:

```php
session('cart');
$request->name;
```

نور مثالونه:

عام Syntax | لنډ او کوډ لوستونکي ته آسانه کوډ
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

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **د object د جوړولو په وخت کې د new کیورد پر ځای IoC container او facades څخه کار واخلئ**


د کلسونو نوی syntax کوډ پېچلی کوي د هغه پر ځای د  IoC container  او  Facades نه استفاده وکړئ.

بد کوډ:

```php
$user = new User;
$user->create($request->validated());
```

ښه کوډ:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

...

$this->user->create($request->validated());
```

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **له .env فایل څخه هېڅ وخت مستقیم ډېټا مه ترلاسه کوئ**


د .env فایل څخه مستقیما ډېټا مه را اخلئ دهغه پر ځای د config() helper function  نه استفاده وکړئ.

بد کوډ:

```php
$apiKey = env('API_KEY');
```

ښه کوډ:

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **تاریخ او وخت په معیاري بڼه کې ذخیره کړئ. د تاریخ او وخت د ښودلو لپاره له Accessors & Mutators څخه کار واخلئ**


یو نېټه د string په بڼه د یو object  لږ باوري ده، لکه د کاربون (Carbon) بیلګه. 
سپارښتنه کیږي چې د نیټې د strings پر ځای د کاربون بیلګې د ټولګیو ترمنځ ولېږدوئ. ښودنه باید په  view  برخه د اپلکېشن  (templates) کې وشي.



بد کوډ:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

ښه کوډ:

```php
// Model
protected $casts = [
    'ordered_at' => 'datetime',
];

// Blade view
{{ $object->ordered_at->toDateString() }}
{{ $object->ordered_at->format('m-d') }}
```

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **ډاټ بلاک مه استفاده کوئ**

DotBlocks  د کوډ ویل سختوي، نو باید د descriptive method  او د php  د نوي خصوصیاتو (features) څخه استفاده وشي لکه return type hints.

بد کوډ:

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

ښه کوډ:

```php
public function isValidAsciiString(string $string): bool
{
}
```

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)

### **نورې ښې طریقې**

 هغه patterns  له هغو وسایلو نه چې له لاراول ته له مشابهو فریمورکونو څخه دي لکه جانګو یا روبي، نه ډډه وکړئ.
 که ستاسو Symfony یا  Sprint فریمورکونه خوښیږي، دا به ښه وي چې د لاراول پر ځای له هغو نه استفاده وکړئ.  

په Route فایلونو کې هیڅکله خپل د اپلکیشن کډ مه لیکئ.

له خالص پي اچ پي یا vanilla php څخه به blade templates کې کوشش وکړئ چې تر آخری خده ډډه وکړئ. 

د ټسټنګ لپاره د in-memory DB نه استفاده وکړئ

د فریمورک په کود او features کې تغییر مه راولئ.

د عصري پی اچ پي‌ څخه استفاده وکړئ، داسې کوډ ولیکئ‌ چې په راتلونکي کې، ویل یې آسانه وي.

د View Composers د استعمال څخه ډډه وکړئ تر هغه پورې چې سل سلنه پرې پوه نشئ. پهر ډېرو حالاتو کې ډېرې نورې آسانې لارې هم شته چې هغه کار تر سره کړئ.

[🔝 بېرته تګ منځپانګو ته](#د-منځپانګو-یا-مطالبو-نوملړ
)
