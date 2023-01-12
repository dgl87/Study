# Protocolo HTTP
- Protocolo base para a comunicação de dados na internet, permitindo obter recursos como páginas HTML.
- Quando desenvolvemos uma API, permitindo o acesso aos seus recursos geralmente através do protocolo HTTP.
Os cabeçalhos de requisição HTTP, tanto de envio quanto de respostas, permitem o envio de informações extras.
Os cabeçalhos mais comuns são:
- Content-Length
- Content-Type
- Cache-Control
- Status

Os Status HTTP são códigos de respostas relacionados ao status de conclusão de uma requisição. Os status mais comuns são:
```diff
+ 200 ok	
+ 201 Created
+ 202 Accepted
+ 204 No Content
  400 Bad Request
  401 Unauthorized
  404 Not Found
- 500 Internal Server Error
```
Os métodos (verbos) HTTP representam ações para um dado recurso, mais comuns são:
- **GET :** uma consulta, ou ação que não altera o estado do sistema, geralmente retorna status 200.
- **POST :** criação de recursos, retornando status 201 ou 400.
- **PUT :** atualização de recurso, retornando status 204, 400 ou 404.
- **DELETE :** remoção de um recurso, retornando 204 ou 404

## O que é uma API REST?
- **REST**, abreviatura de **Representational State Transfer**, é um padrão de comunicação entre sistemas, geralmente utilizado junto ao protocolo HTTP.
- Baseado em recursos, que são representados em um caminho de acesso em uma URL.

Exemplos de URL’s seguindo o padrão REST para acesso de recursos:
- api/users/1 - GET, PUT, DELETE
- api/projects - GET, POST
- api/skills - GET, POST
- api/users/1/projects - GET, POST

## Introdução ao ASP.NET CORE
- Framework open-source, multiplataforma, leve, e de alta performance para a construção de aplicações Web modernas, prontas para a nuvem.
- Lançado em junho de 2016, e desde então tem se tornado a principal opção para desenvolvimento de projetos na plataforma .NET.
- Contém um sistema de configuração baseado em ambientes, facilitando o seu uso em plataforma de nuvem.
- Possui recurso de injeção de dependência nativo, sem a necessidade de se utilizar bibliotecas como o Ninject.

## Criando o Projeto DevFreela

### No terminal
lista as opções de template
```shell
dotnet new 
```

cria solution
```shell
dotnet new sln --name DevFreela
```

cria projeto
```shell
 dotnet new webapi --name DevFreela.API --output DevFreela.API
```

adicionar a referência do projeto para a solution
```shell
dotnet sln add ./DevFreela.API/DevFreela.API.csproj 
```

build’a projeto
```shell
dotnet build
```

restaura pacotes
```shell
dotnet restore
```

executa aplicação
```shell
dotnet run
```

## Controllers e Actions
Controllers
São componentes que definem e agrupam um conjunto de ações (Actions)
Agrupam ações de maneira lógica, baseado no recurso a ser tratado e/ou acessado
Um Controller em uma API ASP.NET Core é uma classe que herda de ControllerBase
Uma informação importante para uma Controller é a rota base para ela, já que isso influenciará na rota de todos pontos de acesso desse Controller. Ela pode ser definida pela anotação Route
Actions
São métodos contidos em uma classe Controller, e representam pontos de acesso em uma API
Para API’s, o tipo de retorno de uma Action geralmente é IActionResult, que é uma interface implementada pelas respostas padrão do HTTP, como o Ok, Not Found, entre outras.

Utilizando o Swagger
Ferramenta que simplifica o desenvolvimento de API’s, permitindo, entre outras funcionalidades, a documentar e testar API’s
Ele consegue gerar uma interface gráfica contendo todos os pontos de acesso (endpoints) da API, permitindo realizar requisições diretamente em sua interface

Aprofundando em Actions
O ASP.NET Core é bem flexível a respeito de definição de Actions e Rotas, além de recepção de parâmetros. Para uma requisição GET, por exemplo, geralmente você vai receber:
Parâmetro de URL
Query String
Já no caso de requisição POST e PUT, geralmente vão ser recebidos parâmetros através de:
Parâmetro de URL
Corpo da requisição
Finalmente, as requisições DELETE geralmente apenas recebem parâmetros de URL

 
 
 
using DevFreela.API.Models;
using Microsoft.AspNetCore.Mvc;
 
namespace DevFreela.API.Controllers
{
   [Route("api/projects")]
   public class ProjectsController : ControllerBase
   {
       //api/projects?query=net core
       [HttpGet]
       public IActionResult Get(string query)
       {
           //Busca todos ou filtrar
           return Ok();
       }
       //api/projects/{id}
       [HttpGet("{id}")]
       public IActionResult GetById(int id)
       {
           //Busca o projeto
           //return NotFound();
           return Ok();
       }
       //api/projects
       [HttpPost]
       public IActionResult Post([FromBody] CreateProjectModel createProjectModel)
       {
           if (createProjectModel.Title.Length < 9)
           {
               return BadRequest();
           }
           return CreatedAtAction(nameof(GetById), new { id = createProjectModel.Id }, createProjectModel);
       }
       //api/projects/{id}
       [HttpPut("{id}")]
       public IActionResult Put(int id, [FromBody] UpdateProjectModel updateProjectModel)
       {
           if (updateProjectModel.Description.Length < 10)
           {
               return BadRequest();
           }
           //Atualizo o objeto
           return NoContent();
       }
       //api/projects/{id}
       [HttpDelete("{id}")]
       public IActionResult Delete(int id)
       {
           //return NotFound() se não existir
           //Remove
           return NoContent();
       }
   }
}

Models
namespace DevFreela.API.Models
{
   public class CreateProjectModel
   {
       public int Id { get; set; }
       public string Title { get; set; }
       public string Description { get; set; }
   }
}
namespace DevFreela.API.Models
{
   public class UpdateProjectModel
   {
       public string Description { get; set; }
   }
}
 
Configurações
O ASP.NET Core permite carregar configurações de aplicação de uma série de fontes, através da interface IConfiguration, como:
Arquivos de configuração, como o appsettings.json;
Variáveis de Ambiente;
Argumentos de linha de comando;
Da nuvem Azure, como configurações de Aplicativo, e pelo Azure Key Vault.

No caso de arquivo de configuração, é possível carregar diferentes versões dele, dependendo do ambiente que é definido pela variável de ambiente ASPNETCORE_ENVIRONMENT, por exemplo:
appsettings.Development.json;
appsettings.Staging.json;
appsettings.Production.json

Uma ótima maneira de obter configurações é usar o padrão Option. 
Já no caso de linha de comando, basta passar como simples parâmetros, como em dotnet run nome=Luis e utilizar o código Configuration.GetValue,T.(“nome_parametro”)

Injeção de Dependência
Técnica que permite a inserção de um objeto em outro, geralmente através do construtor, criando a relação de dependência.
O método que utilizamos para adicionar configurações para o Controller é um exemplo de injeção de Dependência.
Técnica muito utilizada para melhorar a qualidade de código em refatoração, e torná-lo testável.
Singleton: uma instância por aplicação
Scoped: uma instância por requisição
Transient: uma instância por classe
