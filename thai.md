![Laravel best practices](./images/logo-thai.png?raw=true)

การแปลภาษา:

[English](README.md)

[Nederlands](https://github.com/Protoqol/Beste-Laravel-Praktijken) (by [Protoqol](https://github.com/Protoqol))

[한국어](https://github.com/xotrs/laravel-best-practices) (by [cherrypick](https://github.com/xotrs))

[Українська](ukrainian.md) (by [Tenevyk](https://github.com/tenevyk))

[Русский](russian.md)

[日本語](japanese.md) (by [2bo](https://github.com/2bo))

[漢語](chinese.md) (by [xiaoyi](https://github.com/Shiloh520))

[فارسی](persian.md) (by [amirhossein baghaie](https://github.com/amirbagh75))

[Português](https://github.com/jonaselan/laravel-best-practices) (by [jonaselan](https://github.com/jonaselan))

[Tiếng Việt](https://chungnguyen.xyz/posts/code-laravel-lam-sao-cho-chuan) (by [Chung Nguyễn](https://github.com/nguyentranchung))

[Español](spanish.md) (by [César Escudero](https://github.com/cedaesca))

[Français](french.md) (by [Mikayil S.](https://github.com/mikayilsrt))

[Polski](https://github.com/maciejjeziorski/laravel-best-practices-pl) (by [Maciej Jeziorski](https://github.com/maciejjeziorski))

[Türkçe](turkish.md) (by [Burak](https://github.com/ikidnapmyself))

[ภาษาไทย](thai.md) (by [Kongvut](https://github.com/kongvut/laravel-best-practices))

[Deutsche](german.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Italiana](italian.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

เอกสารนี้ไม่ใช่การดัดแปลงหลักการ SOLID หรือรูปแบบและอื่น ๆ ของ Laravel โดยบทความนี้คุณจะพบแนวทางปฏิบัติในการ Coding ที่ดีที่สุด ซึ่งหลายคนมักจะละเลยในงานโปรเจค Laravel จริงของคุณ

## เนื้อหา<a name="contents"></a>

[1. แนวทางรูปแบบการตอบกลับเพียงที่เดียว [Single responsibility principle]](#single-responsibility-principle)

[2. ความอ้วนของ Models และ Controllers ขนาดเล็ก [Fat models, skinny controllers]](#fat-models-skinny-controllers)

[3. การตรวจสอบ [Validation]](#validation)

[4. Business logic ควรจะอยู่ในคลาส Service [Business logic should be in service class]](#business-logic-should-be-in-service-class)

[5. อย่าเรียกตัวเองซ้ำ [Don't repeat yourself (DRY)]](#dont-repeat-yourself-dry)

[6. ควรจะใช้ Eloquent มากกว่า Query Builder หรือ Raw SQL queries และชอบที่จะใช้ collections มากกว่า arrays [Prefer to use Eloquent over using Query Builder and raw SQL queries. Prefer collections over arrays]](#prefer-to-use-eloquent-over-using-query-builder-and-raw-sql-queries-prefer-collections-over-arrays)

[7. ความอ้วนเบอะบะของการกำหนดค่า [Mass assignment]](#mass-assignment)

[8. ไม่ควรที่จะเรียกรัน Queries ในเทมเพลต Blade และใช้เทคนิค Eager loading แทน (เพราะปัญหา N + 1) [Do not execute queries in Blade templates and use eager loading (N + 1 problem)]](#do-not-execute-queries-in-blade-templates-and-use-eager-loading-n--1-problem)

[9. หมั่นคอมเม้นโค้ดของคุณ อีกทั้งควรจะอธิบายการทำงานของเมธอด และชื่อตัวแปร มากกว่าการคอมเม้นเฉย ๆ  [Comment your code, but prefer descriptive method and variable names over comments]](#comment-your-code-but-prefer-descriptive-method-and-variable-names-over-comments)

[10. อย่าใส่ JS และ CSS ในเทมเพลต Blade และอย่าใส่ HTML ใด ๆ ในคลาส PHP [Do not put JS and CSS in Blade templates and do not put any HTML in PHP classes]](#do-not-put-js-and-css-in-blade-templates-and-do-not-put-any-html-in-php-classes)

[11. ใช้ค่าคงที่ Config และไฟล์ภาษา แทนการใส่ข้อความตรง ๆ ลงในโค้ด [Use config and language files, constants instead of text in the code]](#use-config-and-language-files-constants-instead-of-text-in-the-code)

[12. ใช้เครื่องมือ Laravel มาตรฐานที่ชุมชนยอมรับ [Use standard Laravel tools accepted by community]](#use-standard-laravel-tools-accepted-by-community)

[13. ปฏิบัติตามแนวทางการตั้งชื่อต่าง ๆ ตามกรอบกติกา Laravel [Follow Laravel naming conventions]](#follow-laravel-naming-conventions)

[14. ใช้ไวยากรณ์ที่สั้นกว่าและอ่านง่ายกว่าถ้าเป็นไปได้ [Use shorter and more readable syntax where possible]](#use-shorter-and-more-readable-syntax-where-possible)

[15. ใช้ชุดรูปแบบ IoC หรือ Facades แทนเรียกคลาสใหม่ [Use IoC container or facades instead of new Class]](#use-ioc-container-or-facades-instead-of-new-class)

[16. อย่าเรียกข้อมูลจากไฟล์ `.env` โดยตรง [Do not get data from the `.env` file directly]](#do-not-get-data-from-the-env-file-directly)

[17. เก็บวันที่ในรูปแบบมาตรฐาน อีกทั้งใช้ Accessors และ Mutators เพื่อแก้ไขรูปแบบวันที่ [Store dates in the standard format. Use accessors and mutators to modify date format]](#store-dates-in-the-standard-format-use-accessors-and-mutators-to-modify-date-format)

[- แนวทางการปฏิบัติที่ดีอื่น ๆ [Other good practices]](#other-good-practices)

##

### <a name="single-responsibility-principle">1. แนวทางรูปแบบการตอบกลับเพียงที่เดียว [Single responsibility principle]</a>

ภายในคลาส ซึ่งในเมธอดควรมีการ Return ค่าเพียงที่เดียว

ที่แย่:

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

ที่ดี:

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

### <a name="fat-models-skinny-controllers">2. ความอ้วนของ Models และ Controllers ขนาดเล็ก [Fat models, skinny controllers]</a>

เขียนความสัมพันธ์ฐานข้อมูลทั้งหมด (รวมทั้งแบบ Query Builder หรือ raw SQL queries) ลงใน Model Eloquent หรือในคลาส Repository สร้างเป็น Method สำหรับเรียกใช้งาน เพื่อลดความซ้ำซ้อนของ Logic และขนาด Controllers เพื่อให้มีขนาดเล็กลง

ที่แย่:

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

ที่ดี:

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

### <a name="validation">3. การตรวจสอบ [Validation]</a>

ย้ายการตรวจสอบ Validation จาก Controllers ไปที่ Request classes แทน

ที่แย่:

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

ที่ดี:

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

### <a name="business-logic-should-be-in-service-class">4. Business logic ควรจะอยู่ในคลาส Service [Business logic should be in service class]</a>

เพื่อให้ Method ภายใน Controller มีขนาดที่เล็กลง ดังนั้นควรย้าย Business logic จาก Controllers ไปที่คลาส Service แทน

ที่แย่:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

ที่ดี:

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

### <a name="dont-repeat-yourself-dry">5. อย่าเรียกตัวเองซ้ำ [Don't repeat yourself (DRY)]</a>

ทำการ Reuse โค้ดเพื่อช่วยหลีกเลี่ยงโค้ดที่ซ้ำซ้อน เช่นเดียวกันกับการ Reuse เทมเพลต Blade โดยสำหรับ Model ให้ใช้ Eloquent scopes ช่วยเป็นต้น

ที่แย่:

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

ที่ดี:

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

### <a name="prefer-to-use-eloquent-over-using-query-builder-and-raw-sql-queries-prefer-collections-over-arrays">6. ควรที่จะใช้ Eloquent มากกว่า Query Builder หรือ Raw SQL queries และชอบที่จะใช้ collections มากกว่า arrays [Prefer to use Eloquent over using Query Builder and raw SQL queries. Prefer collections over arrays]</a>

Eloquent ช่วยให้คุณสามารถอ่านโค้ดเข้าใจง่าย
และบำรุงรักษาได้ง่าย นอกจากนี้ Eloquent ยังมีเครื่องมือในตัวที่ยอดเยี่ยม เช่น soft deletes, events, scopes เป็นต้น

ที่แย่:

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

ที่ดี:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 Back to contents](#contents)

### <a name="mass-assignment">7. ความอ้วนเบอะบะของการกำหนดค่า [Mass assignment]</a>

ที่แย่:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

ที่ดี:

```php
$category->article()->create($request->validated());
```

[🔝 Back to contents](#contents)

### <a name="do-not-execute-queries-in-blade-templates-and-use-eager-loading-n--1-problem">8. ไม่ควรที่จะเรียกรัน Queries ในเทมเพลต Blade และใช้เทคนิค Eager loading แทน (เพราะปัญหา N + 1) [Do not execute queries in Blade templates and use eager loading (N + 1 problem)]</a>

ที่แย่: (สำหรับข้อมูลตารางผู้ใช้ 100 users โดยจะมีการรันคำสั่ง Queries 101 ครั้ง):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

ที่ดี: (สำหรับข้อมูลตารางผู้ใช้ 100 users โดยจะมีการรันคำสั่ง Queries 2 ครั้ง):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 Back to contents](#contents)

### <a name="comment-your-code-but-prefer-descriptive-method-and-variable-names-over-comments">9. หมั่นคอมเม้นโค้ดของคุณ อีกทั้งควรจะอธิบายการทำงานของเมธอด และชื่อตัวแปร มากกว่าการคอมเม้นเฉย ๆ  [Comment your code, but prefer descriptive method and variable names over comments]</a>

ที่แย่:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

ที่ควร:

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

ที่ดี:

```php
if ($this->hasJoins())
```

[🔝 Back to contents](#contents)

### <a name="do-not-put-js-and-css-in-blade-templates-and-do-not-put-any-html-in-php-classes">10. อย่าใส่ JS และ CSS ในเทมเพลต Blade และอย่าใส่ HTML ใด ๆ ในคลาส PHP [Do not put JS and CSS in Blade templates and do not put any HTML in PHP classes]</a>

ที่แย่:

```php
let article = `{{ json_encode($article) }}`;
```

ที่ควร:

```php
<input id="article" type="hidden" value='@json($article)'>

หรือ

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

ที่ไฟล์ Javascript:

```javascript
let article = $('#article').val();
```

[🔝 Back to contents](#contents)

### <a name="use-config-and-language-files-constants-instead-of-text-in-the-code">11. ใช้ค่าคงที่ Config และไฟล์ภาษา แทนการใส่ข้อความตรง ๆ ลงในโค้ด [Use config and language files, constants instead of text in the code]</a>

ที่แย่:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

ที่ดี:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[🔝 Back to contents](#contents)

### <a name="use-standard-laravel-tools-accepted-by-community">12. ใช้เครื่องมือ Laravel มาตรฐานที่ชุมชนยอมรับ [Use standard Laravel tools accepted by community]</a>

ควรที่จะใช้ฟังก์ชันมาตรฐานที่ Built-in มาใน Laravel และแพ็คเกจคอมมิวนิตี้ยอดนิยม แทนการใช้แพ็คเกจและเครื่องมือของ 3rd party ปัญหาก็คือนักพัฒนาใหม่ ๆ ที่จะมาพัฒนาร่วมกับแอพของคุณในอนาคต จะต้องเรียนรู้เครื่องมือใหม่ ๆ (3rd party packages) นอกจากนี้โอกาสที่จะได้รับความช่วยเหลือจากชุมชน Laravel จะน้อยอย่างมากเมื่อคุณใช้แพ็คเกจหรือเครื่องมือของ 3rd party อีกทั้งอย่าทำให้ลูกค้าของคุณจ่ายเงินเพิ่มเติมสำหรับสิ่งพวกนั้น (Licenses)

*เพิ่มเติม:
ปัญหาอีกอย่างของการใช้ 3rd party packages คืออาจจะถูกละเลยการอัพเดทแพ็คเกจสำหรับคุณสมบัติใหม่ ๆ หรือฟังก์ชันความปลอดภัย

ฟังก์ชัน | เครื่องมือมาตรฐาน | เครื่องมือ 3rd party
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

### <a name="follow-laravel-naming-conventions">13. ปฏิบัติตามแนวทางการตั้งชื่อต่าง ๆ ตามกรอบกติกา Laravel [Follow Laravel naming conventions]</a>

ปฏิบัติตามแนวทาง [มาตรฐาน PSR](http://www.php-fig.org/psr/psr-2/).
 
นอกจากนี้ให้ปฏิบัติตามแบบแผนการตั้งชื่อที่ชุมชน Laravel ยอมรับ:

เกี่ยวกับ | แนวทาง | ที่ดี | ที่แย่
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

### <a name="use-shorter-and-more-readable-syntax-where-possible">14. ใช้ไวยากรณ์ที่สั้นกว่าและอ่านง่ายกว่าถ้าเป็นไปได้ [Use shorter and more readable syntax where possible]</a>

ที่แย่:

```php
$request->session()->get('cart');
$request->input('name');
```

ที่ดี:

```php
session('cart');
$request->name;
```

ตัวอย่างอื่น ๆ เพิ่มเติม:

Syntax ทั่วไป | Syntax ที่สั้นและอ่านง่ายกว่า
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

### <a name="use-ioc-container-or-facades-instead-of-new-class">15. ใช้ชุดรูปแบบ IoC หรือ Facades แทนเรียกคลาสใหม่ [Use IoC container or facades instead of new Class]</a>

การเรียกคลาสใหม่ระหว่างคลาสเป็นอะไรที่ซับซ้อนและซ้ำซ้อน แนะนำให้ใช้หลัก IoC หรือ Facades แทน

*เพิ่มเติม: ในกรณี Controller ของ Model เดียวกันแนะนำให้ทำดังตัวอย่าง

ที่แย่:

```php
$user = new User;
$user->create($request->validated());
```

ที่ดี:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 Back to contents](#contents)

### <a name="do-not-get-data-from-the-env-file-directly">16. อย่าเรียกข้อมูลจากไฟล์ `.env` โดยตรง [Do not get data from the `.env` file directly]</a>

แนะนำให้ส่งผ่านข้อมูลเพื่อกำหนดค่าจากไฟล์ Config แทน จากนั้นเรียกใช้ฟังก์ชันตัวช่วย `config ()` เพื่อเรียกใช้ข้อมูลในแอปพลิเคชัน

ที่แย่:

```php
$apiKey = env('API_KEY');
```

ที่ดี:

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[🔝 Back to contents](#contents)

### <a name="store-dates-in-the-standard-format-use-accessors-and-mutators-to-modify-date-format">17. เก็บวันที่ในรูปแบบมาตรฐาน อีกทั้งใช้ Accessors และ Mutators เพื่อแก้ไขรูปแบบวันที่ [Store dates in the standard format. Use accessors and mutators to modify date format]</a>

ที่แย่:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

ที่ดี:

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

### <a name="other-good-practices">- แนวทางการปฏิบัติที่ดีอื่น ๆ [Other good practices]</a>

- อย่าใส่ Logic ใด ๆ ในไฟล์ routes
- ลดการใช้ Vanilla PHP ให้น้อยที่สุดในเทมเพลต Blade

[🔝 Back to contents](#contents)
