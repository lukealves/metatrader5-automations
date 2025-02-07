# Coleta de Dados do MetaTrader 5

Este projeto tem como objetivo automatizar a extração de dados históricos do MetaTrader 5 (MT5), permitindo que traders e analistas financeiros coletem informações de mercado de forma estruturada e eficiente. Utilizando Python e diversas bibliotecas, o código se conecta ao MT5, coleta dados de ativos específicos e oferece a opção de salvar essas informações em arquivos `.csv` ou `.xlsx`.

## Automação de Trading

Em 2023, iniciei o desenvolvimento deste projeto com a intenção de criar uma solução de trading automatizado, baseada em um modelo LSTM. A ideia original era que o sistema operasse de forma contínua, realizando negociações autônomas por meio da análise de dados e tomada de decisões de compra e venda.

No entanto, posteriormente optei por simplificar a arquitetura, transferindo a integração com o LSTM para um projeto separado, mais focado no aprendizado de máquina. Apesar dessa mudança, as funcionalidades relacionadas às condições e automações do processo de trading foram preservadas no código. Isso garante que elas possam servir como alicerce para um futuro projeto mais robusto, com potencial para integrar interfaces gráficas e aproveitar recursos de processamento paralelo, como o `Celery`.

## Pré-Requisitos

Para executar este projeto, é necessário instalar o `Jupyter Notebook` ou o `Jupyter Lab`, além das bibliotecas que serão mencionadas a seguir. Também é preciso instalar o MetaTrader 5. Se você deseja apenas testar, pode baixá-lo diretamente no [site oficial](https://www.metatrader5.com/pt/download). Com a versão básica já é possível, em uma conta de demonstração, acessar uma ampla gama de dados do `Forex` e da `Nasdaq`. Caso queira acessar os dados da `Ibov`, mesmo em uma conta de teste, será necessário abrir conta em uma corretora.

## Funcionalidades Principais

### Execução de Código com IPython
Para garantir a integração eficiente das funções, o projeto permite a execução do notebook `funcoes.ipynb` diretamente dentro do ambiente atual. Isso facilita a reutilização de funções e módulos necessários.

```python
# Executando o notebook 'funcoes.ipynb' usando o comando correto de line magic
from IPython import get_ipython

# Executando o notebook 'funcoes.ipynb' usando o comando correto de line magic
get_ipython().run_line_magic('run', 'funcoes.ipynb')
```

### Bibliotecas Utilizadas
O código faz uso de diversas bibliotecas para diferentes finalidades:

- **MetaTrader5 (mt5)**: Permite a conexão com a plataforma MT5 para coletar dados e enviar ordens de compra e venda.
- **Pandas (pd)**: Essencial para manipulação de dados financeiros e organização das informações coletadas.
- **Datetime**: Utilizado para gerenciar datas e horários dentro do processo de coleta.
- **OS**: Facilita a manipulação de diretórios e arquivos do sistema.
- **Tkinter e filedialog**: Responsáveis por criar interfaces gráficas para a seleção e salvamento de arquivos.
- **Random**: Gera números aleatórios, caso necessário para testes ou simulações.

## Execução do Script

A execução do projeto começa pela função `main()`, que coordena todo o processo de conexão ao MT5, coleta de dados e armazenamento das informações.

```python
# Executar o script para extração de dados
if __name__ == "__main__":
    main()
```

## Fluxo de Coleta de Dados

### Conexão ao MetaTrader 5
A primeira etapa consiste em estabelecer a conexão com o MT5. Caso a inicialização falhe, a conexão é encerrada automaticamente.

```python
# Função para conectar ao MetaTrader 5
def connect_mt5():
    if not mt5.initialize():
        print("Erro ao inicializar MetaTrader 5.")
        mt5.shutdown()
```

### Coleta de Dados Históricos
A função `get_data()` permite a extração de dados históricos de um ativo específico, definindo o intervalo de tempo e a quantidade de períodos desejados. Os dados são organizados em um `DataFrame` do Pandas para facilitar a análise posterior.

```python
# Função para coletar dados históricos
def get_data(symbol, timeframe, periods):
    rates = mt5.copy_rates_from_pos(symbol, timeframe, 0, periods)
    df = pd.DataFrame(rates)
    df['time'] = pd.to_datetime(df['time'], unit='s')
    return df
```

### Salvamento dos Dados
Após a coleta, o usuário pode salvar os dados em um arquivo `.csv` ou `.xlsx` utilizando uma interface gráfica para escolher o local de armazenamento.

```python
# Função para salvar os dados em .csv ou .excel
def save_file(data):
    # Configura a interface gráfica de seleção de arquivo
    root = tk.Tk()
    root.withdraw()  # Não exibe a janela principal

    # Pergunta onde o usuário deseja salvar o arquivo
    file_path = filedialog.asksaveasfilename(defaultextension=".csv", filetypes=[("CSV files", "*.csv"), ("Excel files", "*.xlsx")])
    
    if file_path:
        if file_path.endswith('.csv'):
            data.to_csv(file_path, index=False)
        elif file_path.endswith('.xlsx'):
            data.to_excel(file_path, index=False, engine='openpyxl')
        print(f"Arquivo salvo em: {file_path}")
```
