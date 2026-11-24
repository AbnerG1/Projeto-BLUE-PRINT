# Nível 4 — Blueprint do Serviço | TechStock

Este diagrama foi redesenhado para facilitar a leitura durante a apresentação. Ele mostra o caminho principal do serviço em três momentos: acesso, consulta do catálogo e manutenção dos componentes.

## Diagrama do Blueprint

O diagrama Mermaid abaixo pode ser visualizado diretamente no GitHub. Se preferir, abra o [arquivo Mermaid separado](./blueprint-nivel-4-techstock.mmd) em um editor compatível.

```mermaid
flowchart TB
  subgraph P1["1. ACESSO"]
    direction LR
    U1["Usuário abre o Portal"] --> F1["Tela de login"]
    F1 --> B1["Rota recebe credenciais"]
    B1 --> B2["Application consulta UserRecord e User_account"]
    B2 --> S1["JSON de usuários + hash da senha"]
    B2 --> F2["Acesso autorizado / mensagem de erro"]
  end

  subgraph P2["2. CONSULTA DO CATÁLOGO"]
    direction LR
    U2["Usuário abre Banco e pesquisa"] --> F3["Lista, indicadores e pesquisa"]
    F3 --> B3["Rota chama Application"]
    B3 --> B4["ComponentRecord consulta DataRecord"]
    B4 --> S2["Arquivo JSON de componentes"]
    B4 --> F4["Lista de componentes apresentada"]
  end

  subgraph P3["3. MANUTENÇÃO DO CATÁLOGO"]
    direction LR
    U3["Usuário cadastra, edita ou exclui"] --> F5["Formulário e confirmação"]
    F5 --> B5["Rota recebe a operação"]
    B5 --> B6["Application coordena; Componente valida"]
    B6 --> B7["ComponentRecord / DataRecord grava a alteração"]
    B7 --> S2
    B7 --> F6["Mensagem de resultado e lista atualizada"]
  end

  subgraph SUP["SUPORTE AO SERVIÇO — presente nas etapas"]
    direction LR
    T1["Bottle: rotas HTTP"] --- T2["Cookie e GerenciadorSessoes"]
    T2 --- T3["Models e Controllers"]
    T3 --- T4["Arquivos JSON"]
    T4 --- T5["Views: HTML, CSS e JavaScript"]
  end

  classDef user fill:#E8F1FF,stroke:#2563EB,color:#172554,stroke-width:1.5px
  classDef front fill:#E9F8EF,stroke:#15803D,color:#14532D,stroke-width:1.5px
  classDef back fill:#FFF4E5,stroke:#C2410C,color:#7C2D12,stroke-width:1.5px
  classDef support fill:#F3E8FF,stroke:#7E22CE,color:#581C87,stroke-width:1.5px
  classDef store fill:#F1F5F9,stroke:#475569,color:#0F172A,stroke-width:1.5px

  class U1,U2,U3 user
  class F1,F2,F3,F4,F5,F6 front
  class B1,B2,B3,B4,B5,B6,B7 back
  class S1,S2 store
  class T1,T2,T3,T4,T5 support
```

## Como interpretar

- **Ações do usuário:** abrir o Portal, entrar, consultar, pesquisar e manter o catálogo.
- **Frontstage:** telas, formulários, lista, mensagens e confirmações que o usuário vê.
- **Backstage:** rotas, Application, registros e modelos que recebem, coordenam e validam as operações.
- **Suporte:** Bottle, sessões, arquivos JSON e recursos da interface que sustentam o serviço.

## Relação com o código

| Parte do Blueprint | Elementos do projeto |
|---|---|
| Acesso | rotas do Portal, `Application`, `UserRecord`, `User_account` e `GerenciadorSessoes` |
| Consulta | rota Banco, `ComponentRecord` e `DataRecord` |
| Cadastro, edição e exclusão | formulários, rotas, `Componente`, `ComponentRecord` e `DataRecord` |
| Suporte | Bottle, cookie de sessão, JSON, HTML, CSS e JavaScript |

## Observação

O catálogo utiliza arquivos JSON para persistir componentes e contas. O diagrama representa as responsabilidades e os fluxos encontrados no código atual; não representa um banco de dados relacional.
