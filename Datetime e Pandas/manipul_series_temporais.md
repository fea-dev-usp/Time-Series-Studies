# Manipulação de séries temporais com pandas
## Autores: Eduardo Salis e João Victor Araujo Evangelista

## Introdução

Por definição, uma série temporal consiste em observações sequenciais de uma variável (ou conjunto de variáveis) **cronologicamente ordenadas** e reunidas em **intervalos regulares de tempo**. Tal intervalo é equivalente à frequência em que os dados são coletados (a cada hora, diariamente, mensalmente, anualmente, etc.). Na área de **negócios** ou **economia**, certamente já nos deparamos com uma série temporal, seja analisando o crescimento econômico de um país (PIB), descrevendo a inflação ao consumidor, estimando as vendas de determinados produtos, ou observando os preços de ativos listados na bolsa de valores. Sua compreensão também é de grande importância para outros campos  além desses, como medicina, música, ecologia , física, etc.

O Estudo de séries temporais busca compreender o comportamento desses tipos de dados, utilizando modelos estatísticos para , através de seu passado histórico, projetar observações futuras, sendo de grande importância para entendimento de mercados (nosso contexto) e na formulação de estratégias para lidar com tais. Por exemplo, ao analisar a viabilidade de investimento em uma varejista, podemos tentar projetar suas vendas nos próximos trimestres (supondo que tenhamos os dados necessários para tal). Podemos, através desses estudos, tentar responder certas perguntas como: " Podemos prever a demanda de determinada commoditie?" ou " Seria possível modelar a série de preços de um ativo, e como isso ganhar dinheiro?"

Esse será o primeiro de uma "série" de artigos que faremos sobre a temática. Conceitos, características, análises, visualização, e modelagem serão assuntos posteriores. O foco deste será em usar o Python, em especial  a biblioteca **pandas** , como **ferramenta** de **manipulação.**

## Tipo de dado temporal e ferramentas de datas

Começaremos com os diferentes formatos de datas há várias fontes que podem representá-la. Uma dessas fontes, é a biblioteca padrão do Python  chamada **datetime**,  a qual fornece classes para manipular datas e tempo. Seu objeto date (datetime.date) fornece pontos (períodos) específicos no tempo, enquanto os atributos e métodos extraem informações/detalhes dessas datas (dia, mês, ano). Para obter informações quanto a "hora", minutos ou segundos; é necessário o objeto time (datetime.time). Para obter as duas funcionalidades, pode ser usado o datetime.datetime() .


```python
import datetime

dt = datetime.date(year=2020, month=12 , day=14)
dt2 = datetime.time(hour= 7, minute= 30 , second=15)
dt3 = datetime.datetime(year=2020, month=12 , day=14,hour= 7, minute= 30 , second=15 )
```


```python
print(dt)
print(dt2)
print(dt3)

print(dt.day)
print(dt.month)
print(dt.year)


print(dt2.hour)
print(dt2.minute)
print(dt2.second)
```

    2020-12-14
    07:30:15
    2020-12-14 07:30:15
    14
    12
    2020
    7
    30
    15
    

Nesse módulo  podemos instanciar as datas de dia e do horário em que rodamos o código


```python
hoje = datetime.date.today() # retorna o dia de hoje
agora = datetime.datetime.now() # retorna o dia e horario de agora

print(hoje)
print(agora)
print(hoje.day) #retorna um atributo do objeto instanciado

```

    2021-09-26
    2021-09-26 20:51:54.634199
    26
    

Também podemos usar strings para representar datas, assim como converter datas em strings. 

Caso queira saber um pouco mais sobre os códigos para representar diferentes tipos de datas, segue o link [https://strftime.org/](https://strftime.org/) .  Para outras funcionalidades, confira a documentação da biblioteca  [https://docs.python.org/pt-br/3/library/datetime.html](https://docs.python.org/pt-br/3/library/datetime.html)


```python
data_str = "2021-08-16"  # YYYY-MM-DD (representação ISO861)

# Para obter um objeto datetime.date a partir da formatação ISO861, basta:
print(datetime.date.fromisoformat(data_str))
# a função strptime retorna uma data dependendo do formato
data_str2 = "08-16-2021 14:45:37"
formato = "%m-%d-%Y %H:%M:%S"
print(datetime.datetime.strptime(data_str2, formato))

data_str3 = "16/08/06"
formato2 = "%d/%m/%y"
print(datetime.datetime.strptime(data_str3, formato2))
```

    2021-08-16
    2021-08-16 14:45:37
    2006-08-16 00:00:00
    


```python
dt4 = datetime.datetime(2020,3,15)
string = str(dt4)
print(string)
print(type(string))
```

    2020-03-15 00:00:00
    <class 'str'>
    

Para outras funcionalidades, confira a documentação da biblioteca  [https://docs.python.org/pt-br/3/library/datetime.html](https://docs.python.org/pt-br/3/library/datetime.html)


```python
import pandas as pd
```


```python
data = pd.Timestamp(datetime.datetime(2021,1,1))
data2 = pd.Timestamp('2021-01-01')
print(data, data2)
print(data == data2)

```

    2021-01-01 00:00:00 2021-01-01 00:00:00
    True
    

Podemos, da mesma forma que no pacote datetime, extrair informações  específicas por meio de métodos e atributos (confira a lista deles na documentação do pandas [https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.Timestamp.html))


```python
print(data.year)
print(data.month)
print(data.day)
print(data.dayofweek)
print(data.month_name())
print(data.day_name())
```

    2021
    1
    1
    4
    January
    Friday
    

O pandas possui uma outra forma de criar datas, pd.Period, com funcionamento similar ao Timestamp, porém com alguns métodos diferenentes


```python
data3 = pd.Period('2021-03')
print(data3)
print(data3.freq) # obtemos qual é a frequência do objeto
print(data3.asfreq("D")) # asfreq converte a frequência
print(data3.asfreq("Y")) # asfreq converte a frequência
```

    2021-03
    <MonthEnd>
    2021-03-31
    2021
    

Possuindo essa noção sobre objetos que representam datas, seguiremos  para como torná-los sequências através do pd.date_range, cujos argumentos principais são : 

- start: início da sequência de datas
- end: final da sequência de datas
- periods: número de instâncias de tempo queremos
- freq: frequência da série (mensal, diária, etc.)


```python
seq_datas = pd.date_range('2021-01', periods=12, freq='M') # freq mensal
print(seq_datas)
seq_datas2 = pd.date_range('2021-01',periods=60, freq='D' ) #freq diária
print(seq_datas2)
seq_datas3= pd.date_range(start='2021-01',end='2021-03') #freq diária é o padrão quando não é explicitada 
print(seq_datas3)
seq_datas4= pd.date_range(end='2021-03', periods=60) #podemos só definir o fim e o número de períodos
print(seq_datas4)
```

    DatetimeIndex(['2021-01-31', '2021-02-28', '2021-03-31', '2021-04-30',
                   '2021-05-31', '2021-06-30', '2021-07-31', '2021-08-31',
                   '2021-09-30', '2021-10-31', '2021-11-30', '2021-12-31'],
                  dtype='datetime64[ns]', freq='M')
    DatetimeIndex(['2021-01-01', '2021-01-02', '2021-01-03', '2021-01-04',
                   '2021-01-05', '2021-01-06', '2021-01-07', '2021-01-08',
                   '2021-01-09', '2021-01-10', '2021-01-11', '2021-01-12',
                   '2021-01-13', '2021-01-14', '2021-01-15', '2021-01-16',
                   '2021-01-17', '2021-01-18', '2021-01-19', '2021-01-20',
                   '2021-01-21', '2021-01-22', '2021-01-23', '2021-01-24',
                   '2021-01-25', '2021-01-26', '2021-01-27', '2021-01-28',
                   '2021-01-29', '2021-01-30', '2021-01-31', '2021-02-01',
                   '2021-02-02', '2021-02-03', '2021-02-04', '2021-02-05',
                   '2021-02-06', '2021-02-07', '2021-02-08', '2021-02-09',
                   '2021-02-10', '2021-02-11', '2021-02-12', '2021-02-13',
                   '2021-02-14', '2021-02-15', '2021-02-16', '2021-02-17',
                   '2021-02-18', '2021-02-19', '2021-02-20', '2021-02-21',
                   '2021-02-22', '2021-02-23', '2021-02-24', '2021-02-25',
                   '2021-02-26', '2021-02-27', '2021-02-28', '2021-03-01'],
                  dtype='datetime64[ns]', freq='D')
    DatetimeIndex(['2021-01-01', '2021-01-02', '2021-01-03', '2021-01-04',
                   '2021-01-05', '2021-01-06', '2021-01-07', '2021-01-08',
                   '2021-01-09', '2021-01-10', '2021-01-11', '2021-01-12',
                   '2021-01-13', '2021-01-14', '2021-01-15', '2021-01-16',
                   '2021-01-17', '2021-01-18', '2021-01-19', '2021-01-20',
                   '2021-01-21', '2021-01-22', '2021-01-23', '2021-01-24',
                   '2021-01-25', '2021-01-26', '2021-01-27', '2021-01-28',
                   '2021-01-29', '2021-01-30', '2021-01-31', '2021-02-01',
                   '2021-02-02', '2021-02-03', '2021-02-04', '2021-02-05',
                   '2021-02-06', '2021-02-07', '2021-02-08', '2021-02-09',
                   '2021-02-10', '2021-02-11', '2021-02-12', '2021-02-13',
                   '2021-02-14', '2021-02-15', '2021-02-16', '2021-02-17',
                   '2021-02-18', '2021-02-19', '2021-02-20', '2021-02-21',
                   '2021-02-22', '2021-02-23', '2021-02-24', '2021-02-25',
                   '2021-02-26', '2021-02-27', '2021-02-28', '2021-03-01'],
                  dtype='datetime64[ns]', freq='D')
    DatetimeIndex(['2021-01-01', '2021-01-02', '2021-01-03', '2021-01-04',
                   '2021-01-05', '2021-01-06', '2021-01-07', '2021-01-08',
                   '2021-01-09', '2021-01-10', '2021-01-11', '2021-01-12',
                   '2021-01-13', '2021-01-14', '2021-01-15', '2021-01-16',
                   '2021-01-17', '2021-01-18', '2021-01-19', '2021-01-20',
                   '2021-01-21', '2021-01-22', '2021-01-23', '2021-01-24',
                   '2021-01-25', '2021-01-26', '2021-01-27', '2021-01-28',
                   '2021-01-29', '2021-01-30', '2021-01-31', '2021-02-01',
                   '2021-02-02', '2021-02-03', '2021-02-04', '2021-02-05',
                   '2021-02-06', '2021-02-07', '2021-02-08', '2021-02-09',
                   '2021-02-10', '2021-02-11', '2021-02-12', '2021-02-13',
                   '2021-02-14', '2021-02-15', '2021-02-16', '2021-02-17',
                   '2021-02-18', '2021-02-19', '2021-02-20', '2021-02-21',
                   '2021-02-22', '2021-02-23', '2021-02-24', '2021-02-25',
                   '2021-02-26', '2021-02-27', '2021-02-28', '2021-03-01'],
                  dtype='datetime64[ns]', freq='D')
    


```python
seq_datas[0]
```




    Timestamp('2021-01-31 00:00:00', freq='M')



----

## Série Temporal: preços de fechamento ajustado PETR4

## Index e DataFrames

Sequências de datas podem compor  "*Pandas Series*" ou colunas dos "*DataFrames*". Para facilitar a leitura desse, incorporamos uma série de datas através dos índices, já em certas situações , precisamos que o índice registre informação sobre o tempo, para que possa ser representado uma série temporal.


```python
petr4 =pd.read_csv('PETR4.csv')
```


```python
petr4.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>data</th>
      <th>PETR4</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2015-01-02</td>
      <td>7.9297</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2015-01-05</td>
      <td>7.2943</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2015-01-06</td>
      <td>7.0571</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2015-01-07</td>
      <td>7.3451</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2015-01-08</td>
      <td>7.7772</td>
    </tr>
  </tbody>
</table>
</div>




```python
petr4.info()
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1638 entries, 0 to 1637
    Data columns (total 2 columns):
     #   Column  Non-Null Count  Dtype  
    ---  ------  --------------  -----  
     0   data    1638 non-null   object 
     1   PETR4   1638 non-null   float64
    dtypes: float64(1), object(1)
    memory usage: 25.7+ KB
    

Perceba que aqui a coluna datas possui não possui suas observações como do tipo data, para isso, basta utilizar aplicar a função pd.to_datetime(). Repare que após essa aplicação, o tipo dos dados será datetime64


```python
petr4['data'] = pd.to_datetime(petr4['data'])
```


```python
print(petr4.info())
```

    <class 'pandas.core.frame.DataFrame'>
    RangeIndex: 1638 entries, 0 to 1637
    Data columns (total 2 columns):
     #   Column  Non-Null Count  Dtype         
    ---  ------  --------------  -----         
     0   data    1638 non-null   datetime64[ns]
     1   PETR4   1638 non-null   float64       
    dtypes: datetime64[ns](1), float64(1)
    memory usage: 25.7 KB
    None
    

Perceba que aqui a coluna datas possui não possui suas observações como do tipo data, para isso, basta utilizar aplicar a função pd.to_datetime(). Repare que após essa aplicação, o tipo dos dados será datetime64


```python
petr4.set_index('data',inplace=True)
```


```python
print(petr4['2015']) # seleciona apenas os dados de 2015
```

                 PETR4
    data              
    2015-01-02  7.9297
    2015-01-05  7.2943
    2015-01-06  7.0571
    2015-01-07  7.3451
    2015-01-08  7.7772
    ...            ...
    2015-12-22  5.7524
    2015-12-23  5.8710
    2015-12-28  5.6762
    2015-12-29  5.6677
    2015-12-30  5.6762
    
    [246 rows x 1 columns]
    

    <ipython-input-21-b2aca8f3671c>:1: FutureWarning: Indexing a DataFrame with a datetimelike index using a single string to slice the rows, like `frame[string]`, is deprecated and will be removed in a future version. Use `frame.loc[string]` instead.
      print(petr4['2015']) # seleciona apenas os dados de 2015
    


```python
print(petr4['2015':'2017'])  # seleciona apenas os dados de 2015 a 2017
```

                  PETR4
    data               
    2015-01-02   7.9297
    2015-01-05   7.2943
    2015-01-06   7.0571
    2015-01-07   7.3451
    2015-01-08   7.7772
    ...             ...
    2017-12-21  13.4364
    2017-12-22  13.3432
    2017-12-26  13.5296
    2017-12-27  13.5974
    2017-12-28  13.6397
    
    [739 rows x 1 columns]
    


```python
print(petr4['2015-06':'2015-08'])  # seleciona apenas os dados de junho a agosto de 2015
```

                  PETR4
    data               
    2015-06-01  10.4797
    2015-06-02  10.8779
    2015-06-03  10.8694
    2015-06-05  10.6407
    2015-06-08  10.6491
    ...             ...
    2015-08-25   6.6928
    2015-08-26   6.8707
    2015-08-27   7.5315
    2015-08-28   7.6247
    2015-08-31   7.7856
    
    [64 rows x 1 columns]
    


```python
print(petr4['2015-06-01':'2015-07-20'])  # seleciona apenas os dados do dia 01 de junho a 20 de julho  de 2015
```

                  PETR4
    data               
    2015-06-01  10.4797
    2015-06-02  10.8779
    2015-06-03  10.8694
    2015-06-05  10.6407
    2015-06-08  10.6491
    2015-06-09  10.9880
    2015-06-10  11.0558
    2015-06-11  11.0558
    2015-06-12  11.0304
    2015-06-15  11.0050
    2015-06-16  11.3099
    2015-06-17  11.1744
    2015-06-18  11.3862
    2015-06-19  11.1575
    2015-06-22  11.1829
    2015-06-23  10.9880
    2015-06-24  11.1829
    2015-06-25  10.6746
    2015-06-26  11.1913
    2015-06-29  10.8016
    2015-06-30  10.7677
    2015-07-01  10.3187
    2015-07-02  10.4204
    2015-07-03   9.9375
    2015-07-06   9.7257
    2015-07-07   9.9799
    2015-07-08   9.7850
    2015-07-10   9.9883
    2015-07-13  10.0138
    2015-07-14  10.1662
    2015-07-15   9.9968
    2015-07-16  10.0985
    2015-07-17   9.6579
    2015-07-20   9.1411
    

Futuramente esse modo será removido , sendo substituído pela seleção por meio do .loc, exemplo:  df.loc['strig_data']


```python
petr4.loc['2015']
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PETR4</th>
    </tr>
    <tr>
      <th>data</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-01-02</th>
      <td>7.9297</td>
    </tr>
    <tr>
      <th>2015-01-05</th>
      <td>7.2943</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>7.0571</td>
    </tr>
    <tr>
      <th>2015-01-07</th>
      <td>7.3451</td>
    </tr>
    <tr>
      <th>2015-01-08</th>
      <td>7.7772</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2015-12-22</th>
      <td>5.7524</td>
    </tr>
    <tr>
      <th>2015-12-23</th>
      <td>5.8710</td>
    </tr>
    <tr>
      <th>2015-12-28</th>
      <td>5.6762</td>
    </tr>
    <tr>
      <th>2015-12-29</th>
      <td>5.6677</td>
    </tr>
    <tr>
      <th>2015-12-30</th>
      <td>5.6762</td>
    </tr>
  </tbody>
</table>
<p>246 rows × 1 columns</p>
</div>



## Funções built in

### pct_change()

O pandas possui uma função nativa para obter a mudança percentual dos valores de uma série, no caso de dados dos preços de fechamento de uma ação, obtemos os retornos diários desse ativo


```python
petr4.pct_change()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PETR4</th>
    </tr>
    <tr>
      <th>data</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-01-02</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-05</th>
      <td>-0.080129</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>-0.032519</td>
    </tr>
    <tr>
      <th>2015-01-07</th>
      <td>0.040810</td>
    </tr>
    <tr>
      <th>2015-01-08</th>
      <td>0.058828</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-08-13</th>
      <td>0.008593</td>
    </tr>
    <tr>
      <th>2021-08-16</th>
      <td>-0.024191</td>
    </tr>
    <tr>
      <th>2021-08-17</th>
      <td>-0.000004</td>
    </tr>
    <tr>
      <th>2021-08-18</th>
      <td>-0.008879</td>
    </tr>
    <tr>
      <th>2021-08-19</th>
      <td>-0.005599</td>
    </tr>
  </tbody>
</table>
<p>1638 rows × 1 columns</p>
</div>



Repare que o primeiro valor da série será um NaN, pois não há valor anterior a tal para calcular a mudança percentual de um dia. 

O primeiro argumento dessa função se refere ao número de períodos que calculamos a diferença percentual. Como padrão, calcula-se a mudança percentual relativa  a 1 dia ( petr4.pct_change(period=1) ), porém podemos passar outros valores


```python
petr4.pct_change(10) #retorno de 10 dias
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PETR4</th>
    </tr>
    <tr>
      <th>data</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-01-02</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-05</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-07</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-08</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-08-13</th>
      <td>0.090675</td>
    </tr>
    <tr>
      <th>2021-08-16</th>
      <td>0.084440</td>
    </tr>
    <tr>
      <th>2021-08-17</th>
      <td>0.066664</td>
    </tr>
    <tr>
      <th>2021-08-18</th>
      <td>0.080120</td>
    </tr>
    <tr>
      <th>2021-08-19</th>
      <td>-0.004350</td>
    </tr>
  </tbody>
</table>
<p>1638 rows × 1 columns</p>
</div>



### diff()

Essa função calcula as diferenças entre as observações periódicas, sua lógica é parecida com a anterior quanto aos períodos


```python
petr4.diff()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PETR4</th>
    </tr>
    <tr>
      <th>data</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-01-02</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-05</th>
      <td>-0.6354</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>-0.2372</td>
    </tr>
    <tr>
      <th>2015-01-07</th>
      <td>0.2880</td>
    </tr>
    <tr>
      <th>2015-01-08</th>
      <td>0.4321</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-08-13</th>
      <td>0.2360</td>
    </tr>
    <tr>
      <th>2021-08-16</th>
      <td>-0.6701</td>
    </tr>
    <tr>
      <th>2021-08-17</th>
      <td>-0.0001</td>
    </tr>
    <tr>
      <th>2021-08-18</th>
      <td>-0.2400</td>
    </tr>
    <tr>
      <th>2021-08-19</th>
      <td>-0.1500</td>
    </tr>
  </tbody>
</table>
<p>1638 rows × 1 columns</p>
</div>




```python
petr4.diff(3)
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PETR4</th>
    </tr>
    <tr>
      <th>data</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-01-02</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-05</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-07</th>
      <td>-0.5846</td>
    </tr>
    <tr>
      <th>2015-01-08</th>
      <td>0.4829</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-08-13</th>
      <td>1.0099</td>
    </tr>
    <tr>
      <th>2021-08-16</th>
      <td>-0.0283</td>
    </tr>
    <tr>
      <th>2021-08-17</th>
      <td>-0.4342</td>
    </tr>
    <tr>
      <th>2021-08-18</th>
      <td>-0.9102</td>
    </tr>
    <tr>
      <th>2021-08-19</th>
      <td>-0.3901</td>
    </tr>
  </tbody>
</table>
<p>1638 rows × 1 columns</p>
</div>



### rolling()

Essa função "agrupa" a série temporal em sub períodos fixos, em outras palavras, cria uma "janela de tempo de mesmo tamanho"  . A partir disso, podemos calcular métricas móveis, como média, desvio padrão, máximo e mínimo. Seu parâmetro principal  é o "window" , que define a "abertura da janela"


```python
petr4.rolling(window=30).mean() #média móvel de 30 dias
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PETR4</th>
    </tr>
    <tr>
      <th>data</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-01-02</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-05</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-07</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-08</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-08-13</th>
      <td>26.068993</td>
    </tr>
    <tr>
      <th>2021-08-16</th>
      <td>26.052007</td>
    </tr>
    <tr>
      <th>2021-08-17</th>
      <td>26.045397</td>
    </tr>
    <tr>
      <th>2021-08-18</th>
      <td>26.067910</td>
    </tr>
    <tr>
      <th>2021-08-19</th>
      <td>26.073467</td>
    </tr>
  </tbody>
</table>
<p>1638 rows × 1 columns</p>
</div>




```python
petr4.rolling(window=30).std() # desv. padr móvel de 30 dias
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PETR4</th>
    </tr>
    <tr>
      <th>data</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-01-02</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-05</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-07</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-08</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-08-13</th>
      <td>0.839203</td>
    </tr>
    <tr>
      <th>2021-08-16</th>
      <td>0.813161</td>
    </tr>
    <tr>
      <th>2021-08-17</th>
      <td>0.804024</td>
    </tr>
    <tr>
      <th>2021-08-18</th>
      <td>0.815404</td>
    </tr>
    <tr>
      <th>2021-08-19</th>
      <td>0.818823</td>
    </tr>
  </tbody>
</table>
<p>1638 rows × 1 columns</p>
</div>




```python
petr4.rolling(window=30).max() # máximo "móvel" de 30 dias
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PETR4</th>
    </tr>
    <tr>
      <th>data</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-01-02</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-05</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-07</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>2015-01-08</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-08-13</th>
      <td>27.7002</td>
    </tr>
    <tr>
      <th>2021-08-16</th>
      <td>27.7002</td>
    </tr>
    <tr>
      <th>2021-08-17</th>
      <td>27.7002</td>
    </tr>
    <tr>
      <th>2021-08-18</th>
      <td>27.7002</td>
    </tr>
    <tr>
      <th>2021-08-19</th>
      <td>27.7002</td>
    </tr>
  </tbody>
</table>
<p>1638 rows × 1 columns</p>
</div>



### Funções para janelas expansoras

Ao invés de termos uma janela com o tempo fixo, podemos ter janelas cujo tamanho aumento ao longo da série. Há duas formas de aplicar esse conceito. A primeira é chamando a função .expanding() e em siga utilizar uma função como mín, max, sum ; ou podemos utlizar funções pré-definidas


```python
petr4.expanding().max() # máximo acumulado 
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PETR4</th>
    </tr>
    <tr>
      <th>data</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-01-02</th>
      <td>7.9297</td>
    </tr>
    <tr>
      <th>2015-01-05</th>
      <td>7.9297</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>7.9297</td>
    </tr>
    <tr>
      <th>2015-01-07</th>
      <td>7.9297</td>
    </tr>
    <tr>
      <th>2015-01-08</th>
      <td>7.9297</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-08-13</th>
      <td>28.4144</td>
    </tr>
    <tr>
      <th>2021-08-16</th>
      <td>28.4144</td>
    </tr>
    <tr>
      <th>2021-08-17</th>
      <td>28.4144</td>
    </tr>
    <tr>
      <th>2021-08-18</th>
      <td>28.4144</td>
    </tr>
    <tr>
      <th>2021-08-19</th>
      <td>28.4144</td>
    </tr>
  </tbody>
</table>
<p>1638 rows × 1 columns</p>
</div>




```python
petr4.cummax() # máximo acumulado 
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>PETR4</th>
    </tr>
    <tr>
      <th>data</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>2015-01-02</th>
      <td>7.9297</td>
    </tr>
    <tr>
      <th>2015-01-05</th>
      <td>7.9297</td>
    </tr>
    <tr>
      <th>2015-01-06</th>
      <td>7.9297</td>
    </tr>
    <tr>
      <th>2015-01-07</th>
      <td>7.9297</td>
    </tr>
    <tr>
      <th>2015-01-08</th>
      <td>7.9297</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-08-13</th>
      <td>28.4144</td>
    </tr>
    <tr>
      <th>2021-08-16</th>
      <td>28.4144</td>
    </tr>
    <tr>
      <th>2021-08-17</th>
      <td>28.4144</td>
    </tr>
    <tr>
      <th>2021-08-18</th>
      <td>28.4144</td>
    </tr>
    <tr>
      <th>2021-08-19</th>
      <td>28.4144</td>
    </tr>
  </tbody>
</table>
<p>1638 rows × 1 columns</p>
</div>



## Lag and Shift

Após entender sobre os tipos de datas e ferramentas, vamos aprender a métodos que manipulam a Time Series, e a primeira manipulação que aprenderemos é como mover os dados ao longo da série temporal. Para isso, usa-se o método do pandas .shift(), que pode ser usado tanto com Series quanto DataFrames. O primeiro parâmetro do método .shift() é o periods, e é nele que definimos se queremos mover os dados para o futuro ou passado, bem como a quantidade de períodos que moveremos os dados.

Basicamente, o número inteiro atribuído ao periods será a quantidade de períodos que os dados se moverão para o futuro no caso dos números positivos ou passado no caso dos números negativos. 

Exemplificando: num contexto econômico, gostaria de calcular quanto a venda de veículos cresceu em um mês para o outro ou de um ano para o outro.


```python
vendas = pd.read_csv('vendas_fenabrave.csv', sep=';', index_col=0)
vendas
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TOTAL Geral</th>
    </tr>
    <tr>
      <th>Período</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>01/01/1979</th>
      <td>86.374</td>
    </tr>
    <tr>
      <th>01/02/1979</th>
      <td>74.507</td>
    </tr>
    <tr>
      <th>01/03/1979</th>
      <td>63.835</td>
    </tr>
    <tr>
      <th>01/04/1979</th>
      <td>84.263</td>
    </tr>
    <tr>
      <th>01/05/1979</th>
      <td>89.071</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>01/04/2021</th>
      <td>269.798</td>
    </tr>
    <tr>
      <th>01/05/2021</th>
      <td>299.054</td>
    </tr>
    <tr>
      <th>01/06/2021</th>
      <td>289.158</td>
    </tr>
    <tr>
      <th>01/07/2021</th>
      <td>288.025</td>
    </tr>
    <tr>
      <th>01/08/2021</th>
      <td>275.507</td>
    </tr>
  </tbody>
</table>
<p>512 rows × 1 columns</p>
</div>



Tratando-se de uma Time Series de frequência mensal, para acharmos a variação mensal (também conhecido como month-over-month) dividimos a série atual pela série movimentada um período para frente. O resultado da divisão deve ser subtraído por um e multiplicado por cem para termos o valor em porcentagem.


```python
fenabrave_mom = ((vendas / vendas.shift(1)) - 1) * 100
fenabrave_mom
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TOTAL Geral</th>
    </tr>
    <tr>
      <th>Período</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>01/01/1979</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>01/02/1979</th>
      <td>-13.739088</td>
    </tr>
    <tr>
      <th>01/03/1979</th>
      <td>-14.323486</td>
    </tr>
    <tr>
      <th>01/04/1979</th>
      <td>32.001253</td>
    </tr>
    <tr>
      <th>01/05/1979</th>
      <td>5.705944</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>01/04/2021</th>
      <td>7.199250</td>
    </tr>
    <tr>
      <th>01/05/2021</th>
      <td>10.843668</td>
    </tr>
    <tr>
      <th>01/06/2021</th>
      <td>-3.309101</td>
    </tr>
    <tr>
      <th>01/07/2021</th>
      <td>-0.391827</td>
    </tr>
    <tr>
      <th>01/08/2021</th>
      <td>-4.346151</td>
    </tr>
  </tbody>
</table>
<p>512 rows × 1 columns</p>
</div>



Tratando-se de uma Time Series de frequência mensal, para acharmos a variação mensal (também conhecido como month-over-month) dividimos a série atual pela série movimentada um período para frente. O resultado da divisão deve ser subtraído por um e multiplicado por cem para termos o valor em porcentagem.


```python
fenabrave_yoy = ((vendas / vendas.shift(12)) - 1) * 100
fenabrave_yoy
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TOTAL Geral</th>
    </tr>
    <tr>
      <th>Período</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>01/01/1979</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>01/02/1979</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>01/03/1979</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>01/04/1979</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>01/05/1979</th>
      <td>NaN</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>01/04/2021</th>
      <td>221.337288</td>
    </tr>
    <tr>
      <th>01/05/2021</th>
      <td>227.224781</td>
    </tr>
    <tr>
      <th>01/06/2021</th>
      <td>61.825559</td>
    </tr>
    <tr>
      <th>01/07/2021</th>
      <td>10.923473</td>
    </tr>
    <tr>
      <th>01/08/2021</th>
      <td>-1.390872</td>
    </tr>
  </tbody>
</table>
<p>512 rows × 1 columns</p>
</div>



A o método .shift() conta com outros parâmetros que poderão ser usados para ampliar a análise, destaco os parâmetros axis e fill_value. O primeiro, você poderá indicar em qual sentido você deseja movimentar sua série, o valor padrão é 0 e você movimenta pelo index, mas também poderá usar o 1 para movimentar pelas colunas. Já o segundo serve para preenchermos os valores que ficariam NaN ao movimentar a série, ou seja, caso movimentássemos para o futuro, os primeiros valores ficariam NaN, caso para o passado os últimos, portanto podemos usar o parâmetro para decidirmos o que gostaríamos de preencher nesses valores faltantes. 

Apresentamos o exemplo de variações percentuais que é simples e pode até ser substituído por outros métodos, mas a movimentação da Time Series ao longo do tempo é útil para qualquer tipo de cálculo que envolva a comparação do valor num período de tempo com outro.

## Resampling

As séries temporais possuem frequências específica, porém, muitas vezes desejamos mudar a frequência para fins de cálculo, apresentação ou comparação entre duas séries. Para fazer isso, usaremos o método .resample(), há outras formas de converter frequência no Python, porém focaremos nesse método por ser mais geral. Basicamente, basta aplicar o método em sua Time Series, passando o primeiro argumento como uma string que representa a frequência que você deseja passar a Time Series. Coloquei abaixo um dicionário de todas as siglas para cada tipo de frequência:

B         business day frequency

C         custom business day frequency (experimental)

D         calendar day frequency

W         weekly frequency

M         month end frequency

SM        semi-month end frequency (15th and end of month)

BM        business month end frequency

CBM       custom business month end frequency

MS        month start frequency

SMS       semi-month start frequency (1st and 15th)

BMS       business month start frequency

CBMS      custom business month start frequency

Q         quarter end frequency

BQ        business quarter endfrequency

QS        quarter start frequency

BQS       business quarter start frequency

A         year end frequency

BA, BY    business year end frequency

AS, YS    year start frequency

BAS, BYS  business year start frequency

BH        business hour frequency

H         hourly frequency

T, min    minutely frequency

S         secondly frequency

L, ms     milliseconds

U, us     microseconds

N         nanoseconds

Para fins didáticos, usaremos as frequência ‘M’ e ‘D’, pois são mais comuns e facilitará a explicação. De modo geral existe dois tipo de resampling, o downsampling e o upsampling, o primeiro é quando seus dados estão em uma frequência maior que o desejado, enquanto o segundo é quando a frequência é menor.

No downsampling, devemos agregar os dados para formar a time series numa sequência menor, tradicionalmente podemos agregar os dados tirando a média ou a mediana. Para isso, após o método .resample() usamos outro método, o .mean() no caso de tirar a média e o .median() para a mediana. Usando como exemplo a mesma série de vendas de carro do Fenabrave, temos:


```python
vendas.index = pd.to_datetime(vendas.index, format = "%d/%m/%Y")
vendas.resample('Y').mean()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TOTAL Geral</th>
    </tr>
    <tr>
      <th>Período</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1979-12-31</th>
      <td>84.577083</td>
    </tr>
    <tr>
      <th>1980-12-31</th>
      <td>81.688417</td>
    </tr>
    <tr>
      <th>1981-12-31</th>
      <td>48.393750</td>
    </tr>
    <tr>
      <th>1982-12-31</th>
      <td>57.607833</td>
    </tr>
    <tr>
      <th>1983-12-31</th>
      <td>60.644333</td>
    </tr>
    <tr>
      <th>1984-12-31</th>
      <td>56.423500</td>
    </tr>
    <tr>
      <th>1985-12-31</th>
      <td>63.598333</td>
    </tr>
    <tr>
      <th>1986-12-31</th>
      <td>86.074000</td>
    </tr>
    <tr>
      <th>1987-12-31</th>
      <td>62.974833</td>
    </tr>
    <tr>
      <th>1988-12-31</th>
      <td>75.532250</td>
    </tr>
    <tr>
      <th>1989-12-31</th>
      <td>76.270167</td>
    </tr>
    <tr>
      <th>1990-12-31</th>
      <td>69.656417</td>
    </tr>
    <tr>
      <th>1991-12-31</th>
      <td>74.829417</td>
    </tr>
    <tr>
      <th>1992-12-31</th>
      <td>68.651000</td>
    </tr>
    <tr>
      <th>1993-12-31</th>
      <td>97.633000</td>
    </tr>
    <tr>
      <th>1994-12-31</th>
      <td>129.469083</td>
    </tr>
    <tr>
      <th>1995-12-31</th>
      <td>156.190500</td>
    </tr>
    <tr>
      <th>1996-12-31</th>
      <td>166.526833</td>
    </tr>
    <tr>
      <th>1997-12-31</th>
      <td>193.310250</td>
    </tr>
    <tr>
      <th>1998-12-31</th>
      <td>166.480500</td>
    </tr>
    <tr>
      <th>1999-12-31</th>
      <td>133.978333</td>
    </tr>
    <tr>
      <th>2000-12-31</th>
      <td>165.195250</td>
    </tr>
    <tr>
      <th>2001-12-31</th>
      <td>188.960500</td>
    </tr>
    <tr>
      <th>2002-12-31</th>
      <td>184.712583</td>
    </tr>
    <tr>
      <th>2003-12-31</th>
      <td>188.571417</td>
    </tr>
    <tr>
      <th>2004-12-31</th>
      <td>206.178000</td>
    </tr>
    <tr>
      <th>2005-12-31</th>
      <td>228.411000</td>
    </tr>
    <tr>
      <th>2006-12-31</th>
      <td>268.763333</td>
    </tr>
    <tr>
      <th>2007-12-31</th>
      <td>347.593667</td>
    </tr>
    <tr>
      <th>2008-12-31</th>
      <td>395.469500</td>
    </tr>
    <tr>
      <th>2009-12-31</th>
      <td>395.829750</td>
    </tr>
    <tr>
      <th>2010-12-31</th>
      <td>443.214167</td>
    </tr>
    <tr>
      <th>2011-12-31</th>
      <td>464.195750</td>
    </tr>
    <tr>
      <th>2012-12-31</th>
      <td>453.276167</td>
    </tr>
    <tr>
      <th>2013-12-31</th>
      <td>440.239583</td>
    </tr>
    <tr>
      <th>2014-12-31</th>
      <td>410.646000</td>
    </tr>
    <tr>
      <th>2015-12-31</th>
      <td>320.189750</td>
    </tr>
    <tr>
      <th>2016-12-31</th>
      <td>254.029333</td>
    </tr>
    <tr>
      <th>2017-12-31</th>
      <td>257.550833</td>
    </tr>
    <tr>
      <th>2018-12-31</th>
      <td>292.214917</td>
    </tr>
    <tr>
      <th>2019-12-31</th>
      <td>322.114750</td>
    </tr>
    <tr>
      <th>2020-12-31</th>
      <td>247.819167</td>
    </tr>
    <tr>
      <th>2021-12-31</th>
      <td>269.375000</td>
    </tr>
  </tbody>
</table>
</div>



Ou seja, a série foi convertida para a frequência anual e os valores refere-se a média de vendas dos 12 meses de cada ano.

No upsampling o processo é semelhante, entretanto nesse caso desejamos acrescentar dados, portanto, podemos usar os métodos .interpolate() e .ffill(), o primeiro adiciona um ponto na linha reta entre os outros dois pontos da série, enquanto o segundo mantém o último valor. Exemplo:


```python
vendas.resample('D').interpolate()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TOTAL Geral</th>
    </tr>
    <tr>
      <th>Período</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1979-01-01</th>
      <td>86.374000</td>
    </tr>
    <tr>
      <th>1979-01-02</th>
      <td>85.991194</td>
    </tr>
    <tr>
      <th>1979-01-03</th>
      <td>85.608387</td>
    </tr>
    <tr>
      <th>1979-01-04</th>
      <td>85.225581</td>
    </tr>
    <tr>
      <th>1979-01-05</th>
      <td>84.842774</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-07-28</th>
      <td>277.122226</td>
    </tr>
    <tr>
      <th>2021-07-29</th>
      <td>276.718419</td>
    </tr>
    <tr>
      <th>2021-07-30</th>
      <td>276.314613</td>
    </tr>
    <tr>
      <th>2021-07-31</th>
      <td>275.910806</td>
    </tr>
    <tr>
      <th>2021-08-01</th>
      <td>275.507000</td>
    </tr>
  </tbody>
</table>
<p>15554 rows × 1 columns</p>
</div>




```python
vendas.resample('D').ffill()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>TOTAL Geral</th>
    </tr>
    <tr>
      <th>Período</th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1979-01-01</th>
      <td>86.374</td>
    </tr>
    <tr>
      <th>1979-01-02</th>
      <td>86.374</td>
    </tr>
    <tr>
      <th>1979-01-03</th>
      <td>86.374</td>
    </tr>
    <tr>
      <th>1979-01-04</th>
      <td>86.374</td>
    </tr>
    <tr>
      <th>1979-01-05</th>
      <td>86.374</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
    </tr>
    <tr>
      <th>2021-07-28</th>
      <td>288.025</td>
    </tr>
    <tr>
      <th>2021-07-29</th>
      <td>288.025</td>
    </tr>
    <tr>
      <th>2021-07-30</th>
      <td>288.025</td>
    </tr>
    <tr>
      <th>2021-07-31</th>
      <td>288.025</td>
    </tr>
    <tr>
      <th>2021-08-01</th>
      <td>275.507</td>
    </tr>
  </tbody>
</table>
<p>15554 rows × 1 columns</p>
</div>



Há outros métodos que poderão ser usados para agregar ou preencher os dados no resampling, apresentamos os mais usados e tudo dependerá de qual fará mais sentido para sua série.

# Resumo 

Vimos ao longo do artigo diversas ferramentas básicas que serão essenciais para possamos futuramente fazer análises e desenvolver modelos de séries temporais. Foram elas

- Datas e Indexing: para facilitar a leitura de dados de séries temporais 
- Lag e shift: manipular a série através do tempo
- Funções que produzem informações relevantes sobre os dados, como:
    - pct_change
    - diff()
    - Rolling window
    - Acumulativas 
- Resampling: convertendo instâncias de tempo


Vale lembrar que esse é apenas um recorte do tema, para mais informações visite as documentações dos pacotes mencionados. 

----

# Referências

Cap 10 e 11 : Python for Data Analysis

[https://docs.python.org/pt-br/3/library/datetime.html](https://docs.python.org/pt-br/3/library/datetime.html) 

[https://realpython.com/python-datetime/](https://realpython.com/python-datetime/) 

[https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.pct_change.html](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.pct_change.html) 

[https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.expanding.html](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.expanding.html)

[https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rolling.html](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rolling.html)
