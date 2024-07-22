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

## 2.2  Identificar y manejar valores nulos

👤 user_info: 
  
  -last_month_salary:7199
  
  -number_dependents: 943

```sql
SELECT
 COUNTIF(user_id IS NULL) AS user_id_nulos,
 COUNTIF(age IS NULL) AS age_nulos,
 COUNTIF(sex IS NULL) AS sex_nulos,
 COUNTIF(last_month_salary IS NULL) AS last_month_salary_nulos,
 COUNTIF(number_dependents IS NULL) AS number_dependents_nulos
FROM `riesgo-relativo-429716.dataset.user_info`;
```
![image](https://github.com/user-attachments/assets/a47cd16f-c97d-4a6c-af4a-0ed7bad4c46f)

💰 loans_outstanding : sin nulos

```sql
SELECT
 COUNTIF(loan_id IS NULL) AS loan_id_nulos,
 COUNTIF(user_id IS NULL) AS user_id_nulos,
 COUNTIF(loan_type IS NULL) AS loan_type_nulos
FROM `riesgo-relativo-429716.dataset.loans_outstanding`;
```
![image](https://github.com/user-attachments/assets/fca73605-af1d-4459-9599-bd0480c40a24)


📑 loans_detail

```sql
SELECT
 COUNTIF(user_id IS NULL) AS user_id_nulos,
 COUNTIF(more_90_days_overdue IS NULL) AS more_90_days_overdue_nulos,
 COUNTIF(using_lines_not_secured_personal_assets IS NULL) AS using_lines_nulos,
 COUNTIF(number_times_delayed_payment_loan_30_59_days IS NULL) AS number_times_30_59_nulos,
 COUNTIF(debt_ratio IS NULL) AS debt_ratio_nulos,
 COUNTIF(number_times_delayed_payment_loan_60_89_days IS NULL) AS number_times_60_89_nulos
FROM `riesgo-relativo-429716.dataset.loans_detail`;
```
![image](https://github.com/user-attachments/assets/679531a8-7f0b-45fa-a704-00477ccccd69)


🚩 default

```sql
SELECT
 COUNTIF(user_id IS NULL) AS user_id_nulos,
 COUNTIF(default_flag IS NULL) AS default_flag_nulos
FROM `riesgo-relativo-429716.dataset.default`;
```
![image](https://github.com/user-attachments/assets/c537f304-1011-47ec-8366-42757b0ba7b2)



## 2.23 Identificar y manejar valores duplicados

*Sin duplicados

👤 user_info: 
```sql
SELECT
  user_id,
  COUNT(*) AS num_duplicates
FROM `riesgo-relativo-429716.dataset.user_info`
GROUP BY user_id
HAVING num_duplicates > 1;
```

![image](https://github.com/user-attachments/assets/f475cd13-198c-4e31-b547-fb61024c62c2)

💰 loans_outstanding : sin nulos
```sql
SELECT
  loan_id,
  user_id,
  COUNT(*) AS num_duplicates
FROM `riesgo-relativo-429716.dataset.loans_outstanding`
GROUP BY loan_id, user_id
HAVING num_duplicates > 1;
```
![image](https://github.com/user-attachments/assets/f475cd13-198c-4e31-b547-fb61024c62c2)

📑 loans_detail
```sql
SELECT
  user_id,
  COUNT(*) AS num_duplicates
FROM `riesgo-relativo-429716.dataset.loans_detail`
GROUP BY user_id
HAVING num_duplicates > 1;
```
![image](https://github.com/user-attachments/assets/1f0009fe-d18d-4689-a66a-8b150dde4953)

🚩 default
```sql
SELECT
  user_id,
  COUNT(*) AS num_duplicates
FROM `riesgo-relativo-429716.dataset.default`
GROUP BY user_id
HAVING num_duplicates > 1;
```

![image](https://github.com/user-attachments/assets/bf348f51-426c-4a01-aaf8-8c4894ee3dc6)



