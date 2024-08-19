## *Hipótesis 3: La presencia de una canción en un mayor número de playlists se relaciona con un mayor número de streams*

*Coeficiente correlación total playlists y streams:*

![image](https://github.com/user-attachments/assets/23325d7f-228d-463e-9856-45d2b08ad7af)

Consulta en sql:
```sql 
SELECT CORR(total_playlists, streams) AS correlation_value
FROM `proyecto-hipotesis-427018.hipotesis.tabla_matriz`
```

El coeficiente de correlación de 0.6047 confirma la relación positiva observada en el gráfico. Este valor indica una correlación moderadamente fuerte, lo que sugiere que la presencia en playlists es un predictor razonablemente bueno del número de streams, pero no el único factor determinante.

*Lo graficamos en power bi con python*

```phyton
import matplotlib.pyplot as plt
import numpy as np

# Personalización de colores y estilo
fig, ax = plt.subplots(figsize=(10, 6), facecolor='#000000')
ax.set_facecolor('#896FF780')

# Cálculo de la línea de tendencia
z = np.polyfit(dataset['total_playlists'], dataset['streams'], 1)
p = np.poly1d(z)

# Gráfico de dispersión
plt.scatter(dataset['total_playlists'], dataset['streams'], alpha=0.5, color='#9071CE')

# Línea de tendencia
plt.plot(dataset['total_playlists'], p(dataset['total_playlists']), linestyle='--', color='white')

# Configuración de textos y etiquetas
plt.title('Relación entre Popularidad en Total Playlists y Streams', fontsize=18, color='white', fontname='Calibri')
plt.xlabel('total_playlists', color='white', fontname='Calibri')
plt.ylabel('Streams', color='white', fontname='Calibri')

# Configuración de ejes y cuadrícula
plt.xlim([dataset['total_playlists'].min(), dataset['total_playlists'].max()])
plt.ylim([dataset['streams'].min(), dataset['streams'].max()])
plt.xticks(color='white', fontname='Calibri')
plt.yticks(color='white', fontname='Calibri')
plt.grid(True, alpha=0.3)

# Mostrar el gráfico
plt.show()
```

![image](https://github.com/user-attachments/assets/dd394b0f-f5db-4867-9073-fba651deb79e)



**Análisis del gráfico:**

* **Tendencia positiva**: El gráfico de dispersión muestra una clara tendencia ascendente, lo que indica que a medida que aumenta el número de playlists en las que aparece una canción, también tiende a aumentar su número de streams.
* **Dispersión**: Aunque hay una tendencia general, también hay una dispersión considerable de los puntos alrededor de la línea de tendencia. Esto significa que si bien hay una relación positiva, no es perfecta y hay excepciones. Algunas canciones pueden tener muchos streams sin estar en muchas playlists, y viceversa.

**Conclusión:**

Basándonos en el gráfico y el coeficiente de correlación, podemos concluir que la tercera hipótesis es válida: existe una relación positiva moderadamente fuerte entre la presencia de una canción en playlists y su número de streams. Esto significa que las canciones que aparecen en más playlists tienden a tener más streams, aunque hay excepciones y otros factores también influyen en la popularidad de una canción.


