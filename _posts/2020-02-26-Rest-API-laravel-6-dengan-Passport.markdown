---
layout: post
title:  "Backend : Rest API Laravel 6 dengan Passport"
date:   2020-02-26 04:31:25 +0700
categories: jekyll update
---

<h3>Apakah REST, API  dan REST API itu?</h3>

<p> REST, singkatan bahasa Inggris dari <i>Representational State Transfer</i> atau transfer keadaan representasi, adalah suatu gaya arsitektur perangkat lunak untuk untuk pendistibusian sistem hipermedia seperti WWW. Istilah ini diperkenalkan pertama kali pada tahun 2000 pada disertasi doktoral Roy Fielding, salah seorang penulis utama spesifikasi HTTP (<a href="https://id.wikipedia.org/wiki/REST">Wikipedia</a>). REST digunakan oleh pemrogram untuk mengirim permintaan dan menerima tanggapan menggunakan metode protokol HTTP seperti GET dan POST. </p> <p>API merupakan singkatan dari <i>Aplication Programing Interface</i> yaitu  penerjemah komunikasi antara klien dengan server untuk menyederhanakan implementasi dan perbaikan software. Bisa diartikan juga sebagai sekumpulan perintah, fungsi, serta protokol yang dapat digunakan oleh programmer saat membangun perangkat lunak untuk sistem operasi tertentu. API memungkinkan programmer untuk menggunakan fungsi standar untuk berinteraksi dengan sistem operasi(<a href="https://id.wikipedia.org/wiki/Antarmuka_pemrograman_aplikasi">Wikipedia</a>). Misalkan ketika di smartphone kita terinstall Facebook Android (client), sedangkan data yang kita akses ada di USA (server), nah ketika kita akan mengakses beranda maka kita perlu cara untuk meminta server facebook memberikan data yang ada diberanda, cara meminta facebook mengirimi anda data yang diminta adalah melalui API yang telah disediakan oleh Facebook.</p>

<p>REST API dapat digunakan oleh situs atau aplikasi apa pun, tidak peduli bahasa apa yang ditulisnya karena permintaan didasarkan pada protokol HTTP universal, dan informasi tersebut biasanya dikembalikan dalam format JSON yang dapat dibaca oleh hampir semua bahasa pemrograman (<a href="https://phpenthusiast.com/blog/what-is-rest-api">sumber</a>).</p>
<div align="center">
<img src="/assets/rest-api-laravel6/rest_api.png" width="100%" alt="REST API" >
</div>
<p>Source : <a href="https://phpenthusiast.com/blog/what-is-rest-api">https://phpenthusiast.com/blog/what-is-rest-api</a></p>

<p>Adapun HTTP Protocol yang sering digunakan:</p>
<table>
    <thead>
        <th>Metode</th>
        <th>Bentuk Request URL</th>
        <th>Fungsi</th>
        <th>Contoh</th>
    </thead>
    <tbody>
        <tr><td>GET</td><td>/api/[statemen_perintah] atau /api/[statemen_perintah]/id</td><td>Mengambil Data</td><td>http://localhost:8000/api/category atau http://localhost:8000/api/category/1 </td></tr>
        <tr><td>POST</td><td>/api/[statemen_perintah]</td><td>Membuat/Menambah Data</td><td>http://localhost:8000/api/category?id=value&name=value</td></tr>
        <tr><td>PUT</td><td>/api/[statemen_perintah]/id</td><td>Mengubah/Mengedit Data</td><td>http://localhost:8000/api/category/1?id=value&name=value</td></tr>
        <tr><td>DELETE</td><td>/api/[statemen_perintah]/id</td><td>Menghapus Data</td><td>http://localhost:8000/api/category/1</td></tr>
    </tbody>
</table>
<p><b>Note :</b> Contoh diatas dibuat jika pada Routes/api.php laravelnya sbb:
<pre><code>Route::get('category','categoryController@index');
Route::post('category','categoryController@store');
Route::put('category/{id}','categoryController@update');
Route::delete('category/{id}','categoryController@destroy');</code></pre> 
Dimana categoryController sebagai Class dan index,store,update,destroy sebagai methode didalam kelasnya, kemudian atribut Classnya hanya id dan name,
</p>

<h3>Terus Passport API itu apa?</h3>

<p>API, biasanya menggunakan token untuk mengautentikasi pengguna dan tidak mempertahankan status sesi di antara permintaan. Dengan kata lain dengan menggunakan fitur ini dapat meningkatkan sisi security program ataupun data yang telah dibuat, karena setiap client yang mengakses akan diberikan token sebagai authentication. Framework Laravel menjadikan otentikasi API sangat mudah menggunakan Laravel Passport, yang menyediakan implementasi server OAuth2 lengkap untuk aplikasi Laravel Anda dalam hitungan menit. Paspor dibangun di atas server League OAuth2 yang dikelola oleh Andy Millington dan Simon Hamp. <a href="https://oauth2.thephpleague.com/terminology/">istilah di dalam OAuth2</a></p>

<h3>Implementasi di Laravel</h3>

<h4>1. Persiapan</h4>
Software:
<ul>
    <li>XAMPP for Linux 7.4.2-64bit(PHP 7.4.2,PhpMyAdmin 5.0.1)</li>
    <li>OS Ubuntu 18.04.3 LTS</li>
    <li>Firefox 68.0.1 64 bit</li>
    <li>Laravel 6.2</li>
    <li>Composer 1.9.3</li>
    <li>Postman 7.17.0</li>
</ul>
Package Laravel yang ditambahkan:
<ul>
    <li>laravel/passport: 8.4</li>
</ul>

<h4>2. Proses Implementasi Passport</h4>
<p>Sebelumnya saya belajar dari laman <a href="https://daengweb.id/api-otentikasi-menggunakan-passport-laravel">ini</a>, lanjut saja:</p>
<ol type="a">
    <li>Pastikan Xampp dan Composer telah terinstall</li>
    <li>Membuat project laravel dengan nama <b>rest_api_inventory</b>
        <p>Buka terminal (linux)/CMD (windows) ke folder htdocs dengan perintah <b>cd</b>, biasanya untuk linux di /opt/lampp/htdocs, dan windows C://xampp/htdocs/, kemudian berikan perintah berikut:</p>
        <pre><code>composer create-project --prefer-dist laravel/laravel rest_api_inventory</code></pre>
    </li>
    <li>Menambahkan package passport
        <p>Pindah ke folder rest_api_inventory, kemudian ketik perintah berikut:</p>
        <pre><code>composer require laravel/passport</code></pre>
        Jika versi tidak dicantumkan maka akan menginstall yang terbaru
    </li>
    <li>Melakukan konfigurasi di config/app.php 
        <p>Buka file config/app.php dengan code editor, kemudian tambahkan:</p>
        <pre><code>'providers' => [

	Laravel\Passport\PassportServiceProvider::class,
],</code></pre>
    </li>
    <li>Membuat Database di PHPMyadmin
        <ul>
            <li>Jalankan Apache dan mysql, (linux) <pre><code>sudo /opt/lampp/lampp start </code></pre> (windows) Jalankan xampp control panel, klik tombol start </li>
            <li>Buka PhpMyadmin dengan membuka browser, kemudian ketik di url <b>localhost</b> kemudian klik <b>PhpMyadmin</b> </li>
            <li>Buat Database baru dengan nama db_rest_api_inventory (bisa diganti). Caranya, klik <b>New</b> pojok kiri atas, isi nama database, kemudian klik <b>create</b>.
                <div align="center">
                <img src="/assets/rest-api-laravel6/buat_db.png" width="100%" alt="database REST API laravel 6" >
                </div>
            </li>
        </ul>
    </li>
    <li>Melakukan Konfigurasi Database
        <p>Buka file .env project laravel dengan code editor. kemudian lihat pada bagian berikut, serta isikan:</p>
        <pre><code>DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=db_rest_api_inventory // ganti sesuai nama database anda 
DB_USERNAME=root
DB_PASSWORD=        //isi password jika ada </code></pre>
    </li>
    <li>Melakukan migrate
        <p>Ketika kita membuat project laravel secara otomatis laravel membuat file migrate untuk user, sehingga ketika migrate akan melakukan execute file tersebut dan membuat table di database yang sebelumnya di konfigurasi. Ketik perintah :</p>
        <pre><code>php artisan migrate</code></pre>
    </li>
    <li>Menginstall passport untuk membuat token keys. dengan perintah: <pre><code>php artisan passport:install</code></pre></li>
    <li>Melakukan konfigurasi di model, service provider dan auth config
    <p>Setelah berhasil <i>generate</i> token keys kemudian melakukan beberapa konfigurasi</p>
    <ul>
        <li>app/User.php
            {% highlight php %}
<?php

namespace App;

use Laravel\Passport\HasApiTokens;    //Tambah ini
use Illuminate\Contracts\Auth\MustVerifyEmail;
use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;

class User extends Authenticatable
{
    use HasApiTokens, Notifiable;      //Tambah ini

    /**
     * The attributes that are mass assignable.
     *
     * @var array
     */
    protected $fillable = [
        'name', 'email', 'password',
    ];

    /**
     * The attributes that should be hidden for arrays.
     *
     * @var array
     */
    protected $hidden = [
        'password', 'remember_token',
    ];

    /**
     * The attributes that should be cast to native types.
     *
     * @var array
     */
    protected $casts = [
        'email_verified_at' => 'datetime',
    ];
}
{% endhighlight %}
</li>
        <li>app/Providers/AuthServiceProvider.php
{% highlight php %}
<?php

namespace App\Providers;

use Laravel\Passport\Passport;         //Tambah ini
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;
use Illuminate\Support\Facades\Gate;

class AuthServiceProvider extends ServiceProvider
{
    /**
     * The policy mappings for the application.
     *
     * @var array
     */
    protected $policies = [
        'App\Model' => 'App\Policies\ModelPolicy', //uncoment ini
    ];

    /**
     * Register any authentication / authorization services.
     *
     * @return void
     */
    public function boot()
    {
        $this->registerPolicies();
        Passport::routes();                 //Tambah ini
        //
    }
}

{% endhighlight %}
        </li>
        <li>config/auth.php
{% highlight php %}
    'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],

        'api' => [
            'driver' => 'passport', //ganti token menjadi passport
            'provider' => 'users',
            'hash' => false,
        ],
    ],
{% endhighlight %}
        </li>       
    </ul>
    </li>
    <li>Menambahkan route di file Routes/api.php
{%highlight php%}
Route::post('login','API\UserController@login');
Route::post('register','API\UserController@register');
Route::group(['middleware'=>'auth:api'], function(){
    Route::post('details','API\UserController@details');
});
{%endhighlight%}
    <p>route ini yang nantinya akan digunakan untuk merequest json/api nya.</p>
    </li>
    <li>Membuat Controller API/UserController
        <p>Ketik perintah:</p>
        <pre><code>php artisan make:controller API/UserController</code></pre>
        <p>File akan tersimpan di folder app/Http/controllers/API/ dengan nama UserController.php. Kemudian isikan kode berikut:</p>
{%highlight php%}
<?php

namespace App\Http\Controllers\API;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use App\User;
use Illuminate\Support\Facades\Auth;
use Validator;

class UserController extends Controller
{
    public $statusBerhasil = 200;

    public function login()
    {
        if(Auth::attempt(['email'=>request('email'),'password'=>request('password')])){
            $pengguna=Auth::user();
            $success['token']=$pengguna->createToken('nApp')->accessToken;
            return response()->json(['success'=>$success], $this->statusBerhasil);
        }
        else{
            return response()->json(['error'=>'Unauthorised'],401);
        }
    }
    public function register(Request $request)
    {
        $validator = Validator::make($request->all(),[
            'name'=>'required',
            'email'=>'required|email',
            'password'=>'required',
            'c_password'=>'required|same:password',
        ]);
        if ($validator->fails()){
            return response()->json(['error'=>$validator->errors()],401);
        }
        $data_masuk=$request->all();
        $data_masuk['password']=bcrypt($data_masuk['password']);
        $pengguna = User::create($data_masuk);
        $success['token']=$pengguna->createToken('nApp')->accessToken;
        $success['name']=$pengguna->name;
        return response()->json(['success'=>$success],$this->statusBerhasil);
    }
    public function details()
    {
        $pengguna=Auth::user();
        return response()->json(['success'=>$pengguna],$this->statusBerhasil);
    }
}
{%endhighlight%}
    </li>
    <li>Melakukan Pengetesan
        <ul>
            <li>Jalankan Laravel dengan perintah: <p><b>Note:</b> Posisi terminal / CMD berada di folder project laravel.</p><pre><code>php artisan serv</code></pre> </li>
            <li>Buka Aplikasi Postman</li>
            <li>Test Registrasi</li>
                <p>Isikan url sesuai nama yang telah dibuat di Routes/api.php, yang telah dibuat register, maka <b>localhost:8000/api/register</b>, kemudian isi valuenya contoh:</p>
                    <div align="center">
                    <img src="/assets/rest-api-laravel6/registrasi.png" width="100%" alt="database REST API laravel 6" >
                    </div>
                Returnnya:
                    <div align="center">
                    <img src="/assets/rest-api-laravel6/registrasi_return.png" width="100%" alt="database REST API laravel 6" >
                    </div>
            <li>Test Login</li>
            <p>Isikan url sesuai nama yang telah dibuat di Routes/api.php, yang telah dibuat login, maka <b>localhost:8000/api/login</b>, kemudian isi valuenya contoh:</p>
                    <div align="center">
                    <img src="/assets/rest-api-laravel6/login.png" width="100%" alt="database REST API laravel 6" >
                    </div>
                Returnnya:
                    <div align="center">
                    <img src="/assets/rest-api-laravel6/login_return.png" width="100%" alt="database REST API laravel 6" >
                    </div>
            <li>Test Details (melihat siapa yang login)
            <p>Copy token hasil login sebelumnya (disarankan dengan return position raw, alasan ketika dalam bentuk json akan ada spasi yang tercopy sehingga token berubah dan akan gagal ketika digunakan, silahkan coba jika penasaran hehe), perhatikan gambar contoh:</p>
                     <div align="center">
                    <img src="/assets/rest-api-laravel6/raw_token.png" width="100%" alt="database REST API laravel 6" >
                    </div>
            <p>Silahkan Copy Token yang ada didalam  tanda "", Kemudian isikan url sesuai nama yang telah dibuat di Routes/api.php, yang telah dibuat details, maka <b>localhost:8000/api/details</b>, kemudian pindah ke header dan isikan valuenya contoh:</p>
                    <div align="center">
                    <img src="/assets/rest-api-laravel6/details.png" width="100%" alt="database REST API laravel 6" >
                    </div>
                <p><b>Note : </b>Pada bagian Authorization diisi Bearer[spasi][token] Alasannya masih mencari tahu.</p>
                Returnnya:
                    <div align="center">
                    <img src="/assets/rest-api-laravel6/details_return.png" width="100%" alt="database REST API laravel 6" >
                    </div>
            </li></ul>
    </li><br>

<h4>3. Proses Menambahkan CRUD </h4>
    <p>Setelah berhasil membuat authentication dengan Token, kemudian kita coba dengan menambahkan CRUD disertai token, tujuannya agar ketika nanti melakukan kirim dan terima data antara client dan server lebih aman, sekaligus buat belajar, hehe. Adapun tabel yang digunakan adalah category yang didalamnya terdapat atribut nama (id tidak disebut karena wajib).</p>
    <ol type="a">
        <li>Membuat Model category serta file migration
            <p><pre><code>php artisan make:model category -m</code></pre> File akan muncul di App/ dengan nama category.php, serta database/migration/ dengan nama  <b>2020_02_27_030044_create_categories_table.php</b>(contoh).</p>
        </li>
        <li>Tambahkan Code di database/migration/2020_02_27_030044_create_categories_table.php
{%highlight php%}
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateCategoriesTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('categories', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->string('nama');
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
        Schema::dropIfExists('categories');
    }
}
{%endhighlight%}         
        </li>
        <li>Melakukan migrate
            <p>Sebelum melakukan migration cek apakah ada file lain di folder database/migration/. jika ada, disarankan pindah terlebih dahulu supaya tidak ikut terexecute sehingga data didatabase terhapus. jika dapat menemukan cara untuk migrate 1 file, maka tidak perlu dilakukan. Untuk migrate ketik perintah : <pre><code>php artisan migrate</code></pre></p>
        </li>
        <li>Tambah Code pada Model category, file: app/category.php
{%highlight php%}
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class category extends Model
{
    protected $fillable = ['nama'];
}

{%endhighlight%}     
         </li>
        <li>Membuat Controller untuk category
            <p>Buat file controller category beserta methode CRUD nya dengan perintah:<pre><code>php artisan make:coontroller --resource</code></pre><b>--resource</b> untuk menambah methode di dalam file/class controller yang dibuat.</p>
        </li>
        <li>Tambah Code pada file app/Http/Controllers/API/categoryController.php
{%highlight php%}
<?php

namespace App\Http\Controllers\API;

use App\category;
use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Validator;

class categoryController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        $category=category::all();
        $data=$category->toArray();
        $response=[
            'success'=>true,
            'data'=>$data,
            'message'=>'Data category berhasil diambil'
        ];
        return response()->json($response,200);
    }

    /**
     * Show the form for creating a new resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function create()
    {
        //
    }

    /**
     * Store a newly created resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Illuminate\Http\Response
     */
    public function store(Request $request)
    {
        $input = $request->all();
        $validator=Validator::make($input,[
            'nama'=>'required',
        ]);
        if ($validator->fails()){
            $response=[
            'success'=>false,
            'data'=>'Gagal Validasi',
            'message'=>$validator->errors()
            ];
            return response()->json($response,404);
        }
        
        $category=category::create($input);
        $data = $category->toArray();
        $response=[
            'success'=>true,
            'data'=>$data,
            'message'=>'Data Category Berhasil disimpan'
        ];
        return response()->json($response,200);
    }

    /**
     * Display the specified resource.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function show($id)
    {
        $category=category::find($id);
        $data=$category->toArray();

        if(is_null($category)){
            $response=[
                'success'=>false,
                'data'=>'Kosong',
                'message'=>'Data Category Tidak Ditemukan.'
            ];
            return response()->json($response,404);
        }
        $response=[
            'success'=>true,
            'data'=>$data,
            'message'=>'Data Category ditemukan.'
        ];
        return response()->json($response,200);
    }

    /**
     * Show the form for editing the specified resource.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function edit($id)
    {
        //
    }

    /**
     * Update the specified resource in storage.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function update(Request $request, category $category)
    {
        $input = $request->all();
        $validator=Validator::make($input,[
            'nama'=>'required'
        ]);
        if ($validator->fails()){
            $response=[
                'success'=>false,
                'data'=>'Gagal Validasi',
                'message'=>$validator->errors()
            ];
            return response()->json($response,404);
        }
        $category->nama=$input['nama'];
        $category->save();
        $data=$category->toArray();
        $response=[
            'success'=>true,
            'data'=>$data,
            'message'=>'Data Category telah diupdate.'
        ];
        return response()->json($response,200);
    }

    /**
     * Remove the specified resource from storage.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function destroy(category $category)
    {
        $category->delete();
        $data=$category->toArray();
        $response=[
            'success'=>true,
            'data'=>$data,
            'message'=>'Data Category Berhasil Dihapus.'
        ];
        return response()->json($response,200);
    }
}
{%endhighlight%}
        </li>
        <li>Tambah Route di dalam file Routes/api.php
        <pre><code>Route::post('login','API\UserController@login');
Route::post('register','API\UserController@register');
Route::group(['middleware'=>'auth:api'], function(){
    Route::post('details','API\UserController@details');
    Route::resource('category','API\categoryController'); //tambah ini
});</code></pre>
        </li>
        <li>Jalankan project laravel
         <pre><code>php artisan serv</code></pre></li>
        <li>Testing</li>
            <ul>
                <li>Login
                    <p>Login kemudian ambil tokennya</p>
                    <div align="center">
                    <img src="/assets/rest-api-laravel6/login.png" width="100%" alt="database REST API laravel 6" >
                    </div>
                </li>
                <li>Tambah Data
                <p>Tambah tab, isi url, klik tab header dibawah url, tambah key accept, value Application/json, tambah key Authorization, value diisi Bearer[spasi][token yang tadi diambil] </p>
                    <div align="center">
                    <img src="/assets/rest-api-laravel6/category_tambah1.png" width="100%" alt="database REST API laravel 6" >
                    </div>
                <p>Ganti Tab Params dibawah url, isikan key: nama, value:makanan (contoh). pastikan HTTP protocol POST. Kemudian lihat returnnya:</p>
                    <div align="center">
                    <img src="/assets/rest-api-laravel6/category_tambah.png" width="100%" alt="database REST API laravel 6" >
                    </div>
                </li>
                <li>Ubah Data
                <p>Sama dengan tambah data, hanya ganti value:makanan1 (contoh) dan url ditambahkan id. Pastikan HTTP protocol PUT. kemudian lihat returnnya: </p>
                    <div align="center">
                    <img src="/assets/rest-api-laravel6/category_ubahdata.png" width="100%" alt="database REST API laravel 6" >
                    </div></li>
                <li>Lihat Data
                <p>Sama dengan tambah data, hanya params dibiarkan kosong. Pastikan HTTP protocol GET. kemudian lihat returnnya: </p>
                    <div align="center">
                    <img src="/assets/rest-api-laravel6/category_ambil_data.png" width="100%" alt="database REST API laravel 6" >
                    </div>
                </li>
                <li>Hapus Data
                <p>Sama dengan tambah data, hanya params dibiarkan kosong, url ditambahkan id yang akan dihapus. Pastikan HTTP protocol DELETE. kemudian lihat returnnya: </p>
                    <div align="center">
                    <img src="/assets/rest-api-laravel6/category_hapusdata.png" width="100%" alt="database REST API laravel 6" >
                    </div>
                </li>
                <li>Lihat Data setelah dihapus
                <p>Sama dengan tambah data, hanya params dibiarkan kosong. Pastikan HTTP protocol GET. kemudian lihat returnnya:</p>
                    <div align="center">
                    <img src="/assets/rest-api-laravel6/category_datakosongdihapus.png" width="100%" alt="database REST API laravel 6" >
                    </div>
                </li>
            </ul>
    </ol>
<h4>4. Proses Menambahkan Logout</h4>
<ol type="a">
    <li>Tambahkan method logout di API/UserController.php
{%highlight php%}
    public function logout(Request $request)
    {
        $request->user()->token()->revoke();
        $response=[
            'success'=>true,
            'message'=>'Selamat Geh Jenengan berhasil keluar'
        ];
        return response()->json($response,200);
    }
{%endhighlight%}
    </li>
    <li>Tambah route di Routes/api.php
        <pre><code>Route::post('login','API\UserController@login');
Route::post('register','API\UserController@register');
Route::group(['middleware'=>'auth:api'], function(){
        Route::post('details','API\UserController@details');
        Route::resource('category','API\categoryController'); 
        Route::post('logout','API\UserController@logout'); //tambah ini
});</code></pre>
    </li>
    <li>Jalankan project dengan perintah 
    <pre><code>php artisan serv</code></pre>
    </li>
    <li>Testing
        <ul>
            <li>Lakukan Login dan ambil tokennya.
                <div align="center">
                    <img src="/assets/rest-api-laravel6/tlogout_login.png" width="100%" alt="log out REST API laravel 6" >
                </div>
            </li>
            <li>Tambah data setelah Login (bisa disimpan)
                <p>Isikan Header</p>
                <div align="center">
                    <img src="/assets/rest-api-laravel6/tlogout_input1.png" width="100%" alt="logout REST API laravel 6" >
                </div>
                <p>Isikan params</p>
                <div align="center">
                    <img src="/assets/rest-api-laravel6/tlogout_input2.png" width="100%" alt="logout REST API laravel 6" >
                </div>
            </li>
            <li>Lakukan Log out
                <div align="center">
                    <img src="/assets/rest-api-laravel6/tlogout_logout.png" width="100%" alt="log out REST API laravel 6" >
                </div>
            </li>
            <li>Tambah data setelah Log out (tidak bisa karena harus login, untuk membuat token baru)
                <div align="center">
                    <img src="/assets/rest-api-laravel6/tlogout_input_aflogout.png" width="100%" alt="log out REST API laravel 6" >
                </div>
            </li>
        </ul>
    </li>
</ol>
<h4>Kesimpulan</h4>
<p>Banyak cara yang digunakan untuk mengamankan REST API, namun untuk kali ini menggunakan Passport. Mohon kritik dan sarannya yang membangun jika ada yang keliru. Semoga bermanfaat.</p>
