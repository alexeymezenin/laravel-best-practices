![Laravel best practices](/images/logo-arabic.png?raw=true)

## <p dir="rtl">الترجمات</p>

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

<p dir='rtl' align='right'>هذا المحتوى لا يصنف بصفته مبادئ SOLID للارافيل أو أنماط التصميم، إلخ... هنا ستجد أفضل الممارسات التي يتم تجاهلها عادةً في مشاريع لارافيل الفعلية.</p>
 

## <p dir="rtl">الفهرس</p>

[<p dir="rtl">نمط المسؤولية الواحدة</p>](#1)

[<p dir="rtl">شيفرة أكثر في النماذج، شيفرة أقل في المتحكمات</p>](#2)

[<p dir="rtl">التحقق</p>](#3)

[<p dir="rtl">الشيفرات المنطقية يجب أن تكون في فئة خادمة منفصلة</p>](#4)

[<p dir="rtl">لا تكرر نفس الشيفرة</p>](#5)
،
[<p dir="rtl">يفضل استخدام نظام التعامل مع قواعد البيانات المسمى بـEloquent بدل استخدام باني الإستعلامات Query Builder أو الاستخدام المباشر لأوامر الإستعلامات SQL عبر raw، ويفضل استخدام المجموعات بدل المصفوفات</p>](#6)

[<p dir="rtl">تقليص المهام</p>](#7)

[<p dir="rtl">لا تقم بتنفيذ الإستعلامات داخل ملفات blade واستخدم التحميل الحثيث مشكلة (N+1)</p>](#8)

[<p dir="rtl">اضف التعليقات للشيفرة ويفضل استخدام صيغ التعليقات القياسية للمتغيرات والخواص والقيم المعادة إلخ</p>](#9)

[<p dir="rtl">لا تضع شيفرات js و css داخل ملفات Blade ولا تضع أي ششفرات HTML في فئات php</p>](#10)

[<p dir="rtl">استخدم ملفات الإعدادت واللغات، والثوابت بدلاً من النص داخل الشيفرة</p>](#11)

[<p dir="rtl">استخدم الأدوات القياسية المعتمدة من مجتمع لارافيل</p>](#12)

[<p dir="rtl">اتبع طريقة لارافيل في التسميات</p>](#13)

[<p dir="rtl">استخدم شيفرة أقصر قابلة للقراءة والفهم السريع قدر المستطاع</p>](#14)

[<p dir="rtl">استخدم الحاويات أو الواجهات بدلاً من الفئات الجديدة</p>](#15)

[<p dir="rtl">لا تقم بجلب البيانات من ملف `.env`</p>](#16)

[<p dir="rtl">احفظ البيانات في الشكل القياسي. استخدم المسترجعات والمُعدلات في تعديل شكل صيغة التاريخ</p>](#17)

[<p dir="rtl">ممارسات جيدة أخرى</p>](#18)
### <p dir="rtl">1</p>
### **<p dir="rtl">نمط المسئولية الواحدة</p>**

<p dir="rtl">وظيفة الفئة والطريقة يجب أن تكون مسئولية واحدة فقط، بمعنى آخر يجب ألا تكون الفئة أو الطريقة متعددة المهام ويجب أن تختص بمهمة واحدة فقط</p>

<p dir="rtl">❌ طريقة سيئة:</p>

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

<p dir="rtl">✔️ طريقة جيدة:</p>

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

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)

### <p dir="rtl">2</p>
### **<p dir="rtl">شيفرة أكثر في النماذج، شيفرة أقل في المتحكمات</p>**

<p dir="rtl">ضع كل الشيفرات الخاصة بالتعامل مع قواعد البيانات في فئات خاصة منفصلة ولا تضعها في المتحكمات</p>
<p dir="rtl">❌ طريقة سيئة:</p>

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

<p dir="rtl">✔️ طريقة جيدة:</p>

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

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">3</p>
### **<p dir="rtl">التحقق</p>**

<p dir="rtl">انقل شيفرات التحقق من المتحكمات إلى فئات الطلبات Request classes</p>
<p dir="rtl">❌ طريقة سيئة:</p>

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

<p dir="rtl">✔️ طريقة جيدة:</p>

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

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">4</p>
### **<p dir="rtl">الشيفرات المنطقية يجب أن تكون في فئة خادمة منفصلة</p>**

<p dir="rtl">المتحكم يجب أن يكون له مسئولية واحدة فقط، أنقل الشيفرات المنطقية لفئات خادمة منفصلة</p>

<p dir="rtl">❌ طريقة سيئة:</p>

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

<p dir="rtl">✔️ طريقة جيدة:</p>

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

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">5</p>
### **<p dir="rtl">لا تكرر نفس الشيفرة</p>**

<p dir="rtl">أعد استخدام نفس الشيفرة قدر المستطاع ولا تقم بإعادة كتابتها، سيساعدك هذا على عدم وجود أكثر من شيفرة لتنفيذ نفس المهمة، وإعادة استخدام قوالب blade وفئات التعامل مع قواعد البيانات Eloquent</p>

<p dir="rtl">مثال استخدم scope في فئات التعامل مع قواعد البيانات Eloquent</p>
 
<p dir="rtl">❌ طريقة سيئة:</p>

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

<p dir="rtl">✔️ طريقة جيدة:</p>

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

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">6</p>
### **<p dir="rtl">يفضل استخدام نظام التعامل مع قواعد البيانات المسمى بـEloquent بدل استخدام باني الإستعلامات Query Builder أو الاستخدام المباشر لأوامر الإستعلامات SQL عبر raw، ويفضل استخدام المجموعات بدل المصفوفات</p>**

<p dir="rtl">Eloquent يجعلك تكتب شيفرة قابلة للقراءة والصيانة. وأيضاً، Eloquent يحتوي على أدوات وخواص داخلية على سبيل الذكر: الحذف الناعم والأحداث والنطاقات إلخ...</p> 

<p dir="rtl">❌ طريقة سيئة:</p>

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

<p dir="rtl">✔️ طريقة جيدة:</p>

```php
Article::has('user.profile')->verified()->latest()->get();
```

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">7</p>
### **<p dir="rtl">تقليص المهام</p>**

<p dir="rtl">❌ طريقة سيئة:</p>

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

<p dir="rtl">✔️ طريقة جيدة:</p>

```php
$category->article()->create($request->validated());
```

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">8</p>
### **<p dir="rtl">لا تقم بتنفيذ الإستعلامات داخل ملفات blade واستخدم التحميل الحثيث مشكلة (N+1)</p>**

<p dir="rtl">❌ طريقة سيئة:</p>
<p dir="rtl">~لعدد 100 مستخدم سيُنفذ 101 استعلام على قاعدة البيانات</p>

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

<p dir="rtl">✔️ طريقة جيدة:</p>
<p dir="rtl">~لعدد 100 مستخدم سيُنفذ 2 استعلام فقط على قاعدة البيانات~</p>

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">9</p>
### **<p dir="rtl">اضف التعليقات للشيفرة، ويفضل استخدام صيغ التعليقات القياسية للمتغيرات والخواص والقيم المعادة إلخ</p>**

<p dir="rtl">❌ طريقة سيئة:</p>

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

<p dir="rtl">طريقة أفضل:</p>

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

<p dir="rtl">✔️ طريقة جيدة:</p>

```php
if ($this->hasJoins())
```

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">10</p>
### **<p dir="rtl">لا تضع شيفرات js و css داخل ملفات Blade ولا تضع أي شيفرات HTML في فئات php</p>**

<p dir="rtl">❌ طريقة سيئة:</p>

```php
let article = `{{ json_encode($article) }}`;
```

<p dir="rtl">✔️ طريقة أفضل:</p>

```php
<input id="article" type="hidden" value='@json($article)'>

Or

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

في ملف جافا سكريبت:

```javascript
let article = $('#article').val();
```

<p dir="rtl">الطريقة الأفضل هي استخدام الحزم الخاصة بنقل البيانات من PHP إلى جافا سكريبت.</p>


[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">11</p>
### **<p dir="rtl">استخدم ملفات الإعدادت واللغات، والثوابت بدلاً من النص داخل الشيفرة</p>**

<p dir="rtl">❌ طريقة سيئة:</p>

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('message', 'Your article has been added!');
```

<p dir="rtl">✔️ طريقة جيدة:</p>

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('message', __('app.article_added'));
```

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">12</p>
### **<p dir="rtl">استخدم الأدوات القياسية المعتمدة من مجتمع لارافيل</p>**

<p dir="rtl">يفضل استخدام الأدوات المدمجة مع إطار عمل لارافيل والحزم المقترحة من مجتمع لارفيل بدل استخدام غيرها، أي مطور سيعمل على تطبيقك في وقت لاحق سيحتاج إلى تعلم تلك الأدوات التي لا يشيع استخدامها في تطبيقات لارافيل، وأيضاً أطلب المساعدة من مجتمع لارافيل عندما تقرر الإعتماد على أحد الأدوات أو الحزم، ولا تجعل عميلك يدفع مقابل ذلك. </p>

الوظيفة | الأدوات القياسية  | أدوات الطرف الثالث
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

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">13</p>
### **<p dir="rtl">اتبع طريقة لارافيل في التسميات</p>**
<p dir="rtl">راجع <a href="http://www.php-fig.org/psr/psr-2">PSR standards</a></p>
 
 <p dir="rtl">وأيضا، راجع اصطلاح التسميات المقبول من جهه مجتمع لارافيل:</p>

ماذا | كيف | جيدة | سيئة
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

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">14</p>
### **<p dir="rtl">استخدم شيفرة أقصر قابلة للقراءة والفهم السريع قدر المستطاع</p>**

<p dir="rtl">❌ طريقة سيئة:</p>

```php
$request->session()->get('cart');
$request->input('name');
```

<p dir="rtl">✔️ طريقة جيدة:</p>

```php
session('cart');
$request->name;
```

<p dir="rtl">أمثلة أكثر:</p>

جمل مركبة | جمل أقصر وأكثر قابلية للقراءة
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

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">15</p>
### **<p dir="rtl">استخدم الحاويات أو الواجهات بدلاً من الفئات الجديدة</p>**

<p dir="rtl">إنشاء فئات جديدة يخلق شيئا من التشويش بين الفئات ويعقد عملة الإختبار، الأفضل الإعتماد على الحاويات أو الواجهات في هذا الأمر</p>

<p dir="rtl">❌ طريقة سيئة:</p>

```php
$user = new User;
$user->create($request->validated());
```

<p dir="rtl">✔️ طريقة جيدة:</p>

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">16</p>
### **<p dir="rtl">لا تقم بجلب البيانات من ملف  `.env` مباشرة</p>**

<p dir="rtl">مرر البيانات لملف الإعدادت ومن ثَم استخدم الدالة المساعدة `config()` لاستخدامها في جلب البيانات المخزنة في ملف الإعدادت داخل تطبيقك.</p>

<p dir="rtl">❌ طريقة سيئة:</p>

```php
$apiKey = env('API_KEY');
```

<p dir="rtl">✔️ طريقة جيدة:</p>

```php
// config/api.php
'key' => env('API_KEY'),

// Use the data
$apiKey = config('api.key');
```

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">17</p>
### **<p dir="rtl">خزن التواريخ بأشكالها القياسية، واستخدم المسترجعات والمُعدلات لتعديل صيغة التواريخ كما تريد.</p>**

<p dir="rtl">❌ طريقة سيئة:</p>

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

<p dir="rtl">✔️ طريقة جيدة:</p>

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

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
### <p dir="rtl">18</p>
### **<p dir="rtl">ممارسات جيدة أخرى</p>**

<p dir="rtl">لا تضع أي شيفرة برمجية في ملفات الموجهات.</p>

<p dir="rtl">قلل من استخدامك الشيفرات البرمجية المنطقية في ملفات العرض blade.</p>

[<p dir="rtl">🔝 الرجوع للفهرس</p>](#الفهرس)
