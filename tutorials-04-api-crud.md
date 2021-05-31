# REST API CRUD

## Use apiResource in our route

```php
// use prefix to tell which version of the api is
Route::middleware('auth:api')->prefix('v1')->group(function() {
  Route::get('/user', function(Request $request) {
    return $request->user();  
  });

  // 1st way
  // Route::get('/authors', [AuthorsController::class, 'index']);
  // Route::get('/authors/{author}', [AuthorsController::class, 'show']);
  // Route::post('/authors', [AuthorsController::class, 'store']);
  // Route::put('/authors/{author}', [AuthorsController::class, 'update']);
  // Route::delete('/authors/{author}', [AuthorsController::class, 'destroy']);

  // 2nd way
  // exclude create and edit routes
  Route::apiResource('/authors', AuthorsController::class);
});
```

## CRUD

```php
class AuthorsController extends Controller
{
  /**
   * Display a listing of the resource.
   *
   * @return \Illuminate\Http\Response
   */
  public function index()
  {
      return AuthorsResource::collection(Author::all());
  }

  /**
   * Show the form for creating a new resource.
   *
   * @return \Illuminate\Http\Response
   */
  public function create()
  {
      // this method is excluded in apiResource route
  }

  /**
   * Store a newly created resource in storage.
   *
   * @param  \Illuminate\Http\Request  $request
   * @return \Illuminate\Http\Response
   */
  public function store(Request $request)
  {
      $faker = \Faker\Factory::create(1);

      // You may use the create method to "save" a new model,
      // However, before using the create method, you will need to specify 
      // either a fillable or guarded property on your model class.
      $author = Author::create([
          'name' => $faker->name
      ]);

      return new AuthorsResource($author);
  }

  /**
   * Display the specified resource.
   *
   * @param  \App\Models\Author  $author
   * @return \Illuminate\Http\Response
   */
  public function show(Author $author)
  {
      // return json response via resource
      return new AuthorsResource($author);
  }

  /**
   * Show the form for editing the specified resource.
   *
   * @param  \App\Models\Author  $author
   * @return \Illuminate\Http\Response
   */
  public function edit(Author $author)
  {
      // this method is excluded in apiResource route
  }

  /**
   * Update the specified resource in storage.
   *
   * @param  \Illuminate\Http\Request  $request
   * @param  \App\Models\Author  $author
   * @return \Illuminate\Http\Response
   */
  public function update(Request $request, Author $author)
  {
      $author->update([
          'name' => $request->input('name'),
      ]);

      return new AuthorsResource($author);
  }

  /**
   * Remove the specified resource from storage.
   *
   * @param  \App\Models\Author  $author
   * @return \Illuminate\Http\Response
   */
  public function destroy(Author $author)
  {
      $author->delete();
      return response(null, 204);
  }
}
```