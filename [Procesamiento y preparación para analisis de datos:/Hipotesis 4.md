## *Hipótesis 4: Los artistas con un mayor número de canciones en Spotify tienen más streams*

*Coeficiente correlación total canciones por artista y streams:*

![image](https://github.com/user-attachments/assets/830d8b83-7f64-4485-8eb5-980da7154af3)

Consulta en sql:
```sql
WITH song_count AS (
  SELECT
    artists_name,
    COUNT(track_id) AS total_songs
  FROM
    `proyecto-hipotesis-427017.hipotesis.tabla_matriz`
  GROUP BY
    artists_name
),


-- Crear una tabla temporal para sumar los streams por artista
stream_sum AS (
  SELECT
    artists_name,
    SUM(streams) AS total_streams
  FROM
    `proyecto-hipotesis-427017.hipotesis.tabla_matriz`
  GROUP BY
    artists_name
),


-- Unir los resultados de las dos tablas temporales
artist_data AS (
  SELECT
    song_count.artists_name,
    song_count.total_songs,
    stream_sum.total_streams
  FROM
    song_count
  JOIN
    stream_sum
  ON
    song_count.artists_name = stream_sum.artists_name
)


-- Calcular la correlación entre el número de canciones y los streams totales
SELECT
  CORR(total_songs, total_streams) AS correlation_value
FROM
  artist_data
```

El coeficiente de correlación de 0.7789 confirma la fuerte relación positiva observada en la tabla y el gráfico. Este valor indica una correlación fuerte, lo que significa que el número de canciones de un artista es un buen predictor de su número de reproducciones en Spotify.

*Lo graficamos en power bi con python*

```phyton
import matplotlib.pyplot as plt
import numpy as np

# Personalización de colores y estilo
fig, ax = plt.subplots(figsize=(10, 6), facecolor='#000000')
ax.set_facecolor('#896FF780')

# Cálculo de la línea de tendencia
z = np.polyfit(dataset['in_spotify_playlists'], dataset['streams'], 1)
p = np.poly1d(z)

# Gráfico de dispersión
plt.scatter(dataset['in_spotify_playlists'], dataset['streams'], alpha=0.5, color='#9071CE')

# Línea de tendencia
plt.plot(dataset['in_spotify_playlists'], p(dataset['in_spotify_playlists']), linestyle='--', color='white')

# Configuración de textos y etiquetas
plt.title('Relación entre Popularidad en Playlists Spotify y Streams', fontsize=18, color='white', fontname='Calibri')
plt.xlabel('in_spotify_playlists', color='white', fontname='Calibri')
plt.ylabel('Streams', color='white', fontname='Calibri')

# Configuración de ejes y cuadrícula
plt.xlim([dataset['in_spotify_playlists'].min(), dataset['in_spotify_playlists'].max()])
plt.ylim([dataset['streams'].min(), dataset['streams'].max()])
plt.xticks(color='white', fontname='Calibri')
plt.yticks(color='white', fontname='Calibri')
plt.grid(True, alpha=0.3)

# Mostrar el gráfico
plt.show()
```
![image](https://github.com/user-attachments/assets/9f4319c2-00d4-400b-8a29-89a4c845d8f8)

**Análisis del gráfico:**

* **Tendencia positiva clara**: El gráfico de dispersión muestra una fuerte tendencia ascendente, lo que indica que a medida que aumenta el número de canciones de un artista en Spotify, también aumenta su número de reproducciones.
* **Pocos valores atípicos**: Aunque hay cierta dispersión, la mayoría de los puntos se agrupan alrededor de la línea de tendencia, lo que sugiere que la relación es bastante consistente en todos los artistas.

**Conclusión:**

Basándonos en el análisis de la tabla, el gráfico y el coeficiente de correlación, podemos afirmar que la cuarta hipótesis es válida: existe una fuerte relación positiva entre el número de canciones de un artista en Spotify y la cantidad de reproducciones que recibe. Esto sugiere que los artistas con más canciones disponibles tienden a tener más éxito en términos de reproducciones en la plataforma.