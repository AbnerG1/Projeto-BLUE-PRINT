# Nível 2 — Blueprint Desktop | TechStock

Este Blueprint representa as etapas do serviço do TechStock em camadas, seguindo a estrutura demonstrada na referência da aula: **Ações do usuário, Frontstage, Backstage e Suporte**.

O conteúdo foi relacionado às funcionalidades que existem no código do projeto: acesso pelo Portal, consulta do catálogo na página Banco e manutenção dos componentes.

## Diagrama

O diagrama abaixo é Mermaid e pode ser visualizado diretamente pelo GitHub. O arquivo editável está em [blueprint-nivel-2-techstock.mmd](./blueprint-nivel-2-techstock.mmd).

```mermaid
flowchart TB
  %% Nível 2 — Blueprint Desktop do TechStock
  %% Camadas do serviço: ações, frontstage, backstage e suporte.

  subgraph ACOES["1. AÇÕES DO USUÁRIO"]
    direction LR
    A1["Abrir o Portal"] --> A2["Informar usuário e senha"]
    A2 --> A3["Acessar o Banco"]
    A3 --> A4["Consultar e pesquisar componentes"]
    A4 --> A5["Cadastrar, editar ou excluir componente"]
  end

  subgraph FRONT["2. FRONTSTAGE — O QUE O USUÁRIO VÊ"]
    direction LR
    F1["Página Portal<br/>formulário de login"]
    F2["Página Banco<br/>lista e indicadores"]
    F3["Formulários de cadastro e edição<br/>confirmação de exclusão"]
    F4["Mensagens de sucesso ou erro<br/>lista atualizada"]
  end

  subgraph BACK["3. BACKSTAGE — PROCESSOS INTERNOS"]
    direction LR
    B1["Rota /portal<br/>recebe credenciais"]
    B2["Application<br/>autentica com UserRecord e User_account"]
    B3["GerenciadorSessoes<br/>cria e valida sessão"]
    B4["Rota /banco<br/>verifica autenticação"]
    B5["ComponentRecord<br/>lista e executa CRUD"]
    B6["Componente<br/>valida nome, categoria,<br/>quantidade e descrição"]
    B7["DataRecord<br/>lê e grava registros"]
  end

  subgraph SUP["4. SUPORTE — RECURSOS QUE SUSTENTAM O SERVIÇO"]
    direction LR
    S1["Bottle<br/>servidor e rotas HTTP"]
    S2["Cookie de sessão<br/>HttpOnly e prazo de validade"]
    S3["Arquivos JSON<br/>contas e componentes"]
    S4["Views e interface<br/>HTML, CSS e JavaScript"]
    S5["Estrutura BMVC<br/>Models e Controllers"]
  end

  A1 --> F1
  A2 --> B1
  F1 --> B1
  B1 --> B2
  B2 --> B3
  B3 --> F2
  A3 --> F2
  F2 --> B4
  B4 --> B3
  B4 --> B5
  A4 --> F2
  B5 --> F2
  A5 --> F3
  F3 --> B5
  B5 --> B6
  B6 --> B7
  B7 --> F4
  F4 --> F2

  B1 -. utiliza .-> S1
  B2 -. consulta .-> S3
  B3 -. mantém .-> S2
  B5 -. persiste por .-> S3
  B6 -. pertence a .-> S5
  F1 -. renderizada por .-> S4
  F2 -. renderizada por .-> S4
  B1 -. organizada em .-> S5

  classDef action fill:#E8F1FF,stroke:#2563EB,color:#172554,stroke-width:1.5px
  classDef front fill:#E9F8EF,stroke:#15803D,color:#14532D,stroke-width:1.5px
  classDef back fill:#FFF4E5,stroke:#C2410C,color:#7C2D12,stroke-width:1.5px
  classDef support fill:#F3E8FF,stroke:#7E22CE,color:#581C87,stroke-width:1.5px

  class A1,A2,A3,A4,A5 action
  class F1,F2,F3,F4 front
  class B1,B2,B3,B4,B5,B6,B7 back
  class S1,S2,S3,S4,S5 support
```

## Como ler as camadas

| Camada | O que representa no TechStock |
|---|---|
| **Ações do usuário** | Abrir o Portal, informar credenciais, acessar o Banco, consultar e pesquisar componentes, cadastrar, editar e excluir. |
| **Frontstage** | As páginas, formulários, lista, indicadores, confirmações e mensagens que aparecem para o usuário. |
| **Backstage** | As rotas Bottle, autenticação, gerenciamento de sessão, coordenação pela classe `Application`, validação pelo modelo `Componente` e operações dos registros. |
| **Suporte** | Servidor Bottle, cookie de sessão, arquivos JSON, templates HTML, CSS, JavaScript e organização BMVC. |

## Relação com os arquivos do projeto

- `route.py`: define as rotas do Portal, Banco e API.
- `app/controllers/application.py`: coordena autenticação, sessões e dados usados nas páginas.
- `app/controllers/datarecord.py`: lê e grava contas e componentes em arquivos JSON.
- `app/models/user_account.py`: representa a conta e confere a senha.
- `app/models/sessao.py`: gerencia a validade das sessões.
- `app/models/componente.py`: representa e valida os dados do componente.
- `app/views/html/`: contém as páginas Portal e Banco.
- `app/static/`: contém CSS e JavaScript.

## Observação

O TechStock utiliza arquivos JSON para persistência; portanto, o diagrama não apresenta um banco de dados relacional. As setas contínuas representam o fluxo principal entre as camadas, enquanto as tracejadas indicam recursos de suporte utilizados pelos processos.
