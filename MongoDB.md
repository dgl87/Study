# MONGODB

https://www.mongodb.com/ 
Instalar - MongoDb Community Server

## Preparando MongoDB
1. No caminho de diretório C:\Program Files\MongoDB\Server\5.0\bin
Para iniciar o servidor do mongo, digite o comando:
```console
C:\Program Files\MongoDB\Server\5.0\bin> mongod.exe
```

2. Crie o caminho de diretório: C:\data\db para que o Mongo funcione

3. Em outro terminal, acesse C:\Program Files\MongoDB\Server\5.0\bin> e digite: 
```console
mongo.exe 
```

**Agora você está dentro do ambiente do Mongo**

## Configura Path
![image](https://user-images.githubusercontent.com/58392536/212484755-ab8b1cdf-4303-434e-9ba2-98fa42711ef6.png)




# Comandos do Mongo
* **mongod** O Servidor... o mongo em si, o Banco de Dados. A última letra "d" significa Daemon
* **mongo** O cliente


Lista opções
```shell 
> db.    // + tecla TAB, mostra opções
``` 

Mostra Databases
```console
> show dbs
```

Acessa Collection
```console
> use meubanco
```

Lista objetos da collection
```console
> db.minhacollection.find() 
```

Mostra primeiro objeto da collection
```console
> db.minhacollection.findOne() 
```

Insere item na collection. Retorno de inserção "nInserted"
```mongo
> db.minhaCollection.insert({"valor": 123})
WriteResult({ "nInserted" : 1 })
```





```console
db.minhacollection.insertOne({name: "Maria José"}) 
```


db.minhaCollection.insert({"valor": 123})





No PowerShell
mongo --help

db. + tecla tab, mostra opções

## Analogias com SQL

MongoDB | SQL
:--------:|:------------:
Database (Banco de Dados) | Database (Banco de Dados)
Collections (Coleção) | Table (Tabelas)
Document (Documento) | Row (Linha / Registros)

![image](https://user-images.githubusercontent.com/58392536/212488367-5c8787da-71fa-4580-a7c5-d614c088eb21.png)

## BSON
Binary JSON  

O ObjectId é composto por 12 bytes, sendo distribuídos da seguinte maneira:

Primeiros 4 bytes: timestamp do momento de inserção do documento.

Próximos 5 bytes: valor aleatório

Últimos 3 bytes: Contador incremental
---------------
O ObjectId é composto por 12 bytes, sendo distribuídos da seguinte maneira:

Primeiros 4 bytes: timestamp do momento de inserção do documento.

Próximos 5 bytes: valor aleatório

Últimos 3 bytes: Contador incremental


# CRUD
https://www.mongodb.com/docs/manual/crud/

## C - CREATED
![image](https://user-images.githubusercontent.com/58392536/212552345-03307971-200d-47bc-ab25-5d49e561a032.png)

Descontinuado
```
- insert({})
  db.crud.insert({a: 123})
```
Insere um objeto
```  
- insertOne({})
  db.crud.insertOne({a: 123})
```
Insere vários objetos
```
- insertMany({}, {}, {})
  db.crud.insertMany([{a: 123},{b: 456}, {c: 789}])
```
ordered: true = padrão. Insere SE nenhum elemento da lista der erro
```  
- db.testInsertMany.insertMany([{_id: 2}, {_id: 7, msg: 'test'}], {ordered: true}) //Insere a lista se não houver erro em nenhum objeto
```
ordered: false = Insere elementos, exceto o objeto com erro
```
- db.crud.insertMany([{a: 123},{b: 456}, {c: 789}], {ordered: false}) //Insere lista exceto o que deu erro
```
## R - READ
![image](https://user-images.githubusercontent.com/58392536/212552414-1841fee7-cddb-48c3-922e-5d72e111f222.png)

```
find({})
```
```
findOne({})
```
```
find({}).pretty()
```
Encontra elementos com valor b: 456
```
db.crud.find({ b: 456})
```
Encontra objetos que possuem c
```
db.crud.find({ c: {$exists: true}})
```
Encontra objetos que não possuem c
```
db.crud.find({ c: {$exists: false}})
```
``` Traz somente campos nome e materias. SQL = SELECT nome, materias FROM alunos
db.alunos.findOne({}, {_id: 0, nome: 1, materias: 1})
ou
db.alunos.findOne({}, {_id: false, nome: true, materias: true})
```
## U - UPDATE
![image](https://user-images.githubusercontent.com/58392536/212552452-d05005d7-29c4-49bc-951a-f675b8236a65.png)

Altera primeiro documento com b: 456 com d: 555
```
- db.crud.updateOne({b: 456}, { $set: {d: 555}})
```
Altera todos documentos com a: 123
```
db.crud.updateMany({a: 123}, { $set: {h: 20}})
```
Reescreve todo documentos
```
db.crud.replaceOne({c: 789}, { outro: "documento"})
```
## D - DELETE
![image](https://user-images.githubusercontent.com/58392536/212552490-f44006d0-1706-4f8d-a1d4-3dcb58ba1a66.png)

Apaga primeiro documento com a: 123
```
db.crud.deleteOne({a: 123})
```
Apaga todos com b: 456
```
db.crud.deleteMany({b: 456})
```

# RELACIONAMENTOS
MER | DER
:------:|:-------:
M - Modelo | D - Diagrama
E - Entidade |E - Entidade
R - Relacionamento | R - Relacionamento

## 1:1
![image](https://user-images.githubusercontent.com/58392536/212570507-94e84114-896f-4249-8e3f-18c49bbce705.png)

- Referência
```
{								
	"_id": 1,
	"cpf": "12345678",
	"nome": "João",	
	"cidade": "São Paulo",
	"carteirinha_id": 8	
}
{
	"_id": 8,
	"turma": "220A",
	"ra": 1245
}
```
- Embedded document
```
{
	"_id": 1,
	"cpf": "12345678",
	"nome": "João",
	"cidade": "São Paulo",
	"carteirinha": {
		"_id": 8,
		"turma": "220A",
		"ra": 1245
	}
}
```

# 1 : N
![image](https://user-images.githubusercontent.com/58392536/212571761-f22701e6-aa1a-423a-98ad-a02a48fe2aa1.png)

- Embedded document
```
{
    "_id": 3,
    "meta": 4000,
    "nome": "Vendas",
    "funcionarios": [
        {
            "_id": 8,
            "salario": 1400,
            "turno": "tarde",
            "cargo": "vendedor"
        },
        {
            "_id": 12,
            "salario": 1600,
            "turno": "noite",
            "cargo": "vendedor"
        }
    ]
}
```
