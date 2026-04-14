# Sales Marketing Dataset

Proyecto de analisis, transformacion y comparacion de resultados para un dataset de ventas y marketing.

## Objetivo

Estandarizar un dataset con problemas de calidad (nulos, formatos inconsistentes y valores extremos), generar versiones limpias para analisis/modelado y comparar resultados antes vs despues de la transformacion.

## Preparacion del entorno

1. Crear entorno virtual:

```bash
python -m venv venv
```

2. Activar entorno virtual:

Windows (PowerShell):

```powershell
.\venv\Scripts\Activate.ps1
```

Linux/macOS:

```bash
source venv/bin/activate
```

3. Instalar dependencias:

```bash
pip install -r requirements.txt
```

## Estructura de trabajo

- notebooks/1_EDA.ipynb: analisis exploratorio inicial.
- notebooks/2_Transformacion_de_datos.ipynb: limpieza, imputacion, winsorizacion, codificacion y exportaciones.
- notebooks/3_Comparacion_de_resultados.ipynb: comparacion entre dataset sucio y limpio + generacion de figuras.
- src/Routes.py: rutas centralizadas del proyecto (raw, processed, notebooks, figures).

## Orden recomendado de ejecucion

1. Ejecutar notebooks/1_EDA.ipynb.
2. Ejecutar notebooks/2_Transformacion_de_datos.ipynb para generar datasets de salida en data/processed/.
3. Ejecutar notebooks/3_Comparacion_de_resultados.ipynb para generar comparativas y figuras en outputs/figures/.

## Transformaciones aplicadas

### Imputacion de datos

Reglas implementadas en notebooks/2_Transformacion_de_datos.ipynb:

- age:
	- Limpieza previa de strings (espacios y variantes de nan como texto).
	- Imputacion por mediana global.
	- Conversion final a entero.
- total_spent:
	- Imputacion por mediana dentro de cada subscription_type.
	- Fallback a mediana global si algun grupo queda sin valor imputable.
- satisfaction_score:
	- Imputacion por mediana global.
- gender:
	- Estandarizacion de formato y reemplazo de nulos por Unknown.
- country:
	- Imputacion por moda global.

### Tratamiento de outliers

Se aplica winsorizacion basada en IQR (sin eliminar filas):

- Regla: limites en Q1 - 1.5 * IQR y Q3 + 1.5 * IQR.
- Accion: clip de valores fuera de limites.
- Variables tratadas:
	- age, total_spent, avg_order_value, lifetime_value, total_visits,
		avg_session_time, pages_per_session, support_tickets y delivery_delay_days.
- Post-proceso:
	- Restauracion de tipo entero en age, total_visits, support_tickets y delivery_delay_days.

### Codificacion con LabelEncoder

Despues de construir df_limpio, se crea un segundo dataset (df_codificado) usando LabelEncoder de scikit-learn.

Columnas codificadas:

- gender
- country
- acquisition_channel
- subscription_type
- payment_method

Nota importante:

- La codificacion se ajusta en el mismo notebook por columna y transforma solo el dataset final codificado.
- El dataset limpio original (sin codificar) se conserva por separado para analisis interpretables.

## Guardado diferenciado de archivos resultantes

El pipeline genera salidas separadas segun objetivo de uso:

1. Dataset limpio para analisis (formato tabular original):
	 - data/processed/Sales_Marketing_Clean.xlsx
2. Dataset codificado para modelado:
	 - data/processed/Sales_Marketing_Clean_(Codificado).csv
3. Figuras de comparacion (notebook 3):
	 - outputs/figures/conteo_nulos_bar.png
	 - outputs/figures/heatmap_nulos.png
	 - outputs/figures/conteo_generos_sucios_bar.png
	 - outputs/figures/conteo_generos_limpios_bar.png

## Validaciones tecnicas implementadas

- Optimizacion de memoria con downcasting y medicion cuantitativa del impacto en bytes y porcentaje.
- Agrupacion multivariable por country y subscription_type.
- Tabla dinamica con pivot_table para acquisition_channel vs country.

## Notas operativas

- Las rutas se gestionan desde src/Routes.py mediante el diccionario RUTAS.
- data/processed/ y outputs/figures/ contienen artefactos generados por notebooks.
- El proyecto ignora archivos de documentacion (docs/) y comprimidos (*.zip) mediante .gitignore para evitar versionar adjuntos pesados o de apoyo.
