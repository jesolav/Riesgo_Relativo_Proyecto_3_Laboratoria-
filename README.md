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



### 💻 [Procesamiento y preparación para analisis de datos:] 

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

### 🔎 [Hacer un análisis exploratorio:] 

3.1  [Agrupar datos según variables categóricas](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/92217673926d10ca511e1b80405d5804d13baa2d/Analisis%20exploratorio/3.1%20Agrupar%20datos%20segun%20variables%20categoricas.md)

3.2  [Medidas de Tendencia Central](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/92217673926d10ca511e1b80405d5804d13baa2d/Analisis%20exploratorio/3.2%20Medidas%20de%20Tendencia%20Central.md)


3.3  [Distribución de Variables](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/2cec995c99fabff285b2bd1de25258ba87168d95/Analisis%20exploratorio/5.%20Medidas%20Tendencia%20Central%2C%20Histograma.md)

3.4  [Distribución de Variables](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/4d5d3036f57f384bed206c5938484a3c358101cd/Analisis%20exploratorio/3.4%20Correlacion%20de%20variables.md)

3.5  [Análisis de Riesgo Relativo](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/746e3b28390b19fee43aef27b4536994d1bde2f2/Analisis%20exploratorio/3.5%20Analisis%20de%20riesgo%20relativo.md)

3.6  [Conclusiones y Recomendaciones](https://github.com/jesolav/Riesgo_Relativo_Proyecto_3_Laboratoria-/blob/4003a58550bbab25fd03fa0778c8396bcf15b0fe/Analisis%20exploratorio/Conclusiones%20y%20Recomendaciones.md)


### 💡 [Enlaces de interés:] 

4.1  [Presentación](https://drive.google.com/file/d/1qTROgofJwOVIb8ihu9XAzY6DbjZTQnKD/view?usp=drive_link)

4.2  [Video Presentación](https://www.loom.com/share/8d09cec8d17e46179fdeaa6a2e9d2c5e?sid=dbeda798-6c5d-4c22-9cd8-5792b0ddfb3b)

4.3  [Dashboard](https://lookerstudio.google.com/reporting/783574cd-6c27-4337-b754-5acadf92ab04)

