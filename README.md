# Riesgo Relativo 

![image](https://github.com/user-attachments/assets/922cdf74-97d5-42ad-ac81-3a5eb2269efc)

📝 1. Caso

>***El aumento de la demanda de crédito y la preocupación por la tasa de incumplimiento impulsan la necesidad de automatizar el análisis de crédito en Super Caja.
Se propone un sistema automatizado basado en análisis de datos para clasificar a los solicitantes según su riesgo de incumplimiento, utilizando la métrica de pagos atrasados existente.
El objetivo es mejorar la eficiencia, precisión y rapidez del proceso de evaluación de crédito, reduciendo el riesgo de morosidad y fortaleciendo la solidez financiera del banco.***

Trabajo individual

## ⚙️ Herramientas y Recursos

- Google BigQuery
- Google Colab
- Google Slides
- Google Looker Studio

## ⚙️ Lenguajes

- SQL / BigQuery
- Python / Google Colab.

## 📄Conjunto de datos

Archivo:

- user_ info
  * user_ id:	Número de identificación del cliente (único para cada cliente)
  * age:	Edad del cliente
  * sex:	Sexo del cliente
  * last_ month_ salary:	Último salario mensual que el cliente reportó al banco
  * number_dependents:	Número de dependientes

- loans_ outstanding
  * loan_ id:	Número de identificación del préstamo (único para cada préstamo)
  * user_ id:	Número de identificación del cliente
  * loan_ type:	Tipo de préstamo (real estate = inmobiliario, others = otro)

- loans_ detail
  * user_ id:	Número de identificación del cliente
  * more_ 90_ days_ overdue:	Número de veces que el cliente estuvo más de 90 días vencido
  * using_ lines_ not_ secured_ personal_ assets:	Cuánto está utilizando el cliente en relación con su límite de crédito, en líneas que no están garantizadas con bienes personales, como inmuebles y automóviles
  * number_ times_ delayed_ payment_ loan_ 30_ 59_ days:	Número de veces que el cliente se retrasó en el pago de un préstamo (entre 30 y 59 días)
  * debt_ ratio:	Relación entre las deudas y el patrimonio del prestatario. Ratio de deuda = Deudas / Patrimonio
  * number_ times_ delayed_ payment_ loan_ 60_ 89_ days:	Número de veces que el cliente retrasó el pago de un préstamo (entre 60 y 89 días)

- default
  * user_ id:	Número de identificación del cliente
  * default_ flag:	Clasificación de los clientes morosos (1 para clientes que pagan mal, 0 para clientes que pagan bien)

### 2. Hito 1

## 2.1 Conectar/importar datos a herramientas

![image](https://github.com/user-attachments/assets/757945bf-d7cc-4e84-823e-a81595fd7035)

Objetivo: Utilizar la interfaz de Google BigQuery para crear las 4 tablas (1 por cada archivo)

## 2.2  Identificar y manejar valores nulos


