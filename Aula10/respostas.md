# Exercício Prático: Modelagem e Consultas em MongoDB

## Parte 1: Modelagem de Dados

### 1. Incorporação (Embedding)

```javascript
db.cursos.insertOne({
  titulo: "Banco de Dados NoSQL com MongoDB",
  descricao: "Curso introdutório sobre modelagem e consultas em MongoDB.",
  carga_horaria_total: 40,
  nivel: "Iniciante",
  modulos: [
    {
      nome: "Introdução ao NoSQL",
      carga_horaria: 8
    },
    {
      nome: "Modelagem de Documentos",
      carga_horaria: 12
    },
    {
      nome: "Consultas com MongoDB",
      carga_horaria: 10
    },
    {
      nome: "Aggregation Framework",
      carga_horaria: 10
    }
  ]
})
```

Neste caso, incorporar os módulos dentro do documento do curso é melhor para leitura porque normalmente, ao buscar um curso, também queremos visualizar seus módulos. Assim, o MongoDB consegue retornar todas as informações em uma única consulta, sem precisar fazer buscas extras em outra coleção.

### 2. Referência (Reference)

Documento do instrutor:

```javascript
db.instrutores.insertOne({
  _id: ObjectId("664000000000000000000001"),
  nome: "Ana Souza",
  email: "ana.souza@email.com",
  especialidade: "Banco de Dados"
})
```

Documento do curso:

```javascript
db.cursos.insertOne({
  titulo: "MongoDB para Iniciantes",
  carga_horaria_total: 30,
  nivel: "Iniciante",
  instrutor_id: ObjectId("664000000000000000000001")
})
```

É preferível referenciar o instrutor quando ele pode ministrar vários cursos ou quando seus dados podem mudar com frequência. Dessa forma, se o email, nome ou especialidade do instrutor forem alterados, a atualização precisa ser feita apenas no documento do instrutor, evitando dados repetidos e inconsistentes em vários cursos.

---

## Parte 2: Consultas e Filtros

### 1. Filtro de Existência

```javascript
db.clientes.find({
  email: { $exists: true }
})
```

### 2. Filtro de Comparação

```javascript
db.clientes.find({
  idade: { $gte: 21 }
})
```

### 3. Filtro Complexo

```javascript
db.clientes.find({
  cidade: "São Paulo",
  idade: { $lt: 30 }
})
```

Também poderia ser escrito usando `$and`:

```javascript
db.clientes.find({
  $and: [
    { cidade: "São Paulo" },
    { idade: { $lt: 30 } }
  ]
})
```

---

## Parte 3: Aggregation Framework (Relatórios)

```javascript
db.vendas.aggregate([
  {
    $match: {
      status: "concluída"
    }
  },
  {
    $group: {
      _id: "$categoria",
      total_quantidade_vendida: {
        $sum: "$quantidade"
      }
    }
  }
])
```

Esse pipeline primeiro filtra apenas as vendas concluídas e depois agrupa os documentos por categoria, somando a quantidade vendida em cada uma delas.
