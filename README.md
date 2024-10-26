# Sistema de Controle de Presença

## Descrição Geral

Este projeto é um sistema de controle de presença desenvolvido com Flask, HTML, CSS e JavaScript. Ele permite gerenciar a presença de funcionários em diferentes sites e empresas, integrando gráficos e tabelas para visualização de dados. O sistema é focado em uma interface intuitiva que permite ao usuário adicionar, visualizar, modificar e desativar registros de presença, além de agendar férias para os funcionários.

### Banco de Dados

O banco de dados utilizado é um banco de dados Access, onde estão armazenadas as informações de sites, empresas, nomes dos funcionários, tipos de presença e registros de controle de presença. A estrutura do banco de dados inclui as seguintes tabelas principais:

1. **Site**: Contém informações sobre os sites disponíveis.
2. **Empresa**: Contém informações sobre as empresas.
3. **Site_Empresa**: Relaciona os sites e empresas, indicando também se a empresa está ativa.
4. **Nome**: Armazena os nomes dos funcionários e associa-os com o site e empresa onde estão lotados.
5. **Presenca**: Contém os tipos de presença (como OK, FALTA, ATESTADO).
6. **Controle**: Armazena os registros de presença, relacionando funcionários com datas específicas e o tipo de presença registrado.

A estrutura foi desenhada para que seja possível realizar consultas dinâmicas e eficientes para gerar relatórios e visualizações gráficas.

### Justificativa do Uso do Flask

Flask foi escolhido como framework principal para este projeto por várias razões:

- **Leveza**: Flask é um framework minimalista que permite um desenvolvimento rápido e eficiente, ideal para aplicações de tamanho médio que não requerem uma estrutura robusta como o Django.
- **Facilidade de Integração**: A biblioteca `pyodbc` facilita a conexão com o banco de dados Access, e o Flask permite uma integração fluida entre as operações de banco de dados e as rotas da aplicação.
- **Flexibilidade**: O Flask fornece flexibilidade para adicionar extensões e personalizar a estrutura do projeto conforme necessário, permitindo a inclusão de funcionalidades como gerenciamento de sessão e flash messages para feedback do usuário.
- **Adequação para APIs REST**: Flask é bem adequado para criar APIs RESTful, facilitando futuras expansões do sistema, como a criação de uma API para integração com outros sistemas de gestão.

### Estrutura do Projeto

O projeto é dividido em rotas principais para exibir e gerenciar os dados de presença, utilizando templates HTML com integração de gráficos (via Plotly) e formulários interativos.

## Rotas

### `/` - Página Principal
- **Método**: GET, POST
- **Descrição**: Exibe a tabela de controle de presença com gráficos para visualização da distribuição de presença, incluindo gráficos de dispersão, pizza e barras empilhadas.
- **Parâmetros**:
  - `site`: Filtro de site.
  - `empresa`: Filtro de empresa.
  - `nomes`: Filtro de nomes.
  - `presenca`: Filtro de tipos de presença.
  - `meses`: Filtro de meses.
  - `ano`: Filtro de ano.
- **Funcionalidade**:
  - Filtra os dados com base nos critérios de seleção e atualiza a tabela e gráficos de presença.
  - Salva os filtros selecionados na sessão para permitir a navegação persistente.

### `/adicionar-presenca` - Página para Adicionar Presença
- **Método**: GET, POST
- **Descrição**: Permite adicionar ou remover registros de presença para os funcionários.
- **Parâmetros**:
  - `site`, `empresa`, `nomes`, `presenca`, `dia`, `mes`, `ano`: Dados capturados para o registro de presença.
- **Funcionalidade**:
  - Permite a seleção de um funcionário, site e tipo de presença e registra a presença na data especificada.
  - Exibe registros existentes para o mês atual.

### `/reativar-nome` - Reativar Nome
- **Método**: POST
- **Descrição**: Reativa um funcionário previamente desativado para que ele possa ter registros de presença novamente.
- **Parâmetros**:
  - `nome_desativado`: Nome do funcionário a ser reativado.
  - `siteempresa_id`: ID do site e empresa associados.
- **Funcionalidade**:
  - Atualiza o status do funcionário na tabela `Nome` para `Ativo`.

### `/inativar-nome` - Inativar Nome
- **Método**: POST
- **Descrição**: Desativa um funcionário para que ele não receba novos registros de presença.
- **Parâmetros**:
  - `nome_ativo`: Nome do funcionário a ser desativado.
  - `siteempresa_id`: ID do site e empresa associados.
- **Funcionalidade**:
  - Atualiza o status do funcionário na tabela `Nome` para `Inativo`.

### `/presenca` - Controlar Presença
- **Método**: POST
- **Descrição**: Adiciona ou remove registros de presença para os funcionários em uma data específica.
- **Parâmetros**:
  - `nomes`, `presenca`, `dia`, `mes`, `ano`, `siteempresa_id`: Dados para controle de presença.
- **Funcionalidade**:
  - Valida a data para não permitir registros em finais de semana e adiciona ou atualiza a presença conforme o tipo selecionado.

### `/adicionar-nome` - Adicionar Novo Nome
- **Método**: POST
- **Descrição**: Adiciona um novo funcionário à tabela `Nome` para um site e empresa específicos.
- **Parâmetros**:
  - `novo_nome`: Nome do funcionário.
  - `siteempresa_id`: ID do site e empresa associados.
- **Funcionalidade**:
  - Insere o nome do funcionário e define o status como ativo.

### `/adicionar-empresa` - Adicionar Nova Empresa
- **Método**: POST
- **Descrição**: Adiciona uma nova empresa ao banco de dados e a associa a um site específico.
- **Parâmetros**:
  - `nova_empresa`: Nome da nova empresa.
  - `site`: Nome do site associado.
- **Funcionalidade**:
  - Insere a empresa e a relaciona com o site na tabela `Site_Empresa`.

### `/desativar-empresa` - Desativar Empresa
- **Método**: POST
- **Descrição**: Desativa uma empresa, removendo-a da lista de empresas ativas.
- **Parâmetros**:
  - `empresa_ativa`: Nome da empresa a ser desativada.
- **Funcionalidade**:
  - Atualiza o status da empresa na tabela `Site_Empresa` para inativo.

### `/ativar-empresa` - Ativar Empresa
- **Método**: POST
- **Descrição**: Ativa uma empresa previamente desativada.
- **Parâmetros**:
  - `empresa_inativa`: Nome da empresa a ser ativada.
- **Funcionalidade**:
  - Atualiza o status da empresa na tabela `Site_Empresa` para ativo.

### `/programa-ferias` - Programar Férias
- **Método**: POST
- **Descrição**: Programa um período de férias para um funcionário.
- **Parâmetros**:
  - `nome`, `data_inicio`, `data_fim`, `siteempresa_id`: Dados do funcionário e período de férias.
- **Funcionalidade**:
  - Insere registros de presença do tipo "FÉRIAS" para cada dia no período especificado, respeitando o limite de 30 dias.

### `/desprogramar-ferias` - Desprogramar Férias
- **Método**: POST
- **Descrição**: Remove o registro de férias para um funcionário em um período específico.
- **Parâmetros**:
  - `nome`, `data_inicio`, `data_fim`, `siteempresa_id`: Dados do funcionário e período de férias a ser removido.
- **Funcionalidade**:
  - Remove os registros de presença do tipo "FÉRIAS" para o período especificado.

## Instalação e Configuração

1. **Clone o repositório**:
   ```bash
   git clone https://github.com/usuario/sistema-controle-presenca.git
   cd sistema-controle-presenca
   ```

2. **Instale as dependências**:
   ```bash
   pip install -r requirements.txt
   ```

3. **Configure o banco de dados**:
   - Certifique-se de ter um banco de dados Access compatível com o projeto.

4. **Execute o servidor**:
   ```bash
   python app.py
   ```
5. **Acesse a aplicação**: Abra o navegador e acesse `http://127.0.0.1:5000`.

--- 
