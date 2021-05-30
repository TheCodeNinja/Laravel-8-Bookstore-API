# Set up an laravel passport as our API authentication

Laravel passport is used to secure your API through different ways of authentication.
Laravel passport needs to interact with the database.

## Preparations

Create database
Set up laravel database env properties

## Set up Passport

1. Install Passport via the Composer package manager

> composer require laravel/passport
> php artisan migrate

Passport's service provider registers its own database migration directory, so you should migrate your database after installing the package. The Passport migrations will create the tables your application needs to store OAuth2 clients and access tokens.

Passport 的服務提供商註冊了自己的數據庫遷移目錄，因此您應該在安裝軟件包後遷移您的數據庫。Passport 遷移將創建本應用程序存儲OAuth2客戶端和訪問令牌的表。

The migrations of laravel passport are included the the composer package that we just performed.

2. Run passport:install command

> php artisan passport:install

This command will create the encryption keys needed to generate secure access tokens. In addition, the command will create "personal access" and "password grant" clients which will be used to generate access tokens.

此命令將創建生成安全訪問令牌所需的加密密鑰。此外，該命令將創建“個人訪問”和“密碼授予”客戶端，用於生成訪問令牌。

3. Add HasApiTokens trait to User model

```php
use Laravel\Passport\HasApiTokens;

class User extends Authenticatable {
  use HasApiTokens, HasFactory, Notifiable;
}
```

Add the Laravel\Passport\HasApiTokens trait to your App\Models\User model. This trait will provide a few helper methods to your model which allow you to inspect the authenticated user's token and scopes

運行 passport:install 命令後，將令牌特徵添加到您的用戶模型中。此特徵將為您的模型提供一些幫助方法，允許您檢查經過身份驗證後的用戶的令牌和範圍。

4. Call the Passport::routes method

```php
use Laravel\Passport\Passport;

class AuthServiceProvider extends ServiceProvider {
  public function boot() {
    $this->registerPolicies();

    // if (! $this->app->routesAreCached()) {
    //   Passport::routes();
    // }

    Passport::routes();
  }
}
```

Next, call the Passport::routes method within the boot method of your App\Providers\AuthServiceProvider. This method will register the routes necessary to issue access tokens and revoke access tokens, clients, and personal access tokens.

接下來，您應該在 AuthServiceProvider 的啟動方法內調用 Passport::routes 方法。此方法將註冊所需的路由用來頒發訪問令牌和撤銷訪問令牌、客戶端和個人訪問令牌。

5. Set the driver option of the api authentication guard

```php
'guards' => [
  'web' => [
    'driver' => 'session',
    'provider' => 'users',
  ],

  'api' => [
    'driver' => 'passport',
    'provider' => 'users',
  ],
],
```

Finally, in your application's config/auth.php configuration file, you should set the driver option of the api authentication guard to passport. This will instruct your application to use Passport's TokenGuard when authenticating incoming API requests.

最後，在應用程序的 config/auth.php 配置文件中，您應該將api身份驗證保護程序的 driver 選項設置為 passport。這將指示您的應用程序在驗證傳入的 API 請求時使用 Passport 的 TokenGuard。

## Generate an user in table

> php artisan tinker

> DB::table('users')->insert([
>   'name' => 'Mario',
>   'email' => 'mario@gmail.com',
>   'password' => Hash::make('pass1234')
> ]);
