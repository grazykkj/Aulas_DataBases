# Neo4j com Docker Compose

Este material mostra como subir o Neo4j com Docker Compose, acessar o banco pelo navegador ou pelo `cypher-shell` e iniciar a base com 100 pessoas já cadastradas.

## O que está incluído

- Neo4j 5 (última versão estável)
- Plugins:
  - [APOC](https://neo4j.com/developer/neo4j-apoc/)
  - [n10s](https://neo4j.com/labs/neosemantics/) (RDF, Linked Data)
- Volumes persistentes: `/data`, `/logs`, `/plugins`
- Configurações de memória e page cache
- Acesso via navegador e Bolt protocol
- Script de carga inicial: `init-data.cypher`
- 100 nós `Pessoa` criados automaticamente
- Nós relacionados de `Cidade` e `Area`
- Relacionamentos:
  - `(:Pessoa)-[:MORA_EM]->(:Cidade)`
  - `(:Pessoa)-[:ATUA_EM]->(:Area)`
  - `(:Pessoa)-[:CONHECE]->(:Pessoa)`
  - `(:Pessoa)-[:MENTORA]->(:Pessoa)`

## Como subir o ambiente

No diretório `Helps/Neo4j`, execute:

```bash
docker-compose up -d
```

Para verificar se os containers estão rodando:

```bash
docker-compose ps
docker-compose logs -f
```

O serviço `neo4j-init` espera o Neo4j ficar pronto e executa o arquivo `init-data.cypher`. Quando a carga terminar, esse container ficará com status `Exited (0)`.

Para parar o ambiente:

```bash
docker-compose down
```

Para parar e remover também os volumes de dados:

```bash
docker-compose down -v
```

## Como acessar o Neo4j

- 🔗 Acesse: [http://localhost:7474](http://localhost:7474)
- 👤 Usuário: `neo4j`
- 🔒 Senha: `senha123`

Também é possível acessar pelo terminal:

```bash
docker exec -it neo4j cypher-shell -u neo4j -p senha123
```

Ou, se você tiver o `cypher-shell` instalado na sua máquina:

```bash
cypher-shell -a bolt://localhost:7687 -u neo4j -p senha123
```

## Conferindo os dados iniciais

Depois de acessar o Neo4j Browser ou o `cypher-shell`, rode:

```cypher
MATCH (p:Pessoa)
RETURN count(p) AS total_pessoas;
```

Para ver alguns registros:

```cypher
MATCH (p:Pessoa)-[:MORA_EM]->(c:Cidade),
      (p)-[:ATUA_EM]->(a:Area)
RETURN p.id, p.nome, p.idade, p.email, p.profissao, c.nome AS cidade, a.nome AS area
ORDER BY p.id
LIMIT 20;
```

Para visualizar a rede de relacionamentos:

```cypher
MATCH caminho = (p:Pessoa)-[r]->(n)
RETURN caminho
LIMIT 50;
```

## Comandos úteis

```bash
docker-compose up -d       # Inicia o container em segundo plano
docker-compose down        # Encerra e remove o container
docker-compose logs -f     # Acompanha os logs em tempo real
docker-compose restart     # Reinicia o serviço
```

## Volumes

- `neo4j_data`: Armazena os dados do banco
- `neo4j_logs`: Logs do servidor
- `neo4j_plugins`: Plugins adicionais (como APOC)

Esses volumes garantem persistência mesmo após reinicializações.

## Teste rápido com Cypher

Após acessar o Neo4j Browser:

```cypher
CREATE (a:Person {name: 'Ana'})-[:FRIENDS_WITH]->(b:Person {name: 'Bruno'})
RETURN a, b
```

## Recursos úteis

- Documentação oficial: [neo4j.com/docs](https://neo4j.com/docs/)
- Cypher tutorial: [Neo4j Cypher Manual](https://neo4j.com/docs/cypher-manual/)
- APOC functions: [APOC Reference](https://neo4j.com/labs/apoc/)

## Requisitos

- Docker
- Docker Compose
- Acesso à internet (para baixar a imagem)

## Notas

- Para produção, configure autenticação segura e backup dos volumes.
- Para importar/exportar arquivos, monte volumes adicionais apontando para o diretório desejado do host.
- Se você já tinha subido o Neo4j antes e quer recarregar os dados do zero, execute `docker-compose down -v` e depois `docker-compose up -d`.

Material preparado para uso local em aula e testes.
