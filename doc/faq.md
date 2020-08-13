
# FAQ

  ### ERRO 1  

  **Error Unable to prepare route [api/user] for serialization. Uses Closure.**

  1. **Solução 1:**  

    Execute o comando `php artisan route:list` e veja as closures. Necessario alterar para Controllers.  

    Verifique o arquivo `routes/api.php` Lá tem uma rota default do Laravel. Remova ou comente esta rota e teste novamente.  

    ```
    Route::middleware('auth:api')->get('/user', function (Request $request) {
        return $request->user();
    });
    ```

  2. **Solução 2**  

  [https://stackoverflow.com/questions/45266254/laravel-unable-to-prepare-route-for-serialization-uses-closure](https://stackoverflow.com/questions/45266254/laravel-unable-to-prepare-route-for-serialization-uses-closure)


  ### ERRO 2  

  **Após executar o comando `gcloud app deploy` no google cloud terminal, apresenta o seguinte erro:**  

  ```
  {\"ResourceType\":\"compute.beta.regionAutoscaler\",
    \"ResourceErrorCode\":\"403\",
    \"ResourceErrorMessage\":{\"code\":403,
    \"message\":\"The caller does not have permission\",
    \"status\":\"PERMISSION_DENIED\",
    \"statusMessage\":\"Forbidden\", 
    \"requestPath\":\"https://compute.googleapis.com/compute/beta/projects/<MYAPPNAME>/regions/europe-west1/autoscalers\",
      \"httpMethod\":\"POST\"}	
  ```

  1. **Solução 1:**  

    Altere o arquivo `app.yaml` e adicione o código abaixo:

      ```
      automatic_scaling:
          min_num_instances: 1
          max_num_instances: 7 
      ```
      
