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
          "neighborhood": "Chácara Dora",
          "city": "Araçariguama",
          "state": "SP",
          "postal_code": "18147-000"
        }
      },
      "applications": [
        {
          "id": 123456,
          "type": "exam",
          "exam": {
            "id":456,
            "course_skus": [
              "ADM-MANHA-SP",
              "DIR-MANHA-SP",
              "ADM-NOITE-RJ"
            ],
            "exam_location": {
              "address": "Rua Márcia",
              "number": "4231",
              "neighborhood": "Morro do Barreto",
              "city": "São Roque",
              "state": "SP",
              "postal_code": "19110-000"
            },
            "dates": "2016-11-01",
            "times": "18:30",
            "status": "active",
            "created_at": "2016-10-01T03:15:44Z"
          },
          "result": "approved",
          "created_at": "2016-10-01T03:15:44Z"
        }
      ],
      "created_at": "2018-12-05T13:01:38Z",
      "extra_data": {
        "external_enrollment_id": "NPW231"
      },
      "documents": [
        {
          "type": "titulo_de_eleitor",
          "id": 226,
          "created_at": "2018-12-19T14:36:37Z"
        },
        ...,
        {
          "type": "historico_escolar",
          "id": 225,
          "created_at": "2018-12-19T14:36:19Z"
        }
      ]
    }
  ]
}, ...
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
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions?starting_after=12343
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
          "neighborhood": "Chácara Dora",
          "city": "Araçariguama",
          "state": "SP",
          "postal_code": "18147-000"
        }
      },
      "applications": [
        {
          "id": 123456,
          "type": "exam",
          "exam": {
            "id":456,
            "course_skus": [
              "ADM-MANHA-SP",
              "DIR-MANHA-SP",
              "ADM-NOITE-RJ"
            ],
            "exam_location": {
              "address": "Rua Márcia",
              "number": "4231",
              "neighborhood": "Morro do Barreto",
              "city": "São Roque",
              "state": "SP",
              "postal_code": "19110-000"
            },
            "dates": "2016-11-01",
            "times": "18:30",
            "status": "active",
            "created_at": "2016-10-01T03:15:44Z"
          },
          "result": "approved",
          "created_at": "2016-10-01T03:15:44Z"
        }
      ],
      "created_at": "2018-12-05T13:01:38Z",
      "extra_data": {
        "external_enrollment_id": "NPW231"
      },
      "documents": [
        {
          "type": "titulo_de_eleitor",
          "id": 226,
          "created_at": "2018-12-19T14:36:37Z"
        },
        ...,
        {
          "type": "historico_escolar",
          "id": 225,
          "created_at": "2018-12-19T14:36:19Z"
        }
      ]
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
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions?ending_before=12346
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
          "neighborhood": "Chácara Dora",
          "city": "Araçariguama",
          "state": "SP",
          "postal_code": "18147-000"
        }
      },
      "applications": [
        {
          "id": 123456,
          "type": "exam",
          "exam": {
            "id":456,
            "course_skus": [
              "ADM-MANHA-SP",
              "DIR-MANHA-SP",
              "ADM-NOITE-RJ"
            ],
            "exam_location": {
              "address": "Rua Márcia",
              "number": "4231",
              "neighborhood": "Morro do Barreto",
              "city": "São Roque",
              "state": "SP",
              "postal_code": "19110-000"
            },
            "dates": "2016-11-01",
            "times": "18:30",
            "status": "active",
            "created_at": "2016-10-01T03:15:44Z"
          },
          "result": "approved",
          "created_at": "2016-10-01T03:15:44Z"
        }
      ],
      "created_at": "2018-12-05T13:01:38Z",
      "extra_data": {
        "external_enrollment_id": "NPW231"
      },
      "documents": [
        {
          "type": "titulo_de_eleitor",
          "id": 226,
          "created_at": "2018-12-19T14:36:37Z"
        },
        ...,
        {
          "type": "historico_escolar",
          "id": 225,
          "created_at": "2018-12-19T14:36:19Z"
        }
      ]
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
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions?start_date=05/12/2018
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
          "neighborhood": "Chácara Dora",
          "city": "Araçariguama",
          "state": "SP",
          "postal_code": "18147-000"
        }
      },
      "applications": [
        {
          "id": 123456,
          "type": "exam",
          "exam": {
            "id":456,
            "course_skus": [
              "ADM-MANHA-SP",
              "DIR-MANHA-SP",
              "ADM-NOITE-RJ"
            ],
            "exam_location": {
              "address": "Rua Márcia",
              "number": "4231",
              "neighborhood": "Morro do Barreto",
              "city": "São Roque",
              "state": "SP",
              "postal_code": "19110-000"
            },
            "dates": "2016-11-01",
            "times": "18:30",
            "status": "active",
            "created_at": "2016-10-01T03:15:44Z"
          },
          "result": "approved",
          "created_at": "2016-10-01T03:15:44Z"
        }
      ],
      "created_at": "2018-12-05T13:01:38Z",
      "extra_data": {
        "external_enrollment_id": "NPW231"
      },
      "documents": [
        {
          "type": "titulo_de_eleitor",
          "id": 226,
          "created_at": "2018-12-19T14:36:37Z"
        },
        ...,
        {
          "type": "historico_escolar",
          "id": 225,
          "created_at": "2018-12-19T14:36:19Z"
        }
      ]
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
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions?end_date=15/12/2018
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
          "neighborhood": "Chácara Dora",
          "city": "Araçariguama",
          "state": "SP",
          "postal_code": "18147-000"
        }
      },
      "applications": [
        {
          "id": 123456,
          "type": "exam",
          "exam": {
            "id":456,
            "course_skus": [
              "ADM-MANHA-SP",
              "DIR-MANHA-SP",
              "ADM-NOITE-RJ"
            ],
            "exam_location": {
              "address": "Rua Márcia",
              "number": "4231",
              "neighborhood": "Morro do Barreto",
              "city": "São Roque",
              "state": "SP",
              "postal_code": "19110-000"
            },
            "dates": "2016-11-01",
            "times": "18:30",
            "status": "active",
            "created_at": "2016-10-01T03:15:44Z"
          },
          "result": "approved",
          "created_at": "2016-10-01T03:15:44Z"
        }
      ],
      "created_at": "2018-12-05T13:01:38Z",
      "extra_data": {
        "external_enrollment_id": "NPW231"
      },
      "documents": [
        {
          "type": "titulo_de_eleitor",
          "id": 226,
          "created_at": "2018-12-19T14:36:37Z"
        },
        ...,
        {
          "type": "historico_escolar",
          "id": 225,
          "created_at": "2018-12-19T14:36:19Z"
        }
      ]
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
## Admissão

| Status | Descrição |
| ---- | --------- |
| initiated | Inscrição para exame pendente |
| pre_registered | Agendamento solicitado para exame vestibular |
| registered | Agendamento confirmado no exame vestibular |
| approved | Aprovado no exame vestibular |
| pending_docs | Documentação pendente |
| submitted_docs | Documentação completamente enviada |
| rejected_docs | Documentação rejeitada |
| awaiting_enrollment | Aguardando formação de turma |
| enrolled | Matriculado |#### Status de interrupção
| | |
| rejected_enrollment | Matrícula rejeitada |
| failed | Reprovado no exame vestibular |
| dropped_out | Desistente |
| dropping_out | Desistindo |
| drop_out_confirmed | Desistência confirmada |

## Admissão digital

| Status | Descrição |
| ---- | --------- |
| initiated | Inscrição para exame pendente |
| pending_docs | Documentação pendente |
| partially_submitted_docs | Documentação de bolso enviada e contrato assinado |
| submitted_docs | Documentação completamente enviada |
| rejected_docs | Documentação rejeitada |
| awaiting_enrollment | Aguardando formação de turma |
| enrolled | Matriculado |
| | |
| rejected_enrollment | Matrícula rejeitada |
| failed | Reprovado no exame vestibular |
| dropped_out | Desistente |
| dropping_out | Desistindo |
| drop_out_confirmed | Desistência confirmada |

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
    "rg": "45.478.123-X",
    "birth_date": "1991-01-01",
    "emails": [
      "teste@exemplo.com"
    ],
    "phones": [
      "(11) 98888-7777"
    ],
    "document_expeditor": "SSP-SP",
    "address_information": {
      "address": "Rua Sandra",
      "number": "432S",
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
  "status": "error",
  "errors": [
    {
      "title": "Student not found",
      "details": "No student associated with this CPF was found"
    }
  ]
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
| rg | string | RG do aluno |
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
  "rg": "45.478.123-X",
  "birth_date": "1991-01-01",
  "emails": [
    "teste@exemplo.com"
  ],
  "phones": [
    "(11) 98888-7777"
  ],
  "document_expeditor": "SSP-SP",
  "address_information": {
    "address": "Rua Sandra",
    "number": "432S",
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
  "status": "error",
  "errors": [
    {
      "title": "Student not found",
      "details": "No student associated with this ID was found"
    }
  ]
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
| rg | string | RG do aluno |
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

# Informações de processos de admissão

## Listar todos os processos de admissão

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions
```

> Requisição usando filtro de status

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions?status=pending_docs
```

> Resposta

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
          "neighborhood": "Chácara Dora",
          "city": "Araçariguama",
          "state": "SP",
          "postal_code": "18147-000"
        }
      },
      "applications": [
        {
          "id": 123456,
          "type": "exam",
          "exam": {
            "id":456,
            "course_skus": [
              "ADM-MANHA-SP",
              "DIR-MANHA-SP",
              "ADM-NOITE-RJ"
            ],
            "exam_location": {
              "address": "Rua Márcia",
              "number": "4231",
              "neighborhood": "Morro do Barreto",
              "city": "São Roque",
              "state": "SP",
              "postal_code": "19110-000"
            },
            "dates": "2016-11-01",
            "times": "18:30",
            "status": "active",
            "created_at": "2016-10-01T03:15:44Z"
          },
          "result": "approved",
          "created_at": "2016-10-01T03:15:44Z"
        }
      ],
      "created_at": "2016-10-01T03:15:44Z",
      "extra_data": {
        "external_enrollment_id": "NPW231"
      },
      "documents": [
        {
          "type": "titulo_de_eleitor",
          "id": 226,
          "created_at": "2018-12-19T14:36:37Z"
        },
        ...,
        {
          "type": "historico_escolar",
          "id": 225,
          "created_at": "2018-12-19T14:36:19Z"
        }
      ]
    }
  ]
}
```

Retorna todas as admissões da faculdade.

Admissões são retornadas em páginas de até 25 elementos, ordenadas pela última atualização realizada. Se houver mais resultados, `has_more` retorna `true` indicando que é possível usar o parâmetro `ending_before` para consultar objetos antecessores à lista atual. Para mais informações, consulte a seção de [paginação](#paginacao).

Também é possível utilizar um parâmetro extra de `status` na URL para filtrar as admissões em um determinado status.


### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| starting_after | cursor | Cursor para uso em paginação. Retorna elementos listados após o dado cujo id foi indicado |
| ending_before | cursor | Cursor para uso em paginação. Retorna elementos listados antes do dado cujo id foi indicado |
| start_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados a partir da data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| end_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados até a data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| status | string | Aplica filtro de status para admissões (opcional) |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| has_more | boolean | Indica se há mais elementos disponíveis antes ou após essa página |
| items | array | Lista de elementos com dados de inscrições de vestibular |
| id | number | Id da admissão |
| course | object | Objeto com dados do curso referente a essa matrícula |
| [course] id | string | Código do curso fornecido pela universidade referente a essa matrícula |
| [course] offer | object | Objeto com dados da oferta do curso |
| [offer] discount | float | Desconto do curso que o aluno adquiriu |
| status | string | Status da admissão do aluno |
| student | object | Objeto com dados do aluno |
| application | array | Lista de objetos de inscrições de exame (pode estar vazio) |
| [application] id | number | Id da inscrição para exame |
| [application] type | string | Tipo de exame vestibular (exam ou enem) |
| [application] student | object | Objeto com os dados do aluno referente a essa matrícula |
| exam | object | Objeto com informações do exame referente a essa matrícula |
| [exam] id | number | Id do exame vestibular |
| [exam] course_skus | array | Lista com os cursos pertencentes a este exame vestibular |
| [exam] local | object | Objeto com dados do local do exame vestibular |
| [exam_location] address | string | Endereço da localização do exame vestibular |
| [exam_location] number | string | Número da localização do exame vestibular |
| [exam_location] neighborhood | string | Bairro da localização do exame vestibular |
| [exam_location] city | string | Cidade da localização do exame vestibular |
| [exam_location] state | string | Estado da localização do exame vestibular |
| [exam_location] postal_code | string | Código postal da localização do exame vestibular |
| [exam] dates | string | Data da realização do exame vestibular no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [exam] times | string | Hora da realização do exame vestibular no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [exam] status | string | Status do exame vestibular |
| [exam] created_at | string | Data da criação do exame vestibular no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [application] result | string | Resultado do exame vestibular |
| [application] created_at | string | Data de criação da inscrição para exame no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| created_at | string | Data de criação da admissão no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [extra_data] external_enrollment_id | string | Identificador de matrícula enviado pela faculdade |

### Significado dos valores em result
| Nome | Descrição |
| ---- | --------- |
| null | Inscrição para exame Pendente |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |

## Dados de um único processo de admissão

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions/12345
```

> Resposta

```json
{
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
      "neighborhood": "Chácara Dora",
      "city": "Araçariguama",
      "state": "SP",
      "postal_code": "18147-000"
    }
  },
  "applications": [
    {
      "id": 123456,
      "type": "exam",
      "exam": {
        "id":456,
        "course_skus": [
          "ADM-MANHA-SP",
          "DIR-MANHA-SP",
          "ADM-NOITE-RJ"
        ],
        "exam_location": {
          "address": "Rua Márcia",
          "number": "4231",
          "neighborhood": "Morro do Barreto",
          "city": "São Roque",
          "state": "SP",
          "postal_code": "19110-000"
        },
        "dates": "2016-11-01",
        "times": "18:30",
        "status": "active",
        "created_at": "2016-10-01T03:15:44Z"
      },
      "result": "approved",
      "created_at": "2016-10-01T03:15:44Z"
    }
  ],
  "created_at": "2016-10-01T03:15:44Z",
  "extra_data": {
    "external_enrollment_id": "NPW231"
  },
  "documents": [
    {
      "type": "titulo_de_eleitor",
      "id": 226,
      "created_at": "2018-12-19T14:36:37Z"
    },
    ...,
    {
      "type": "historico_escolar",
      "id": 225,
      "created_at": "2018-12-19T14:36:19Z"
    }
  ]
}
```

> Resposta quando não encontra nenhuma admissão

```json
{
  "status": "error",
  "errors": [
    {
      "title": "Admission not found",
      "details": "No admission associated with this ID was found"
    }
  ]
}
```

Retorna uma admissão específica da faculdade.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | path | Id da admissão |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | Id da admissão |
| course | object | Objeto com dados do curso referente a essa matrícula |
| [course] id | string | Código do curso fornecido pela universidade referente a essa matrícula |
| [course] offer | object | Objeto com dados da oferta do curso |
| [offer] discount | float | Desconto do curso que o aluno adquiriu |
| status | string | Status da admissão do aluno |
| student | object | Objeto com dados do aluno |
| application | array | Lista de objetos de inscrição para exame (pode estar vazio) |
| [application] id | number | Id da inscrição para exame |
| [application] type | string | Tipo de exame vestibular (exam ou enem) |
| [application] student | object | Objeto com os dados do aluno referente a essa matrícula |
| exam | object | Objeto com informações do exame referente a essa matrícula |
| [exam] id | number | Id do exame vestibular |
| [exam] course_skus | array | Lista com os cursos pertencentes a este exame vestibular |
| [exam] local | object | Objeto com dados do local do exame vestibular |
| [exam_location] address | string | Endereço da localização do exame vestibular |
| [exam_location] number | string | Número da localização do exame vestibular |
| [exam_location] neighborhood | string | Bairro da localização do exame vestibular |
| [exam_location] city | string | Cidade da localização do exame vestibular |
| [exam_location] state | string | Estado da localização do exame vestibular |
| [exam_location] postal_code | string | Código postal da localização do exame vestibular |
| [exam] dates | string | Data da realização do exame vestibular no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [exam] times | string | Hora da realização do exame vestibular no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [exam] status | string | Status do exame vestibular |
| [exam] created_at | string | Data da criação do exame vestibular no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [application] result | string | Resultado do exame vestibular |
| [application] created_at | string | Data de criação da inscrição para exame no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| created_at | string | Data de criação da admissão no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [extra_data] external_enrollment_id | string | Identificador de matrícula enviado pela faculdade |

### Significado dos valores em result

| Nome | Descrição |
| ---- | --------- |
| null | Inscrição para exame Pendente |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |

## Atualizar processo de admissão

> Requisição

```bash
curl -X PUT --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions/12345 \
  --data '{"status": "enrolled", "extra_data": {"external_enrollment_id": "NPW231"}}'
```

> Resposta

```json
{
  "id": 12345,
  "course": {
    "id": "ADM-MANHA-SP",
    "offer": {
      "discount": 50.0
    }
  },
  "status": "enrolled",
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
      "neighborhood": "Chácara Dora",
      "city": "Araçariguama",
      "state": "SP",
      "postal_code": "18147-000"
    }
  },
  "applications": [
    {
      "id": 123456,
      "type": "exam",
      "exam": {
        "id":456,
        "course_skus": [
          "ADM-MANHA-SP",
          "DIR-MANHA-SP",
          "ADM-NOITE-RJ"
        ],
        "exam_location": {
          "address": "Rua Márcia",
          "number": "4231",
          "neighborhood": "Morro do Barreto",
          "city": "São Roque",
          "state": "SP",
          "postal_code": "19110-000"
        },
        "dates": "2016-11-01",
        "times": "18:30",
        "status": "active",
        "created_at": "2016-10-01T03:15:44Z"
      },
      "result": "approved",
      "created_at": "2016-10-01T03:15:44Z"
    }
  ],
  "created_at": "2016-10-01T03:15:44Z",
  "extra_data": {
    "external_enrollment_id": "NPW231"
  },
  "documents": [
    {
      "type": "titulo_de_eleitor",
      "id": 226,
      "created_at": "2018-12-19T14:36:37Z"
    },
    ...,
    {
      "type": "historico_escolar",
      "id": 225,
      "created_at": "2018-12-19T14:36:19Z"
    }
  ]
}
```

> Retorno quando parâmetros estão incorretos

```json
{
  "error": true,
  "message": "Invalid parameters",
  "description": "Unknown status. Please check the API manual at https://docs.queroalunos.com/"
}
```

> Retorno quando não encontra a admissão

```json
{
  "error": true,
  "errors":
  [
    {
      "title": "Admission not found",
      "details": "No admission associated with this ID was found"
    }
  ]
}
```

> Exemplo de retorno quando há um erro na atualização requisitada

```json
{
  "error": true,
  "errors":
  [
    {
      "title": "Error in Admission API",
      "details": "Cannot transit to enrolled, current status is enrolled"
    }
  ]
}
```

Realiza atualização de um processo de admissão específico de um aluno. Para isso é preciso enviar a informação atual dele pelo parâmetro `status`.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | path | Id da admissão |
| status | form | situação da admissão |
| [extra_data] external_enrollment_id | string | Identificador de matrícula enviado pela faculdade (opcional) |

### Possíveis valores para o parâmetro status

| Status | Descrição |
| ------ | --------- |
| registered | Agendamento confirmado no exame vestibular |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |
| pending_docs | Documentação Pendente |
| enrolled | Matriculado |
| drop_out_confirmed | Confirmação de desistência do aluno |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | Id da admissão |
| course | object | Objeto com dados do curso referente a essa matrícula |
| [course] id | string | Código do curso fornecido pela universidade referente a essa matrícula |
| [course] offer | object | Objeto com dados da oferta do curso |
| [offer] discount | float | Desconto do curso que o aluno adquiriu |
| status | string | Status da admissão do aluno |
| student | object | Objeto com dados do aluno |
| application | array | Lista de objetos de inscrição para exame (pode estar vazio) |
| [application] id | number | Id da inscrição para exame |
| [application] type | string | Tipo de exame vestibular (exam ou enem) |
| [application] student | object | Objeto com os dados do aluno referente a essa matrícula |
| exam | object | Objeto com informações do exame referente a essa matrícula |
| [exam] id | number | Id do exame vestibular |
| [exam] course_skus | array | Lista com os cursos pertencentes a este exame vestibular |
| [exam] local | object | Objeto com dados do local do exame vestibular |
| [exam_location] address | string | Endereço da localização do exame vestibular |
| [exam_location] number | string | Número da localização do exame vestibular |
| [exam_location] neighborhood | string | Bairro da localização do exame vestibular |
| [exam_location] city | string | Cidade da localização do exame vestibular |
| [exam_location] state | string | Estado da localização do exame vestibular |
| [exam_location] postal_code | string | Código postal da localização do exame vestibular |
| [exam] dates | string | Data da realização do exame vestibular no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [exam] times | string | Hora da realização do exame vestibular no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [exam] status | string | Status do exame vestibular |
| [exam] created_at | string | Data da criação do exame vestibular no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [application] result | string | Resultado do exame vestibular |
| [application] created_at | string | Data de criação da inscrição para exame no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| created_at | string | Data de criação da admissão no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [extra_data] external_enrollment_id | string | Identificador de matrícula enviado pela faculdade |

### Significado dos valores em result

| Nome | Descrição |
| ---- | --------- |
| null | Inscrição para exame Pendente |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |

## Aprovar processo de admissão aguardando a formação de turma

> Requisição

```bash
curl -X PUT --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions/12345/awaiting_enrollment \
  --data '{"days_to_enroll": "5"}'
```

> Resposta

```json
{
  "id": 12345,
  "course": {
    "id": "ADM-MANHA-SP",
    "offer": {
      "discount": 50.0
    }
  },
  "status": "awaiting_enrollment",
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
      "neighborhood": "Chácara Dora",
      "city": "Araçariguama",
      "state": "SP",
      "postal_code": "18147-000"
    }
  },
  "applications": [
    {
      "id": 123456,
      "type": "exam",
      "exam": {
        "id":456,
        "course_skus": [
          "ADM-MANHA-SP",
          "DIR-MANHA-SP",
          "ADM-NOITE-RJ"
        ],
        "exam_location": {
          "address": "Rua Márcia",
          "number": "4231",
          "neighborhood": "Morro do Barreto",
          "city": "São Roque",
          "state": "SP",
          "postal_code": "19110-000"
        },
        "dates": "2016-11-01",
        "times": "18:30",
        "status": "active",
        "created_at": "2016-10-01T03:15:44Z"
      },
      "result": "approved",
      "created_at": "2016-10-01T03:15:44Z"
    }
  ],
  "created_at": "2016-10-01T03:15:44Z",
  "extra_data": {
    "external_enrollment_id": "NPW231"
  }
}
```

> Retorno quando parâmetros estão incorretos

```json
{
  "error": true,
  "message": "Invalid parameters",
  "description": "Unknown status. Please check the API manual at https://docs.queroalunos.com/"
}
```

> Retorno quando não encontra a admissão

```json
{
  "error": true,
  "errors":
  [
    {
      "title": "Admission not found",
      "details": "No admission associated with this ID was found"
    }
  ]
}
```

> Exemplo de retorno quando há um erro na atualização requisitada

```json
{
  "error": true,
  "errors":
  [
    {
      "title": "Error in Admission API",
      "details": "Cannot reject enrollment, current status is enrolled"
    }
  ]
}
```

Realiza atualização de um processo de admissão específico de um aluno. Para isso é preciso enviar a informação atual dele pelo parâmetro `status`.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | path | Id da admissão |
| days_to_enroll | string | Previsão para formação de turma para essa admissão (em dias) |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | Id da admissão |
| course | object | Objeto com dados do curso referente a essa matrícula |
| [course] id | string | Código do curso fornecido pela universidade referente a essa matrícula |
| [course] offer | object | Objeto com dados da oferta do curso |
| [offer] discount | float | Desconto do curso que o aluno adquiriu |
| status | string | Status da admissão do aluno |
| student | object | Objeto com dados do aluno |
| application | array | Lista de objetos de inscrição para exame (pode estar vazio) |
| [application] id | number | Id da inscrição para exame |
| [application] type | string | Tipo de exame vestibular (exam ou enem) |
| [application] student | object | Objeto com os dados do aluno referente a essa matrícula |
| exam | object | Objeto com informações do exame referente a essa matrícula |
| [exam] id | number | Id do exame vestibular |
| [exam] course_skus | array | Lista com os cursos pertencentes a este exame vestibular |
| [exam] local | object | Objeto com dados do local do exame vestibular |
| [exam_location] address | string | Endereço da localização do exame vestibular |
| [exam_location] number | string | Número da localização do exame vestibular |
| [exam_location] neighborhood | string | Bairro da localização do exame vestibular |
| [exam_location] city | string | Cidade da localização do exame vestibular |
| [exam_location] state | string | Estado da localização do exame vestibular |
| [exam_location] postal_code | string | Código postal da localização do exame vestibular |
| [exam] dates | string | Data da realização do exame vestibular no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [exam] times | string | Hora da realização do exame vestibular no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [exam] status | string | Status do exame vestibular |
| [exam] created_at | string | Data da criação do exame vestibular no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [application] result | string | Resultado do exame vestibular |
| [application] created_at | string | Data de criação da inscrição para exame no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| created_at | string | Data de criação da admissão no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [extra_data] external_enrollment_id | string | Identificador de matrícula enviado pela faculdade |

### Significado dos valores em result

| Nome | Descrição |
| ---- | --------- |
| null | Inscrição para exame Pendente |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |

# Informações de documentos de um processo de admissão

## Listar todos os documentos de um processo de admissão

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions/456/documents
```

> Resposta

```json
{
  "has_more": false,
  "items": [
    {
      "type": "cnh",
      "id": 220,
      "created_at": "2018-12-19T14:34:16Z",
      "admission": {
        "status": "enrolled",
        "id": 456,
        "extra_data": { ... },
        "created_at": "2018-12-19T14:33:02Z",
        "course": {
          "offer": {
            "enrollment_semester": "2019.1",
            "discount": 10
          },
          "id": "dWQ66a#hjnwYa"
        }
      }
    },
    ...,
    {
      "type": "certificado_de_reservista",
      "id": 222,
      "created_at": "2018-12-19T14:34:51Z",
      "admission": {
        "status": "enrolled",
        "id": 456,
        "extra_data": { ... },
        "created_at": "2018-12-19T14:33:02Z",
        "course": {
          "offer": {
            "enrollment_semester": "2019.1",
            "discount": 10
          },
          "id": "dWQdda#hjnwAa"
        }
      }
    }
  ]
}
```

Retorna todas os documentos para um processo de admissão.

A lista de documentos são retornadas em páginas de até 25 elementos, ordenadas pela última atualização realizada. Se houver mais resultados, `has_more` retorna `true` indicando que é possível usar o parâmetro `ending_before` para consultar objetos antecessores à lista atual. Para mais informações, consulte a seção de [paginação](#paginacao).

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| starting_after | cursor | Cursor para uso em paginação. Retorna elementos listados após o dado cujo id foi indicado |
| ending_before | cursor | Cursor para uso em paginação. Retorna elementos listados antes do dado cujo id foi indicado |
| start_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados a partir da data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| end_date | string | Data para filtrar a listagem de elementos. Lista apenas elementos criados até a data indicada no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| id | Path | Id da admissão |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| has_more | boolean | Indica se há mais elementos disponíveis antes ou após essa página |
| items | array | Lista de elementos com dados de inscrições de vestibular |
| id | number | Id do documento de admissão |
| type | string | Tipo do documento |
| created_at | string | Data de submissão do documento no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [admission] | object | Objeto que identifica o processo de admissão a que o documento se refere |

## Dados de um único documento de um processo de admissão

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions/456/documents/12345
```

> Resposta

```json
{
  "id": 12345,
  "type": "rg",
  "url": "https://s3-example.amazonaws.com/example.png",
  "created_at": "2018-12-19T14:34:51Z",
  "admission": {
    "id": 456,
    "status": "submitted_docs",
    "extra_data": { ... },
    "created_at": "2018-12-19T14:33:02Z",
    "course": {
      "id": "ADM-MANHA-SP",
      "offer": {
        "enrollment_semester": "2019.1",
        "discount": 50.0
      }
    }
  }
}
```


> Resposta quando não encontra nenhuma admissão

```json
{
  "error": true,
  "errors": [
    {
      "title": "Admission not found",
      "details": "No admission associated with this ID was found"
    }
  ]
}
```

> Resposta quando não encontra nenhum documento associado à admissão

```json
{
  "error": true,
  "errors": [
    {
      "title": "Document not found",
      "details": "No document associated with this ID was found"
    }
  ]
}
```

Retorna um documento específico para um processo de admissão.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | Path | Id da admissão |
| document_id | Path | Id do documento referente à admissão |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | Id do documento de admissão |
| type | string | Tipo do documento |
| url | string | URL que aponta para a imagem do documento |
| created_at | string | Data de submissão do documento no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [admission] | object | Objeto que identifica o processo de admissão a que o documento se refere |


# Informações de agendamento de processo seletivo

## Informações de campus pelo código do curso

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://rota-da-faculdade.com/path/de/informacoes/de/campus?course_id=XYZ123
```

> Exemplo de resposta

```json
{
  "campus": {
    "id": "ABC123",
    "name": "Unidade SJC",
    "address": "Rua da Alegria, 999",
    "city": "São José dos Campos",
    "state": "SP"
  }
}
```

A rota com informações do campus deve ser fornecida pela faculdade, junto de um token para autenticação via Basic Auth.

A rota deve aceitar o formato JSON e tipo de requisição GET.

É esperado erro 401 na resposta caso exista algum problema com a autenticação.

É esperado erro 404 na resposta caso o `course_id` não seja reconhecido.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| course_id | Query | Código interno do curso na faculdade, enviado anteriormente para o Quero Bolsa |

### Informações do resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | string | Código interno do campus |
| name | string | Nome do campus |
| address | string | Endereço do campus |
| city | string | Cidade do campus |
| state | string | Estado do campus |

## Informações de horário de agendamento por campus

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://rota-da-faculdade.com/path/de/informacoes/de/horarios?campus_id=ADM-MANHA-SP-SJC
```

> Exemplo de resposta

```json
{
  "schedule": [
    {
      "date": "2018-02-15",
      "time": ["10:00", "11:00", "12:00"]
    },
    {
      "date": "2018-02-16",
      "time": ["10:00", "11:00", "12:00"]
    },
    {
      "date": "2018-02-17",
      "time": ["10:00", "11:00", "12:00", "15:00", "16:00"]
    },
    {
      "date": "2018-02-18",
      "time": ["10:00", "11:00", "12:00", "15:00", "16:00"]
    }
  ]
}
```

A rota com informações para horários de agendamento por campus deve ser fornecida pela faculdade, junto de um token para autenticação via Basic Auth.

A rota deve aceitar o formato JSON e tipo de requisição GET.

É esperado erro 401 na resposta caso exista algum problema com a autenticação.

É esperado erro 404 na resposta caso o `campus_id` não seja reconhecido.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| campus_id | Query | Código interno do campus na faculdade, que foi recuperado pelo evento de [informação de campus pelo código do curso](#informacoes-de-campus-pelo-codigo-do-curso) |

### Informações de resultado

Os resultados devem vir em um array de objetos `schedule`, onde cada objeto contem chaves `date` e `time`, correspondendo aos dias e horários disponíveis para agendamento de processo seletivo.

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| schedule | array | Array de objetos com os horários de processo seletivo do campus |
| [schedule] date | string | Data da solicitação de agendamento do processo seletivo no formato [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [schedule] time | array de string | Array com horários da solicitação de agendamento do processo seletivo. Formato: hh:mm (padrão 24 horas) |

## Realizar agendamento de processo seletivo

> Requisição

```bash
curl -X POST --header "Authorization: Token ########" --header "Content-Type: application/json" https://rota-da-faculdade.com/path/de/agendar \
  --data '{
    "postback_url": "https://queroalunos.com/api/v1/applications/e9929544-00be-4a2e-b534-5191746fc61c/update",
    "campus_id": "XYZ123",
    "date": "2018-02-15",
    "time": "12:00",
    "cpf": "416.529.073-43",
    "full_name": "João da Silva",
    "course_id": "ADM-MANHA-SP-SJC"
  }'
```

> Resposta quando o agendadamento é realizado

```json
{
  "status": "registered"
}
```

> Resposta quando o agendadamento é rejeitado

```json
{
  "status": "rejected",
  "reason": "Aluno bloqueado no sistema."
}
```

A rota para fazer agendamento de Processo Seletivo deve ser fornecida pela faculdade, junto de um token para autenticação via HTTP Basic.

A rota deve aceitar o formato JSON e tipo de requisição POST.

É esperado erro 401 na resposta caso exista algum problema com a autenticação.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| postback_url | string | Rota de retorno para enviar resultado do processo seletivo do aluno |
| campus_id | string | Código interno do campus na faculdade |
| date | string | Data da solicitação de agendamento do processo seletivo no formato [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| time | string | Hora da solicitação de agendamento do processo seletivo. Formato: hh:mm (padrão 24 horas) |
| cpf | string | CPF do aluno que solicitou agendamento |
| full_name | string | Nome completo do aluno que solicitou agendamento |
| course_id | string | Código interno do curso na faculdade |

### Informações do resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| status | string | Status da solicitação de agendamento |
| reason | string | Descrição do motivo da rejeição, caso o agendamento falhe ou seja negado |

### Status do resultado

| Nome | Significado |
| ---- | ----------- |
| registered | Agendamento realizado com sucesso |
| rejected | Agendamento não realizado |

## Envio de resultado de processo seletivo

> Requisição

```bash
curl -X POST --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/applications/e9929544-00be-4a2e-b534-5191746fc61c/update \
  --data '{
    "result": "approved",
    "grade": "83"
  }'
```

> Resposta quando o resultado não é computado

```json
{
  "error": "Cannot approve, current status is approved"
}
```

A rota para onde a requisição será feita vem no parâmetro `postback_url` no evento de [realizar agendamento de processo seletivo](#realizar-agendamento-de-processo-seletivo)

É enviado status `200` na resposta caso o resultado chegue corretamente.

É enviado status `404` na resposta caso a url de postback seja inválida.

É enviado status `500` com parâmetro `error` na resposta caso o resultado não seja computado.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| result | string | Resultado do aluno no processo seletivo |
| grade | string | Nota do aluno no processo seletivo (opcional) |

### Valores para `result`

| Nome | Significado |
| ---- | ----------- |
| approved | Aprovado no processo seletivo |
| failed | Reprovado no processo seletivo |

# Notificações

Notificações utilizam uma rota única de callback, que deve ser fornecida pela faculdade, de um token para autenticação via Basic Auth.

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


## Notificar atualização de um processo de admissão

```json
{
  "event_type": "admission.updated",
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

Esta notificação informa a atualização de um processo de admissão.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| created | string | Data que foi criado o evento no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| api_version | string | Informação da versão atual da API |
| event_type | string | Tipo de evento, no caso `admission.updated` |
| data | object | Objeto com informações de acordo com o tipo de evento |
| admission | object | Objeto com dados do processo de admissão do aluno |
| [admission] id | number | Id do processo de admissão |
| [admission] course | object | Objeto com dados do curso referente a essa matrícula |
| [course] id | string | Código do curso fornecido pela universidade referente a essa matrícula |
| [course] offer | object | Objeto com dados da oferta do curso |
| [offer] discount | float | Porcentagem de desconto do curso que o aluno adquiriu |
| [admission] status | string | Status da admissão do aluno |
| [admission] student | object | Objeto com dados do aluno |

## Notificar novo exame

```json
{
  "event_type": "application.submitted",
  "created": "2017-12-15T17:34:26.173",
  "api_version": "1.0.0",
  "data": {
    "application": {
      "id": "5bb84b6c-8716-4363-b59d-7005a97a08a4",
      "type": "enem",
      "result": "approved|failed",
      "created_at": "2016-10-15T03:15:44Z",
      "enem": {
        "natureza": "500.0",
        "humanas": "500.0",
        "linguagens": "500.0",
        "matematica": "500.0",
        "redacao": "500.0"
      },
      "exam": {
        "standards_score": 200,
        "argument_score": 200,
        "theme_score": 200,
        "cohesion_score": 200,
        "proposal_score": 200,
        "total_score": 200,
        "essay_body": "lorem ipsum dolor sit amet"
      },
      "admission": {
        "id": 456,
        "status": "initiated",
        "course": {
          "id": "ADM-MANHA-SP",
          "offer": {
            "discount": 50.0
          }
        }
      }
    }
  }
}
```

Esta notificação informa a realização do vestibular (via Exame Quero Bolsa ou Nota do Enem) do processo de admissão.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| created | string | Data que foi criado o evento no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| api_version | string | Informação da versão atual da API |
| event_type | string | Tipo de evento, no caso `application.submitted` |
| data | object | Objeto com informações de acordo com o tipo de evento |
| application | object | Objeto com dados do processo de admissão do aluno |
| [application] id | string (uuid) | Id do exame |
| [application] type | string | Indica o tipo de vestibular |
| [application] result | string | Resultado do vestibular `approved|failed` |
| [application] created_at | string | Data da criação do exam no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| [application] enem | object | Objeto com as notas do enem separadas por area de conhecimento
| [application][enem] natureza | string | Nota em `Natureza e suas Tecnologias` |
| [application][enem] humanas | string | Nota em `Humanas` |
| [application][enem] linguagens | string | Nota em `Linguagens` |
| [application][enem] matematica | string | Nota em `Matemática` |
| [application][enem] redacao | string | Nota em `Redação` |
| [application] exam | object | Objeto com as notas do exame de cada competência e a redação |
| [application][exam] standards_score | number | Nota em `Norma culta` |
| [application][exam] argument_score | number | Nota em `Adequação ao tema` |
| [application][exam] theme_score | number | Nota em `Argumentação` |
| [application][exam] cohesion_score | number | Nota em `Coesão textual` |
| [application][exam] proposal_score | number | Nota em `Proposta de intervenção` |
| [application][exam] total_score | number | Soma das notas (nota final) |
| [application][exam] essay_body | string | Conteúdo da redação |

## Notificar nova submissão de documento de admissão

```json
{
  "event_type": "document.submitted",
  "created": "2017-12-15T17:34:26.173",
  "api_version": "1.0.0",
  "data": {
    "document": {
      "id": 12345,
      "type": "rg",
      "admission": {
        "id": 456,
        "status": "submitted_docs",
        "course": {
          "id": "ADM-MANHA-SP",
          "offer": {
            "discount": 50.0
          }
        }
      }
    }
  }
}
```

Esta notificação informa a submissão de um documento do processo de admissão.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| created | string | Data que foi criado o evento no formato UTC [ISO 8601](https://pt.wikipedia.org/wiki/ISO_8601) |
| api_version | string | Informação da versão atual da API |
| event_type | string | Tipo de evento, no caso `document.submitted` |
| data | object | Objeto com informações de acordo com o tipo de evento |
| document | object | Objeto com dados do documento de admissão do aluno |
| [document] id | number | Id do documento de admissão |
| [document] type | string | Tipo do documento |

### Significado dos valores em type

| Nome | Descrição |
| ---- | --------- |
| rg | RG|
| cpf | CPF |
| cnh | CNH |
| rg_do_guardiao | RG do guardião do menor de idade |
| cpf_do_guardiao | CPF do guardião do menor de idade |
| cnh_do_guardiao | CNH do guardião do menor de idade |
| certificado_de_reservista | Certificado de Reservista (se maior de 18 anos e do sexo masculino) |
| titulo_de_eleitor | Título de Eleitor (se maior de 18 anos) |
| historico_escolar | Histórico Escolar |
| diploma | Diploma de Graduação |
| comprovante_de_residencia | Comprovante de Residência |
| comprovante_de_residencia_do_guardiao | Comprovante de Residência do guardião (se menor de idade) |
| comprovante_de_voto | Comprovante de Voto |

## Listagem de notificações

> Requisição

```bash
curl --header "Authorization: Base ########" --header "Content-Type: application/json" https://queroalunos.com/api/{version}/notifications/search
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
