# Desafio Técnico — Technical Writer na Lacrei Saúde

Documentação técnica produzida como parte do processo seletivo para a vaga de Technical Writer (voluntariado) na [Lacrei Saúde](https://lacreisaude.com.br).

---

## Sobre a Lacrei Saúde

A Lacrei Saúde é uma health tech de impacto social fundada em 2020 com a missão de conectar a comunidade LGBTQIAPN+ a profissionais da saúde de forma segura, inclusiva e acolhedora.

---

## Sobre este repositório

Este repositório contém a documentação técnica do fluxo de **consulta e apresentação de perfis de profissionais da saúde** — o caminho percorrido desde o acesso da usuária na interface até a exibição dos resultados retornados pela API.

### Estrutura

```
lacrei-tech-challenge/
├── README.md                          ← este arquivo
└── docs/
    └── fluxo-perfil-profissionais.md  ← documentação principal
```

---

## Como navegar pelo documento

| Seção | O que você encontra |
|---|---|
| [1. Visão geral](docs/fluxo-perfil-profissionais.md#1-visão-geral) | Objetivo do fluxo, público-alvo e responsabilidade de cada componente |
| [2. Arquitetura](docs/fluxo-perfil-profissionais.md#2-arquitetura) | Diagrama de sequência e descrição passo a passo do fluxo |
| [3. Contrato da API](docs/fluxo-perfil-profissionais.md#3-contrato-da-api) | Endpoints, parâmetros, schemas de resposta e códigos de erro |
| [4. Exemplos práticos](docs/fluxo-perfil-profissionais.md#4-exemplos-práticos) | Requisições e respostas reais em HTTP e JSON |
| [5. Segurança e privacidade](docs/fluxo-perfil-profissionais.md#5-segurança-e-privacidade) | LGPD, autenticação, controle de acesso e boas práticas |
| [6. Glossário e referências](docs/fluxo-perfil-profissionais.md#6-glossário-e-referências) | Termos técnicos, fontes consultadas e pontos pendentes de validação |

---

## Convenções

| Marcador | Significado |
|---|---|
| **[MOCK]** | Exemplo ilustrativo não confirmado nas fontes públicas disponíveis. |
| **[CONFIRMAR]** | Informação que requer validação com a equipe da Lacrei Saúde antes da publicação definitiva. |

Como o acesso aos repositórios internos não estava disponível durante a elaboração deste documento, os contratos de API, rotas e campos de resposta foram construídos com base nas convenções do Django REST Framework e identificados como `[MOCK]`. Os pontos que precisam de validação estão listados na [seção de referências](docs/fluxo-perfil-profissionais.md#pontos-pendentes-de-validação).

---

## Visualização dos diagramas

Os diagramas foram criados com [Mermaid](https://mermaid.js.org) e são renderizados automaticamente pelo GitHub. Caso prefira visualizar localmente, utilize o editor online em [mermaid.live](https://mermaid.live).
