# Pipeline de Dados para Varejista Global de Eletrônicos

> Repositório responsável pela ingestão, transformação e estruturação de dados brutos em um modelo Medallion (Bronze → Silver → Gold) no Databricks, servindo como base para análises de BI e modelagem preditiva.

## Ecossistema de Projetos

Este repositório faz parte de um ecossistema com três projetos modulares:

| Repositório | Função | Descrição |
|-------------|--------|-----------|
| **`core-data-pipeline`** | **Ingestão e Transformação** | **Pipeline de dados com arquitetura Medallion (Bronze → Silver → Gold)** |
| [`sales-performance-bi`](https://github.com/victorvanazzi/sales-performance-bi) | Visualização | Análise de desempenho comercial no Power BI |
| [`sales-demand-forecasting`](https://github.com/victorvanazzi/sales-demand-forecasting) | Modelagem Preditiva | Previsão de vendas mensais com séries temporais |

## Estrutura do Repositório

```
core-data-pipeline/
│
├── notebooks/
│   ├── 00_bronze_ingestao.ipynb        # Ingestão de dados brutos para camada Bronze
│   ├── 01_silver_transformacoes.ipynb    # Transformações e integração de dados para camada Silver
│   ├── 02a_gold_agregacoes_mensais.ipynb   # Agregações temporais mensais para séries de vendas
│   ├── 02b_gold_modelo_dimensional.ipynb   # Modelagem dimensional para análises no Power BI
│
└── README.md                         # Documentação do projeto
```

## Objetivo do Projeto

O objetivo principal deste projeto é estabelecer um ecossistema robusto de dados para uma varejista global de eletrônicos, através da ingestão e transformação de dados brutos em uma arquitetura Medallion no Databricks. O pipeline unifica, padroniza e enriquece os dados, gerando camadas prontas para consumo em análises de desempenho comercial e modelos de previsão de vendas.

## Estrutura do Pipeline (Arquitetura Medallion)

Este projeto implementa a arquitetura Medallion, estruturando os dados em três camadas principais:

### 1. Camada Bronze (Dados Brutos)

Responsável pela ingestão inicial dos dados brutos provenientes de arquivos CSV.

  * **Ingestão:** Leitura de arquivos CSV de vendas, produtos, lojas, clientes e taxas de câmbio.
  * **Padronização:** Limpeza e padronização dos nomes das colunas.
  * **Armazenamento:** Gravação dos dados como Delta Tables no DBFS, mantendo a estrutura original.

### 2. Camada Silver (Dados Refinados e Unificados)

Focada na limpeza, unificação e transformação dos dados da camada Bronze.

  * **Joins:** Unificação de dados de vendas com informações de produtos, lojas, clientes e taxas de câmbio.
  * **Tratamento de Dados:** Limpeza de nulos, ajuste de tipos de dados e remoção de colunas intermediárias.
  * **Armazenamento:** Gravação dos dados como uma única Delta Table (`sales_silver`) pronta para agregações e análises mais complexas.

### 3. Camada Gold (Dados para Consumo Final)

Construção de dados agregados e modelos dimensionais otimizados para casos de uso específicos, como BI e modelagem preditiva.

  * **Agregações Mensais:**
      * Agregação mensal do total de vendas em USD para todas as `storekey`.
      * Criação de uma série temporal mensal contínua, preenchendo meses sem vendas com zero.
      * **Output:** Tabela `sales_monthly_gold` (Parquet/Delta), utilizada pelo projeto de previsão de vendas.
  * **Modelo Dimensional:**
      * Construção de um modelo dimensional em esquema estrela.
      * Criação das dimensões (`dim_date`, `dim_product`, `dim_store`, `dim_customer`) e da tabela fato (`fact_sales`).
      * **Output:** Tabelas Delta (`dim_date`, `dim_product`, `dim_store`, `dim_customer`, `fact_sales`) prontas para consumo em ferramentas de Business Intelligence como o Power BI.

## Tecnologias e Ferramentas

| Categoria | Ferramentas |
|-----------|-------------|
| **Plataforma de Dados** | Databricks |
| **Processamento de Dados** | PySpark |
| **Formato de Armazenamento** | Delta Lake, Parquet |
| **Linguagem de Programação** | Python |
| **Controle de Versão** | Git/GitHub |

## Entregas Principais

As principais entregas do pipeline são os datasets nas camadas Silver e Gold, que servem como base para as análises e modelos subsequentes:

  * **`sales_silver` (Camada Silver):** Tabela Delta unificada e limpa, contendo todas as transações de vendas com dados enriquecidos e padronizados em USD.
  * **`sales_monthly_gold` (Camada Gold - Série Temporal):** Tabela agregada mensalmente, contendo o total de vendas em USD para a série temporal contínua, pronta para modelos preditivos.
  * **Modelo Dimensional (Camada Gold - BI):** Conjunto de tabelas Delta (`dim_date`, `dim_product`, `dim_store`, `dim_customer`, `fact_sales`) estruturadas em esquema estrela, otimizadas para análise de desempenho em BI.