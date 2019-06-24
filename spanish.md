![Laravel best practices](/images/logo-english.png?raw=true)

No se trata de una adaptación de principios SÓLIDOS para Laravel ni patrones. Aquí encontrarás las mejores prácticas que, por lo general, son ignoradas en proyectos de la vida real.

## Índice de contenido

[Principio de propósito único](#principio-de-proposito-unico)

[Modelos gordos, controladores delgados](#modelos-gordos-controladores-delgados)

[Validación](#validacion)

[La lógica de negocios debe estar en una clase ayudante](#la-logica-de-negocios-debe-estar-en-una-clase-ayudante)

[No te repitas (DRY)](#no-te-repitas-dry)

[Prioriza el uso de Eloquent por sobre el constructor de consultas y consultas puras. Prioriza las colecciones sobre los arreglos](#prioriza-el-uso-de-eloquent-por-sobre-el-constructor-de-consultas-y-consultas-puras-prioriza-las-colecciones-sobre-los-arreglos)

[Asignación en masa](#asignacion-en-masa)

[No ejecutes consultas en las plantillas blade y utiliza el cargado prematuro (Problema N + 1)](#no-ejecutes-consultas-en-las-plantillas-blade-y-utiliza-el-cargado-prematuro-problema-n--1))

[Comenta tu código, pero prioriza los métodos y nombres de variables descriptivas por sobre los comentarios](#comenta-tu-codigo-pero-prioriza-los-metodos-y-nombres-de-variables-descriptivas-por-sobre-los-comentarios)

[No coloques JS ni CSS en las plantillas blade y no coloques HTML en clases de PHP](#no-coloques-js-ni-css-en-las-plantillas-blade-y-no-coloques-html-en-clases-de-php)

[Utiliza los archivos de configuración y lenguaje en lugar de texto en el código](#utiliza-los-archivos-de-configuracion-y-lenguaje-en-lugar-de-texto-en-el-codigo)

[Utiliza las herramientas estándar de Laravel aceptadas por la comunidad](#utiliza-las-herramientas-estandar-de-laravel-aceptadas-por-la-comunidad)

[Sigue la convención de Laravel para los nombres](#sigue-la-convencion-de-laravel-para-los-nombres)

[Utiliza sintaxis cortas y legibles siempre que sea posible](#utiliza-sintaxis-cortas-y-legibles-siempre-que-sea-posible)

[Utiliza contenedores IoC o fachadas en lugar de new Class](#utiliza-contenedores-ioc-o-fachadas-en-lugar-de-new-class)

[No saques información directamente del archivo .env](#no-saques-informacion-directamente-del-archivo-env)

[Guarda las fechas en los formatos estándares. Utiliza los accessors y mutators para modificar el formato](#guarda-las-fechas-en-los-formatos-estandares-utiliza-los-accessors-y-mutators-para-modificar-el-formato)

[Otras buenas prácticas](#otras-buenas-practicas)

### **Principio de proposito unico**

Las clases y los métodos deben tener un solo propósito.

Malo:

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

Bueno:

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

[🔝 Volver al índice](#índice-de-contenido)

### **Modelos gordos, controladores delgados**

Coloca toda la lógica relacionada a la base de datos en los modelos de Eloquent o en un repositorio de clases si estás utilizando el constructor de consultas o consultas SQL puras.

Malo:

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

Bueno:

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

[🔝 Volver al índice](#índice-de-contenido)

### **Validacion**

Quita las validaciones de los controladores y colócalas en clases Request

Malo:

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

Bueno:

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

[🔝 Volver al índice](#índice-de-contenido)

### **La logica de negocios debe estar en una clase ayudante**

Un controlador solo debe tener un propósito, así que mueve la lógica de negocio fuera de los controladores y colócala en clases ayudantes.

Malo:

```php
public function store(Request $request)
{
    if ($request->hasFile('image')) {
        $request->file('image')->move(public_path('images') . 'temp');
    }
    
    ....
}
```

Bueno:

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

[🔝 Volver al índice](#índice-de-contenido)

### **No te repitas (DRY)**

Reutiliza código cada vez que puedas.  El SRP te ayuda a evitar la duplicación. Reutiliza también las plantillas blade, utiliza eloquent scope, etcétera.

Malo:

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

Bueno:

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

[🔝 Volver al índice](#índice-de-contenido)

### **Prioriza el uso de Eloquent por sobre el constructor de consultas y consultas puras. Prioriza las colecciones sobre los arreglos**

Eloquent te permite escribir código legible y mantenible. Eloquent también tiene muy buenas herramientas preconstruidas como los borrados leves, eventos, scopes, etcétera.

Malo:

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

Bueno:

```php
Article::has('user.profile')->verified()->latest()->get();
```

[🔝 Volver al índice](#índice-de-contenido)

### **Asignacion en masa**

Malo:

```php
$article = new Article;
$article->title = $request->title;
$article->content = $request->content;
$article->verified = $request->verified;
// Add category to article
$article->category_id = $category->id;
$article->save();
```

Bueno:

```php
$category->article()->create($request->validated());
```

[🔝 Volver al índice](#índice-de-contenido)

### **No ejecutes consultas en las plantillas blade y utiliza el cargado prematuro (Problema N + 1)**

Malo (Para 100 usuarios, se ejecutarán 101 consultas):

```php
@foreach (User::all() as $user)
    {{ $user->profile->name }}
@endforeach
```

Bueno (Para 100 usuarios, se ejecutarán 2 consultas):

```php
$users = User::with('profile')->get();

...

@foreach ($users as $user)
    {{ $user->profile->name }}
@endforeach
```

[🔝 Volver al índice](#índice-de-contenido)

### **Comenta tu codigo, pero prioriza los metodos y nombres de variables descriptivas por sobre los comentarios**

Malo:

```php
if (count((array) $builder->getQuery()->joins) > 0)
```

Mejor:

```php
// Determina si hay alguna unión
if (count((array) $builder->getQuery()->joins) > 0)
```

Bueno:

```php
if ($this->hasJoins())
```

[🔝 Volver al índice](#índice-de-contenido)

### **No coloques JS ni CSS en las plantillas blade y no coloques HTML en clases de PHP**

Malo:

```php
let article = `{{ json_encode($article) }}`;
```

Mejor:

```php
<input id="article" type="hidden" value="@json($article)">

Or

<button class="js-fav-article" data-article="@json($article)">{{ $article->name }}<button>
```

En el archivo JavaScript:

```javascript
let article = $('#article').val();
```

La mejor ruta es utilizar algún paquete especializado para transferir información de PHP a JS.

[🔝 Volver al índice](#índice-de-contenido)

### **Utiliza los archivos de configuracion y lenguaje en lugar de texto en el codigo**

Malo:

```php
public function isNormal()
{
    return $article->type === 'normal';
}

return back()->with('mensaje', '¡Su artículo ha sido agregado!');
```

Bueno:

```php
public function isNormal()
{
    return $article->type === Article::TYPE_NORMAL;
}

return back()->with('mensaje', __('app.articulo_agregado'));
```

[🔝 Volver al índice](#índice-de-contenido)

### **Utiliza las herramientas estandar de Laravel aceptadas por la comunidad**

Prioriza la utilización de funcionalidades integradas y los paquetes de la comunidad en lugar de utilizar paquetes o herramientas de terceros ya que cualquier desarrollador que vaya a trabajar a futuro en tu aplicación necesitará aprender a utilizar nuevas herramientas. También, las probabilidades de recibir ayuda de la comunidad son significativamente más bajas cuando utilizas herramientas o paquetes de terceros. No hagas que tu cliente pague por ello.

Tarea | Herramienta estándar | Herramientas de terceras personas
------------ | ------------- | -------------
Autorización | Policies | Entrust, Sentinel y otros paquetes
Compilar assets | Laravel Mix | Grunt, Gulp, paquetes de terceros
Entorno de desarrollo | Homestead | Docker
Deployment | Laravel Forge | Deployer y otras soluciones
Unit testing | PHPUnit, Mockery | Phpspec
Testeo en el navegador | Laravel Dusk | Codeception
Base de datos | Eloquent | SQL, Doctrine
Plantillas | Blade | Twig
Trabajar con data | Laravel collections | Arreglos
Validación de formularios | Clases Request | Paquetes de terceros, validación en el controlador
Autenticación | Integrada | Paquetes de terceros, solución propia
Autenticación para API's | Laravel Passport | Paquetes oAuth y JWT de terceros
Creación de API's | Integrado | Dingo API y paquetes similares
Estructura de la base de datos | Migraciones | Trabajar directamente con la estructura
Localización | Integrada | Paquetes de terceros
Interfaces en tiempo real | Laravel Echo, Pusher | Paquetes de terceros y trabajar directamente con WebSockets.
Generación de información de prueba | Clases Seeder, Fábricas de modelos, Faker | Crear la información manualmente
Programación de tareas | Laravel Task Scheduler | Scripts y paquetes de terceros
Base de datos | MySQL, PostgreSQL, SQLite, SQL Server | MongoDB

[🔝 Volver al índice](#índice-de-contenido)

### **Sigue la convencion de Laravel para los nombres**

 Sigue los [estándares PSR](http://www.php-fig.org/psr/psr-2/).
 
 También, sigue la convención aceptada por la comunidad:

Qué | Cómo | Bueno | Malo
------------ | ------------- | ------------- | -------------
Controlador | singular | ControladorArticulo | ~~ControladorArticulos~~
Ruta | plural | articulos/1 | ~~articulo/1~~
Nombres de rutas | snake_case con notación de puntos | usuarios.mostrar_activos | ~~usuarios.mostrar-activos, mostrar-usuarios-activos~~
Modelo | singular | Usuario | ~~Usuarios~~
Relaciones hasOne o belongsTo | singular | comentarioArticulo | ~~comentariosArticulo, comentario_articulo~~
Cualquier otra relación | plural | comentariosArticulo | ~~comentarioArticulo, comentarios_articulo~~
Tabla | plural | comentarios_articulo | ~~comentario_articulo, comentariosArticulo~~
Tabla de pivote | Nombres de modelos en singular y en orden alfabético | articulo_usuario | ~~usuario_articulo, articulos_usuarios~~
Columna de tabla | snake_case sin el nombre del modelo | meta_titulo | ~~MetaTitulo; articulo_meta_titulo~~
Propiedad de mdelo | snake_case | $model->created_at | ~~$model->createdAt~~
Clave foránea | Nombre en singular del modelo con el sufijo _id | articulo_id | ~~articuloId, id_articulo, articulos_id~~
Clave primaria | - | id | ~~id_personalizado~~
Migración | - | 2017_01_01_000000_create_articles_table | ~~2017_01_01_000000_articles~~
Método | camelCase | traerTodo | ~~traer_todo~~
Método en controlador de recursos | [table](https://laravel.com/docs/master/controllers#resource-controllers) | guardar | ~~guardarArticulo~~
Método en clase de pruebas | camelCase | testGuestCannotSeeArticle | ~~test_guest_cannot_see_article~~
Variable | camelCase | $articulosConAutor | ~~$articulos_con_autor~~
Colección | descriptiva, plural | $usuariosActivos = Usuario::active()->get() | ~~$activo, $data~~
Objeto | descriptivo, singular | $usuarioActivo = Usuario::active()->first() | ~~$usuarios, $obj~~
Índice de archivos de configuración y lenguaje | snake_case | articulos_habilitados | ~~articulosHabilitados; articulos-habilitados~~
Vistas | snake_case | mostrar_filtrados.blade.php | ~~mostrarFiltrados.blade.php, mostrar-filtrados.blade.php~~
Configuración | snake_case | google_calendar.php | ~~googleCalendar.php, google-calendar.php~~
Contrato (interface) | adjetivo o sustantivo | Autenticable | ~~interfaceAutenticacion, IAutenticacion~~
Trait | adjetivo | Notifiable | ~~NotificationTrait~~

[🔝 Volver al índice](#índice-de-contenido)

### **Utiliza sintaxis cortas y legibles siempre que sea posible**

Malo:

```php
$request->session()->get('cart');
$request->input('name');
```

Bueno:

```php
session('cart');
$request->name;
```

Más ejemplos

Sintaxis común | Sintaxis corta y legible
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

[🔝 Volver al índice](#índice-de-contenido)

### **Utiliza contenedores IoC o fachadas en lugar de new Class**

La sintaxis new Class crea acoplamientos estrechos y complica las pruebas. Utiliza contenedores IoC o fachadas en lugar de ello.

Malo:

```php
$user = new User;
$user->create($request->validated());
```

Bueno:

```php
public function __construct(User $user)
{
    $this->user = $user;
}

....

$this->user->create($request->validated());
```

[🔝 Volver al índice](#índice-de-contenido)

### **No saques informacion directamente del archivo `.env`**

En lugar de ello, pasa la información a un archivo de configuración y luego utiliza el ayudante `config()` para obtener la información en tu aplicación.

Malo:

```php
$apiKey = env('API_KEY');
```

Bueno:

```php
// config/api.php
'key' => env('API_KEY'),

// Utiliza la información
$apiKey = config('api.key');
```

[🔝 Volver al índice](#índice-de-contenido)

### **Guarda las fechas en los formatos estandares. Utiliza los accessors y mutators para modificar el formato**

Malo:

```php
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->toDateString() }}
{{ Carbon::createFromFormat('Y-d-m H-i', $object->ordered_at)->format('m-d') }}
```

Bueno:

```php
// Modelo
protected $dates = ['ordered_at', 'created_at', 'updated_at'];
public function getSomeDateAttribute($date)
{
    return $date->format('m-d');
}

// Vista
{{ $object->ordered_at->toDateString() }}
{{ $object->ordered_at->some_date }}
```

[🔝 Volver al índice](#índice-de-contenido)

### **Otras buenas practicas**

No coloques ningún tipo de lógica en los archivos de rutas.

Minimiza el uso de PHP vanilla en las plantillas blade.

[🔝 Volver al índice](#índice-de-contenido)
