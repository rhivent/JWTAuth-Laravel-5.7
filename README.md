# JWTAuth-Laravel-5.7
Sistem Authenthikasi dengan menggunakan JWTAuth pada Laravel 5.7 di Postman

USERNAME: riventus atau qwe123 atau qweqwe
PASSword: qweqwe 


"require" : {
	"tymon/jwt-auth": "0.5.*"
}

composer update 

atau

composer require tymon/jwt-auth:dev-develop --prefer-source

-------setting----
cekk di file config/app.php trus tambah kode pada 

	array provider yaitu
	Tymon\JWTAuth\Providers\LaravelServiceProvider::class
	
	array aliases yaitu:
	'JWTAuth' => Tymon\JWTAuth\Facades\JWTAuth::class,
	'JWTFactory' => Tymon\JWTAuth\Facades\JWTFactory::class,

run command pada CMD yaitu untuk laravel 5
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\JWTAuthServiceProvider"

command ini supaya ada file jwt-auth pada folder config

untuk generate key JWT dengan command di CMD
php artisan jwt:secret

kemudian tulis pada file .env dibawah APP_URL yaitu:
JWT_SECRET=secretjwt

jika path model seperti user ada di dalam folder maka harus setting pada jwt.php pada folder config :
untuk model user path (lokasi file)
dan juga pada auth.php pada array providers ubah bagian path model 


I found my problem.
on config/jwt.php file change following provider :

<p><i>NamshiAdapter to Namshi<br/>
IlluminateAuthAdapter to Illuminate<br/>
IlluminateCacheAdapter to Illuminate<br/>

Type error: Argument 1 passed to Tymon\JWTAuth\JWT::fromUser() must be an instance of Tymon\JWTAuth\Contracts\JWTSubject, 
instance of App\User given, called in /Applications/XAMPP/xamppfiles/htdocs/git/jwt-test/vendor/tymon/jwt-auth/src/JWTAuth.php on line 54<br/>

I fix it by implement JWTSubject and modify the class:

namespace App;<br/>
use Illuminate\Foundation\Auth\User as Authenticatable;<br/>
use Tymon\JWTAuth\Contracts\JWTSubject;<br/>
class User extends Authenticatable implements JWTSubject<br/>
{<br/>
    public function getJWTIdentifier()<br/>
    {<br/>
        return $this->getKey();<br/>
    }<br/>
    public function getJWTCustomClaims()<br/>
    {<br/>
        return [];<br/>
    }<br/>
}<br/>
</i></p><br/>
Sebelum membuat JWT buat route dulu pada file route/api.php

Route::post('/auth/signin','AuthController@signin');

Setelah terbuat token maka untuk memakai token sebgai middleware yaitu melakukan authentication. <br/>
Pada saat kita mengakses endpoint yang terproteksi, kita harus mengirim dibagian headernya yaitu:

key = Authorization
value = Bearer {yourtokenhere}

untuk membuat middleware pada laravel 5.* maka harus menaruh kode pada app/Http/Kernel.php pada bagian $routeMiddleware property :
<br/>protected $routeMiddleware = [
....<br/>
	'jwt.auth' => \Tymon\JWTAuth\Middleware\GetUserFromToken::class,<br/>
		'jwt.refresh' => \Tymon\JWTAuth\Middleware\RefreshToken::class,<br/>
	];<br/>
