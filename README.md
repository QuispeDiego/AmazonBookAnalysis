# Analisis del Comportamiento de Compra de Libros en Amazon 2009-2019
# Amazon Book-Dashboard

### Data Link: [Kaggle Dataset](https://www.kaggle.com/datasets/sootersaalu/amazon-top-50-bestselling-books-2009-2019)

## Problem Statement

Este dashboard ayudará a Amazon a entender mejor el comportamiento de sus clientes en la compra de libros. Permite conocer si los clientes están satisfechos con sus compras a través de diferentes calificaciones y reviews. De este modo, Amazon puede identificar áreas de mejora y desarrollar estrategias para motivar la lectura en segmentos digitales interesados.

El análisis también revela la relación entre el precio, la cantidad de reviews y las calificaciones, ayudando a entender los factores que influyen en el éxito de los libros más vendidos. Con esta información, Amazon podrá ajustar sus estrategias de venta y promoción para maximizar la satisfacción del cliente y las ventas de libros.

## Hipótesis
- Los libros baratos son mejor calificados.
- No necesariamente los géneros mejor calificados son los más caros.
- Los precios de los libros están asociados a eventos exógenos y no al tipo de género.
- El precio, la cantidad de reviews y el rating se encuentran fuertemente relacionados.

### Pasos Seguidos

1. **Cargar los datos**: Se cargaron los datos CSV en Power BI Desktop.
2. **Transformación de datos**: Se transformaron los datos numéricos a su tipo original (entero o flotante).
3. **Revisión de valores nulos**: No se encontraron valores nulos o vacíos, por lo que se procedió a guardar los cambios.
4. **Selección de colores**: Se seleccionaron colores para mostrar los precios y la correlación de las principales variables: precio, cantidad de reviews y calificaciones.
![damz_2](https://github.com/QuispeDiego/An-lisis-del-Comportamiento-de-Compra-de-Libros-en-Amazon-2009-2019-/assets/89101133/331ec8f5-93fe-4a2b-97e5-4e44f0046e28)
5. **Creación de columnas con DAX**: Se crearon columnas para manejar mejor los valores de los libros y mejorar su visualización.
    - **Rango de Calificación**:
    ```DAX
    Rango de Calificación = 
    SWITCH(
        TRUE(),
        Bestseller[User Rating] < 4.0, "Bajo",
        Bestseller[User Rating] < 4.5, "Medio",
        "Alto"
    )
    ```
    - **Rango de Precio**:
    ```DAX
    Rango de Precio = 
    SWITCH(
        TRUE(),
        Bestseller[Price] <= PERCENTILE.INC(Bestseller[Price], 0.33), "Bajo",
        Bestseller[Price] <= PERCENTILE.INC(Bestseller[Price], 0.66), "Medio",
        "Alto"
    )
    ```
6. **Generación de la tabla 'top 10'**: Para visualizar el impacto de las tres variables en los libros, se decidió analizar los libros más vendidos. Esta elección se basó en su capacidad para generar mayores ventas. Se generó una tabla 'top 10' que incluye únicamente los libros mejor calificados de cada año. Además, se creó una métrica para contar la cantidad de libros que están en el top 10.
    ```python
    # Ordenar filas por Año y User Rating
    df_sorted = df.sort_values(by=['Year', 'User Rating'], ascending=[True, False])
    
    # Obtener los primeros 10 de cada Año
    top_books_per_year = df_sorted.groupby('Year').head(10)
    ```
    ```DAX
    Cantidad libros top 10 = DISTINCTCOUNT(top10[Name])
    ```
7. **Visualización en gráfico de barras**: Se usó un gráfico de barras para mostrar los libros que se mantenían más tiempo en el top 10.Ademas, se filtro solo para que solo se muestren las 10 primeras barras. Aqui se uso Name de la tabla Top10, tanto para el etiquetado como para el recuento de apariciones de Name.
8. **Matriz de correlación**: Se realizó una matriz de correlación para user rating, price y reviews usando Python.
    ```python
    correlation_matrix = df[['User Rating', 'Price', 'Reviews']].corr()
    
    plt.figure(figsize=(10, 8))
    sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm', center=0)
    plt.show()
    ```
9. **Distribución con boxplot**: Se realizaron boxplots para analizar la distribución del precio y rating según género y rango de precio.
10. **Filtros adicionales**: Se añadieron 2 cajas de filtros por nombre del libro y rango de año.
11. **Etiqueta de cantidad de libros en el top 10**: Se añadió una etiqueta para mostrar la cantidad de libros en el top 10 basada en la métrica "Cantidad de libros top 10".

# Report Snapshot (Power BI DESKTOP)
![dashboardAmazon](https://github.com/QuispeDiego/An-lisis-del-Comportamiento-de-Compra-de-Libros-en-Amazon-2009-2019-/assets/89101133/88f74e95-ab07-4583-ad04-cb3ded7284bf)

# Insights

A continuación, se presentan las respuestas a nuestras hipótesis y los nuevos conocimientos obtenidos.

### Hipótesis 1 (Falsa): Los libros baratos son mejor calificados

Según el gráfico de boxplot, el resumen estadístico del user rating de los libros en las tres categorías de precio (alto, medio, bajo) es:

- **Libros de alto precio**:
    - Media: 4.60
    - Máximo: 4.90
    - Mínimo: 4.20
    - IQR 75: 4.70
    - IQR 25: 4.50

- **Libros de precio medio**:
    - Media: 4.70
    - Máximo: 4.90
    - Mínimo: 4.20
    - IQR 75: 4.70
    - IQR 25: 4.50

- **Libros de bajo precio**:
    - Media: 4.70
    - Máximo: 4.90
    - Mínimo: 4.30
    - IQR 75: 4.80
    - IQR 25: 4.60

Esto muestra que los libros en las tres categorías tienen una similitud alta en su user rating, lo que indica que los libros baratos no son necesariamente mejor calificados por ser baratos. Además, se encontró que en los libros de precio medio, el tercer cuartil (Q3) es igual a la media, lo cual podría deberse a una distribución con asimetría negativa, es decir, la mayoría de estos libros tienen un rating superior a la media.

### Hipótesis 2 (Verdadera): No necesariamente los géneros mejor calificados son los más caros

![damz_1](https://github.com/QuispeDiego/An-lisis-del-Comportamiento-de-Compra-de-Libros-en-Amazon-2009-2019-/assets/89101133/1ef3f800-ac98-4a3f-8c48-b1675c50ab25)

Por medio de otro grafico, se comparo tambien el promedio de calificacion de los dos generos, y se nota que ambas medias no difieren tanto una de la otra, por lo que solo quedaria comprobar si alguno de estos dos tienen algun comportamiento en sus precios. 

Según el gráfico de boxplot, el resumen estadístico del precio de los libros de ficción y no ficción es:

- **Libros de ficción**:
    - Media: 11
    - Máximo: 82
    - Mínimo: 0
    - IQR 75: 13
    - IQR 25: 6

- **Libros de no ficción**:
    - Media: 15
    - Máximo: 105
    - Mínimo: 0
    - IQR 75: 17
    - IQR 25: 8

Ahora, segun estas estadisticas mostradas se puede notar que la diferencia entre los valores de ambos tipos es similar. Esto nos lleva a concluir que no necesariamente los generos mejores calificados son los mas caros.

### Hipótesis 3 (Verdadera): Los precios de los libros están asociados a eventos exógenos y no al tipo de género

Las estadísticas muestran que el precio de los libros no está asociado al tipo de género, ya que no hay un comportamiento diferente en el precio de los distintos géneros. Es posible que los precios se deban a factores externos. Esta conclusión no es completamente certera debido a la falta de datos externos.

### Hipótesis 4 (Falsa): El precio, la cantidad de reviews y el rating se encuentran fuertemente relacionados

La correlación entre las tres variables no supera el 0.5, tanto en la parte negativa como en la positiva. El valor máximo negativo es -0.23 (rating con precio) y el positivo es 0.2 (reviews con rating). Esto indica que la correlación entre estos valores es nula, ya que no alcanza el 0.5 para considerarse ni siquiera una correlación moderada.

### Top 10 Histórico

Se encontró que la mayoría del top 10 es de libros de bajo precio. Además, en su mayoría, el género es ficción.

# Conclusiones Finales

La falta de correlación entre las variables dificulta la creación de un perfil del producto, ya que no hay manera de conectar las características principales de los libros. Tanto el precio como el rating no muestran un patrón diferencial significativo.

# Recomendaciones

- Proporcionar más datos sobre las variables ya vistas para encontrar patrones en los datos que generen resultados diferenciados.
- Proporcionar nuevas características de los libros que puedan generar nuevos resultados.
- En caso de no poseer tantos datos, seguir las estrategias basadas en el top 10 de libros más vendidos y mejor calificados, que son principalmente de ficción y de bajo precio.
- Generar nuevas hipótesis y considerar diferentes enfoques, como el análisis de los reviews.
