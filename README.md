# SystemsWayAPI
Documentação para a API SystemsWay

[Login](https://github.com/SystemsWay/SystemsWayAPI/blob/master/README.md#login)

[Gerência de Usuários](https://github.com/SystemsWay/SystemsWayAPI/blob/master/README.md#ger%C3%AAncia-de-usu%C3%A1rio)

# Introdução

A API da Systems Way foi criado com o intuito de facilitar a integração do WAY com demais aplicações e permitir que um mesmo 
sistema responda a solicitações das diversas plataformas que serão utilizadas pelo WAY.

## Detalhes técnicos
  > * É implementado utilizando .Net Core 2.0.
  > * Utiliza protocolo HTTP,
  > * Basicamente é uma WEB API RESTFul, respondendo ao métodos GET, POST, PUT e DELETE com suas features específicas, atendendo às necessidades da SystemWay.
  > * Todos os dados de login (senha) são salvos e trafegados utilzando o Algoritmo SHA256 com 64 caracteres. ([Mais detalhes sobre SHA256](http://www.iwar.org.uk/comsec/resources/cipher/sha256-384-512.pdf))
  > * O caminho padrão da API é definido como sendo http://IP:5000/api/function, onde IP é o endereço de ip onde o mesmo está hospedado, e function é a função/módulo que está sendo requisitado.
  
**Obs.: Para os exemplos desse tutorial, assuma que a API está ospedada em localhost**

# Considerações iniciais
  > * É necessário realizar login somente uma vez, cada login realizado cria uma sessão, as demais atividades serão realizadas conforme essa sessão.
  > * Caso queira trocar a sessão, basta realizar o login novamente, neste caso, a sessão antiga será inativada. E a nova sessão válida será a retornada no login.
  > * Não será possível conseguir o ID de uma sessão, exceto com a realização de um novo login.
  > * Todos os dados criados e manipulados por umas sessão são associados à empresa associada ao usuário da sessão.
  > * Um usuário possui somente uma sessão ativa.
  
# Login
  Para realizar login deve solicitar um médoto Post para a Função 'Main' informando um request com o nome do usuário e o hash SHA256 da senha.
  Exemplo de json de solicitação de login.
  >URL: http://localhost:50000/api/main
  ```javascript
  {
    "User":"admin",
    "Passwd":"46070D4BF934FB0D4B06D9E2C46E346944E322444900A435D7D9A95E6D7435F5"
  }
  ```
  Será retornado outro json que pode assumir dois possíveis casos
  1. Login correto:
  Neste caso o json retorna o ID da sessão:
  ```javascript
  {
    "Status":"OK",
    "IDSessao":"46070D4BF934FB0D4B06D9E2C46E346944E322444900A435D7D9A95E6D7435F5",
    "Message":"Login Realizado com sucesso"
  }
  ```
  2. Login incorreto
  ```javascript
  {
    "Status":"ERROR",
    "IDSessao":"",
    "Message":"Usuário ou senha estão inválidos"
  }
  ```
  > * Um usuário pode receber ERROR ao tentar realizar um login por um dos motivos: usuário ou senha inválidos, usuário inativo, empresa do usuário inativa.

# Gerência de Usuário
> Link base: http://localhost:50000/Api/Usuario
## Get
	Recebe como parâmetro o id da sessão (Obrigatório) e o id do usuário (opcional).
### Exemplo de link sem id do usuário:
> http://localhost:5000/api/Usuario/EB078EB1ABFC9722EBAB9BA3DD303AFD37DD1AE50A791CBA16ABEE0A727029DF

Para este exemplo será retornado todos os usuários cadastrados para a mesma empresa do usuário da sessão informada.
	
Exemplo de Retorno:
```javascript
[{
    "Id":"1",
    "UserName":"admin",
    "Ativo":"true"
  },
  {
    "Id":"2",
    "UserName":"user2",
    "Ativo":"true"
 }]
 ```
 ### Exemplo de link com id do usuário:
 > http://localhost:5000/api/Usuario/EB078EB1ABFC9722EBAB9BA3DD303AFD37DD1AE50A791CBA16ABEE0A727029DF?id=1
 
 Para este exemplo será retornado o usuário cadastrado com o id igual a 1
 Exemplo de Retorno:
 ```javascript
[{
    "Id":"1",
    "UserName":"admin",
    "Ativo":"true"
  }]
 ```
 * Caso o ID da sessão informado na URL sejá inválido, será retornado um json com status ERROR, informando que o ID da sessão está inválido. Neste caso, verifique se o id está correto, caso esteja, loge novamente, a sessão pode ter sido inativada pelas rotinas da API (inativação por inatividade, por alteração no usuário vínculado ou alteração no registro da empresa do usuário)
 
 ## Post
 Recebe um json e o id da sessão do usuário
 Exemplo de link:
 > http://localhost:5000/api/Usuario/EB078EB1ABFC9722EBAB9BA3DD303AFD37DD1AE50A791CBA16ABEE0A727029DF
 Arquivo json associado:
  ```javascript
{
    "UserName":"user3",
    "PassWord":"EB078EB1ABFC9722EBAB9BA3DD303AFD37DD1AE50A791CBA16ABEE0A727029DF",
    "Ativo":"true"
 }
 ```
 Caso o usuário seja cadastrado, será retornado um json informando que o usuário foi cadastrado, tal como o do exemplo:
  ```javascript
{
    "Status":"OK",
    "IDSessao":"EB078EB1ABFC9722EBAB9BA3DD303AFD37DD1AE50A791CBA16ABEE0A727029DF",
    "Message":"",
    "StackTrace":""
 }
 ```
 Caso o usuário não seja cadastrado, será retornado um json infotmando o problema:
   ```javascript
{
    "Status":"ERROR",
    "IDSessao":"EB078EB1ABFC9722EBAB9BA3DD303AFD37DD1AE50A791CBA16ABEE0A727029DF",
    "Message":"",
    "StackTrace":""
 }
 ```
 Onde a mensagem pode ser uma das seguintes (Assumindo que o ID da sessão seja válido):
 > * O formato de senha está incorreto, a senha deve estar sob o Hash SHA/256, com 64 caracteres.
 > * O nome do usuário não pode conter espaços
 > * Já Existe um usuário cadastrado com este nome.
 
 ## Put
 > Link Base: http://localhost:5000/api/Usuario/EB078EB1ABFC9722EBAB9BA3DD303AFD37DD1AE50A791CBA16ABEE0A727029DF?id=1
 
 Recebe um arquivo na URL o id da Sessão e o ID do usuário, ambos obrigatórios, e um arquivo json com os novos dados do usuário.
 Exemplo de arquivo json:
 
 ```javascript
{
    "UserName":"admin",
    "PassWord":"EB078EB1ABFC9722EBAB9BA3DD303AFD37DD1AE50A791CBA16ABEE0A727029DF",
    "Ativo":"true"
 }
 ```
Caso o ID exista, será realizar a atualização dos dodos, utilizando os dados informados no json.

Obs.: Não é necessário enviar o arquivo json completo, somente o campo que foi editado, isso vale para todos os métodos post da api. 

**SEMPRE INFORME O VALOR DO CAMPO Ativo POIS CASO NÃO SEJA INFORMADO, O REGISTRO SERÁ INATIVADO**

## Delete
> Link Base: http://localhost:5000/api/Usuario/EB078EB1ABFC9722EBAB9BA3DD303AFD37DD1AE50A791CBA16ABEE0A727029DF?id=1
Recebe o ID da sessão e o ID do usuário

Obs.: Nenhum registro será apagado, o delete somente inativa o registro com id informado
