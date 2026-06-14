# Our-World-in-Data---CO2-and-Greenhouse-Gas-Dataset
Trabalho Faculdade - Visualização da Informação

import pandas as pd

import plotly.express as px

import plotly.graph_objects as go



# ==========================================

# 0. CARREGAMENTO E PREPARAÇÃO DOS DADOS (DADOS REAIS)

# ==========================================

# Utilizando um dataset real do Banco Mundial hospedado publicamente

url = "https://raw.githubusercontent.com/owid/co2-data/master/owid-co2-data.csv"

df_raw = pd.read_csv(url)



# Filtrando anos recentes relevantes (ex: 2000 a 2021) e removendo agregações continentais

paises_reais = df_raw[(df_raw['year'] >= 2000) & (df_raw['year'] <= 2021) & (~df_raw['iso_code'].isna()) & (df_raw['iso_code'] != 'OWID_WRL')]



# Selecionando colunas que vamos usar para as 3 unidades

df = paises_reais[['country', 'iso_code', 'year', 'gdp', 'co2_per_capita', 'population']].dropna()



print("Dados carregados com sucesso! Total de registros:", len(df))



# ==========================================

# GRAFICO 1 (Unidade: Estatística Descritiva)

# Técnica: Gráfico de Barras

# Natureza: Comparação categórica de valores absolutos (PIB em 2018)

# ==========================================

df_2018 = df[df['year'] == 2018].sort_values(by='gdp', ascending=False).head(10)



fig1 = px.bar(

  df_2018, 

  x='gdp', 

  y='country', 

  orientation='h',

  title='Top 10 Maiores PIBs Globais (Ano: 2018)',

  labels={'gdp': 'PIB (Em Dólares - Paridade de Poder de Compra)', 'country': 'País'},

  color='gdp',

  color_continuous_scale='Viridis'

)

fig1.update_layout(yaxis={'categoryorder':'total ascending'}, template='plotly_white')

fig1.show()



# ==========================================

# GRAFICO 2 (Unidade: Informação Temporal)

# Técnica: Gráfico de Linhas

# Natureza: Tendência e evolução de uma variável contínua ao longo do tempo

# ==========================================

# Filtrando algumas potências econômicas do gráfico anterior para ver a evolução

paises_selecionados = ['United States', 'China', 'Japan', 'Germany', 'United Kingdom', 'Brazil']

df_temporal = df[df['country'].isin(paises_selecionados)]



fig2 = px.line(

  df_temporal, 

  x='year', 

  y='gdp', 

  color='country',

  title='Evolução Temporal do PIB (2000 - 2021)',

  labels={'year': 'Ano', 'gdp': 'PIB', 'country': 'País'},

  markers=True

)

fig2.update_layout(template='plotly_white')

fig2.show()



# ==========================================

# GRAFICO 3 (Unidade: Informação Geográfica)

# Técnica: Mapa Choropleth

# Natureza: Distribuição espacial/geográfica de uma métrica (CO2 por Habitante)

# ==========================================

# Usando a média de emissões no período para o mapa global

df_mapa = df.groupby(['country', 'iso_code'])['co2_per_capita'].mean().reset_index()



fig3 = px.choropleth(

  df_mapa, 

  locations="iso_code",

  color="co2_per_capita",

  hover_name="country",

  color_continuous_scale=px.colors.sequential.Reds,

  title="Pegada de Carbono Global: Média de Emissões de CO2 per Capita (2000-2021)",

  labels={'co2_per_capita': 'CO2 per Capita (Toneladas)'}

)

fig3.update_layout(geo=dict(showframe=False, showcoastlines=True, projection_type='equirectangular'))

fig3.show()
