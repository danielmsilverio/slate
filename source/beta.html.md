---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  -

search: true
---

# Começando com API de Admissões do Quero Alunos

Bem-vindo à documentação da API de Admissões do Quero Alunos. Aqui você encontrará guias e referências sobre como utilizar nossa API. Qualquer dúvida, entre em contato pelo email api-admissoes@quero.education.

## Informações básicas

Todas as respostas da API são feitas em JSON

Endpoint utilizado

`https://queroalunos.com/api/v1`


# Autenticação

```
POST https://queroalunos.com/api/v1/{action} HTTP/1.1
Content-Type: application/json
authorization: Token ••••••••••••
```

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
  "status": "error",
  "errors": [{
    "title": "Invalid Permission",
    "detail": "You are not authorized to access this resource with these paramaters"
  }]
}
```

Em caso de token válido mas sem permissão de acesso ao recurso específico


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
      "neighborhood": "Chácara Dora",
      "city": "Araçariguama",
      "state": "SP",
      "postal_code": "18147-000"
    },
    "admissions": [
      {
        "id": 12345,
        "course_sku": "ADM-NOITE-EAD",
        "status": "pending_docs"
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
| cpf | Query| CPF do aluno procurado. Exige CPF completamente formatado (ex: 123.456.789-10). |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do aluno. |
| name | string | nome do aluno |
| cpf | string | cpf do aluno |
| birth_date | string | data de nascimento do aluno |
| emails | array de string | lista de emails do aluno |
| phones | array de string | lista de telefones do aluno |
| address_information | object | objeto com dados onde aluno reside |
| [address_information] address | string | endereço onde aluno reside |
| [address_information] number | string | número onde aluno reside |
| [address_information] neighborhood | string | bairro onde aluno reside |
| [address_information] city | string | cidade onde aluno reside |
| [address_information] state | string | estado onde aluno reside |
| [address_information] postal_code | string | código postal onde aluno reside |
| admissions | array | lista de objeto com informações de processo de matricula |
| id (admissions) | number | id do processo de matricula |
| course_sku (admissions) | string | código do curso fornecido pela universidade |
| status (admissions) | string | status que se encontra o processo de matricula |

### Significado dos valores em status
| Nome | Descrição |
| ---- | --------- |
| initiated | Inscrição para exame Pendente |
| pre_registered | Agendamento solicitado para exame vestibular |
| registered | Agendamento confirmado no exame vestibular |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |
| pending_docs | Documentação Pendente |
| submitted_docs | Documentação Enviada |
| rejected_docs | Documentação Rejeitada |
| enrolled | Matriculado |
| dropped_out | Desistente |
| dropping_out | Desistindo |
| drop_out_confirmed | Desistência confirmada |

## Busca de aluno por ID

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
    "neighborhood": "Chácara Dora",
    "city": "Araçariguama",
    "state": "SP",
    "postal_code": "18147-000"
  },
  "admissions": [
    {
      "id": 12345,
      "course_sku": "ADM-NOITE-EAD",
      "status": "pending_docs"
    }
  ]
}
```
> Resposta quando não encontra nenhum aluno

```json
{
  "error": true,
  "message": "ID not found",
  "description": "Could not find any student by given ID."
}
```

Somente busca por alunos que tenham pré-matrícula na faculdade pertencente ao usuário fazendo pesquisa.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | Path | ID do aluno procurado. |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do aluno. |
| name | string | nome do aluno |
| cpf | string | cpf do aluno |
| birth_date | string | data de nascimento do aluno |
| emails | array de string | lista de emails do aluno |
| phones | array de string | lista de telefones do aluno |
| address_information | object | objeto com dados onde aluno reside |
| [address_information] address | string | endereço onde aluno reside |
| [address_information] number | string | número onde aluno reside |
| [address_information] neighborhood | string | bairro onde aluno reside |
| [address_information] city | string | cidade onde aluno reside |
| [address_information] state | string | estado onde aluno reside |
| [address_information] postal_code | string | código postal onde aluno reside |
| admissions | array | lista de objetos com informações de processo de matricula |
| [admissions] id | number | id do processo de matricula |
| [admissions] course_sku | string | código do curso fornecido pela universidade |
| [admissions] status | string | status que se encontra o processo de matricula |

### Significado dos valores em status
| Nome | Descrição |
| ---- | --------- |
| initiated | Inscrição para exame Pendente |
| pre_registered | Agendamento solicitado para exame vestibular |
| registered | Agendamento confirmado no exame vestibular |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |
| pending_docs | Documentação Pendente |
| submitted_docs | Documentação Enviada |
| rejected_docs | Documentação Rejeitada |
| enrolled | Matriculado |
| dropped_out | Desistente |
| dropping_out | Desistindo |
| drop_out_confirmed | Desistência confirmada |


# Informações de processos de admissão

## Listar todos os processos de admissão

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/admissions
```

> Resposta

```json
{
  "items": [
    {
      "id": 12345,
      "course_sku": "ADM-MANHA-SP",
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
            "status": "active"
          },
          "result": "approved"
        }
      ]
    }
  ],
  "next_page": "12345",
  "previous_page": null
}
```

Retorna todas as admissões da faculdade.

Admissões são retornadas em lote de 10, ordenadas pela última atualização realizada. Se houver mais resultados, `next_page` retorna preenchido para ser utilizado como parâmetro na próxima requisição para continuar. O parâmetro `previous_page` será preenchido com o primeiro id quando utilizado `next_page`, que serve para pegar as informações da página anterior.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| `next_page` | Query | valor utilizado para continuar uma paginação anterior |
| `previous_page` | Query | valor utilizado para retornar na paginação anterior |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| items | array | lista de objetos com dados de inscrições de vestibular |
| id | number | id da admissão |
| course_sku | string | código do curso referente a essa matrícula |
| status | string | status da admissão do aluno |
| [student] id | number | id do aluno |
| [student] name | string | nome do aluno |
| [student] cpf | string | cpf do aluno |
| [student] birth_date | string | data de nascimento do aluno |
| [student] emails | array de string | lista de emails do aluno |
| [student] phones | array de string | lista de telefones do aluno |
| [student] address_information | object | objeto com dados onde aluno reside |
| [address_information] address | string | endereço onde aluno reside |
| [address_information] number | string | número onde aluno reside |
| [address_information] neighborhood | string | bairro onde aluno reside |
| [address_information] city | string | cidade onde aluno reside |
| [address_information] state | string | estado onde aluno reside |
| [address_information] postal_code | string | código postal onde aluno reside |
| application | array | lista de objetos de inscrições de exame (pode estar vazio) |
| [application] id | number | id da inscrição para exame |
| [application] type | string | tipo de exame vestibular (exam ou enem) |
| [application] student | object | objeto com os dados do aluno referente a essa matrícula |
| exam | object | objeto com informações do exame referente a essa matrícula |
| [exam] id | number | id do exame vestibular |
| [exam] course_skus | array | lista com os cursos pertencentes a este exame vestibular |
| [exam] local | object | objeto com dados do local do exame vestibular |
| [exam_location] address | string | endereço da localização do exame vestibular |
| [exam_location] number | string | número da localização do exame vestibular |
| [exam_location] neighborhood | string | bairro da localização do exame vestibular |
| [exam_location] city | string | cidade da localização do exame vestibular |
| [exam_location] state | string | estado da localização do exame vestibular |
| [exam_location] postal_code | string | código postal da localização do exame vestibular |
| [exam] dates | string | data da realização do exame vestibular |
| [exam] times | string | hora da realização do exame vestibular |
| [exam] status | string | status do exame vestibular |
| [application] result | string | resultado do exame vestibular |
| next_page | string | código para pegar os próximos passos |
| previous_page | string | código para pegar os passos anteriores |

### Significado dos valores em status
| Nome | Descrição |
| ---- | --------- |
| initiated | Inscrição para exame Pendente |
| pre_registered | Agendamento solicitado para exame vestibular |
| registered | Agendamento confirmado no exame vestibular |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |
| pending_docs | Documentação Pendente |
| submitted_docs | Documentação Enviada |
| rejected_docs | Documentação Rejeitada |
| enrolled | Matriculado |
| dropped_out | Desistente |
| dropping_out | Desistindo |
| drop_out_confirmed | Desistência confirmada |

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
  "course_sku": "ADM-MANHA-SP",
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
        "status": "active"
      },
      "result": "approved"
    }
  ]
}
```

> Resposta quando não encontra nenhuma admissão

```json
{
  "error": true,
  "message": "ID not found",
  "description": "Could not find any admission by given ID."
}
```

Retorna uma admissão específica da faculdade.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | path | id da admissão |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| items | array | lista de objetos com dados de inscrições de vestibular |
| id | number | id da admissão |
| course_sku | string | código do curso referente a essa matrícula |
| status | string | status da admissão do aluno |
| [student] id | number | id do aluno |
| [student] name | string | nome do aluno |
| [student] cpf | string | cpf do aluno |
| [student] birth_date | string | data de nascimento do aluno |
| [student] emails | array de string | lista de emails do aluno |
| [student] phones | array de string | lista de telefones do aluno |
| [student] address_information | object | objeto com dados onde aluno reside |
| [address_information] address | string | endereço onde aluno reside |
| [address_information] number | string | número onde aluno reside |
| [address_information] neighborhood | string | bairro onde aluno reside |
| [address_information] city | string | cidade onde aluno reside |
| [address_information] state | string | estado onde aluno reside |
| [address_information] postal_code | string | código postal onde aluno reside |
| application | array | lista de objetos de inscrição para exame (pode estar vazio) |
| [application] id | number | id da inscrição para exame |
| [application] type | string | tipo de exame vestibular (exam ou enem) |
| [application] student | object | objeto com os dados do aluno referente a essa matrícula |
| exam | object | objeto com informações do exame referente a essa matrícula |
| [exam] id | number | id do exame vestibular |
| [exam] course_skus | array | lista com os cursos pertencentes a este exame vestibular |
| [exam] local | object | objeto com dados do local do exame vestibular |
| [exam_location] address | string | endereço da localização do exame vestibular |
| [exam_location] number | string | número da localização do exame vestibular |
| [exam_location] neighborhood | string | bairro da localização do exame vestibular |
| [exam_location] city | string | cidade da localização do exame vestibular |
| [exam_location] state | string | estado da localização do exame vestibular |
| [exam_location] postal_code | string | código postal da localização do exame vestibular |
| [exam] dates | string | data da realização do exame vestibular |
| [exam] times | string | hora da realização do exame vestibular |
| [exam] status | string | status do exame vestibular |
| [application] result | string | resultado do exame vestibular |

### Significado dos valores em status
| Nome | Descrição |
| ---- | --------- |
| initiated | Inscrição para exame Pendente |
| pre_registered | Agendamento solicitado para exame vestibular |
| registered | Agendamento confirmado no exame vestibular |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |
| pending_docs | Documentação Pendente |
| submitted_docs | Documentação Enviada |
| rejected_docs | Documentação Rejeitada |
| enrolled | Matriculado |
| dropped_out | Desistente |
| dropping_out | Desistindo |
| drop_out_confirmed | Desistência confirmada |

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
  --data '{"status": "enrolled"}'
```

> Resposta

```json
{
  "id": 12345,
  "course_sku": "ADM-MANHA-SP",
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
        "status": "active"
      },
      "result": "approved"
    }
  ]
}
```

> Retorno quando parâmetros estão incorretos

```json
{
  "error": true,
  "message": "Invalid parameters",
  "description": "Unknown status. Please check the API manual at http://docs.queroalunos.com/#atualizar-processo-de-admissao"
}
```

> Retorno quando não encontra a admissão

```json
{
  "error": true,
  "message": "ID not found",
  "description": "Could not find any admission by given ID."
}
```

Realiza atualização de um processo de admissão específico de um aluno. Para isso é preciso enviar a informação atual dele pelo parâmetro `status`.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| status | form | situação da admissão |

### Possíveis valores para o parâmetro status

| Status | Descrição |
| ------ | --------- |
| initiated | Inscrição para exame Pendente |
| pre_registered | Agendamento solicitado para exame vestibular |
| registered | Agendamento confirmado no exame vestibular |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |
| pending_docs | Documentação Pendente |
| submitted_docs | Documentação Enviada |
| rejected_docs | Documentação Rejeitada |
| enrolled | Matriculado |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| items | array | lista de objetos com dados de inscrições de vestibular |
| id | number | id da admissão |
| course_sku | string | código do curso referente a essa matrícula |
| status | string | status da admissão do aluno |
| [student] id | number | id do aluno |
| [student] name | string | nome do aluno |
| [student] cpf | string | cpf do aluno |
| [student] birth_date | string | data de nascimento do aluno |
| [student] emails | array de string | lista de emails do aluno |
| [student] phones | array de string | lista de telefones do aluno |
| [student] address_information | object | objeto com dados onde aluno reside |
| [address_information] address | string | endereço onde aluno reside |
| [address_information] number | string | número onde aluno reside |
| [address_information] neighborhood | string | bairro onde aluno reside |
| [address_information] city | string | cidade onde aluno reside |
| [address_information] state | string | estado onde aluno reside |
| [address_information] postal_code | string | código postal onde aluno reside |
| application | array | lista de objetos de inscrição para exame (pode estar vazio) |
| [application] id | number | id da inscrição para exame |
| [application] type | string | tipo de exame vestibular (exam ou enem) |
| [application] student | object | objeto com os dados do aluno referente a essa matrícula |
| exam | object | objeto com informações do exame referente a essa matrícula |
| [exam] id | number | id do exame vestibular |
| [exam] course_skus | array | lista com os cursos pertencentes a este exame vestibular |
| [exam] local | object | objeto com dados do local do exame vestibular |
| [exam_location] address | string | endereço da localização do exame vestibular |
| [exam_location] number | string | número da localização do exame vestibular |
| [exam_location] neighborhood | string | bairro da localização do exame vestibular |
| [exam_location] city | string | cidade da localização do exame vestibular |
| [exam_location] state | string | estado da localização do exame vestibular |
| [exam_location] postal_code | string | código postal da localização do exame vestibular |
| [exam] dates | string | data da realização do exame vestibular |
| [exam] times | string | hora da realização do exame vestibular |
| [exam] status | string | status do exame vestibular |
| [application] result | string | resultado do exame vestibular |

### Significado dos valores em status
| Nome | Descrição |
| ---- | --------- |
| initiated | Inscrição para exame Pendente |
| pre_registered | Agendamento solicitado para exame vestibular |
| registered | Agendamento confirmado no exame vestibular |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |
| pending_docs | Documentação Pendente |
| submitted_docs | Documentação Enviada |
| rejected_docs | Documentação Rejeitada |
| enrolled | Matriculado |
| dropped_out | Desistente |
| dropping_out | Desistindo |
| drop_out_confirmed | Desistência confirmada |

### Significado dos valores em result
| Nome | Descrição |
| ---- | --------- |
| null | Inscrição para exame Pendente |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |

# Informações de inscrição para exame

## Listar todas as inscrições de exame

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/applications
```

> Resposta

```json
{
  "items": [
    {
      "id": 123456,
      "type": "exam",
      "admission": {
        "id": 12345,
        "course_sku": "ADM-MANHA-SP",
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
          "address_information": {
            "address": "Rua Sandra",
            "number": "432S",
            "neighborhood": "Chácara Dora",
            "city": "Araçariguama",
            "state": "SP",
            "postal_code": "18147-000"
          }
        },
      },
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
        "status": "active"
      },
      "result": "approved"
    }
  ],
  "next_page": "12345",
  "previous_page": null
}
```

Retorna todas as inscrições de exame da faculdade.

Inscrições são retornadas em lote de 10, ordenadas pela última atualização realizada. Se houver mais resultados, `next_page` retorna preenchido para ser utilizado como parâmetro na próxima requisição para continuar. O parâmetro `previous_page` será preenchido com o primeiro id quando utilizado `next_page`, que serve para pegar as informações da página anterior.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| `next_page` | Query | valor utilizado para continuar uma paginação anterior |
| `previous_page` | Query | valor utilizado para retornar na paginação anterior |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| items | array| lista de objetos com dados de inscrições de vestibular |
| id | number | id da inscrição para exame |
| type | string | tipo de exame vestibular (exam ou enem) |
| admission | object | objeto com dados do processo de admissão do aluno |
| [admission] id | number | id do processo de admissão |
| [admission] course_sku | string | código do curso referente a essa matrícula |
| [admission] status | string | status da admissão do aluno |
| [admission] student | object | objeto com dados do aluno |
| [student] id | number | id do aluno |
| [student] name | string | nome do aluno |
| [student] cpf | string | cpf do aluno |
| [student] birth_date | string | data de nascimento do aluno |
| [student] emails | array de string | lista de emails do aluno |
| [student] phones | array de string | lista de telefones do aluno |
| [student] address_information | object | objeto com dados onde aluno reside |
| [address_information] address | string | endereço onde aluno reside |
| [address_information] number | string | número onde aluno reside |
| [address_information] neighborhood | string | bairro onde aluno reside |
| [address_information] city | string | cidade onde aluno reside |
| [address_information] state | string | estado onde aluno reside |
| [address_information] postal_code | string | código postal onde aluno reside |
| exam | object | objeto com informações do exame vestibular |
| [exam] id | number | id do exame vestibular |
| [exam] course_skus | array | lista com os cursos pertencentes a este exame vestibular |
| [exam] local | object | objeto com dados do exame vestibular |
| [exam_location] address | string | endereço da localização do exame vestibular |
| [exam_location] number | string | número da localização do exame vestibular |
| [exam_location] neighborhood | string | bairro da localização do exame vestibular |
| [exam_location] city | string | cidade da localização do exame vestibular |
| [exam_location] state | string | estado da localização do exame vestibular |
| [exam_location] postal_code | string | código postal da localização do exame vestibular |
| [exam] date | string | data da realização do exame vestibular |
| [exam] time | string | hora da realização do exame vestibular |
| [exam] status | string | status do exame vestibular |
| result | string | resultado do exame vestibular |
| next_page | string | código para pegar os próximos passos |
| previous_page | string | código para pegar os passos anteriores |

### Significado dos valores em status
| Nome | Descrição |
| ---- | --------- |
| initiated | Inscrição para exame Pendente |
| pre_registered | Agendamento solicitado para exame vestibular |
| registered | Agendamento confirmado no exame vestibular |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |
| pending_docs | Documentação Pendente |
| submitted_docs | Documentação Enviada |
| rejected_docs | Documentação Rejeitada |
| enrolled | Matriculado |
| dropped_out | Desistente |
| dropping_out | Desistindo |
| drop_out_confirmed | Desistência confirmada |

### Significado dos valores em result
| Nome | Descrição |
| ---- | --------- |
| null | Inscrição para exame Pendente |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |

## Dados de uma única inscrição para exame

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/applications/123456
```

> Resposta

```json
{
  "id": 123456,
  "type": "exam",
  "admission": {
    "id": 12345,
    "course_sku": "ADM-MANHA-SP",
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
      "address_information": {
        "address": "Rua Sandra",
        "number": "432S",
        "neighborhood": "Chácara Dora",
        "city": "Araçariguama",
        "state": "SP",
        "postal_code": "18147-000"
      }
    },
  },
  "exam": {
    "id": 456,
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
    "date": "2016-11-01",
    "time": "18:30",
    "status": "active"
  },
  "result": "approved"
}
```

> Resposta quando não encontra nenhuma inscrição para exame

```json
{
  "error": true,
  "message": "ID not found",
  "description": "Could not find any application by given ID."
}
```

Retorna uma inscrição específica na inscrição do exame da faculdade.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | Path | id da inscrição para exame |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id da inscrição para exame |
| type | string | tipo de exame vestibular (exam ou enem) |
| admission | object | objeto com dados do processo de admissão do aluno |
| [admission] id | number | id do processo de admissão |
| [admission] course_sku | string | código do curso referente a essa matrícula |
| [admission] status | string | status da admissão do aluno |
| [admission] student | object | objeto com dados do aluno |
| [student] id | number | id do aluno |
| [student] name | string | nome do aluno |
| [student] cpf | string | cpf do aluno |
| [student] birth_date | string | data de nascimento do aluno |
| [student] emails | array de string | lista de emails do aluno |
| [student] phones | array de string | lista de telefones do aluno |
| [student] address_information | object | objeto com dados onde aluno reside |
| [address_information] address | string | endereço onde aluno reside |
| [address_information] number | string | número onde aluno reside |
| [address_information] neighborhood | string | bairro onde aluno reside |
| [address_information] city | string | cidade onde aluno reside |
| [address_information] state | string | estado onde aluno reside |
| [address_information] postal_code | string | código postal onde aluno reside |
| exam | object | objeto com informações do exame vestibular |
| [exam] id | number | id do exame vestibular |
| [exam] course_skus | array | lista com os cursos pertencentes a este exame vestibular |
| [exam] exam_location | object | objeto com dados do exame vestibular |
| [exam_location] address | string | endereço da localização do exame vestibular |
| [exam_location] number | string | número da localização do exame vestibular |
| [exam_location] neighborhood | string | bairro da localização do exame vestibular |
| [exam_location] city | string | cidade da localização do exame vestibular |
| [exam_location] state | string | estado da localização do exame vestibular |
| [exam_location] postal_code | string | código postal da localização do exame vestibular |
| [exam] date | string | data da realização do exame vestibular |
| [exam] time | string | hora da realização do exame vestibular |
| [exam] status | string | status do exame vestibular |
| result | string | resultado do exame vestibular |

### Significado dos valores em status
| Nome | Descrição |
| ---- | --------- |
| initiated | Inscrição para exame Pendente |
| pre_registered | Agendamento solicitado para exame vestibular |
| registered | Agendamento confirmado no exame vestibular |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |
| pending_docs | Documentação Pendente |
| submitted_docs | Documentação Enviada |
| rejected_docs | Documentação Rejeitada |
| enrolled | Matriculado |
| dropped_out | Desistente |
| dropping_out | Desistindo |
| drop_out_confirmed | Desistência confirmada |

### Significado dos valores em result
| Nome | Descrição |
| ---- | --------- |
| null | Inscrição para exame Pendente |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |

## Atualizar inscrição para exame

> Requisição

```bash
curl -X PUT --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/applications/123456 \
  --data '{"result":"approved"}'
```

> Resposta

```json
{
  "id": 123456,
  "type": "exam",
  "admission": {
    "id": 12345,
    "course_sku": "ADM-MANHA-SP",
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
      "address_information": {
        "address": "Rua Sandra",
        "number": "432S",
        "neighborhood": "Chácara Dora",
        "city": "Araçariguama",
        "state": "SP",
        "postal_code": "18147-000"
      }
    },
  },
  "exam": {
    "id": 456,
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
    "date": "2016-11-01",
    "time": "18:30",
    "status": "active"
  },
  "result": "approved"
}
```

> Retorno quando parâmetros estão incorretos

```json
{
  "error": true,
  "message": "Invalid parameters",
  "description": "Unknown result. Please check the API manual at http://docs.queroalunos.com/#atualizar-inscricao-para-exame"
}
```

> Retorno quando não encontra a inscrição para exame

```json
{
  "error": true,
  "message": "ID not found",
  "description": "Could not find any application by given ID."
}
```

Atualiza a situação de agendamento ou resultado de uma inscrição para exame.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| result | Form | Situação do aluno entre `approved` e `failed` |

### Possíveis valores para o parâmetro result

| Status | Descrição |
| ------ | --------- |
| approved | Aprovado no vestibular |
| failed | Reprovado no vestibular |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id da inscrição para exame |
| type | string | tipo de exame vestibular (exam ou enem) |
| admission | object | objeto com dados do processo de admissão do aluno |
| [admission] id | number | id do processo de admissão |
| [admission] course_sku | string | código do curso referente a essa matrícula |
| [admission] status | string | status da admissão do aluno |
| [admission] student | object | objeto com dados do aluno |
| [student] id | number | id do aluno |
| [student] name | string | nome do aluno |
| [student] cpf | string | cpf do aluno |
| [student] birth_date | string | data de nascimento do aluno |
| [student] emails | array de string | lista de emails do aluno |
| [student] phones | array de string | lista de telefones do aluno |
| [student] address_information | object | objeto com dados onde aluno reside |
| [address_information] address | string | endereço onde aluno reside |
| [address_information] number | string | número onde aluno reside |
| [address_information] neighborhood | string | bairro onde aluno reside |
| [address_information] city | string | cidade onde aluno reside |
| [address_information] state | string | estado onde aluno reside |
| [address_information] postal_code | string | código postal onde aluno reside |
| exam | object | objeto com informações do exame vestibular |
| [exam] id | number | id do exame vestibular |
| [exam] course_skus | array | lista com os cursos pertencentes a este exame vestibular |
| [exam] local | object | objeto com dados do exame vestibular |
| [exam] exam_location | string | endereço da localização do exame vestibular |
| [exam_location] number | string | número da localização do exame vestibular |
| [exam_location] neighborhood | string | bairro da localização do exame vestibular |
| [exam_location] city | string | cidade da localização do exame vestibular |
| [exam_location] state | string | estado da localização do exame vestibular |
| [exam_location] postal_code | string | código postal da localização do exame vestibular |
| [exam] date | string | data da realização do exame vestibular |
| [exam] time | string | hora da realização do exame vestibular |
| [exam] status | string | status do exame vestibular |
| result | string | resultado do exame vestibular |

### Significado dos valores em status
| Nome | Descrição |
| ---- | --------- |
| initiated | Inscrição para exame Pendente |
| pre_registered | Agendamento solicitado para exame vestibular |
| registered | Agendamento confirmado no exame vestibular |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |
| pending_docs | Documentação Pendente |
| submitted_docs | Documentação Enviada |
| rejected_docs | Documentação Rejeitada |
| enrolled | Matriculado |
| dropped_out | Desistente |
| dropping_out | Desistindo |
| drop_out_confirmed | Desistência confirmada |

### Significado dos valores em result
| Nome | Descrição |
| ---- | --------- |
| null | Inscrição para exame Pendente |
| failed | Reprovado no exame vestibular |
| approved | Aprovado no exame vestibular |

# Exames em lote

## Listar todos os exames em lote

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/exam_groups
```

> Resposta

```json
{
  "items": [
    {
      "id": 1234,
      "course_skus": [
        "ADM-MANHA-SP",
        "DIR-MANHA-SP",
        "ADM-NOITE-RJ"
      ],
      "exam_locations": [
        {
          "address": "Rua Márcia",
          "number": "4231",
          "neighborhood": "Morro do Barreto",
          "city": "São Roque",
          "state": "SP",
          "postal_code": "19110-000"
        }
      ],
      "dates": [
        "2016-11-01", "2016-11-01",
        "2016-11-01", "2016-11-01"
      ],
      "times": [ "18:30", "19:30"],
      "status": "active",
      "kind": "scheduled"
    }
  ],
  "next_page": "12345",
  "previous_page": null
}
```

Retorna todos os exames em lote.

Exames em lote são retornados em lote de 10, ordenadas pela última atualização realizada. Se houver mais resultados, `next_page` retorna preenchido para ser utilizado como parâmetro na próxima requisição para continuar. O parâmetro `previous_page` será preenchido com o primeiro id quando utilizado `next_page`, que serve para pegar as informações da página anterior.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| `next_page` | Query | valor utilizado para continuar uma paginação anterior |
| `previous_page` | Query | valor utilizado para retornar na paginação anterior |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do lote de exames |
| course_skus | array | cursos que usam como entrada algum exame desse lote |
| exam_locations | array de objects | lista dos objetos dos endereços dos exames desse lote |
| [exam_locations] address | string | endereço onde será feito o exame vestibular |
| [exam_locations] number | string | número de onde será feito o exame vestibular |
| [exam_locations] neighborhood | string | bairro onde será feito o exame vestibular |
| [exam_locations] city | string | cidade onde será feito o exame vestibular |
| [exam_locations] state | string | estado onde será feito o exame vestibular |
| [exam_locations] postal_code | string | código postal de onde será feito o exame vestibular |
| dates | array | lista das datas dos exames |
| times | array | lista dos horários de aplicações dos exames |
| status | string | situação dos exames entre `active` e `inactive` |
| kind | string | tipo dos exames desse lote |
| next_page | string | código para pegar os próximos passos |
| previous_page | string | código para pegar os passos anteriores |

### Significado dos status

| Status | Descrição |
| ------ | --------- |
| active | Todas as combinações de exame nesse lote estão ativas para serem escolhidas |
| inactive | Nenhuma combinação de exame nesse lote está ativa para ser escolhida |

## Informações de um único lote de exames

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/exam_groups/1234
```

> Resposta

```json
{
  "id": 1234,
  "course_skus": [
    "ADM-MANHA-SP",
    "DIR-MANHA-SP",
    "ADM-NOITE-RJ"
  ],
  "exam_locations": [
    {
      "address": "Rua Márcia",
      "number": "4231",
      "neighborhood": "Morro do Barreto",
      "city": "São Roque",
      "state": "SP",
      "postal_code": "19110-000"
    }
  ],
  "dates": [
    "2016-11-01", "2016-11-01",
    "2016-11-01", "2016-11-01"
  ],
  "times": [ "18:30", "19:30"],
  "status": "active",
  "kind": "scheduled"
}
```

> Resposta quando não encontra nenhum lote de exames

```json
{
  "error": true,
  "message": "ID not found",
  "description": "Could not find any exam group by given ID."
}
```
Retorna um lote de exames específico.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | path | id do lote de exames |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do lote de exames |
| course_skus | array | cursos que usam como entrada algum exame desse lote |
| exam_locations | array de objects | lista dos objetos dos endereços dos exames desse lote |
| [exam_locations] address | string | endereço onde será feito o exame vestibular |
| [exam_locations] number | string | número de onde será feito o exame vestibular |
| [exam_locations] neighborhood | string | bairro onde será feito o exame vestibular |
| [exam_locations] city | string | cidade onde será feito o exame vestibular |
| [exam_locations] state | string | estado onde será feito o exame vestibular |
| [exam_locations] postal_code | string | código postal de onde será feito o exame vestibular |
| dates | array | lista das datas dos exames |
| times | array | lista dos horários de aplicações dos exames |
| status | string | situação dos exames entre `active` e `inactive` |
| kind | string | tipo dos exames desse lote |

### Significado dos status

| Status | Descrição |
| ------ | --------- |
| active | Todas as combinações de exame nesse lote estão ativas para serem escolhidas |
| inactive | Nenhuma combinação de exame nesse lote está ativa para ser escolhida |

## Criação de exames em lote

> Requisição

```bash
curl -X POST --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/exam_groups \
  --file "data.json"
```

> estrutura de data.json

```json
{
  "course_skus": [
    "ADM-MANHA-SP",
    "DIR-MANHA-SP",
    "ADM-NOITE-RJ"
  ],
  "exam_locations": [
    {
      "address": "Rua Márcia",
      "number": "4231",
      "neighborhood": "Morro do Barreto",
      "city": "São Roque",
      "state": "SP",
      "postal_code": "19110-000"
    }
  ],
  "dates": [
    "2016-11-01", "2016-11-01",
    "2016-11-01", "2016-11-01"
  ],
  "times": [ "18:30", "19:30" ],
  "status": "active",
  "kind": "scheduled"
}
```

> Retorno quando parâmetros estão incorretos

```json
{
  "error": true,
  "message": "Invalid parameters",
  "description": "Could not validate json file. Make sure you are following the keys and values as described by the API manual on the link http://docs.queroalunos.com/#criacao-de-exames-em-lote"
}
```

Cria um lote de exames.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| *.json | json | arquivo json com as informações do lote de exames |

### Informações dos argumentos

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| course_skus | array | cursos que usam como entrada algum exame desse lote |
| exam_locations | array de objects | lista dos objetos dos endereços dos exames desse lote |
| [exam_locations] address | string | endereço onde será feito o exame vestibular |
| [exam_locations] number | string | número de onde será feito o exame vestibular |
| [exam_locations] neighborhood | string | bairro onde será feito o exame vestibular |
| [exam_locations] city | string | cidade onde será feito o exame vestibular |
| [exam_locations] state | string | estado onde será feito o exame vestibular |
| [exam_locations] postal_code | string | código postal de onde será feito o exame vestibular |
| dates | array | lista das datas dos exames |
| times | array | lista dos horários de aplicações dos exames |
| status | string | situação dos exames entre `active` e `inactive` |
| kind | string | tipo dos exames desse lote |

### Significado dos status

| Status | Descrição |
| ------ | --------- |
| active | Todas as combinações de exame nesse lote estão ativas para serem escolhidas |
| inactive | Nenhuma combinação de exame nesse lote está ativa para ser escolhida |

> Resposta

```json
{
  "id": 123,
  "course_skus": [
    "ADM-MANHA-SP",
    "DIR-MANHA-SP",
    "ADM-NOITE-RJ"
  ],
  "exam_locations": [
    {
      "address": "Rua Márcia",
      "number": "4231",
      "neighborhood": "Morro do Barreto",
      "city": "São Roque",
      "state": "SP",
      "postal_code": "19110-000"
    }
  ],
  "dates": [
    "2016-11-01", "2016-11-01",
    "2016-11-01", "2016-11-01"
  ],
  "times": [ "18:30", "19:30"],
  "status": "active",
  "kind": "scheduled"
}
```

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do lote de exames |
| course_skus | array | cursos que usam como entrada algum exame desse lote |
| exam_locations | array de objects | lista dos objetos dos endereços dos exames desse lote |
| [exam_locations] address | string | endereço onde será feito o exame vestibular |
| [exam_locations] number | string | número de onde será feito o exame vestibular |
| [exam_locations] neighborhood | string | bairro onde será feito o exame vestibular |
| [exam_locations] city | string | cidade onde será feito o exame vestibular |
| [exam_locations] state | string | estado onde será feito o exame vestibular |
| [exam_locations] postal_code | string | código postal de onde será feito o exame vestibular |
| dates | array | lista das datas dos exames |
| times | array | lista dos horários de aplicações dos exames |
| status | string | situação dos exames entre `active` e `inactive` |
| kind | string | tipo dos exames desse lote |


## Suspender ou ativar lote de exames

> Requisição

```bash
curl -X PUT --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/exam_groups/1234 \
  --data '{"status": "active"}'
```

> Resposta

```json
{
  "id": 1234,
  "course_skus": [
    "ADM-MANHA-SP",
    "DIR-MANHA-SP",
    "ADM-NOITE-RJ"
  ],
  "exam_locations": [
    {
      "address": "Rua Márcia",
      "number": "4231",
      "neighborhood": "Morro do Barreto",
      "city": "São Roque",
      "state": "SP",
      "postal_code": "19110-000"
    }
  ],
  "dates": [
    "2016-11-01", "2016-11-01",
    "2016-11-01", "2016-11-01"
  ],
  "times": [ "18:30", "19:30"],
  "status": "active",
  "kind": "scheduled"
}
```

> Retorno quando parâmetros estão incorretos

```json
{
  "error": true,
  "message": "Invalid parameters",
  "description": "Unknown status. Please check the API manual at http://docs.queroalunos.com/#suspender-ou-ativar-lote-de-exames"
}
```

> Retorno quando não encontra o grupo de exame

```json
{
  "error": true,
  "message": "ID not found",
  "description": "Could not find any exam group by given ID."
}
```

Suspende ou ativa um lote de exames.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | path | id do grupo de exame vestibular |
| status | string | status a ser alterado |

### Possíveis valores para o parâmetro status

| Nome | Descrição |
| ---- | --------- |
| active | status ativado |
| inactive | status inativo |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do lote de exames |
| course_skus | array | cursos que usam como entrada algum exame desse lote |
| exam_locations | array de objects | lista dos objetos dos endereços dos exames desse lote |
| [exam_locations] address | string | endereço onde será feito o exame vestibular |
| [exam_locations] number | string | número de onde será feito o exame vestibular |
| [exam_locations] neighborhood | string | bairro onde será feito o exame vestibular |
| [exam_locations] city | string | cidade onde será feito o exame vestibular |
| [exam_locations] state | string | estado onde será feito o exame vestibular |
| [exam_locations] postal_code | string | código postal de onde será feito o exame vestibular |
| dates | array | lista das datas dos exames |
| times | array | lista dos horários de aplicações dos exames |
| status | string | situação dos exames entre `active` e `inactive` |
| kind | string | tipo dos exames desse lote |

### Significado do retorno de status

| Status | Descrição |
| ------ | --------- |
| active | Todas as combinações de exame nesse lote estão ativas para serem escolhidas |
| inactive | Nenhuma combinação de exame nesse lote está ativa para ser escolhida |

# Informações de exames

## Listar todos os exames

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/exams
```

> Resposta

```json
{
  "items": [
    {
      "id": 456,
      "exam_group_id": 123,
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
      "date": "2016-11-01",
      "time": "18:30",
      "status": "active"
    }
  ],
  "next_page": "12345",
  "previous_page": null
  }
```

Retorna todos os exames (ativos e suspensos).

Exames são retornados em lote de 10, ordenadas pela última atualização realizada. Se houver mais resultados, `next_page` retorna preenchido para ser utilizado como parâmetro na próxima requisição para continuar. O parâmetro `previous_page` será preenchido com o primeiro id quando utilizado `next_page`, que serve para pegar as informações da página anterior.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| `next_page` | Query | valor utilizado para continuar uma paginação anterior |
| `previous_page` | Query | valor utilizado para retornar na paginação anterior |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| items | array | lista de objetos dos exames |
| id | number | id do exame vestibular |
| exam_group_id | number | id referente ao grupo de exame pertencente |
| course_skus | array | cursos que usam como entrada esse exame vestibular |
| exam_location | object | objeto do endereço onde será feito o exame vestibular |
| [exam_location] address | string | endereço onde será feito o exame vestibular |
| [exam_location] number | string | número de onde será feito o exame vestibular |
| [exam_location] neighborhood | string | bairro onde será feito o exame vestibular |
| [exam_location] city | string | cidade onde será feito o exame vestibular |
| [exam_location] state | string | estado onde será feito o exame vestibular |
| [exam_location] postal_code | string | código postal de onde será feito o exame vestibular |
| date | string | data do exame vestibular |
| time | string | horário do exame vestibular |
| status | string | situação do exame entre `active` e `inactive` |
| next_page | string | código para pegar os próximos passos |
| previous_page | string | código para pegar os passos anteriores |

## Informações de um único exame

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/exams/456
```

> Resposta

```json
{
  "id": 456,
  "exam_group_id": 1234,
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
  "date": "2016-11-01",
  "time": "18:30",
  "status": "active"
}
```

> Resposta quando não encontra nenhum exame

```json
{
  "error": true,
  "message": "ID not found",
  "description": "Could not find any exam by given ID."
}
```

Retorna um exame específico.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | path | id do exame vestibular |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do exame vestibular |
| exam_group_id | number | id referente ao grupo de exame pertencente |
| course_skus | array | cursos que usam como entrada esse exame vestibular |
| exam_location | object | objeto do endereço onde será feito o exame vestibular |
| [exam_location] address | string | endereço onde será feito o exame vestibular |
| [exam_location] number | string | número de onde será feito o exame vestibular |
| [exam_location] neighborhood | string | bairro onde será feito o exame vestibular |
| [exam_location] city | string | cidade onde será feito o exame vestibular |
| [exam_location] state | string | estado onde será feito o exame vestibular |
| [exam_location] postal_code | string | código postal de onde será feito o exame vestibular |
| date | string | data do exame vestibular |
| time | string | horário do exame vestibular |
| status | string | situação do exame entre `active` e `inactive` |

## Criação de exame

> Requisição

```bash
curl -X POST --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/exams \
  --file "data.json"
```

> estrutura de data.json

```json
{
  "course_skus": [ "ADM-MANHA-SP" ],
  "exam_location": [
    {
      "address": "Rua Márcia",
      "number": "4231",
      "neighborhood": "Morro do Barreto",
      "city": "São Roque",
      "state": "SP",
      "postal_code": "19110-000"
    }
  ],
  "date": "2016-11-01",
  "time": "18:30",
  "status": "active",
  "kind": "scheduled"
}
```

> Retorno quando parâmetros estão incorretos

```json
{
  "error": true,
  "message": "Invalid parameters",
  "description": "Could not validate json file. Make sure you are following the keys and values as described by the API manual on the link http://docs.queroalunos.com/#criacao-de-exame"
}
```

Cria um novo exame.

### Informações dos argumentos

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| course_skus | array | cursos que usam como entrada algum exame desse lote |
| exam_location | array de objects | lista dos objetos dos endereços dos exames desse lote |
| [exam_location] address | string | endereço onde será feito o exame vestibular |
| [exam_location] number | string | número de onde será feito o exame vestibular |
| [exam_location] neighborhood | string | bairro onde será feito o exame vestibular |
| [exam_location] city | string | cidade onde será feito o exame vestibular |
| [exam_location] state | string | estado onde será feito o exame vestibular |
| [exam_location] postal_code | string | código postal de onde será feito o exame vestibular |
| date | string | data do exame vestibular |
| time | string | horário do exame vestibular |
| status | string | situação dos exames entre `active` ou `inactive` |
| kind | string | tipo dos exames desse lote |

> Resposta

```json
{
  "id": 456,
  "exam_group_id": 1234,
  "course_skus": [ "ADM-MANHA-SP" ],
  "exam_location": [
    {
      "address": "Rua Márcia",
      "number": "4231",
      "neighborhood": "Morro do Barreto",
      "city": "São Roque",
      "state": "SP",
      "postal_code": "19110-000"
    }
  ],
  "date": "2016-11-01",
  "time": "18:30",
  "status": "active",
  "kind": "scheduled"
}
```

### Informações da resposta

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do lote de exames |
| exam_group_id | number | id referente ao grupo de exame pertencente |
| course_skus | array | cursos que usam como entrada algum exame desse lote |
| exam_location | array de objects | lista dos objetos dos endereços dos exames desse lote |
| [exam_location] address | string | endereço onde será feito o exame vestibular |
| [exam_location] number | string | número de onde será feito o exame vestibular |
| [exam_location] neighborhood | string | bairro onde será feito o exame vestibular |
| [exam_location] city | string | cidade onde será feito o exame vestibular |
| [exam_location] state | string | estado onde será feito o exame vestibular |
| [exam_location] postal_code | string | código postal de onde será feito o exame vestibular |
| date | string | data do exame vestibular |
| time | string | horário do exame vestibular |
| status | string | situação dos exames entre `active` ou `inactive` |
| kind | string | tipo dos exames desse lote |

### Significado dos status

| Status | Descrição |
| ------ | --------- |
| active | Todas as combinações de exame nesse lote estão ativas para serem escolhidas |
| inactive | Nenhuma combinação de exame nesse lote está ativa para ser escolhida |

## Suspender ou ativar um exame

> Requisição

```bash
curl -X PUT --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/exams/456 \
  --data '{"status":"active"}'
```

> Resposta

```json
{
  "id": 456,
  "exam_group_id": 123,
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
  "date": "2016-11-01",
  "time": "18:30",
  "status": "active"
}
```

> Retorno quando parâmetros estão incorretos

```json
{
  "error": true,
  "message": "Invalid parameters",
  "description": "Unknown status. Please check the API manual at http://docs.queroalunos.com/#suspender-ou-ativar-um-exame"
}
```

> Resposta quando não encontra nenhum exame

```json
{
  "error": true,
  "message": "ID not found",
  "description": "Could not find any exam by given ID."
}
```

Suspende ou ativa um exame.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | path | id do exame vestibular |
| status | string | status a ser alterado |

### Possíveis valores para o parâmetro status

| Nome | Descrição |
| ---- | --------- |
| active | status ativado |
| inactive | status inativo |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do exame vestibular |
| exam_group_id | number | id referente ao grupo de exame pertencente |
| course_skus | array | cursos que usam como entrada esse exame vestibular |
| exam_location | object | objeto do endereço onde será feito o exame vestibular |
| [exam_location] address | string | endereço onde será feito o exame vestibular |
| [exam_location] number | string | número de onde será feito o exame vestibular |
| [exam_location] neighborhood | string | bairro onde será feito o exame vestibular |
| [exam_location] city | string | cidade onde será feito o exame vestibular |
| [exam_location] state | string | estado onde será feito o exame vestibular |
| [exam_location] postal_code | string | código postal de onde será feito o exame vestibular |
| date | string | data do exame vestibular |
| time | string | horário do exame vestibular |
| status | string | situação do exame entre `active` e `inactive` |

# Exame de Enem

## Listar todos os exames de Enem

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/enem_exams
```

> Resposta

```json
{
  "items": [
    {
      "id": 789,
      "course_skus": [
        "ADM-MANHA-SP",
        "DIR-MANHA-SP",
        "ADM-NOITE-RJ"
      ],
      "minimum_grades": {
        "redacao": 600.0,
        "humanas": 600.0,
        "natureza": 600.0,
        "linguagens": 600.0,
        "matematica": 600.0
      },
      "status": "active"
    }
  ],
  "next_page": "12345",
  "previous_page": null
  }
```

Retorna todos os exames de enem.

Exames de ENEM são retornados em lote de 10, ordenadas pela última atualização realizada. Se houver mais resultados, `next_page` retorna preenchido para ser utilizado como parâmetro na próxima requisição para continuar. O parâmetro `previous_page` será preenchido com o primeiro id quando utilizado `next_page`, que serve para pegar as informações da pagina anterior.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| `next_page` | Query | valor utilizado para continuar uma paginação anterior |
| `previous_page` | Query | valor utilizado para retornar na paginação anterior |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do exame de enem |
| course_skus | array | cursos que usam como entrada esse exame de enem |
| minimum_grades | object | objeto com informações de nota mínima exigida pela faculdade |
| [minimum_grades] redacao | float | nota mínima de redação exigida pela faculdade |
| [minimum_grades] humanas | float | nota mínima de humanas exigida pela faculdade |
| [minimum_grades] natureza | float | nota mínima de natureza exigida pela faculdade |
| [minimum_grades] linguagens | float | nota mínima de linguagens exigida pela faculdade |
| [minimum_grades] matematica | float | nota mínima de matemática exigida pela faculdade |
| status | string | situação do exame de enem entre `active` e `inactive` |
| next_page | string | código para pegar os próximos passos |
| previous_page | string | código para pegar os passos anteriores |

## Informações de um único exame de Enem

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/enem_exams/789
```

> Resposta

```json
{
  "id": 789,
  "course_skus": [
    "ADM-MANHA-SP",
    "DIR-MANHA-SP",
    "ADM-NOITE-RJ"
  ],
  "minimum_grades": {
    "redacao": 600.0,
    "humanas": 600.0,
    "natureza": 600.0,
    "linguagens": 600.0,
    "matematica": 600.0
  },
  "status": "active"
}
```

> Retorno quando não encontra o exame de enem

```json
{
  "error": true,
  "message": "ID not found",
  "description": "Could not find any enem exam by given ID."
}
```

Retorna um exame de enem específico.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do exame de enem |

### Informações de resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do exame de enem |
| course_skus | array | cursos que usam como entrada esse exame de enem |
| minimum_grades | object | objeto com informações de nota mínima exigida pela faculdade |
| [minimum_grades] redacao | float | nota mínima de redação exigida pela faculdade |
| [minimum_grades] humanas | float | nota mínima de humanas exigida pela faculdade |
| [minimum_grades] natureza | float | nota mínima de natureza exigida pela faculdade |
| [minimum_grades] linguagens | float | nota mínima de linguagens exigida pela faculdade |
| [minimum_grades] matematica | float | nota mínima de matemática exigida pela faculdade |
| status | string | situação do exame de enem entre `active` e `inactive` |

## Criação de exame de Enem

> Requisição

```bash
curl -X POST --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/enem_exams \
  --file "data.json"
```

> estrutura de data.json

```json
{
  "course_skus": [
    "ADM-MANHA-SP",
    "DIR-MANHA-SP",
    "ADM-NOITE-RJ"
  ],
  "minimum_grades": {
    "redacao": 600.0,
    "humanas": 600.0,
    "natureza": 600.0,
    "linguagens": 600.0,
    "matematica": 600.0
  },
  "status": "active"
}
```

> Retorno quando parâmetros estão incorretos

```json
{
  "error": true,
  "message": "Invalid parameters",
  "description": "Could not validate json file. Make sure you are following the keys and values as described by the API manual on the link http://docs.queroalunos.com/#criacao-de-exame-de-enem"
}
```

Cria um exame de enem.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| *.json | json | arquivo json com as informações do exame de enem |

### Informações dos argumentos

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| course_skus | array | cursos que usam como entrada esse exame de enem |
| minimum_grades | object | objeto com informações de nota mínima exigida pela faculdade |
| [minimum_grades] redacao | float | nota mínima de redação exigida pela faculdade |
| [minimum_grades] humanas | float | nota mínima de humanas exigida pela faculdade |
| [minimum_grades] natureza | float | nota mínima de natureza exigida pela faculdade |
| [minimum_grades] linguagens | float | nota mínima de linguagens exigida pela faculdade |
| [minimum_grades] matematica | float | nota mínima de matemática exigida pela faculdade |
| status | string | situação do exame de enem entre `active` e `inactive` |

> Resposta

```json
{
  "id": 789,
  "course_skus": [
    "ADM-MANHA-SP",
    "DIR-MANHA-SP",
    "ADM-NOITE-RJ"
  ],
  "minimum_grades": {
    "redacao": 600.0,
    "humanas": 600.0,
    "natureza": 600.0,
    "linguagens": 600.0,
    "matematica": 600.0
  },
  "status": "active"
}
```

### Informações da resposta

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do exame de enem |
| course_skus | array | cursos que usam como entrada esse exame de enem |
| minimum_grades | object | objeto com informações de nota mínima exigida pela faculdade |
| [minimum_grades] redacao | float | nota mínima de redação exigida pela faculdade |
| [minimum_grades] humanas | float | nota mínima de humanas exigida pela faculdade |
| [minimum_grades] natureza | float | nota mínima de natureza exigida pela faculdade |
| [minimum_grades] linguagens | float | nota mínima de linguagens exigida pela faculdade |
| [minimum_grades] matematica | float | nota mínima de matemática exigida pela faculdade |
| status | string | situação do exame de enem entre `active` e `inactive` |

## Suspender um exame de Enem inteiro

> Requisição

```bash
curl -X PUT --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/enem_exams/789 \
  --data '{"status":"inactive"}'
```

> Resposta

```json
{
  "id": 789,
  "course_skus": [
    "ADM-MANHA-SP",
    "DIR-MANHA-SP",
    "ADM-NOITE-RJ"
  ],
  "minimum_grades": {
    "redacao": 600.0,
    "humanas": 600.0,
    "natureza": 600.0,
    "linguagens": 600.0,
    "matematica": 600.0
  },
  "status": "inactive"
}
```

> Retorno quando parâmetros estão incorretos

```json
{
  "error": true,
  "message": "Invalid parameters",
  "description": "Unknown status. Please check the API manual at http://docs.queroalunos.com/#suspender-um-exame-de-enem-inteiro"
}
```

> Retorno quando não encontra o exame de enem

```json
{
  "error": true,
  "message": "ID not found",
  "description": "Could not find any enem exam by given ID."
}
```

Suspende um exame de Enem específico.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do exame de enem |
| status | string | situação do exame de enem |

### Informações do resultado

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| id | number | id do exame de enem |
| course_skus | array | cursos que usam como entrada esse exame de enem |
| minimum_grades | object | objeto com informações de nota mínima exigida pela faculdade |
| [minimum_grades] redacao | float | nota mínima de redação exigida pela faculdade |
| [minimum_grades] humanas | float | nota mínima de humanas exigida pela faculdade |
| [minimum_grades] natureza | float | nota mínima de natureza exigida pela faculdade |
| [minimum_grades] linguagens | float | nota mínima de linguagens exigida pela faculdade |
| [minimum_grades] matematica | float | nota mínima de matemática exigida pela faculdade |
| status | string | situação do exame de enem entre `active` e `inactive` |

## Suspender um exame de Enem parcialmente

> Requisição

```bash
curl --header "Authorization: Token ########" --header "Content-Type: application/json" https://queroalunos.com/api/v1/enem_exams/suspend \
  --file "data.json"
```

> estrutura de data.json

```json
{
  "course_skus": [
    "ADM-MANHA-SP",
    "DIR-MANHA-SP"
  ]
}
```

> Retorno quando parâmetros estão incorretos

```json
{
  "error": true,
  "message": "Invalid parameters",
  "description": "Could not validate json file. Make sure you are following the keys and values as described by the API manual on the link http://docs.queroalunos.com/#suspender-um-exame-de-enem-parcialmente"
}
```

Suspende um exame de Enem específico.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| *.json | json | id do exame de enem |

### Informações dos argumentos

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| course_skus | array | cursos que usam como entrada esse exame de enem |

# Notificações

Notificações utilizam uma rota única de callback, que deve ser fornecida pela faculdade, de um token para autenticação via HTTP Basic.

A rota deve aceitar JSON.

A rota deve aceitar apenas POSTs.

## Definição base do evento

Estrutura base das notificações:

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| created | string | data que foi criado o evento (timestamp UTC)
| api_version | string | informação da versão atual da API
| event_type | string | tipo de evento |
| data | object | Objeto com informações de acordo com o tipo de evento |

## Notificar novo processo de admissão

```json
{
  "event_type": "NewAdmission",
  "created": "2017-12-15T17:34:26.173",
  "api_version": "1.0.0",
  "data": {
    "admission": {
      "id": 12345,
      "course_sku": "ADM-MANHA-SP",
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

Esta notificação informando uma novo aluno para o processo de admissão na universidade.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| created | string | data que foi criado o evento (timestamp UTC)
| api_version | string | informação da versão atual da API
| event_type | string | tipo de evento, no caso `NewAdmission` |
| data | object | objeto com informações de acordo com o tipo de evento |
| admission | object | objeto com dados do processo de admissão do aluno |
| [admission] id | number | id do processo de admissão |
| [admission] course_sku | string | código do curso referente a essa matrícula |
| [admission] status | string | status da admissão do aluno |
| [admission] student | object | objeto com dados do aluno |
| [student] id | number | id do aluno |
| [student] name | string | nome do aluno |
| [student] cpf | string | cpf do aluno |
| [student] birth_date | string | data de nascimento do aluno |
| [student] emails | array de string | lista de emails do aluno |
| [student] phones | array de string | lista de telefones do aluno |
| [student] address_information | object | objeto com dados onde aluno reside |
| [address_information] address | string | endereço onde aluno reside |
| [address_information] number | string | número onde aluno reside |
| [address_information] neighborhood | string | bairro onde aluno reside |
| [address_information] city | string | cidade onde aluno reside |
| [address_information] state | string | estado onde aluno reside |
| [address_information] postal_code | string | código postal onde aluno reside |

### Significado dos valores em status

| Nome | Descrição |
| ---- | --------- |
| initiated | Inscrição para exame Pendente |
| pending_docs | Documentação Pendente |

## Nova "inscrição para exame" (Vestibular)

```json
{
  "event_type": "NewApplication",
  "created": "2017-12-15T17:34:26.173",
  "api_version": "1.0.0",
  "data": {
    "application": {
      "id": 123456,
      "type": "exam",
      "admission": {
        "id": 12345,
        "course_sku": "ADM-MANHA-SP",
        "status": "pre-registered",
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
          "address_information": {
            "address": "Rua Sandra",
            "number": "432S",
            "neighborhood": "Chácara Dora",
            "city": "Araçariguama",
            "state": "SP",
            "postal_code": "18147-000"
          }
        },
      },
      "exam": {
        "id": 456,
        "exam_group_id": 123,
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
        "date": "2016-11-01",
        "time": "18:30",
        "status": "active"
      },
      "result": null
    }
  }
}
```

Esta notificação informando uma nova inscrição para exame via vestibular.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| created | string | data que foi criado o evento (timestamp UTC)
| api_version | string | informação da versão atual da API
| event_type | string | tipo de evento, no caso `NewApplication` |
| data | object | objeto com informações de acordo com o tipo de evento |
| application | object | objeto com informações do vestibular |
| [application] id | number | id da inscrição para exame |
| [application] type | string | tipo de exame vestibular (_neste evento o valor sempre será `exam`_) |
| [application] admission | object | objeto com dados do processo de admissão do aluno |
| [admission] id | number | id do processo de admissão |
| [admission] course_sku | string | código do curso referente a essa matrícula |
| [admission] status | string | status da admissão do aluno (_neste evento o valor sempre será `pre-registered`_) |
| [admission] student | object | objeto com dados do aluno |
| [student] id | number | id do aluno |
| [student] name | string | nome do aluno |
| [student] cpf | string | cpf do aluno |
| [student] birth_date | string | data de nascimento do aluno |
| [student] emails | array de string | lista de emails do aluno |
| [student] phones | array de string | lista de telefones do aluno |
| [student] address_information | object | objeto com dados onde aluno reside |
| [address_information] address | string | endereço onde aluno reside |
| [address_information] number | string | número onde aluno reside |
| [address_information] neighborhood | string | bairro onde aluno reside |
| [address_information] city | string | cidade onde aluno reside |
| [address_information] state | string | estado onde aluno reside |
| [address_information] postal_code | string | código postal onde aluno reside |
| [application] exam | object | objeto com informações do exame vestibular|
| [exam] id | number | id do exame vestibular |
| [exam] exam_group_id | number | id referente ao grupo de exame pertencente |
| [exam] course_skus | array | lista com os cursos pertencentes a este exame vestibular |
| [exam] exam_location | object | objeto com dados do exame vestibular|
| [exam_location] address | string | endereço da localização do exame vestibular |
| [exam_location] number | string | número da localização do exame vestibular |
| [exam_location] neighborhood | string | bairro da localização do exame vestibular |
| [exam_location] city | string | cidade da localização do exame vestibular |
| [exam_location] state | string | estado da localização do exame vestibular |
| [exam_location] postal_code | string | código postal da localização do exame vestibular |
| [exam] date | string | data da realização do exame vestibular |
| [exam] time | string | hora da realização do exame vestibular |
| [exam] status | string | status do exame vestibular |
| [application] result | string | resultado do exame vestibular (_neste evento o valor sempre será `null`_) |

## Nova "inscrição para exame" (ENEM)

```json
{
  "event_type": "NewApplication",
  "created": "2017-12-15T17:34:26.173",
  "api_version": "1.0.0",
  "data": {
    "application": {
      "id": 123456,
      "type": "enem",
      "admission": {
        "id": 12345,
        "course_sku": "ADM-MANHA-SP",
        "status": "pre-registered",
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
          "address_information": {
            "address": "Rua Sandra",
            "number": "432S",
            "neighborhood": "Chácara Dora",
            "city": "Araçariguama",
            "state": "SP",
            "postal_code": "18147-000"
          }
        },
      },
      "exam_application_info": {
        "grades": {
          "redacao": 600.0,
          "humanas": 600.0,
          "natureza": 600.0,
          "linguagens": 600.0,
          "matematica": 600.0
        }
      },
      "exam": {
        "id": 456,
        "exam_group_id": 123,
        "course_skus": [
          "ADM-MANHA-SP",
          "DIR-MANHA-SP",
          "ADM-NOITE-RJ"
        ],
        "minimum_grades": {
          "redacao": 600.0,
          "humanas": 600.0,
          "natureza": 600.0,
          "linguagens": 600.0,
          "matematica": 600.0
        },
        "status": "active"
      },
      "result": "pre-registered"
    }
  }
}
```

Esta notificação informa uma nova inscrição para exame via ENEM.

### Parâmetros

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| created | string | data que foi criado o evento (timestamp UTC)
| api_version | string | informação da versão atual da API
| event_type | string | tipo de evento, no caso `NewApplication` |
| data | object | objeto com informações de acordo com o tipo de evento |
| application | object | objeto com informações do vestibular |
| [application] id | number | id da inscrição para exame |
| [application] type | string | tipo de exame vestibular (_neste evento o valor sempre será `enem`_) |
| [application] admission | object | objeto com dados do processo de admissão do aluno |
| [admission] id | number | id do processo de admissão |
| [admission] course_sku | string | código do curso referente a essa matrícula |
| [admission] status | string | status da admissão do aluno (_neste evento o valor sempre será `pre-registered`_) |
| [admission] student | object | objeto com dados do aluno |
| [student] id | number | id do aluno |
| [student] name | string | nome do aluno |
| [student] cpf | string | cpf do aluno |
| [student] birth_date | string | data de nascimento do aluno |
| [student] emails | array de string | lista de emails do aluno |
| [student] phones | array de string | lista de telefones do aluno |
| [student] address_information | object | objeto com dados onde aluno reside |
| [address_information] address | string | endereço onde aluno reside |
| [address_information] number | string | número onde aluno reside |
| [address_information] neighborhood | string | bairro onde aluno reside |
| [address_information] city | string | cidade onde aluno reside |
| [address_information] state | string | estado onde aluno reside |
| [address_information] postal_code | string | código postal onde aluno reside |
| [application] exam_application_info | object | objeto com informações da grade de notas obtida pelo aluno no enem |
| [exam_application_info] grades | object | objeto com informações de notas obtida pelo aluno no enem |
| [grades] redacao | float | nota obtida pelo aluno na redação |
| [grades] humanas | float | nota obtida pelo aluno em humanas |
| [grades] natureza | float | nota obtida pelo aluno em natureza |
| [grades] linguagens | float | nota obtida pelo aluno em linguagens |
| [grades] matematica | float | nota obtida pelo aluno em matemática |
| [application] exam | object | objeto com informações do exame vestibular|
| [exam] id | number | id do exame vestibular |
| [exam] exam_group_id | number | id referente ao grupo de exame pertencente |
| [exam] course_skus | array | lista com os cursos pertencentes a este exame vestibular |
| [exam] minimum_grades | object | objeto com informações de nota mínima exigida pela faculdade |
| [minimum_grades] redacao | float | nota mínima de redação exigida pela faculdade |
| [minimum_grades] humanas | float | nota mínima de humanas exigida pela faculdade |
| [minimum_grades] natureza | float | nota mínima de natureza exigida pela faculdade |
| [minimum_grades] linguagens | float | nota mínima de linguagens exigida pela faculdade |
| [minimum_grades] matematica | float | nota mínima de matemática exigida pela faculdade |
| [exam] status | string | status do exame vestibular |
| [application] result | string | resultado do exame vestibular (_neste evento o valor sempre será `null`_) |

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

| Nome | Tipo | Descrição | Formato |
| ---- | ---- | --------- | ------- |
| start_date     | string | Data inicial do filtro | DD/MM/AAAA
| end_date       | string | Data final do filtro | DD/MM/AAAA
| starting_after | string | Cursor para a proxima página
| ending_before  | string | Cursor para a página anterior

### Informações de retorno

| Nome | Tipo | Descrição |
| ---- | ---- | --------- |
| has_more      | boolean          | indica a existência de outras paginas |
| notifications | array de objetos | lista de notificações: [referência](#definicao-base-do-evento)