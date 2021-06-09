---
layout: post
title: "Analisando a Violência no Rio de Janeiro"
subtitle: "Neste notebook iremos analisar os dados do ISP RJ com relação ao número de ocorrências de violência no Estado do Rio de Janeiro."
background: '/img/posts/analise-rj/analise-rj.jpg'
---

# Analisando a Violência no Rio de Janeiro

Neste notebook iremos analisar os dados do ISP RJ com relação ao número de ocorrências de violência no Estado do Rio de Janeiro.

É fato que a violência atinge todas as cidades do Brasil, e vem ganhando destaque nos noticiários cada vez mais.

Atualmente, as leis de acesso à informação e a conscientização dos orgãos públicos, vêm proporcionando a disponibilidade de várias fontes de dados.
Neste exemplo, o Governo do Rio de Janeiro vem trazendo a iniciativa do portal [ISP Dados](https://http://www.ispdados.rj.gov.br/estatistica.html). Neste projeto, faremos uma análise em cima dos indicadores estatísticos da violência no Rio, explorando alguns dos dados fornecidos.

## Obtenção dos Dados


Os dados foram obtidos diretamente do Portal ISP Dados, que é uma página de dados abertos do Instituto de Segurança Pública do Rio de Janeiro.

As estatísticas divulgadas no portal são construídas a partir dos Registros de Ocorrência(RO) lavrados nas Delegacias de Polícia Civil do Estado do Rio de Janeiro, além de informações complementares de orgãos específicos da Polícia Militar do Estado do Rio de Janeiro.

Como forma de criar um controle de qualidade, antes de serem consolidados no ISP o RO é submetido à Corregedoria Interna da Polícia Civil(COINPOL). As estatísticas produzidas se baseiam na data em que foi confeccionado o Registro de Ocorrência.

### Importando os dados
Inicialmente iremos importar a biblioteca Pandas e o Numpy seguido dos dados deste [link](https://raw.githubusercontent.com/carlosfab/dsnp2/master/datasets/violencia_rio.csv), usando `df = pd.read_csv("endereço_do_csv")`


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt


df = pd.read_csv("https://raw.githubusercontent.com/anssodre/datasets/master/violencia_rio.csv")
```

## Análise Inicial dos Dados

Antes de começarmos a explorar o dataset, é preciso saber com o que estamos lidando. Para começarmos a entender melhor esse aspecto, vamos verificar as dimensões do dataset.

* Qual o tamanho do seu DataFrame (`shape`)
* Extrair e imprimir os nomes das colunas (`columns`)
* Quais os tipos das variáveis (`dtypes`)
* Visualizar as 5 primeiras linhas (`head`)
* Identifique a porcentagem de valores ausentes das colunas



```python
# verificar o tamanho do DataFrame
print("Variáveis: ", df.shape[1])
print("Entradas: ", df.shape[0])
```

    Variáveis:  56
    Entradas:  344
    

Usamos o `df.shape` para exibir o tamanho do Dataframe, sendo `df.shape[1]` o número de colunas e `df.shape[0]` o número de linhas.

Agora que já sabemos com a dimensão dos dados com que estamos lidando, vamos verificar que tipo de dados nós temos, verificando as variáveis existentes no nosso conjunto de dados.


```python
#imprimir o nome das colunas
df.columns
```




    Index(['vano', 'mes', 'hom_doloso', 'lesao_corp_morte', 'latrocinio',
           'hom_por_interv_policial', 'tentat_hom', 'lesao_corp_dolosa', 'estupro',
           'hom_culposo', 'lesao_corp_culposa', 'roubo_comercio',
           'roubo_residencia', 'roubo_veiculo', 'roubo_carga', 'roubo_transeunte',
           'roubo_em_coletivo', 'roubo_banco', 'roubo_cx_eletronico',
           'roubo_celular', 'roubo_conducao_saque', 'roubo_apos_saque',
           'roubo_bicicleta', 'outros_roubos', 'total_roubos', 'furto_veiculos',
           'furto_transeunte', 'furto_coletivo', 'furto_celular',
           'furto_bicicleta', 'outros_furtos', 'total_furtos', 'sequestro',
           'extorsao', 'sequestro_relampago', 'estelionato', 'apreensao_drogas',
           'posse_drogas', 'trafico_drogas', 'apreensao_drogas_sem_autor',
           'recuperacao_veiculos', 'apf', 'aaapai', 'cmp', 'cmba', 'ameaca',
           'pessoas_desaparecidas', 'encontro_cadaver', 'encontro_ossada',
           'pol_militares_mortos_serv', 'pol_civis_mortos_serv',
           'indicador_letalidade', 'indicador_roubo_rua', 'indicador_roubo_veic',
           'registro_ocorrencias', 'fase'],
          dtype='object')



Sabendo com quantas variáveis e linhas estamos lidando, e que tipo de informação vamos encontrar no dataset, é importante analisarmos o tipo de cada variável que temos.

Esse tipo de informação é bastante útil, e nos ajudará a direcionar nosso trabalho na fase de limpeza dos dados.


```python
# ver os tipos das variáveis
df.dtypes
```




    vano                            int64
    mes                             int64
    hom_doloso                      int64
    lesao_corp_morte              float64
    latrocinio                      int64
    hom_por_interv_policial       float64
    tentat_hom                      int64
    lesao_corp_dolosa               int64
    estupro                       float64
    hom_culposo                   float64
    lesao_corp_culposa            float64
    roubo_comercio                  int64
    roubo_residencia                int64
    roubo_veiculo                   int64
    roubo_carga                     int64
    roubo_transeunte                int64
    roubo_em_coletivo               int64
    roubo_banco                     int64
    roubo_cx_eletronico           float64
    roubo_celular                 float64
    roubo_conducao_saque          float64
    roubo_apos_saque              float64
    roubo_bicicleta               float64
    outros_roubos                   int64
    total_roubos                    int64
    furto_veiculos                  int64
    furto_transeunte                int64
    furto_coletivo                float64
    furto_celular                 float64
    furto_bicicleta               float64
    outros_furtos                   int64
    total_furtos                    int64
    sequestro                     float64
    extorsao                      float64
    sequestro_relampago           float64
    estelionato                   float64
    apreensao_drogas                int64
    posse_drogas                  float64
    trafico_drogas                float64
    apreensao_drogas_sem_autor    float64
    recuperacao_veiculos            int64
    apf                           float64
    aaapai                        float64
    cmp                           float64
    cmba                          float64
    ameaca                        float64
    pessoas_desaparecidas         float64
    encontro_cadaver                int64
    encontro_ossada               float64
    pol_militares_mortos_serv     float64
    pol_civis_mortos_serv         float64
    indicador_letalidade            int64
    indicador_roubo_rua             int64
    indicador_roubo_veic            int64
    registro_ocorrencias          float64
    fase                            int64
    dtype: object



Aqui é interessante notar que todas as variáveis presentes no dataset são do tipo numérico, sejam elas `int` ou `float`.

Com tudo isso em mente, é hora de darmos uma olhada nesse dataset e ver como ele está organizado, como esses dados são apresentados e termos uma ideia do que podemos fazer com eles.


```python
# ver as 5 primeiras entradas do dataset
df.head()
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
      <th>vano</th>
      <th>mes</th>
      <th>hom_doloso</th>
      <th>lesao_corp_morte</th>
      <th>latrocinio</th>
      <th>hom_por_interv_policial</th>
      <th>tentat_hom</th>
      <th>lesao_corp_dolosa</th>
      <th>estupro</th>
      <th>hom_culposo</th>
      <th>lesao_corp_culposa</th>
      <th>roubo_comercio</th>
      <th>roubo_residencia</th>
      <th>roubo_veiculo</th>
      <th>roubo_carga</th>
      <th>roubo_transeunte</th>
      <th>roubo_em_coletivo</th>
      <th>roubo_banco</th>
      <th>roubo_cx_eletronico</th>
      <th>roubo_celular</th>
      <th>roubo_conducao_saque</th>
      <th>roubo_apos_saque</th>
      <th>roubo_bicicleta</th>
      <th>outros_roubos</th>
      <th>total_roubos</th>
      <th>furto_veiculos</th>
      <th>furto_transeunte</th>
      <th>furto_coletivo</th>
      <th>furto_celular</th>
      <th>furto_bicicleta</th>
      <th>outros_furtos</th>
      <th>total_furtos</th>
      <th>sequestro</th>
      <th>extorsao</th>
      <th>sequestro_relampago</th>
      <th>estelionato</th>
      <th>apreensao_drogas</th>
      <th>posse_drogas</th>
      <th>trafico_drogas</th>
      <th>apreensao_drogas_sem_autor</th>
      <th>recuperacao_veiculos</th>
      <th>apf</th>
      <th>aaapai</th>
      <th>cmp</th>
      <th>cmba</th>
      <th>ameaca</th>
      <th>pessoas_desaparecidas</th>
      <th>encontro_cadaver</th>
      <th>encontro_ossada</th>
      <th>pol_militares_mortos_serv</th>
      <th>pol_civis_mortos_serv</th>
      <th>indicador_letalidade</th>
      <th>indicador_roubo_rua</th>
      <th>indicador_roubo_veic</th>
      <th>registro_ocorrencias</th>
      <th>fase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1991</td>
      <td>1</td>
      <td>657</td>
      <td>NaN</td>
      <td>15</td>
      <td>NaN</td>
      <td>162</td>
      <td>3051</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>350</td>
      <td>188</td>
      <td>1174</td>
      <td>63</td>
      <td>967</td>
      <td>381</td>
      <td>37</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>158</td>
      <td>3318</td>
      <td>2009</td>
      <td>1147</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3561</td>
      <td>6717</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>133</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>872</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>217</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>672</td>
      <td>1348</td>
      <td>1174</td>
      <td>NaN</td>
      <td>3</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1991</td>
      <td>2</td>
      <td>732</td>
      <td>NaN</td>
      <td>17</td>
      <td>NaN</td>
      <td>175</td>
      <td>3421</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>357</td>
      <td>171</td>
      <td>1097</td>
      <td>64</td>
      <td>963</td>
      <td>432</td>
      <td>35</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>142</td>
      <td>3261</td>
      <td>1850</td>
      <td>1066</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3309</td>
      <td>6225</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>127</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>751</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>209</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>749</td>
      <td>1395</td>
      <td>1097</td>
      <td>NaN</td>
      <td>3</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1991</td>
      <td>3</td>
      <td>713</td>
      <td>NaN</td>
      <td>25</td>
      <td>NaN</td>
      <td>216</td>
      <td>3613</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>443</td>
      <td>191</td>
      <td>1265</td>
      <td>71</td>
      <td>963</td>
      <td>422</td>
      <td>43</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>131</td>
      <td>3529</td>
      <td>2084</td>
      <td>1062</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3573</td>
      <td>6719</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>80</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>789</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>188</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>738</td>
      <td>1385</td>
      <td>1265</td>
      <td>NaN</td>
      <td>3</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1991</td>
      <td>4</td>
      <td>634</td>
      <td>NaN</td>
      <td>20</td>
      <td>NaN</td>
      <td>200</td>
      <td>3211</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>425</td>
      <td>199</td>
      <td>1415</td>
      <td>81</td>
      <td>1186</td>
      <td>354</td>
      <td>36</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>112</td>
      <td>3808</td>
      <td>2085</td>
      <td>1195</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3290</td>
      <td>6570</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>133</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>787</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>140</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>654</td>
      <td>1540</td>
      <td>1415</td>
      <td>NaN</td>
      <td>3</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1991</td>
      <td>5</td>
      <td>650</td>
      <td>NaN</td>
      <td>20</td>
      <td>NaN</td>
      <td>146</td>
      <td>3051</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>553</td>
      <td>231</td>
      <td>1449</td>
      <td>82</td>
      <td>931</td>
      <td>335</td>
      <td>27</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>147</td>
      <td>3755</td>
      <td>2185</td>
      <td>723</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3546</td>
      <td>6454</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>163</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>845</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>78</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>670</td>
      <td>1266</td>
      <td>1449</td>
      <td>NaN</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>




```python
# ver as 5 últimas entradas do dataset
df.tail()
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
      <th>vano</th>
      <th>mes</th>
      <th>hom_doloso</th>
      <th>lesao_corp_morte</th>
      <th>latrocinio</th>
      <th>hom_por_interv_policial</th>
      <th>tentat_hom</th>
      <th>lesao_corp_dolosa</th>
      <th>estupro</th>
      <th>hom_culposo</th>
      <th>lesao_corp_culposa</th>
      <th>roubo_comercio</th>
      <th>roubo_residencia</th>
      <th>roubo_veiculo</th>
      <th>roubo_carga</th>
      <th>roubo_transeunte</th>
      <th>roubo_em_coletivo</th>
      <th>roubo_banco</th>
      <th>roubo_cx_eletronico</th>
      <th>roubo_celular</th>
      <th>roubo_conducao_saque</th>
      <th>roubo_apos_saque</th>
      <th>roubo_bicicleta</th>
      <th>outros_roubos</th>
      <th>total_roubos</th>
      <th>furto_veiculos</th>
      <th>furto_transeunte</th>
      <th>furto_coletivo</th>
      <th>furto_celular</th>
      <th>furto_bicicleta</th>
      <th>outros_furtos</th>
      <th>total_furtos</th>
      <th>sequestro</th>
      <th>extorsao</th>
      <th>sequestro_relampago</th>
      <th>estelionato</th>
      <th>apreensao_drogas</th>
      <th>posse_drogas</th>
      <th>trafico_drogas</th>
      <th>apreensao_drogas_sem_autor</th>
      <th>recuperacao_veiculos</th>
      <th>apf</th>
      <th>aaapai</th>
      <th>cmp</th>
      <th>cmba</th>
      <th>ameaca</th>
      <th>pessoas_desaparecidas</th>
      <th>encontro_cadaver</th>
      <th>encontro_ossada</th>
      <th>pol_militares_mortos_serv</th>
      <th>pol_civis_mortos_serv</th>
      <th>indicador_letalidade</th>
      <th>indicador_roubo_rua</th>
      <th>indicador_roubo_veic</th>
      <th>registro_ocorrencias</th>
      <th>fase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>339</th>
      <td>2019</td>
      <td>4</td>
      <td>360</td>
      <td>1.0</td>
      <td>11</td>
      <td>124.0</td>
      <td>466</td>
      <td>5573</td>
      <td>483.0</td>
      <td>172.0</td>
      <td>2182.0</td>
      <td>502</td>
      <td>124</td>
      <td>3755</td>
      <td>667</td>
      <td>7031</td>
      <td>1585</td>
      <td>2</td>
      <td>4.0</td>
      <td>2424.0</td>
      <td>10.0</td>
      <td>82.0</td>
      <td>23.0</td>
      <td>2245</td>
      <td>18454</td>
      <td>1244</td>
      <td>1519</td>
      <td>607.0</td>
      <td>1318.0</td>
      <td>211.0</td>
      <td>7633</td>
      <td>12532</td>
      <td>0.0</td>
      <td>151.0</td>
      <td>11.0</td>
      <td>3260.0</td>
      <td>1909</td>
      <td>745.0</td>
      <td>1021.0</td>
      <td>187.0</td>
      <td>2624</td>
      <td>3128.0</td>
      <td>497.0</td>
      <td>1648.0</td>
      <td>350.0</td>
      <td>5310.0</td>
      <td>408.0</td>
      <td>22</td>
      <td>3.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>496</td>
      <td>11040</td>
      <td>3755</td>
      <td>67797.0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>340</th>
      <td>2019</td>
      <td>5</td>
      <td>345</td>
      <td>2.0</td>
      <td>15</td>
      <td>172.0</td>
      <td>478</td>
      <td>4958</td>
      <td>465.0</td>
      <td>145.0</td>
      <td>2304.0</td>
      <td>514</td>
      <td>93</td>
      <td>3649</td>
      <td>710</td>
      <td>7332</td>
      <td>1625</td>
      <td>1</td>
      <td>3.0</td>
      <td>2427.0</td>
      <td>9.0</td>
      <td>87.0</td>
      <td>7.0</td>
      <td>2216</td>
      <td>18673</td>
      <td>1480</td>
      <td>1475</td>
      <td>715.0</td>
      <td>1423.0</td>
      <td>220.0</td>
      <td>7711</td>
      <td>13024</td>
      <td>0.0</td>
      <td>147.0</td>
      <td>7.0</td>
      <td>3498.0</td>
      <td>2034</td>
      <td>735.0</td>
      <td>1133.0</td>
      <td>199.0</td>
      <td>2450</td>
      <td>3169.0</td>
      <td>527.0</td>
      <td>1771.0</td>
      <td>248.0</td>
      <td>5068.0</td>
      <td>390.0</td>
      <td>20</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>534</td>
      <td>11384</td>
      <td>3649</td>
      <td>68336.0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>341</th>
      <td>2019</td>
      <td>6</td>
      <td>332</td>
      <td>3.0</td>
      <td>8</td>
      <td>153.0</td>
      <td>436</td>
      <td>4769</td>
      <td>414.0</td>
      <td>152.0</td>
      <td>2481.0</td>
      <td>382</td>
      <td>85</td>
      <td>3115</td>
      <td>599</td>
      <td>6077</td>
      <td>1287</td>
      <td>1</td>
      <td>4.0</td>
      <td>2187.0</td>
      <td>19.0</td>
      <td>83.0</td>
      <td>9.0</td>
      <td>1984</td>
      <td>15832</td>
      <td>1251</td>
      <td>1443</td>
      <td>630.0</td>
      <td>1345.0</td>
      <td>189.0</td>
      <td>7224</td>
      <td>12082</td>
      <td>0.0</td>
      <td>133.0</td>
      <td>6.0</td>
      <td>3029.0</td>
      <td>1854</td>
      <td>763.0</td>
      <td>976.0</td>
      <td>158.0</td>
      <td>2041</td>
      <td>2848.0</td>
      <td>467.0</td>
      <td>1585.0</td>
      <td>233.0</td>
      <td>4497.0</td>
      <td>403.0</td>
      <td>20</td>
      <td>1.0</td>
      <td>3.0</td>
      <td>0.0</td>
      <td>496</td>
      <td>9551</td>
      <td>3115</td>
      <td>61202.0</td>
      <td>3</td>
    </tr>
    <tr>
      <th>342</th>
      <td>2019</td>
      <td>7</td>
      <td>309</td>
      <td>5.0</td>
      <td>10</td>
      <td>194.0</td>
      <td>399</td>
      <td>4740</td>
      <td>402.0</td>
      <td>140.0</td>
      <td>2334.0</td>
      <td>386</td>
      <td>72</td>
      <td>3198</td>
      <td>691</td>
      <td>6637</td>
      <td>1208</td>
      <td>1</td>
      <td>0.0</td>
      <td>2226.0</td>
      <td>12.0</td>
      <td>97.0</td>
      <td>16.0</td>
      <td>2110</td>
      <td>16654</td>
      <td>1353</td>
      <td>1570</td>
      <td>746.0</td>
      <td>1391.0</td>
      <td>230.0</td>
      <td>7632</td>
      <td>12922</td>
      <td>0.0</td>
      <td>135.0</td>
      <td>12.0</td>
      <td>3627.0</td>
      <td>1861</td>
      <td>709.0</td>
      <td>996.0</td>
      <td>187.0</td>
      <td>2355</td>
      <td>2907.0</td>
      <td>490.0</td>
      <td>1856.0</td>
      <td>267.0</td>
      <td>4678.0</td>
      <td>400.0</td>
      <td>32</td>
      <td>7.0</td>
      <td>0.0</td>
      <td>0.0</td>
      <td>518</td>
      <td>10071</td>
      <td>3198</td>
      <td>65817.0</td>
      <td>2</td>
    </tr>
    <tr>
      <th>343</th>
      <td>2019</td>
      <td>8</td>
      <td>318</td>
      <td>1.0</td>
      <td>6</td>
      <td>170.0</td>
      <td>457</td>
      <td>4760</td>
      <td>460.0</td>
      <td>156.0</td>
      <td>2437.0</td>
      <td>433</td>
      <td>103</td>
      <td>3181</td>
      <td>587</td>
      <td>6452</td>
      <td>1215</td>
      <td>1</td>
      <td>1.0</td>
      <td>2245.0</td>
      <td>9.0</td>
      <td>98.0</td>
      <td>17.0</td>
      <td>1794</td>
      <td>16136</td>
      <td>1361</td>
      <td>1568</td>
      <td>674.0</td>
      <td>1534.0</td>
      <td>185.0</td>
      <td>7280</td>
      <td>12602</td>
      <td>0.0</td>
      <td>132.0</td>
      <td>12.0</td>
      <td>3546.0</td>
      <td>1862</td>
      <td>668.0</td>
      <td>1067.0</td>
      <td>171.0</td>
      <td>2081</td>
      <td>3114.0</td>
      <td>476.0</td>
      <td>2028.0</td>
      <td>265.0</td>
      <td>4662.0</td>
      <td>367.0</td>
      <td>27</td>
      <td>8.0</td>
      <td>2.0</td>
      <td>0.0</td>
      <td>495</td>
      <td>9912</td>
      <td>3181</td>
      <td>65285.0</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>



É interessante reaparar que algumas variáveis apresentam muitos dados ausentes. Dado o grande intervalo de tempo apresentado no dataset, podemos sugerir algumas hipóteses do que poderia explicar essa ausência, e como podemos contorná-la para continuar explorando o conjunto de dados.

* Como temos dados registrados desde o ano de 1991, alguns desses crimes poderiam não estar sendo contabilizados pelo órgão que coletou esses dados e gerou esse dataset. Estarem ausentes não significa que não existiram, e vamos pensar em formas de lidar com essa ausência logo mais.

* Infelizmente, se hoje temos uma cultura de dados que ainda deixa a desejar, o que dizer da mesma no período em tela? Devemos levar em consideração também o formato em que esses dados eram registrados à época. Será que foram registrados corretamente e perdidos em períodos de digitalização? Ou será que o gerenciamento dessas informações sempre foi falho?

Antes de decidirmos o que fazer em relação aos dados ausentes, vamos verificar a proporção deles em relação à totalidade do dataset.


```python
# ver os valores faltantes das colunas
(df.isnull().sum()/ df.shape[0]).sort_values(ascending=False)
```




    roubo_bicicleta               0.802326
    furto_bicicleta               0.802326
    apreensao_drogas_sem_autor    0.523256
    posse_drogas                  0.523256
    apf                           0.523256
    aaapai                        0.523256
    cmp                           0.523256
    trafico_drogas                0.523256
    cmba                          0.523256
    sequestro_relampago           0.418605
    furto_coletivo                0.418605
    extorsao                      0.418605
    furto_celular                 0.418605
    roubo_apos_saque              0.418605
    roubo_conducao_saque          0.418605
    roubo_cx_eletronico           0.418605
    estupro                       0.418605
    estelionato                   0.418605
    pol_civis_mortos_serv         0.418605
    pol_militares_mortos_serv     0.418605
    pessoas_desaparecidas         0.383721
    encontro_ossada               0.383721
    roubo_celular                 0.313953
    lesao_corp_culposa            0.279070
    ameaca                        0.279070
    sequestro                     0.279070
    lesao_corp_morte              0.279070
    registro_ocorrencias          0.279070
    hom_por_interv_policial       0.244186
    hom_culposo                   0.244186
    tentat_hom                    0.000000
    roubo_comercio                0.000000
    roubo_residencia              0.000000
    latrocinio                    0.000000
    lesao_corp_dolosa             0.000000
    hom_doloso                    0.000000
    roubo_transeunte              0.000000
    mes                           0.000000
    roubo_veiculo                 0.000000
    roubo_carga                   0.000000
    fase                          0.000000
    roubo_em_coletivo             0.000000
    roubo_banco                   0.000000
    outros_roubos                 0.000000
    total_roubos                  0.000000
    furto_veiculos                0.000000
    furto_transeunte              0.000000
    outros_furtos                 0.000000
    total_furtos                  0.000000
    apreensao_drogas              0.000000
    recuperacao_veiculos          0.000000
    encontro_cadaver              0.000000
    indicador_letalidade          0.000000
    indicador_roubo_rua           0.000000
    indicador_roubo_veic          0.000000
    vano                          0.000000
    dtype: float64



Acima, podemos ver que algumas das variáveis tem valores faltantes em proporções consideráveis como é o exemplo de crimes relacionados a bicicletas e apreensão de drogas.

Como os dados estão estruturados ao longo do tempo, uma forma de realizar uma análise que condiz melhor com a realidade, podemos analisá-los a partir da data em que começaram a ser registrados.

Além disso, existem outras formas de encararmos e explorarmos os dados, como por exemplo:

* Números anuais por crime.
* Números mensais por crime.
* Categorias de crimes (Contra a vida, Contra o patrimônio, etc.)

## Informações Estatísticas da Violência no Rio de Janeiro

Usando o método `describe()` podemos visualizar a distribuição estatística do dataframe.

Utilizando esse método, temos informações que podem nos ajudar a:

* Identificar outliers.
* Entender melhor a distribuição dos dados.
* Identificar falhas na distribuição dos dados.
* Após a limpeza dos dados, verificar a eficácia e as diferenças entre os dados tratados e não tratados.



```python
# imprimir o resultado estatístico do DataFrame
df.describe()
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
      <th>vano</th>
      <th>mes</th>
      <th>hom_doloso</th>
      <th>lesao_corp_morte</th>
      <th>latrocinio</th>
      <th>hom_por_interv_policial</th>
      <th>tentat_hom</th>
      <th>lesao_corp_dolosa</th>
      <th>estupro</th>
      <th>hom_culposo</th>
      <th>lesao_corp_culposa</th>
      <th>roubo_comercio</th>
      <th>roubo_residencia</th>
      <th>roubo_veiculo</th>
      <th>roubo_carga</th>
      <th>roubo_transeunte</th>
      <th>roubo_em_coletivo</th>
      <th>roubo_banco</th>
      <th>roubo_cx_eletronico</th>
      <th>roubo_celular</th>
      <th>roubo_conducao_saque</th>
      <th>roubo_apos_saque</th>
      <th>roubo_bicicleta</th>
      <th>outros_roubos</th>
      <th>total_roubos</th>
      <th>furto_veiculos</th>
      <th>furto_transeunte</th>
      <th>furto_coletivo</th>
      <th>furto_celular</th>
      <th>furto_bicicleta</th>
      <th>outros_furtos</th>
      <th>total_furtos</th>
      <th>sequestro</th>
      <th>extorsao</th>
      <th>sequestro_relampago</th>
      <th>estelionato</th>
      <th>apreensao_drogas</th>
      <th>posse_drogas</th>
      <th>trafico_drogas</th>
      <th>apreensao_drogas_sem_autor</th>
      <th>recuperacao_veiculos</th>
      <th>apf</th>
      <th>aaapai</th>
      <th>cmp</th>
      <th>cmba</th>
      <th>ameaca</th>
      <th>pessoas_desaparecidas</th>
      <th>encontro_cadaver</th>
      <th>encontro_ossada</th>
      <th>pol_militares_mortos_serv</th>
      <th>pol_civis_mortos_serv</th>
      <th>indicador_letalidade</th>
      <th>indicador_roubo_rua</th>
      <th>indicador_roubo_veic</th>
      <th>registro_ocorrencias</th>
      <th>fase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>count</th>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>248.000000</td>
      <td>344.000000</td>
      <td>260.00000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>200.000000</td>
      <td>260.000000</td>
      <td>248.000000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>200.000000</td>
      <td>236.000000</td>
      <td>200.000000</td>
      <td>200.000000</td>
      <td>68.000000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>200.000000</td>
      <td>200.000000</td>
      <td>68.000000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>248.000000</td>
      <td>200.000000</td>
      <td>200.000000</td>
      <td>200.000000</td>
      <td>344.000000</td>
      <td>164.000000</td>
      <td>164.000000</td>
      <td>164.000000</td>
      <td>344.000000</td>
      <td>164.000000</td>
      <td>164.000000</td>
      <td>164.000000</td>
      <td>164.000000</td>
      <td>248.000000</td>
      <td>212.000000</td>
      <td>344.000000</td>
      <td>212.000000</td>
      <td>200.000000</td>
      <td>200.00000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>344.000000</td>
      <td>248.000000</td>
      <td>344.000000</td>
    </tr>
    <tr>
      <th>mean</th>
      <td>2004.837209</td>
      <td>6.453488</td>
      <td>504.555233</td>
      <td>4.334677</td>
      <td>15.668605</td>
      <td>71.90000</td>
      <td>318.267442</td>
      <td>5492.799419</td>
      <td>369.945000</td>
      <td>212.119231</td>
      <td>3410.762097</td>
      <td>455.005814</td>
      <td>133.284884</td>
      <td>2448.697674</td>
      <td>340.322674</td>
      <td>3418.520349</td>
      <td>659.002907</td>
      <td>9.375000</td>
      <td>3.550000</td>
      <td>957.974576</td>
      <td>11.320000</td>
      <td>145.665000</td>
      <td>10.808824</td>
      <td>1211.223837</td>
      <td>9428.119186</td>
      <td>1674.671512</td>
      <td>1518.921512</td>
      <td>650.230000</td>
      <td>1449.175000</td>
      <td>124.808824</td>
      <td>5994.328488</td>
      <td>10433.177326</td>
      <td>0.665323</td>
      <td>133.265000</td>
      <td>7.755000</td>
      <td>2231.860000</td>
      <td>1071.930233</td>
      <td>609.237805</td>
      <td>759.146341</td>
      <td>186.146341</td>
      <td>1772.238372</td>
      <td>2319.585366</td>
      <td>568.628049</td>
      <td>1255.512195</td>
      <td>83.042683</td>
      <td>5530.326613</td>
      <td>435.132075</td>
      <td>66.261628</td>
      <td>3.301887</td>
      <td>2.085000</td>
      <td>0.34000</td>
      <td>577.691860</td>
      <td>4734.738372</td>
      <td>2448.697674</td>
      <td>53794.161290</td>
      <td>2.994186</td>
    </tr>
    <tr>
      <th>std</th>
      <td>8.289036</td>
      <td>3.447759</td>
      <td>113.774481</td>
      <td>2.755357</td>
      <td>5.861427</td>
      <td>36.48882</td>
      <td>127.606645</td>
      <td>1514.220579</td>
      <td>94.392434</td>
      <td>54.234700</td>
      <td>776.156561</td>
      <td>119.765076</td>
      <td>44.317963</td>
      <td>789.689559</td>
      <td>223.855243</td>
      <td>2459.017549</td>
      <td>297.622342</td>
      <td>10.564894</td>
      <td>2.634808</td>
      <td>624.309034</td>
      <td>6.404113</td>
      <td>55.392367</td>
      <td>7.767628</td>
      <td>800.576723</td>
      <td>4578.013053</td>
      <td>294.852020</td>
      <td>971.853102</td>
      <td>268.836273</td>
      <td>368.638776</td>
      <td>80.247427</td>
      <td>1964.767779</td>
      <td>3696.725756</td>
      <td>0.959128</td>
      <td>43.303821</td>
      <td>5.089646</td>
      <td>772.093064</td>
      <td>643.381991</td>
      <td>246.307700</td>
      <td>312.871487</td>
      <td>36.221243</td>
      <td>656.761843</td>
      <td>650.520866</td>
      <td>248.040620</td>
      <td>357.035450</td>
      <td>62.951374</td>
      <td>1100.752625</td>
      <td>65.353516</td>
      <td>34.464519</td>
      <td>2.192922</td>
      <td>1.761616</td>
      <td>0.63752</td>
      <td>104.973929</td>
      <td>3204.054864</td>
      <td>789.689559</td>
      <td>11039.051992</td>
      <td>0.076138</td>
    </tr>
    <tr>
      <th>min</th>
      <td>1991.000000</td>
      <td>1.000000</td>
      <td>272.000000</td>
      <td>0.000000</td>
      <td>2.000000</td>
      <td>10.00000</td>
      <td>122.000000</td>
      <td>2350.000000</td>
      <td>188.000000</td>
      <td>111.000000</td>
      <td>848.000000</td>
      <td>243.000000</td>
      <td>49.000000</td>
      <td>1097.000000</td>
      <td>59.000000</td>
      <td>688.000000</td>
      <td>281.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>1.000000</td>
      <td>11.000000</td>
      <td>0.000000</td>
      <td>81.000000</td>
      <td>3261.000000</td>
      <td>1159.000000</td>
      <td>322.000000</td>
      <td>23.000000</td>
      <td>484.000000</td>
      <td>0.000000</td>
      <td>2322.000000</td>
      <td>4691.000000</td>
      <td>0.000000</td>
      <td>23.000000</td>
      <td>0.000000</td>
      <td>717.000000</td>
      <td>80.000000</td>
      <td>248.000000</td>
      <td>262.000000</td>
      <td>114.000000</td>
      <td>729.000000</td>
      <td>1173.000000</td>
      <td>225.000000</td>
      <td>628.000000</td>
      <td>21.000000</td>
      <td>1409.000000</td>
      <td>236.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.000000</td>
      <td>0.00000</td>
      <td>334.000000</td>
      <td>1086.000000</td>
      <td>1097.000000</td>
      <td>30621.000000</td>
      <td>2.000000</td>
    </tr>
    <tr>
      <th>25%</th>
      <td>1998.000000</td>
      <td>3.000000</td>
      <td>413.750000</td>
      <td>2.000000</td>
      <td>11.000000</td>
      <td>41.75000</td>
      <td>200.000000</td>
      <td>4067.000000</td>
      <td>282.000000</td>
      <td>176.000000</td>
      <td>3040.000000</td>
      <td>365.750000</td>
      <td>103.750000</td>
      <td>1889.750000</td>
      <td>208.250000</td>
      <td>1053.000000</td>
      <td>430.750000</td>
      <td>2.000000</td>
      <td>2.000000</td>
      <td>549.750000</td>
      <td>6.000000</td>
      <td>106.000000</td>
      <td>2.250000</td>
      <td>811.750000</td>
      <td>5107.750000</td>
      <td>1430.500000</td>
      <td>732.000000</td>
      <td>501.000000</td>
      <td>1297.000000</td>
      <td>3.750000</td>
      <td>3978.000000</td>
      <td>6700.250000</td>
      <td>0.000000</td>
      <td>109.750000</td>
      <td>4.750000</td>
      <td>1654.500000</td>
      <td>714.500000</td>
      <td>360.500000</td>
      <td>426.000000</td>
      <td>160.000000</td>
      <td>1276.750000</td>
      <td>1681.000000</td>
      <td>332.500000</td>
      <td>950.500000</td>
      <td>46.000000</td>
      <td>4868.500000</td>
      <td>390.000000</td>
      <td>42.000000</td>
      <td>2.000000</td>
      <td>1.000000</td>
      <td>0.00000</td>
      <td>500.000000</td>
      <td>1557.000000</td>
      <td>1889.750000</td>
      <td>45374.500000</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>50%</th>
      <td>2005.000000</td>
      <td>6.000000</td>
      <td>507.000000</td>
      <td>4.000000</td>
      <td>15.000000</td>
      <td>70.00000</td>
      <td>318.500000</td>
      <td>5830.500000</td>
      <td>384.000000</td>
      <td>203.000000</td>
      <td>3372.000000</td>
      <td>433.500000</td>
      <td>125.000000</td>
      <td>2322.000000</td>
      <td>285.500000</td>
      <td>2788.500000</td>
      <td>592.000000</td>
      <td>4.500000</td>
      <td>3.000000</td>
      <td>717.500000</td>
      <td>10.000000</td>
      <td>149.500000</td>
      <td>13.000000</td>
      <td>1154.000000</td>
      <td>9243.500000</td>
      <td>1637.500000</td>
      <td>1076.500000</td>
      <td>717.500000</td>
      <td>1492.500000</td>
      <td>158.000000</td>
      <td>6559.000000</td>
      <td>10448.500000</td>
      <td>0.000000</td>
      <td>132.500000</td>
      <td>7.000000</td>
      <td>2170.500000</td>
      <td>979.500000</td>
      <td>623.000000</td>
      <td>815.000000</td>
      <td>184.000000</td>
      <td>1755.000000</td>
      <td>2424.000000</td>
      <td>530.000000</td>
      <td>1259.000000</td>
      <td>58.000000</td>
      <td>5455.000000</td>
      <td>427.000000</td>
      <td>60.500000</td>
      <td>3.000000</td>
      <td>2.000000</td>
      <td>0.00000</td>
      <td>590.000000</td>
      <td>4190.000000</td>
      <td>2322.000000</td>
      <td>54926.500000</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>75%</th>
      <td>2012.000000</td>
      <td>9.000000</td>
      <td>577.250000</td>
      <td>6.000000</td>
      <td>19.000000</td>
      <td>96.25000</td>
      <td>398.250000</td>
      <td>6582.500000</td>
      <td>441.250000</td>
      <td>236.250000</td>
      <td>3733.500000</td>
      <td>536.250000</td>
      <td>151.250000</td>
      <td>2778.000000</td>
      <td>401.250000</td>
      <td>5385.250000</td>
      <td>784.000000</td>
      <td>14.000000</td>
      <td>5.000000</td>
      <td>1306.750000</td>
      <td>15.000000</td>
      <td>180.500000</td>
      <td>17.000000</td>
      <td>1635.000000</td>
      <td>11590.500000</td>
      <td>1833.250000</td>
      <td>2334.250000</td>
      <td>860.000000</td>
      <td>1731.250000</td>
      <td>181.500000</td>
      <td>7601.500000</td>
      <td>13877.500000</td>
      <td>1.000000</td>
      <td>152.250000</td>
      <td>11.000000</td>
      <td>2880.500000</td>
      <td>1546.000000</td>
      <td>763.500000</td>
      <td>1045.250000</td>
      <td>208.000000</td>
      <td>2202.000000</td>
      <td>2905.500000</td>
      <td>793.500000</td>
      <td>1515.250000</td>
      <td>93.000000</td>
      <td>6261.750000</td>
      <td>481.500000</td>
      <td>87.000000</td>
      <td>4.000000</td>
      <td>3.000000</td>
      <td>1.00000</td>
      <td>649.000000</td>
      <td>6872.750000</td>
      <td>2778.000000</td>
      <td>63292.500000</td>
      <td>3.000000</td>
    </tr>
    <tr>
      <th>max</th>
      <td>2019.000000</td>
      <td>12.000000</td>
      <td>831.000000</td>
      <td>21.000000</td>
      <td>36.000000</td>
      <td>194.00000</td>
      <td>645.000000</td>
      <td>9050.000000</td>
      <td>561.000000</td>
      <td>449.000000</td>
      <td>6653.000000</td>
      <td>914.000000</td>
      <td>300.000000</td>
      <td>5358.000000</td>
      <td>1329.000000</td>
      <td>9669.000000</td>
      <td>1628.000000</td>
      <td>54.000000</td>
      <td>12.000000</td>
      <td>2626.000000</td>
      <td>37.000000</td>
      <td>283.000000</td>
      <td>26.000000</td>
      <td>3661.000000</td>
      <td>23203.000000</td>
      <td>2630.000000</td>
      <td>5368.000000</td>
      <td>1036.000000</td>
      <td>2215.000000</td>
      <td>243.000000</td>
      <td>9880.000000</td>
      <td>18003.000000</td>
      <td>5.000000</td>
      <td>271.000000</td>
      <td>31.000000</td>
      <td>3627.000000</td>
      <td>2543.000000</td>
      <td>1160.000000</td>
      <td>1282.000000</td>
      <td>289.000000</td>
      <td>3695.000000</td>
      <td>3425.000000</td>
      <td>1114.000000</td>
      <td>2314.000000</td>
      <td>350.000000</td>
      <td>9034.000000</td>
      <td>628.000000</td>
      <td>217.000000</td>
      <td>13.000000</td>
      <td>10.000000</td>
      <td>3.00000</td>
      <td>848.000000</td>
      <td>13833.000000</td>
      <td>5358.000000</td>
      <td>75403.000000</td>
      <td>3.000000</td>
    </tr>
  </tbody>
</table>
</div>



Somando os registros de furto(`furto_veiclos` ,`furto_transeunte` ,`furto_coletivo` , `furto_celular` , `furto_bicicleta` , `outros_furtos`) é o que tem a maior média, com cerca de 10.433 registros. Enquanto o de policiais civis mortos em serviço tem a menor média com cerca de 0.34.

### Crimes Contra o Patrimônio - Veículos

Vamos dedicar essa seção do projeto para investigar dados em relação aos crimes de roubo e furto de veículos.

Problema comum nas grandes metrópoles brasileiras, esse tipo de crime merece destaque em nosso projeto. Como temos dados sobre roubo, furto e recuperação de veículos, conseguimos utilizar essas informações para encontrar resultados pertinentes em relação à situação e ao combate a esse tipo de crime.


```python
# ver a média da variável roubo_veiculo
mean_roubo_veiculo = df.roubo_veiculo.mean()
print(round(mean_roubo_veiculo,2))
```

    2448.7
    

* Podemos verificar que a média de roubo de veículos é de 2488 por mês.


```python
# ver a média da variável furto_veiculos
mean_furto_veiculos = df.furto_veiculos.mean()
print(round(mean_furto_veiculos,2))
```

    1674.67
    

* Em relação aos furtos, os números são um pouco menores, mas ainda altos. Foram registrados em média, 1674 furtos de veículos por mês.


```python
# ver a média da variável recuperacao_veiculos
mean_recuperacao_veiculos = df.recuperacao_veiculos.mean()
print(round(mean_recuperacao_veiculos,2))
```

    1772.24
    


```python
# total de veículos perdidos
total = mean_roubo_veiculo + mean_furto_veiculos
print(round(total,2))

```

    4123.37
    


```python
# porcentagem de veículos recuperados
pct = mean_recuperacao_veiculos/total*100

print(pct)
```

    42.980346705075085
    

Podemos concluir que:
* Há uma média de 4123 veículos perdidos por mês.
* Cerca de 1772 veículos são recuperados por mês.
* 42.98% dos veículos roubados ou furtados são recuperados.

### Crimes Contra o Patrimônio - Roubo

Abaixo, vamos identificar qual foi o máximo e mínimo registrado para a variável `total_roubos` ao longo do período observado no dataset.


```python
# valores max e min de total_roubos
max_total_roubos = df.total_roubos.max()
min_total_roubos = df.total_roubos.min()

# imprimir os valores máximos e mínimos
print("Min:\t{}".format(min_total_roubos))
print("Max:\t{}".format(max_total_roubos))
```

    Min:	3261
    Max:	23203
    

Acima podemos ver que o mês com menor número de casos foram registrados 3261 roubos.

Em relação ao mês com números mais elevado, houveram 23203 roubos.

Vamos investigar um pouco mais a fundo essas entradas.


```python
# verificando o mês com menor número de roubo
df[df.total_roubos == 3261]
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
      <th>vano</th>
      <th>mes</th>
      <th>hom_doloso</th>
      <th>lesao_corp_morte</th>
      <th>latrocinio</th>
      <th>hom_por_interv_policial</th>
      <th>tentat_hom</th>
      <th>lesao_corp_dolosa</th>
      <th>estupro</th>
      <th>hom_culposo</th>
      <th>lesao_corp_culposa</th>
      <th>roubo_comercio</th>
      <th>roubo_residencia</th>
      <th>roubo_veiculo</th>
      <th>roubo_carga</th>
      <th>roubo_transeunte</th>
      <th>roubo_em_coletivo</th>
      <th>roubo_banco</th>
      <th>roubo_cx_eletronico</th>
      <th>roubo_celular</th>
      <th>roubo_conducao_saque</th>
      <th>roubo_apos_saque</th>
      <th>roubo_bicicleta</th>
      <th>outros_roubos</th>
      <th>total_roubos</th>
      <th>furto_veiculos</th>
      <th>furto_transeunte</th>
      <th>furto_coletivo</th>
      <th>furto_celular</th>
      <th>furto_bicicleta</th>
      <th>outros_furtos</th>
      <th>total_furtos</th>
      <th>sequestro</th>
      <th>extorsao</th>
      <th>sequestro_relampago</th>
      <th>estelionato</th>
      <th>apreensao_drogas</th>
      <th>posse_drogas</th>
      <th>trafico_drogas</th>
      <th>apreensao_drogas_sem_autor</th>
      <th>recuperacao_veiculos</th>
      <th>apf</th>
      <th>aaapai</th>
      <th>cmp</th>
      <th>cmba</th>
      <th>ameaca</th>
      <th>pessoas_desaparecidas</th>
      <th>encontro_cadaver</th>
      <th>encontro_ossada</th>
      <th>pol_militares_mortos_serv</th>
      <th>pol_civis_mortos_serv</th>
      <th>indicador_letalidade</th>
      <th>indicador_roubo_rua</th>
      <th>indicador_roubo_veic</th>
      <th>registro_ocorrencias</th>
      <th>fase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>1991</td>
      <td>2</td>
      <td>732</td>
      <td>NaN</td>
      <td>17</td>
      <td>NaN</td>
      <td>175</td>
      <td>3421</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>357</td>
      <td>171</td>
      <td>1097</td>
      <td>64</td>
      <td>963</td>
      <td>432</td>
      <td>35</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>142</td>
      <td>3261</td>
      <td>1850</td>
      <td>1066</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>3309</td>
      <td>6225</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>127</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>751</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>209</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>749</td>
      <td>1395</td>
      <td>1097</td>
      <td>NaN</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>




```python
# verificando o mês com maior número de roubos
df[df.total_roubos == 23203]
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
      <th>vano</th>
      <th>mes</th>
      <th>hom_doloso</th>
      <th>lesao_corp_morte</th>
      <th>latrocinio</th>
      <th>hom_por_interv_policial</th>
      <th>tentat_hom</th>
      <th>lesao_corp_dolosa</th>
      <th>estupro</th>
      <th>hom_culposo</th>
      <th>lesao_corp_culposa</th>
      <th>roubo_comercio</th>
      <th>roubo_residencia</th>
      <th>roubo_veiculo</th>
      <th>roubo_carga</th>
      <th>roubo_transeunte</th>
      <th>roubo_em_coletivo</th>
      <th>roubo_banco</th>
      <th>roubo_cx_eletronico</th>
      <th>roubo_celular</th>
      <th>roubo_conducao_saque</th>
      <th>roubo_apos_saque</th>
      <th>roubo_bicicleta</th>
      <th>outros_roubos</th>
      <th>total_roubos</th>
      <th>furto_veiculos</th>
      <th>furto_transeunte</th>
      <th>furto_coletivo</th>
      <th>furto_celular</th>
      <th>furto_bicicleta</th>
      <th>outros_furtos</th>
      <th>total_furtos</th>
      <th>sequestro</th>
      <th>extorsao</th>
      <th>sequestro_relampago</th>
      <th>estelionato</th>
      <th>apreensao_drogas</th>
      <th>posse_drogas</th>
      <th>trafico_drogas</th>
      <th>apreensao_drogas_sem_autor</th>
      <th>recuperacao_veiculos</th>
      <th>apf</th>
      <th>aaapai</th>
      <th>cmp</th>
      <th>cmba</th>
      <th>ameaca</th>
      <th>pessoas_desaparecidas</th>
      <th>encontro_cadaver</th>
      <th>encontro_ossada</th>
      <th>pol_militares_mortos_serv</th>
      <th>pol_civis_mortos_serv</th>
      <th>indicador_letalidade</th>
      <th>indicador_roubo_rua</th>
      <th>indicador_roubo_veic</th>
      <th>registro_ocorrencias</th>
      <th>fase</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>316</th>
      <td>2017</td>
      <td>5</td>
      <td>425</td>
      <td>1.0</td>
      <td>20</td>
      <td>97.0</td>
      <td>504</td>
      <td>5190</td>
      <td>419.0</td>
      <td>188.0</td>
      <td>2755.0</td>
      <td>669</td>
      <td>124</td>
      <td>4596</td>
      <td>1240</td>
      <td>9669</td>
      <td>1616</td>
      <td>1</td>
      <td>12.0</td>
      <td>2548.0</td>
      <td>7.0</td>
      <td>53.0</td>
      <td>13.0</td>
      <td>2655</td>
      <td>23203</td>
      <td>1302</td>
      <td>1866</td>
      <td>813.0</td>
      <td>1410.0</td>
      <td>243.0</td>
      <td>8359</td>
      <td>13993</td>
      <td>1.0</td>
      <td>156.0</td>
      <td>17.0</td>
      <td>3059.0</td>
      <td>1862</td>
      <td>610.0</td>
      <td>1114.0</td>
      <td>182.0</td>
      <td>3522</td>
      <td>2982.0</td>
      <td>639.0</td>
      <td>1443.0</td>
      <td>128.0</td>
      <td>4650.0</td>
      <td>379.0</td>
      <td>18</td>
      <td>4.0</td>
      <td>1.0</td>
      <td>0.0</td>
      <td>543</td>
      <td>13833</td>
      <td>4596</td>
      <td>71895.0</td>
      <td>3</td>
    </tr>
  </tbody>
</table>
</div>



Acima, vemos que o mês de Fevereiro de 1991 foi o mês com mais casos de roubo registrados no nosso dataset, enquanto o mês com menor número de casos foi Maio de 2017.

Isso sugere um mau sinal, indicando que, talvez, o crime venha aumentando. Apenas uma análise mais aprofundada pode nos dizer.

## Visualização de Dados

Histogramas são poderosas ferramentas de visualização, que nos auxiliam a entender a distribuição dos valores em uma variável.

Abaixo, faremos essa análise em relação à variável `hom_doloso`.



```python
df['hom_doloso'].hist();
plt.xlabel('Quantidade de ocorrências');
```


    
![png](\img\posts\analise-rj\output_36_0.png)
    


Podemos ver que em quase 70 meses tivemos em torno de 500 a 550 registros de homicídos dolosos.


```python
df.registro_ocorrencias.plot();
plt.ylabel('Registros de Ocorrência');
```


    
![png](\img\posts\analise-rj\output_38_0.png)
    


Podemos ver que ao longo do tempo a quantidade de registros de ocorrência vem aumentando.

## Conclusão

Com essa análise podemos concluir que:

* Há uma média de 4.123 veículos perdidos por mês.
* Cerca de 1.772 veículos são recuperados por mês.
* 42.98% dos veículos roubados ou furtados são recuperados.
* A maior média de registros é do tipo furto, com média de 10.433 registros.
* A menor média de registros é de policias civis mortos em serviço, com 0.34 registros.
* A quantidade de Registros de Ocorrência vem aumentando com o tempo.
