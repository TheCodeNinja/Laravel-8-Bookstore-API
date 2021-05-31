# Building API Resource

## Preparation

Before starting, we use factory to seed the author data to database, we need to set up the migration, model, factory, seeder and api route.

> php artisan make:model -a -r Author
> php artisan migrate
> php artisan migrate --seed

-a, --all (Generate a migration, seeder, factory, and resource controller for the model)

-r, --resource (Indicates if the generated controller should be a resource controller)

## Why we need resource

Example: send back response in controller

```php
public function show(Author $author) {
  // 1st way: not good, because it's not normal json pattern
  return $author;

  // 2nd way: good, return json response via json() method
  return response()->json([
    'data' => [
      'id' => $author->id,
      'type' => 'Authors',
      'attributes' => [
        'name' => $author->name,
        'created_at' => $author->created_at,
        'updated_at' => $author->updated_at
      ]
    ]
  ]);

  // 3rd way: very good, return json response via resource
  return new AuthorsResource($author);
}
```

Now, let's generate resource.

## Generating Resources

A resource class represents a single model that needs to be transformed into a JSON structure.

Every resource class defines a toArray method which returns the array of attributes that should be converted to JSON when the resource is returned as a response from a route or controller method.

> php artisan make:resource AuthorsResource

```php
public function toArray($request) {
  return [
    'id' => (string)$this->id,
      'type' => 'Authors',
      'attributes' => [
        'name' => $this->name,
        'created_at' => $this->created_at,
        'updated_at' => $this->updated_at
      ]
  ];
}
```

## Return json response via resource

```php
public function show(Author $author) {
  return new AuthorsResource($author);
}
```

## Make request

1. Testing the request in Postman

HTTP Method:
  GET

URL:
  http://127.0.0.1:8000/api/v1/authors/1

Headers:
  Accept: application/json
  Authorization: Bearer {access-token}

After making the request, we got the following information:

```json
{
  "data": {
    "id": "1",
    "type": "Authors",
    "attributes": {
      "name": "Bella Sanford",
      "created_at": "2021-05-31T07:18:05.000000Z",
      "updated_at": "2021-05-31T07:18:05.000000Z"
    }
  }
}
```
