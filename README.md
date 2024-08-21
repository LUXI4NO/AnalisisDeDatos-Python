# Informe: Análisis de Juegos y Valoraciones de Steam

### Autores
**Luciano Ezequiel Alvarez**

---

## Tabla de Contenidos
1. [Introducción](#introducción)
2. [Objetivo del Proyecto](#objetivo-del-proyecto)
3. [Metodología](#metodología)
4. [Configuración de Limpieza y Exploración de Datos](#configuración-de-limpieza-y-exploración-de-datos)
5. [Informe del Análisis](#informe-del-análisis)
   - [Cálculo del Promedio de Horas por Usuario](#cálculo-del-promedio-de-horas-por-usuario)
   - [Identificación de los 10 Juegos Más Jugados](#identificación-de-los-10-juegos-más-jugados)
   - [Identificación de los Géneros Más Vendidos](#identificación-de-los-géneros-más-vendidos)
   - [Identificación de los 10 Juegos con Más Ventas](#identificación-de-los-10-juegos-con-más-ventas)
   - [Desarrollador con Más Ventas](#desarrollador-con-más-ventas)
   - [Desarrollador con Más Ganancias](#desarrollador-con-más-ganancias)
   - [Análisis de Valoraciones Positivas y Negativas](#análisis-de-valoraciones-positivas-y-negativas)
   - [Juegos con Más Valoraciones Positivas](#juegos-con-más-valoraciones-positivas)
   - [Juegos con Más Valoraciones Negativas](#juegos-con-más-valoraciones-negativas)
   - [Juegos con la Mayor Valoración Positiva y Negativa](#juegos-con-la-mayor-valoración-positiva-y-negativa)
   - [Correlación entre Horas Jugadas y Valoraciones Positivas](#correlación-entre-horas-jugadas-y-valoraciones-positivas)
   - [Mes con Más Juegos Publicados](#mes-con-más-juegos-publicados)
   - [Desarrolladores con Más Tipos de Juegos Desarrollados](#desarrolladores-con-más-tipos-de-juegos-desarrollados)
   - [Horas Jugadas Mensualmente por Tipo de Juego](#horas-jugadas-mensualmente-por-tipo-de-juego)
6. [Conclusiones](#conclusiones)

---

## Introducción
En este informe se realiza un análisis exhaustivo de un conjunto de datos relacionados con juegos de Steam. Se exploran diversas métricas, incluyendo las valoraciones positivas y negativas, la relación entre las horas jugadas y las valoraciones, así como la distribución de lanzamientos de juegos a lo largo del año. El objetivo es proporcionar una visión detallada sobre las tendencias y patrones en el mercado de videojuegos.

## Objetivo del Proyecto
El objetivo principal de este proyecto es analizar un conjunto de datos de juegos para identificar patrones significativos y obtener información valiosa sobre la relación entre las valoraciones de los juegos y las horas jugadas. Además, se busca comprender las tendencias de lanzamientos de juegos y evaluar el impacto de estas métricas en el mercado.

## Metodología
Para llevar a cabo este análisis, se implementaron las siguientes metodologías:

- **Recopilación de Datos**: Se obtuvieron los datos de una fuente confiable y se prepararon para el análisis.
- **Limpieza de Datos**: Se realizaron procesos de limpieza para asegurar la calidad y precisión de los datos.
- **Exploración de Datos**: Se realizaron análisis descriptivos y visualizaciones para explorar las métricas clave.
- **Análisis Estadístico**: Se aplicaron métodos estadísticos para determinar correlaciones y tendencias significativas.
- **Visualización de Datos**: Se crearon gráficos y visualizaciones para representar los hallazgos de manera clara y concisa.

## Configuración de Limpieza y Exploración de Datos

### 1. Carga y Preprocesamiento de Datos
```python
import pandas as pd

# Cargar el conjunto de datos
df = pd.read_csv('juegos.csv')

# Preprocesamiento de datos
df['release_date'] = pd.to_datetime(df['release_date'], format='%Y-%m-%d')
df['month'] = df['release_date'].dt.month
df['avg_hours_per_user'].fillna(0, inplace=True)
df['positive_ratings'] = df['positive_ratings'].astype(int)
df['negative_ratings'] = df['negative_ratings'].astype(int)
df['positive_ratings'] = df['positive_ratings'].astype(int)
df['negative_ratings'] = df['negative_ratings'].astype(int)
```

### 2. Limpieza y Transformación de Datos
```python
# Eliminar duplicados
df.drop_duplicates(inplace=True)

# Reemplazar valores nulos
df.fillna({
    'positive_ratings': 0,
    'negative_ratings': 0,
    'avg_hours_per_user': 0
}, inplace=True)
```

# Análisis

### 1. Promedio de Horas Jugadas por Usuario:
```python
# Calculo el promedio de horas por usuario
promedio_horas = df['avg_hours_per_user'].mean()
print(f'Promedio de horas por usuario: {promedio_horas:.2f} horas')
```

### 2. Identificación de los 10 Juegos Más Jugados:
```python
JuegosMasJugados = df.sort_values(by='avg_hours_per_user', ascending=False).head(10)
JuegosMasJugados
```

### 3. Identificación de los Géneros Más Vendidos:
```python
generos = ['action', 'adventure', 'rpg', 'simulation', 'strategy', 'racing', 'free_to_play', 'early_access']
ventas_por_genero = {genero: 0 for genero in generos}

for genero in generos:
    ventas_por_genero[genero] = df[df[genero]]['sells'].sum()

ventas_ordenadas = sorted(ventas_por_genero.items(), key=lambda x: x[1], reverse=True)

print("Géneros más vendidos:")
for genero, ventas in ventas_ordenadas:
    print(f"{genero.capitalize()}: {ventas:,.0f} ventas")

generos_capitalizados = [item[0].capitalize() for item in ventas_ordenadas]
ventas = [item[1] for item in ventas_ordenadas]
```

### 4. Identificación de los 10 Juegos con Más Ventas:
```python
top_10_juegos_vendidos = df.nlargest(10, 'sells')
print(top_10_juegos_vendidos[['name', 'sells']])
```

### 5. Desarrollador con Más Ventas
```python
   # Primero, sumo las ventas por desarrollador
ventas_por_desarrollador = df.groupby('developer')['sells'].sum()

# Obtengo el desarrollador con más ventas
desarrollador_con_mas_ventas = ventas_por_desarrollador.idxmax()

# Obtengo el número total de ventas de ese desarrollador
total_ventas = ventas_por_desarrollador.max()

print(f"El desarrollador con más ventas es {desarrollador_con_mas_ventas} con un total de {total_ventas:,.0f} ventas.")
```

### 6. Desarrollador con Más Ganancias:
```python
   # Calculo los ingresos por desarrollador
df['ingresos'] = df['sells'] * df['price']
ingresos_por_desarrollador = df.groupby('developer')['ingresos'].sum()

# Obtengo el desarrollador que más dinero recaudó
desarrollador_con_mas_ingresos = ingresos_por_desarrollador.idxmax()
total_recaudado = ingresos_por_desarrollador.max()

print(f"El desarrollador que más dinero recaudó es {desarrollador_con_mas_ingresos} con un total de {total_recaudado:,.0f}.")
```

### 7. Análisis de Valoraciones Positivas y Negativas:
```python
# Sumar las valoraciones por género
generos = ['action', 'adventure', 'rpg', 'simulation', 'strategy', 'racing', 'free_to_play', 'early_access']

valoraciones_positivas = {genero: 0 for genero in generos}
valoraciones_negativas = {genero: 0 for genero in generos}

for genero in generos:
    valoraciones_positivas[genero] = df[df[genero]]['positive_ratings'].sum()
    valoraciones_negativas[genero] = df[df[genero]]['negative_ratings'].sum()

# Ordenar los resultados
valoraciones_ordenadas_positivas = sorted(valoraciones_positivas.items(), key=lambda x: x[1], reverse=True)
valoraciones_ordenadas_negativas = sorted(valoraciones_negativas.items(), key=lambda x: x[1], reverse=True)

# Mostrar los resultados
print("Géneros con más valoraciones positivas:")
for genero, valoracion_positiva in valoraciones_ordenadas_positivas:
    print(f"{genero.capitalize()}: {valoracion_positiva:,} valoraciones positivas")

print("\nGéneros con más valoraciones negativas:")
for genero, valoracion_negativa in valoraciones_ordenadas_negativas:
    print(f"{genero.capitalize()}: {valoracion_negativa:,} valoraciones negativas")

# Valores de las valoraciones por género
valores_positivos = list(valoraciones_positivas.values())
valores_negativos = list(valoraciones_negativas.values())
generos = list(valoraciones_positivas.keys())
```

### 8. Juegos con Más Valoraciones Positivas:
```python
Se identificaron los 10 juegos con la mayor cantidad de valoraciones positivas. La visualización de estos datos se realizó mediante un gráfico de barras horizontales.
# Selecciono los 10 juegos con más valoraciones positivas
top_10_juegos_positivos = df.nlargest(10, 'positive_ratings')

# Muestro los resultados
print("Top 10 juegos con más valoraciones positivas:")
print(top_10_juegos_positivos[['name', 'positive_ratings']])
```

### 9. Juegos con Más Valoraciones Negativas:
```python
Para los juegos con más valoraciones negativas, se utilizó un gráfico de barras horizontales similar, lo que facilita la comparación entre las valoraciones positivas y negativas.

# Selecciono los 10 juegos con más valoraciones negativas
top_10_juegos_negativos = df.nlargest(10, 'negative_ratings')

# Muestro los resultados
print("Top 10 juegos con más valoraciones negativas:")
print(top_10_juegos_negativos[['name', 'negative_ratings']])
```

### 10.  Juegos con Mayor Valoración Positiva y Negativa:
```python
# Juego con más valoraciones positivas
juego_mas_positivo = df.loc[df['positive_ratings'].idxmax()]

# Juego con más valoraciones negativas
juego_mas_negativo = df.loc[df['negative_ratings'].idxmax()]

# Mostrar los resultados
print(f"Juego con más valoraciones positivas: {juego_mas_positivo['name']} ({juego_mas_positivo['positive_ratings']} valoraciones positivas)")
print(f"Juego con más valoraciones negativas: {juego_mas_negativo['name']} ({juego_mas_negativo['negative_ratings']} valoraciones negativas)")
```

### 11. Correlación entre Horas Jugadas y Valoraciones Positivas:
```python
# Calcular la correlación entre las horas promedio jugadas y las valoraciones positivas
correlacion = df['avg_hours_per_user'].corr(df['positive_ratings'])
print(f"Correlación entre las horas promedio jugadas y las valoraciones positivas: {correlacion:.2f}")

# Identificar los 3 juegos con más valoraciones positivas
top_3_juegos = df.nlargest(3, 'positive_ratings')
```

### 12. Mes con Más Juegos Publicados:
```python
# Me aseguro de que la columna de fechas esté en formato de fecha
df['release_date'] = pd.to_datetime(df['release_date'], format='%Y-%m-%d')

# Extraigo el mes del lanzamiento
df['month'] = df['release_date'].dt.month

# Cuento la cantidad de lanzamientos por mes
lanzamientos_por_mes = df['month'].value_counts().sort_index()
```
### 13.Desarrolladores con Más Tipos de Juegos Desarrollados:
```python
    # Creo una lista con los nombres de las columnas que representan los tipos de juegos
tipo_juegos_cols = ['action', 'adventure', 'rpg', 'simulation', 'strategy', 'racing', 'free_to_play', 'early_access']

# Sumo la cantidad de tipos de juegos diferentes que cada desarrollador ha creado
desarrollador_tipo_juegos = df.groupby('developer')[tipo_juegos_cols].sum()

# Cuento cuántos tipos de juegos diferentes ha desarrollado cada desarrollador
desarrollador_tipo_juegos['total_tipo_juegos'] = desarrollador_tipo_juegos[tipo_juegos_cols].gt(0).sum(axis=1)

# Ordeno por la cantidad de tipos de juegos desarrollados
desarrolladores_mas_tipos = desarrollador_tipo_juegos.sort_values(by='total_tipo_juegos', ascending=False)

# Filtrado de los 10 desarrolladores con más tipos de juegos
top_10_desarrolladores_mas_tipos = desarrolladores_mas_tipos.nlargest(10, 'total_tipo_juegos')

# Muestro los 10 desarrolladores que han creado la mayor cantidad de tipos de juegos
print("\nTop 10 desarrolladores que han creado la mayor cantidad de tipos de juegos:")
print(top_10_desarrolladores_mas_tipos[['total_tipo_juegos']])
```
###  14. Las horas jugadas mensualmente por tipo de juego:
```python
# Creo una nueva columna para calcular las horas totales jugadas por juego
df['horas_totales'] = df['avg_hours_per_user'] * df['sells']

# Defino una lista con los tipos de juegos
tipos_de_juegos  = ['action', 'adventure', 'rpg', 'simulation', 'strategy', 'racing', 'free_to_play', 'early_access']

# Multiplico el total de horas jugadas por cada tipo de juego
for tipo_de_juego  in tipos_de_juegos :
    df[tipo_de_juego  + '_horas'] = df[tipo_de_juego ] * df['horas_totales']

# Agrupo por mes y sumo las horas jugadas por cada tipo de juego
horas_por_mes  = df.groupby('month')[[tipo_de_juego  + '_horas' for tipo_de_juego  in tipos_de_juegos ]].mean()

# Convierto el índice numérico de meses a nombres de meses
horas_por_mes.index = horas_por_mes.index.map({
    1: 'Enero', 2: 'Febrero', 3: 'Marzo', 4: 'Abril',
    5: 'Mayo', 6: 'Junio', 7: 'Julio', 8: 'Agosto',
    9: 'Septiembre', 10: 'Octubre', 11: 'Noviembre', 12: 'Diciembre'
})

# Creo una copia del DataFrame para aplicar el formato sin modificar los datos originales
horas_por_mes_formateado = horas_por_mes.copy()

# Aplico el formato .0f a todos los valores en el DataFrame usando map()
horas_por_mes_formateado = horas_por_mes_formateado.apply(lambda x: x.map(lambda y: f"{y:,.0f}"))

# Muestro el DataFrame resultante con las horas jugadas por tipo de juego, agrupadas por mes
print("Horas jugadas por tipo de juego, agrupadas por mes:")
print(horas_por_mes_formateado)
```

## Conclusiones

El análisis realizado ofrece una **visión integral** sobre las valoraciones y lanzamientos de juegos. Los gráficos y estadísticas obtenidos revelan **patrones significativos** en el mercado de juegos, destacando:

- **Juegos con más valoraciones positivas y negativas**.
- **Relación entre horas jugadas y valoraciones**.
- **Tendencias de lanzamientos a lo largo del año**.

Estos hallazgos pueden ser utilizados para tomar **decisiones informadas** sobre futuras inversiones y desarrollos en la industria del gaming.

