# Nível 4 — Blueprint do Serviço | TechStock

Este blueprint representa a experiência de uso do TechStock e os processos que sustentam as operações do catálogo. O fluxo foi montado a partir das rotas, modelos, controladores e telas presentes no código do projeto.

## Diagrama

O diagrama também está disponível em [formato Mermaid separado](./blueprint-nivel-4-techstock.mmd), para abrir em um editor compatível.

```mermaid
flowchart LR

  subgraph E["Evidências físicas"]
    E1["Portal de login"]
    E2["Página Banco"]
    E3["Tabela e pesquisa"]
    E4["Formulário de cadastro e edição"]
    E5["Confirmação de exclusão"]
    E6["Mensagem de resultado"]
  end

  subgraph U["Ações do usuário"]
    U1["Acessa o Portal"]
    U2["Informa usuário e senha"]
    U3["Consulta e pesquisa componentes"]
    U4["Preenche dados de um componente"]
    U5["Edita ou solicita exclusão"]
    U6["Confere o resultado na lista"]
  end

  subgraph F["Frontstage — interação visível"]
    F1["Exibe o formulário de autenticação"]
    F2["Apresenta a página Banco"]
    F3["Mostra componentes e indicadores"]
    F4["Recebe cadastro ou alteração"]
    F5["Solicita confirmação para excluir"]
    F6["Exibe mensagem e lista atualizada"]
  end

  subgraph B["Backstage — processamento interno"]
    B1["route.py recebe as requisições"]
    B2["Application coordena o fluxo"]
    B3["UserRecord localiza a conta"]
    B4["User_account confere a senha"]
    B5["GerenciadorSessoes cria e valida a sessão"]
    B6["ComponentRecord executa o CRUD"]
    B7["Componente valida os campos"]
    B8["DataRecord lê e grava os registros"]
  end

  subgraph S["Processos de suporte"]
    S1["Bottle faz o roteamento HTTP"]
    S2["Sessão por cookie e expiração"]
    S3["Arquivos JSON: componentes e usuários"]
    S4["Hash PBKDF2-HMAC-SHA256 para senhas"]
    S5["HTML, CSS e JavaScript da interface"]
  end

  U1 --> E1 --> F1
  U2 --> F1
  F1 --> B1 --> B2 --> B3 --> B4 --> B5
  B3 -. consulta .-> S3
  B4 -. proteção da senha .-> S4
  B5 -. controle da sessão .-> S2
  S1 -. encaminha as rotas .-> B1
  S5 -. apresenta as telas .-> F1

  B5 --> F2
  U3 --> E2 --> F2
  F2 --> F3
  F3 --> B1
  B1 --> B2 --> B6
  B6 --> B8
  B8 -. lê .-> S3
  B8 --> F3

  U4 --> E4 --> F4
  F4 --> B1 --> B2 --> B6 --> B7 --> B8
  B8 -. grava .-> S3
  B8 --> F6

  U5 --> E5 --> F5
  F5 --> B1 --> B2 --> B6 --> B7 --> B8
  B8 -. atualiza ou remove .-> S3
  B8 --> F6

  U6 --> E6
  F6 --> E3
```

## Leitura do fluxo

**Evidências físicas:** telas e elementos que o usuário encontra durante a utilização: Portal, página Banco, tabela, pesquisa, formulários, confirmação de exclusão e mensagens de resultado.

**Ações do usuário:** entrar no Portal, informar as credenciais, consultar e pesquisar componentes, cadastrar, editar, excluir e conferir o resultado.

**Frontstage:** respostas visíveis da aplicação. A interface apresenta os formulários, a lista de componentes, os indicadores do catálogo e as mensagens após cada operação.

**Backstage:** a rota recebe a requisição; a classe `Application` coordena o fluxo; `UserRecord` e `User_account` participam da autenticação; `GerenciadorSessoes` controla a sessão; `ComponentRecord` executa as operações do catálogo; `Componente` valida os campos; e `DataRecord` realiza a leitura e gravação dos dados.

**Processos de suporte:** Bottle encaminha as requisições HTTP; o cookie identifica a sessão; os arquivos JSON mantêm os registros; PBKDF2-HMAC-SHA256 protege as senhas armazenadas; e HTML, CSS e JavaScript compõem a interface.

## Relação com as operações do catálogo

| Operação | Ação visível | Processamento interno |
|---|---|---|
| CREATE | Preencher o formulário e cadastrar | A rota recebe os campos, o modelo valida e o registro é salvo |
| READ | Abrir a página Banco e pesquisar | O controlador consulta os registros e a página apresenta a lista |
| UPDATE | Editar os dados e salvar | O registro é localizado, validado e gravado novamente |
| DELETE | Solicitar exclusão e confirmar | O registro é localizado e removido da lista persistida |

## Observações sobre a implementação

- O catálogo é armazenado em arquivos JSON, não em SQLite.
- A página Banco permite cadastrar, pesquisar, editar e excluir componentes.
- As operações também permanecem disponíveis por meio da API JSON.
- As sessões são mantidas durante a execução da aplicação e possuem prazo de expiração.
- A senha é verificada a partir de hash com salt; ela não é armazenada em texto puro.

O diagrama descreve o fluxo implementado no TechStock e não inclui funcionalidades que não aparecem no código atual.
