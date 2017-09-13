# Adianti Framework  Com Vuejs

## Importando as Dependencias

Bem para nosso primeiro artigo vamos mostrar como integrar o vuejs com o Adianti Framework, 
um otimo framework php voltado para o desenvolvimento de sistemas.

Inicialmente vamos importar as bibliotecas que iremos usar, nesse exemplo iremos usar o Vuejs titulo do artigo e a lib axios usadas para 
requisição ajax.

em seu arquivo em app/templates/theme3/libraries.html insira as seguintes linhas no final desse aqruivo, em nosso exemplo estamos usando template
ERP3, mas esse procedimento pode ser feito em qualquer template.

```html

   <!-- import Vue-->
   <script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.4.2/vue.min.js"></script>

    <script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.16.2/axios.js"
    
```

apos isso, iremos fazer nosso controller essa versão 4 do framework permite a criação de services, algo bem bacana que iremos usar, claro
futuramente irei escrever essa integração de forma mais organizada, mas para nosso primeiro contato , esta forma demostra de forma
bem didatica como fazer.

em nosso controller faremos assim

```php

class AtendenteController extends TPage

 {

    
    public function onReload(){
    
            try{
        
        parent::__construct();
        $html = new THtmlRenderer('app/view/listar.html');
        
        $html->enableSection('main');
        
        parent::add($html);
        
        }
        catch(Exception $e){
        
        new TMessage('error',$e->getMessage()); 
       
        }
        
    }
    
    

}
    
```
Notem que nosso controller renderiza uma view html, algo ouco explorado no framework ,
mas que permite coisas que irão se surpreender.

abora vamos fazer nossa pagina HTML, para manter organizado gosto de colocar o HTML na pasta view, mas cada um organiza como achar melhor






```html
<!--[main]-->
<div id="app">

 <table class="table table-hover">
  <thead>
    <tr>
      <th>#</th>
      <th>Name</th>
      <th>Email</th>
     
    </tr>
  </thead>
  <tbody>
    <tr v-for="item in itens">
      <th scope="row">{{item.id}}</th>
      <td>{{item.name}}</td>
      <td>{{item.email}}</td>
    </tr>
  </tbody>
</table>
</div>
<script src="https://cdnjs.cloudflare.com/ajax/libs/axios/0.16.2/axios.js"></script>
<script src="app/view/listar.js"></script>
<!--[/main]-->
   
 ```
 
 percebam que importei o axios novamente e também um listar que esta na pasta view do framework
 o motivo de importar o axios novamente é que o framework não carrega toda a pagina novamente, assim ele não carrega o axios para que seja usado aqui
 pelo menos foi oque pareceu, e por esse motivo carrego ele novamente.
 
 Notem que nesse HTML tenho ``` <div id="app" ``` que é usado pelo vuejs, lembrando que essa forma é totalmente com fins de datica.
 agora vamos comessar a bincar com nosso listar.js
 
 ```js
 
 new Vue({
  el: '#app',
  data: {
    itens: []
  },
  
  created: function(){
    setInterval(this.atualiza1, 5000);
    
  },
  
  methods: {
  
      atualiza : function(){
      
      vm = this;
      axios.get('engine.php?class=AtendenteService')
      .then(function (response) {
      
      vm.itens = response.data;
      
        console.log(response);
      })
      .catch(function (error) {
        console.log(error);
      })
      
  }
  
  }
})

 ```
 
 então não iremos abordar a parte de vuejs, acredito que esse será um dever de casa hahaha bons tempos.
 então com o axios temos o pulo do gato
 
 ```js
 axios.get('engine.php?class=AtendenteService')
 ```
 
 ai perguntam, porra alexandre que merda é essa, isso é para que possamos chamar o serviço AtendenteService que irei mostrar logo mais
 claro que irei refatorar isso para usar uma class de rest, mas será mo artigo adianti framework rest apis :)
 
 vamos ver como ficou nossa class de service, na pasta app/service
 
 
  ```php
  
  <?php

class AtendenteService {

    public function getData(){
    
    try{
    
    TTransaction::open('permission');
    
   
    
    $users = SystemUser::getObjects();
    $obj = [];
    
    foreach($users as $user){
    
    $obj_user =  ["id"=>$user->id,"name"=>$user->name, "email"=>$user->email];
    
    $obj[] = $obj_user;
    }
    

    
    TTransaction::close();
    
   return  json_encode($obj);
    
    }catch(Exception $e){
    
    echo "error ".$e->getMessage();
    }
  }  
    
    public function show(){
    
    echo $this->getData();
    
    }

}


   ```
 
 infelizmente não podemos criar services 100% no adianti , então todo service tem que ter o metodo show, a  menos que seja algum metodo statico
 
 feito isso é so dar permissão ao nosso controller e nosso service e testar, nesse exemplo ele ira atualizar a listagem a cada 5s
 
 
 espero que gostem :)
