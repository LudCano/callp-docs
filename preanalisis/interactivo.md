---
title: interactivo.py
layout: default
nav_order: 2
parent: Pre análisis
---

# interactivo.py
_Última actualización: 27 feb 2024_  
Como dicho en la página principal, este módulo se centra en crear la interfaz para el menú principal, es llamado como un ejecutable a parte debido a particularidades de la librería usada (`tkinter`). Se apoya de varias funciones en el módulo [read_modules.py](read_modules).

{: .librerias }
> Utiliza las siguientes librerías
> - `tkinter` : Librería de interfaces.
> - `PIL` : (pillow) Para mostrar imágenes.
> - `pandas` : Manejo de tablas .csv.

El menú principal del código se ve como la siguiente figura:  
![](figs/menu.png)

### Primera columna: Carpetas
En la primera columna se observan los siguientes parámetros:
 - **Carpeta de datos**: Ruta general donde se encuentran los datos (éstos pueden estar muy anidados, es decir, encontrarse dentro de varias subcarpetas).
 - **Carpeta de calibs**: Ruta de calibradores
 - **Carpeta auxiliar**: Para algunos outputs, los archivos `log` se escriben acá.
 - **Carpeta figuras**: Lugar donde se almacenan las figuras generadas por el código.
 - **Generar LOG** (checkbox): Si se desea que se genere el archivo log (se encuentra almacenado en la carpeta auxiliar).

Posteriormente tenemos un botón **ACTUALIZAR** que busca los datos y genera el archivo de compilado de información de los datos disponibles.

{: .note }
Es recomendado que después de usar el botón ACTUALIZAR se reinicie el programa, este bug será reparado en futuras versiones.

### Segunda columna: Escoger dato
En esta columna escogeremos el dato deseado a través de 3 listas desplegables. El manejo de las listas superiores (gestión y mes) filtran automáticamente los datos disponibles en la última lista desplegable (Dato a escoger)  
 - **GESTION**: Nos permite escoger el año.
 - **MES**: Filtra por mes en formato `YYYY_MM` (por ejemplo `2022_10` es Octubre de 2023).
 - **Dato a escoger**: Fechas disponibles de datos. En caso que aparezca algún duplicado la elección es indiferente. Por defecto existe un dato ya seleccionado, se puede cambiar este parámetro en [run_params.py](run_params)

### Tercera columna: Opciones
Acá tenemos parámetros a usar durante el código, la mayoría son checkboxes para decidir si mostraremos algún gráfico en específico. Todos los parámetros por defecto se encuentran (y se pueden modificar en [run_params.py](run_params)).
 - **Segundos a promediar**: Cantidad de segundos a promediar los datos.
 - **Mostrar ruido**: Mostrar gráfico de ruido (detalles en [noise_calibration.py](noise_calibration)).
 - **Mostrar CDEP**: Mostrar gráfico de calibradores (detalles en [noise_calibration.py](noise_calibration)).
 - **Calibrador**: Calibradores disponibles, es recomendado usar el más reciente, deberían estar almacenados en la carpeta de calibs de la primera columna.
 - **Mostrar Quicklook**: Mostrar quicklook (detalles en [quicklook](../quicklook)).
 - **Mostrar DEP**: Mostrar depolarización (detalles en [depolarización](../depolarizacion)).
 - **Mostrar Molecular**: Realizar y mostrar el ajuste molecular (detalles en [Ajuste Molecular](../ajustemolecular)).

# Código
Ahora seccionaremos el código para mostrar lo que hace, en realidad, en Tkinter creamos objetos como botones, listas, etc. y posteriormente obtenemos los parámetros puestos a través de algún método.

Primero las librerías a utilizar
```python
import tkinter as tk
from tkinter import ttk
from PIL import Image, ImageTk
import pandas as pd
import params
import run_params
from lidar_master import get_from_interactive
```

Notemos que llamamos al master, específicamente llamamos a una función ahí que se llama `get_from_interactive` y es la función que comunica este módulo con el master.  
Ésta función toma los valores (mandados por el interactivo) y genera una variable global (que se sale de la función), lo que hace que estos valores pasen directamente al master.
```python
#------------ en lidar_master.py -----------------
def get_from_interactive(vals):
    global vals_from_interac
    vals_from_interac = vals
```

Posteriormente necesitamos los valores predeterminados de las listas desplegables, entonces leemos el archivo `data.csv` generado por [read_modules](read_modules) y lo trabajamos con pandas
```python
## Obteniendo los datos
lst_data = pd.read_csv("./data.csv")                             #leemos el archivo
lst_data["anho"] = [i[:4] for i in lst_data.fecha.to_list()]     #obtenemos años
lst_data["mes"] = [i[:7] for i in lst_data.fecha.to_list()]      #obtenemos meses 
anho_unique = list(lst_data.anho.unique())                       #generamos la lista unica de años
mes_unique = list(lst_data.mes.unique())                         #generamos la lista unica de meses
dark_lst = lst_data[lst_data.tipo == "dark"].fecha.to_list()     #generamos las opciones de calibradores (darks)
```


