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

```mermaid
flowchart LR
    A[IBGE API] -->|Ingestão PySpark| B[Azure Data Lake Storage Gen2]
    B --> C[Camada Bronze (Raw - JSON)]
    C --> D[Camada Silver (Curated - Parquet)]
    D --> E[Camada Gold (Delivery - Modelagem)]
    E --> F[Power BI Dashboard]
