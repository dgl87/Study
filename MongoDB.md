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

## C
```
- insert({})
  db.crud.insert({a: 123})
  
- insertOne({})
  db.crud.insertOne({a: 123})
  
- insertMany({}, {}, {})
  db.crud.insertMany([{a: 123},{b: 456}, {c: 789}])
  
  db.crud.insertMany([{a: 123},{b: 456}, {c: 789}], {ordered: false}) //Insere mesmo se der erro
```
## R
```
- find({})
- findOne({})
- find({}).pretty()
```
## U
```
- update
- updateOne
- updateMany
```
## D
```
- deleteOne
- deleteMany
```

