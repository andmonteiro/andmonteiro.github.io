---
layout: post
title: "Detecção de Fraudes em Cartões de Crédito com Algoritmos de Machine Learning"
subtitle: "Neste projeto iremos abordar o problema das fraudes em cartões de crédito, uma das principais preocupações das instituições financeiras como bancos e fintechs."
background: '/img/posts/fraud-detection/home.jpg'
---

Apenas no Brasil, de acordo com a pesquisa da CNDL/SPC Brasil mostra que 59% dos internautas sofreram algum tipo de fraude financeira nos últimos 12 meses, contra 46% em 2019. Isso corresponde a um contingente 16,7 milhões de brasileiros. O estudo estima que o prejuízo decorrente de fraudes financeiras sofridas no universo dos internautas brasileiros chegou a R$ 2,7 bilhões nos últimos 12 meses, incluídos os gastos na busca de reparação do problema.

<p align=center>
<img src="https://www.texascitizensbank.com/wp-content/uploads/2019/04/credit-card-fraud.jpg" width="60%"></p>

Dentre essas fraudes, aquelas envolvendo cartões de crédito são de grande relevância uma vez que não sendo detectado acarretará em prejuízos consideráveis, tanto para o consumidor quanto para a instituição financeira.

Um outro fator a ser considerado é a quantidade de falsos positivos, ou seja, aquelas vezes em que você tentou fazer uma compra e teve seu cartão bloqueado preventivamente - o que provavelmente gerou estresse e constrangimento.

Por todos esses motivos, o investimento na área de detecção de fraudes por meio de Inteligência Artificial vem crescendo a cada ano, representando uma grande oportunidade em *Data Science*. 

Devido ao enorme volume de dados de cada cliente e cada atividade financeira, um algoritmo de *Machine Learning* pode ser utilizado para identificar com eficácia padrões suspeitos nas transações. Para aumentar a precisão das análises, muitas instituições estão investindo no aprimoramento de algoritmos de Inteligência Artifical. E esse é o desafio, aprimorar cada vez mais o uso de algoritmos visando inibir ou evitar transações fraudulentas.

## Sobre os Dados

O *dataset* utilizado neste projeto foi retirado do [Kaggle](https://www.kaggle.com/mlg-ulb/creditcardfraud) e contém operações financeiras realizadas em Setembro de 2013 que ocorreram no perído de dois dias.

Podemos observar que o *dataset* é bastante desbalanceado, uma vez que possui 492 fraudes das 284.807 transações, correspondendo há cerca de 0,17% do total.

Todas as *features* do *dataset* são numéricas. Devido ao sigilo do cliente, as colunas foram renomeadas para V1, V2, ..., V28, e suas variáveis passaram por uma transformação PCA, que consiste em reduzir a dimensionalidade do *dataset*, ou seja, diminuir a quantidade de variáveis do mesmo. Para saber mais sobre PCA veja neste [link](https://jakevdp.github.io/PythonDataScienceHandbook/05.09-principal-component-analysis.html).


```python
# importar os pacotes necessários
import pandas  as pd
import numpy   as np
import seaborn as sns
import scikitplot as skplt
import matplotlib.pyplot as plt

from sklearn.model_selection import train_test_split
from sklearn.preprocessing   import StandardScaler
from sklearn.linear_model    import LogisticRegression
from sklearn.tree    import DecisionTreeClassifier
from sklearn.metrics import classification_report
from sklearn.metrics import confusion_matrix, classification_report, roc_auc_score, roc_curve, accuracy_score

from imblearn.under_sampling import RandomUnderSampler

import warnings
warnings.filterwarnings("ignore")

# configurar o estilo dos gráficos com o Seaborn
sns.set_style('dark')

#importar os dados para um dataframe
df = pd.read_csv("https://www.dropbox.com/s/b44o3t3ehmnx2b7/creditcard.csv?dl=1")
```

Os dados fornecidos pelo *Kaggle* estão prontos para uso e não precisam de qualquer alteração. Podemos passar para a análise exploratória a fim de preparar um modelo de *Machine Learning*.

## Análise Exploratória

Olhando as cinco primeiras entradas do dataframe é possível tirar algumas conclusões:

* A transformação PCA alterou as variáveis V1-v28 para numéricas, garantindo o anonimato dos dados.

* As duas únicas exceções foram as variáveis `Time` e `Amount`, contendo os segundos decorridos entre cada transação e a primeira transação no *dataset*, e o valor da transação, respectivamente.

* A *feature* `Class` é a variável alvo e assume os valores:

  0 - para transações regulares

  1 - para transações fraudulentas


```python
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
      <th>Time</th>
      <th>V1</th>
      <th>V2</th>
      <th>V3</th>
      <th>V4</th>
      <th>V5</th>
      <th>V6</th>
      <th>V7</th>
      <th>V8</th>
      <th>V9</th>
      <th>V10</th>
      <th>V11</th>
      <th>V12</th>
      <th>V13</th>
      <th>V14</th>
      <th>V15</th>
      <th>V16</th>
      <th>V17</th>
      <th>V18</th>
      <th>V19</th>
      <th>V20</th>
      <th>V21</th>
      <th>V22</th>
      <th>V23</th>
      <th>V24</th>
      <th>V25</th>
      <th>V26</th>
      <th>V27</th>
      <th>V28</th>
      <th>Amount</th>
      <th>Class</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.0</td>
      <td>-1.359807</td>
      <td>-0.072781</td>
      <td>2.536347</td>
      <td>1.378155</td>
      <td>-0.338321</td>
      <td>0.462388</td>
      <td>0.239599</td>
      <td>0.098698</td>
      <td>0.363787</td>
      <td>0.090794</td>
      <td>-0.551600</td>
      <td>-0.617801</td>
      <td>-0.991390</td>
      <td>-0.311169</td>
      <td>1.468177</td>
      <td>-0.470401</td>
      <td>0.207971</td>
      <td>0.025791</td>
      <td>0.403993</td>
      <td>0.251412</td>
      <td>-0.018307</td>
      <td>0.277838</td>
      <td>-0.110474</td>
      <td>0.066928</td>
      <td>0.128539</td>
      <td>-0.189115</td>
      <td>0.133558</td>
      <td>-0.021053</td>
      <td>149.62</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.0</td>
      <td>1.191857</td>
      <td>0.266151</td>
      <td>0.166480</td>
      <td>0.448154</td>
      <td>0.060018</td>
      <td>-0.082361</td>
      <td>-0.078803</td>
      <td>0.085102</td>
      <td>-0.255425</td>
      <td>-0.166974</td>
      <td>1.612727</td>
      <td>1.065235</td>
      <td>0.489095</td>
      <td>-0.143772</td>
      <td>0.635558</td>
      <td>0.463917</td>
      <td>-0.114805</td>
      <td>-0.183361</td>
      <td>-0.145783</td>
      <td>-0.069083</td>
      <td>-0.225775</td>
      <td>-0.638672</td>
      <td>0.101288</td>
      <td>-0.339846</td>
      <td>0.167170</td>
      <td>0.125895</td>
      <td>-0.008983</td>
      <td>0.014724</td>
      <td>2.69</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1.0</td>
      <td>-1.358354</td>
      <td>-1.340163</td>
      <td>1.773209</td>
      <td>0.379780</td>
      <td>-0.503198</td>
      <td>1.800499</td>
      <td>0.791461</td>
      <td>0.247676</td>
      <td>-1.514654</td>
      <td>0.207643</td>
      <td>0.624501</td>
      <td>0.066084</td>
      <td>0.717293</td>
      <td>-0.165946</td>
      <td>2.345865</td>
      <td>-2.890083</td>
      <td>1.109969</td>
      <td>-0.121359</td>
      <td>-2.261857</td>
      <td>0.524980</td>
      <td>0.247998</td>
      <td>0.771679</td>
      <td>0.909412</td>
      <td>-0.689281</td>
      <td>-0.327642</td>
      <td>-0.139097</td>
      <td>-0.055353</td>
      <td>-0.059752</td>
      <td>378.66</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1.0</td>
      <td>-0.966272</td>
      <td>-0.185226</td>
      <td>1.792993</td>
      <td>-0.863291</td>
      <td>-0.010309</td>
      <td>1.247203</td>
      <td>0.237609</td>
      <td>0.377436</td>
      <td>-1.387024</td>
      <td>-0.054952</td>
      <td>-0.226487</td>
      <td>0.178228</td>
      <td>0.507757</td>
      <td>-0.287924</td>
      <td>-0.631418</td>
      <td>-1.059647</td>
      <td>-0.684093</td>
      <td>1.965775</td>
      <td>-1.232622</td>
      <td>-0.208038</td>
      <td>-0.108300</td>
      <td>0.005274</td>
      <td>-0.190321</td>
      <td>-1.175575</td>
      <td>0.647376</td>
      <td>-0.221929</td>
      <td>0.062723</td>
      <td>0.061458</td>
      <td>123.50</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2.0</td>
      <td>-1.158233</td>
      <td>0.877737</td>
      <td>1.548718</td>
      <td>0.403034</td>
      <td>-0.407193</td>
      <td>0.095921</td>
      <td>0.592941</td>
      <td>-0.270533</td>
      <td>0.817739</td>
      <td>0.753074</td>
      <td>-0.822843</td>
      <td>0.538196</td>
      <td>1.345852</td>
      <td>-1.119670</td>
      <td>0.175121</td>
      <td>-0.451449</td>
      <td>-0.237033</td>
      <td>-0.038195</td>
      <td>0.803487</td>
      <td>0.408542</td>
      <td>-0.009431</td>
      <td>0.798278</td>
      <td>-0.137458</td>
      <td>0.141267</td>
      <td>-0.206010</td>
      <td>0.502292</td>
      <td>0.219422</td>
      <td>0.215153</td>
      <td>69.99</td>
      <td>0</td>
    </tr>
  </tbody>
</table>
</div>



Verificando abaixo o resumo estatístico por meio do método `describe()` podemos confirmar que as variáveis relativas aos componentes principais (transformação PCA) não têm nenhuma discrepância aparente, bem como a variável `Time`.

Nota-se que a variável `Amount` tem um valor médio de 88.34 e uma mediana de 22. O desvio padrão é de 250.12 e temos um valor máximo em uma transação de 25.961,16.


```python
df.describe()
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
      <th>Time</th>
      <th>V1</th>
      <th>V2</th>
      <th>V3</th>
      <th>V4</th>
      <th>V5</th>
      <th>V6</th>
      <th>V7</th>
      <th>V8</th>
      <th>V9</th>
      <th>V10</th>
      <th>V11</th>
      <th>V12</th>
      <th>V13</th>
      <th>V14</th>
      <th>V15</th>
      <th>V16</th>
      <th>V17</th>
      <th>V18</th>
      <th>V19</th>
      <th>V20</th>
      <th>V21</th>
      <th>V22</th>
      <th>V23</th>
      <th>V24</th>
      <th>V25</th>
      <th>V26</th>
      <th>V27</th>
      <th>V28</th>
      <th>Amount</th>
      <th>Class</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>284807.000000</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>2.848070e+05</td>
      <td>284807.000000</td>
      <td>284807.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>94813.859575</td>
      <td>3.919560e-15</td>
      <td>5.688174e-16</td>
      <td>-8.769071e-15</td>
      <td>2.782312e-15</td>
      <td>-1.552563e-15</td>
      <td>2.010663e-15</td>
      <td>-1.694249e-15</td>
      <td>-1.927028e-16</td>
      <td>-3.137024e-15</td>
      <td>1.768627e-15</td>
      <td>9.170318e-16</td>
      <td>-1.810658e-15</td>
      <td>1.693438e-15</td>
      <td>1.479045e-15</td>
      <td>3.482336e-15</td>
      <td>1.392007e-15</td>
      <td>-7.528491e-16</td>
      <td>4.328772e-16</td>
      <td>9.049732e-16</td>
      <td>5.085503e-16</td>
      <td>1.537294e-16</td>
      <td>7.959909e-16</td>
      <td>5.367590e-16</td>
      <td>4.458112e-15</td>
      <td>1.453003e-15</td>
      <td>1.699104e-15</td>
      <td>-3.660161e-16</td>
      <td>-1.206049e-16</td>
      <td>88.349619</td>
      <td>0.001727</td>
    </tr>
    <tr>
      <th>std</th>
      <td>47488.145955</td>
      <td>1.958696e+00</td>
      <td>1.651309e+00</td>
      <td>1.516255e+00</td>
      <td>1.415869e+00</td>
      <td>1.380247e+00</td>
      <td>1.332271e+00</td>
      <td>1.237094e+00</td>
      <td>1.194353e+00</td>
      <td>1.098632e+00</td>
      <td>1.088850e+00</td>
      <td>1.020713e+00</td>
      <td>9.992014e-01</td>
      <td>9.952742e-01</td>
      <td>9.585956e-01</td>
      <td>9.153160e-01</td>
      <td>8.762529e-01</td>
      <td>8.493371e-01</td>
      <td>8.381762e-01</td>
      <td>8.140405e-01</td>
      <td>7.709250e-01</td>
      <td>7.345240e-01</td>
      <td>7.257016e-01</td>
      <td>6.244603e-01</td>
      <td>6.056471e-01</td>
      <td>5.212781e-01</td>
      <td>4.822270e-01</td>
      <td>4.036325e-01</td>
      <td>3.300833e-01</td>
      <td>250.120109</td>
      <td>0.041527</td>
    </tr>
    <tr>
      <th>min</th>
      <td>0.000000</td>
      <td>-5.640751e+01</td>
      <td>-7.271573e+01</td>
      <td>-4.832559e+01</td>
      <td>-5.683171e+00</td>
      <td>-1.137433e+02</td>
      <td>-2.616051e+01</td>
      <td>-4.355724e+01</td>
      <td>-7.321672e+01</td>
      <td>-1.343407e+01</td>
      <td>-2.458826e+01</td>
      <td>-4.797473e+00</td>
      <td>-1.868371e+01</td>
      <td>-5.791881e+00</td>
      <td>-1.921433e+01</td>
      <td>-4.498945e+00</td>
      <td>-1.412985e+01</td>
      <td>-2.516280e+01</td>
      <td>-9.498746e+00</td>
      <td>-7.213527e+00</td>
      <td>-5.449772e+01</td>
      <td>-3.483038e+01</td>
      <td>-1.093314e+01</td>
      <td>-4.480774e+01</td>
      <td>-2.836627e+00</td>
      <td>-1.029540e+01</td>
      <td>-2.604551e+00</td>
      <td>-2.256568e+01</td>
      <td>-1.543008e+01</td>
      <td>0.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>54201.500000</td>
      <td>-9.203734e-01</td>
      <td>-5.985499e-01</td>
      <td>-8.903648e-01</td>
      <td>-8.486401e-01</td>
      <td>-6.915971e-01</td>
      <td>-7.682956e-01</td>
      <td>-5.540759e-01</td>
      <td>-2.086297e-01</td>
      <td>-6.430976e-01</td>
      <td>-5.354257e-01</td>
      <td>-7.624942e-01</td>
      <td>-4.055715e-01</td>
      <td>-6.485393e-01</td>
      <td>-4.255740e-01</td>
      <td>-5.828843e-01</td>
      <td>-4.680368e-01</td>
      <td>-4.837483e-01</td>
      <td>-4.988498e-01</td>
      <td>-4.562989e-01</td>
      <td>-2.117214e-01</td>
      <td>-2.283949e-01</td>
      <td>-5.423504e-01</td>
      <td>-1.618463e-01</td>
      <td>-3.545861e-01</td>
      <td>-3.171451e-01</td>
      <td>-3.269839e-01</td>
      <td>-7.083953e-02</td>
      <td>-5.295979e-02</td>
      <td>5.600000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>84692.000000</td>
      <td>1.810880e-02</td>
      <td>6.548556e-02</td>
      <td>1.798463e-01</td>
      <td>-1.984653e-02</td>
      <td>-5.433583e-02</td>
      <td>-2.741871e-01</td>
      <td>4.010308e-02</td>
      <td>2.235804e-02</td>
      <td>-5.142873e-02</td>
      <td>-9.291738e-02</td>
      <td>-3.275735e-02</td>
      <td>1.400326e-01</td>
      <td>-1.356806e-02</td>
      <td>5.060132e-02</td>
      <td>4.807155e-02</td>
      <td>6.641332e-02</td>
      <td>-6.567575e-02</td>
      <td>-3.636312e-03</td>
      <td>3.734823e-03</td>
      <td>-6.248109e-02</td>
      <td>-2.945017e-02</td>
      <td>6.781943e-03</td>
      <td>-1.119293e-02</td>
      <td>4.097606e-02</td>
      <td>1.659350e-02</td>
      <td>-5.213911e-02</td>
      <td>1.342146e-03</td>
      <td>1.124383e-02</td>
      <td>22.000000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>139320.500000</td>
      <td>1.315642e+00</td>
      <td>8.037239e-01</td>
      <td>1.027196e+00</td>
      <td>7.433413e-01</td>
      <td>6.119264e-01</td>
      <td>3.985649e-01</td>
      <td>5.704361e-01</td>
      <td>3.273459e-01</td>
      <td>5.971390e-01</td>
      <td>4.539234e-01</td>
      <td>7.395934e-01</td>
      <td>6.182380e-01</td>
      <td>6.625050e-01</td>
      <td>4.931498e-01</td>
      <td>6.488208e-01</td>
      <td>5.232963e-01</td>
      <td>3.996750e-01</td>
      <td>5.008067e-01</td>
      <td>4.589494e-01</td>
      <td>1.330408e-01</td>
      <td>1.863772e-01</td>
      <td>5.285536e-01</td>
      <td>1.476421e-01</td>
      <td>4.395266e-01</td>
      <td>3.507156e-01</td>
      <td>2.409522e-01</td>
      <td>9.104512e-02</td>
      <td>7.827995e-02</td>
      <td>77.165000</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>172792.000000</td>
      <td>2.454930e+00</td>
      <td>2.205773e+01</td>
      <td>9.382558e+00</td>
      <td>1.687534e+01</td>
      <td>3.480167e+01</td>
      <td>7.330163e+01</td>
      <td>1.205895e+02</td>
      <td>2.000721e+01</td>
      <td>1.559499e+01</td>
      <td>2.374514e+01</td>
      <td>1.201891e+01</td>
      <td>7.848392e+00</td>
      <td>7.126883e+00</td>
      <td>1.052677e+01</td>
      <td>8.877742e+00</td>
      <td>1.731511e+01</td>
      <td>9.253526e+00</td>
      <td>5.041069e+00</td>
      <td>5.591971e+00</td>
      <td>3.942090e+01</td>
      <td>2.720284e+01</td>
      <td>1.050309e+01</td>
      <td>2.252841e+01</td>
      <td>4.584549e+00</td>
      <td>7.519589e+00</td>
      <td>3.517346e+00</td>
      <td>3.161220e+01</td>
      <td>3.384781e+01</td>
      <td>25691.160000</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>



Quanto à qualidade do *dataset*, este não apresentou nenhum valor ausente ou que demandasse uma etapa de limpeza, então estamos prontos para seguir em frente.


```python
# verificar se há valores nulos
df.isnull().sum().max()
```




    0



Como foi informado na descrição dos dados, as entradas relativas à transações fraudulentas correspondam a 0,17% do total. Vamos verificar essa discrepância no gráfico abaixo.



```python
# plotar gráfico de barras para as Classes
fig, ax = plt.subplots()
sns.countplot(x='Class', data=df)
ax.set_title('Distribuição das Classes')
plt.show()
```


![png](\img\posts\fraud-detection\output_11_0.png)
 


Agora vamos verificar a distribuição das duas classes ao longo do tempo. No entanto, não foi identificada nenhum informação a partir das distribuições de frequência abaixo.


```python
fig, ax = plt.subplots(nrows=2, ncols=1, figsize=(12,6))

ax[0].hist(df.Time[df.Class == 0], bins=40)
ax[0].set_title('Transações regulares')
ax[0].set_xlabel('Tempo (segundos)')
ax[0].set_ylabel('Quantidade')
plt.tight_layout()

ax[1].hist(df.Time[df.Class == 1], bins=40)
ax[1].set_title('Transações fraudulentas')
ax[1].set_xlabel('Tempo (segundos)')
ax[1].set_ylabel('Quantidade')
plt.tight_layout()
```


    
![png](\img\posts\fraud-detection\output_13_0.png)
    


Embora o número de transações fraudulentas seja significativamente menor do que o número de transações regulares, podemos ver um comportamento distinto, especialmente em torno da marca de tempo 100.000.

Observe que o número de transações regulares cai drasticamente em torno da marca de 90.000 segundos, para aumentar novamente em torno da marca de 110.000 segundos. Não seria absurdo supor que esse período seja durante a noite, quando as pessoas naturalmente realizam menos compras do que durante o dia. Por outro lado, um grande número de transações fraudulentas ocorreram em torno da marca de 100.000, o que poderia confirmar a premissa anterior, considerando que os fraudadores devem preferir cometer fraudes tarde da noite, supondo que haveria menos vigilância.

Isso é apenas uma hipótese. Vamos esperar para ver como nosso modelo de *Machine Learning* interpretará esses números.

Podemos fazer a mesma análise utilizando a variável `Amount` de cada transação.


```python
fig, ax = plt.subplots(nrows=2, ncols=1, figsize=(12,6))

ax[0].hist(df.Amount[df.Class == 0], bins=20)
ax[0].set_title('Transações regulares')
ax[0].set_xlabel('Valor')
ax[0].set_ylabel('Quantidade')
plt.tight_layout()

ax[1].hist(df.Amount[df.Class == 1], bins=20)
ax[1].set_title('Transações fraudulentas')
ax[1].set_xlabel('Valor')
ax[1].set_ylabel('Quantidade')
plt.tight_layout()
```


    
![png](\img\posts\fraud-detection\output_16_0.png)
    


Quanto ao valor das transações, aparentemente não há um *insight* significativo que possamos obter delas. A maioria das transações, regulares e fraudulentas, eram de valores "pequenos". Como vimos anteriormente, a maioria das transações são de menos de 100.00.

No entanto, podemos realizar uma inspeção mais profunda com um gráfico de *boxplot*.

Usamos o *boxplot* para ver se há alguma diferença no padrão transações em relação à dimensão `Amount`. De uma maneira geral, percebe-se uma distribuição diferente para as duas classes, o que provavelmente irá contribuir para o treinamento do modelo de *Machine Learning*.


```python
fig, ax = plt.subplots(figsize=(6,10), sharex=True)
sns.boxplot(df.Class, df.Amount, showmeans=True, ax=ax)
plt.ylim((-20, 400))
plt.xticks([0, 1], ['Normal', 'Fraude'])
plt.tight_layout()
```


    
![png](\img\posts\fraud-detection\output_19_0.png)
    


Vamos plotar um gráfico de matriz de correlação para determinar a correlação entre as variáveis.


```python
#cria uma variável que recebe a função corr(), que calcula a correlação entre as colunas.
corr = df.corr()
```


```python
#plota um gráfico de correlação utilizando a variável criada com a função corr()
fig, ax = plt.subplots(figsize=(9, 7))
sns.heatmap(corr, xticklabels=corr.columns, yticklabels=corr.columns,
            linewidths=.1, cmap="coolwarm", ax=ax)
plt.tight_layout()
```


    
![png](\img\posts\fraud-detection\output_22_0.png)
    


Não foi observado nenhuma correlação forte entre as variáveis.

## Preparando os dados

**Padronizar as variáveis `Time` e `Amount`**

Este é um passo essencial para que os dados sejam interpretados de forma correta pelo algoritmo de *Machine Learning*. Uma vez que a coluna `Amount` possui *outliers*, será usada a padronização por meio da classe `StandardScaler`.


```python
#criar uma cópia do dataframe
df_clean = df.copy()

#padronizar as colunas Time e Amount
std_scaler = StandardScaler()
df_clean['std_amount'] = std_scaler.fit_transform(df_clean['Amount'].values.reshape(-1,1))
df_clean['std_time'] = std_scaler.fit_transform(df_clean['Time'].values.reshape(-1,1))

df_clean.drop(['Time', 'Amount'], axis=1, inplace=True)
```

Após a padronização vamos verificar como ficou o *dataframe*:


```python
df_clean.head()
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
      <th>V1</th>
      <th>V2</th>
      <th>V3</th>
      <th>V4</th>
      <th>V5</th>
      <th>V6</th>
      <th>V7</th>
      <th>V8</th>
      <th>V9</th>
      <th>V10</th>
      <th>V11</th>
      <th>V12</th>
      <th>V13</th>
      <th>V14</th>
      <th>V15</th>
      <th>V16</th>
      <th>V17</th>
      <th>V18</th>
      <th>V19</th>
      <th>V20</th>
      <th>V21</th>
      <th>V22</th>
      <th>V23</th>
      <th>V24</th>
      <th>V25</th>
      <th>V26</th>
      <th>V27</th>
      <th>V28</th>
      <th>Class</th>
      <th>std_amount</th>
      <th>std_time</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>-1.359807</td>
      <td>-0.072781</td>
      <td>2.536347</td>
      <td>1.378155</td>
      <td>-0.338321</td>
      <td>0.462388</td>
      <td>0.239599</td>
      <td>0.098698</td>
      <td>0.363787</td>
      <td>0.090794</td>
      <td>-0.551600</td>
      <td>-0.617801</td>
      <td>-0.991390</td>
      <td>-0.311169</td>
      <td>1.468177</td>
      <td>-0.470401</td>
      <td>0.207971</td>
      <td>0.025791</td>
      <td>0.403993</td>
      <td>0.251412</td>
      <td>-0.018307</td>
      <td>0.277838</td>
      <td>-0.110474</td>
      <td>0.066928</td>
      <td>0.128539</td>
      <td>-0.189115</td>
      <td>0.133558</td>
      <td>-0.021053</td>
      <td>0</td>
      <td>0.244964</td>
      <td>-1.996583</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1.191857</td>
      <td>0.266151</td>
      <td>0.166480</td>
      <td>0.448154</td>
      <td>0.060018</td>
      <td>-0.082361</td>
      <td>-0.078803</td>
      <td>0.085102</td>
      <td>-0.255425</td>
      <td>-0.166974</td>
      <td>1.612727</td>
      <td>1.065235</td>
      <td>0.489095</td>
      <td>-0.143772</td>
      <td>0.635558</td>
      <td>0.463917</td>
      <td>-0.114805</td>
      <td>-0.183361</td>
      <td>-0.145783</td>
      <td>-0.069083</td>
      <td>-0.225775</td>
      <td>-0.638672</td>
      <td>0.101288</td>
      <td>-0.339846</td>
      <td>0.167170</td>
      <td>0.125895</td>
      <td>-0.008983</td>
      <td>0.014724</td>
      <td>0</td>
      <td>-0.342475</td>
      <td>-1.996583</td>
    </tr>
    <tr>
      <th>2</th>
      <td>-1.358354</td>
      <td>-1.340163</td>
      <td>1.773209</td>
      <td>0.379780</td>
      <td>-0.503198</td>
      <td>1.800499</td>
      <td>0.791461</td>
      <td>0.247676</td>
      <td>-1.514654</td>
      <td>0.207643</td>
      <td>0.624501</td>
      <td>0.066084</td>
      <td>0.717293</td>
      <td>-0.165946</td>
      <td>2.345865</td>
      <td>-2.890083</td>
      <td>1.109969</td>
      <td>-0.121359</td>
      <td>-2.261857</td>
      <td>0.524980</td>
      <td>0.247998</td>
      <td>0.771679</td>
      <td>0.909412</td>
      <td>-0.689281</td>
      <td>-0.327642</td>
      <td>-0.139097</td>
      <td>-0.055353</td>
      <td>-0.059752</td>
      <td>0</td>
      <td>1.160686</td>
      <td>-1.996562</td>
    </tr>
    <tr>
      <th>3</th>
      <td>-0.966272</td>
      <td>-0.185226</td>
      <td>1.792993</td>
      <td>-0.863291</td>
      <td>-0.010309</td>
      <td>1.247203</td>
      <td>0.237609</td>
      <td>0.377436</td>
      <td>-1.387024</td>
      <td>-0.054952</td>
      <td>-0.226487</td>
      <td>0.178228</td>
      <td>0.507757</td>
      <td>-0.287924</td>
      <td>-0.631418</td>
      <td>-1.059647</td>
      <td>-0.684093</td>
      <td>1.965775</td>
      <td>-1.232622</td>
      <td>-0.208038</td>
      <td>-0.108300</td>
      <td>0.005274</td>
      <td>-0.190321</td>
      <td>-1.175575</td>
      <td>0.647376</td>
      <td>-0.221929</td>
      <td>0.062723</td>
      <td>0.061458</td>
      <td>0</td>
      <td>0.140534</td>
      <td>-1.996562</td>
    </tr>
    <tr>
      <th>4</th>
      <td>-1.158233</td>
      <td>0.877737</td>
      <td>1.548718</td>
      <td>0.403034</td>
      <td>-0.407193</td>
      <td>0.095921</td>
      <td>0.592941</td>
      <td>-0.270533</td>
      <td>0.817739</td>
      <td>0.753074</td>
      <td>-0.822843</td>
      <td>0.538196</td>
      <td>1.345852</td>
      <td>-1.119670</td>
      <td>0.175121</td>
      <td>-0.451449</td>
      <td>-0.237033</td>
      <td>-0.038195</td>
      <td>0.803487</td>
      <td>0.408542</td>
      <td>-0.009431</td>
      <td>0.798278</td>
      <td>-0.137458</td>
      <td>0.141267</td>
      <td>-0.206010</td>
      <td>0.502292</td>
      <td>0.219422</td>
      <td>0.215153</td>
      <td>0</td>
      <td>-0.073403</td>
      <td>-1.996541</td>
    </tr>
  </tbody>
</table>
</div>



**Dividir entre conjuntos de treino e teste**

Depois de padronizar as *features* `Time` e `Amount`, vamos dividir o *dataset* em treino e teste. Caso contrário, não teremos parâmetros para conferir se no momento do balanceamento dos dados foi adequado. Usaremos a divisão padrão para o conjunto de teste de 25%.
Para garantir que os conjuntos de treino e teste tenham a mesma quantidade de classes proporcionalmente, passamos o parâmetro `stratify=True`.


```python
#separar target vector da feature matrix
X = df_clean.drop('Class', axis=1)
y = df_clean['Class']

#dividir entre treino e teste
X_train, X_test, y_train, y_test = train_test_split(X, y, stratify=y, shuffle=True)
```

**Balanceamento dos dados**

Como mencionado antes, o dataset está bastante desbalanceado. Como existe uma grande diferença na distribuição das classes(284.315 classe 0 e 492 classe 1) o conjunto de treino pode ser tendencioso e influenciar o algoritmo de *Machine Learning* a exibir resultados desbalanceados, por exemplo, ignorando a classe com menos entradas.

Para resolver esse problema, vamos usar a biblioteca `imblearn` e aplicar técnicas de balanceamento de dados. Existem duas abordagens principais para isso, sendo:

- *Random Oversampling*: Duplica aleatoriamente observações da classe de menor ocorrência utilizando *RandomOverSampler()*
- *Random Undersampling*: Exclui aleatoriamente observações da classe de maior ocorrência utilizando *RandomUnderSampler()*

Utilizaremos o *Random Undersamping*.


```python
#usar técnica de undersampling
rus = RandomUnderSampler()
X_rus, y_rus = rus.fit_sample(X_train, y_train)
```

Vamos verificar como ficou os dados após o balanceamento:


```python
print(pd.Series(y_rus).value_counts())
sns.countplot(y_rus);
```

    1    369
    0    369
    dtype: int64
    


    
![png](\img\posts\fraud-detection\output_37_1.png)
    


Nós balanceamos os dados, com 369 entradas para cada classe. Vamos verificar a matriz de correlação novamente para ver se podemos determinar alguma correlação.



```python
#plotar matriz de correção
corr = X_train.corr()
corr_rus = pd.DataFrame(X_rus).corr()

fig, ax = plt.subplots(nrows=1, ncols=2, figsize = (19,9))
fig.suptitle('Matriz de Correlação')

sns.heatmap(corr_rus, xticklabels=corr.columns, yticklabels=corr.columns,
            linewidths=.1, cmap="coolwarm", ax=ax[1])
ax[1].set_title('Balanceado')

plt.show()
```


    
![png](\img\posts\fraud-detection\output_39_0.png)
    


Observe como anteriormente, sem o balanceamento de dados, a matriz de correlação não trazia nenhuma informação relevante. Entretanto, ela traz muito mais informações após um correto balanceamento.

## Modelo de Machine Learning

Com todos os dados preparados e após uma análise exploratória completa ,vamos utilizar dois dos principais métodos de classificação de *Machine Learning*:
- Regressão Logística
- *Decision Tree*

Após instanciar o modelo, o mesmo será treinado em cima dos dados em `X_rus` e `y_rus`. Na sequência, serão realizadas as previsões sobre os dados de teste.

### Regressão Logística

O modelo de Regressão Logística é usado para determinar as chances de uma determinada classe ou evento existir. No nosso caso, vai estabelecer a probabilidade de uma transação pertencer a classe 0 ou 1, ou seja, regular ou fraudulenta.


```python
#instanciar e treinar o modelo de Regressão Logística
model_lr = LogisticRegression(random_state=0)
model_lr.fit(X_rus, y_rus)

y_pred_lr = model_lr.predict(X_test)
```

Vamos avaliar a acurácia do modelo usando o `classification_report`, `roc_auc_score` e `confusion_matrix`.


```python
# exibir o relatório de classificação
print(classification_report(y_test, y_pred_lr))

# imprimir o score AUC
print("ROC AUC score: {:.2f}\n".format(roc_auc_score(y_test, y_pred_lr)))


# plotar a matriz de confusão
skplt.metrics.plot_confusion_matrix(y_test, y_pred_lr, normalize=True);
```

                  precision    recall  f1-score   support
    
               0       1.00      0.97      0.98     71079
               1       0.05      0.89      0.09       123
    
        accuracy                           0.97     71202
       macro avg       0.52      0.93      0.54     71202
    weighted avg       1.00      0.97      0.98     71202
    
    ROC AUC score: 0.93
    
    


    
![png](\img\posts\fraud-detection\output_45_1.png)
    


O modelo teve uma acurácia de 97% e uma AUC de 93%, o que significa que o nosso modelo de Regressão Logística teve um desempenho muito bom.

A partir da matriz de confusão podemos concluir que 97% das transações regulares foram corretamente classificadas como regulares(Verdadeiro Negativo), e 89% das transações fraudulentas foram corretamente classificadas como fraudes(Verdadeiro Positivo).

### Decision Tree

Nós vimos que o modelo de Regressão Logística teve um bom desempenho. Agora vamos ver como o modelo de *Decision Tree* se sai.


```python
#instanciar e treinar o modelo de Decision Tree
model_tree = DecisionTreeClassifier(max_depth=4, criterion="entropy", random_state=0)
model_tree.fit(X_rus, y_rus)

y_pred_tree = model_tree.predict(X_test)
```


```python
# classification report
print(classification_report(y_test, y_pred_tree))

# ROC AUC score
print("ROC AUC Score: {:.2f}\n".format(roc_auc_score(y_test, y_pred_tree)))

# plotar a matriz de confusão
skplt.metrics.plot_confusion_matrix(y_test, y_pred_tree, normalize=True);
```

                  precision    recall  f1-score   support
    
               0       1.00      0.96      0.98     71079
               1       0.03      0.87      0.06       123
    
        accuracy                           0.96     71202
       macro avg       0.52      0.91      0.52     71202
    weighted avg       1.00      0.96      0.98     71202
    
    ROC AUC Score: 0.91
    
    


    
![png](\img\posts\fraud-detection\output_49_1.png)
    


O modelo de *Decision Tree* obteve um desempenho com uma acurácia de 96% e *AUC score* de 91%. Os verdadeiros positivos e verdadeiros negativos foram bem previstos, com 96% e 87% respectivamente. 

## Conclusão

Ambos os modelos, de Regressão Logística e *Decision Tree* desempenharam muito bem na classificação das atividades de cartão de crédito, com uma acurácia, AUC e precisão acima de 90%.

Embora tenham produzido resultados semelhantes, o modelo de Regressão Logística exibiu um resultado melhor, com uma acurácia de 97%.

É importante também ressaltar o pré-processamento e o balanceamento dos dados. Lembre-se de como a matriz de correlação teve um desempenho melhor após o balanceamento dos mesmos.

Obviamente, apesar do bom resultado final, há espaço para testar o desempenho com outros algoritmos de classificação, além de otimizar seus parâmetros.

Os algoritmos de *Machine Learning* para detectar fraudes de cartão de crédito são altamente eficientes, mas ainda existem falhas a serem corrigidas. Um dos maiores problemas é a ocorrência de Falsos Positivos, ou seja, quando o algoritmo detecta incorretamente uma fraude.
