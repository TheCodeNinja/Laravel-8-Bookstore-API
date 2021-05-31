# Generating Bearer Token For Protected API Routes

## Setting routes

Laravel includes an authentication guard that will automatically validate API tokens on incoming requests. You only need to specify the auth:api middleware on any route that requires a valid access token.

Laravel 包含一個身份驗證守衛，它會自動驗證傳入請求的 API 令牌。您只需要在需要有效訪問令牌的路由上指定 auth:api 中間件。

```php
use Illuminate\Http\Request;

Route::middleware('auth:api')->get('/user', function(Request $request) {
  return $request->user();
});
```

## Requesting an access token

You may request an access token by issuing a POST request to the /oauth/token route. The route /oauth/token is already registered by the Passport::routes method so there is no need to define it manually. 

If the request is successful, you will receive an access_token and refresh_token in the JSON response from the server.

1. Make request in Postman

HTTP Method:
  POST

URL:
  http://127.0.0.1:8000/oauth/token

Body form-data:
  grant_type: password
  client_id: 2
  client_secret: 5fWwUafMNqfumkJ6eap5NwXdigsOtTuIeGyNpp4r
  username: mario@gmail.com
  password: pass1234
  scope: 

2. Make request in controller

```php
use Illuminate\Support\Facades\Http;

$response = Http::asForm()->post('http://passport-app.com/oauth/token', [
  'grant_type' => 'password',
  'client_id' => 'client-id',
  'client_secret' => 'client-secret',
  'username' => 'taylor@laravel.com',
  'password' => 'my-password',
  'scope' => '',
]);

return $response->json();
```

After making the request, we can get back the following information:

```json
{
  "token_type": "Bearer",
  "expires_in": 31536000,
  "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdWQiOiIyIiwianRpIjoiYThmNWMyMzZmOWZhNmM1ZDFlN2FkMzkxNDkwZWE4NzI2MDBhNTI2MGRkNGIzMjJiMDRkOTYyN2Q1YmNkYzdiNzEyZGEyODg0ZGJlNDUzNGEiLCJpYXQiOjE2MjI0NDE4OTUuMzIwODEsIm5iZiI6MTYyMjQ0MTg5NS4zMjA4MTQsImV4cCI6MTY1Mzk3Nzg5NS4zMTE2MjgsInN1YiI6IjEiLCJzY29wZXMiOltdfQ.OqSHxu_vGuA_CakOe6uZ5fcuZDu31x6jP_ieL9jISD6JV0vMl2mlFkV6SvvGUjtP2qhF7IwtP_h6G7gbsdBqdtc6fg-mY3yFRoc6p0sp_Y7TZirzhS8yHc0yfgtiCJxSYCiPzfRGUXbs1UF0dCEgxehDgolwIl0BHB8THmYGToU_rMKH58u3mCTJClEvQGTQj-79B_tnz-lXkzfZSpAWysQDU8MDYsgnaoJ_TdXgglw-MOvuuEMWaDn6SzdLBpaglGFPISoNFgxqPtNruM-qxigyC8OtaoHxcYvDBAzdJ3wAM4kyMyi--akiU-qzH20NRgjrK2FQUeioUXrVs803F4aM-tpMQPXhnvXWdVXQzszbFHdZUWb7scAMLXVoxtcapMIIjGXlIsCJhENtOGXOajk3-IBHZkr_MyNNHxMA9tAQa28s0jOSUaFA833XKfNvmoqS1j06-grJAnymDw94WbgW0HQKYB5frPUbY4R91ajQDF2GDRMNEGHQz_pLLZzj9qeIsuTh7gCfViwz-Nbb0kmr7FpbOig1nCdvZ0KSd56ikrxD24cT-7vkTtLwrrGnY4j90dSOghX_nxMx2YEZUOl49a1al0fFzGKnRRkJYTxYk4AtYNLogn4OTqBwDTfbPViF1H_lgGpW7bS2xDDhzRLr2osWqB4DD9PBF75iO5k",
  "refresh_token": "def502000bc9970e1bd5302c37a730b328b33f3462cff7ee86d4c64927dfd4e422cf2f07bee08749eebaa9135ef8274dfe4c5a7205e4f5b3d3ad854aaa64c2efec2f26391583234e7d90924919d3fb3bdacd390d098ad9515e3be4ffcec2a1a598176571d5304c64abb3c0de9ab484b7d874ca19f6795d3606116129744fe6758a3afe09796841f3ea4e9305b1368738c414bd9bef64b36795a6fe67ac9bc368d284238d1399d70e94e010560f7fa9525a3689a649ecdcb021c6bd2bec3b87368b53a858e3af70de20c242d17416a9eb579cbaa64093c2a557e0a487da9fb37043ec7e262b33a1c2c96b4f91d2f0a68e7b78f1f29acd6f7ff5c71db8d97c94fef77bd7fe73668a071d55a8279c834964bccbffa977c023c201370a5796bd8cde451930dd438ce52703df8abec748fee683affa754e41dd9fe01a5f905fabbcbae401b6db071349b8a0977763665cd7ae84444078539d5306914f4816ac28f38266"
}
```

## Testing our protected api routes

1. Make request in Postman

HTTP Method:
  GET

URL:
  http://127.0.0.1:8000/api/user

Headers:
  Accept: application/json
  Authorization: Bearer {access-token}

Afer making the request, we can get back the following information:

```json
{
  "id": 1,
  "name": "Mario",
  "email": "mario@gmail.com",
  "email_verified_at": null,
  "created_at": null,
  "updated_at": null
}
```
