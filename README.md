# TP Final - Pipeline ETL de exportaciones del NEA

**Unidad II · Fundamentos de la Programación**  
Diplomatura en Data Analytics e IA Aplicada - UNNE / Extender

## Descripción

Este proyecto implementa un pipeline ETL (Extract, Transform, Load) para obtener, transformar, validar y almacenar datos de exportaciones de las provincias del NEA.

El pipeline trabaja con datos de las siguientes provincias:

- Chaco
- Corrientes
- Formosa
- Misiones

El período analizado comprende los años 1993 a 2024 y la unidad de medida es millones de dólares FOB.

La información proviene de la API de Series de Tiempo de datos.gob.ar, con datos del INDEC. Se utilizan los datasets 357.1 "Exportaciones por provincia y país de destino" y 350.1 "Exportaciones por provincia y rubro".
## Funcionamiento del pipeline

El proceso está dividido en tres etapas:

```text
EXTRACT → TRANSFORM → LOAD
Extract

La etapa de extracción consulta la API de datos.gob.ar y descarga la información correspondiente a las cuatro provincias.

Se realizan 8 descargas en total:

4 grupos de datos por país de destino.
4 grupos de datos por rubro.

Los datos crudos se almacenan en formato JSON dentro de data/raw/ sin realizar modificaciones.

Transform

La etapa de transformación convierte los datos del formato ancho al formato largo y genera las variables necesarias para el análisis.

Las principales transformaciones realizadas son:

Conversión de la fecha a año.
Clasificación del destino según región geoeconómica.
Cálculo de la década.
Cálculo de la participación porcentual de cada destino sobre el total provincial.
Cálculo de la variación interanual.
Ranking de destinos dentro de cada provincia y año.
Identificación de los tres principales destinos.
Determinación del rubro principal de cada provincia y año.
Cálculo de la participación de los productos primarios.
Unión de la información de destinos con la información de rubros.

El dataset final contiene 13 columnas.

Load

La etapa de carga realiza controles de calidad antes de guardar los resultados.

Los controles implementados verifican:

Cantidad mínima de filas.
Cumplimiento del esquema de 13 columnas.
Ausencia de duplicados.
Valores dentro de rangos plausibles.
Cobertura de las variables derivadas.

Una vez superados los controles, se generan tres salidas:

data/processed/exportaciones_nea.csv
data/processed/resumen.json
logs/pipeline.log
## Resultados

La ejecución del pipeline produjo:

- **1408 filas**
- **13 columnas**
- **4 provincias**
- **Período 1993-2024**
- **8 de 8 descargas exitosas**

Los controles de calidad finalizaron correctamente:

```text
Cantidad de filas: OK
Columnas: OK
Unicidad: OK
Rangos: OK
Cobertura: OK
El archivo resumen.json registra además las siguientes estadísticas sobre valor_musd:

Estadística	Valor
Mínimo	0,00
Máximo	399,14
Promedio	20,17
## Una observación sobre los datos

Una observación que se puede identificar en el dataset es la importancia de China dentro de las exportaciones de Chaco.

En 2024, China registra **110,93 millones de dólares FOB**, equivalentes al **27,61 %** del total exportado por Chaco en ese año. Además, ocupa el **primer lugar del ranking de destinos** para esa provincia y año.

Este ejemplo muestra cómo las variables de participación porcentual y ranking permiten identificar rápidamente qué destinos tienen mayor peso dentro de las exportaciones de una provincia en un determinado año.
   ## Estructura del proyecto

```text
├── config.py
├── src/
│   ├── extract.py
│   ├── transform.py
│   ├── load.py
│   └── main.py
├── tests/
│   └── test_transform.py
├── data/
│   ├── raw/
│   └── processed/
├── logs/
├── README.md
└── requirements.txt
Principales archivos

config.py contiene la configuración del proyecto, como los identificadores de las series, las rutas y los parámetros utilizados por el pipeline.

src/extract.py se encarga de obtener los datos desde la API y almacenarlos en formato JSON dentro de data/raw/.

src/transform.py transforma los datos crudos y genera el dataset analítico final.

src/load.py realiza los controles de calidad y guarda las salidas del pipeline.

src/main.py coordina las tres etapas y permite ejecutar el proceso completo.

tests/test_transform.py contiene las pruebas utilizadas para verificar el funcionamiento de las transformaciones.
## Instalación

Se requiere **Python 3.8 o superior**.

Desde la terminal de VS Code, ubicándose en la carpeta raíz del proyecto, se puede verificar la versión instalada con:

python --version
El proyecto utiliza la biblioteca estándar de Python, por lo que no requiere la instalación de librerías externas adicionales.

Ejecución

Para ejecutar el pipeline completo y descargar los datos desde la API:

python src/main.py

La primera ejecución consulta la API y almacena los datos crudos en data/raw/.

Una vez descargados los datos, también es posible ejecutar el pipeline sin conexión a Internet:

python src/main.py --sin-internet

Esta opción reutiliza los archivos JSON previamente almacenados en data/raw/.

Tests

Para ejecutar las pruebas de transformación:

python tests/test_transform.py

## Idempotencia

El pipeline fue ejecutado dos veces utilizando los datos almacenados localmente.

En ambas ejecuciones, el archivo `exportaciones_nea.csv` produjo el mismo hash:

```text
8F45528B6B845ECA6051EE5C1D368B88523C3C79F33F82091C1D11F932ADCF92
Esto permite comprobar que el contenido del CSV permanece igual entre ejecuciones.

Por otra parte, el archivo pipeline.log conserva el historial de ejecuciones agregando una nueva línea por cada corrida.

Ejemplo:

2026-09-28 12:08 | OK | 1408 filas | 1993-2024
2026-09-28 12:11 | OK | 1408 filas | 1993-2024
Fuente de datos

Los datos utilizados provienen del Instituto Nacional de Estadística y Censos (INDEC), a través del portal de datos abiertos del Estado argentino:

https://datos.gob.ar/

La información se obtiene mediante la API de Series de Tiempo:

https://apis.datos.gob.ar/series/api/

Datasets utilizados:

357.1 - Exportaciones por provincia y país de destino.
350.1 - Exportaciones por provincia y rubro.
