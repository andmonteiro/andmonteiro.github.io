---
layout: post
title: "Análise dos Dados do Airbnb em Portugal"
subtitle: "Neste notebook, iremos analisar os dados referentes ao Distrito de Lisboa, e ver quais insights podem ser extraídos a partir de dados brutos."
background: '/img/posts/analise-airbnb/analise-airbnb.jpeg'
---


O [Airbnb](https://www.airbnb.com.br/) já é considerado como sendo a **maior empresa hoteleira da atualidade**. O detalhe é que ele **não possui nenhum hotel**!

Conectando pessoas que querem viajar (e se hospedar) com anfitriões que querem alugar seus imóveis de maneira prática, o Airbnb fornece uma plataforma inovadora para tornar essa hospedagem alternativa.

No final de 2018, a Startup fundada 10 anos atrás, já havia **hospedado mais de 300 milhões** de pessoas ao redor de todo o mundo, desafiando as redes hoteleiras tradicionais.

Uma das iniciativas do Airbnb é disponibilizar dados do site, para algumas das principais cidades do mundo. Por meio do portal [Inside Airbnb](http://insideairbnb.com/get-the-data.html), é possível baixar uma grande quantidade de dados para desenvolver projetos e soluções de *Data Science*.

<center><img alt="Analisando Airbnb" width="30%" src="https://www.area360.com.au/wp-content/uploads/2017/09/airbnb-logo.jpg"></center>

**Neste *notebook*, iremos analisar os dados referentes ao Distrito de Lisboa, e ver quais insights podem ser extraídos a partir de dados brutos.**

Lisboa é a capital de Portugal e é uma das cidades mais carismáticas e vibrantes da Europa. É uma cidade que mistura o patrimônio tradicional sem esforço, com um impressionante modernismo. Como um destino de férias, Lisboa oferece uma história rica e variada, uma agitada vida noturna e é abençoada com um clima glorioso durante o ano todo.

Lisboa é uma cidade movimentada e animada, possuindo uma grande variedade de atividades e atrações turísticas fascinantes. A cidade tem uma atmosfera acolhedora e liberal, enquanto ainda abraça a sua herança profundamente enraizada e história vasta. Lisboa irá atrair uma seleção diversificada de idades e turistas; pode formar uma viagem cultural, uma extravagância da vida noturna, férias em família, uma pausa relaxante na cidade ou até mesmo como base para umas férias na praia.

A capital portuguesa é reconhecida constantemente como uma das melhores cidades do mundo, uma afirmação confirmada por “Lonely Planet Guides”, que nomeou Lisboa como uma das 10 melhores cidades do mundo. Surpreendentemente, Lisboa ainda é uma das capitais menos visitadas da Europa, mas isso está mudando rapidamente à medida que novos visitantes descobrem o fascínio de Portugal.

Fonte: https://lisbonlisboaportugal.com/lisboa-portugal-guia-br.html

#### Obtenção dos Dados
Os dados usados foram obtidos do site [Inside Airbnb](https://insideairbnb.com/get-the-data.html), com a data de compilação em 28 de maio de 2020.


```python
# Iremos importar os pacotes necessários
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

%matplotlib inline
```


```python
# importando o arquivo listings.csv para um DataFrame
df = pd.read_csv("http://data.insideairbnb.com/portugal/lisbon/lisbon/2020-04-29/visualisations/listings.csv")
```

## Análise dos Dados

**Dicionário das variáveis**

* `id` - número de id gerado para identificar o imóvel
* `name` - nome da propriedade anunciada
* `host_id` - número de id do proprietário (anfitrião) da propriedade
* `host_name` - Nome do anfitrião
* `neighbourhood_group` - município
* `neighbourhood` - bairro
* `latitude` - coordenada da latitude da propriedade
* `longitude` - coordenada da longitude da propriedade
* `room_type` - informa o tipo de quarto que é oferecido
* `price` - preço para alugar o imóvel
* `minimum_nights` - quantidade mínima de noites para reservar
* `number_of_reviews` - número de reviews que a propriedade possui
* `last_review` - data do último review
* `reviews_per_month` - quantidade de reviews por mês
* `calculated_host_listings_count` - quantidade de imóveis do mesmo anfitrião
* `availability_365` - número de dias de disponibilidade dentro de 365 dias

Antes de iniciar qualquer análise, vamos verificar a cara do nosso *dataset*, analisando as 5 primeiras entradas.


```python
# mostrar as 5 primeiras entradas
df.head()
```

<div style="overflow-y: scroll; height:auto; overflow-x: scroll; width:auto;">
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
      <th>id</th>
      <th>name</th>
      <th>host_id</th>
      <th>host_name</th>
      <th>neighbourhood_group</th>
      <th>neighbourhood</th>
      <th>latitude</th>
      <th>longitude</th>
      <th>room_type</th>
      <th>price</th>
      <th>minimum_nights</th>
      <th>number_of_reviews</th>
      <th>last_review</th>
      <th>reviews_per_month</th>
      <th>calculated_host_listings_count</th>
      <th>availability_365</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6499</td>
      <td>Belém 1 Bedroom Historical Apartment</td>
      <td>14455</td>
      <td>Bruno</td>
      <td>Lisboa</td>
      <td>Belm</td>
      <td>38.69750</td>
      <td>-9.19768</td>
      <td>Entire home/apt</td>
      <td>40</td>
      <td>3</td>
      <td>26</td>
      <td>2020-01-03</td>
      <td>0.38</td>
      <td>1</td>
      <td>307</td>
    </tr>
    <tr>
      <th>1</th>
      <td>25659</td>
      <td>Sunny,  Alfama  Sleeps 3 - Coeur d'Alfama</td>
      <td>107347</td>
      <td>Ellie</td>
      <td>Lisboa</td>
      <td>Santa Maria Maior</td>
      <td>38.71167</td>
      <td>-9.12696</td>
      <td>Entire home/apt</td>
      <td>60</td>
      <td>3</td>
      <td>113</td>
      <td>2019-12-08</td>
      <td>1.54</td>
      <td>1</td>
      <td>317</td>
    </tr>
    <tr>
      <th>2</th>
      <td>29248</td>
      <td>Apartamento Alfama com vista para o rio!</td>
      <td>125768</td>
      <td>Bárbara</td>
      <td>Lisboa</td>
      <td>Santa Maria Maior</td>
      <td>38.71272</td>
      <td>-9.12628</td>
      <td>Entire home/apt</td>
      <td>60</td>
      <td>3</td>
      <td>321</td>
      <td>2020-03-11</td>
      <td>2.82</td>
      <td>1</td>
      <td>351</td>
    </tr>
    <tr>
      <th>3</th>
      <td>29396</td>
      <td>Alfama Hill - Boutique apartment</td>
      <td>126415</td>
      <td>Mónica</td>
      <td>Lisboa</td>
      <td>Santa Maria Maior</td>
      <td>38.71239</td>
      <td>-9.12887</td>
      <td>Entire home/apt</td>
      <td>60</td>
      <td>1</td>
      <td>239</td>
      <td>2020-03-15</td>
      <td>2.47</td>
      <td>2</td>
      <td>318</td>
    </tr>
    <tr>
      <th>4</th>
      <td>29720</td>
      <td>TheHOUSE - Your luxury home</td>
      <td>128075</td>
      <td>Francisco</td>
      <td>Lisboa</td>
      <td>Estrela</td>
      <td>38.71195</td>
      <td>-9.15877</td>
      <td>Entire home/apt</td>
      <td>1100</td>
      <td>2</td>
      <td>55</td>
      <td>2020-03-15</td>
      <td>0.48</td>
      <td>2</td>
      <td>300</td>
    </tr>
  </tbody>
</table>
</div>



### **Q1. Quantos atributos (variáveis) e quantas entradas o nosso conjunto de dados possui? Quais os tipos das variáveis?**

Vamos prosseguir e identificar a quantidade de entradas que nosso conjunto de dados possui e ver os tipos de cada coluna.


```python
# identificar o volume de dados do DataFrame
print("Entradas:\t {}".format(df.shape[0]))
print("Variáveis:\t {}\n".format(df.shape[1]))

# verificar as 5 primeiras entradas do dataset
display(df.dtypes)
```

    Entradas:	 24640
    Variáveis:	 16
    
    


    id                                  int64
    name                               object
    host_id                             int64
    host_name                          object
    neighbourhood_group                object
    neighbourhood                      object
    latitude                          float64
    longitude                         float64
    room_type                          object
    price                               int64
    minimum_nights                      int64
    number_of_reviews                   int64
    last_review                        object
    reviews_per_month                 float64
    calculated_host_listings_count      int64
    availability_365                    int64
    dtype: object


**Este *dataset* que baixamos é a versão "resumida" do Airbnb, que contém 24640 entradas e 16 variáveis.**

### **Q2. Qual a porcentagem de valores ausentes no *dataset*?**

A qualidade de um *dataset* está diretamente relacionada à quantidade de valores ausentes. É importante entender logo no início se esses valores nulos são significativos comparados ao total de entradas.



```python
# Verificar valores ausentes no dataset e colocar em ordem decrescente
(df.isnull().sum() / df.shape[0]).sort_values(ascending=False)
```




    reviews_per_month                 0.168547
    last_review                       0.168547
    name                              0.000771
    host_name                         0.000041
    availability_365                  0.000000
    calculated_host_listings_count    0.000000
    number_of_reviews                 0.000000
    minimum_nights                    0.000000
    price                             0.000000
    room_type                         0.000000
    longitude                         0.000000
    latitude                          0.000000
    neighbourhood                     0.000000
    neighbourhood_group               0.000000
    host_id                           0.000000
    id                                0.000000
    dtype: float64



* É possível ver que a coluna `reviews_per_month` possui 16,8% dos seus valores faltantes.
* É possível ver que a coluna `last_review` possui 16,8% dos seus valores faltantes. 

### **Q3. Qual o tipo de distribuição das variáveis?** 

Para identificar a distribuição das variáveis, irei plotar o histograma.


```python
# plotar o histograma das variáveis numéricas
df.hist(bins=15, figsize=(15,10));
```


    
![png](\img\posts\analise-airbnb\output_14_0.png)
    


### **Q4. Há *outliers* presentes?**

Pela distribuição do histograma, é possível verificar indícios da presença de *outliers*. Olhe por exemplo as variáveis `price`, `minimum_nights` e `calculated_host_listings_count`.

Os valores não seguem uma distruição, e distorcem toda a representação gráfica. Para confirmar, há duas maneiras rápidas que auxiliam a detecção de *outliers*. São elas:

* Resumo estatístico por meio do método `describe()`
* Plotar `boxplots` para a variável.


```python
# ver o resumo estatístico das variáveis numéricas
df[['price', 'minimum_nights', 'number_of_reviews', 'reviews_per_month',
    'calculated_host_listings_count', 'availability_365']].describe()
```




<div style="overflow-y: scroll; height:auto; overflow-x: scroll; width:auto;">
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
      <th>price</th>
      <th>minimum_nights</th>
      <th>number_of_reviews</th>
      <th>reviews_per_month</th>
      <th>calculated_host_listings_count</th>
      <th>availability_365</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>24640.000000</td>
      <td>24640.000000</td>
      <td>24640.000000</td>
      <td>20487.000000</td>
      <td>24640.000000</td>
      <td>24640.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>110.739732</td>
      <td>3.294440</td>
      <td>41.781291</td>
      <td>1.459542</td>
      <td>15.057224</td>
      <td>225.239448</td>
    </tr>
    <tr>
      <th>std</th>
      <td>350.763230</td>
      <td>15.970015</td>
      <td>65.881125</td>
      <td>1.461515</td>
      <td>43.355842</td>
      <td>132.790977</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>47.000000</td>
      <td>1.000000</td>
      <td>2.000000</td>
      <td>0.320000</td>
      <td>1.000000</td>
      <td>118.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>69.000000</td>
      <td>2.000000</td>
      <td>14.000000</td>
      <td>0.960000</td>
      <td>3.000000</td>
      <td>269.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>100.000000</td>
      <td>3.000000</td>
      <td>53.000000</td>
      <td>2.210000</td>
      <td>10.000000</td>
      <td>349.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>10000.000000</td>
      <td>1000.000000</td>
      <td>877.000000</td>
      <td>19.890000</td>
      <td>340.000000</td>
      <td>365.000000</td>
    </tr>
  </tbody>
</table>
</div>



## Olhando o resumo estatístico acima, podemos confirmar algumas hipóteses como:

* A variável `price` possui 75% do valor abaixo de 100, porém seu valor máximo é 10000.
* A quantidade mínima de noites (`minimum_nights`) está acima de 365 dias no ano.

#### Boxplot para minimum_nights


```python
# minimum_nights
df.minimum_nights.plot(kind='box', vert=False, figsize=(15, 3))
plt.show()

# ver quantidade de valores acima de 30 dias para minimum_nights
print("minimum_nights: valores acima de 30:")
print("{} entradas".format(len(df[df.minimum_nights > 30])))
print("{:.4f}%".format((len(df[df.minimum_nights > 30]) / df.shape[0])*100))
```


    
![png](\img\posts\analise-airbnb\output_20_0.png)
    


    minimum_nights: valores acima de 30:
    100 entradas
    0.4058%
    

#### Boxplot para price


```python
# price
df.price.plot(kind='box', vert=False, figsize=(15, 3),)
plt.show()

# ver quantidade de valores acima de 1500 para price
print("\nprice: valores acima de 1500")
print("{} entradas".format(len(df[df.price > 1500])))
print("{:.4f}%".format((len(df[df.price > 1500]) / df.shape[0])*100))

# df.price.plot(kind='box', vert=False, xlim=(0,1300), figsize=(15,3));
```


    
![png](\img\posts\analise-airbnb\output_22_0.png)
    


    
    price: valores acima de 1500
    57 entradas
    0.2313%
    

#### Histogramas sem *outliers*

Já que identificamos *outliers* nas variáveis `price` e `minimum_nights`, vamos agora limpar o *DataFrame* delas e plotar novamente o histograma.


```python
# remover os *outliers* em um novo DataFrame
df_clean = df.copy()
df_clean.drop(df_clean[df_clean.price > 1500].index, axis=0, inplace=True)
df_clean.drop(df_clean[df_clean.minimum_nights > 30].index, axis=0, inplace=True)
df_clean.drop(df_clean[df_clean.calculated_host_listings_count > 60].index, axis=0, inplace=True)
```


```python
# plotar o histograma para as variáveis numéricas
df_clean.hist(bins=15, figsize=(15,10));
```


    
![png](\img\posts\analise-airbnb\output_25_0.png)
    



```python
print('Quantidade de variáveis {}'.format(df_clean.shape[0]))
print('Quantidade de entradas {}'.format(df_clean.shape[1]))
df_limpo = (df_clean.shape[0] / df.shape[0]) -1
print('Foram removidos {:.2%} dos dados na limpeza do dataset.'.format(df_limpo * (-1)))
```

    Quantidade de variáveis 23220
    Quantidade de entradas 16
    Foram removidos 5.76% dos dados na limpeza do dataset.
    

### Resumo estatístico após limpeza dos outliers


```python
df_clean[['price', 'minimum_nights','number_of_reviews', 'reviews_per_month', 'calculated_host_listings_count', 
          'availability_365']].describe()
```




<div style="overflow-y: scroll; height:auto; overflow-x: scroll; width:auto;">
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
      <th>price</th>
      <th>minimum_nights</th>
      <th>number_of_reviews</th>
      <th>reviews_per_month</th>
      <th>calculated_host_listings_count</th>
      <th>availability_365</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>23220.000000</td>
      <td>23220.000000</td>
      <td>23220.000000</td>
      <td>19336.000000</td>
      <td>23220.000000</td>
      <td>23220.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>97.228424</td>
      <td>2.716968</td>
      <td>43.391602</td>
      <td>1.502402</td>
      <td>7.262317</td>
      <td>224.608570</td>
    </tr>
    <tr>
      <th>std</th>
      <td>124.306829</td>
      <td>3.384602</td>
      <td>67.243900</td>
      <td>1.483627</td>
      <td>10.123960</td>
      <td>132.692619</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>0.000000</td>
      <td>0.010000</td>
      <td>1.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>45.000000</td>
      <td>1.000000</td>
      <td>2.000000</td>
      <td>0.330000</td>
      <td>1.000000</td>
      <td>117.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>69.000000</td>
      <td>2.000000</td>
      <td>14.000000</td>
      <td>1.010000</td>
      <td>3.000000</td>
      <td>269.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>100.000000</td>
      <td>3.000000</td>
      <td>56.000000</td>
      <td>2.300000</td>
      <td>8.000000</td>
      <td>349.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>1500.000000</td>
      <td>30.000000</td>
      <td>877.000000</td>
      <td>19.890000</td>
      <td>60.000000</td>
      <td>365.000000</td>
    </tr>
  </tbody>
</table>
</div>



### **Q5. Qual a correlação existente entre as variáveis?**

Correlação significa que existe uma relação entre duas coisas. No nosso contexto, estamos buscando relação ou semelhança entre duas variáveis.

Essa relação pode ser medida, e é função do coeficiente de correlação estabelecer qual a intensidade dela. Para identificar as correlações existentes entre as variáveis de interesse, vou:

* Criar uma matriz de correlação
* Gerar um *heatmap* a partir dessa matriz, usando a biblioteca `seaborn`


```python
# criar uma matriz de correlação
corr = df_clean[['price', 'minimum_nights', 'number_of_reviews', 'reviews_per_month',
    'calculated_host_listings_count', 'availability_365']].corr()

display(corr)
```


<div style="overflow-y: scroll; height:auto; overflow-x: scroll; width:auto;">
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
      <th>price</th>
      <th>minimum_nights</th>
      <th>number_of_reviews</th>
      <th>reviews_per_month</th>
      <th>calculated_host_listings_count</th>
      <th>availability_365</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>price</th>
      <td>1.000000</td>
      <td>-0.035038</td>
      <td>-0.126661</td>
      <td>-0.124047</td>
      <td>-0.002353</td>
      <td>0.010003</td>
    </tr>
    <tr>
      <th>minimum_nights</th>
      <td>-0.035038</td>
      <td>1.000000</td>
      <td>-0.051181</td>
      <td>-0.101343</td>
      <td>-0.049146</td>
      <td>-0.023528</td>
    </tr>
    <tr>
      <th>number_of_reviews</th>
      <td>-0.126661</td>
      <td>-0.051181</td>
      <td>1.000000</td>
      <td>0.780244</td>
      <td>-0.072072</td>
      <td>0.085859</td>
    </tr>
    <tr>
      <th>reviews_per_month</th>
      <td>-0.124047</td>
      <td>-0.101343</td>
      <td>0.780244</td>
      <td>1.000000</td>
      <td>-0.063167</td>
      <td>0.076246</td>
    </tr>
    <tr>
      <th>calculated_host_listings_count</th>
      <td>-0.002353</td>
      <td>-0.049146</td>
      <td>-0.072072</td>
      <td>-0.063167</td>
      <td>1.000000</td>
      <td>0.040110</td>
    </tr>
    <tr>
      <th>availability_365</th>
      <td>0.010003</td>
      <td>-0.023528</td>
      <td>0.085859</td>
      <td>0.076246</td>
      <td>0.040110</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



```python
sns.heatmap(corr, cmap='RdBu', fmt='.2f', square=True, linecolor='white', annot=True);
```


    
![png](\img\posts\analise-airbnb\output_31_0.png)
    


### **Q6. Qual o tipo de imóvel mais alugado no Airbnb?**

A coluna da variável `room_type` indica o tipo de locação que está anunciada no Airbnb. Se você já alugou no site, sabe que existem opções de apartamentos/casas inteiras, apenas o aluguel de um quarto ou mesmo dividir o quarto com outras pessoas.

Vamos contar a quantidade de ocorrências de cada tipo de aluguel, usando o método `value_counts()`.


```python
# mostrar a quantidade de cada tipo de imóvel disponível
df_clean.room_type.value_counts()
```




    Entire home/apt    16618
    Private room        5588
    Hotel room           571
    Shared room          443
    Name: room_type, dtype: int64




```python
#gráfico representando por tipo de imóvel
plt.figure(figsize=(15,5))
sns.countplot(df_clean['room_type'])
plt.title('Tipos de imóveis para se alugar');
```


    
![png](\img\posts\analise-airbnb\output_34_0.png)
    



```python
# mostrar a porcentagem de cada tipo de imóvel disponível
a = (df_clean.room_type.value_counts() / df_clean.shape[0])
df_clean_a = pd.DataFrame(a)
df_clean_a.style.format('{:.2%}')
```




<style  type="text/css" >
</style><table id="T_61baa950_af1e_11ea_aaa9_408d5cd9f65f" ><thead>    <tr>        <th class="blank level0" ></th>        <th class="col_heading level0 col0" >room_type</th>    </tr></thead><tbody>
                <tr>
                        <th id="T_61baa950_af1e_11ea_aaa9_408d5cd9f65flevel0_row0" class="row_heading level0 row0" >Entire home/apt</th>
                        <td id="T_61baa950_af1e_11ea_aaa9_408d5cd9f65frow0_col0" class="data row0 col0" >71.57%</td>
            </tr>
            <tr>
                        <th id="T_61baa950_af1e_11ea_aaa9_408d5cd9f65flevel0_row1" class="row_heading level0 row1" >Private room</th>
                        <td id="T_61baa950_af1e_11ea_aaa9_408d5cd9f65frow1_col0" class="data row1 col0" >24.07%</td>
            </tr>
            <tr>
                        <th id="T_61baa950_af1e_11ea_aaa9_408d5cd9f65flevel0_row2" class="row_heading level0 row2" >Hotel room</th>
                        <td id="T_61baa950_af1e_11ea_aaa9_408d5cd9f65frow2_col0" class="data row2 col0" >2.46%</td>
            </tr>
            <tr>
                        <th id="T_61baa950_af1e_11ea_aaa9_408d5cd9f65flevel0_row3" class="row_heading level0 row3" >Shared room</th>
                        <td id="T_61baa950_af1e_11ea_aaa9_408d5cd9f65frow3_col0" class="data row3 col0" >1.91%</td>
            </tr>
    </tbody></table>



Podemos concluir que:
* Há 16618 opções de aluguel de apartamentos/casas, sendo 71,57% das opções de locação.
* Há 5588 opções de aluguel de quartos privados, sendo 24,07% das opções de locação.
* Há 571 opções de aluguel de quartos compartilhados, sendo 2,46% das opções de locação.
* Há 443 opções de aluguel em hotel, sendo 1,91% das opções de locação.

### **Q7. Qual a localidade mais cara de Lisboa?**

Uma maneira de se verificar uma variável em função da outra é usando `groupby()`. No caso, queremos comparar os bairros (*neighbourhoods*) a partir do preço de locação.


```python
df_clean.groupby(['neighbourhood']).price.mean().sort_values(ascending=False)[:10]
```




    neighbourhood
    Vermelha                  292.500000
    Cardosas                  280.000000
    Aveiras de Baixo          269.500000
    Ota                       249.000000
    Freiria                   220.000000
    Peral                     174.800000
    Bucelas                   170.300000
    So Domingos de Benfica    167.225352
    Alcabideche               154.391509
    Vale do Paraso            150.000000
    Name: price, dtype: float64



* Vemos que Vermelha, Cardosas e Aveiras de Baixo são os locais com a média mais cara de Lisboa.

Só para dar um único exemplo de como uma amostra pode ser não-representativa, veja quantas entradas há para Vermelha, Cardosas e Aveiras de Baixo.


```python
#ver quantidade de imóveis nos 3 primeiros bairros e a porcentagem em relação ao total de imóveis
print("{} entradas".format(len(df_clean[df_clean.neighbourhood == "Vermelha"].index)))
print("{:.4f}%".format((len(df_clean[df_clean.neighbourhood == "Vermelha"].index) / df.shape[0])*100))
print("{} entradas".format(len(df_clean[df_clean.neighbourhood == "Cardosas"].index)))
print("{:.4f}%".format((len(df_clean[df_clean.neighbourhood == "Cardosas"].index) / df.shape[0])*100))
print("{} entradas".format(len(df_clean[df_clean.neighbourhood == "Aveiras de Baixo"].index)))
print("{:.4f}%".format((len(df_clean[df_clean.neighbourhood == "Aveiras de Baixo"].index) / df.shape[0])*100))
```

    2 entradas
    0.0081%
    1 entradas
    0.0041%
    2 entradas
    0.0081%
    


```python
#quantidade de imóveis por bairro
df_clean.neighbourhood.value_counts()
```




    Santa Maria Maior      3450
    Misericrdia            2730
    Arroios                2235
    Cascais e Estoril      1484
    So Vicente             1322
                           ... 
    Vila Nova da Rainha       1
    Cardosas                  1
    Alcoentre                 1
    Vale do Paraso            1
    Ota                       1
    Name: neighbourhood, Length: 127, dtype: int64



### **Q8. Como é a distribuição dos imóveis pela cidade de Lisboa?**

As variáveis Latitude e Longitude dos imóveis possibilita plotar cada ponto e mapear os imóveis. Considera-se x=longitude e y=latitude.


```python
# plotar os imóveis pela latitude-longitude
df_clean.plot(kind="scatter", x='longitude', y='latitude', alpha=1.0, c=df_clean['price'], s=8,
              cmap=plt.get_cmap('jet'), figsize=(12,8));
```


    
![png](\img\posts\analise-airbnb\output_45_0.png)
    


## Conclusão

Foi feita apenas uma análise superficial na base de dados do Airbnb, porém já se percebeu que existem outliers nas colunas `price`, `minimum_nights` e `calculated_host_listings_count`. Também se notou que há em algumas localidades poucos imóveis disponíveis, o que pode distorcer as informações estatísticas de alguns atributos.

* A análise aponta que Vermelha é o bairro mais caro para se hospedar, com uma média de 292 euros. 
* No geral, a média das hospedagens são feitas com o mínimo de 3 dias e uma média de preço de 97 euros e com o máximo de 1500 euros. 
* Os apartamentos são os tipos de imóveis de maior opção, sendo 71,57% das opções de locação.
* Santa Maria Maior é o bairro com a maior quantidade de imóveis disponíveis, com cerca de 3450.

Por fim, lembra-se que este dataset é uma versão resumida, ideal apenas para uma abordagem inicial. Recomenda-se que seja usado, em uma próxima análise exploratória, o conjunto de dados completos, com 106 atributos disponíveis.
