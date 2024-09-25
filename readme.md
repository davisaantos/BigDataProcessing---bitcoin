
# Análise de Dados do Bitcoin no Databricks

Este projeto realiza a análise de dados históricos da cotação do Bitcoin usando Apache Spark no Databricks. 
Os dados de cotação incluem registros OHLC (Open, High, Low, Close) e volume de transações em BTC e USD. A análise inclui o cálculo do preço médio ponderado diário, conversão de timestamps Unix para datas legíveis e visualização dos resultados.

## Pré-requisitos

1. Conta no [Databricks](https://databricks.com/try-databricks);
2. Dataset dos dados históricos do Bitcoin no formato CSV;
3. Python, PySpark e Spark DataFrames

## Etapas do Projeto

### 1. Criar uma Conta no Databricks [Caso não tenha]

1. Acesse o site [Databricks](https://databricks.com/try-databricks) e crie uma conta gratuita.
2. Acesse o painel de controle do Databricks após a criação da conta.

### 2. Criar um Cluster no Databricks

1. No painel esquerdo, clique em **Clusters**.
2. Clique em **Create Cluster**.
3. Dê um nome ao seu cluster (por exemplo, `bitcoin-cluster`).
4. Selecione uma versão do Spark (por exemplo, `3.0` ou superior).
5. Clique em **Create Cluster** e aguarde a inicialização.

### 3. Carregar o Dataset no Databricks

1. No painel esquerdo, clique em **Data**.
2. Clique em **Create Table** e selecione **Upload File**.
3. Carregue o arquivo CSV contendo os dados históricos do Bitcoin (por exemplo, `bitcoin_data.csv`).
4. Aguarde o upload do arquivo e copie o caminho gerado para ser usado no código.

### 4. Criar e Executar um Notebook no Databricks

1. No painel esquerdo, clique em **Workspace**.
2. Selecione **Create** > **Notebook**.
3. Dê um nome ao seu notebook (por exemplo, `BitcoinAnalysis`), selecione **Python** como linguagem e associe o cluster criado.
4. No notebook, cole o seguinte código PySpark para carregar, processar e analisar os dados do Bitcoin:

```python

# Criação da sessão Spark
spark = SparkSession.builder.appName('BitcoinAnalysis').getOrCreate()

# Caminho para o dataset CSV (substitua pelo caminho correto gerado pelo upload)
file_path = "/FileStore/tables/bitcoin_data.csv"

# Carregar os dados do Bitcoin no DataFrame
df = spark.read.csv(file_path, header=True, inferSchema=True)

# Exibir o esquema do dataset
df.printSchema()

# Limpeza de dados: Remover NaNs
df_cleaned = df.na.drop()

# Conversão de timestamp Unix para uma data legível
df_cleaned = df_cleaned.withColumn('date', from_unixtime(col('Timestamp')).cast('date'))

# Agrupar os dados por data e calcular o preço médio ponderado diário
df_daily_avg = df_cleaned.groupBy('date').agg(avg('Weighted_Price').alias('Daily_Avg_Price'))

# Ordenar por data
df_daily_avg = df_daily_avg.orderBy('date')

# Mostrar os primeiros 10 resultados
df_daily_avg.show(10)
```

5. Execute o código clicando no botão **Run** ou pressionando `Shift + Enter`.

### 5. Visualizar e Salvar Resultados

Se desejar salvar os resultados em um novo arquivo CSV, adicione o seguinte código no final do notebook:

```python
# Salvar os resultados em um novo arquivo CSV
output_path = "/FileStore/tables/bitcoin_daily_avg.csv"
df_daily_avg.write.csv(output_path, header=True)
```

Agora, você pode acessar os resultados na seção **Data** do Databricks, navegando até o diretório `/FileStore/tables/`.

### 6. Visualizar Dados com Gráficos

Para criar um gráfico do preço médio diário do Bitcoin, adicione o seguinte código ao seu notebook:

```python
# Importar bibliotecas de visualização
import matplotlib.pyplot as plt
import pandas as pd

# Converter o DataFrame Spark para um DataFrame Pandas
df_pandas = df_daily_avg.toPandas()

# Criar o gráfico de preço médio diário
plt.figure(figsize=(10,6))
plt.plot(df_pandas['date'], df_pandas['Daily_Avg_Price'], color='blue', label='Preço Médio Diário')
plt.xlabel('Data')
plt.ylabel('Preço Médio ($)')
plt.title('Preço Médio Diário do Bitcoin')
plt.xticks(rotation=45)
plt.grid(True)
plt.show()
```

### 7. Integração com GitHub

#### 7.1 Conectar Databricks ao GitHub

1. No painel do Databricks, vá para **User Settings** (Configurações do Usuário).
2. Na aba **Git Integration**, selecione **GitHub**.
3. Gere um token de acesso pessoal no GitHub e insira o token no Databricks para conectar as duas plataformas.
4. Agora, você pode salvar o notebook no GitHub.

#### 7.2 Salvar Notebook no GitHub

1. No seu notebook do Databricks, clique em **File** > **Revision History**.
2. Selecione **Git** > **Link to Git**.
3. Configure o repositório GitHub ao qual você deseja conectar.
4. Depois de conectado, você pode **commit** o notebook diretamente para o repositório do GitHub.

### 8. Agendamento de Jobs no Databricks (Opcional)

Você pode agendar a execução automática de notebooks periodicamente:

1. No Databricks, vá para **Jobs** no painel esquerdo.
2. Crie um novo job e selecione o notebook que você deseja executar.
3. Defina a periodicidade (por exemplo, diariamente, semanalmente) e clique em **Create**.

## Estrutura do Repositório

- `bitcoin_data.csv`: Arquivo de dados históricos do Bitcoin.
- `BitcoinAnalysis.ipynb`: Notebook com o código de análise de dados.
- `README.md`: Este arquivo, explicando as etapas de execução no Databricks.

## Conclusão

Este projeto demonstra como utilizar Databricks e PySpark para analisar dados históricos da cotação do Bitcoin, realizar o processamento e visualização dos dados, e integrar o trabalho com o GitHub. Você pode expandir o projeto para incluir mais métricas e analisar outras criptomoedas ou ativos financeiros.
