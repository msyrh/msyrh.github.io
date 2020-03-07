---
layout: post
title:  "Backend : Rest API Laravel 6 Upload File dan CRUD Berelasi"
date:   2020-03-06 12:00:25 +0700
categories: jekyll update
---

<p>Pada Postingan <a href="https://msyrh.github.io/jekyll/update/2020/02/25/Rest-API-laravel-6-dengan-Passport.html">sebelumnya</a>, kita telah mempelajari cara menggunakan fitur passport dalam mengautentikasi pengguna. Sekarang kita akan mempelajari cara membuat CRUD data berelasi dan Upload File. Dalam menampilkan data, setiap progammer memiliki cara yang berbeda. Jadi sebelum saya melanjutkan, saat teman-teman membaca artikel ini, kemudian ditemukan kekurangan mohon kritik dan sarannya melalui email yang tertera.
<p>Sebagai gambaran, terdapat beberapa macam relasi tabel yang sering dijumpai, diantaranya <i>one to one, one to many, many to many</i></p>
<table>
    <thead>
        <th>No</th>
        <th>Macam Relasi</th>
        <th>Contoh</th>
        <th>Eloquent Laravel</th>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>One to One</td>
            <td>Setiap Product memiliki satu Category </td>
            <td>belongsTo</td>
        </tr>
        <tr>
            <td>2</td>
            <td>One to Many</td>
            <td>Satu Supplier dapat menyuplai beberapa Product</td>
            <td>hasMany</td>
        </tr>
        <tr>
            <td>3</td>
            <td>Many to Many</td>
            <td>Pengguna memliki banyak peran, di mana peran tersebut juga dimiliki oleh pengguna lain. Misalnya, banyak pengguna mungkin memiliki peran "Admin".</td>
            <td>belongstoMany</td>
        </tr>
    </tbody>
</table>
<p>Terdapat relasi lain yang dapat digunakan Laravel, selebihnya dapat cek <a href="https://laravel.com/docs/6.x/eloquent-relationships">disini</a>.</p>
<h3>Implementasi di Laravel</h3>
<p>Sebelum melanjutkan, bagi yang belum mengikuti postingan <a href="https://msyrh.github.io/jekyll/update/2020/02/25/Rest-API-laravel-6-dengan-Passport.html">sebelumnya</a> harap dibaca terlebih dahulu. Karena project ini berkaitan dengan sebelumnya. Kemudian hasil project dapat di unduh <a href="https://github.com/msyrh/Rest_Api_Laravel_6">disini</a>. </p>
<p>Relasi data yang akan dibuat dengan menambahkan tabel product dari project sebelumnya. Dimana setiap Product memiliki 1 kategori. Adapun bentuk relasinya adalah sbb: (tabel user dan lainnya saya hidde, ini hanya sebagai gambaran) </p>
<div align="center">
     <img src="/assets/rest-api-laravel6/relasi.png" width="100%" alt="database relasi REST API laravel 6" >
 </div>
<p>Kemudian File yang akan diupload adalah gambar product. Dimana gambar tesebut akan tersimpan di folder public/upload/products/. Berikut langkah-langkahnya:</p>
<ol type="1">
    <li>Persiapan
        <p>Buka project yang sebelumnya telah dibuat dengan buka Terminal(linux)/CMD(windows), kemudian arahkan ke /opt/lampp/htdocs/rest_api_inventory (linux), C:/Xampp/htdocs/rest_api_inventory (widows) dengan perintah <b>cd</b>. 
        <pre><code>cd /opt/lampp/htdocs/rest_api_inventory</code></pre>
        Kemudian pastikan xampp sudah dijalankan serta telah dilakukan configurasi database (lihat postingan <a href="https://msyrh.github.io/jekyll/update/2020/02/25/Rest-API-laravel-6-dengan-Passport.html">sebelumnya</a> bagi yang belum)
        </p>
    </li>
    <li>Membuat Database Product
        <p>Untuk membuat database, bisa menggunakan fitur migrate pada laravel. Dan file migrate dapat dibuat berbarengan dengan model. Ketik perintah berikut, kemudian enter.</p>
        <pre><code>php artisan make:model product -m</code></pre>
        <p>File model tersimpan di app/ kemudian file migrate di database/migration/. Buka file migrate dengan nama 2020_02_29_023843_create_products_table.php (contoh) dengan kode editor. kemudian isikan kode berikut:</p>
{% highlight php linenos  %}
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateProductsTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('products', function (Blueprint $table) {
            $table->bigIncrements('id');
            $table->bigInteger('id_category')->unsigned();
            $table->string('nama');
            $table->integer('harga');
            $table->string('image');
            $table->integer('qty');
            $table->timestamps();
            $table->foreign('id_category')->references('id')->on('categories')->onDelete('cascade');
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('products');
    }
}
{% endhighlight %}
        <p>Dari kode diatas baris 16 menunjukan nama table yang akan dibuat. kemudian baris 17 -23 atribut didalamnya yang akan dibuat. Baris ke 24 merupakan bentuk relasi yang akan dibentuk dimana tabel produk memiliki foreign key dengan nama id_category yang akan terelasi dengan id tabel categories. sebelum migrate, jika ada file selain product didalam folder database/migrations/ disarankan di pindah terlebih dahulu supaya data yang pernah tersimpan tidak hilang. Kemudian ketik perintah berikut di terminal:
        <pre><code>php artisan migrate</code></pre>
        Setelah dilakukan migrate, silahkan cek database project ini (saya: db_rest_api_inventory) di PHPMyAdmin, bahwa tabel products telah dibuat.</p>
    </li>
    <li>Membuat Model Product
        <p>Karena model telah dibuat bersamaan dengan file migrate, maka kemudian isikan kode berikut pada file app/product.php.</p>
{% highlight php linenos  %}
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class product extends Model
{
    protected $table='products';
    protected $fillable=['id_category','nama','harga','image','qty'];
    protected $hidden=['created_at','updated_at'];

    public function category()
    {
    	return $this->belongsTo(category::class,'id_category','id');
    }
}


{% endhighlight %}
        <p>Dari kode diatas baris ke-13 sampai ke-16 merupakan fungsi category yang akan mengambil data dari kelas/model category, dimana reference yang digunakan adalah id_category pada model product dan id pada model category. </p>
    </li>
    <li>Membuat Controller Product</li>
        <p>Untuk menyamakan lokasi file controller product dengan file category. Maka untuk membuat controllernya dengan perintah berikut: 
        <pre><code>hp artisan make:controller API/productController </code></pre>
        Dari perintah tersebut file controller product akan tersimpan difolder app/Http/controllers/API/. Kemudian buka file productController.php kemudian isikan code berikut:</p>
{% highlight php linenos  %}
<?php

namespace App\Http\Controllers\API;

use App\category;
use App\product;
use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Validator;

class productController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        /** 
         *  model 1
         */
        // $product=product::join('categories','products.id_category','=','categories.id')->select('products.*','categories.nama as nama_category')->get();
        // $data=$product->toArray();

        /** 
         *  model 2
         */
        $product=product::with(['category:id,nama'])->get();
        $data=$product->toArray();
        $response=[
            'success'=>true,
            'data'=>$data,
            'mesage'=>'Data Product Berhasil diambil'
        ];
        // return response()->json($response,200);
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
        $input =$request->all();
        $validator=Validator::make($input,[
            'id_category'=>'required',
            'nama'=>'required',
            'harga'=>'required',
            'image'=>'required|image|mimes:jpeg,png,jpg,gif,svg|max:2048',
            'qty'=>'required',
        ]);
        if ($validator->fails()){
            $response=[
                'success'=>false,
                'data'=>'Gagal Validasi',
                'message'=>$validator->errors()
            ];
            return response()->json($response,404);
        }
        $id=product::max('id');
        $id+=1;
        if ($request->hasFile('image')){
            $name=str_replace(' ','_',$input['nama']);
            $input['image']='/upload/products/'.$name.'_'.$id.'.'.$request->image->getClientOriginalExtension();
            $request->image->move(public_path('/upload/products/'),$input['image']);
        }
        $product=product::create($input);
        $data=$product->toArray();
        $response=[
            'success'=>true,
            'data'=>$data,
            'message'=>'Data Product Berhasil disimpan'
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
        $product=product::with(['category:id,nama'])->where('id',$id)->get();
        $data=$product->toArray();
        $response=[
            'success'=>true,
            'data'=>$data,
            'mesage'=>'Data Product Berhasil diambil'
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
        //mengambil data yang akan diedit
        $product=product::find($id);
        $data=$product->toArray();
        $category=category::orderBy('nama','ASC')->get()->pluck('nama','id');
        
        if(is_null($product)){
            $response=[
                'success'=>false,
                'data'=>'Kosong',
                'message'=>'Data Product tidak ditemukan'
            ];
            return response()->json($response,404);
        }
        $response=[
            'success'=>true,
            'data'=>$data,
            'combo'=>$category,
            'message'=>'Data berhasil ditemukan',
        ];
        return response()->json($response,200);
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
        $input=$request->all();
        $validator=Validator::make($input,[
            'id_category'=>'required',
            'nama'=>'required',
            'harga'=>'required',
            // 'image'=>'required',
            'qty'=>'required',
        ]);
        if ($validator->fails()){
            $response=[
                'success'=>false,
                'data'=>'Gagal Validasi',
                'message'=>$validator->errors()
            ];
            return response()->json($response,404);
        }
        $product=product::findorFail($id);
        if($request->hasFile('image')){
            if($product->image != NULL){
                unlink(public_path($product->image));
            }
            $input['image']='/upload/products/'.str_slug($input['nama'],'-').'_'.$input['id'].'.'.$request->image->getClientOriginalExtension();
            $request->move(public_path('/upload/products'),$input['image']); 
        }
        $product->update($input);
        $response=[
            'success'=>true,
            'data'=>$input,
            'message'=>'Data Produk Berhasil diupdate'
        ];
        return response()->json($response,200); 
    }
    /**
     * Remove the specified resource from storage.
     *
     * @param  int  $id
     * @return \Illuminate\Http\Response
     */
    public function destroy($id)
    {
        $product = Product::findOrFail($id);

        if (!$product->image == NULL){
            unlink(public_path($product->image));
        }
        Product::destroy($id);
        $response=[
            'success'=>true,
            'message'=>'Data Berhasil Dihapus'
        ];
        return response()->json($response,200);
    }
}
{% endhighlight %}
        <p>Dari code diatas baris ke-5 menunjukan import file/kelas model category. Kemudian pada function index  baris ke-29 mengambil data product dan data category dimana data category yang diambil sesuai dengan id_category pada table product.</p>
        <p>Kemudian pada function store baris ke-62 : melakukan validasi file image yang diupload dimana harus gambar dan berekstensi jpeg,png,jpg,gif,svg dan ukuran maximal 2048. Baris ke-73 : meload id terbesar dari tabel products dan disimpan ke variable $id (ini akan digunakan untuk memberikan nama pada file gambar yang akan diupload). baris ke-74 : menambahkan satu nilai pada variable $id. Baris ke-75 sampai ke-79 : proses kondisional, dimana jika data yang diinput menyertakan file maka akan diproses, jika tidak maka tidak diproses. Baris ke-76 : membuat nama file dengan mengambil nama product kemudian melakukan replace spasi dengan underscore dan disimpan di variable $name. Baris 77 : merubah value array di dalam $input['image'] menjadi /upload/products/[nama product]_[id product].[extensi bawaan file]. Baris ke-78 : menempatkan file ke dalam folder public/upload/products/.</p>
        <p>Pada function update sama dengan function store, hanya pada baris ke-162 sampai 164 : mengecek jika dalam melakukan update menyertakan gambar maka file sebelumnya akan dihapus terlebih dahulu, kemudian akan diganti dengan yang baru pada baris ke-165 dan ke-166. </p>
        <p>Kemudian buatkan folder /upload/products/ didalam folder public/ project laravel.</p>
    <li>Menambahkan Route
        <p>Setelah membuat controller product kemudian menambahkan route pada routes/api.php</p>
{% highlight php linenos  %}
Route::middleware('auth:api')->get('/user', function (Request $request) {
    return $request->user();
});
Route::post('login','API\UserController@login');
Route::post('register','API\UserController@register');
Route::group(['middleware'=>'auth:api'], function(){
    Route::post('details','API\UserController@details');
    Route::resource('category','API\categoryController');
    Route::resource('product','API\productController'); //tambahkan ini
    Route::post('logout','API\UserController@logout'); 
});
{% endhighlight %}
        <p>Penambahan dilakukan didalam Route::group, hal ini dilakukan supaya setiap kali mengakses cotroller product harus melewati authenticati terlebih dahulu (login).</p>
    </li>
    <li>Test CRUD
        <p>Setelah membuat database kemudian Model dan Controller, kita akan coba test CRUD dan Upload file nya. Silahkan buka aplikasi Postman, dan ikuti langkah berikut:</p>
        <ol type="a">
            <li>Login
                <p>Jika sudah mengikuti proses sebelumnya silahkan ubah HTTP Protocol ke POST, isikan Url postman "localhost:8000/api/login" kemudian klik tab params dibawah url dan isikan email dan password kemudian enter. Jika belum silahkan baca terlebih dahulu karena email dan password diperoleh setelah registrasi. Setelah terisi dan enter akan mendapat token passportnya. Copy token tersebut.
                <div align="center">
                    <img src="/assets/rest-api-laravel6/login_1.png" width="100%" alt="database relasi REST API laravel 6" >
                </div></p>
            </li>
            <li>Input Data dan Upload Gambar
                <p>Buka tab baru. Ubah HTTP Protocol ke POST, isikan url "localhost:8000/api/product". kemudian buka tab header dibawah url, isikan key:Accept value:application/json, key:Authorization value: Bearer[spasi][paste token], dan key: Content-Type value:application/x-www-form-urlencoded.
                <div align="center">
                    <img src="/assets/rest-api-laravel6/login_pastetoken.png" width="100%" alt="database relasi REST API laravel 6" >
                </div></p> 
                <p>Kemudian pindah ke tab Body kemudian radiobutton form-data, isikan key susai atribut table product. pada atribut image pilih type file. lihat gambar:
                <div align="center">
                    <img src="/assets/rest-api-laravel6/product_textkefile.png" width="100%" alt="database relasi REST API laravel 6" >
                </div></p>
                <p>kemudian isikan value yang akan diinputkan. contoh:
                <div align="center">
                    <img src="/assets/rest-api-laravel6/product_inputfile.png" width="100%" alt="database relasi REST API laravel 6" >
                </div></p>
                <p>Kemudian send. akan mendapat return sebagai berikut:
                <div align="center">
                    <img src="/assets/rest-api-laravel6/product_returninput.png" width="100%" alt="database relasi REST API laravel 6" >
                </div></p>
                <p>Kemudian cek file image apakah terupload?. buka folder project laravel /public/upload/products/.
                <div align="center">
                    <img src="/assets/rest-api-laravel6/product_image.png" width="100%" alt="database relasi REST API laravel 6" >
                </div></p>
                <p>Silahkan tambah lagi datanya.</p>
            </li>
            <li>View Data
                <p>Kemudian buka tab baru ubah HTTP Protocol ke GET. Isikan url "localhost:8000/api/product". Kemudian buka tab header dibawah url, isikan key:Accept value:application/json, key:Authorization value: Bearer[spasi][paste token], dan key: Content-Type value:application/x-www-form-urlencoded. Kemudian Send. <b>Note:</b> tidak perlu mengisi tab params dan Body -> form-data. 
                <div align="center">
                    <img src="/assets/rest-api-laravel6/product_lihat.png" width="100%" alt="database relasi REST API laravel 6" >
                </div>
                </p>
            </li>
            <li>Update Data
                <p>Buka tab baru ubah HTTP Protocol ke PUT. Isikan url "localhost:8000/api/product/1" (ini mengeset bahwa data product yang akan diupdate idnya 1). Kemudian buka tab header dibawah url, isikan key:Accept value:application/json, key:Authorization value: Bearer[spasi][paste token], dan key: Content-Type value:application/x-www-form-urlencoded. Kemudian klik tab params dibawah url. Isikan key dan value sesuai atribut table product. <b>Note:</b>karena tidak ada fitur file di tab params maka image tidak perlu di cantumkan, saya mencoba dengan from-data pada tab body, akan tetapi tidak dapat melakukan update karena waktu proses send katanya tidak terdapat file yang dikirim, jika teman teman menemukan cara lain untuk melakukan update file dari postman, saya berharap share ilmunya, hehe. Kemudian Send.
                <div align="center">
                    <img src="/assets/rest-api-laravel6/product_update.png" width="100%" alt="database relasi REST API laravel 6" >
                </div>
                </p>
            </li>
            <li>Delete Data
                <p>Buka tab baru ubah HTTP Protocol ke DELETE. Isikan url "localhost:8000/api/product/2" (ini mengeset bahwa data product yang akan dihapus idnya 2). Kemudian buka tab header dibawah url, isikan key:Accept value:application/json, key:Authorization value: Bearer[spasi][paste token], dan key: Content-Type value:application/x-www-form-urlencoded. Kemudian Send. 
                <div align="center">
                    <img src="/assets/rest-api-laravel6/product_delete.png" width="100%" alt="database relasi REST API laravel 6" >
                </div>
                </p>
                <p>Kemudian lakukan pengecekan dengan melakukan view data.
                <div align="center">
                    <img src="/assets/rest-api-laravel6/product_lihat1.png" width="100%" alt="database relasi REST API laravel 6" >
                </div></p>
                <p>Terlihat bahwa data product dengan id 2 sudah tidak ada. Begitu juga file gambar didalam folder public/upload/products/.</p>
            </li>
            <li>Log out
                <p>Melakukan logout dengan membuka tab baru ubah HTTP Protocol ke POST. Isikan url "localhost:8000/api/logout" . Kemudian buka tab header dibawah url, isikan key:Accept value:application/json, key:Authorization value: Bearer[spasi][paste token]. Kemudian Send.
                    <div align="center">
                        <img src="/assets/rest-api-laravel6/product_logout.png" width="100%" alt="database relasi REST API laravel 6" >
                    </div></p>
            </li>
        </ol>
    </li>
</ol>
<h3>Kesimpulan</h3>
<p>Ngoding itu seni, saya menyebutnya begitu. Banyak cara untuk melakukan upload file dan CRUD berelasi. Didalam project yang kita buat masih terdapat kelemahan yaitu ketika melakukan update file, file image tidak tervalidasi. Jika Validasi "image" pada function update dibuka maka jika melakukan update tanpa melakukan input file, akan mengalami gagal validasi. Saya menyarankan menambahkan validasi didalam kondisional jika image memiliki file baris ke-161.
{% highlight php %}
if($request->hasFile('image')){ 
    $validator=Validator::make($input,[
            'image'=>'image|mimes:jpeg,png,jpg,gif,svg|max:2048',
    ]);
    if ($validator->fails()){
        $response=[
            'success'=>false,
            'data'=>'File tidak valid : cek extensi jpeg,png,jpg,gif,svg dan ukuran >=2048 byte',
            'message'=>$validator->errors()
        ];
        return response()->json($response,404);
    }
    ....
}
{% endhighlight  %} 
</p>
<p>Semoga bermanfaat, mohon kritik dan sarannya. Project dapat diunduh <a href="https://github.com/msyrh/Rest_Api_Laravel_6">disini</a>.</p>