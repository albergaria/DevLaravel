# Implementação de novos módulos
A seguinte forma de implementar novos módulos foi estipulada para que haja uma organização no projeto. A forma de implementação abaixo deve ser seguida rigorosamente.

Esta é a forma árvore de pastas do projeto:
```
.
├──app
├──bootstrap
├──config
├──database
├──node_modules
├──public
├──resources
├──routes
├──storage
├──tests
└──vendor
```
Suas subpastas serão explanadas aos poucos, mas o implementador irá utilizar basicamente as pastas `app`, `routes`,`database`, `public` e `resources`. 

## Controllers
Cada um dos módulos terá uma pasta para seus `Controllers` na pasta raiz de `Controllers`. Abaixo é mostrado o exemplo do caminho da pasta de `Controllers` do módulo Concrete e Mixer.
```
.
├──...
├──app
│  ├──...
│  └──Http
│  │  ├──...
|  |  └──Controllers
|  |  |  ├──Concrete
|  |  |  |  └──ConcreteController.php
|  |  |  └──Mixer
|  |  |  |  └──MixerController.php
```

## Rotas
As rotas de cada módulo serão separadas por um comentário de início e fim de rotas no arquivo `web.php` dentro de `routes`. Abaixo é mostrado o exemplo das rotas dos módulos Concrete e Mixer.

```
/*---------------      Concrete Routes       -------------*/

Route::get('/concrete', 'ConcreteController@index');

/*---------------      End Concrete Routes       ---------*/

/*---------------      Mixer Routes       -------------*/

Route::get('/mixer', 'MixerController@index');

/*---------------      End Mixer Routes       ---------*/
```

## Views
Cada um dos módulos terá uma pasta para suas `views` dentro da pasta `views` localizada em `resources`. Abaixo o exemplo das `views` dos módulos Concrete e Mixer.
```
├──...
├──resources
│  ├──...
│  └──views
│  │  ├──...
|  |  ├──Concrete
|  |  |  └──index.blade.php
|  |  └──Mixer
|  |  |  └──index.blade.php
```

## VueJS/Webpackmix
Alguns dos módulos podem precisar utilizar componentes de [VueJS](https://vuejs.org/) ou armazenar arquivos JavaScript. Logo é necessário uma organização na pasta `js` dentro da pasta `resources`.

Cada módulo terá uma pasta para seus aquivos `.js` dentro da pasta `js` dentro de `resources`. Assim como para cada módulo que precisar utilizar componentes VueJS terá uma pasta dentro da pasta `components`. Abaixo um exemplo da utilização de ambas pastas para o módulo Concrete
```
├──...
├──resources
│  ├──...
│  └──js
│  │  ├──...
|  |  ├──components
│  │  |  ├──...
|  |  |  └──Concrete
|  |  |  |  └──Flexao.vue
|  |  ├──Concrete
|  |  |  └──flexao.js
|  |  └──bootstrap.js
```
Alterações serão necessárias no arquivo `webpack.mix.js`. Os módulos devem separar suas declarações por comentários de início e fim das declarações. Um exemplo dessas declarações é mostrado abaixo.
```
/*------------      Concrete Components       -------------*/  
  
mix.js('resources/js/Concrete/flexao.js', 'public/js/Concrete'); 
  
/*-----------      End Concrete Components       ----------*/
```

Pode ser necessário a inclusão de componentes no layout. É recomendado deixar um comentário para saber a a origem da inclusão do componente na view de layout.


## Classes Auxiliares
Cada um dos módulos terá uma pasta para suas classes auxiliares na pasta `Helpers` localizada em `app`. Abaixo é mostrado o exemplo do caminho da pasta de `Helpers` do módulo Concrete e Mixer.

```
.
├──...
├──app
│  ├──...
│  └──Helpers
│  │  ├──...
|  |  ├──Concrete
|  |  |  └──Cisalhamento.php
|  |  └──Mixer
|  |  |  └──ABCP.php
```
