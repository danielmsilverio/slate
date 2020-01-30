---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  -

search: true
---

# API de Admissões do Quero Alunos

Bem-vindo à documentação da API de Admissões do Quero Alunos. Aqui você encontrará guias e referências sobre como utilizar nossa API. Qualquer dúvida, entre em contato pelo email [api-admissoes@quero.education](mailto:api-admissoes@quero.education).

## Informações básicas

Todas as respostas da API são feitas em JSON

Endpoint de produção: `https://queroalunos.com/api/v1`
Endpoint de homologação: `https://queroalunos-admission.querobolsa.space/api/v1`

# Autenticação

Todas as requisições são autenticadas por um token adicionado ao header.
Caso não tenha o token, solicite-o ao setor de desenvolvimento da Quero Educação.

## Requisição sem token
```
Ausência de Token
Status Code: 401 Unauthorized
www-authenticate: 'Token realm="University Panel"'
```

Em caso de requisição sem o envio do token será retornado status `401 - Unauthorized`.

## Requisição com token inválido

```
Formato de token inválido
Status Code: 400 Bad Request
```

Em caso de token em formato inválido será retornado status `400 - Bad Request`.

## Requisição com token não autorizado

```
Não autorizado
Status Code: 403 Forbidden
```

Em caso de token não autorizado será retornado erro `403 - Forbidden`.

## Requisição com token sem permissão

```
Sem permissão
Status Code: 200 OK
```
```json
{
  "error": true,
  "errors": [{
    "title": "Invalid Permission",
    "detail": "You are not authorized to access this resource with these parameters"
  }]
}
```

Em caso de token válido mas sem permissão de acesso ao recurso específico

# Paginação

## Exemplo de paginação

> Exemplo de requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions
```

> Exemplo de retorno

```json
{
  "has_more": true,
  "items": [
    {
      "id": 12345,
      "course": {
        "id": "ADM-MANHA-SP",
        "offer": {
          "discount": 50.0
        }
      },
      "status": "pending_docs",
      "student": { ... },
      "applications": [ {...} ],
      "created_at": "2016-10-01T02:01:44Z"
    },
    ...,
    {
      "id": 12369,
      "course": {
        "id": "ADM-MANHA-SP",
        "offer": {
          "discount": 50.0
        }
      },
      "status": "pre_registered",
      "student": { ... },
      "applications": [ {...} ],
      "created_at": "2016-10-15T03:15:44Z"
    }
  ]
}
```

A API utiliza paginação baseada em cursor atráves dos parâmetros `starting_after` e `ending_before`. Ambos recebem um id de um dado existente e retorna uma lista com até 25 elementos no máximo.
O parâmetro `ending_before` faz a requisição retornar elementos cujo id é maior que o indicado pelo parâmetro. Em contrapartida, o parâmetro `starting_after` faz a requisição retornar elementos listados após o dado cujo id foi indicado.
O atributo `has_more` da resposta indica se há mais dados disponíveis depois dessa página. Se for `false`, significa que é o fim da lista e não há mais dados. Se for `true`, significa que há mais dados a serem resgatados. Após uma requisição utilizando o parâmetro `ending_before`, o atributo `has_more` se refere a possibilidade de retornar mais dados anteriores à página atual.

### Parâmetros de paginação

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| starting_after | cursor | Cursor para uso em paginação. Retorna elementos listados após o dado cujo id foi indicado |
| ending_before | cursor | Cursor para uso em paginação. Retorna elementos listados antes do dado cujo id foi indicado |
| start_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados a partir da data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| end_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados até a data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |

### Informações de resultado de dados com paginação

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| has_more | boolean | Indica se há mais elementos disponíveis antes ou após essa página |
| items | array | Lista dos elementos retornados pela requisição |

## Uso do cursor starting_after

> Exemplo de requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions?starting_after=12369
```

> Retorno

```json
{
  "has_more": false,
  "items": [
    {
      "id": 12370,
      "course": {
        "id": "ADM-MANHA-SP",
        "offer": {
          "discount": 50.0
        }
      },
      "status": "pending_docs",
      "student": { ... },
      "applications": [ {...} ],
      "created_at": "2016-10-16T03:15:44Z"
    },
    ...,
    {
      "id": 12380,
      "course": {
        "id": "ADM-MANHA-SP",
        "offer": {
          "discount": 50.0
        }
      },
      "status": "pre_registered",
      "student": { ... },
      "applications": [ {...} ],
      "created_at": "2016-10-20T03:15:44Z"
    }
  ]
}
```

No exemplo acima, vimos que `has_more` retornou `true`, portanto existem mais dados a serem resgatados após essa página. Para poder consultá-los, na próxima requisição o parâmetro `starting_after` precisa ter o id do último elemento da lista `items`. A requisição e retorno ao lado são referentes ao exemplo [acima](#exemplo-de-paginacao).

### Parâmetros de paginação

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| starting_after | cursor | Cursor para uso em paginação. Retorna elementos listados após o dado cujo id foi indicado |
| ending_before | cursor | Cursor para uso em paginação. Retorna elementos listados antes do dado cujo id foi indicado |
| start_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados a partir da data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| end_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados até a data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |

### Informações de resultado de dados com paginação

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| has_more | boolean | Indica se há mais elementos disponíveis antes ou após essa página |
| items | array | Lista dos elementos retornados pela requisição |

## Uso do cursor ending_before

> Exemplo de requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions?ending_before=12369
```

> Retorno

```json
{
  "has_more": true,
  "items": [
    {
      "id": 12345,
      "course": {
        "id": "ADM-MANHA-SP",
        "offer": {
          "discount": 50.0
        }
      },
      "status": "pending_docs",
      "student": { ... },
      "applications": [ {...} ],
      "created_at": "2016-10-01T03:15:44Z"
    },
    ...,
    {
      "id": 12369,
      "course": {
        "id": "ADM-MANHA-SP",
        "offer": {
          "discount": 50.0
        }
      },
      "status": "pre_registered",
      "student": { ... },
      "applications": [ {...} ],
      "created_at": "2016-10-15T03:15:44Z"
    }
  ]
}
```

Assim como avançamos uma página, é possível facilmente retornar a dados anteriores à página atual. Para fazer isso, na próxima requisição o parâmetro `ending_before` precisa ter o id do primeiro elemento da lista `items`. A requisição e retorno ao lado são referentes ao exemplo [acima](#uso-do-cursor-starting_after).

### Parâmetros de paginação

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| starting_after | cursor | Cursor para uso em paginação. Retorna elementos listados após o dado cujo id foi indicado |
| ending_before | cursor | Cursor para uso em paginação. Retorna elementos listados antes do dado cujo id foi indicado |
| start_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados a partir da data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| end_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados até a data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |

### Informações de resultado de dados com paginação

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| has_more | boolean | Indica se há mais elementos disponíveis antes ou após essa página |
| items | array | Lista dos elementos retornados pela requisição |

## Uso do filtro start_date

> Exemplo de requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions?start_date=15/10/2016
```

> Retorno

```json
{
  "has_more": false,
  "items": [
    {
      "id": 12369,
      "course": {
        "id": "ADM-MANHA-SP",
        "offer": {
          "discount": 50.0
        }
      },
      "status": "pre_registered",
      "student": { ... },
      "applications": [ {...} ],
      "created_at": "2016-10-15T03:15:44Z"
    },
    ...,
    {
      "id": 12380,
      "course": {
        "id": "ADM-MANHA-SP",
        "offer": {
          "discount": 50.0
        }
      },
      "status": "pre_registered",
      "student": { ... },
      "applications": [ {...} ],
      "created_at": "2016-10-20T03:15:44Z"
    }
  ]
}
```

É possível listar os elementos baseado na data de criação. Usando o parâmetro `start_date`, apenas os elementos criados após esta data serão listados. O parâmetro deve estar no formato `dd/mm/aaaa`. Esse filtro poderá ser verificado pelo atributo `created_at` da lista `items`, que segue o formato [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601).

### Parâmetros de paginação

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| starting_after | cursor | Cursor para uso em paginação. Retorna elementos listados após o dado cujo id foi indicado |
| ending_before | cursor | Cursor para uso em paginação. Retorna elementos listados antes do dado cujo id foi indicado |
| start_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados a partir da data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| end_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados até a data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |

### Informações de resultado de dados com paginação

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| has_more | boolean | Indica se há mais elementos disponíveis antes ou após essa página |
| items | array | Lista dos elementos retornados pela requisição |

## Uso do filtro end_date

> Exemplo de requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions?end_date=15/10/2016
```

> Retorno

```json
{
  "has_more": false,
  "items": [
    {
      "id": 12345,
      "course": {
        "id": "ADM-MANHA-SP",
        "offer": {
          "discount": 50.0
        }
      },
      "status": "pending_docs",
      "student": { ... },
      "applications": [ {...} ],
      "created_at": "2016-10-01T03:15:44Z"
    },
    ...,
    {
      "id": 12369,
      "course": {
        "id": "ADM-MANHA-SP",
        "offer": {
          "discount": 50.0
        }
      },
      "status": "pre_registered",
      "student": { ... },
      "applications": [ {...} ],
      "created_at": "2016-10-15T03:15:44Z"
    }
  ]
}
```

É possível listar os elementos baseado na data de criação. Usando o parâmetro `end_date`, apenas os elementos criados antes dessa data serão listados. O parâmetro deve estar no formato `dd/mm/aaaa`. Esse filtro poderá ser verificado pelo atributo `created_at` da lista `items`, que segue o formato [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601).

### Parâmetros de paginação

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| starting_after | cursor | Cursor para uso em paginação. Retorna elementos listados após o dado cujo id foi indicado |
| ending_before | cursor | Cursor para uso em paginação. Retorna elementos listados antes do dado cujo id foi indicado |
| start_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados a partir da data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| end_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados até a data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |

### Informações de resultado de dados com paginação

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| has_more | boolean | Indica se há mais elementos disponíveis antes ou após essa página |
| items | array | Lista dos elementos retornados pela requisição |

# Possíveis estados de admissão
## Estados

| Status | Descrição |
| ---- | --------- |
| initiated | Inscrição para exame pendente |
| enrolled | Matriculado |#### Status de interrupção
| | |
| dropped_out | Desistente do lado do Quero Bolsa |
| dropping_out | Desistindo do lado do Quero Bolsa |
| drop_out_confirmed | Tentativa de desistência do Quero Bolsa confirmada pela faculdade |

# Informações de alunos

## Busca de alunos por CPF

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/students?cpf=111.222.333-44
```

> Retorno

```json
[
  {
    "id": 123456,
    "name": "José da Silva",
    "cpf": "111.222.333-44",
    "birth_date": "1991-01-01",
    "emails": [
      "teste@exemplo.com"
    ],
    "phones": [
      "(11) 98888-7777"
    ],
    "address_information": {
      "address": "Rua Sandra",
      "number": "432S",
      "complement": "Apto. 201",
      "neighborhood": "Chácara Dora",
      "city": "Araçariguama",
      "state": "SP",
      "postal_code": "18147-000"
    },
    "enem": {
      "year": 2018,
      "scores": {
        "essay": 200.0,
        "math": 200.1,
        "language": 200.2,
        "nature": 200.4,
        "social": 200.5
      }
    },
    "admissions": [
      {
        "id": 12345,
        "course": {
          "id": "ADM-MANHA-SP",
          "offer": {
            "discount": 50.0
          }
        },
        "status": "pending_docs",
        "created_at": "2016-10-01T03:15:44Z",
        "extra_data": {
          "external_enrollment_id": "NPW231"
        }
      }
    ]
  }
]
```

> Resposta quando não encontra nenhum aluno

```json
{
  "error": true,
  "message": "CPF not found",
  "description": "Could not find any student by given CPF. Make sure you are following the pattern: xxx.xxx.xxx-xx"
}
```

Encontra um ou mais alunos a partir de um dado CPF.

Somente busca por alunos que tenham pré-matrícula na faculdade pertencente ao usuário fazendo pesquisa.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| cpf | Query | CPF do aluno procurado. Exige CPF completamente formatado (ex: 123.456.789-10) |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | Id do aluno |
| name | string | Nome do aluno |
| cpf | string | CPF do aluno |
| birth_date | string | Data de nascimento do aluno no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| emails | array de string | Lista de emails do aluno |
| phones | array de string | Lista de telefones do aluno |
| address_information | object | Objeto com dados onde aluno reside |
| [address_information] address | string | Endereço onde aluno reside |
| [address_information] number | string | Número onde aluno reside |
| [address_information] neighborhood | string | Bairro onde aluno reside |
| [address_information] city | string | Cidade onde aluno reside |
| [address_information] state | string | Estado onde aluno reside |
| [address_information] postal_code | string | Código postal onde aluno reside |
| enem | object | Objeto com dados do ENEM |
| [enem] year | id | number | Ano em que foi realizada a prova do ENEM |
| [enem] scores | object | Objetos com dados de notas do ENEM |
| [enem] [scores] essay | float | Nota de Redação do ENEM |
| [enem] [scores] math | float | Nota de Matemática do ENEM |
| [enem] [scores] language | float | Nota de Linguagens do ENEM |
| [enem] [scores] nature | float | Nota de Ciências da Natureza do ENEM |
| [enem] [scores] social | float | Nota de Ciências Humanas do ENEM |
| admissions | array | Lista de objetos com informações de processo de matrícula |
| [admissions] id | number | Id do processo de matrícula |
| [admissions] course | object | Objeto com dados do curso referente a essa matrícula |
| [course] id | string | Código do curso fornecido pela universidade referente a essa matrícula |
| [course] offer | object | Objeto com dados da oferta do curso |
| [offer] discount | float | Desconto do curso que o aluno adquiriu |
| [admissions] status | string | Status que se encontra o processo de matrícula |
| [admissions] created_at | string | Data da criação do processo de matrícula no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [admissions] [extra_data] external_enrollment_id | string | Identificador de matrícula enviado pela faculdade |

## Busca de aluno por id

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/students/{id}
```

> Resposta

```json
{
  "id": 394932,
  "name": "José da Silva",
  "cpf": "111.222.333-44",
  "birth_date": "1991-01-01",
  "emails": [
    "teste@exemplo.com"
  ],
  "phones": [
    "(11) 98888-7777"
  ],
  "address_information": {
    "address": "Rua Sandra",
    "number": "432S",
    "complement": "Apto. 201",
    "neighborhood": "Chácara Dora",
    "city": "Araçariguama",
    "state": "SP",
    "postal_code": "18147-000"
  },
  "enem": {
    "year": 2018,
    "scores": {
      "essay": 200.0,
      "math": 200.1,
      "language": 200.2,
      "nature": 200.4,
      "social": 200.5
    }
  },
  "admissions": [
    {
      "id": 12345,
      "course": {
        "id": "ADM-MANHA-SP",
        "offer": {
          "discount": 50.0
        }
      },
      "status": "pending_docs",
      "created_at": "2016-10-01T03:15:44Z",
      "extra_data": {
        "external_enrollment_id": "NPW231"
      }
    }
  ]
}
```
> Resposta quando não encontra nenhum aluno

```json
{
  "error": true,
  "message": "id not found",
  "description": "Could not find any student by given id."
}
```

Somente busca por alunos que tenham pré-matrícula na faculdade pertencente ao usuário fazendo pesquisa.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | Path | Id do aluno procurado |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | Id do aluno |
| name | string | Nome do aluno |
| cpf | string | CPF do aluno |
| birth_date | string | Data de nascimento do aluno |
| emails | array de string | Lista de emails do aluno |
| phones | array de string | Lista de telefones do aluno |
| address_information | object | Objeto com dados onde aluno reside |
| [address_information] address | string | Endereço onde aluno reside |
| [address_information] number | string | Número onde aluno reside |
| [address_information] neighborhood | string | Bairro onde aluno reside |
| [address_information] city | string | Cidade onde aluno reside |
| [address_information] state | string | Estado onde aluno reside |
| [address_information] postal_code | string | Código postal onde aluno reside |
| enem | object | Objeto com dados do ENEM |
| [enem] year | id | number | Ano em que foi realizada a prova do ENEM |
| [enem] scores | object | Objetos com dados de notas do ENEM |
| [enem] [scores] essay | float | Nota de Redação do ENEM |
| [enem] [scores] math | float | Nota de Matemática do ENEM |
| [enem] [scores] language | float | Nota de Linguagens do ENEM |
| [enem] [scores] nature | float | Nota de Ciências da Natureza do ENEM |
| [enem] [scores] social | float | Nota de Ciências Humanas do ENEM |
| admissions | array | Lista de objetos com informações de processo de matrícula |
| [admissions] id | number | Id do processo de matrícula |
| [admissions] course | object | Objeto com dados do curso referente a essa matrícula |
| [course] id | string | Código do curso fornecido pela universidade referente a essa matrícula |
| [course] offer | object | Objeto com dados da oferta do curso |
| [offer] discount | float | Desconto do curso que o aluno adquiriu |
| [admissions] status | string | Status que se encontra o processo de matrícula |
| [admissions] created_at | string | Data da criação do processo de matrícula no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [admissions] [extra_data] external_enrollment_id | string | Identificador de matrícula enviado pela faculdade |

# Notificações

Notificações utilizam uma rota única de callback, que deve ser fornecida pela faculdade, de um token para autenticação via HTTP Basic.

A rota deve aceitar JSON.

A rota deve aceitar apenas POSTs.

## Definição base do evento

Estrutura base das notificações:

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| created | string | Data que foi criado o evento no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| api_version | string | Informação da versão atual da API |
| event_type | string | Tipo de evento |
| data | object | Objeto com informações de acordo com o tipo de evento |

## Notificar novo processo de admissão

```json
{
  "event_type": "admission.created",
  "created": "2017-12-15T17:34:26.173",
  "api_version": "1.0.0",
  "data": {
    "admission": {
      "id": 12345,
      "course": {
        "id": "ADM-MANHA-SP",
        "offer": {
          "discount": 50.0
        }
      },
      "status": "initiated",
      "student": {
        "id": 394932,
        "name": "José da Silva",
        "cpf": "111.222.333-44",
        "birth_date": "1991-01-01",
        "emails": [
          "teste@exemplo.com"
        ],
        "phones": [
          "(11) 98888-7777"
        ],
        "enem": {
          "year": 2018,
          "scores": {
            "essay": 200.0,
            "math": 200.1,
            "language": 200.2,
            "nature": 200.4,
            "social": 200.5
          }
        },
        "address_information": {
          "address": "Rua Sandra",
          "number": "432S",
          "complement": "Apto. 201",
          "neighborhood": "Chácara Dora",
          "city": "Araçariguama",
          "state": "SP",
          "postal_code": "18147-000"
        }
      }
    }
  }
}
```

Esta notificação informa o início de um processo de admissão.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| created | string | Data que foi criado o evento no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| api_version | string | Informação da versão atual da API |
| event_type | string | Tipo de evento, no caso `admission.created` |
| data | object | Objeto com informações de acordo com o tipo de evento |
| admission | object | Objeto com dados do processo de admissão do aluno |
| [admission] id | number | Id do processo de admissão |
| [admission] course | object | Objeto com dados do curso referente a essa matrícula |
| [course] id | string | Código do curso fornecido pela universidade referente a essa matrícula |
| [course] offer | object | Objeto com dados da oferta do curso |
| [offer] discount | float | Porcentagem de desconto do curso que o aluno adquiriu |
| [admission] status | string | Status da admissão do aluno |
| [admission] student | object | Objeto com dados do aluno |

## Listagem de notificações

> Requisição

```bash
curl --header "Authorization: Basic ########" --header "Content-Type: application/json" https://queroalunos.com/api/{version}/notifications/search
```

> Resposta

```json
{
  "has_more": true,
  "notifications": [
    {...}
  ]
}
```

Lista eventos enviados

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| start_date     | string | Data inicial do filtro no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| end_date       | string | Data final do filtro no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| starting_after | string | Cursor para a próxima página
| ending_before  | string | Cursor para a página anterior

### Informações de retorno

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| has_more | boolean          | indica a existência de outras páginas |
| items    | array de objetos | lista de notificações: [referência](#definicao-base-do-evento)


## Notificar universidade sobre aluno pronto para matrícula

```json
{
  "event_type": "admission.enroll",
  "created": "2017-12-15T17:34:26.173",
  "api_version": "1.0.0",
  "data": {
    "admission": {
      "id": 12345,
      "course": {
        "id": "ADM-MANHA-SP",
        "offer": {
          "discount": 50.0
        }
      },
      "status": "submitted_docs",
      "student": {
        "id": 394932,
        "name": "José da Silva",
        "cpf": "111.222.333-44",
        "rg": "45.478.123-X",
        "birth_date": "1991-01-01",
        "emails": [
          "teste@exemplo.com"
        ],
        "phones": [
          "(11) 98888-7777"
        ],
        "enem": {
          "year": 2018,
          "scores": {
            "essay": 200.0,
            "math": 200.1,
            "language": 200.2,
            "nature": 200.4,
            "social": 200.5
          }
        },
        "address_information": {
          "address": "Rua Sandra",
          "number": "432S",
          "complement": "Apto. 201",
          "neighborhood": "Chácara Dora",
          "city": "Araçariguama",
          "state": "SP",
          "postal_code": "18147-000"
        }
      }
    }
  }
}
```

Essa notificação informa a universidade que há um aluno pronto para ser matrículado, após todos os documentos terem sido aprovados pela Quero. 


### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| created | string | Data que foi criado o evento no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| api_version | string | Informação da versão atual da API |
| event_type | string | Tipo de evento, no caso `admission.enroll` |
| data | object | Objeto com informações de acordo com o tipo de evento |
| admission | object | Objeto com dados do processo de admissão do aluno |
| [admission] id | number | Id do processo de admissão |
| [admission] course | object | Objeto com dados do curso referente a essa matrícula |
| [course] id | string | Código do curso fornecido pela universidade referente a essa matrícula |
| [course] offer | object | Objeto com dados da oferta do curso |
| [offer] discount | float | Porcentagem de desconto do curso que o aluno adquiriu |
| [admission] status | string | Status da admissão do aluno |
| [admission] student | object | Objeto com dados do aluno |
