# Fluxo: Consulta e Apresentação de Perfis de Profissionais da Saúde

> **Convenções:**
> - **[MOCK]** - exemplo ilustrativo; não confirmado nas fontes públicas disponíveis.
> - **[CONFIRMAR]** - requer validação com a equipe da Lacrei Saúde antes da publicação definitiva.

---

## 1. Visão Geral

### Objetivo

Este fluxo descreve como uma pessoa usuária acessa a plataforma Lacrei Saúde e visualiza perfis de profissionais da saúde desde a navegação na interface até a resposta da API renderizada em tela. 

### Público-alvo

Pessoas desenvolvedoras no projeto Lacrei Saúde e contribuidoras open-source que precisam entender como o Front-end se comunica com a API para exibir perfis de profissionais.

### Componentes envolvidos

| Componente | Tecnologia | Responsabilidade |
|---|---|---|
| Interface (Front-end) | Next.js + TypeScript | Renderizar a listagem e os perfis, realizar requisições autenticadas à API, gerenciar o estado de autenticação da usuária. |
| API | Django REST Framework | Expor os endpoints de listagem e detalhe, validar tokens, aplicar regras de negócio e serializar os dados retornados. |
| Banco de dados | PostgreSQL | Armazenar os perfis de profissionais e demais dados estruturados da plataforma. |
| Cache | Amazon ElastiCache (Valkey) | Armazenar em cache respostas frequentes para reduzir a carga no banco de dados. **[CONFIRMAR]** |

---

## 2. Arquitetura

### Diagrama de sequência

```mermaid
sequenceDiagram
    actor U as Usuária
    participant FE as Front-end<br/>(Next.js)
    participant Cache as ElastiCache<br/>(Valkey)
    participant API as lacrei-api<br/>(DRF)
    participant DB as PostgreSQL

    U->>FE: acessa /profissionais
    FE->>API: GET /api/v1/profissionais/ [MOCK]

    alt token ausente ou expirado
        API-->>FE: 401 Unauthorized
        FE-->>U: redireciona para login
    else token válido
        alt cache disponível [CONFIRMAR]
            API->>Cache: verifica cache
            Cache-->>API: retorna dados em cache
        else cache indisponível
            API->>DB: consulta registros
            DB-->>API: retorna dados
            API->>Cache: armazena em cache [CONFIRMAR]
        end
        API-->>FE: 200 OK - JSON
        FE-->>U: renderiza lista de perfis
    end
```

> **[CONFIRMAR]** Rota, versão da API e uso do cache precisam de validação com a equipe de desenvolvimento.

### Descrição do fluxo

1. A usuária acessa a página `/profissionais` no navegador.
2. O Front-end realiza uma requisição `GET` autenticada ao endpoint de listagem da API, podendo incluir filtros via parâmetros de query.
3. A API valida o token de autenticação no cabeçalho `Authorization`. Se inválido ou ausente, retorna `401 Unauthorized` e o fluxo é interrompido.
4. Com o token válido, a API verifica se existe resposta em cache. **[CONFIRMAR]**
5. Se o cache estiver disponível, retorna os dados diretamente. Caso contrário, consulta o PostgreSQL.
6. O banco retorna os registros que atendem aos filtros aplicados.
7. A API serializa os dados em JSON e retorna `200 OK` ao Front-end.
8. O Front-end renderiza a lista de perfis para a usuária.

---

## 3. Contrato da API

> **[MOCK]** As definições abaixo são exemplos ilustrativos baseados nas convenções do Django REST Framework. 

### 3.1 Listar profissionais

| Campo | Valor |
|---|---|
| Método | `GET` |
| Rota | `/api/v1/profissionais/` **[MOCK]** |
| Autenticação | Bearer token (JWT) **[CONFIRMAR]** |

#### Parâmetros de query

| Parâmetro | Tipo | Obrigatório | Descrição |
|---|---|---|---|
| `especialidade` | string | não | Filtra por especialidade. **[MOCK]** |
| `page` | integer | não | Número da página (paginação). **[MOCK]** |

#### Resposta de sucesso - `200 OK`

A resposta segue o padrão de paginação do Django REST Framework. **[MOCK]**

| Campo | Tipo | Descrição |
|---|---|---|
| `count` | integer | Total de registros encontrados. |
| `next` | string \| null | URL da próxima página, ou `null` se não houver. |
| `previous` | string \| null | URL da página anterior, ou `null` se for a primeira. |
| `results` | array | Lista de perfis retornados na página atual. |

Cada objeto dentro de `results`: **[MOCK - confirmar campos reais]**

| Campo | Tipo | Descrição |
|---|---|---|
| `id` | string (UUID) | Identificador único do perfil. **[CONFIRMAR tipo]** |
| `nome_social` | string | Nome social da pessoa profissional. |
| `especialidade` | string | Especialidade ou área de atuação. |
| `aceita_plano` | boolean | Indica se atende por plano de saúde. |
| `atende_online` | boolean | Indica se realiza consultas online. |

#### Códigos de erro

| Código | Situação |
|---|---|
| `400 Bad Request` | Parâmetro de query inválido. |
| `401 Unauthorized` | Token ausente ou expirado. |
| `403 Forbidden` | Token válido, mas sem permissão para o recurso. |
| `500 Internal Server Error` | Erro inesperado no servidor. |

---

## 4. Exemplos Práticos

### 4.1 Listagem com sucesso

**Requisição**

```http
GET /api/v1/profissionais/?especialidade=psicologia 
```

**Resposta - `200 OK`** [MOCK]

```json
{
  "count": 2,
  "next": null,
  "previous": null,
  "results": [
    {
      "id": "3f6a2b1c-d4e5-4f67-89ab-cdef01234567",
      "nome_social": "Dra. Ana Lima",
      "especialidade": "Psicologia",
      "registro_profissional": "CRP 00/00000",
      "aceita_plano": true,
      "atende_online": true
    },
    {
      "id": "7c8d9e0f-1a2b-3c4d-5e6f-789012345678",
      "nome_social": "Dr. Carlos Souza",
      "especialidade": "Psicologia",
      "registro_profissional": "CRP 00/11111",
      "aceita_plano": false,
      "atende_online": true
    }
  ]
}
```

---

### 4.2 Detalhe de perfil com sucesso

**Requisição**

```http
GET /api/v1/profissionais/3f6a2b1c-d4e5-4f67-89ab-cdef01234567/ HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Accept: application/json
```

**Resposta - `200 OK`** [MOCK]

```json
{
  "id": "3f6a2b1c-d4e5-4f67-89ab-cdef01234567",
  "nome_social": "Dra. Ana Lima",
  "especialidade": "Psicologia",
  "registro_profissional": "CRP 00/00000",
  "aceita_plano": true,
  "atende_online": true
}
```

---

### 4.3 Falha de autenticação - `401 Unauthorized`

**Requisição sem token**

```http
GET /api/v1/profissionais/ HTTP/1.1
Accept: application/json
```

**Resposta** [MOCK]

```json
{
  "detail": "As credenciais de autenticação não foram fornecidas."
}
```

---

### 4.4 Parâmetro inválido - `400 Bad Request`

**Requisição com valor inválido no parâmetro `page`**

```http
GET /api/v1/profissionais/?page=abc HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Accept: application/json
```

**Resposta** [MOCK]

```json
{
  "detail": "Número de página inválido."
}
```

---

### 4.5 Recurso não encontrado - `404 Not Found`

**Requisição com `id` inexistente**

```http
GET /api/v1/profissionais/00000000-0000-0000-0000-000000000000/ HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
Accept: application/json
```

**Resposta** [MOCK]

```json
{
  "detail": "Não encontrado."
}
```

---

## 5. Segurança e Privacidade

### Dados pessoais e LGPD

Os dados retornados neste fluxo referem-se ao **perfil público** da pessoa profissional - nome social, especialidade e registro profissional - e não incluem dados clínicos ou de saúde da pessoa usuária. Ainda assim, a LGPD se aplica ao tratamento dessas informações.

A Lei Geral de Proteção de Dados (Lei nº 13.709/2018) classifica **dados de saúde** como dados sensíveis, exigindo atenção redobrada no tratamento.

| Aspecto | Orientação |
|---|---|
| Exposição mínima | Retornar apenas os campos necessários para cada contexto: a listagem expõe menos campos que o detalhe. **[CONFIRMAR campos reais]** |
| Base legal | Os dados são exibidos com base no consentimento dado no cadastro, conforme o Art. 7º, I da LGPD. **[CONFIRMAR fluxo de consentimento]** |
| Retenção | Os dados são mantidos enquanto o perfil estiver ativo na plataforma. **[CONFIRMAR política de retenção]** |

### Autenticação e controle de acesso

- Controle de acesso por perfil: **[CONFIRMAR]**
  - **Usuária paciente:** pode listar e visualizar perfis de profissionais.
  - **Profissional de saúde:** pode listar perfis e gerenciar o próprio cadastro.
  - **Administração:** acesso irrestrito para fins de moderação e suporte.
- Token ausente ou expirado retorna `401 Unauthorized`; token válido sem permissão retorna `403 Forbidden`

---

## 6. Glossário e Referências

### Glossário

| Termo | Definição |
|---|---|
| **API** | Interface de Programação de Aplicações - conjunto de regras que permite a comunicação entre sistemas. |
| **Bearer token** | Credencial de acesso enviada no cabeçalho `Authorization` para autenticar uma requisição HTTP. |
| **DRF** | Django REST Framework - biblioteca Python para construção de APIs REST sobre o Django. |
| **ElastiCache** | Serviço gerenciado da AWS para cache em memória; na Lacrei Saúde, utiliza o motor Valkey. **[CONFIRMAR]** |
| **HTTPS** | Versão segura do protocolo HTTP, com tráfego criptografado via TLS. |
| **HttpOnly** | Atributo de cookie que impede seu acesso via JavaScript, mitigando ataques XSS. |
| **JWT** | JSON Web Token - padrão para transmissão segura de informações entre partes como objeto JSON assinado. |
| **LGPD** | Lei Geral de Proteção de Dados (Lei nº 13.709/2018) - legislação brasileira que regula o tratamento de dados pessoais. |
| **Mock** | Exemplo ilustrativo criado para fins de documentação; não representa o comportamento real confirmado do sistema. |
| **Next.js** | Framework React para aplicações web com suporte a renderização no servidor (SSR) e geração estática (SSG). |
| **PostgreSQL** | Sistema de gerenciamento de banco de dados relacional de código aberto utilizado no Back-end da Lacrei Saúde. |
| **SSR** | Server-Side Rendering - técnica em que o HTML da página é gerado no servidor a cada requisição. |
| **UUID** | Universally Unique Identifier - identificador único de 128 bits no formato `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. |
| **Valkey** | Motor de cache em memória utilizado pelo Amazon ElastiCache na Lacrei Saúde. **[CONFIRMAR]** |

### Fontes consultadas

- [Django REST Framework - documentação oficial](https://www.django-rest-framework.org/)
- [Next.js - documentação oficial](https://nextjs.org/docs)
- [TypeScript - documentação oficial](https://www.typescriptlang.org/docs/)
- [Mermaid - documentação oficial](https://mermaid.js.org/intro/)
- [LGPD - Lei nº 13.709/2018](https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm)

### Pontos pendentes de validação

Os itens abaixo precisam ser confirmados com a equipe de desenvolvimento da Lacrei Saúde antes da publicação definitiva:

- [ ] Rota exata e versão da API para listagem e detalhe de profissionais.
- [ ] Campos retornados em cada endpoint (esquema completo de resposta).
- [ ] Mecanismo de autenticação em uso (JWT, sessão, OAuth).
- [ ] Regras de autorização - quais perfis podem acessar quais recursos.
- [ ] Uso e configuração do Amazon ElastiCache (Valkey) neste fluxo.
- [ ] Política de retenção de dados e fluxo de consentimento (LGPD).
- [ ] URLs dos repositórios públicos (`lacrei-api`, `lacrei-models`, `lacrei-migrations`).
