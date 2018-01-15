# SystemsWayAPI
Documentação para a API SystemsWay
# Introdução
	A API da Systems Way foi criado com o intuito de facilitar a integração do WAY com demais aplicações e permitir que um mesmo sistema responda a solicitações das diversas plataformas que serão utilizadas pelo WAY.
## Detalhes técnicos
  > * É implementado utilizando .Net Core 2.0.
  > * Utiliza protocolo HTTP,
  > * Basicamente é uma WEB API RESTFul, respondendo ao métodos GET, POST, PUT e DELETE com suas features específicas, atendendo às necessidades da SystemWay.
  > * Todos os dados de login (senha) são salvos e trafegados utilzando o Algoritmo SHA256 com 64 caracteres. ([Mais detalhes sobre SHA256](http://www.iwar.org.uk/comsec/resources/cipher/sha256-384-512.pdf))
  > * O caminho padrão da API é definido como sendo http://IP:5000/api/function, onde IP é o endereço de ip onde o mesmo está hospedado, e function é a função/módulo que está sendo requisitado.
  
**Obs.: Para os exemplos desse tutorial, assuma que a API está ospedada em localhost**

#Considerações iniciais
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
  > * Um usuário pode receber ERROR ao tentar realizar um login por um dos motinos: usuário ou senha inválidos, usuário inativo, empresa do usuário inativa.
