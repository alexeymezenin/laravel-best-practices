![Laravel ベストプラクティス](/images/logo-japanese.png?raw=true)

翻訳:

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

[Deutsche](german.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

[Italiana](italian.md) (by [Sujal Patel](https://github.com/sujalpatel2209))

これはSOLID原則やパターンなどをLavavelに適用させたものではありません。
ここでは、実際のLaravelプロジェクトでは通常無視されるベストプラクティスを見つけることができます。

## コンテンツ

[単一責任の原則](#単一責任の原則)

[ファットモデル, スキニーコントローラ](#ファットモデル、スキニーコントローラ)

[バリデーション](#バリデーション)

[ビジネスロジックはサービスクラスの中に書く](#ビジネスロジックはサービスクラスの中に書く)

[繰り返し書かない (DRY)](#繰り返し書かない-(DRY))

[クエリビルダや生のSQLクエリよりもEloquentを優先して使い、配列よりもコレクションを優先する](#クエリビルダや生のSQLクエリよりもEloquentを優先して使い、配列よりもコレクションを優先する)

[マスアサインメント](#マスアサインメント)

[Bladeテンプレート内でクエリを実行しない。Eager Lodingを使う(N + 1問題)](#Bladeテンプレート内でクエリを実行しない。Eager-Lodingを使う(N-+-1問題))

[コメントを書く。ただしコメントよりも説明的なメソッド名と変数名を付けるほうが良い](#コメントを書く。ただしコメントよりも説明的なメソッド名と変数名を付けるほうが良い)

[JSとCSSをBladeテンプレートの中に入れない、PHPクラスの中にHTMLを入れない](#JSとCSSをBladeテンプレートの中に入れない、PHPクラスの中にHTMLを入れない)

[コード内の文字列の代わりにconfigファイルとlanguageのファイル、定数を使う](#コード内の文字列の代わりにconfigファイルとlanguageのファイル、定数を使う)

[コミュニティに受け入れられた標準のLaravelツールを使う](#コミュニティに受け入れられた標準のLaravelツールを使う)

[Laravelの命名規則に従う](#Laravelの命名規則に従う)

[できるだけ短く読みやすい構文で書く](#できるだけ短く読みやすい構文で書く)

[newの代わりにIoCコンテナもしくはファサードを使う](#newの代わりにIoCコンテナもしくはファサードを使う)

[`.env`ファイルのデータを直接参照しない](#`.env`ファイルのデータを直接参照しない)

[日付を標準フォーマットで保存する。アクセサとミューテータを使って日付フォーマットを変更する](#日付を標準フォーマットで保存する。アクセサとミューテータを使って日付フォーマットを変更する)

[その他 グッドプラクティス](#その他-グッドプラクティス)

### **単一責任の原則**

クラスとメソッドは1つの責任だけを持つべきです。

Bad:

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

[🔝 コンテンツに戻る](#コンテンツ)

### **ファットモデル、スキニーコントローラ**

DBに関連するすべてのロジックはEloquentモデルに入れるか、もしクエリビルダもしくは生のSQLクエリを使用する場合はレポジトリークラスに入れます。

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

[🔝 コンテンツに戻る](#コンテンツ)

### **バリデーション**

バリデーションはコントローラからリクエストクラスに移動させます。

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

[🔝 コンテンツに戻る](#コンテンツ)

### **ビジネスロジックはサービスクラスの中に書く**

コントローラはただ1つの責任だけを持たないといけません、そのためビジネスロジックはコントローラからサービスクラスに移動させます。

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

[🔝 コンテンツに戻る](#コンテンツ)

### **繰り返し書かない (DRY)**

可能であればコードを再利用します。単一責任の原則は重複を避けることに役立ちます。また、Bladeテンプレートを再利用したり、Eloquentのスコープなどを使用したりします。

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

[🔝 コンテンツに戻る](#コンテンツ)

### **クエリビルダや生のSQLクエリよりもEloquentを優先して使い、配列よりもコレクションを優先する**

Eloquentにより読みやすくメンテナンスしやすいコードを書くことができます。また、Eloquentには論理削除、イベント、スコープなどの優れた組み込みツールがあります。

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

[🔝 コンテンツに戻る](#コンテンツ)

### **マスアサインメント**

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

[🔝 コンテンツに戻る](#コンテンツ)

### **Bladeテンプレート内でクエリを実行しない。Eager Lodingを使う(N + 1問題)**

Bad (100ユーザに対して、101回のDBクエリが実行される):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Good (100ユーザに対して、2回のDBクエリが実行される):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 コンテンツに戻る](#コンテンツ)

### **コメントを書く。ただしコメントよりも説明的なメソッド名と変数名を付けるほうが良い**

Bad:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

Better:

```php
// Determine if there are any joins.
if (count((array) $builder->getQuery()->joins) > 0)
```

Good:

```php
if ($this->hasJoins())
```

[🔝 コンテンツに戻る](#コンテンツ)

### **JSとCSSをBladeテンプレートの中に入れない、PHPクラスの中にHTMLを入れない**

Bad:

```php
let article = `{{ json_encode($article) }}`;
```

Better:

```php
<input id="article" type="hidden" value='@json($article)'>

Or

<button class="js-fav-article" data-article='@json($article)'>{{ $article->name }}<button>
```

JavaScript ファイルで以下のように記述します:

```javascript
let article = $('#article').val();
```

もっとも良い方法は、データを転送するためJSパッケージに特別なPHPを使用することです。


[🔝 コンテンツに戻る](#コンテンツ)

### **コード内の文字列の代わりにconfigファイルとlanguageのファイル、定数を使う**

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

[🔝 コンテンツに戻る](#コンテンツ)

### **コミュニティに受け入れられた標準のLaravelツールを使う**

サードパーティ製のパッケージやツールの代わりに、Laravel標準機能とコミュニティパッケージを使うことを推奨します。将来あなたと共に働くことになるどの開発者も新しいツールを学習する必要があります。また、サードパーティ製のパッケージやツールを使用している場合は、Laravelコミュニティから助けを得る機会が大幅に少なくなります。あなたのクライアントにその代金を払わせないでください。

タスク | 標準ツール | サードパーティ製ツール
------------ | ------------- | -------------
認可 | Policies | Entrust, Sentinel または他のパッケージ
アセットコンパイル | Laravel Mix | Grunt, Gulp, サードパーティ製パッケージ
開発環境 | Homestead | Docker
デプロイ | Laravel Forge | Deployer またはその他ソリューション
単体テスト| PHPUnit, Mockery | Phpspec
ブラウザテスト | Laravel Dusk | Codeception
DB | Eloquent | SQL, Doctrine
テンプレート | Blade | Twig
データの取り扱い | Laravel collections | Arrays
フォームバリデーション | Request classes | サードパーティ製パッケージ、コントローラ内でバリデーション
認証 | 標準組み込み | サードパーティ製パッケージ、独自実装
API 認証 | Laravel Passport | サードパーティ製の JWT や OAuth パッケージ
API作成 | 標準組み込み | Dingo API や類似パッケージ
DB構造の取り扱い | Migrations | 直接DB構造を扱う
ローカライゼーション | 標準組み込み | サードパーティ製パッケージ
リアルタイムユーザインターフェース | Laravel Echo, Pusher | サードパーティ製パッケージ または直接Webソケットを扱う
テストデータ生成 | Seeder classes, Model Factories, Faker | 手動でテストデータを作成
タスクスケジューリング | Laravel Task Scheduler | スクリプトやサードパーティ製パッケージ
DB | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝 コンテンツに戻る](#コンテンツ)

### **Laravelの命名規則に従う**

 [PSR](http://www.php-fig.org/psr/psr-2/)に従います。
 
 また、Laravelコミュニティに受け入れられた命名規則に従います。

対象 | 規則 | Good | Bad
------------ | ------------- | ------------- | -------------
コントローラ | 単数形 | ArticleController | ~~ArticlesController~~
ルート | 複数形 | articles/1 | ~~article/1~~
名前付きルート | スネークケースとドット表記 | users.show_active | ~~users.show-active, show-active-users~~
モデル | 単数形 | User | ~~Users~~
hasOne または belongsTo 関係 | 単数形 | articleComment | ~~articleComments, article_comment~~
その他すべての関係 | 複数形 | articleComments | ~~articleComment, article_comments~~
テーブル | 複数形 | article_comments | ~~article_comment, articleComments~~
Pivotテーブル | 単数形 モデル名のアルファベット順 | article_user | ~~user_article, articles_users~~
テーブルカラム | スネークケース モデル名は含めない | meta_title | ~~MetaTitle; article_meta_title~~
モデルプロパティ | スネークケース | $model->created_at | ~~$model->createdAt~~
外部キー | 単数形 モデル名の最後に_idをつける | article_id | ~~ArticleId, id_article, articles_id~~
主キー | - | id | ~~custom_id~~
マイグレーション | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
メソッド | キャメルケース | getAll | ~~get_all~~
リソースコントローラのメソッド | [一覧](https://laravel.com/docs/master/controllers#resource-controllers) | store | ~~saveArticle~~
テストクラスのメソッド | キャメルケース | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
変数 | キャメルケース | $articlesWithAuthor | ~~$articles_with_author~~
コレクション | 説明的、 複数形 | $activeUsers = User::active()->get() | ~~$active, $data~~
オブジェクト | 説明的, 単数形 | $activeUser = User::active()->first() | ~~$users, $obj~~
設定ファイルと言語ファイルのインデックス | スネークケース | articles_enabled | ~~ArticlesEnabled; articles-enabled~~
ビュー | ケバブケース | show-filtered.blade.php | ~~showFiltered.blade.php, show_filtered.blade.php~~
コンフィグ | スネークケース | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
契約 (インターフェイス) | 形容詞または名詞 | Authenticatable | ~~AuthenticationInterface, IAuthentication~~
Trait | 形容詞 | Notifiable | ~~NotificationTrait~~

[🔝 コンテンツに戻る](#コンテンツ)

### **できるだけ短く読みやすい構文で書く**

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

さらなる例:

一般的な構文 | 短く読みやすい構文
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

[🔝 コンテンツに戻る](#コンテンツ)

### **newの代わりにIoCコンテナもしくはファサードを使う**

new構文はクラス間の密結合を生み出し、テストすることを難しくします。IoCコンテナまたはファサードを代わりに使います。

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

[🔝 コンテンツに戻る](#コンテンツ)

### **`.env`ファイルのデータを直接参照しない**

代わりにconfigファイルへデータを渡します。そして、アプリケーション内でデータを参照する場合は`config()`ヘルパー関数を使います。

Bad:

```php
$apiKey = env('API_KEY');
```

Good:

```php
// config/api.php
'key' => env('API_KEY'),

// データを使用する
$apiKey = config('api.key');
```

[🔝 コンテンツに戻る](#コンテンツ)

### **日付を標準フォーマットで保存する。アクセサとミューテータを使って日付フォーマットを変更する**

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

[🔝 コンテンツに戻る](#コンテンツ)

### **その他 グッドプラクティス**

ルートファイルにはロジックを入れないでください。

Bladeテンプレートの中でVanilla PHP(標準のPHPコードを記述すること)の使用は最小限にします。

[🔝 コンテンツに戻る](#コンテンツ)
