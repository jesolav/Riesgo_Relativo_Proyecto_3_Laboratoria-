# Riesgo Relativo 

![image](https://github.com/user-attachments/assets/922cdf74-97d5-42ad-ac81-3a5eb2269efc)

📝 1. Caso

>***El aumento de la demanda de crédito y la preocupación por la tasa de incumplimiento impulsan la necesidad de automatizar el análisis de crédito en Super Caja.
Se propone un sistema automatizado basado en análisis de datos para clasificar a los solicitantes según su riesgo de incumplimiento, utilizando la métrica de pagos atrasados existente.
El objetivo es mejorar la eficiencia, precisión y rapidez del proceso de evaluación de crédito, reduciendo el riesgo de morosidad y fortaleciendo la solidez financiera del banco.***

Trabajo individual

<details>
<summary> ⚙️ Herramientas y Recursos</summary>

- Google BigQuery
- Google Colab
- Google Slides
- Google Looker Studio

</details>


<details>
<summary> ⚙️ Lenguajes</summary>

- SQL / BigQuery
- Python / Google Colab.

</details>

<details>
<summary>📄 Conjunto de datos</summary>

<details>
<summary>👤 <strong>- user_info</strong></summary>

* user_id: Número de identificación del cliente (único para cada cliente)
* age: Edad del cliente
* sex: Sexo del cliente
* last_month_salary: Último salario mensual que el cliente reportó al banco
* number_dependents: Número de dependientes
</details>

<details>
<summary>💰 <strong>- loans_outstanding</strong></summary>

* loan_id: Número de identificación del préstamo (único para cada préstamo)
* user_id: Número de identificación del cliente
* loan_type: Tipo de préstamo (real estate = inmobiliario, others = otro)
</details>

<details>
<summary>📑 <strong>- loans_detail</strong></summary>

* user_id: Número de identificación del cliente
* more_90_days_overdue: Número de veces que el cliente estuvo más de 90 días vencido
* using_lines_not_secured_personal_assets: Cuánto está utilizando el cliente en relación con su límite de crédito, en líneas que no están garantizadas con bienes personales, como inmuebles y automóviles
* number_times_delayed_payment_loan_30_59_days: Número de veces que el cliente se retrasó en el pago de un préstamo (entre 30 y 59 días)
* debt_ratio: Relación entre las deudas y el patrimonio del prestatario. Ratio de deuda = Deudas / Patrimonio
* number_times_delayed_payment_loan_60_89_days: Número de veces que el cliente retrasó el pago de un préstamo (entre 60 y 89 días)
</details>

<details>
<summary>🚩 <strong>- default</strong></summary>

* user_id: Número de identificación del cliente
* default_flag: Clasificación de los clientes morosos (1 para clientes que pagan mal, 0 para clientes que pagan bien)
</details>

</details>


### 2. Hito 1

## 2.1 Conectar/importar datos a herramientas

![image](https://github.com/user-attachments/assets/757945bf-d7cc-4e84-823e-a81595fd7035)

Objetivo: Utilizar la interfaz de Google BigQuery para crear las 4 tablas (1 por cada archivo)

## 2.2  Identificar y manejar valores nulos y duplicados

👤 user_info: 

  Sin duplicados, solo nulos en : 
  
    -last_month_salary:7199
    -number_dependents: 943

```sql
WITH total_rows AS (
  SELECT COUNT(*) AS total_rows
  FROM `riesgo-relativo-429716.dataset.user_info`
),
null_counts AS (
  SELECT
    COUNTIF(user_id IS NULL) AS user_id_nulos,
    COUNTIF(age IS NULL) AS age_nulos,
    COUNTIF(sex IS NULL) AS sex_nulos,
    COUNTIF(last_month_salary IS NULL) AS last_month_salary_nulos,
    COUNTIF(number_dependents IS NULL) AS number_dependents_nulos
  FROM `riesgo-relativo-429716.dataset.user_info`
),
duplicate_counts AS (
  SELECT
    COALESCE(CAST(user_id AS STRING), 'NULL_USER') AS user_id,
    COUNT(*) AS num_duplicates
  FROM `riesgo-relativo-429716.dataset.user_info`
  GROUP BY COALESCE(CAST(user_id AS STRING), 'NULL_USER')
  HAVING num_duplicates > 1
)
SELECT
  total_rows.total_rows,
  null_counts.user_id_nulos,
  null_counts.age_nulos,
  null_counts.sex_nulos,
  null_counts.last_month_salary_nulos,
  null_counts.number_dependents_nulos,
  duplicate_counts.user_id,
  duplicate_counts.num_duplicates
FROM
  total_rows,
  null_counts
LEFT JOIN
  duplicate_counts
ON null_counts.user_id_nulos IS NOT NULL OR duplicate_counts.user_id IS NOT NULL;
```
![image](https://github.com/user-attachments/assets/7023aad3-f36b-48a8-a4a8-99754d5cafde)

Para analizar estos nulos se creo una nueva consulta que busca analizar la relación entre el salario de los clientes y su probabilidad de incumplimiento (default). Para ello, divide a los clientes en dos grupos según tengan o no un salario registrado y luego calcula, para cada grupo, la cantidad y proporción de clientes que han incumplido con sus pagos. Esto permite evaluar si la falta de información sobre el salario es un factor relevante en la predicción del riesgo crediticio. Por el momento, se mantendran 

1. La consulta (CTE):

WITH datos_unidos AS (...):  Esto es una Common Table Expression (CTE), que crea una tabla temporal llamada datos_unidos. Piensa en ella como una subconsulta que puedes referenciar más adelante.

SELECT ui.user_id, ui.last_month_salary, d.default_flag: La CTE selecciona tres columnas:

user_id de la tabla user_info.
last_month_salary de la tabla user_info.
default_flag de la tabla default.
FROM user_info AS ui LEFT JOIN default AS d ON ui.user_id = d.user_id:  Realiza un LEFT JOIN, que combina información de ambas tablas basándose en la coincidencia del user_id. La diferencia clave es que un LEFT JOIN incluye todas las filas de la tabla de la izquierda (user_info), incluso si no tienen coincidencias en la tabla de la derecha (default). En este caso, si un usuario no tiene un registro en default, se mostrará como NULL en la columna default_flag.

2. La consulta principal:

SELECT CASE WHEN last_month_salary IS NULL THEN 'Sin Salario' ELSE 'Con Salario' END AS tiene_salario:  Crea una nueva columna llamada tiene_salario que categoriza a los usuarios como "Con Salario" si tienen un valor en last_month_salary y "Sin Salario" si este valor es nulo.

default_flag: Selecciona la columna default_flag (que indica si el cliente ha incumplido).

COUNT(*) AS cantidad_clientes: Cuenta el número de filas (clientes) en cada grupo.

COUNT(*) / SUM(COUNT(*)) OVER () AS proporcion_clientes: Calcula la proporción de clientes en cada grupo respecto al total.  SUM(COUNT(*)) OVER () calcula el total de clientes en todas las categorías, y luego cada grupo se divide por ese total.

GROUP BY tiene_salario, default_flag:  Agrupa los resultados por las columnas tiene_salario y default_flag.

ORDER BY tiene_salario, default_flag:  Ordena los resultados primero por tiene_salario y luego por default_flag.


```sql
WITH datos_unidos AS (
 SELECT
  ui.user_id,
  ui.last_month_salary,
  d.default_flag
 FROM `riesgo-relativo-429716.dataset.user_info` AS ui
 LEFT JOIN `riesgo-relativo-429716.dataset.default` AS d
  ON ui.user_id = d.user_id
)

SELECT
 CASE 
  WHEN last_month_salary IS NULL THEN 'Sin Salario'
  ELSE 'Con Salario'
 END AS tiene_salario,
 default_flag,
 COUNT(*) AS cantidad_clientes,
 COUNT(*) / SUM(COUNT(*)) OVER () AS proporcion_clientes
FROM datos_unidos
GROUP BY tiene_salario, default_flag
ORDER BY tiene_salario, default_flag;
```
![image](https://github.com/user-attachments/assets/e766dc12-cea8-44be-bbbb-14e19531e9b5)

Interpretación:

* La mayoría de los clientes (78.47%) tienen salario registrado y no han incumplido.
* Un pequeño porcentaje (1.54%) tiene salario registrado y ha incumplido.
* Un porcentaje significativo (19.63%) no tiene salario registrado y no ha incumplido.
* Un porcentaje muy pequeño (0.36%) no tiene salario registrado y ha incumplido.

Conclusión:

Esta consulta nos da una visión general de cómo se relacionan el salario registrado (o la falta de él) con el incumplimiento de los clientes. Es un buen punto de partida para un análisis más profundo del riesgo crediticio.


💰 loans_outstanding : sin nulos

```sql
WITH null_counts_loans_outstanding AS (
  SELECT
    COUNT(*) AS total_rows,
    COUNTIF(loan_id IS NULL) AS loan_id_nulls,
    COUNTIF(user_id IS NULL) AS user_id_nulls,
    COUNTIF(loan_type IS NULL) AS loan_type_nulls
  FROM `riesgo-relativo-429716.dataset.loans_outstanding`
),
duplicate_counts_loans_outstanding AS (
  SELECT
    COALESCE(CAST(loan_id AS STRING), 'NULL_LOAN') AS loan_id,
    COALESCE(CAST(user_id AS STRING), 'NULL_USER') AS user_id,
    COUNT(*) AS num_duplicates
  FROM `riesgo-relativo-429716.dataset.loans_outstanding`
  GROUP BY COALESCE(CAST(loan_id AS STRING), 'NULL_LOAN'), COALESCE(CAST(user_id AS STRING), 'NULL_USER')
  HAVING num_duplicates > 1
)
SELECT
  null_counts_loans_outstanding.total_rows,
  null_counts_loans_outstanding.loan_id_nulls,
  null_counts_loans_outstanding.user_id_nulls,
  null_counts_loans_outstanding.loan_type_nulls,
  duplicate_counts_loans_outstanding.loan_id,
  duplicate_counts_loans_outstanding.user_id,
  duplicate_counts_loans_outstanding.num_duplicates
FROM
  null_counts_loans_outstanding
LEFT JOIN
  duplicate_counts_loans_outstanding
ON null_counts_loans_outstanding.loan_id_nulls IS NOT NULL 
OR duplicate_counts_loans_outstanding.loan_id IS NOT NULL;
```
![image](https://github.com/user-attachments/assets/668dd430-29c4-4799-8c22-3826075b4a52)


📑 loans_detail

```sql
WITH null_counts_loans_detail AS (
  SELECT
    COUNT(*) AS total_rows,
    COUNTIF(user_id IS NULL) AS user_id_nulls,
    COUNTIF(number_times_delayed_payment_loan_30_59_days IS NULL) AS loan_identifier_nulls
  FROM `riesgo-relativo-429716.dataset.loans_detail`
),
duplicate_counts_loans_detail AS (
  SELECT
    COALESCE(CAST(user_id AS STRING), 'NULL_USER') AS user_id,
    number_times_delayed_payment_loan_30_59_days AS loan_identifier,
    COUNT(*) AS num_duplicados
  FROM
    `riesgo-relativo-429716.dataset.loans_detail`
  GROUP BY
    COALESCE(CAST(user_id AS STRING), 'NULL_USER'), loan_identifier
  HAVING
    COUNT(*) > 1
)
SELECT
  null_counts_loans_detail.total_rows,
  null_counts_loans_detail.user_id_nulls,
  null_counts_loans_detail.loan_identifier_nulls,
  duplicate_counts_loans_detail.user_id,
  duplicate_counts_loans_detail.loan_identifier,
  duplicate_counts_loans_detail.num_duplicados
FROM
  null_counts_loans_detail
LEFT JOIN
  duplicate_counts_loans_detail
ON null_counts_loans_detail.user_id_nulls IS NOT NULL OR duplicate_counts_loans_detail.user_id IS NOT NULL;
```
![image](https://github.com/user-attachments/assets/4d1dd15c-dbde-48bd-84c1-371dec81f033)


🚩 default

```sql
WITH null_counts_default AS (
 SELECT
  COUNT(*) AS total_rows,
  COUNTIF(user_id IS NULL) AS user_id_nulls,
  COUNTIF(default_flag IS NULL) AS default_flag_nulls
 FROM `riesgo-relativo-429716.dataset.default`
),
duplicate_counts_default AS (
 SELECT
  user_id,
  COUNT(*) AS num_duplicates
 FROM `riesgo-relativo-429716.dataset.default`
 GROUP BY user_id
 HAVING num_duplicates > 1
)
SELECT
 null_counts_default.total_rows,
 null_counts_default.user_id_nulls,
 null_counts_default.default_flag_nulls,
 duplicate_counts_default.user_id,
 duplicate_counts_default.num_duplicates
FROM
 null_counts_default,
 duplicate_counts_default;
```
![image](https://github.com/user-attachments/assets/c80a0665-3485-4c3d-9431-da3c832416cf)



## 2.4  Identificar y manejar datos fuera del alcance del análisis

Se realiza correlación:

```sql
SELECT
  corr(t1.default_flag, t2.more_90_days_overdue) AS corr_default_flag_more_90_days_overdue,
  corr(t1.default_flag, t2.debt_ratio) AS corr_default_flag_debt_ratio,
  corr(t1.default_flag, t2.number_times_delayed_payment_loan_30_59_days) AS corr_default_flag_number_times_delayed_payment_loan_30_59_days,
  corr(t1.default_flag, t2.number_times_delayed_payment_loan_60_89_days) AS corr_default_flag_number_times_delayed_payment_loan_60_89_days,
  corr(t1.default_flag, t3.age) AS corr_default_flag_age,
  corr(t1.default_flag, t3.last_month_salary) AS corr_default_flag_last_month_salary,
  corr(t1.default_flag, t3.number_dependents) AS corr_default_flag_number_dependents
 FROM
  `riesgo-relativo-429716.dataset.default` AS t1
  INNER JOIN `riesgo-relativo-429716.dataset.loans_detail` AS t2 ON t1.user_id = t2.user_id
  INNER JOIN `riesgo-relativo-429716.dataset.user_info` AS t3 ON t2.user_id = t3.user_id;
```

![image](https://github.com/user-attachments/assets/915568ee-3280-4241-bf34-200294118994)

Python:

![image](https://github.com/user-attachments/assets/6a5f4160-056b-450b-b8a3-6b650c0ec32c)


> La matriz de correlación muestra la relación entre diferentes variables en un conjunto de datos de riesgo crediticio. Cada celda de la matriz representa el coeficiente de correlación entre dos variables, que varía de -1 a 1. Un valor cercano a 1 indica una fuerte correlación positiva (cuando una variable aumenta, la otra también), un valor cercano a -1 indica una fuerte correlación negativa (cuando una variable aumenta, la otra disminuye) y un valor cercano a 0 indica una correlación débil o nula.

# **Observaciones clave:**

## Correlaciones Positivas Fuertes:

- more_90_days_overdue con number_times_delayed_payment_loan_30_59_days (0.98) y number_times_delayed_payment_loan_60_89_days (0.99): Esto sugiere que los clientes con más de 90 días de atraso en un préstamo también tienden a tener un mayor número de pagos atrasados en otros préstamos, tanto en el rango de 30-59 días como en el de 60-89 días.

## Correlaciones Negativas Moderadas:

- age con number_times_delayed_payment_loan_30_59_days (-0.06) y number_times_delayed_payment_loan_60_89_days (-0.06): Esto indica una ligera tendencia a que los clientes más jóvenes tengan un mayor número de pagos atrasados.
- age con number_dependents (-0.22): Sugiere una ligera tendencia a que los clientes más jóvenes tengan más dependientes.

## Correlaciones Débiles:

- default_flag con debt_ratio (-0.01): La relación entre el incumplimiento y la relación deuda-ingreso es muy débil, lo que significa que esta variable no es un predictor fuerte de incumplimiento en este conjunto de datos.
- last_month_salary con todas las demás variables: El salario del último mes muestra correlaciones muy débiles con todas las demás variables, lo que indica que no es un factor determinante en el riesgo crediticio en este análisis.

## Implicaciones:

- Las variables relacionadas con los pagos atrasados son predictores fuertes del incumplimiento.
- La edad del cliente podría ser un factor a considerar en la evaluación del riesgo, aunque su efecto parece ser moderado.
- El ratio de deuda y el salario del último mes no parecen ser buenos predictores del incumplimiento en este conjunto de datos.


Analizaremos mas a profundidad la relación entre la edad y los pagos atrasados para comprender mejor por qué los clientes más jóvenes podrían tener un mayor riesgo.

```sql
WITH GruposEdad AS (
    SELECT
        CASE
            WHEN ld.user_id BETWEEN 1 AND 24 THEN 'Menos de 25'
            WHEN ld.user_id BETWEEN 25 AND 34 THEN '25-34'
            WHEN ld.user_id BETWEEN 35 AND 44 THEN '35-44'
            WHEN ld.user_id BETWEEN 45 AND 54 THEN '45-54'
            ELSE '55 o más'
        END AS grupo_edad,
        number_times_delayed_payment_loan_30_59_days AS atrasos_30_59,
        number_times_delayed_payment_loan_60_89_days AS atrasos_60_89
    FROM
        `riesgo-relativo-429716.dataset.loans_detail` AS ld
)
SELECT
    grupo_edad,
    COUNT(*) AS num_clientes,
    AVG(atrasos_30_59) AS promedio_atrasos_30_59,
    AVG(atrasos_60_89) AS promedio_atrasos_60_89
FROM
    `GruposEdad`
GROUP BY
    grupo_edad
ORDER BY
    grupo_edad;
```

![image](https://github.com/user-attachments/assets/e6d4ec54-38c6-4b30-b864-7c4a566715f6)

Los resultados muestran el número de clientes, el promedio de atrasos de 30-59 días y el promedio de atrasos de 60-89 días para cada grupo de edad:

## Interpretación:

- Muestra pequeña: Los grupos de edad "25-34", "35-44" y "45-54" tienen solo 10 clientes cada uno, lo que es una muestra muy pequeña para sacar conclusiones definitivas.
- Tendencia general: A pesar de la muestra pequeña en algunos grupos, se observa una tendencia general a que los clientes más jóvenes (menos de 25 años) tienen un promedio de atrasos menor en ambos rangos de días (30-59 y 60-89) en comparación con los grupos de mayor edad.
- Grupo "55 o más": Este grupo tiene la mayor cantidad de clientes y un promedio de atrasos ligeramente superior al grupo de "Menos de 25 años".

## Conclusiones:

- Los resultados no respaldan la hipótesis inicial: Los datos no muestran claramente que los clientes más jóvenes tengan un mayor riesgo de atrasos en los pagos. De hecho, el grupo de "Menos de 25 años" tiene el menor promedio de atrasos en ambos rangos.
- Se necesita más investigación: Dado el tamaño pequeño de la muestra en algunos grupos de edad, se requiere un análisis más profundo para confirmar o refutar la relación entre la edad y los atrasos en los pagos. Se podrían considerar otras variables relevantes, como el nivel de ingresos, el tipo de empleo o el historial crediticio.
- Precaución al interpretar: Es importante tener en cuenta que esta es una primera exploración y que se necesitan más análisis para llegar a conclusiones sólidas.

Profundizaremos este analisis con una nueva consulta, agregando otras variables relevantes.


```sql
WITH datos_combinados AS (
  SELECT
    ui.user_id,
    ui.age,
    ui.last_month_salary,
    ui.number_dependents,
    lo.loan_type,
    ld.more_90_days_overdue,
    ld.using_lines_not_secured_personal_assets,
    ld.number_times_delayed_payment_loan_30_59_days AS atrasos_30_59,
    ld.number_times_delayed_payment_loan_60_89_days AS atrasos_60_89,
    ld.debt_ratio,
    d.default_flag
  FROM
    `riesgo-relativo-429716.dataset.user_info` ui
  LEFT JOIN
    `riesgo-relativo-429716.dataset.loans_outstanding` lo ON ui.user_id = lo.user_id
  LEFT JOIN
    `riesgo-relativo-429716.dataset.loans_detail` ld ON ui.user_id = ld.user_id
  LEFT JOIN
    `riesgo-relativo-429716.dataset.default` d ON ui.user_id = d.user_id
),

grupos_edad_y_variables AS (
  SELECT
    CASE
      WHEN age < 25 THEN 'Menos de 25'
      WHEN age >= 25 AND age < 35 THEN '25-34'
      WHEN age >= 35 AND age < 45 THEN '35-44'
      WHEN age >= 45 AND age < 55 THEN '45-54'
      ELSE '55 o más'
    END AS grupo_edad,
    last_month_salary,
    loan_type,
    more_90_days_overdue,
    using_lines_not_secured_personal_assets,
    atrasos_30_59,
    atrasos_60_89,
    debt_ratio,
    default_flag
  FROM
    datos_combinados
)

SELECT
  grupo_edad,
  AVG(IF(default_flag = 1, 1, 0)) AS tasa_incumplimiento,
  AVG(last_month_salary) AS promedio_salario,
  COUNTIF(loan_type = 'REAL ESTATE') / COUNT(*) AS proporcion_inmobiliario,
  AVG(more_90_days_overdue) AS promedio_mas_90_dias_atraso,
  AVG(using_lines_not_secured_personal_assets) AS promedio_lineas_no_garantizadas,
  AVG(atrasos_30_59) AS promedio_atrasos_30_59,
  AVG(atrasos_60_89) AS promedio_atrasos_60_89,
  AVG(debt_ratio) AS promedio_debt_ratio
FROM
  grupos_edad_y_variables
GROUP BY
  grupo_edad
ORDER BY
  grupo_edad;
```

Esta consulta proporcionará una tabla que muestra, para cada grupo de edad, diversas métricas relacionadas con el riesgo crediticio, incluyendo la tasa de incumplimiento, el salario promedio, el tipo de préstamo más común, el historial de atrasos y el nivel de endeudamiento.


![image](https://github.com/user-attachments/assets/4a1ef069-468d-4819-aa3e-cb59ae8963d8)

Al analizar estos resultados, podremos identificar patrones y relaciones entre las variables y la edad, lo que ayudará a comprender mejor los factores que influyen en el riesgo crediticio de los clientes y a desarrollar estrategias más efectivas para gestionarlo.

### Observaciones clave:

# **Tasa de incumplimiento:**

* La tasa de incumplimiento es baja en todos los grupos de edad, siendo el grupo de "55 o más" el que presenta la tasa más baja (0.68%).
* El grupo de "Menos de 25" tiene la tasa más alta (2.25%), pero aún así es relativamente baja.
*Esto sugiere que la edad por sí sola no es un factor determinante en el incumplimiento.

# **Salario promedio:**

* El salario promedio aumenta con la edad, siendo el grupo de "45-54" el que tiene el salario más alto.
*Esto podría indicar que los clientes con mayores ingresos tienen un menor riesgo de incumplimiento, pero se necesita más análisis para confirmarlo.

# **Proporción de préstamos inmobiliarios:**

* Ningún cliente en la muestra tiene préstamos inmobiliarios, por lo que esta variable no es relevante en este análisis.

# **Atrasos y líneas de crédito no garantizadas:**

* El grupo de "35-44" tiene los promedios más altos en cuanto a atrasos de 30-59 y 60-89 días, así como en el uso de líneas de crédito no garantizadas.
*Esto podría indicar que este grupo tiene un mayor riesgo crediticio en comparación con los otros grupos, pero se necesita más investigación para determinar las causas.

# **Ratio de deuda:**

* El ratio de deuda también aumenta con la edad, siendo el grupo de "55 o más" el que tiene el ratio más alto.
*Esto podría ser un indicador de que los clientes mayores tienen más deudas en relación con sus ingresos, lo que podría aumentar su riesgo crediticio.

## **Conclusiones:**

* La edad no es el único factor: Aunque hay algunas diferencias entre los grupos de edad, la edad por sí sola no parece ser un predictor fuerte del incumplimiento.
Otras variables son importantes: El salario, el historial de atrasos, el uso de líneas de crédito no garantizadas y el ratio de deuda parecen ser factores más relevantes en la evaluación del riesgo crediticio.
* Se necesita más análisis: Es necesario realizar un análisis más detallado para comprender las relaciones entre estas variables y cómo interactúan para influir en el riesgo de incumplimiento.

Lo revisaremos mas adelante...

## 2.5  Identificar y manejar datos fuera del alcance del análisis


Dado que en tus tablas tienes variables categóricas como sex y loan_type, es importante verificar si hay valores inconsistentes en ellas. Para ello, puedes usar las siguientes consultas:

Tabla user_info (columna sex):

```sql
SELECT sex, COUNT(*) AS cantidad
FROM `riesgo-relativo-429716.dataset.user_info`
GROUP BY sex;
```

![image](https://github.com/user-attachments/assets/ea733ade-e0b4-405c-9458-da776a812b70)

Los valores están bien en esta columna, ya que solo tenemos dos valores distintos: 'F' y 'M', que corresponden a "Femenino" y "Masculino" respectivamente. No es necesario realizar ninguna modificación aquí.

Tabla loans_outstanding (columna loan_type):

```sql
SELECT loan_type, COUNT(*) AS cantidad
FROM `riesgo-relativo-429716.dataset.loans_outstanding`
GROUP BY loan_type;
```
![image](https://github.com/user-attachments/assets/55096e12-98fe-4f35-b6d9-80e516b8febf)

Aquí sí encontramos inconsistencias debido a diferencias en mayúsculas/minúsculas y plurales. Para solucionarlo, podemos hacer lo siguiente:

1.- Unificar Mayusculas:

```sql
UPDATE `riesgo-relativo-429716.dataset.loans_outstanding`
SET loan_type = LOWER(loan_type)
WHERE TRUE;
```
1.- Corregir Plurales:

```sql
UPDATE `riesgo-relativo-429716.dataset.loans_outstanding`
SET loan_type = 'other'
WHERE loan_type = 'others'; 
```
Esto reemplazará "others" por "other".


Después de aplicar estas modificaciones, ejecutamos nuevamente la consulta de verificación para asegurar que los valores hayan sido corregidos:

```sql
SELECT loan_type, COUNT(*) AS cantidad
FROM `riesgo-relativo-429716.dataset.loans_outstanding`
GROUP BY loan_type;
```

![image](https://github.com/user-attachments/assets/f3faa01b-4bc1-47fe-bfe4-463f12cbc405)

## 2.5 Identificar y manejar datos discrepantes en variables numéricas

Generamos una consulta que tiene como objetivo identificar posibles valores atípicos (outliers) en las variables numéricas "edad" y "último salario mensual" de los clientes. Para ello, calcula los valores mínimos y máximos de cada variable, lo que permite detectar valores extremos que podrían ser errores de registro o casos inusuales que requieran un análisis más detallado.

```sql
SELECT
  MIN(age) AS edad_minima,
  MAX(age) AS edad_maxima,
  MIN(last_month_salary) AS salario_minimo,
  MAX(last_month_salary) AS salario_maximo
FROM `riesgo-relativo-429716.dataset.user_info`;
```
![image](https://github.com/user-attachments/assets/cbfa9cf1-ab45-4e28-855f-c39d05c5e925)

Los resultados que obtuvimos de la consulta en la tabla user_info muestran algunos valores que podrían ser atípicos y requerir atención:

* Edad máxima (109 años): Si bien es posible que haya personas de esa edad, es poco común. Podría ser un error de registro o un caso excepcional.
* Salario máximo (1,560,100.0): Este valor es extremadamente alto y probablemente sea un outlier. Podría representar un error de ingreso de datos o un caso muy particular.

Analizamos en profundidad estas variables: 

- Investigar edades cercanas a 109 años:

```sql
SELECT *
FROM `riesgo-relativo-429716.dataset.user_info`
WHERE age >= 100;
```
- Investigar salarios muy altos:

```sql
SELECT *
FROM `riesgo-relativo-429716.dataset.user_info`
WHERE last_month_salary >= 1000000; -- Ajusta el umbral según consideres apropiad
```

![image](https://github.com/user-attachments/assets/29655a4a-4bc0-4d10-947f-e5142a428860)

Los resultados que obtuvimos son interesantes y confirman la presencia de valores atípicos (outliers) en el dataset:

- Edades cercanas a 109 años: La consulta muestra tres registros con edades de 101, 103 y 109 años. Aunque es posible que existan personas con estas edades, es bastante inusual y podría tratarse de errores de ingreso de datos.

- Salarios muy altos:El resultado muestra un registro con un salario de 1,560,100.0, que es un valor extremadamente alto y probablemente un outlier.

![image](https://github.com/user-attachments/assets/ca7f4a04-5154-4dae-8409-4f26c0cc90ad)


**Distribución de Edades:**

* Mayoría de clientes entre 40 y 60 años: El boxplot de edades muestra que la mayoría de tus clientes se encuentran en este rango. La caja (que representa el rango intercuartílico, donde se encuentra el 50% de los datos) se extiende aproximadamente entre 42 y 60 años.
* Mediana alrededor de 50 años: La línea dentro de la caja indica la mediana, que es el valor que divide los datos en dos mitades iguales. En este caso, la mediana de las edades está alrededor de los 50 años.
* Valores atípicos por encima de 90 años: Los puntos por encima de la línea superior del bigote (que representa el límite superior del rango normal) indican valores atípicos (outliers). En este caso, tienes varios outliers en el rango de 90 a 110 años, lo que confirma lo que vimos en el análisis anterior.

**Distribución de Salarios:**

* Mayoría de salarios bajos: El boxplot de salarios muestra una distribución muy sesgada hacia la izquierda. La mayoría de los salarios se concentran en valores bajos, con la caja muy cerca del cero y un bigote superior muy corto.
* Mediana muy baja: La mediana de los salarios es extremadamente baja, lo que indica que la mitad de tus clientes tienen salarios muy por debajo del promedio.
* Valores atípicos muy altos: Los puntos aislados en la parte superior del gráfico representan salarios atípicos extremadamente altos, que confirman la presencia del outlier que identificamos anteriormente (1,560,100.0).

Para este último análisis, procedemos a eliminar los outliers de edades mayores o iguales a 100 años y el outlier de salario extremadamente alto.

```sql
DELETE FROM `riesgo-relativo-429716.dataset.user_info`
WHERE (age >= 100) OR (last_month_salary >= 1000000);
```

Realizaremos un análisis adicional a traves de una segmentación. Dividiremos a los clientes en grupos según sus características (edad, sexo, tipo de préstamo, etc.) y analizaremos la distribución de salarios dentro de cada grupo.

```sql
WITH datos_unidos AS (
  SELECT
    ui.user_id,
    ui.age,
    ui.sex,
    ui.last_month_salary,
    lo.loan_type
  FROM `riesgo-relativo-429716.dataset.user_info` AS ui
  LEFT JOIN `riesgo-relativo-429716.dataset.loans_outstanding` AS lo
    ON ui.user_id = lo.user_id
),
medianas_por_grupo AS (
  SELECT 
    age,
    sex,
    loan_type,
    PERCENTILE_CONT(last_month_salary, 0.5) OVER (PARTITION BY age, sex, loan_type) AS salario_mediana
  FROM datos_unidos
  GROUP BY age, sex, loan_type, last_month_salary  -- Agrupar por todas las columnas relevantes
)
SELECT
  du.age,
  du.sex,
  du.loan_type,
  COUNT(*) AS num_clientes,
  MIN(du.last_month_salary) AS salario_min,
  MAX(du.last_month_salary) AS salario_max,
  AVG(du.last_month_salary) AS salario_promedio,
  mpg.salario_mediana 
FROM datos_unidos AS du
JOIN medianas_por_grupo AS mpg
  ON du.age = mpg.age AND du.sex = mpg.sex AND du.loan_type = mpg.loan_type
GROUP BY du.age, du.sex, du.loan_type, mpg.salario_mediana
ORDER BY du.age, du.sex, du.loan_type;
```
Esta consulta permite analizar la distribución de salarios dentro de diferentes grupos de clientes, definidos por su edad, sexo y tipo de préstamo. Esto ayudará a identificar patrones y diferencias entre los grupos, lo cual es crucial para construir un modelo de riesgo crediticio preciso y efectivo.

El resultado lo dejaremos solo de manera visual (la tabla unificada la pueden revisar en archivo):

## Boxplots: Para comparar la distribución de salarios entre diferentes grupos de edad, sexo o tipo de préstamo.

![image](https://github.com/user-attachments/assets/c3d6b84a-1a5a-48c1-82e1-15702c4fbd53)

**Análisis por edad:**

* Tendencia general: Los salarios tienden a aumentar ligeramente con la edad, pero la variabilidad también aumenta, lo que significa que hay una mayor dispersión de salarios en los grupos de mayor edad.
* Outliers: Hay algunos valores atípicos (outliers) en todos los grupos de edad, especialmente en los grupos de mayor edad, lo que indica que algunas personas en esos grupos tienen salarios significativamente más altos que la mayoría.

**Análisis por sexo:**

* Diferencias de género: Los hombres (M) tienden a tener salarios ligeramente más altos que las mujeres (F), aunque la diferencia no es muy pronunciada.
* Outliers: Hay más valores atípicos en el grupo de hombres que en el de mujeres.

**Análisis por tipo de préstamo:**

* Variabilidad: La distribución de salarios es similar para los dos tipos de préstamo ("real estate" y "other").
* Outliers: Hay más valores atípicos en el tipo de préstamo "real estate".


## Histogramas: Para ver la distribución general de salarios dentro de cada grupo.

![image](https://github.com/user-attachments/assets/982c2262-b6ef-487f-be11-35c06d0e97b1)

Los histogramas muestran la distribución de frecuencias de los salarios para cada grupo de edad y sexo. Confirman las observaciones anteriores:

* Sesgo a la derecha: La distribución de salarios está sesgada a la derecha, lo que significa que la mayoría de los salarios son bajos, pero hay algunos salarios muy altos que afectan la media.
* Mayor variabilidad en edades mayores: Los histogramas de los grupos de mayor edad muestran una mayor dispersión de salarios.

## **Conclusiones**

*La edad y el sexo parecen tener un efecto leve en los salarios, pero la relación no es muy fuerte.
*Hay una gran variabilidad en los salarios dentro de cada grupo, lo que indica que otros factores no considerados en este análisis (como la educación, la experiencia laboral, etc.) también pueden ser importantes.
*Los valores atípicos deben ser investigados más a fondo para determinar si son errores de datos o representan casos reales.


## 2.6  Comprobar y cambiar tipo de dato


## 2.7 Crear nuevas variables

Para continuar nuestro analisis, creamos nuevas variables:

## **Variables Derivadas de user_info:**

* **age_group (Rango de Edad):** Analizar si el riesgo de incumplimiento varía según la edad del solicitante, ya que diferentes grupos de edad pueden tener diferentes comportamientos financieros.
Valores: Categorías como "18-29", "30-39", "40-49", "50-59" y "60+".

* **salary_per_dependent (Relación Salario/Dependientes):** Evaluar la capacidad de pago del solicitante en relación con sus responsabilidades familiares, ya que un salario más alto por dependiente puede indicar una mayor capacidad para cumplir con los pagos del préstamo.
Valores: Números reales que representan el salario mensual dividido por el número de dependientes (o 1 si no hay dependientes).

* **income_group (Rango de Ingresos):** Examinar si el nivel de ingresos del solicitante afecta su probabilidad de incumplimiento, ya que los ingresos pueden estar relacionados con la estabilidad financiera.
Valores: Categorías como "Bajo", "Medio" y "Alto" (los límites de los rangos pueden ajustarse según tus datos).

## **Variables Derivadas de loans_detail:**

* **total_delays (Total de Retrasos en Pagos):** Obtener una medida global del historial de retrasos del solicitante, ya que un mayor número de retrasos puede indicar un mayor riesgo de incumplimiento.
Valores: Números enteros que representan la suma de los retrasos en pagos de 30-59 días y 60-89 días.

## **Variables Derivadas de loans_outstanding:**

* **num_loans_outstanding (Número de Préstamos Pendientes):** Evaluar si la cantidad de préstamos pendientes afecta el riesgo de incumplimiento, ya que tener múltiples préstamos podría indicar una mayor carga financiera.
Valores: Números enteros que representan la cantidad de préstamos pendientes por usuario.

* **most_common_loan_type (Tipo de Préstamo Más Común):** Identificar el tipo de préstamo que el solicitante utiliza con mayor frecuencia, ya que ciertos tipos de préstamos podrían estar asociados a un mayor riesgo.
Valores: Categorías que representan los diferentes tipos de préstamo (por ejemplo, "Personal", "Automóvil", etc.).


Calcularemos nuevamente la correlación:
```sql
SELECT
  corr(d.default_flag, ui.age) AS corr_default_age,
  corr(d.default_flag, ui.salary_per_dependent) AS corr_default_salary_per_dependent, -- Nueva
  corr(d.default_flag, ld.total_delays) AS corr_default_total_delays, -- Nueva
  corr(d.default_flag, ld.more_90_days_overdue) AS corr_default_more_90_days_overdue,
  corr(d.default_flag, ld.using_lines_not_secured_personal_assets) AS corr_default_using_lines,
  corr(d.default_flag, ld.number_times_delayed_payment_loan_30_59_days) AS corr_default_delays_30_59,
  corr(d.default_flag, ld.debt_ratio) AS corr_default_debt_ratio,
  corr(d.default_flag, ld.number_times_delayed_payment_loan_60_89_days) AS corr_default_delays_60_89
FROM 
  `riesgo-relativo-429716.dataset.default` d
JOIN 
  `riesgo-relativo-429716.dataset.user_info` ui ON d.user_id = ui.user_id
JOIN 
  `riesgo-relativo-429716.dataset.loans_detail` ld ON d.user_id = ld.user_id;
```

![image](https://github.com/user-attachments/assets/082d7374-4d82-4a92-94aa-4edd88ecfd25)

**Correlaciones Positivas:**

* corr_default_total_delays (0.29): Existe una correlación positiva moderada entre el total de retrasos en pagos y la probabilidad de incumplimiento. Esto significa que a medida que aumenta el número de retrasos, también aumenta la probabilidad de que el cliente incumpla.
* corr_default_more_90_days_overdue (0.31): La correlación positiva más fuerte se observa con la variable que indica si el cliente ha tenido retrasos de más de 90 días. Esto sugiere que los retrasos prolongados son un indicador muy importante del riesgo de incumplimiento.
* corr_default_delays_30_59 (0.30): También hay una correlación positiva moderada con el número de retrasos de 30 a 59 días, lo que indica que incluso los retrasos más cortos pueden ser relevantes para predecir el incumplimiento.
* corr_default_delays_60_89 (0.28): Similar a la anterior, esta correlación muestra que los retrasos de 60 a 89 días también están asociados con un mayor riesgo de incumplimiento.

**Correlaciones Negativas:**

* corr_default_age (-0.08): Hay una correlación negativa débil entre la edad y el incumplimiento. Esto sugiere que, en general, los clientes más jóvenes podrían tener una probabilidad ligeramente mayor de incumplir en comparación con los clientes mayores.
* corr_default_salary_per_dependent (-0.04): La correlación negativa más débil se observa con la relación salario/dependientes. Esto indica que, aunque no es muy fuerte, podría haber una tendencia a que los clientes con un salario más bajo por dependiente tengan un riesgo ligeramente mayor de incumplimiento.
* Correlación Casi Nula:

* corr_default_using_lines (0.003): La correlación entre el uso de líneas de crédito no garantizadas y el incumplimiento es prácticamente nula. Esto sugiere que esta variable no es un buen predictor del riesgo de incumplimiento en este conjunto de datos.


**Conclusiones:**

* El historial de retrasos en pagos, especialmente los retrasos de más de 90 días, son los predictores más fuertes del incumplimiento en este análisis.
* La edad y la relación salario/dependientes tienen una influencia menor, pero aún podrían ser relevantes para el modelo de riesgo crediticio.
* El uso de líneas de crédito no garantizadas no parece estar relacionado con el riesgo de incumplimiento en este caso.

## 2.8 Unión de tablas

Esta consulta dará una tabla con una fila por cada usuario en default, incluyendo todas las variables creadas y las originales relevantes para tu análisis de riesgo crediticio.


```sql
CREATE TABLE `riesgo-relativo-429716.dataset.datos_unidos` AS
SELECT
  d.user_id,
  d.default_flag,
  ui.age,
  ui.sex,
  ui.age_group,
  ui.income_group,
  ld.more_90_days_overdue,
  ld.using_lines_not_secured_personal_assets,
  ld.number_times_delayed_payment_loan_30_59_days AS delays_30_59,
  ld.debt_ratio,
  ld.number_times_delayed_payment_loan_60_89_days AS delays_60_89,
  ld.total_delays,
  lo.num_loans_outstanding,
  mclt.loan_type AS most_common_loan_type
FROM `riesgo-relativo-429716.dataset.default` d
LEFT JOIN `riesgo-relativo-429716.dataset.user_info` ui ON d.user_id = ui.user_id
LEFT JOIN `riesgo-relativo-429716.dataset.loans_detail` ld ON d.user_id = ld.user_id
LEFT JOIN (
  SELECT user_id, COUNT(*) AS num_loans_outstanding
  FROM `riesgo-relativo-429716.dataset.loans_outstanding`
  GROUP BY user_id
) lo ON d.user_id = lo.user_id
LEFT JOIN `riesgo-relativo-429716.dataset.most_common_loan_type` mclt ON d.user_id = mclt.user_id;
```
Al realizar esta consulta nos encontramos con 452 filas con nulos en diferentes columnas. Esto representa un 1.3% de nuestro dataset, por lo que decidimos crear una nueva tabla sin nulos, para continuar nuestro analisis.

```sql
SELECT
    *
  FROM
    `riesgo-relativo-429716.dataset.all_data` AS all_data
  WHERE all_data.age IS NULL
   OR all_data.sex IS NULL
   OR all_data.age_group IS NULL
   OR all_data.income_group IS NULL
   OR all_data.more_90_days_overdue IS NULL
   OR all_data.using_lines_not_secured_personal_assets IS NULL
   OR all_data.delays_30_59 IS NULL
   OR all_data.debt_ratio IS NULL
   OR all_data.delays_60_89 IS NULL
   OR all_data.total_delays IS NULL
   OR all_data.loan_id IS NULL
   OR all_data.loan_type IS NULL
   OR all_data.num_loans IS NULL
   OR all_data.most_common_loan_type IS NULL;
```
![image](https://github.com/user-attachments/assets/9cb7dfb6-e76e-4765-891b-2ee0cddb674c)


## 2.9 Crear tablas auxiliares

Se crea una nueva tabla auxiliar, con todos los datos sin considerar nulos.

```sql
CREATE TABLE `riesgo-relativo-429716.dataset.all_data_no_nulls` AS
SELECT *
FROM `riesgo-relativo-429716.dataset.all_data`
WHERE age IS NOT NULL
  AND sex IS NOT NULL
  AND age_group IS NOT NULL
  AND income_group IS NOT NULL
  AND more_90_days_overdue IS NOT NULL
  AND using_lines_not_secured_personal_assets IS NOT NULL
  AND delays_30_59 IS NOT NULL
  AND debt_ratio IS NOT NULL
  AND delays_60_89 IS NOT NULL
  AND total_delays IS NOT NULL
  AND loan_id IS NOT NULL
  AND loan_type IS NOT NULL
  AND num_loans IS NOT NULL
  AND most_common_loan_type IS NOT NULL;
```



```sql
```

```sql
```

```sql
```

```sql
```

```sql
```
