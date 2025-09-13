# # 📊 Case de Engenharia de Dados – IBGE (População & PIB Municipal)

## 🎯 Objetivo
Implementar uma solução de dados que consuma as bases do **IBGE** de População (2024) e PIB Municipal (2021), realize ingestão via API, modele os dados, armazene em **formato Parquet** e exponha indicadores em tabelas e um dashboard no **Power BI**.

---

## 🏛️ Fontes Oficiais (IBGE)

- **População residente estimada (2024)**  
  [Agregado 6579](https://servicodados.ibge.gov.br/api/v3/agregados/6579/)

- **PIB Municipal (2021)**  
  [Agregado 5938](https://servicodados.ibge.gov.br/api/v3/agregados/5938/)

---

## ⚙️ Arquitetura da Solução (Azure)

# 1) Arquitetura Recomendada (Azure)

## Breve Diagrama Lógico (Texto)

### Ingestão
- **Azure Data Factory (ADF)** ou **Azure Logic Apps** → orquestra chamadas à **API IBGE**  
- **Alternativa:** script **PySpark** agendado/cron  

### Armazenamento Raw
- **Azure Data Lake Storage Gen2**  
  - Contêineres:  
    - `raw/ibge/pop/`  
    - `raw/ibge/pib/`  
  - Formato: **JSON / NDJSON**  

### Processamento / Transformação
- **Azure Databricks (PySpark)** ou **Azure Synapse Spark pool**  
- Jobs que transformam:  
  **raw → bronze → silver → gold**  

### Curadoria / Serving (Delivery)
- **Gold** em **Parquet** particionado  
  - Exemplos de diretórios:  
    - `gold/municipio_ano/`  
    - `gold/uf_ano/`  
    - `gold/brasil_ano/`  
- **Opcional:** gravação em **Azure SQL** / **Synapse dedicated SQL pool** para consumo no **Power BI**  

### Visualização
- **Power BI** conectado:  
  - Diretamente aos arquivos **Parquet** no ADLS (via **Databricks SQL endpoint**)  
  - Ou à tabela no **Azure SQL**  

### Observabilidade
- Logs do **ADF** / **Databricks**  
- Monitoramento via **Azure Monitor**  

---

## Diagrama (Mermaid)

```mermaid
flowchart TD

subgraph INGESTAO[Ingestão]
  A[ADF / Logic Apps]:::azure -->|API Calls| B[API IBGE]
  C[PySpark Script Cron]:::alt --> B
end

subgraph RAW[Armazenamento Raw]
  D[Azure Data Lake Gen2 <br/> raw/ibge/pop/ , raw/ibge/pib/ <br/> JSON/NDJSON]:::storage
end

subgraph PROC[Processamento / Transformação]
  E[Azure Databricks <br/> PySpark]:::compute
  F[Azure Synapse <br/> Spark Pool]:::compute
end

subgraph SERVING[Curadoria / Serving]
  G[Parquet Gold <br/> gold/municipio_ano/ , gold/uf_ano/ , gold/brasil_ano/]:::gold
  H[Azure SQL / Synapse Dedicated SQL]:::sql
end

subgraph VIS[Visualização]
  I[Power BI]:::pbi
end

subgraph OBS[Observabilidade]
  J[Logs ADF / Databricks]:::log
  K[Azure Monitor]:::monitor
end

A --> D
C --> D
D --> E
D --> F
E --> G
F --> G
G --> H
G --> I
H --> I
E --> J
F --> J
J --> K

classDef azure fill:#D6EAF8,stroke:#1B4F72,stroke-width:2px;
classDef alt fill:#FADBD8,stroke:#7B241C,stroke-width:2px;
classDef storage fill:#FDEDEC,stroke:#7D3C98,stroke-width:2px;
classDef compute fill:#F9E79F,stroke:#7D6608,stroke-width:2px;
classDef gold fill:#D5F5E3,stroke:#145A32,stroke-width:2px;
classDef sql fill:#E8DAEF,stroke:#4A235A,stroke-width:2px;
classDef pbi fill:#FCF3CF,stroke:#B7950B,stroke-width:2px;
classDef log fill:#F2F3F4,stroke:#616A6B,stroke-width:2px;
classDef monitor fill:#EBF5FB,stroke:#1B4F72,stroke-width:2px;




