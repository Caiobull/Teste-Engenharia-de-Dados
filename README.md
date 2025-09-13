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

# Arquitetura Recomendada (Azure)

## 1) Breve Diagrama Lógico (texto)

### Ingestão
- **Azure Data Factory (ADF)** ou **Azure Logic Apps** → orquestra chamadas à API IBGE  
- **Alternativa:** script **PySpark** agendado/cron  

### Armazenamento Raw
- **Azure Data Lake Storage Gen2**  
  - Contêiner:  
    - `raw/ibge/pop/`  
    - `raw/ibge/pib/`  
  - Formato: **JSON/NDJSON**  

### Processamento / Transformação
- **Azure Databricks (PySpark)** ou **Azure Synapse Spark pool**  
- Jobs que transformam **raw → bronze → silver → gold**  

### Curadoria / Serving (Delivery)
- **Gold** em formato **Parquet** particionado  
  - Exemplo de diretórios:  
    - `gold/municipio_ano/`  
    - `gold/uf_ano/`  
    - `gold/brasil_ano/`  
- **Opcional:** gravação em **Azure SQL** / **Synapse dedicated SQL pool** para consumo pelo Power BI  

### Visualização
- **Power BI** conectando:  
  - Diretamente aos arquivos **Parquet** no ADLS (via **Databricks SQL endpoint**)  
  - Ou à tabela no **Azure SQL**  

### Observabilidade
- Logs do **ADF** / **Databricks**  
- Monitoramento via **Azure Monitor**  
