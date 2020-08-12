# Passo a passo para o desenvolvimento

### Passo a passo para utilização do Laravel

[https://laravel.com/docs/7.x/installation](https://laravel.com/docs/7.x/installation)

1. **Instalar o Xampp**

	[https://www.apachefriends.org/pt_br/download.html](https://www.apachefriends.org/pt_br/download.html)
	
2. **Após a instalação, verificar se o PHP foi instalado corretamente.**
	Abra uma janela de comando e digite o seguinte comando:

		` php -v `  
	
  	Se tudo estiver correto vai aparecer a seguinte mensagem:
	
		```
		PHP 7.4.2 (cli) (built: Jan 21 2020 17:52:43) ( ZTS Visual C++ 2017 x64 )
		Copyright (c) The PHP Group
		Zend Engine v3.4.0, Copyright (c) Zend Technologies
		```

3. **Instalar o composer**

	[https://getcomposer.org/download/](https://getcomposer.org/download/)
	
4. **Após a instalação, verificar se o Compoer foi instalado corretamente.**

	Abra uma janela de comando e digite o seguinte comando:

	'composer -v'

	Se tudo estiver correto vai aparecer a seguinte mensagem:

```	 ______  
	/ ____/___  ____ ___  ____  ____  ________  _____  
	/ /   / __ \/ __ `__ \/ __ \/ __ \/ ___/ _ \/ ___/  
	/ /___/ /_/ / / / / / / /_/ / /_/ (__  )  __/ /  
	\____/\____/_/ /_/ /_/ .___/\____/____/\___/_/  
                    /_/  

	Composer version 1.9.3 2020-02-04 12:58:49  

	Usage:
		command [options] [arguments]
```

5. **Instalar o laravel**
	
	Para criar um projeto novo utilizando o laravel, abra uma janela de comando e vá para a pasta onde o projeto será criado.
		No meu caso `c:\laragon\wwww` e digite o seguinte comando:
		
	'composer create-project --prefer-dist laravel/laravel coretectools'
	
6. **Após a criação do projeto vá para a pasta criada.**
	No meu caso *c:\laragon\www\coretectools-v0*

7. **Crie o banco de dados a ser utilizado.** 
	No meu caso será criado o seguinte BD:
	
  	'bdCoretec'

8. Altere o arquivo .env com o nome do BD. No meu caso será criado o BD bdCoretec

    ```
	DB_CONNECTION=mysql  
	DB_HOST=127.0.0.1  
	DB_PORT=3306  
	DB_DATABASE=bdCoretec  
	DB_USERNAME=root  
	DB_PASSWORD=
	```  

9. **Após a criação do banco de dados e a alteração do .env com as informações de conexão, vamos criar as tabelas através do comando abaixo:**
	
  	`php artisan migrate`
  
10. **Criando a parte de login da aplicação**

	[https://laravel.com/docs/7.x/authentication](https://laravel.com/docs/7.x/authentication)
	
	Executar os comandos abaixo:
        
	```
	composer require laravel/ui   
	php artisan ui bootstrap --auth   
	npm install   
	npm run dev   
	php artisan migrate:fresh
	```
		
11. **Para que possa ser enviado email através da aplicação, o .env deverá ser alterado da seguinte forma:**

	```
    MAIL_MAILER=smtp   
	MAIL_HOST=smtp.gmail.com   
	MAIL_PORT=587   
	MAIL_USERNAME=coretectools@gmail.com   
	MAIL_PASSWORD=xatgpkgpytcibuyj   
	MAIL_ENCRYPTION=tls   
	MAIL_FROM_ADDRESS=coretectools@gmail.com   
	MAIL_FROM_NAME="${APP_NAME}"
    ```

12. **Alterar as configurações para solicitar a confirmação do email após o cadastro**

    [https://laravel.com/docs/7.x/verification](https://laravel.com/docs/7.x/verification)

	
	Para que a verificação de email seja feita com sucesso é necessário fazer as seguintes alterações:
	

  	1. Arquivos routes/web.php
		Auth::routes(['verify' => true]);
	
	2. Para cada controller que necessite a verificação de confoirmação do email é necessário ea alteração abaixo:
	
  		```
		public function __construct()
		{
			$this->middleware('auth');
			$this->middleware('verified');        
		}
		```


13. **Autenticação com o Google**

	[https://appdividend.com/2018/04/11/laravel-google-login-tutorial/](https://appdividend.com/2018/04/11/laravel-google-login-tutorial/)  
	[https://console.developers.google.com/](https://console.developers.google.com/)
	[https://medium.com/employbl/add-login-with-google-to-your-laravel-app-d2205f01b895](https://medium.com/employbl/add-login-with-google-to-your-laravel-app-d2205f01b895)
	

  	* Alterar o arquivo '2014_10_12_000000_create_users_table.php' de acordo com as informações abaixo:
	
  		```
		$table->string('google_id')->nullable();
    	$table->string('google_avatar')->nullable();
		```

	* Serão incluidos na tabela user os dois campos informados acima. Após a alteração do arquivo executar o comando abaixo para atualizar a tabela users
	
		`php artisan migrate:fresh`
		
	* Após a alteração da tabela seguir os passos
	
	  	'composer require laravel/socialite'
	
	* Localize os providers no arquivo 'config >> app.php' file e inclua o SocialiteServiceProvider.

  		''providers' => [
				// ...
				Laravel\Socialite\SocialiteServiceProvider::class,
			]'

	* Localize os aliases no arquivo config >> app.php file e inclua os aliases abaixo.

  		''aliases' => [
				// ...
				'Socialite' => Laravel\Socialite\Facades\Socialite::class,
			]'

	* Vá para o portal de desenvolvedor do Google [https://console.developers.google.com](https://console.developers.google.com) e crie o token para sua aplicação
	
	* Altere o arquivo 'config >> services.php' e inclua as seguintes linhas: 
	
    	''google' => [
        'client_id' =>  env('GOOGLE_CLIENT_ID'),
        'client_secret' => env('GOOGLE_CLIENT_SECRET'),
        'redirect' => env('GOOGLE_CALLBACK_URL'),
		  ],'
    	
	* Crie o controller para o Google
	
		'php artisan make:controller SocialAuthGoogleController'
	
	* Altere o controler para o codigo abaixo:
	
    	'
		<?php

		namespace App\Http\Controllers;

		use Illuminate\Http\Request;
		use App\User;
		use Socialite;
		use Auth;
		use Exception;

		class SocialAuthGoogleController extends Controller
		{
			public function redirect_google()
			{
				return Socialite::driver('google')->redirect();
			}


			public function callback_google()
			{
				try {
					
				
					$googleUser = Socialite::driver('google')->user();
					$existUser = User::where('email',$googleUser->email)->first();
					

					if($existUser) {
						Auth::loginUsingId($existUser->id);
					}
					else {
						$user = new User;
						$user->name = $googleUser->name;
						$user->email = $googleUser->email;
						$user->google_id = $googleUser->id;
						$user->google_avatar = $googleUser->avatar;
						$user->password = md5(rand(1,10000));
						$user->save();
						Auth::loginUsingId($user->id);
					}
					return redirect()->to('/home');
				} 
				catch (Exception $e) {
					return 'Erro de Autenticação no Google';
				}
			}
		}'
  	
	* Inclua as seguintes rotas no arquivo 'ROUTES >> web.php'
	
    	'Route::get('/redirect_google', 'SocialAuthGoogleController@redirect_google');
		Route::get('/callback_google', 'SocialAuthGoogleController@callback_google');'

	* Na view 'login.blade.php' inclua o link para login com o Google
	
    	'
		<a href="{{ url('/redirect_google') }}" type="button" class="btn btn-block btn-outline-danger"><i class="fab fa-google"></i> {{ __('Login with Google') }}</a>
		'

	* Agora só testar
	
14. **Autenticação com o Facebook**
		[https://appdividend.com/2017/07/12/laravel-facebook-login/](https://appdividend.com/2017/07/12/laravel-facebook-login/)
  		[https://developers.facebook.com/apps/](https://developers.facebook.com/apps/)
	

	* Alterar o arquivo '2014_10_12_000000_create_users_table.php' de acordo com as informações abaixo:
	
    	'$table->string('facebook_id')->nullable();
    	$table->string('facebook_avatar')->nullable();'
	
	* Serão incluidos na tabela user os dois campos informados acima. Após a alteração do arquivo executar o comando abaixo para atualizar a tabela users
	
		'php artisan migrate:fresh'
		
	* Após a alteração da tabela seguir os passos
	
	  'composer require laravel/socialite'
	
	* Localize os providers no arquivo config >> app.php file e inclua o SocialiteServiceProvider.

  		''providers' => [
				// ...
				Laravel\Socialite\SocialiteServiceProvider::class,
			]'

	* Localize os aliases no arquivo 'config >> app.php' file e inclua os aliases abaixo.

  		''aliases' => [
				// ...
				'Socialite' => Laravel\Socialite\Facades\Socialite::class,
			]'

	* Vá para o portal de desenvolvedor do Facebook [https://developers.facebook.com/](https://developers.facebook.com/) e crie o token para sua aplicação
	
	* Altere o arquivo 'config >> services.php' e inclua as seguintes linhas: 
	
  '
    'facebook' => [
      'client_id' =>  env('GOOGLE_CLIENT_ID'),
      'client_secret' => env('GOOGLE_CLIENT_SECRET'),
      'redirect' => env('GOOGLE_CALLBACK_URL'),
    ],
  '
	
	* Crie o controller para o Google
	
		'php artisan make:controller SocialAuthFacebookController'
	
	* Altere o controler para o codigo abaixo:
	
  '
	<?php

	namespace App\Http\Controllers;

	use Illuminate\Http\Request;
	use App\User;
	use Socialite;
	use Auth;
	use Exception;

	class SocialAuthFacebookController extends Controller
	{

		public function redirect_facebook()
		{
			return Socialite::driver('facebook')->redirect();
		}

		public function callback_facebook()
		{
			try {
				
				$facebookUser = Socialite::driver('facebook')->user();
				$existUser = User::where('email',$facebookUser->email)->first();

				if($existUser) {
					Auth::loginUsingId($existUser->id);
				}
				else {
					$user = new User;
					$user->name = $facebookUser->name;
					$user->email = $facebookUser->email;
					$user->facebook_id = $facebookUser->id;
					$user->facebook_avatar = $facebookUser->avatar;
					$user->password = md5(rand(1,10000));
					$user->save();
					Auth::loginUsingId($user->id);
				}
				return redirect()->to('/home');
			} 
			catch (Exception $e) {
				return 'Erro de Autenticação no Facebook';
			}
		}
	}
	'

	* Inclua as seguintes rotas no arquivo 'ROUTES >> web.php'

  '	
		Route::get('/redirect_google', 'SocialAuthGoogleController@redirect_google');
		Route::get('/callback_google', 'SocialAuthGoogleController@callback_google');
  '

	Na view 'login.blade.php' inclua o link para login com o Google
	
  '
	<a href="{{ url('/redirect_facebook') }}" type="button" class="btn btn-block btn-outline-primary"><i class="fab fa-facebook"></i> {{ __('Login with Facebook') }}</a>
  '

	* Agora só testar

15. Adicionando um projeto existente a um repositorio GitHub existente 
	[https://docs.github.com/en/github/importing-your-projects-to-github/adding-an-existing-project-to-github-using-the-command-line](https://docs.github.com/en/github/importing-your-projects-to-github/adding-an-existing-project-to-github-using-the-command-line)
	
16. Publicando a aplicação no Google App Engine
	  [https://medium.com/hackernoon/how-to-deploy-a-laravel-web-app-on-google-app-engine-ecfbb0d49b00](https://medium.com/hackernoon/how-to-deploy-a-laravel-web-app-on-google-app-engine-ecfbb0d49b00)
	
17. Configurando o MySQL no Google Cloud
	
  '
	Instance name: sqlcoretec
	Instance pwd: crtls@2020
	Instance connection name: coretectools-283503:us-east1:sqlcoretec
	Region: US-east1 (south carolina)
	'

  [https://www.youtube.com/watch?v=QhQ1PrYH3q0](https://www.youtube.com/watch?v=QhQ1PrYH3q0)
	
	
18. FAQ

  1. ### ERRO 1
  Error Unable to prepare route [api/user] for serialization. Uses Closure.

    * ### Solução 1:
    Execute o comando: 'php artisan route:list' e veja as closures. Necessario alterar para Controllers.

    Verifique o arquivo 'routes/api.php' Lá tem uma rota default do Laravel. Remova ou comente esta rota e teste novamente.

    '
    Route::middleware('auth:api')->get('/user', function (Request $request) {
        return $request->user();
    });
    '

    * ### Solução 2:
	    [https://stackoverflow.com/questions/45266254/laravel-unable-to-prepare-route-for-serialization-uses-closure](https://stackoverflow.com/questions/45266254/laravel-unable-to-prepare-route-for-serialization-uses-closure)

  2. ### ERRO 2
  Após executar o comando 'gcloud app deploy' no google cloud terminal, apresenta o seguinte erro:

  '
  {\"ResourceType\":\"compute.beta.regionAutoscaler\",
    \"ResourceErrorCode\":\"403\",
    \"ResourceErrorMessage\":{\"code\":403,
    \"message\":\"The caller does not have permission\",
    \"status\":\"PERMISSION_DENIED\",
    \"statusMessage\":\"Forbidden\", 
    \"requestPath\":\"https://compute.googleapis.com/compute/beta/projects/<MYAPPNAME>/regions/europe-west1/autoscalers\",
      \"httpMethod\":\"POST\"}	
  '

    * ### Solução 1:
    Altere o arquivo 'app.yaml' e adicione as linhas abaixo:

    '
    automatic_scaling:
        min_num_instances: 1
        max_num_instances: 7 
    '



XX - VERIFICAR - Customização de email

https://medium.com/@christianjombo/customizing-laravels-default-notification-email-template-adding-a-logo-and-changing-ff6f107dd640
https://medium.com/@lordgape/custom-verification-email-notification-laravel-5-8-573ba3183ead
https://blog.mailtrap.io/laravel-email-verification/
https://www.youtube.com/watch?v=HCe700GIpz4
https://laracasts.com/discuss/channels/laravel/how-to-create-custom-email-verification-template-laravel-57

XX - Modules
	https://nwidart.com/laravel-modules/v6/introduction
	
	https://www.diegobrocanelli.com.br/php/laravel-module/?unapproved=625&moderation-hash=b1d103d1b5a6ab80f3dd83a6e9abbfe0#comment-625



git add .
git push origin master
gcloud app deploy
gcloud app browse


------------------------------------------------------
ferreomodelo@gmail.com

instancia mySQL
ID: mysql-inst
Pwd: laravel@2020ufcat
Nome da conexao da instancia: coretectools-285601:us-central1:mysql-inst
BD: bdcoretectools

cloud_sql_proxy -instances=coretectools-285601:us-central1:mysql-inst=tcp:3306



Erro
Step #1: In Connection.php line 664:
Step #1:                                                                                
Step #1:   SQLSTATE[HY000] [2002] No such file or directory (SQL: delete from `cache`)  

Solução:
https://stackoverflow.com/questions/49127983/laravel-project-gcloud-app-deploy-failed


Troca do Icone da pagina
https://www.it-swarm.dev/pt/laravel/como-ligar-o-icone-favicon-em-laravel/1050235952/



<div class="fb-login-button" data-size="large" data-button-type="continue_with" data-layout="rounded" data-auto-logout-link="false" data-use-continue-as="false" data-width=""></div>

