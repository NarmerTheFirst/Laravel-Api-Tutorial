<h1 id="top">Laravel Api Tutorial</h1>

<details>
  <summary>Table of Contents</summary></br>
  <ol>
    <li>
      <a href="#Creating-Project">Creating Project</a>
    </li></br>
	<li>
      <a href="#Database-Setup">Database Setup</a>
    </li></br>
	<li>
      <a href="#Creating-Model">Creating Model</a>
    </li></br>
	<li>
      <a href="#Migration">Migration</a>
    </li></br>
	<li>
      <a href="#Creating-Controller">Creating Controller</a>
	</br>
      <ul>
		</br>
        <li><a href="#Index">Index</a></li></br>
		<li><a href="#Store">Store</a></li></br>
		<li><a href="#Show">Show</a></li></br>
		<li><a href="#Update">Update</a></li></br>
		<li><a href="#Destroy">Destroy</a></li></br>
      </ul>
    </li>
	<li>
      <a href="#Creating-Routes">Creating Routes</a>
    </li></br>
	<li>
      <a href="#Testing">Testing</a>
      <ul>
		</br>
        <li><a href="#Get-All">Get All Tasks</a></li></br>
		<li><a href="#Get-By-Id">Get Task By Id</a></li></br>
		<li><a href="#Create-Task">Create New Task</a></li></br>
		<li><a href="#Update-Task">Update A Task</a></li></br>
		<li><a href="#Delete-Task">Delete A Task</a></li></br>
      </ul>
    </li>
  </ol>
</details>

\
&nbsp;

<h2 id="Creating-Project">1.Creating Project</h2>

```sh
composer create-project laravel/laravel ApiTutorial --ignore-platform-reqs
````

<p align="right">(<a href="#top">back to top</a>)</p>

\
&nbsp;

<h2 id="Database-Setup">2.Database Setup</h2>

Database configuration can be found in <p style=color:green;/> .env

```php
DB_CONNECTION=mysql
DB_HOST=127.0.0.1 //localhost
DB_PORT=3306 //mysql default port
DB_DATABASE=ApiTutorial //mysql db name
DB_USERNAME=root //mysql user
DB_PASSWORD=root //mysql pass
````

<p align="right">(<a href="#top">back to top</a>)</p>

&nbsp;

<h2 id="Creating-Model">3.Creating Model</h2>

```sh
php artisan make:model Task
```

Task Model Can Be Found In:

```sh
app
├── Models
│  │── Task.php
````


```php
<?php
namespace App\Models;
use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;
class Task extends Model
{
  use HasFactory;
  protected $fillable = [
    'Title',
    'ToDo'
  ]; // Added protected $fillable
}
```

<p align="right">(<a href="#top">back to top</a>)</p>

\
&nbsp;

<h2 id="Migration">4.Migration</h2>

```sh
php artisan make:migration create_tasks_table
```

```sh
database
├── migrations
│   ├── create_tasks_table.php
```

```php
<?php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;
class CreateTasksTable extends Migration
{
  /**
    * Run the migrations.
    *
    * @return void
    */
  public function up()
  {
    Schema::create('tasks', function (Blueprint $table) {
      $table->id();
      $table->string('Title'); //Added Title
      $table->string('ToDo'); //Added ToDo
      $table->timestamps();
    });
  }
  /**
    * Reverse the migrations.
    *
    * @return void
    */
  public function down()
  {
    Schema::dropIfExists('tasks');
  }
}
```

Run Command:

```sh
php artisan migrate
```

<p align="right">(<a href="#top">back to top</a>)</p>

\
&nbsp;

<h2 id="Creating-Controller">5.Creating Controller</h2>

```sh
php artisan make:controller API/TaskController --resource
```

```sh
app
├── Http
│   ├──Controllers
│   │   ├──API
│   │   │  └──TaskController.php
```

```php
<?php
namespace App\Http\Controllers\API;
use App\Http\Controllers\Controller;
use App\Models\Task; //Dont Forget To Add Task Model
use Illuminate\Http\Request;
class TaskController extends Controller
{
  /**
    * Display a listing of the resource.
    *
    * @return \Illuminate\Http\Response
    */
  public function index()
  {
    $Tasks = Task::all();
    return response()->json($Tasks);
  }
  /**
    * Store a newly created resource in storage.
    *
    * @param  \Illuminate\Http\Request  $request
    * @return \Illuminate\Http\Response
    */
  public function store(Request $request)
  {
    $request->validate([
      'Title' => 'required',
      'ToDo' => 'required'
    ]);
    $newTask = new Task([
      'Title' => $request->get('Title'),
      'ToDo' => $request->get('ToDo')
    ]);
    $newTask->save();
    return response()->json($newTask);
  }
  /**
    * Display the specified resource.
    *
    * @param  int  $id
    * @return \Illuminate\Http\Response
    */
  public function show($id)
  {
    $Task = Task::findOrFail($id);
    return response()->json($Task);
  }
  /**
    * Update the specified resource in storage.
    *
    * @param  \Illuminate\Http\Request  $request
    * @param  int  $id
    * @return \Illuminate\Http\Response
    */
  public function update(Request $request, $id)
  {
    $Task = Task::findOrFail($id);
    $request->validate([
      'Title' => 'required',
      'ToDo' => 'required'
    ]);
    $Task->Title = $request->get('Title');
    $Task->ToDo = $request->get('ToDo');
    $Task->save();
    return response()->json($Task);
  }
  /**
    * Remove the specified resource from storage.
    *
    * @param  int  $id
    * @return \Illuminate\Http\Response
    */
  public function destroy($id)
  {
    $Task = Task::findOrFail($id);
    $Task->delete();
    return response()->json($Task::all());
  }
}
```

<p align="right">(<a href="#top">back to top</a>)</p>

\
&nbsp;

<h4>☆All Functions In TaskController</h4>

\
&nbsp;

<h5 id="Index">Index()</h5>

```php
public function index()
{
  $tasks = task::all();
  return response()->json($tasks);
}
```
&nbsp;

<h5 id="Store">Store()</h5>

```php
public function store(Request $request)
{
  $request->validate([
    'Title' => 'required',
    'ToDo' => 'required'
  ]);
  $newTask = new Task([
    'Title' => $request->get('Title'),
    'ToDo' => $request->get('ToDo')
  ]);
  $newTask->save();
  return response()->json($newTask);
}
```

&nbsp;

<h5 id="Show">Show()</h5>

```php
public function show($id)
{
  $task = Task::findOrFail($id);
  return response()->json($task);
}
```
&nbsp;

<h5 id="Update">Update()</h5>

```PHP
public function update(Request $request, $id)
{
  $task = Task::findOrFail($id);
  $request->validate([
    'Title' => 'required',
    'ToDo' => 'required'
  ]);
  $task->Title = $request->get('Title');
  $task->ToDo = $request->get('ToDo');
  $task->save();
  return response()->json($task);
}
```
&nbsp;

<h5 id="Destroy">Destroy()</h5>

```php
public function destroy($id)
{
  $task = Task::findOrFail($id);
  $task->delete();
  return response()->json($task::all());
}
```

<p align="right">(<a href="#top">back to top</a>)</p>


&nbsp;

<h2 id="Creating-Routes">6.Creating Routes</h2>

```sh
routes
├──api.php
```


```php
<?php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Route;
use App\Http\Controllers\API\TaskController;
/*
|--------------------------------------------------------------------------
| API Routes
|--------------------------------------------------------------------------
|
| Here is where you can register API routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| is assigned the "api" middleware group. Enjoy building your API!
|
*/
Route::apiResource('tasks', TaskController::class); //Add This Route
```


<p style=color:orange;>List All Routes With artisan</p>


```sh
php artisan route:list
```

<p align="right">(<a href="#top">back to top</a>)</p>

&nbsp;

<h2 id="Testing">7.Testing</h2>

&nbsp;

<h5 id="Get-All">Get All Tasks</h5>

```sh
curl http://localhost:8000/api/tasks -i
```
&nbsp;

<h5 id="Get-By-Id">Get Task By Id</h5>

```sh
curl http://localhost:8000/api/tasks/1 -i
````

&nbsp;

<h5 id="Create-Task">Create New Task</h5>

```sh
curl -X POST -H 'Content-Type: application/json' -d '{
    "Title": "laravel",
    "ToDo": "Api"
}' http://localhost:8000/api/tasks -i
```

&nbsp;

<h5 id="Update-Task">Update A Task</h5>

```sh
curl -X PUT -H 'Content-Type: application/json' -d '{
    "Title": "laravel",
    "ToDo": "Api & Auth"
}' http://localhost:8000/api/tasks/1 -i
```

&nbsp;

<h5 id="Delete-Task">Delete A Task</h5>


```sh
curl -X DELETE http://localhost:8000/api/tasks/1 -i
```


<p align="right">(<a href="#top">back to top</a>)</p>


