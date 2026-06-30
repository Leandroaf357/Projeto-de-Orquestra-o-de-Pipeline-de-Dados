# Terminal de análise e pipeline de dados ponta a ponta do Bitcoin

Este projeto implementa uma arquitetura completa de Engenharia de Dados e Business Intelligence para aquisição, tratamento, orquestração e visualização de dados históricos do mercado de Bitcoin (período de 2017 a 2023).

O projeto adota a **Arquitetura Medalhão** (Bronze, Silver e Gold) processada de forma distribuída via Apache Spark e visualizada em um dashboard de alta performance.

## 🛠️ Tecnologias e Ferramentas
* **Ambiente de Processamento:** Databricks (ambiente nativo da nuvem)
* **Processamento de Big Data:** Apache Spark (PySpark / Spark SQL)
* **Orquestração:** Fluxos de trabalho programáticos do Databricks via `dbutils`
* **Camada de Consumo / BI:** Power BI Desktop (via Conexão JDBC / PAT Token)

## 📐 Arquitetura do Projeto

### 1. Camada Bronze (Ingestão)
* **Notebook:** `01_Ingestão de dados brutos`
* **Objetivo:** Captura dos registros puros do mercado e persistência no armazenamento de arquivos em `/Volumes/leitura/bronze/dados` sem alterações estruturais, preservando o histórico de origem.

### 2. Camada Silver (Qualidade de Dados)
* **Caderno:** `02_Qualidade e limpeza de dados`
* **Objetivo:** Limpeza, transformação e aplicação de regras de qualidade de dados. Inclui desduplicação, conversão e padronização de campos temporais (`reference_date`), tratamento de valores ausentes e validação de esquemas de dados.

### 3. Camada Gold (Negócios)
* **Caderno:** `03_Business Aggregation for Power BI`
* **Objetivo:** Criação da tabela final agregada (`daily_summary`). Estruturação das principais métricas do ativo de forma consolidada (Preço Médio de Fechamento, Máximas e Mínimas do Dia, e Volume de Negociação Global) pronta para o consumo analítico.

### 4. Orquestração Programática (`dbutils`)
* **Notebook Mestre:** `00_Orquestrador_Pipeline`
* Para mitigar as limitações de hardware local com ambientes conteinerizados tradicionais, a orquestração foi projetada especificamente na nuvem. O script gerencia dependências cronológicas e define travas de segurança (*timeout*) para cada etapa da execução.

```píton
# Trecho da lógica de execução sequencial controlada
dbutils.notebook.run("./01_Ingestão de dados brutos", 120)
dbutils.notebook.run("./02_Qualidade e limpeza de dados", 120)
dbutils.notebook.run("./03_Business Aggregation para Power BI", 120)

📊 Camada de Visualização (Terminal Power BI)
A tabela Gold foi integrada ao Power BI por meio de autenticação de Token de Acesso Pessoal (PAT). O painel foi desenvolvido utilizando o padrão de design Block UI / Dark Theme, otimizando a legibilidade e o comportamento visual para simular um terminal financeiro real.

Principais Indicadores e Visuais Desenvolvidos:
Métricas de KPI: Preço Médio de Fechamento, Máxima Histórica, Mínima Histórica, Volume Total Negociado, Total de Dias Monitorados e Amplitude Macroeconômica.

Análise de Tendência: Gráfico de linha contínua mapeando a evolução do preço do ativo ao longo dos anos.

Análise de Sazonalidade: Gráficos de colunas verticais agrupando as médias de preço por mês e volume anualizado.

Cálculos via DAX: Criação de medidas dinâmicas para controle de volatilidade diária (Amplitude de Preço) e captura pontual do último preço de mercado registrado.
