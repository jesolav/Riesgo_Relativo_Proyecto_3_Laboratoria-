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



## Temas

* Caso
* Herramientas y Recursos
* Lenguajes
* Conjunto de datos
* Procesamiento y preparación de datos
* Importación y Creación de Tablas en BigQuery
* Identificación y Manejo de Valores Nulos
* Tratamiento de Valores Nulos
* Identificación y Manejo de Duplicados
* Outliers
* Tratamiento de Outliers
* Correlaciones
* Variables Creadas
* Hipótesis
* Análisis de Riesgo de Crédito
* Creación de Variables Dummies
* Cálculo del Score de Riesgo
* Matriz de Confusión y Evaluación del Modelo
* Resultados
* Conclusiones y recomendaciones
* Recursos



### 💻 [Procesamiento y preparación para analisis de datos:] (

2.1. [Conectar/importar datos a herramientas](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/01d3cde026e2852c46698d89d54d617758d478b1/%5BProcesamiento%20y%20preparaci%C3%B3n%20para%20analisis%20de%20datos%3A/2.1%20Conectar%20e%20importar%20datos%20a%20herramientas.md)

2.2. [Identificar y manejar valores nulos y duplicados](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/9997a478435d6f661f1612bc8e5ba62f3add8efa/%5BProcesamiento%20y%20preparaci%C3%B3n%20para%20analisis%20de%20datos%3A/2.2.%20Identificar%20y%20manejar%20valores%20nulos%20y%20duplicados.md)

2.3  [Identificar y manejar datos fuera del alcance del análisis](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/9997a478435d6f661f1612bc8e5ba62f3add8efa/%5BProcesamiento%20y%20preparaci%C3%B3n%20para%20analisis%20de%20datos%3A/2.3%20%20Identificar%20y%20manejar%20datos%20fuera%20del%20alcance%20del%20analisis.md)

2.4  [Identificar y manejar datos inconsistentes en variables categóricas](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/9997a478435d6f661f1612bc8e5ba62f3add8efa/%5BProcesamiento%20y%20preparaci%C3%B3n%20para%20analisis%20de%20datos%3A/2.3%20%20Identificar%20y%20manejar%20datos%20fuera%20del%20alcance%20del%20analisis.md)


2.5  [Identificar y manejar datos discrepantes en variables numéricas](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/2248056ccccf8e898f20a3ba8365ae3b247266e4/%5BProcesamiento%20y%20preparaci%C3%B3n%20para%20analisis%20de%20datos%3A/2.5%20%20Identificar%20y%20manejar%20datos%20discrepantes%20en%20variables%20numericas.md)

2.6  [Comprobar y cambiar tipo de dato](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/db3e2802fd34676861426a22990f1659774c2475/%5BProcesamiento%20y%20preparaci%C3%B3n%20para%20analisis%20de%20datos%3A/2.6%20Comprobar%20y%20cambiar%20tipo%20de%20dato.md)

2.7  [Crear nuevas variables](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/f670a61a3d46c3d51bbea9dd74bd1523e8698af4/%5BProcesamiento%20y%20preparaci%C3%B3n%20para%20analisis%20de%20datos%3A/2.7%20%20Crear%20nuevas%20variables.md)

2.8  [Correlación nuevas variables](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/02ff28b234a548ba4e82129ce0744ac6a28911de/%5BProcesamiento%20y%20preparaci%C3%B3n%20para%20analisis%20de%20datos%3A/2.8%20%20Correlaci%C3%B3n%20nuevas%20variables.md)

2.9  [Unión de tablas](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/05b2b43bff156a5dc57bfc1c87aafcb935a91b90/%5BProcesamiento%20y%20preparaci%C3%B3n%20para%20analisis%20de%20datos%3A/2.9%20%20Uni%C3%B3n%20de%20tablas.md)

2.10  [Crear tablas auxiliares](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/05b2b43bff156a5dc57bfc1c87aafcb935a91b90/%5BProcesamiento%20y%20preparaci%C3%B3n%20para%20analisis%20de%20datos%3A/rtar%20datos%20a%20herramientas.md)

### 💻 [Hacer un análisis exploratorio:] (


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
