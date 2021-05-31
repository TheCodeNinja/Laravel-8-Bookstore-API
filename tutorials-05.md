
## Validate request

Create a custom request

> php artisan make:request AuthorsRequest

```php
public function authorize() {
  return true;
}

public function rules() {
  return [
    'name' => 'required|unique:authors|max:255'
  ];
}
```

## Create book model, and define migration file

Create the book model as well as its migration, factory, seeder and resource.
Once set up the migration, migrate it.
Also seed dummy data to book table.

> php artisan make:model Book -a
> php artisaan migrate // for migration file
> php artisan migrate --seed // for seeding data

## Create books resource

> php artisan make:resource BooksResource

```php
public function toArray($request) {
  return [
    'id' => (string)$this->id,
    'type' => 'Books',
    'attributes' => [
      'name' => $this->name,
      'description' => $this->description,
      'publication_year' => $this->publication_year,
      'created_at' => $this->created_at,
      'updated_at' => $this->updated_at
    ]
  ];
}
```

## Define book API resources

Set up book resource API routes and API handlers


## Has Many Through Relationship

1. Create a pivot table 

> php artisan make:migration create_book_author_table

In the pivot table migration file:

```php
public function up()
{
  Schema::create('book_author', function (Blueprint $table) {
    $table->unsignedBigInteger('author_id');
    $table->unsignedBigInteger('book_id');
    $table->foreign('author_id')->references('id')->on('authors')->cascade('delete');
    $table->foreign('book_id')->references('id')->on('books')->cascade('delete');
  });
}
```

In the book model:

```php
class Book extends Model {
  use HasFactory;

  protected $fillable = ['name', 'description', 'publication_year'];

  public function authors() {
    return $this->hasManyThrough(
      '\App\Models\Author',
      '\App\Models\BookAuthor', // Pivot model
      'book_id', // Foreign key on the book_author table...
      'id', // Foreign key on the authors table.
      'id', // Local key on the books table...
      'author_id' // Local key on the book_author table...
    );
  }
}
```

2. Ceate model for pivot table

> php artisan make:model BookAuthor -a

In BookAuthor model file:

```php
class BookAuthor extends Model {
  use HasFactory;

  protected $table = 'book_author';
}
```

3. Add author attribute to the books resource

In BooksResource file:

```php
public function toArray($request) {
  return [
    'id' => (string)$this->id,
    'type' => 'Books',
    'attributes' => [
      'name' => $this->name,
      'author' => $this->authors, // public function authors()
      'description' => $this->description,
      'publication_year' => $this->publication_year,
      'created_at' => $this->created_at,
      'updated_at' => $this->updated_at
    ]
  ];
}
```

4. Test the request in Postman



# Example of Has Many Through:

A Project model might access many Deployment models through an intermediate Environment model.

projects
  id - integer
  name - string

environments // (pivot table)
  id - integer
  project_id - integer // (fk)
  name - string

deployments
  id - integer
  environment_id - integer // (fk)
  commit_hash - string

```php
class Project extends Model {
  public function deployments() {
    return $this->hasManyThrough(
      Deployment::class,
      Environment::class,
      'project_id', // Foreign key on the environments table...
      'environment_id', // Foreign key on the deployments table...
      'id', // Local key on the projects table...
      'id' // Local key on the environments table...
    );
  }
}
```
