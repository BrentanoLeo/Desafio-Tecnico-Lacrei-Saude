# Desafio Técnico - Technical Writer na Lacrei Saúde

Documentação técnica produzida como parte do processo seletivo para a vaga de Technical Writer (voluntariado) na [Lacrei Saúde](https://lacreisaude.com.br).

---

## Sobre a Lacrei Saúde

A Lacrei Saúde é uma health tech de impacto social fundada em 2020 com a missão de conectar a comunidade LGBTQIAPN+ a profissionais da saúde de forma segura, inclusiva e acolhedora.

---

## Sobre este repositório

Este repositório contém a documentação técnica do fluxo de **consulta e apresentação de perfis de profissionais da saúde** - o caminho percorrido desde o acesso da usuária na interface até a exibição dos resultados retornados pela API.

### Estrutura

```
lacrei-tech-challenge/
├── README.md                          ← este arquivo
└── docs/
    └── fluxo-perfil-profissionais.md  ← documentação principal
```

---

## Convenções

| Marcador | Significado |
|---|---|
| **[MOCK]** | Exemplo ilustrativo não confirmado nas fontes públicas disponíveis. |
| **[CONFIRMAR]** | Informação que requer validação com a equipe da Lacrei Saúde antes da publicação definitiva. |

Como o acesso aos repositórios internos não estava disponível durante a elaboração deste documento, os contratos de API, rotas e campos de resposta foram construídos com base nas convenções do Django REST Framework e identificados como `[MOCK]`. Os pontos que precisam de validação estão listados na [seção de referências](docs/fluxo-perfil-profissionais.md#pontos-pendentes-de-validação).

---

## Visualização dos diagramas

Os diagramas foram criados com [Mermaid](https://mermaid.js.org) e são renderizados automaticamente pelo GitHub. 
