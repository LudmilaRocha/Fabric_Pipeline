# 🏅 Fabric Pipeline — Olimpíadas de Verão 1896–2024
### *Summer Olympics Data Pipeline with Microsoft Fabric*

<p align="center">
  <img src="https://img.shields.io/badge/Microsoft%20Fabric-0078D4?style=for-the-badge&logo=microsoft&logoColor=white"/>
  <img src="https://img.shields.io/badge/PySpark-E25A1C?style=for-the-badge&logo=apache-spark&logoColor=white"/>
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black"/>
  <img src="https://img.shields.io/badge/OneLake-0078D4?style=for-the-badge&logo=microsoft&logoColor=white"/>
</p>

---

## 🇧🇷 Português

### 📌 Sobre o Projeto

Projeto prático focado no exame **DP-600 (Microsoft Fabric Analytics Engineer)**. Implementa uma pipeline completa de engenharia de dados com as camadas **Bronze → Silver → Gold** no **Microsoft Fabric**, utilizando dados históricos das Olimpíadas de Verão de 1896 a 2024.

O objetivo é consolidar conceitos de **governança, orquestração e transformação de dados** no OneLake, com análises focadas no desempenho do Brasil nas Olimpíadas.

> ⚠️ **Nota:** Os dados e tabelas Delta ficam armazenados no Microsoft Fabric (ambiente privado). Este repositório contém apenas o **código-fonte** e a **documentação** do projeto.

---

### 🗂️ Arquitetura da Pipeline

```
Kaggle API (kagglehub)
        │
        ▼
┌─────────────────────────────────────────────────────┐
│               Microsoft Fabric OneLake              │
│                                                     │
│  ┌──────────────┐                                   │
│  │    BRONZE    │  Dados brutos ingeridos via        │
│  │  atletas_    │  Data Factory Pipeline             │
│  │  olimpicos   │  (kagglehub → Delta Table)         │
│  └──────┬───────┘                                   │
│         │  Limpeza e padronização (PySpark)          │
│         ▼                                           │
│  ┌──────────────┐                                   │
│  │    SILVER    │  251.099 registros únicos          │
│  │  atletas_    │  sem duplicatas, tipos corretos    │
│  │  olimpicos   │  e medalhas padronizadas           │
│  │  _tratado    │                                   │
│  └──────┬───────┘                                   │
│         │  Agregações e análises (PySpark)           │
│         ▼                                           │
│  ┌──────────────────────────────────────────────┐   │
│  │                   GOLD                       │   │
│  │  brasil_medalhas_por_ano                     │   │
│  │  brasil_medalhas_por_esporte                 │   │
│  │  brasil_medalhas_por_genero                  │   │
│  └──────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────┘
        │
        ▼
   Power BI (Análise e Visualização)
```

---

### 📦 Dataset

- **Fonte:** Kaggle — [Summer Olympics Medals 1896–2024](https://www.kaggle.com/datasets/stefanydeoliveira/summer-olympics-medals-1896-2024)
- **Ingestão:** via biblioteca `kagglehub` direto no notebook PySpark
- **Volume:** ~252.000 registros brutos

```python
import kagglehub
path = kagglehub.dataset_download("stefanydeoliveira/summer-olympics-medals-1896-2024")
```

---

### 🛠️ Tecnologias Utilizadas

| Tecnologia | Uso |
|---|---|
| Microsoft Fabric | Plataforma principal (Lakehouse, Pipeline, Notebook) |
| Data Factory (Fabric) | Ingestão e orquestração da pipeline |
| PySpark (Python) | Transformação e análise de dados |
| Delta Lake / OneLake | Armazenamento das camadas Bronze, Silver e Gold |
| kagglehub | Download do dataset diretamente no notebook |
| Power BI | Visualização e análise dos dados Gold |
| GitHub | Versionamento do código e documentação |

---

### 🔄 Etapas da Pipeline

#### ⚙️ 1. Criação do Lakehouse e da Pipeline

Criação do `LH_Olimpiadas` no workspace `Engenharia_Fabric` e configuração da `Pipeline_Ingestao_Git` no Data Factory.

![Criação Lakehouse e Pipeline](assets/01_lakehouse_pipeline_criacao.png)

---

#### 🔗 2. Configuração da Fonte HTTP

Conexão via HTTP anônimo apontando para o dataset público do Kaggle.

![Configuração HTTP](assets/02_pipeline_http_config.png)

---

#### 🎨 3. Canvas da Pipeline

Atividade de cópia de dados configurada no canvas do Data Factory.

![Canvas Pipeline](assets/03_pipeline_canvas.png)

---

#### 🎯 4. Configuração do Destino e Validação

Destino configurado para o Lakehouse com esquema `Bronze_Atletas` e tabela `Atletas_Olimpicos`. Pipeline validada com sucesso.

![Destino e Validação](assets/04_pipeline_destino_validar.png)

---

#### 📅 5. Agendamento de Execução

Pipeline agendada para execução diária às 12h (UTC-03:00 Brasília).

![Agendamento](assets/05_pipeline_agendamento.png)

---

#### 🥉 6. Camada Bronze — Ingestão dos Dados Brutos

Dados carregados com sucesso via `kagglehub` e armazenados como Tabela Delta no Lakehouse.

- ✅ Fonte: `olympics_athletes_dataset.csv` via Kaggle API
- ✅ Tabela: `Bronze_Atletas.atletas_olimpicos`
- ✅ **252.000+ registros** carregados

![Camada Bronze](assets/06_bronze_camada.png)

---

#### 🥈 7. Camada Silver — Limpeza e Padronização

Transformações aplicadas para garantir qualidade e consistência dos dados:

- ✅ Nulos em `medalha` → `"Nenhuma Medalha"`
- ✅ Coluna `year` → `IntegerType`
- ✅ Coluna `esporte` → letras maiúsculas com `F.upper()`
- ✅ Duplicatas → removidas com `dropDuplicates()`

![Silver Limpeza](assets/07_silver_limpeza.png)

**Resultado:** `Silver_Atletas.atletas_olimpicos_tratado` com **251.099 registros únicos, 0 duplicatas**.

![Silver Resultado](assets/08_silver_resultado.png)

---

#### 🥇 8. Camada Gold — Análises do Brasil

Filtro aplicado: apenas atletas brasileiros com medalhas. Respostas às 4 perguntas analíticas do projeto.

![Gold Análises](assets/09_gold_analises.png)

Tabelas Gold geradas com os resultados consolidados:

![Gold Tabelas](assets/10_gold_tabelas.png)

Medalhas por ano — **melhor edição: Rio 2016 com 74 medalhas**:

![Gold Medalhas por Ano](assets/11_gold_medalhas_ano.png)

---

### 📊 Resultados

| Métrica | Resultado |
|---|---|
| 🏅 Total de medalhas do Brasil | **570** |
| 🏆 Melhor edição | **Rio 2016 (74 medalhas)** |
| 🤸 Esportes de destaque | Futebol, Vôlei, Atletismo |
| ♀️ Medalhas femininas Bronze | 21 |
| ♂️ Medalhas masculinas Bronze | 27 |
| 📅 Período analisado | 1896 – 2024 |

---

### 🚀 Como Reproduzir

#### Pré-requisitos
- Conta Microsoft Fabric (trial gratuito em [fabric.microsoft.com](https://app.fabric.microsoft.com))
- Conta Kaggle com API configurada

#### Passo a Passo

**1. Criar o Lakehouse**
```
Nome: LH_Olimpiadas
Workspace: Engenharia_Fabric
```

**2. Criar a Pipeline de Ingestão**
```
Nome: Pipeline_Ingestao_Git
Tipo: Data Pipeline
```

**3. Configurar o Notebook — instalar e usar o kagglehub:**
```python
%pip install kagglehub -q
import kagglehub
path = kagglehub.dataset_download("stefanydeoliveira/summer-olympics-medals-1896-2024")
```

**4. Executar as camadas na ordem:**
```
Bronze → Silver → Gold
```

**5. Conectar ao Power BI**

Use as tabelas Gold diretamente pelo conector do Microsoft Fabric no Power BI.

---

## 🇺🇸 English

### 📌 About the Project

Hands-on project focused on the **DP-600 (Microsoft Fabric Analytics Engineer)** exam. Implements a complete data engineering pipeline with **Bronze → Silver → Gold** layers in **Microsoft Fabric**, using historical Summer Olympics data from 1896 to 2024.

> ⚠️ **Note:** Data and Delta tables are stored in Microsoft Fabric (private environment). This repository contains only the **source code** and **documentation**.

---

### 🛠️ Tech Stack

| Technology | Usage |
|---|---|
| Microsoft Fabric | Main platform (Lakehouse, Pipeline, Notebook) |
| Data Factory (Fabric) | Pipeline ingestion and orchestration |
| PySpark (Python) | Data transformation and analysis |
| Delta Lake / OneLake | Bronze, Silver and Gold layer storage |
| kagglehub | Dataset download directly in the notebook |
| Power BI | Gold data visualization |
| GitHub | Code versioning and documentation |

---

### 📦 Dataset

- **Source:** Kaggle — [Summer Olympics Medals 1896–2024](https://www.kaggle.com/datasets/stefanydeoliveira/summer-olympics-medals-1896-2024)
- **Ingestion:** via `kagglehub` library directly in PySpark notebook
- **Volume:** ~252,000 raw records

---

### 🔄 Pipeline Layers

| Layer | Description | Records |
|---|---|---|
| 🥉 Bronze | Raw data ingested via Data Factory | 252,000+ |
| 🥈 Silver | Cleaned and standardized data | 251,099 unique |
| 🥇 Gold | Brazil-focused aggregated analysis | 3 tables |

---

### 📊 Key Results

| Metric | Result |
|---|---|
| 🏅 Total Brazil medals | **570** |
| 🏆 Best edition | **Rio 2016 (74 medals)** |
| 🤸 Top sports | Football, Volleyball, Athletics |
| 📅 Period | 1896 – 2024 |

---

### 🚀 How to Reproduce

1. Create a free Microsoft Fabric trial at [fabric.microsoft.com](https://app.fabric.microsoft.com)
2. Create a Lakehouse named `LH_Olimpiadas`
3. Create a Data Pipeline named `Pipeline_Ingestao_Git`
4. Run `Notebook.ipynb` in order: Bronze → Silver → Gold
5. Connect Power BI to the Gold tables

---

## 📁 Estrutura do Repositório / Repository Structure

```
Fabric_Pipeline/
│
├── Notebook.ipynb          # Pipeline completa (Bronze → Silver → Gold)
├── README.md               # Documentação do projeto
├── assets/                 # Prints das execuções no Microsoft Fabric
│   ├── 01_lakehouse_pipeline_criacao.png
│   ├── 02_pipeline_http_config.png
│   ├── 03_pipeline_canvas.png
│   ├── 04_pipeline_destino_validar.png
│   ├── 05_pipeline_agendamento.png
│   ├── 06_bronze_camada.png
│   ├── 07_silver_limpeza.png
│   ├── 08_silver_resultado.png
│   ├── 09_gold_analises.png
│   ├── 10_gold_tabelas.png
│   └── 11_gold_medalhas_ano.png
└── .gitignore
```

---

## 👩‍💻 Autora / Author

**Ludmila Rocha**

[![GitHub](https://img.shields.io/badge/GitHub-LudmilaRocha-181717?style=flat&logo=github)](https://github.com/LudmilaRocha)

---

> 📚 Projeto desenvolvido como parte dos estudos para a certificação **DP-600 — Microsoft Fabric Analytics Engineer**
