# SRAG Report por Indicium HealthCare Inc.

## Descrição do Projeto

Este repositório contém a implementação de uma Prova de Conceito (PoC) para a Indicium HealthCare Inc., desenvolvida como parte da certificação em Artificial Intelligence Engineer. O objetivo é criar uma solução baseada em Inteligência Artificial Generativa que consulta dados históricos de internações por Síndrome Respiratória Aguda Grave (SRAG) do Open DATASUS e integra notícias em tempo real para gerar relatórios automatizados. Esses relatórios incluem métricas chave, explicações contextualizadas e gráficos para auxiliar profissionais da saúde no monitoramento de surtos de doenças.

A solução utiliza LangChain para orquestrar agentes de IA que processam dados, buscam notícias e geram análises.

### Funcionalidades Principais
- **Consulta a Dados**: Extração e cálculo de métricas a partir de um banco de dados baseado no CSV do Open DATASUS.
- **Busca de Notícias**: Integração com ferramentas como Tavily para obter notícias recentes sobre SRAG.
- **Análise e Revisão**: Agentes dedicados para análise de métricas e revisão de conteúdo.
- **Geração de Relatório**: Produção de um relatório em HTML com métricas, explicações, comentários baseados em notícias e gráficos (diário dos últimos 30 dias e mensal dos últimos 12 meses).
- **Métricas Calculadas**:
  - Taxa de aumento de casos.
  - Taxa de mortalidade.
  - Taxa de ocupação de UTI.
  - Taxa de vacinação da população.

## Instalação

1. Clone o repositório:
   ```
   git clone https://github.com/fevecn/ai_engineer_challenge.git
   cd ai_engineer_challenge
   ```

2. Instale as dependências:
   ```
   pip install -r requirements.txt
   ```

3. Configure variáveis de ambiente:
   - Crie um arquivo `.env` na raiz do projeto com:
     ```
     TAVILY_API_KEY=sua-chave-aqui
     LLM_API_KEY=sua-chave-para-llm-aqui  # Ex.: OpenAI API Key
     ```

## Uso

1. Execute o script principal para gerar o relatório:
   ```
   python main.py
   ```
   - Isso orquestra os agentes, consulta dados e notícias, e gera um arquivo HTML na pasta `reports/`.

2. Exemplo de saída:
   - O relatório HTML inclui:
     - Métricas com valores calculados e explicações.
     - Comentários baseados em notícias recentes.
     - Gráficos gerados com Matplotlib (número diário de casos nos últimos 30 dias e mensal nos últimos 12 meses).

## Arquitetura da Solução

A arquitetura é baseada em agentes de IA orquestrados por LangChain e LangGraph. O diagrama conceitual está disponível no arquivo `arquitetura.pdf` neste repositório. Ele ilustra:

- **Agente Principal (Orquestrador)**: Um `Runnable Parallel` que coordena os fluxos paralelos.
- **Ferramentas (Tools)**:
  - **Delta Table**: Consulta ao banco de dados (usando Delta Tables do Databricks).
  - **Query Agent**: Processa consultas aos dados para calcular métricas.
  - **News Search Agent**: Busca notícias via Tavily, focando em fontes confiáveis como G1, Fiocruz, saude.gov.br e folha.uol.br.
  - **Analyst Agent**: Analisa métricas e integra contexto de notícias.
  - **Reviewer Agent**: Revisa o conteúdo para precisão e consistência.
- **Saída Final**: Template HTML que compila o relatório.
- **Interações**: O LLM (o GPT Mini) é chamado em cada agente para raciocínio e geração de texto. Fluxos incluem paralelismo para eficiência.

A arquitetura segue o diagrama da imagem fornecida, que mostra o fluxo de agentes.

### Considerações Técnicas
- **Tratamento de Dados**: 
  - Seleção de colunas: Foco em datas de internação, óbitos, uso de UTI, status de vacinação.
  - Tratamento: Tratamento seguindo as guidelines da geração de relatório do repositório do Ministério de Saúde.
- **Gráficos**: Gerados com Matplotlib e embutidos no HTML via base64.
- **Guardrails**: Implementado uma LLM no final da rede para revisão, o objetivo é limitar consultas a fontes confiáveis, evitar alucinações (validação cruzada entre dados e notícias) e manejar erros (ex.: retry em falhas de API).

## Governança e Transparência

- **Auditoria**: Logs de todas as decisões dos agentes são salvos em runs do MLflow.
- **Registro de Decisões**: Cada agente registra seu raciocínio em um formato estruturado (JSON) para rastreabilidade.
- **Guardrails**: 
  - Validação de fontes: Apenas sites oficiais e jornalísticos confiáveis.
  - Tratamento de Erros: Handlers para APIs falhando, com fallbacks para dados cached.
