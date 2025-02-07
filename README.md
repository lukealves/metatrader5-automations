<div align="center">
  <img src="imagens/cover.png" />
</div>
<hr>

# Coleta de Dados do MetaTrader 5

Este projeto tem como objetivo automatizar a extração de dados históricos do MetaTrader 5 (MT5), permitindo que traders e analistas financeiros coletem informações de mercado de forma estruturada e eficiente. Utilizando Python e diversas bibliotecas, o código se conecta ao MT5, coleta dados de ativos específicos e oferece a opção de salvar essas informações em arquivos `.csv` ou `.xlsx`.

## Automação de Trading

Em 2023, iniciei o desenvolvimento deste projeto com a intenção de criar uma solução de trading automatizado, baseada em um modelo LSTM. A ideia original era que o sistema operasse de forma contínua, realizando negociações autônomas por meio da análise de dados e tomada de decisões de compra e venda.

No entanto, posteriormente optei por simplificar a arquitetura, transferindo a integração com o LSTM para um projeto separado, mais focado no aprendizado de máquina. Apesar dessa mudança, as funcionalidades relacionadas às condições e automações do processo de trading foram preservadas no código. Isso garante que elas possam servir como alicerce para um futuro projeto mais robusto, com potencial para integrar interfaces gráficas e aproveitar recursos de processamento paralelo, como o `Celery`.

## Pré-Requisitos

Para executar este projeto, é necessário instalar o `Jupyter Notebook` ou o `Jupyter Lab`, além das bibliotecas que serão mencionadas a seguir. Também é preciso instalar o MetaTrader 5. Se você deseja apenas testar, pode baixá-lo diretamente no [site oficial](https://www.metatrader5.com/pt/download). Com a versão básica já é possível, em uma conta de demonstração, acessar uma ampla gama de dados do `Forex`. Caso queira acessar os dados da `Ibov` ou da `Nasdaq`, mesmo em uma conta de teste, será necessário abrir conta em uma corretora.

## Formato dos Dados Extraídos

<div align="center">

| DATE                | open    | high    | low     | close   | tick_volume | spread |
| ------------------- | ------- | ------- | ------- | ------- | ----------- | ------ |
| 2025-02-05 05:30:00 | 0.62544 | 0.62556 | 0.62531 | 0.62541 | 277         | 8      |
| 2025-02-05 05:45:00 | 0.62541 | 0.62551 | 0.62499 | 0.62499 | 306         | 8      |
| 2025-02-05 06:00:00 | 0.62499 | 0.62507 | 0.62475 | 0.62480 | 204         | 8      |
| 2025-02-05 06:15:00 | 0.62481 | 0.62488 | 0.62454 | 0.62474 | 254         | 8      |

</div>

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

### Exemplo de saída
> Digite o símbolo do ativo (por exemplo, EURUSD):  USDCAD

> Escolha o timeframe:

> 1 - M1 (1 minuto)

> 2 - M5 (5 minutos)

> 3 - M15 (15 minutos)

> 4 - M30 (30 minutos)

> 5 - H1 (1 hora)

> 6 - H4 (4 horas)

> 7 - D1 (1 dia)

> 8 - W1 (1 semana)

> 9 - MN1 (1 mês)

> Digite o número correspondente ao timeframe:  1

> Digite o número de períodos desejados:  1000

Será mostrado na tela, também, um breve dataframe mostrando os dados coletados e, feito isso, perguntar onde você quer salva-los, podendo ser selecionado o formato `.csv` ou `.xlsx`.

> Arquivo salvo em: C:/Users/local_escolhido/datasets/USDCAD_M1_1000P.csv

## Sistema de Negociação Automatizada no MetaTrader 5

Para iniciar o sistema de negociação, basta chamar a função `executar_negociacao()`:

```python
# Executar negociação
executar_negociacao()
```

### Como Funciona
Este projeto implementa um sistema de negociação automatizada utilizando o MetaTrader 5 (MT5). A execução do sistema é iniciada com a chamada da função `executar_negociacao()`, que gerencia todo o processo de negociação com base nas entradas do usuário e nas condições do mercado.

### 1. Inicialização do MetaTrader 5

A função `executar_negociacao()` começa inicializando o MetaTrader 5. Caso a inicialização falhe, o sistema exibe uma mensagem de erro e encerra a execução.

### 2. Entrada do Usuário
O usuário é solicitado a fornecer:
- O **símbolo do ativo** (por exemplo, `EURUSD`).
- O **timeframe** desejado (como M1, H1, D1, etc.), que é mapeado para um valor numérico correspondente.
- O **volume** da negociação, que é validado para garantir que esteja dentro do intervalo permitido (0.00 a 500.00).

### 3. Definição do Intervalo de Loop
O intervalo de tempo entre as verificações de mercado é definido com base no timeframe escolhido, utilizando a função `get_loop_interval`. Essa função mapeia o timeframe para um intervalo em segundos. Por exemplo:
- M1 (1 minuto) -> 15 segundos.
- H1 (1 hora) -> 30 minutos.

Caso o timeframe seja inválido, o intervalo padrão é de 60 segundos.

### 4. Verificação do Mercado e Ordens Abertas
O sistema entra em um loop infinito onde:
- Verifica se o mercado está aberto para o ativo selecionado, utilizando a função `is_market_open`. Essa função verifica se o ativo está habilitado para trading e se é um dia útil (segunda a sexta-feira).
- Verifica se há ordens abertas para o ativo, utilizando a função `check_open_positions`. Caso existam ordens abertas, o sistema aguarda antes de tomar novas decisões.

### 5. Execução da Negociação
Se o mercado estiver aberto e não houver ordens abertas, o sistema executa uma decisão de negociação utilizando a função `make_trade_decision`. Após a execução, o sistema aguarda o intervalo de tempo definido antes de repetir o processo.

### 6. Limpeza do Console
A cada iteração, o console é limpo utilizando a função `clear_console`, que mantém a saída organizada e legível.

### Exemplo de saída
> Verificando ordens abertas para o ativo EURUSD:

> DEBUG: Mercado aberto? True | Ordens abertas? None

> Iniciando negociação para EURUSD...

> Previsão de fechamento calculada: 1.03308

> Ordem de compra enviada com sucesso, id da ordem: 52100431685

> Compra ao preço 1.03269. Venda (take profit) ao preço 1.033083942179518. Stop loss: 0.8264671537436143

> Negociação executada. Aguardando 300 segundos para a próxima verificação...

## Função para Tomada de Decisão de Negócio no MetaTrader 5

A função `make_trade_decision` é um exemplo de **trading algorítmico** que simula a tomada de decisões de compra ou venda com base em uma previsão de preço. É importante destacar que essa função foi desenvolvida para **fins de teste e execução de exemplo**, e **não deve ser utilizada como uma estratégia de trading real** sem revisões e adaptações. As condições de trading foram originalmente projetadas para funcionar em conjunto com um modelo de previsão LSTM (Long Short-Term Memory), que gerava previsões de preços armazenadas na variável `predicted_close`. No entanto, após a remoção do modelo LSTM, foi incluído um trecho de código que simula uma previsão de preço apenas para testes.

### Como Funciona

1. **Obtenção dos Preços Atuais**:
   - A função começa obtendo os preços de **Bid** (compra) e **Ask** (venda) do ativo especificado (`symbol`) usando a função `mt5.symbol_info_tick`.

2. **Simulação de Previsão de Preço**:
   - Como o modelo LSTM foi removido, a função agora simula uma previsão de preço usando uma **média ponderada** entre os preços de Bid e Ask, com uma pequena variação aleatória de ±0.1%. Esse trecho é apenas para **testes de funcionamento** e não deve ser considerado uma previsão assertiva.
   - Exemplo do código:
     ```python
     predicted_close = (current_ask * 0.7) + (current_bid * 0.3)  # Média ponderada
     predicted_close *= 1 + random.uniform(-0.001, 0.001)  # Pequena variação aleatória
     ```

3. **Tomada de Decisão**:
   - A função compara o preço previsto (`predicted_close`) com os preços atuais de Bid e Ask para decidir se deve **comprar** ou **vender** o ativo.
   - Se o preço previsto for **maior** que os preços atuais, a decisão é de **compra**.
   - Se o preço previsto for **menor**, a decisão é de **venda**.
   - Caso o preço previsto seja **igual** aos preços atuais, nenhuma ação é tomada.

4. **Definição de Stop Loss e Take Profit**:
   - Para ordens de **compra**, o **take profit** é definido como o preço previsto, e o **stop loss** é calculado como 80% do take profit.
   - Para ordens de **venda**, o **take profit** também é o preço previsto, e o **stop loss** é calculado como 120% do take profit.

5. **Execução da Ordem**:
   - Dependendo da decisão (compra ou venda), a função envia uma ordem limitada (`send_buy_limit_order` ou `send_sell_limit_order`) com os parâmetros definidos.

### Exemplo de Uso
A função é chamada automaticamente pelo sistema de negociação quando as condições de mercado são favoráveis (mercado aberto e sem ordens abertas). Um exemplo de saída no console seria:

> Previsão de fechamento calculada: 1.03308

> Compra ao preço 1.03269. Venda (take profit) ao preço 1.03308. Stop loss: 0.82647

### Considerações Importantes
- **Fins Educacionais**: Esta função foi criada para **fins educacionais e de teste**. As condições de trading são generalistas e não refletem uma estratégia real ou assertiva.
- **Revisão Necessária**: Caso o usuário queira utilizar o sistema para trading real, é **essencial revisar e adaptar** as condições e estratégias presentes na função.
- **Simulação de Previsão**: A previsão de preço atual é apenas uma simulação para testes. Para uso real, recomenda-se a integração com um modelo de previsão confiável, como o LSTM originalmente utilizado.
