---
title: run_params.py
layout: default
nav_order: 2
parent: Parámetros iniciales
---

# run_params.py
Este archivo tiene dos partes, la primera se encarga de utilizar las rutas por defecto para el código, que se basa en el módulo `place.py`, y depende fuertemente del lugar donde se ejecuta el código. Mientras la segunda consta de parámetros regulares que son los de defecto.

## Parámetros regulares
```python
data_picked = "2024_02_26" #dato escogido por defecto
calib_picked = "2023_09_07" #calibrador escogido por defecto
# Banderas por defecto
plot_dark_noise = False #mostrar gráfico de ruido
plot_cdep = False       #mostrar calibrador
plot_ql = True          #mostrar quicklook
plot_dep = False        #mostrar depolarizador
use_log = True          #crear log
tiempo_promedio = 10    #tiempo a promediar
plot_molecular = True   #hacer ajuste molecular
```

## Parámetros basados en lugar
Se lee el archivo `place.py`, generado por el módulo [pick_place.py](pick_place). Acorde a eso escogemos los parámetros.

```python
from place import place #importando el parámetro deseado

#escogiendo las rutas acorde a cet parámetro
if place == "Dev":
    # a) Carpeta donde están todos los datos
    data_dir = "D:\LFA\lidar_data"
    # b) Carpeta donde están los archivos de polarización
    # (será ignorada, al igual si hay una carpeta de zips)
    pola_dir = "D:\LFA\lidar_data\calib_files"
    # f) Carpeta donde guardar archivos auxiliares (como calibración y figuras)
    carpeta_aux = "D:\LFA\lidar_data\\auxs"
    # Carpeta donde se guardan solamente las figuras
    carpeta_figs = "D:\LFA\lidar_data\\figs"
elif place == "Cota Cota":
    # a) Carpeta donde están todos los datos
    data_dir = "K:\\2024\\Datos"
    # b) Carpeta donde están los archivos de polarización
    # (será ignorada, al igual si hay una carpeta de zips)
    pola_dir = "K:\\2024\\calib_files"
    # f) Carpeta donde guardar archivos auxiliares (como calibración y figuras)
    carpeta_aux = "K:\\2024\\AUXI"
    # Carpeta donde se guardan solamente las figuras
    carpeta_figs = "K:\\2024\\FIGS"
elif place == "Linux":
    # a) Carpeta donde están todos los datos
    data_dir = "/home/ludving/LFA/LIDAR/lidar_data"
    # b) Carpeta donde están los archivos de polarización
    # (será ignorada, al igual si hay una carpeta de zips)
    pola_dir = "/home/ludving/LFA/LIDAR/lidar_data/lidar_calib"
    # f) Carpeta donde guardar archivos auxiliares (como calibración y figuras)
    carpeta_aux = "/home/ludving/LFA/LIDAR/aux"
    # Carpeta donde se guardan solamente las figuras
    carpeta_figs = "/home/ludving/LFA/LIDAR/lidar_figs"
```

Si se desea que su computadora esté en las opciones, añadir un `elif` a esta función y añadir el nombre del lugar al módulo [pick_place.py](pick_place).
